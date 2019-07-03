---
title: spring-boot-elasticsearch-demo
date: 2019-05-30 22:00:12
tags: 学习
---

首先安装elasticsearch，为了测试就以docker方式安装，elastic.yml 配置如下

```
version: '3'
services:
  es:
    image: docker.elastic.co/elasticsearch/elasticsearch:6.4.3
    container_name: es
    hostname: es
    environment:
      - discovery.type=single-node
      - cluster.name=es
      - "ES_JAVA_OPTS=-Xms512m -Xmx512m"
    ulimits:
      memlock:
        soft: -1
        hard: -1
    volumes:
      - esdata:/usr/share/elasticsearch/data
    restart: always
    ports:
      - 9200:9200
      - 9300:9300
    networks:
      - esnet  

    
  elastichq:
    image: elastichq/elasticsearch-hq
    container_name: elastichq
    hostname: elastichq
    environment:
      - HQ_DEFAULT_URL=http://es:9200
    ports:
      - 5000:5000
    depends_on:
      - es
    networks:
      - esnet  
    restart: always
    
volumes:
  esdata:
    driver: local

networks:
  esnet:

```

运行以下命令启动elasticsearch

```
docker-compose -f elastic.yml up -d
```

打开chrome 访问http://your-ip:5000可以看到如下

![1](https://files.catbox.moe/8llvdg.png)

然后点击connect按钮

![2](https://files.catbox.moe/qyrxgm.png)

可以看到cluster-name为es， 安装完成，下面代码
新建spring-boot 工程

pom.xml

```
<dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-data-elasticsearch</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-devtools</artifactId>
            <scope>runtime</scope>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-configuration-processor</artifactId>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>
```

先依照例子玩一下，刚开始研究

LuckFairy

```

@Data
@Document(indexName = "test",type = "luckfairy", shards = 1,replicas = 0, refreshInterval = "-1")
public class LuckFairy {

    @Id
    private String id;
    @Field(type= FieldType.Text)
    private String firstName;
    @Field(type= FieldType.Text)
    private String lastName;
    @Field(type= FieldType.Integer)
    private Integer age = 0;
    @Field(type= FieldType.Text)
    private String about;

}

```

LuckFairyRepository

```
@Repository
public interface LuckFairyRepository extends ElasticsearchRepository<LuckFairy,String> {

    LuckFairy getById(String id);

}
```

LuckFairyController

```
@Slf4j
@RestController
public class LuckFairyController {


    @Autowired
    private LuckFairyRepository luckFairyRepository;

    @GetMapping("/add")
    public String add(){
        LuckFairy luckFairy = new LuckFairy();
        luckFairy.setId("1");
        luckFairy.setFirstName("luckFairy");
        luckFairy.setLastName("luckFairy");
        luckFairy.setAge(26);
        luckFairy.setAbout("i am luckFairy");
        luckFairyRepository.save(luckFairy);
        System.err.println("add a obj");
        return "success";

    }

    @GetMapping("/query")
    public LuckFairy query() {
        LuckFairy accountInfo = luckFairyRepository.getById("1");
        return accountInfo;
    }

}
```

LuckFairyApplication

```
@SpringBootApplication
@EnableElasticsearchRepositories
public class LuckFairyApplication {

    public static void main(String[] args) {
        SpringApplication.run(LuckFairyApplication.class, args);
    }

}

```

application.yml

```
spring:
  data:
    elasticsearch:
      cluster-name: es
      cluster-nodes: 192.168.56.5:9300
      repositories:
        enabled: true
        
```

启动工程
访问 http://localhost:80080/add

![3](https://files.catbox.moe/0vpuun.png)

可以看上图我们添加成功了test

打开test链接,可以从Mappings中看到我们添加的luckfairy类

![4](https://files.catbox.moe/zbuoy1.png)

访问http://localhost:80080/query查询刚才添加的对象

![5](https://files.catbox.moe/p2mohj.png)

demo地址
[https://github.com/fuzhouxxdong/spring-boot-elasticsearch-demo](https://github.com/fuzhouxxdong/spring-boot-elasticsearch-demo)

未完待续。。。刚开始玩。。。

---
title: Swagger2使用
date: 2017-06-30 14:25:35
type: "tags"
tags: 学习
categories: 学习
---

 ###### 练习代码地址 

  [spring-boot-swagger2](https://github.com/flowerflash/spring-boot-swagger2)
 ###### Swagger2
 
 它既可以减少我们创建文档的工作量，同时说明内容又整合入实现代码中，让维护文档和修改代码整合为一体，
 可以让我们在修改代码逻辑的同时方便的修改文档说明。 
 ###### 添加Swagger2依赖
  
  用maven 构建，在pom.xml添加依赖，如下
  
  	    <dependency>
  			<groupId>io.springfox</groupId>
  			<artifactId>springfox-swagger2</artifactId>
  			<version>2.7.0</version>
  		</dependency>
  
  		<dependency>
  			<groupId>io.springfox</groupId>
  			<artifactId>springfox-swagger-ui</artifactId>
  			<version>2.7.0</version>
  		</dependency>
  		
  可能下载jar的时候要添加下面的repositories
  
          <repositories>
                <repository>
                    <id>jcenter-snapshots</id>
                    <name>jcenter</name>
                    <url>https://jcenter.bintray.com/</url>
                </repository>
          </repositories>
            
            
 ###### 配置Swagger2
  
          package com.example.demo;
          
          import com.google.common.base.Predicate;
          import com.google.common.base.Predicates;
          import org.springframework.context.annotation.Bean;
          import org.springframework.context.annotation.ComponentScan;
          import org.springframework.context.annotation.Configuration;
          import springfox.documentation.builders.ApiInfoBuilder;
          import springfox.documentation.builders.PathSelectors;
          import springfox.documentation.service.ApiInfo;
          import springfox.documentation.service.Contact;
          import springfox.documentation.spi.DocumentationType;
          import springfox.documentation.spring.web.plugins.Docket;
          import springfox.documentation.swagger2.annotations.EnableSwagger2;
          
          /**
           * Created by Dxx on 2017/6/30.
           */
          
          @Configuration
          @EnableSwagger2
          @ComponentScan("com.example.demo.controllers")
          public class Swagger2 {
          
              @Bean
              public Docket Api() {
                  return new Docket(DocumentationType.SWAGGER_2)
                      .groupName("Swagger2 Test API")
                      .forCodeGeneration(true)
                      .pathMapping("/")
                      .select()
                      .paths(paths())
                      .build()
                      .apiInfo(apiInfo())
                      .useDefaultResponseMessages(true);
              }
          
              private Predicate<String> paths() {
                  return Predicates
                      .and(PathSelectors.regex("/.*"), Predicates.not(PathSelectors.regex("/error")));
              }
          
              private ApiInfo apiInfo() {
                  Contact contact = new Contact("Dxx", "https://dxx.flowerflash.me/", "dfz@flowerflash.me");
                  return new ApiInfoBuilder()
                      .title("Document Api")
                      .description("Swagger2 Example")
                      .license("https://www.apache.org/licenses/LICENSE-2.0")
                      .contact(contact)
                      .version("1.0")
                      .build();
              }
          
          }



 ###### 添加Controller API接口

        package com.example.demo.controllers;
        
        import com.example.demo.domains.Order;
        import io.swagger.annotations.ApiImplicitParam;
        import io.swagger.annotations.ApiOperation;
        import java.util.ArrayList;
        import java.util.List;
        import org.springframework.web.bind.annotation.GetMapping;
        import org.springframework.web.bind.annotation.PathVariable;
        import org.springframework.web.bind.annotation.RequestMapping;
        import org.springframework.web.bind.annotation.RestController;
        
        /**
         * Created by Dxx on 2017/6/30.
         */
        @RestController
        @RequestMapping("/api")
        public class OrderController {
        
            @ApiOperation(value = "取得所有订单信息", notes = "124141412" , produces = "application/json")
            @GetMapping("/orders")
            public List<Order> getUsers() {
                List<Order> orderList = new ArrayList<Order>();
                for (int i = 0; i < 5; i++) {
                    Order Order = new Order(i, "OrderNo" + i);
                    orderList.add(Order);
                }
                return orderList;
            }
        
            @ApiOperation(value = "通过订单ID取得单个订单信息", notes = "124141412", produces = "application/json")
            @ApiImplicitParam(required = true, name = "id", value = "用户ID", dataType = "int", paramType = "path")
            @GetMapping(value = "/order/{id}")
            public Order getUser(@PathVariable("id") Integer id) {
                List<Order> orderList = new ArrayList<Order>();
                for (int i = 0; i < 5; i++) {
                    Order Order = new Order(i, "OrderNo" + i);
                    orderList.add(Order);
                }
                Order order = new Order();
                for (Order o: orderList) {
                    if (o.getId() == id){
                        order = o;
                        break;
                    }
                }
                return order;
            }
        }


 ###### 运行结果

  访问如下地址后：[http://localhost:8080/swagger-ui.html]()
  
  ![QQ20170630145529.png](https://tuchuang001.com/images/2017/06/30/QQ20170630145529.png)

  
 ###### 最后

  总体来说还是挺方便的，还需要努力学习一下。。。。
  

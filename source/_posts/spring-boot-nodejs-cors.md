---
title: spring-boot2和nodejs跨域解决
date: 2018-10-15 14:11:19
categories: learning
tags: learning
keywords: learning,spring boot,spring cloud,docker,maven,nodejs,cors
---

最近使用项目使用spring boot2和nodejs,前后端分离,要求解决跨域问题,记录一下

<!--more-->

spring boot java config 统一配置

```java
@Configuration
public class MyConfig {

	@Bean
	public WebMvcConfigurer corsConfigurer() {
		return new WebMvcConfigurer() {
			@Override
			public void addCorsMappings(CorsRegistry registry) {
				registry.addMapping("/api/**").allowedOrigins("*");
			}
		};
	}

}
```

还可以用注解需要的controller上面加

```java
@CrossOrigin(origins = "*", maxAge = 3600)
@RestController
@RequestMapping("/api")
public class HomeController {
	
	@Autowired
	private HomeService homeService;
}	
```

<!--more-->

nodejs cors app.js 
```javascript
//app.js
app.all('*', function(req, res, next) {
    res.header("Access-Control-Allow-Origin", "*");
    res.header("Access-Control-Allow-Headers", "X-Requested-With");
    res.header("Access-Control-Allow-Methods","PUT,POST,GET,DELETE,OPTIONS");
    res.header("X-Powered-By",' 3.2.1')
    res.header("Content-Type", "application/json;charset=utf-8");
    next();
});
```
还可以直接引入cors,我用的这种
```bash
npm install cors
```
```javascript
//app.js
var cors = require('cors');
app.use(cors());
```
header头三件套
> header(‘Access-Control-Allow-Origin :’.$origin); // 允许的域名（ * 所有域） 
> header(‘Access-Control-Allow-Methods : POST’); // 允许的方法 
> header(‘Access-Control-Allow-Headers : x-requested-with , content-type’); // 服务器支持的头信息


# Spring Framework notes

### 在Spring启动后做一些事情.
> 实现 Lifecycle 推荐  
> org.springframework.context.SmartLifecycle
> 实现:
> org.springframework.context.ApplicationContextAware等

### 获取参数名
>org.springframework.core.LocalVariableTableParameterNameDiscoverer

### 注解工具类
> org.springframework.core.annotation.AnnotationUtils

### 获取所有的URL mapping, 方法
> org.springframework.web.servlet.mvc.method.annotation.RequestMappingHandlerMapping

### swagger2
```
@Bean
public Docket productApi() {
	return new Docket(DocumentationType.SWAGGER_2)
			.select()
			.apis(RequestHandlerSelectors.basePackage("com.rolyh.company"))
			.paths(PathSelectors.ant("/**"))
			.build() 
			.globalOperationParameters(Lists.newArrayList(
					new ParameterBuilder().name("access-token")
					.description("令牌")
					.modelRef(new ModelRef("string"))
					.parameterType("header")
					.required(true)
					.build()))
			.apiInfo(
				new ApiInfo("Luoyh company system", "APP interface", "v1.0", "/",
				new Contact("luoyh(Roy)", 
					"http://www.rolyh.com", 
					"Me", 
					"http://www.rolyh.com"));
}
```

### URL参数匹配工具类
> org.springframework.util.PatternMatchUtils
# Spring Framework notes

### 在Spring启动后做一些事情.

```
> 实现 Lifecycle 推荐  
> org.springframework.context.SmartLifecycle
> 实现:
> org.springframework.context.ApplicationContextAware等
> CommandLineRunner , ApplicationRunner 
```

### Like FeignClient implementation

```
1, BeanDefinitionRegistryPostProcessor
2, ClassPathBeanDefinitionScanner
3, FactoryBean
```

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


### Dynamic change controller path

```

private static final String PATH_PATTERNS = "/708140bc2ef3fb8540bcd59beeaa1cb2579a896d";
    
    @Bean
    public WebMvcRegistrations webMvcRegistrations() {
        return new WebMvcRegistrationsAdapter() {

            @Override
            public RequestMappingHandlerMapping getRequestMappingHandlerMapping() {
                
                return new RequestMappingHandlerMapping() {

                    @Override
                    public void registerMapping(RequestMappingInfo mapping, Object handler, Method method) {
                        
                        if (method.isAnnotationPresent(RpcApi.class)) {
                            System.out.println(method);
                            mapping = new RequestMappingInfo(new PatternsRequestCondition(PATH_PATTERNS).combine(mapping.getPatternsCondition()), 
                                    mapping.getMethodsCondition(), 
                                    mapping.getParamsCondition(), 
                                    mapping.getHeadersCondition(), 
                                    mapping.getConsumesCondition(), 
                                    mapping.getProducesCondition(), 
                                    mapping.getCustomCondition());
                        }
                        super.registerMapping(mapping, handler, method);
                    }
                    
                };
            }
            
        };
    }

```

### SpringBoot 自定义Jackson序列化, 反序列化

```
   private JsonSerializer<BigDecimal> bigDecimalSser() {
        return new JsonSerializer<BigDecimal>() {

            @Override
            public void serialize(BigDecimal value, JsonGenerator gen, SerializerProvider serializers) throws IOException {
                if (null != value) {
                    gen.writeString(value.setScale(2, BigDecimal.ROUND_HALF_UP).toString());
                }
            }

        };
    }

    private JsonDeserializer<BigDecimal> bigDecimalDeser() {

        return new JsonDeserializer<BigDecimal>() {

            @Override
            public BigDecimal deserialize(JsonParser p, DeserializationContext ctxt) throws IOException, JsonProcessingException {
                return p.getDecimalValue().setScale(2, BigDecimal.ROUND_HALF_UP);
            }

        };
    }


    @Bean
    public ObjectMapper objectMapper() {
        ObjectMapper mapper = new ObjectMapper();
        SimpleModule testModule = new SimpleModule("bigDecimal-module", new Version(1, 0, 0, null, "", ""))
                .addSerializer(BigDecimal.class, bigDecimalSser())
                .addDeserializer(BigDecimal.class, bigDecimalDeser());
        mapper.registerModule(testModule);
        return mapper;
    }

// or

    @Bean
    public WebMvcConfigurer webMvcConfig() {
        return new WebMvcConfigurer() {

            @Override
            public void extendMessageConverters(List<HttpMessageConverter<?>> converters) {
                for (HttpMessageConverter<?> converter : converters) {
                    if (converter instanceof org.springframework.http.converter.json.MappingJackson2HttpMessageConverter) {
                        ObjectMapper mapper = ((MappingJackson2HttpMessageConverter) converter).getObjectMapper();
                        SimpleModule testModule = new SimpleModule("bigDecimal-module", new Version(1, 0, 0, null, "", ""))
                                .addSerializer(BigDecimal.class, bigDecimalSser())
                                .addDeserializer(BigDecimal.class, bigDecimalDeser());
                        mapper.registerModule(testModule);
                    }
                }
            }

        };
    }



```

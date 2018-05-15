```
@Bean
    public BeanDefinitionRegistryPostProcessor beanPostProcessor() {

        return new BeanDefinitionRegistryPostProcessor() {

            @Override
            public void postProcessBeanFactory(ConfigurableListableBeanFactory beanFactory) throws BeansException {
            }

            @Override
            public void postProcessBeanDefinitionRegistry(BeanDefinitionRegistry registry) throws BeansException {
                ClassPathBeanDefinitionScanner scanner = rpcScan(registry);
                scanner.scan("com.xxx.service");
            }
        };
    }

    private ClassPathBeanDefinitionScanner rpcScan(BeanDefinitionRegistry registry) {

        return new ClassPathBeanDefinitionScanner(registry) {
            @Override
            public void registerDefaultFilters() {
                this.addIncludeFilter(new AnnotationTypeFilter(RPC.class));
            }

            @Override
            public Set<BeanDefinitionHolder> doScan(String... basePackages) {
                Set<BeanDefinitionHolder> beanDefinitions = super.doScan(basePackages);
                for (BeanDefinitionHolder holder : beanDefinitions) {
                    GenericBeanDefinition definition = (GenericBeanDefinition) holder.getBeanDefinition();
                    definition.getPropertyValues().add("interfaceClass", definition.getBeanClassName());
                    definition.setBeanClass(RPCProxy.class);
                }
                return beanDefinitions;
            }

            @Override
            public boolean isCandidateComponent(AnnotatedBeanDefinition beanDefinition) {
                boolean isInterface = beanDefinition.getMetadata().isInterface();
                boolean hasAnnotation = beanDefinition.getMetadata().hasAnnotation(RPC.class.getName());
                return isInterface && hasAnnotation;
            }
        };
    }
```

> APIRPC

```
@RPC("/rpc")
public interface ApiRPC {

    R<Map<String, Object>> data(String code, Long timestamp);
    
}
```

> RPCProxy

```
public class RPCProxy<T> implements FactoryBean<T> {
    
    private Class<T> interfaceClass;

    public Class<T> getInterfaceClass() {
        return interfaceClass;
    }
    public void setInterfaceClass(Class<T> interfaceClass) {
        this.interfaceClass = interfaceClass;
    }
    @Override
    public T getObject() throws Exception {
        return (T) new Proxy().obtain(interfaceClass);
    }

    @Override
    public Class<?> getObjectType() {
        return interfaceClass;
    }

}
```

> ProxyService

```
public class ProxyService implements InvocationHandler {

    private int id = 0;
    private Class<?> clazz;

    @SuppressWarnings("unchecked")
    public <T> T obtain(Class<T> clazz) {
        this.clazz = clazz;
        return (T) Proxy.newProxyInstance(clazz.getClassLoader(), new Class[] { clazz }, this);
    }

    @Override
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
        // TODO code here, HTTP/RPC/method...
        System.out.println(clazz);
        Map<String, Object> data = Maps.newHashMap();
        data.put("id", id++);
        data.put("name", UUID.randomUUID().toString());
        data.put("timestamp", System.currentTimeMillis());
        return R.ok(data);
    }

}
```

# Custom GetMapping/PostMapping with spring 4.1


> GetMapping

```

import java.lang.annotation.Documented;
import java.lang.annotation.ElementType;
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;
import java.lang.annotation.Target;

/**
 *
 * @author luoyh(Roy)
 * @date Apr 11, 2017
 */
@Target(ElementType.METHOD)
@Retention(RetentionPolicy.RUNTIME)
@Documented
public @interface GetMapping {

	String name() default "";
	String[] value() default {};
	String[] path() default {};
	String[] params() default {};
	String[] headers() default {};
	String[] consumes() default {};
	String[] produces() default {};

}


```

> PostMapping

```


import java.lang.annotation.Documented;
import java.lang.annotation.ElementType;
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;
import java.lang.annotation.Target;

/**
 *
 * @author luoyh(Roy)
 * @date Apr 11, 2017
 */
@Target(ElementType.METHOD)
@Retention(RetentionPolicy.RUNTIME)
@Documented
public @interface PostMapping {

	String name() default "";
	String[] value() default {};
	String[] path() default {};
	String[] params() default {};
	String[] headers() default {};
	String[] consumes() default {};
	String[] produces() default {};

}


```

> Processor

```


import java.lang.annotation.Annotation;
import java.lang.reflect.Method;
import java.util.LinkedHashMap;
import java.util.List;
import java.util.Map;
import java.util.Set;

import org.apache.commons.logging.Log;
import org.apache.ibatis.reflection.MetaObject;
import org.apache.ibatis.reflection.SystemMetaObject;
import org.apache.ibatis.reflection.wrapper.ObjectWrapper;
import org.springframework.beans.BeansException;
import org.springframework.beans.factory.config.BeanFactoryPostProcessor;
import org.springframework.beans.factory.config.ConfigurableListableBeanFactory;
import org.springframework.context.ApplicationContext;
import org.springframework.core.annotation.AnnotationUtils;
import org.springframework.stereotype.Component;
import org.springframework.stereotype.Controller;
import org.springframework.util.LinkedMultiValueMap;
import org.springframework.util.MultiValueMap;
import org.springframework.util.PathMatcher;
import org.springframework.web.accept.ContentNegotiationManager;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestMethod;
import org.springframework.web.method.HandlerMethod;
import org.springframework.web.servlet.handler.HandlerMethodMappingNamingStrategy;
import org.springframework.web.servlet.mvc.condition.ConsumesRequestCondition;
import org.springframework.web.servlet.mvc.condition.HeadersRequestCondition;
import org.springframework.web.servlet.mvc.condition.ParamsRequestCondition;
import org.springframework.web.servlet.mvc.condition.PatternsRequestCondition;
import org.springframework.web.servlet.mvc.condition.ProducesRequestCondition;
import org.springframework.web.servlet.mvc.condition.RequestCondition;
import org.springframework.web.servlet.mvc.condition.RequestMethodsRequestCondition;
import org.springframework.web.servlet.mvc.method.RequestMappingInfo;
import org.springframework.web.servlet.mvc.method.annotation.RequestMappingHandlerMapping;
import org.springframework.web.util.UrlPathHelper;

/**
 * Implements BeanFactoryPostProcessor and use MyBatis MetaObject.
 *
 * @author luoyh(Roy)
 * @date Apr 11, 2017
 */
@Component
public class Processor implements BeanFactoryPostProcessor {
	protected HandlerMethod createHandlerMethod(Object handler, Method method, ApplicationContext applicationContext) {
		HandlerMethod handlerMethod;
		if (handler instanceof String) {
			String beanName = (String) handler;
			handlerMethod = new HandlerMethod(beanName, applicationContext, method);
		}
		else {
			handlerMethod = new HandlerMethod(handler, method);
		}
		return handlerMethod;
	}
	
	
	private void updateNameMap(MultiValueMap<String, HandlerMethod> nameMap, String name, HandlerMethod newHandlerMethod) {

		List<HandlerMethod> handlerMethods = nameMap.get(name);
		if (handlerMethods != null) {
			for (HandlerMethod handlerMethod : handlerMethods) {
				if (handlerMethod.getMethod().equals(newHandlerMethod.getMethod())) {
					return;
				}
			}
		}

		nameMap.add(name, newHandlerMethod);

	}
	
	protected void registerHandlerMethod(
			Log logger,
			Map<RequestMappingInfo, HandlerMethod> handlerMethods, 
			MultiValueMap<String, RequestMappingInfo> urlMap,
			HandlerMethodMappingNamingStrategy<RequestMappingInfo> namingStrategy,
			MultiValueMap<String, HandlerMethod> nameMap,
			ApplicationContext applicationContext,
			PathMatcher pathMatcher,
			Object handler, Method method, RequestMappingInfo mapping) {
		HandlerMethod newHandlerMethod = createHandlerMethod(handler, method, applicationContext);
		HandlerMethod oldHandlerMethod = handlerMethods.get(mapping);
		if (oldHandlerMethod != null && !oldHandlerMethod.equals(newHandlerMethod)) {
			throw new IllegalStateException("Ambiguous mapping found. Cannot map '" + newHandlerMethod.getBean() +
					"' bean method \n" + newHandlerMethod + "\nto " + mapping + ": There is already '" +
					oldHandlerMethod.getBean() + "' bean method\n" + oldHandlerMethod + " mapped.");
		}

		handlerMethods.put(mapping, newHandlerMethod);
		if (logger.isInfoEnabled()) {
			logger.info("Mapped \"" + mapping + "\" onto " + newHandlerMethod);
		}
		Set<String> patterns = mapping.getPatternsCondition().getPatterns();
		for (String pattern : patterns) {
			if (!pathMatcher.isPattern(pattern)) {
				urlMap.add(pattern, mapping);
			}
		}

		if (namingStrategy != null) {
			String name = namingStrategy.getName(newHandlerMethod, mapping);
			updateNameMap(nameMap, name, newHandlerMethod);
		}
	}
	
	protected RequestMappingInfo createRequestMappingInfo(
			RequestMapping annotation, 
			RequestCondition<?> customCondition,
			UrlPathHelper urlPathHelper,
			PathMatcher pathMatcher,
			boolean useSuffixPatternMatch,
			boolean useTrailingSlashMatch,
			List<String> fileExtensions,
			ContentNegotiationManager contentNegotiationManager
			) {
		String[] patterns = annotation.value();
		return new RequestMappingInfo(
				annotation.name(),
				new PatternsRequestCondition(patterns, urlPathHelper, pathMatcher,
						useSuffixPatternMatch, useTrailingSlashMatch, fileExtensions),
				new RequestMethodsRequestCondition(annotation.method()),
				new ParamsRequestCondition(annotation.params()),
				new HeadersRequestCondition(annotation.headers()),
				new ConsumesRequestCondition(annotation.consumes(), annotation.headers()),
				new ProducesRequestCondition(annotation.produces(), annotation.headers(), contentNegotiationManager),
				customCondition);
	}
	protected RequestMappingInfo getMappingForMethod(
			PostMapping postMapping,
			Method method, Class<?> handlerType,
			UrlPathHelper urlPathHelper,
			PathMatcher pathMatcher,
			boolean useSuffixPatternMatch,
			boolean useTrailingSlashMatch,
			List<String> fileExtensions,
			ContentNegotiationManager contentNegotiationManager) {
		RequestMappingInfo info = null;
		RequestMapping methodAnnotation = buildRequestMapping(postMapping);
		if (methodAnnotation != null) {
			info = createRequestMappingInfo(methodAnnotation, null, urlPathHelper, pathMatcher, useSuffixPatternMatch, useTrailingSlashMatch, fileExtensions, contentNegotiationManager);
			RequestMapping typeAnnotation = AnnotationUtils.findAnnotation(handlerType, RequestMapping.class);
			if (typeAnnotation != null) {
				info = createRequestMappingInfo(typeAnnotation, null, urlPathHelper, pathMatcher, useSuffixPatternMatch, useTrailingSlashMatch, fileExtensions, contentNegotiationManager).combine(info);
			}
		}
		return info;
	}
	
	protected RequestMappingInfo getMappingForMethod(
			GetMapping getMapping,
			Method method, Class<?> handlerType,
			UrlPathHelper urlPathHelper,
			PathMatcher pathMatcher,
			boolean useSuffixPatternMatch,
			boolean useTrailingSlashMatch,
			List<String> fileExtensions,
			ContentNegotiationManager contentNegotiationManager) {
		RequestMappingInfo info = null;
		RequestMapping methodAnnotation = buildRequestMapping(getMapping);
		if (methodAnnotation != null) {
			info = createRequestMappingInfo(methodAnnotation, null, urlPathHelper, pathMatcher, useSuffixPatternMatch, useTrailingSlashMatch, fileExtensions, contentNegotiationManager);
			RequestMapping typeAnnotation = AnnotationUtils.findAnnotation(handlerType, RequestMapping.class);
			if (typeAnnotation != null) {
				info = createRequestMappingInfo(typeAnnotation, null, urlPathHelper, pathMatcher, useSuffixPatternMatch, useTrailingSlashMatch, fileExtensions, contentNegotiationManager).combine(info);
			}
		}
		return info;
	}
	
	private RequestMapping buildRequestMapping(final PostMapping mapping) {
		return new RequestMapping() {
			
			@Override
			public Class<? extends Annotation> annotationType() {
				return RequestMapping.class;
			}
			
			@Override
			public String[] value() {
				return mapping.value();
			}
			
			@Override
			public String[] produces() {
				return mapping.produces();
			}
			
			@Override
			public String[] params() {
				return mapping.params();
			}
			
			@Override
			public String name() {
				return mapping.name();
			}
			
			@Override
			public RequestMethod[] method() {
				return new RequestMethod[] { RequestMethod.POST };
			}
			
			@Override
			public String[] headers() {
				return mapping.headers();
			}
			
			@Override
			public String[] consumes() {
				return mapping.consumes();
			}
		};
	}
	
	private RequestMapping buildRequestMapping(final GetMapping mapping) {
		return new RequestMapping() {
			
			@Override
			public Class<? extends Annotation> annotationType() {
				return RequestMapping.class;
			}
			
			@Override
			public String[] value() {
				return mapping.value();
			}
			
			@Override
			public String[] produces() {
				return mapping.produces();
			}
			
			@Override
			public String[] params() {
				return mapping.params();
			}
			
			@Override
			public String name() {
				return mapping.name();
			}
			
			@Override
			public RequestMethod[] method() {
				return new RequestMethod[] { RequestMethod.GET };
			}
			
			@Override
			public String[] headers() {
				return mapping.headers();
			}
			
			@Override
			public String[] consumes() {
				return mapping.consumes();
			}
		};
	}

	@Override
	public void postProcessBeanFactory(ConfigurableListableBeanFactory factory) throws BeansException {
		RequestMappingHandlerMapping object = factory.getBean(RequestMappingHandlerMapping.class);
		//MetaObject metaObject = MetaObject.forObject(factory.getBean(RequestMappingHandlerMapping.class), objectFactory, objectWrapperFactory)
		MetaObject metaObject = SystemMetaObject.forObject(object);
		
		ObjectWrapper wrapperObject = metaObject.getObjectWrapper();
		
		Map<String, Object> classes = factory.getBeansWithAnnotation(Controller.class);
		for (Map.Entry<String, Object> entry : classes.entrySet()) {
			
			Class<?> clazz = entry.getValue().getClass();
			Method[] methods = clazz.getDeclaredMethods();
			for (Method method : methods) {
				Map<RequestMappingInfo,HandlerMethod> map = new LinkedHashMap<>();
				final GetMapping getMapping = method.getAnnotation(GetMapping.class);
				if (null != getMapping) {
					
					//Map<RequestMappingInfo,HandlerMethod>, MultiValueMap<String,RequestMappingInfo>, HandlerMethodMappingNamingStrategy<RequestMappingInfo>, 
					//MultiValueMap<String,HandlerMethod>, ApplicationContext, PathMatcher, Object, Method, RequestMappingInfo
					registerHandlerMethod(
							(Log) metaObject.getValue("logger"),
							map, 
							(LinkedMultiValueMap<String,RequestMappingInfo>) metaObject.getValue("urlMap"), 
							(HandlerMethodMappingNamingStrategy<RequestMappingInfo>) metaObject.getValue("namingStrategy"), 
							(LinkedMultiValueMap<String,HandlerMethod>) metaObject.getValue("nameMap"), 
							(ApplicationContext) metaObject.getValue("applicationContext"), 
							(PathMatcher) metaObject.getValue("pathMatcher"), 
							entry.getKey(), method, 
							//GetMapping, Method, Class<?>, UrlPathHelper, PathMatcher, boolean, boolean, List<String>, ContentNegotiationManager
							getMappingForMethod(getMapping, method, clazz, 
									(UrlPathHelper) metaObject.getValue("urlPathHelper"), 
									(PathMatcher) metaObject.getValue("pathMatcher"), 
									(boolean) metaObject.getValue("useSuffixPatternMatch"), 
									(boolean) metaObject.getValue("useTrailingSlashMatch"), 
									(List<String>) metaObject.getValue("fileExtensions"), 
									(ContentNegotiationManager) metaObject.getValue("contentNegotiationManager"))
							);
				}
				
				final PostMapping postMapping = method.getAnnotation(PostMapping.class);
				if (null != postMapping) {
					//Map<RequestMappingInfo,HandlerMethod>, MultiValueMap<String,RequestMappingInfo>, HandlerMethodMappingNamingStrategy<RequestMappingInfo>, 
					//MultiValueMap<String,HandlerMethod>, ApplicationContext, PathMatcher, Object, Method, RequestMappingInfo
					registerHandlerMethod(
							(Log) metaObject.getValue("logger"),
							map, 
							(MultiValueMap<String,RequestMappingInfo>) metaObject.getValue("urlMap"), 
							(HandlerMethodMappingNamingStrategy<RequestMappingInfo>) metaObject.getValue("namingStrategy"), 
							(MultiValueMap<String,HandlerMethod>) metaObject.getValue("nameMap"), 
							(ApplicationContext) metaObject.getValue("applicationContext"), 
							(PathMatcher) metaObject.getValue("pathMatcher"), 
							entry.getKey(), method, 
							//GetMapping, Method, Class<?>, UrlPathHelper, PathMatcher, boolean, boolean, List<String>, ContentNegotiationManager
							getMappingForMethod(postMapping, method, clazz, 
									(UrlPathHelper) metaObject.getValue("urlPathHelper"), 
									(PathMatcher) metaObject.getValue("pathMatcher"), 
									(boolean) metaObject.getValue("useSuffixPatternMatch"), 
									(boolean) metaObject.getValue("useTrailingSlashMatch"), 
									(List<String>) metaObject.getValue("fileExtensions"), 
									(ContentNegotiationManager) metaObject.getValue("contentNegotiationManager"))
							);
				}

				Map<RequestMappingInfo,HandlerMethod> src = (Map<RequestMappingInfo,HandlerMethod>) metaObject.getValue("handlerMethods");
				if (null != src) {
					for (Map.Entry<RequestMappingInfo, HandlerMethod> e : src.entrySet()) {
						map.put(e.getKey(), e.getValue());
					}
				}
				
				//metaObject.getValue("handlerMethods");
				metaObject.setValue("handlerMethods", map);
				
//				final RequestMapping requestMapping = method.getAnnotation(RequestMapping.class);
//				if (null != requestMapping) {
//					if (null != postMapping) {
//						//Map<RequestMappingInfo,HandlerMethod>, MultiValueMap<String,RequestMappingInfo>, HandlerMethodMappingNamingStrategy<RequestMappingInfo>, 
//						//MultiValueMap<String,HandlerMethod>, ApplicationContext, PathMatcher, Object, Method, RequestMappingInfo
//						registerHandlerMethod(
//								map, 
//								(MultiValueMap<String,RequestMappingInfo>) metaObject.getValue("urlMap"), 
//								(HandlerMethodMappingNamingStrategy<RequestMappingInfo>) metaObject.getValue("namingStrategy"), 
//								(MultiValueMap<String,HandlerMethod>) metaObject.getValue("nameMap"), 
//								(ApplicationContext) metaObject.getValue("applicationContext"), 
//								(PathMatcher) metaObject.getValue("pathMatcher"), 
//								entry.getKey(), method, 
//								//GetMapping, Method, Class<?>, UrlPathHelper, PathMatcher, boolean, boolean, List<String>, ContentNegotiationManager
//								getMappingForMethod(postMapping, method, clazz, 
//										(UrlPathHelper) metaObject.getValue("urlPathHelper"), 
//										(PathMatcher) metaObject.getValue("pathMatcher"), 
//										(boolean) metaObject.getValue("useSuffixPatternMatch"), 
//										(boolean) metaObject.getValue("useTrailingSlashMatch"), 
//										(List<String>) metaObject.getValue("fileExtensions"), 
//										(ContentNegotiationManager) metaObject.getValue("contentNegotiationManager"))
//								);
//					}
//				}
			}
			
		}
		
//		
//		//Object handler, Method method, T mapping
//		try {
//			Method[] methods = RequestMappingHandlerMapping.class.getDeclaredMethods();
//			for (Method method : methods) {
//				System.out.println(method);
//			}
////			Method m = RequestMappingHandlerMapping.class.getDeclaredMethod("registerHandlerMethod", 
////								new Class[] {Object.class, Method.class, Object.class});
////			System.err.println(m);
//		} catch (Exception e) {
//			e.printStackTrace();
//		}
//		System.err.println(metaObject);
//		System.err.println();
//		System.err.println();
//		System.err.println();
//		String[] beanNames = factory.getBeanDefinitionNames();
//		for (String beanName : beanNames) {
//			System.err.println(beanName + ":" + factory.getBean(beanName));
//			
//		}
//		System.err.println(factory.getBeansWithAnnotation(Controller.class));
//		Map<String, Object> classes = factory.getBeansWithAnnotation(Controller.class);
//		for (Map.Entry<String, Object> entry : classes.entrySet()) {
//			Class<?> clazz = (Class<?>) entry.getValue();
//			Method[] methods = clazz.getDeclaredMethods();
//			for (Method method : methods) {
//				final GetMapping getmapping = method.getAnnotation(GetMapping.class);
//				RequestMappingHandlerMapping rmhm;
//			}
//			
//		}
	}

}


```
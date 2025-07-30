---
title: Spring
tags:
  - Java
categories:
  - Java
  - Spring
cover: https://image.runtimes.cc/202404051534866.png
abbrlink: 18155
date: 2023-08-25 14:58:26
updated: 2023-08-25 14:58:26
---


# SpringMVC

![](https://image.runtimes.cc/202404051458028.png)

总结： 

1. 首先请求进入DispatcherServlet 由DispatcherServlet 从HandlerMappings中提取对应的Handler。 
2. 2.此时只是获取到了对应的Handle，然后得去寻找对应的适配器，即：HandlerAdapter。 
3. 拿到对应HandlerAdapter时，这时候开始调用对应的Handler处理业务逻辑了。 （这时候实际上已经执行完了我们的Controller） 执行完成之后返回一个ModeAndView 
4. 这时候交给我们的ViewResolver通过视图名称查找出对应的视图然后返回。 
5. 最后 渲染视图 返回渲染后的视图 -->响应请求。

## 初始化过程

version 5.3.8

```java
// org.springframework.web.servlet.HttpServletBean#init
@Override
public final void init() throws ServletException {
  // 子类实现,初始化web环境
  // Let subclasses do whatever initialization they like.
  initServletBean();
}
```

```java
// org.springframework.web.servlet.FrameworkServlet#initServletBean
@Override
protected final void initServletBean() throws ServletException {
    // 初始化spring上下文
    this.webApplicationContext = initWebApplicationContext();
    // 子类实现
    initFrameworkServlet();
}
```

```java
// org.springframework.web.servlet.FrameworkServlet#initWebApplicationContext
protected WebApplicationContext initWebApplicationContext() {
		WebApplicationContext rootContext =
				WebApplicationContextUtils.getWebApplicationContext(getServletContext());
		WebApplicationContext wac = null;

		if (this.webApplicationContext != null) {
			// A context instance was injected at construction time -> use it
			wac = this.webApplicationContext;
			if (wac instanceof ConfigurableWebApplicationContext) {
				ConfigurableWebApplicationContext cwac = (ConfigurableWebApplicationContext) wac;
				if (!cwac.isActive()) {
					// The context has not yet been refreshed -> provide services such as
					// setting the parent context, setting the application context id, etc
					if (cwac.getParent() == null) {
						// The context instance was injected without an explicit parent -> set
						// the root application context (if any; may be null) as the parent
						cwac.setParent(rootContext);
					}
          
          //配置和刷新spring容器（重要）
					//这个无非就是初始化spring ioc的环境，创建bean和实例化bean等操作
					//这个方法最终也是调用refresh()方法，已在spring源码解析中解析过了
					configureAndRefreshWebApplicationContext(cwac);
				}
			}
		}
		if (wac == null) {
			// No context instance was injected at construction time -> see if one
			// has been registered in the servlet context. If one exists, it is assumed
			// that the parent context (if any) has already been set and that the
			// user has performed any initialization such as setting the context id
			wac = findWebApplicationContext();
		}
		if (wac == null) {
			// No context instance is defined for this servlet -> create a local one
			wac = createWebApplicationContext(rootContext);
		}

		if (!this.refreshEventReceived) {
			// Either the context is not a ConfigurableApplicationContext with refresh
			// support or the context injected at construction time had already been
			// refreshed -> trigger initial onRefresh manually here.
			synchronized (this.onRefreshMonitor) {
        // 初始化DispatcherServlet的配置initStrategies()
				onRefresh(wac);
			}
		}

		if (this.publishContext) {
			// Publish the context as a servlet context attribute.
			String attrName = getServletContextAttributeName();
			getServletContext().setAttribute(attrName, wac);
		}

		return wac;
	}
```

```java
// org.springframework.web.servlet.DispatcherServlet#onRefresh	
protected void onRefresh(ApplicationContext context) {
    // //初始化springmvc的配置
		initStrategies(context);
	}
```
总体流程:

* 执行DispatcherServlet的init()方法，

* 会执行父类的HttpServletBean的init()方法

* 然后调用了FrameworkServlet的initServletBean()方法

> 没看懂,执行initWebApplicationContext()方法，就是对spring ioc环境的初始化。那么这里就衍生出了一个面试题：spring容器和spring mvc的容器的区别？通过源码的分析，spring和spring mvc底层，都是调用了同一个refresh()方法，所以spring容器和spring mvc容器是没有区别的，都是指的是同一个容器。
>
> （3）执行到onRefresh()方法，就是开始初始化DispatcherServlet了，也就是开始初始化spring mvc。

```java
// org.springframework.web.servlet.DispatcherServlet#initStrategies	
protected void initStrategies(ApplicationContext context) {
  	//上传文件
		initMultipartResolver(context);
  	//国际化
		initLocaleResolver(context);
  	//前段的主题样式
		initThemeResolver(context);
  	//初始化HandlerMappings（请求映射器）重点
		initHandlerMappings(context);
  	// 初始化HandlerAdapters（处理适配器）
		initHandlerAdapters(context);
		initHandlerExceptionResolvers(context);
		initRequestToViewNameTranslator(context);
  	//视图转换器
		initViewResolvers(context);
  	//重定向数据管理器
		initFlashMapManager(context);
	}
```

```java
// org.springframework.web.servlet.DispatcherServlet#initHandlerMappings
private void initHandlerMappings(ApplicationContext context) {
  
  // Ensure we have at least one HandlerMapping, by registering
  // a default HandlerMapping if no other mappings are found.
  // 通过配置文件中的配置信息，得到handlerMappings
  if (this.handlerMappings == null) {
    this.handlerMappings = getDefaultStrategies(context, HandlerMapping.class);
    if (logger.isTraceEnabled()) {
      logger.trace("No HandlerMappings declared for servlet '" + getServletName() +
          "': using default strategies from DispatcherServlet.properties");
    }
  }
}
```

```java
// org.springframework.web.servlet.DispatcherServlet#getDefaultStrategies

private static final String DEFAULT_STRATEGIES_PATH = "DispatcherServlet.properties";

protected <T> List<T> getDefaultStrategies(ApplicationContext context, Class<T> strategyInterface) {
  
  	if (defaultStrategies == null) {
			try {
				// Load default strategy implementations from properties file.
				// This is currently strictly internal and not meant to be customized
				// by application developers.
        /**
         * 从属性文件加载默认策略实现
         * 说白了这里的意思就是从DEFAULT_STRATEGIES_PATH这个文件当中拿出所有的配置
         * 可以去数一下一共有8个： DispatcherServlet.properties == DEFAULT_STRATEGIES_PATH
         */
				ClassPathResource resource = new ClassPathResource(DEFAULT_STRATEGIES_PATH, DispatcherServlet.class);
				defaultStrategies = PropertiesLoaderUtils.loadProperties(resource);
			}
			catch (IOException ex) {
				throw new IllegalStateException("Could not load '" + DEFAULT_STRATEGIES_PATH + "': " + ex.getMessage());
			}
		}
  
		String key = strategyInterface.getName(); 
  	// defaultStrategies 是DispatcherServlet.properties 配置文件,在static静态代码块初始化
  	// 版本变了,不是从静态方法中获取到的
		String value = defaultStrategies.getProperty(key);
		if (value != null) {
			String[] classNames = StringUtils.commaDelimitedListToStringArray(value);
			List<T> strategies = new ArrayList<>(classNames.length);
			for (String className : classNames) {
				try {
          // 获取class字节码文件
					Class<?> clazz = ClassUtils.forName(className, DispatcherServlet.class.getClassLoader());
          // 底层是通过调用spring的getBean的方式创建该对象（可以进行bean的属性装配）
					// 请求映射就是在这个方法实现装配的
					Object strategy = createDefaultStrategy(context, clazz);
					strategies.add((T) strategy);
				}
				catch (ClassNotFoundException ex) {
					throw new BeanInitializationException(
							"Could not find DispatcherServlet's default strategy class [" + className +
							"] for interface [" + key + "]", ex);
				}
				catch (LinkageError err) {
					throw new BeanInitializationException(
							"Unresolvable class definition for DispatcherServlet's default strategy class [" +
							className + "] for interface [" + key + "]", err);
				}
			}
			return strategies;
		}
		else {
			return Collections.emptyList();
		}
	}
```

DispatcherServlet.properties

从DispatcherServlet.properties配置文件，可以看出handlerMapping默认是有两个： 

1.BeanNameUrlHandlerMapping （主要处理object） 

2.RequestMappingHandlerMapping（主要处理method）

```properties
# Default implementation classes for DispatcherServlet's strategy interfaces.
# Used as fallback when no matching beans are found in the DispatcherServlet context.
# Not meant to be customized by application developers.

org.springframework.web.servlet.LocaleResolver=org.springframework.web.servlet.i18n.AcceptHeaderLocaleResolver

org.springframework.web.servlet.ThemeResolver=org.springframework.web.servlet.theme.FixedThemeResolver


// HandlerMapping
org.springframework.web.servlet.HandlerMapping=org.springframework.web.servlet.handler.BeanNameUrlHandlerMapping,\
	org.springframework.web.servlet.mvc.method.annotation.RequestMappingHandlerMapping,\
	org.springframework.web.servlet.function.support.RouterFunctionMapping

// HandlerAdapter
org.springframework.web.servlet.HandlerAdapter=org.springframework.web.servlet.mvc.HttpRequestHandlerAdapter,\
	org.springframework.web.servlet.mvc.SimpleControllerHandlerAdapter,\
	org.springframework.web.servlet.mvc.method.annotation.RequestMappingHandlerAdapter,\
	org.springframework.web.servlet.function.support.HandlerFunctionAdapter


org.springframework.web.servlet.HandlerExceptionResolver=org.springframework.web.servlet.mvc.method.annotation.ExceptionHandlerExceptionResolver,\
	org.springframework.web.servlet.mvc.annotation.ResponseStatusExceptionResolver,\
	org.springframework.web.servlet.mvc.support.DefaultHandlerExceptionResolver

org.springframework.web.servlet.RequestToViewNameTranslator=org.springframework.web.servlet.view.DefaultRequestToViewNameTranslator

org.springframework.web.servlet.ViewResolver=org.springframework.web.servlet.view.InternalResourceViewResolver

org.springframework.web.servlet.FlashMapManager=org.springframework.web.servlet.support.SessionFlashMapManager
```

(1) initHandlerMappings方法，就是初始化我们的handlerMapping（请求映射器）。

(2) handlerMapping的主要作用是，找到请求路径对应的controller的方法。

> 例如：请求的路径 "/index"，然后这个handlerMapping，在初始化的时候，已经将所有controller的请求路径映射保存在一个map集合，当请求过来的时候，就将"/index"作为一个key，从map集合中找到对应的controller的index方法。

(3) 这里初始化handlerMappings ，默认是有两个handlerMappings ，是直接在defaultStrategies配置文件中获取。 

(4) 那么defaultStrategies的值是什么时候初始化的呢？

> 通过查看源码，defaultStrategies这个值，是DispatcherServlet类的静态代码块初始化的。 全世界都知道，当一个类被初始化的时候，会执行该类的static静态代码块的。

## 请求阶段分析

用户的一个请求过来，会由servlet接收到，然后一步一步调用到DispatcherServlet的doService方法。

```java
// org.springframework.web.servlet.DispatcherServlet#doService
@Override
protected void doService(HttpServletRequest request, HttpServletResponse response) throws Exception {
		// 核心方法（重点）
    doDispatch(request, response);
}
```

```java
// org.springframework.web.servlet.DispatcherServlet#doDispatch
protected void doDispatch(HttpServletRequest request, HttpServletResponse response) throws Exception {
		HttpServletRequest processedRequest = request;
		HandlerExecutionChain mappedHandler = null;
		boolean multipartRequestParsed = false;

  	// 异步编程
		WebAsyncManager asyncManager = WebAsyncUtils.getAsyncManager(request);

		try {
      // 定义变量,哈哈哈,好熟悉呀
			ModelAndView mv = null;
			Exception dispatchException = null;

			try {
        //检查请求中是否有文件上传操作
				processedRequest = checkMultipart(request);
				multipartRequestParsed = (processedRequest != request);

				// Determine handler for the current request.
				// 确定当前请求的处理程序（重点），推断controller和handler的类型，
        // 进到这里的getHandler方法
				mappedHandler = getHandler(processedRequest);
				if (mappedHandler == null) {
					noHandlerFound(processedRequest, response);
					return;
				}

				// Determine handler adapter for the current request.
        //推断适配器，不同的controller类型，交给不同的适配器去处理
				//如果是一个bean，mappedHandler.getHandler()返回的是一个对象
				//如果是一个method，mappedHandler.getHandler()返回的是一个方法	
				HandlerAdapter ha = getHandlerAdapter(mappedHandler.getHandler());
        //到这里，spring才确定我要怎么反射调用

				// Process last-modified header, if supported by the handler.
				String method = request.getMethod();
				boolean isGet = HttpMethod.GET.matches(method);
				if (isGet || HttpMethod.HEAD.matches(method)) {
					long lastModified = ha.getLastModified(request, mappedHandler.getHandler());
					if (new ServletWebRequest(request, response).checkNotModified(lastModified) && isGet) {
						return;
					}
				}

        // 前置拦截器处理
				if (!mappedHandler.applyPreHandle(processedRequest, response)) {
					return;
				}

				//通过适配器，处理请求（可以理解为，反射调用方法）（重点）
				// Actually invoke the handler.
				mv = ha.handle(processedRequest, response, mappedHandler.getHandler());

				if (asyncManager.isConcurrentHandlingStarted()) {
					return;
				}

				applyDefaultViewName(processedRequest, mv);
				mappedHandler.applyPostHandle(processedRequest, response, mv);
			}
			catch (Exception ex) {
				dispatchException = ex;
			}
			catch (Throwable err) {
				// As of 4.3, we're processing Errors thrown from handler methods as well,
				// making them available for @ExceptionHandler methods and other scenarios.
				dispatchException = new NestedServletException("Handler dispatch failed", err);
			}
			processDispatchResult(processedRequest, response, mappedHandler, mv, dispatchException);
		}
		catch (Exception ex) {
			triggerAfterCompletion(processedRequest, response, mappedHandler, ex);
		}
		catch (Throwable err) {
			triggerAfterCompletion(processedRequest, response, mappedHandler,
					new NestedServletException("Handler processing failed", err));
		}
	}
```

通过对DispatcherServlet的分析，得到请求的核心处理方法是doDispatch()，

主要是分了几步： 

(1) 检查请求中是否有文件上传操作

 (2) 确定当前请求的处理的handler（重点）

 (3) 推断适配器，不同的controller类型，交给不同的适配器去处理

 (4) 执行前置拦截器处理interceptor

 (5) 通过找到的HandlerAdapter ，反射执行相关的业务代码controller的方法。

 (6) 返回结果。

```java
// org.springframework.web.servlet.DispatcherServlet#getHandler
@Nullable
protected HandlerExecutionChain getHandler(HttpServletRequest request) throws Exception {
  if (this.handlerMappings != null) {
    //循环所有的HandlerMappings
		//this.handlerMappings这个是什么时候初始化的？（重点）
		//在handlerMappings初始化的时候
    for (HandlerMapping mapping : this.handlerMappings) {
      //把请求传过去看能不能得到一个handler
			//注意：怎么得到handler和handlerMapping自己实现的逻辑有关系
      HandlerExecutionChain handler = mapping.getHandler(request);
      if (handler != null) {
        return handler;
      }
    }
  }
  return null;
}
```

```java
// org.springframework.web.servlet.handler.AbstractHandlerMapping#getHandler
@Override
@Nullable
public final HandlerExecutionChain getHandler(HttpServletRequest request) throws Exception {
  //获取handler（重点）
  Object handler = getHandlerInternal(request);
  if (handler == null) {
    handler = getDefaultHandler();
  }
  if (handler == null) {
    return null;
  }
  // Bean name or resolved handler?
  if (handler instanceof String) {
    String handlerName = (String) handler;
    handler = obtainApplicationContext().getBean(handlerName);
  }

  // Ensure presence of cached lookupPath for interceptors and others
  if (!ServletRequestPathUtils.hasCachedPath(request)) {
    initLookupPath(request);
  }

  HandlerExecutionChain executionChain = getHandlerExecutionChain(handler, request);

  if (logger.isTraceEnabled()) {
    logger.trace("Mapped to " + handler);
  }
  else if (logger.isDebugEnabled() && !DispatcherType.ASYNC.equals(request.getDispatcherType())) {
    logger.debug("Mapped to " + executionChain.getHandler());
  }

  if (hasCorsConfigurationSource(handler) || CorsUtils.isPreFlightRequest(request)) {
    CorsConfiguration config = getCorsConfiguration(handler, request);
    if (getCorsConfigurationSource() != null) {
      CorsConfiguration globalConfig = getCorsConfigurationSource().getCorsConfiguration(request);
      config = (globalConfig != null ? globalConfig.combine(config) : config);
    }
    if (config != null) {
      config.validateAllowCredentials();
    }
    executionChain = getCorsHandlerExecutionChain(request, executionChain, config);
  }

  return executionChain;
}
```

(1) getHandler()方法，主要是遍历在DispatcherServlet初始化是，初始化的handlerMappings。 

(2) 这个方法的主要思想是，通过request的路径，去匹配对应的controller去处理。 

(3) SpringMVC自己自带了2个HandlerMapping 来供我们选择 至于 为什么要有2个呢？

### 两种注册Controller的方式

我们用2种方式来注册Controller 分别是：

(1) 作为Bean的形式：实现Controller接口，重写handleRequest方法，请求路径为"/test"

```java
@Component("/test")
public class TesrController implements org.springframework.web.servlet.mvc.Controller{
	@Override
	public ModelAndView handleRequest(HttpServletRequest request, 
	HttpServletResponse	response) throws Exception {
		System.out.println("1");
		return null;
	}
}
```

(2) 以Annotation形式：

```java
@Controller
public class AnnotationController {
	@RequestMapping("/test2")
	public Object test(){
		System.out.println("test");
		return null;
	}
}
```

经过测试:

(1)可以得到以Bean方式的controller，是通过BeanNameUrlHandlerMapping去匹配

(2)以注解方法的controller，是通过RequestMappingHandlerMapping去匹配

#### BeanNameUrlHandlerMapping

BeanNameUrlHandlerMapping处理bean方式的源码分析：

```java
// org.springframework.web.servlet.handler.AbstractUrlHandlerMapping#getHandlerInternal
  @Override
	@Nullable
	protected Object getHandlerInternal(HttpServletRequest request) throws Exception {
    // 获取请求的路径
		String lookupPath = initLookupPath(request);
    // 到对应的handler（重点）调用 lookupHandler()
		Object handler;
		if (usesPathPatterns()) {
			RequestPath path = ServletRequestPathUtils.getParsedRequestPath(request);
			handler = lookupHandler(path, lookupPath, request);
		}
		else {
			handler = lookupHandler(lookupPath, request);
		}
		if (handler == null) {
			// We need to care for the default handler directly, since we need to
			// expose the PATH_WITHIN_HANDLER_MAPPING_ATTRIBUTE for it as well.
			Object rawHandler = null;
			if (StringUtils.matchesCharacter(lookupPath, '/')) {
				rawHandler = getRootHandler();
			}
			if (rawHandler == null) {
				rawHandler = getDefaultHandler();
			}
			if (rawHandler != null) {
				// Bean name or resolved handler?
				if (rawHandler instanceof String) {
					String handlerName = (String) rawHandler;
					rawHandler = obtainApplicationContext().getBean(handlerName);
				}
				validateHandler(rawHandler, request);
				handler = buildPathExposingHandler(rawHandler, lookupPath, lookupPath, null);
			}
		}
		return handler;
	}
```

```java
// org.springframework.web.servlet.handler.AbstractUrlHandlerMapping#lookupHandler(java.lang.String, javax.servlet.http.HttpServletRequest)
@Nullable
	protected Object lookupHandler(String lookupPath, HttpServletRequest request) throws Exception {
    // 查看这里的方法
		Object handler = getDirectMatch(lookupPath, request);
		if (handler != null) {
			return handler;
		}

		// Pattern match?
		List<String> matchingPatterns = new ArrayList<>();
		for (String registeredPattern : this.handlerMap.keySet()) {
			if (getPathMatcher().match(registeredPattern, lookupPath)) {
				matchingPatterns.add(registeredPattern);
			}
			else if (useTrailingSlashMatch()) {
				if (!registeredPattern.endsWith("/") && getPathMatcher().match(registeredPattern + "/", lookupPath)) {
					matchingPatterns.add(registeredPattern + "/");
				}
			}
		}

		String bestMatch = null;
		Comparator<String> patternComparator = getPathMatcher().getPatternComparator(lookupPath);
		if (!matchingPatterns.isEmpty()) {
			matchingPatterns.sort(patternComparator);
			if (logger.isTraceEnabled() && matchingPatterns.size() > 1) {
				logger.trace("Matching patterns " + matchingPatterns);
			}
			bestMatch = matchingPatterns.get(0);
		}
		if (bestMatch != null) {
			handler = this.handlerMap.get(bestMatch);
			if (handler == null) {
				if (bestMatch.endsWith("/")) {
					handler = this.handlerMap.get(bestMatch.substring(0, bestMatch.length() - 1));
				}
				if (handler == null) {
					throw new IllegalStateException(
							"Could not find handler for best pattern match [" + bestMatch + "]");
				}
			}
			// Bean name or resolved handler?
			if (handler instanceof String) {
				String handlerName = (String) handler;
				handler = obtainApplicationContext().getBean(handlerName);
			}
			validateHandler(handler, request);
			String pathWithinMapping = getPathMatcher().extractPathWithinPattern(bestMatch, lookupPath);

			// There might be multiple 'best patterns', let's make sure we have the correct URI template variables
			// for all of them
			Map<String, String> uriTemplateVariables = new LinkedHashMap<>();
			for (String matchingPattern : matchingPatterns) {
				if (patternComparator.compare(bestMatch, matchingPattern) == 0) {
					Map<String, String> vars = getPathMatcher().extractUriTemplateVariables(matchingPattern, lookupPath);
					Map<String, String> decodedVars = getUrlPathHelper().decodePathVariables(request, vars);
					uriTemplateVariables.putAll(decodedVars);
				}
			}
			if (logger.isTraceEnabled() && uriTemplateVariables.size() > 0) {
				logger.trace("URI variables " + uriTemplateVariables);
			}
			return buildPathExposingHandler(handler, bestMatch, pathWithinMapping, uriTemplateVariables);
		}

		// No handler found...
		return null;
	}
```

```java
// org.springframework.web.servlet.handler.AbstractUrlHandlerMapping#getDirectMatch
@Nullable
	private Object getDirectMatch(String urlPath, HttpServletRequest request) throws Exception {
    // 通过请求的路径，在handlerMap中去匹配。
		// handlerMap这个值，什么时候填充值？在init初始化的时候，就已经存放在这个handlerMap种
		Object handler = this.handlerMap.get(urlPath);
		if (handler != null) {
			// Bean name or resolved handler?
			if (handler instanceof String) {
				String handlerName = (String) handler;
				handler = obtainApplicationContext().getBean(handlerName);
			}
			validateHandler(handler, request);
			return buildPathExposingHandler(handler, urlPath, urlPath, null);
		}
		return null;
	}
```

(1) 以Bean方式的controller，匹配请求的路径，是通过一个handlerMap去匹配，比较简单。

(2) 这里的问题是，这个handlerMap的值，是什么时候放进去的？

> 通过源码分析，BeanNameUrlHandlerMapping是实现了ApplicationContextAware接口。 如果你精通spring的源码，就知道spring的实例bean的时候，会回调这些类的setApplicationContext()方法。

```java
// org.springframework.context.support.ApplicationObjectSupport#setApplicationContext
@Override
	public final void setApplicationContext(@Nullable ApplicationContext context) throws BeansException {
		if (context == null && !isContextRequired()) {
			// Reset internal context state.
			this.applicationContext = null;
			this.messageSourceAccessor = null;
		}
		else if (this.applicationContext == null) {
			// Initialize with passed-in context.
			if (!requiredContextClass().isInstance(context)) {
				throw new ApplicationContextException(
						"Invalid application context: needs to be of type [" + requiredContextClass().getName() + "]");
			}
			this.applicationContext = context;
			this.messageSourceAccessor = new MessageSourceAccessor(context);
			// 初始化ApplicationContext，就会执行到子类的方法（重点）
			initApplicationContext(context);
		}
		else {
			// Ignore reinitialization if same context passed in.
			if (this.applicationContext != context) {
				throw new ApplicationContextException(
						"Cannot reinitialize with different application context: current one is [" +
						this.applicationContext + "], passed-in one is [" + context + "]");
			}
		}
	}
```

```java
// 没看懂怎么走到这里来呢
// org.springframework.web.servlet.handler.AbstractDetectingUrlHandlerMapping#initApplicationContext
	@Override
	public void initApplicationContext() throws ApplicationContextException {
		super.initApplicationContext();
    // 检测出handler
		detectHandlers();
	}
  
```

```java
// org.springframework.web.servlet.handler.AbstractDetectingUrlHandlerMapping#detectHandlers
	protected void detectHandlers() throws BeansException {
    // 获取spring ioc所有的beanName，然后判断beanName，那些是以 "/" 开头
		ApplicationContext applicationContext = obtainApplicationContext();
		String[] beanNames = (this.detectHandlersInAncestorContexts ?
				BeanFactoryUtils.beanNamesForTypeIncludingAncestors(applicationContext, Object.class) :
				applicationContext.getBeanNamesForType(Object.class));

		// Take any bean name that we can determine URLs for.
		for (String beanName : beanNames) {
      // 然后判断beanName，那些是以 "/" 开头
			String[] urls = determineUrlsForHandler(beanName);
			if (!ObjectUtils.isEmpty(urls)) {
        // 注册handler（重点）
				// URL paths found: Let's consider it a handler.
				registerHandler(urls, beanName);
			}
		}
	}
```



```java
// org.springframework.web.servlet.handler.AbstractUrlHandlerMapping#registerHandler(java.lang.String[], java.lang.String)	
protected void registerHandler(String[] urlPaths, String beanName) throws BeansException, IllegalStateException {
		Assert.notNull(urlPaths, "URL path array must not be null");
		for (String urlPath : urlPaths) {
			registerHandler(urlPath, beanName);
		}
	}
```



```java
// org.springframework.web.servlet.handler.AbstractUrlHandlerMapping#registerHandler(java.lang.String, java.lang.Object)
protected void registerHandler(String urlPath, Object handler) throws BeansException, IllegalStateException {
		Assert.notNull(urlPath, "URL path must not be null");
		Assert.notNull(handler, "Handler object must not be null");
		Object resolvedHandler = handler;

		// Eagerly resolve handler if referencing singleton via name.
		if (!this.lazyInitHandlers && handler instanceof String) {
			String handlerName = (String) handler;
			ApplicationContext applicationContext = obtainApplicationContext();
			if (applicationContext.isSingleton(handlerName)) {
				resolvedHandler = applicationContext.getBean(handlerName);
			}
		}

		Object mappedHandler = this.handlerMap.get(urlPath);
		if (mappedHandler != null) {
			if (mappedHandler != resolvedHandler) {
				throw new IllegalStateException(
						"Cannot map " + getHandlerDescription(handler) + " to URL path [" + urlPath +
						"]: There is already " + getHandlerDescription(mappedHandler) + " mapped.");
			}
		}
		else {
			if (urlPath.equals("/")) {
				if (logger.isTraceEnabled()) {
					logger.trace("Root mapping to " + getHandlerDescription(handler));
				}
				setRootHandler(resolvedHandler);
			}
			else if (urlPath.equals("/*")) {
				if (logger.isTraceEnabled()) {
					logger.trace("Default mapping to " + getHandlerDescription(handler));
				}
				setDefaultHandler(resolvedHandler);
			}
			else {
        // 最终put到map集合中（省略其他无关代码）
				this.handlerMap.put(urlPath, resolvedHandler);
				if (getPatternParser() != null) {
					this.pathPatternHandlerMap.put(getPatternParser().parse(urlPath), resolvedHandler);
				}
				if (logger.isTraceEnabled()) {
					logger.trace("Mapped [" + urlPath + "] onto " + getHandlerDescription(handler));
				}
			}
		}
	}
```

> BeanNameUrlHandlerMapping处理bean方式的源码分析，其实是很简单： 
>
> (1) 在类初始化的时候，就已经将所有实现了Controller接口的controller类，拿到他们的@Componet('/test') 
>
> (2) 然后将'/test'这个作为key，controller类作为value，放入到一个map集合。 
>
> (3) 当一个请求过来的时候，拿到这个请求的uri，在map里面找，找到了即表示匹配上

#### RequestMappingHandlerMapping

处理注解方式的源码分析：

```java
// org.springframework.web.servlet.handler.AbstractHandlerMethodMapping#getHandlerInternal
// 对于RequestMappingHandlerMapping，indexController.index()，方法的请求路径映射
	@Override
	@Nullable
	protected HandlerMethod getHandlerInternal(HttpServletRequest request) throws Exception {
    // 获取请求路径
		String lookupPath = initLookupPath(request);
		this.mappingRegistry.acquireReadLock();
		try {
      // 通过请求路径，获取handler
			HandlerMethod handlerMethod = lookupHandlerMethod(lookupPath, request);
			return (handlerMethod != null ? handlerMethod.createWithResolvedBean() : null);
		}
		finally {
			this.mappingRegistry.releaseReadLock();
		}
	}
```



```java
// org.springframework.web.servlet.handler.AbstractHandlerMethodMapping#lookupHandlerMethod
@Nullable
	protected HandlerMethod lookupHandlerMethod(String lookupPath, HttpServletRequest request) throws Exception {
		List<Match> matches = new ArrayList<>();
    // 从mappingRegistry的urlLookup，匹配请求路径
		List<T> directPathMatches = this.mappingRegistry.getMappingsByDirectPath(lookupPath);
		if (directPathMatches != null) {
			addMatchingMappings(directPathMatches, matches, request);
		}
		if (matches.isEmpty()) {
			addMatchingMappings(this.mappingRegistry.getRegistrations().keySet(), matches, request);
		}
		if (!matches.isEmpty()) {
			Match bestMatch = matches.get(0);
			if (matches.size() > 1) {
				Comparator<Match> comparator = new MatchComparator(getMappingComparator(request));
				matches.sort(comparator);
				bestMatch = matches.get(0);
				if (logger.isTraceEnabled()) {
					logger.trace(matches.size() + " matching mappings: " + matches);
				}
				if (CorsUtils.isPreFlightRequest(request)) {
					for (Match match : matches) {
						if (match.hasCorsConfig()) {
							return PREFLIGHT_AMBIGUOUS_MATCH;
						}
					}
				}
				else {
					Match secondBestMatch = matches.get(1);
					if (comparator.compare(bestMatch, secondBestMatch) == 0) {
						Method m1 = bestMatch.getHandlerMethod().getMethod();
						Method m2 = secondBestMatch.getHandlerMethod().getMethod();
						String uri = request.getRequestURI();
						throw new IllegalStateException(
								"Ambiguous handler methods mapped for '" + uri + "': {" + m1 + ", " + m2 + "}");
					}
				}
			}
			request.setAttribute(BEST_MATCHING_HANDLER_ATTRIBUTE, bestMatch.getHandlerMethod());
			handleMatch(bestMatch.mapping, lookupPath, request);
      // 返回handler
			return bestMatch.getHandlerMethod();
		}
		else {
			return handleNoMatch(this.mappingRegistry.getRegistrations().keySet(), lookupPath, request);
		}
	}
```

```java
// 3.AbstractHandlerMethodMapping.MappingRegistry#getMappingsByDirectPath
@Nullable
public List<T> getMappingsByDirectPath(String urlPath) {
  return this.pathLookup.get(urlPath);
}
```

RequestMappingHandlerMapping处理注解方式的源码分析，比较复杂，用一个MappingRegistry维护所有的请求路径映射。

MappingRegistry的初始化，也是在该bean实例化的时候，就已经做好的了。

原理也是和上一个差不多，都是从一个map集合里面匹配。所以这里就不再做解析了

> 总结：getHandler()

找适配器

```java
// org.springframework.web.servlet.DispatcherServlet#getHandlerAdapter
	protected HandlerAdapter getHandlerAdapter(Object handler) throws ServletException {
		if (this.handlerAdapters != null) {
			for (HandlerAdapter adapter : this.handlerAdapters) {
				if (adapter.supports(handler)) {
					return adapter;
				}
			}
		}
		throw new ServletException("No adapter for handler [" + handler +
				"]: The DispatcherServlet configuration needs to include a HandlerAdapter that supports this handler");
	}
```



> 其实能看见他是从一个handlerAdapters属性里面遍历了我们的适配器 这个handlerAdapters哪来的呢？ 
>
> 跟我们的HandlerMappings一样 在他的配置文件里面有写，就是我们刚刚所说的 。
>
> 至于什么是适配器，我们结合Handler来讲， 就如我们在最开始的总结时所说的， 一开始只是找到了Handler 现在要执行了，但是有个问题，Handler不止一个， 自然而然对应的执行方式就不同了， 这时候适配器的概念就出来了：对应不同的Handler的执行方案。当找到合适的适配器的时候， 基本上就已经收尾了，因为后面在做了一些判断之后（判断请求类型之类的），就开始执行了你的Handler了，上代码：
>
> `mv = ha.handle(processedRequest, response, mappedHandler.getHandler());`
>
> 这个mv就是我们的ModlAndView 其实执行完这一行 我们的Controller的逻辑已经执行完了， 剩下的就是寻找视图 渲染图的事情了。

> 总结： 其实我们的SpringMVC关键的概念就在于Handler（处理器） 和Adapter(适配器) 通过一个关键的HandlerMappings 找到合适处理你的Controller的Handler 然后再通过HandlerAdapters找到一个合适的HandlerAdapter 来执行Handler即Controller里面的逻辑。 最后再返回ModlAndView...

> 参考:https://juejin.cn/post/6991290858880368676



# Spring

## 事务的传播

> 参考:https://segmentfault.com/a/1190000013341344

| 传播等级      | 描述                                                         | 理解                                                         |
| ------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| REQUIRED      | 默认的事务传播级别<br />表示如果当前存在事务，则加入该事务；<br />如果当前没有事务，则创建一个新的事务。 | A有事务,B就跟着用<br />A没有事务,B就开启自己的事务,只B方法用 |
| SUPPORTS      | 如果当前存在事务，则加入该事务；<br />如果当前没有事务，则以非事务的方式继续运行。 |                                                              |
| MANDATORY     | 如果当前存在事务，则加入该事务；<br />如果当前没有事务，则抛出异常。 |                                                              |
|               |                                                              |                                                              |
| REQUIRES_NEW  | 表示创建一个新的事务<br />如果当前存在事务，则把当前事务挂起。<br />也就是说不管外部方法是否开启事务，Propagation.REQUIRES_NEW 修饰的内部方法会新开启自己的事务，且开启的事务相互独立，互不干扰。 |                                                              |
| NOT_SUPPORTED | 以非事务方式运行，<br />如果当前存在事务，则把当前事务挂起。 |                                                              |
| NEVER         | 以非事务方式运行，<br />如果当前存在事务，则抛出异常。       |                                                              |
|               |                                                              |                                                              |
| NESTED        | 如果当前存在事务，则创建一个事务作为当前事务的嵌套事务来运行；<br />如果当前没有事务，则该取值等价于REQUIRED。 |                                                              |



默认数据是anthony和0

**REQUIRED**

A方法`有`事务,A方法报错,有一个报错都会回滚,结果是:anthony和0

```java
@PostMapping("/test1")
@Transactional
public void methodA(){
    Test byId = testService.getById(1);
    byId.setUsername("anthony2");
    byId.updateById();

    transactionalController.methodB();
    int i = 1 / 0;
}

@Transactional(propagation= Propagation.REQUIRED)
public void methodB(){
    Test byId = testService.getById(1);
    byId.setParentId(1);
    byId.updateById();
}
```



A方法`没有`事务,A方法有报错,结果是:anthony2和1

B方法自己开启事务,就不管A事务了,所以A方法,就算报错了,也成功写入数据库,B事务没有报错,也成功写入数据库

```java
@PostMapping("/test1")
public void methodA(){
    Test byId = testService.getById(1);
    byId.setUsername("anthony2");
    byId.updateById();

    transactionalController.methodB();
    int i = 1 / 0;
}

@Transactional(propagation= Propagation.REQUIRED)
public void methodB(){
    Test byId = testService.getById(1);
    byId.setParentId(1);
    byId.updateById();
}
```



A方法没有事务,A方法和B方法都报错,,结果是:anthony2和0

A方法没有事务,所以A方法插入数据库成功,就算报错,也没有回滚

B方法自己开始事务,B方法报错,所以回滚

```java
@PostMapping("/test1")
public void methodA(){
    Test byId = testService.getById(1);
    byId.setUsername("anthony2");
    byId.updateById();

    transactionalController.methodB();
    int i = 1 / 0;
}

@Transactional(propagation= Propagation.REQUIRED)
public void methodB(){
    Test byId = testService.getById(1);
    byId.setParentId(1);
    byId.updateById();
    int i = 1 / 0;
}
```

**SUPPORTS**

A方法有事务,A方法报错,都回滚,,结果是:anthony和0

```java
@PostMapping("/test1")
@Transactional
public void methodA(){
    Test byId = testService.getById(1);
    byId.setUsername("anthony2");
    byId.updateById();

    transactionalController.methodB();
    int i = 1 / 0;
}

@Transactional(propagation= Propagation.SUPPORTS)
public void methodB(){
    Test byId = testService.getById(1);
    byId.setParentId(1);
    byId.updateById();
}
```



A没有事务,,A方法报错,都没有回滚,结果是:anthony2和1

```java
@PostMapping("/test1")
public void methodA(){
    Test byId = testService.getById(1);
    byId.setUsername("anthony2");
    byId.updateById();

    transactionalController.methodB();
    int i = 1 / 0;
}

@Transactional(propagation= Propagation.SUPPORTS)
public void methodB(){
    Test byId = testService.getById(1);
    byId.setParentId(1);
    byId.updateById();
}
```



A没有事务,,A,B方法都报错,都没有回滚,结果是:anthony2和1

```java
@PostMapping("/test1")
public void methodA(){
    Test byId = testService.getById(1);
    byId.setUsername("anthony2");
    byId.updateById();

    transactionalController.methodB();
    int i = 1 / 0;
}

@Transactional(propagation= Propagation.SUPPORTS)
public void methodB(){
    Test byId = testService.getById(1);
    byId.setParentId(1);
    byId.updateById();
    int i = 1 / 0;
}
```



**MANDATORY**

A有事务,A报错,都回滚,结果是:anthony和0

```java
@PostMapping("/test1")
@Transactional
public void methodA(){
    Test byId = testService.getById(1);
    byId.setUsername("anthony2");
    byId.updateById();

    transactionalController.methodB();
    int i = 1 / 0;
}

@Transactional(propagation= Propagation.MANDATORY)
public void methodB(){
    Test byId = testService.getById(1);
    byId.setParentId(1);
    byId.updateById();
}
```



A没有事务,运行报错了

```java
@PostMapping("/test1")
public void methodA(){
    Test byId = testService.getById(1);
    byId.setUsername("anthony2");
    byId.updateById();

    transactionalController.methodB();
    int i = 1 / 0;
}

@Transactional(propagation= Propagation.MANDATORY)
public void methodB(){
    Test byId = testService.getById(1);
    byId.setParentId(1);
    byId.updateById();
}
```



**REQUIRES_NEW**

测试的时候,不要操作同一条数据,容易超时.....

A开始事务,B也开始事务,B报错了,B回滚,A插入成功

```java
@PostMapping("/test1")
@Transactional
public void methodA(){
    Test byId = testService.getById(1);
    byId.setUsername("anthony2");
    byId.updateById();

    transactionalController.methodB();
    int i = 1 / 0;
}

@Transactional(propagation= Propagation.REQUIRES_NEW)
public void methodB(){
    Test byId = testService.getById(2);
    byId.setParentId(1);
    byId.updateById();
}
```

这样没有复现出问题

```java
@PostMapping("/test1")
@Transactional
public void methodA(){
    Test byId = testService.getById(1);
    byId.setUsername("anthony2");
    byId.updateById();
    transactionalController.methodB();
}

@Transactional(propagation= Propagation.REQUIRES_NEW)
public void methodB(){
    Test byId = testService.getById(2);
    byId.setParentId(1);
    byId.updateById();
    int i = 1 / 0;
}
```



* A没有事务,B有事务
* A报错,没有回滚,
  B插入数据成功
* `外围方法异常,不影响内部调用的方法`

```java
@PostMapping("/test1")
public void methodA(){
    Test byId = testService.getById(1);
    byId.setUsername("anthony2");
    byId.updateById();

    transactionalController.methodB();
    int i = 1 / 0;
}

@Transactional(propagation= Propagation.REQUIRES_NEW)
public void methodB(){
    Test byId = testService.getById(2);
    byId.setParentId(1);
    byId.updateById();
}
```



* A没有事务,B有事务
* A插入数据成功,B回滚
* `内部调用的方法,不影响外围的方法成功插入`

```java
@PostMapping("/test1")
public void methodA(){
    Test byId = testService.getById(1);
    byId.setUsername("anthony2");
    byId.updateById();
    transactionalController.methodB();
}

@Transactional(propagation= Propagation.REQUIRES_NEW)
public void methodB(){
    Test byId = testService.getById(2);
    byId.setParentId(1);
    byId.updateById();
    int i = 1 / 0;
}
```

**NOT_SUPPORTED**

A有事务,B也有事务,A回滚了,B报错了,没有回滚

```java
@PostMapping("/test1")
@Transactional
public void methodA(){
    Test byId = testService.getById(1);
    byId.setUsername("anthony2");
    byId.updateById();

    transactionalController.methodB();

}

@Transactional(propagation= Propagation.NOT_SUPPORTED)
public void methodB(){
    Test byId = testService.getById(2);
    byId.setParentId(1);
    byId.updateById();
    int i = 1 / 0;

}
```



A有事务,B也有事务,A回滚了,B没有回滚

```java
@PostMapping("/test1")
@Transactional
public void methodA(){
    Test byId = testService.getById(1);
    byId.setUsername("anthony2");
    byId.updateById();

    transactionalController.methodB();
    int i = 1 / 0;

}

@Transactional(propagation= Propagation.NOT_SUPPORTED)
public void methodB(){
    Test byId = testService.getById(2);
    byId.setParentId(1);
    byId.updateById();
    int i = 1 / 0;

}
```

**NEVER**

直接报错

```java
    @PostMapping("/test1")
    @Transactional
    public void methodA(){
        Test byId = testService.getById(1);
        byId.setUsername("anthony2");
        byId.updateById();
        transactionalController.methodB();
    }

    @Transactional(propagation= Propagation.NEVER)
    public void methodB(){
        Test byId = testService.getById(2);
        byId.setParentId(1);
        byId.updateById();
    }
```

**NESTED**

全部提交成功

```java
@PostMapping("/test1")
@Transactional
public void methodA(){
    Test byId = testService.getById(1);
    byId.setUsername("anthony2");
    byId.updateById();
    transactionalController.methodB();
}

@Transactional(propagation= Propagation.NESTED)
public void methodB(){
    Test byId = testService.getById(2);
    byId.setParentId(1);
    byId.updateById();
}
```

全部失败

```java
@PostMapping("/test1")
@Transactional
public void methodA(){
    Test byId = testService.getById(1);
    byId.setUsername("anthony2");
    byId.updateById();
    transactionalController.methodB();
}

@Transactional(propagation= Propagation.NESTED)
public void methodB(){
    Test byId = testService.getById(2);
    byId.setParentId(1);
    byId.updateById();
    int i = 1 / 0;
}
```

A没有事务,B有事务

A执行成功,B回滚成功

```java
@PostMapping("/test1")
public void methodA(){
    Test byId = testService.getById(1);
    byId.setUsername("anthony2");
    byId.updateById();
    transactionalController.methodB();
}

@Transactional(propagation= Propagation.NESTED)
public void methodB(){
    Test byId = testService.getById(2);
    byId.setParentId(1);
    byId.updateById();
    int i = 1 / 0;
}
```


# 拦截器和过滤器

1、过滤器和拦截器**触发时机不一样**，先拦截器,后过滤器

2、**拦截器**可以获取IOC容器中的各个bean，而过滤器就不行，因为拦**截器是spring提供并管理的**，spring的功能可以被拦截器使用，在拦截器里注入一个service，可以调用业务逻辑。而过滤器是JavaEE标准，只需依赖servlet api ，不需要依赖spring。

3、**过滤器的实现**基于**回调函数**。而**拦截器**（代理模式）的实现**基于反射**

4、**过滤器**是依**赖于Servlet容**器，**属于Servlet规范的一部分**，而**拦截器则是独立存**在的，可以在任何情况下使用。

5、**Filte**r的执行由**Servlet容器回调完成**，而**拦截器**通常通**过动态代理（反射）**的方式来执行。

6、**Filter的生命周**期**由Servlet容器管理**，而**拦截器则**可以通过I**oC容器来管理**，因此可以通过注入等方式来获取其他Bean的实例，因此使用会更方便。

7、**过滤器只能在请求的前后使用，而拦截器可以详细到每个方法**
![](https://image.runtimes.cc/202404051459166.png)

# Spring IOC

Spring提供了两种容器：BeanFactory和ApplicationContext

- **BeanFactory：**基本的IoC容器，默认采用延迟初始化策略（lazy-load），即只有当客户端对象需要访问容器中某个bean对象的时候，才会对该bean对象进行初始化以及依赖注入操作。所以BeanFactory容器的特点是启动初期速度快，所需资源有限，适合于资源有限功能要求不严格的场景。
- **ApplicationContext：** ApplicationContext在BeanFactory基础上构建，支持其他的高级特性，如国际化，事件发布等。相对于BeanFactory容器来说，ApplicationContext在启动的时候即完成资源的初始化，所以启动时间较长，适合于系统资源充足，需要更多功能的场景

# Spring Bean

Java 中Bean的定义：

- 类中所有的属性都必须封装，即：使用private声明；`这个不太确定`
- 封装的属性如果需要被外部所操作，则必须编写对应的setter、getter方法；
- 一个JavaBean中至少存在一个无参构造方法。

```java
public class Staff{

    private String name;
    private int age;

    public void setName(String name) {
        this.name = name;
    }

    public void setAge(int age) {
        this.age = age;
    }

    public String getName() {
        return name;
    }

    public int getAge() {
        return age;
    }

}
```

而Spring IoC容器就是管理bean的工厂。Spring中bean 是一个被实例化，组装，并通过 Spring IoC 容器所管理的对象。这些 bean 是由用容器提供的配置元数据创建的。Spring可以采用XML配置文件的方式来管理和配置Bean信息，如下：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

<bean id="user" class="com.wgs.spring.bean.User"></bean>

</beans>
```

`<beans>`是XML配置文件中根节点，下面可包含多个`<bean>`子节点。Spring的XML配置文件中的配置与`<bean>`元素是一一对应的。

| 属性 | 描述 |
| --- | --- |
| id | 注册到容器的对象都有一个唯一的id值，如id="user" |
| name | bean的别名,name可以使用逗号、空格或冒号等分割指定多个name，而id就不可以 |
| scope | 作用域 |
| constructor-arg | 用来注入依赖关系 |
| properties | 用来注入依赖关系 |
| autowiring mode | 用来注入依赖关系 |
| lazy-initialization mode | 是否延迟加载 |
| initialization method | bean被创建的时候,初始化的的方法 |
| destruction method | 销毁指定的方法 |


![](https://image.runtimes.cc/202404051459690.png)


![](https://image.runtimes.cc/202404051458611.png)

# Spring Bean 生命周期

2.低昂registerBeanFactoryPostProcessor  完成扫描,运行之前,不会有我们自己的类,除了`@componentScan`这个注解的这个类,等完成之后,就会有我们自己的类

1：实例化一个ApplicationContext的对象；2：调用bean工厂后置处理器完成扫描；3：循环解析扫描出来的类信息；4：实例化一个BeanDefinition对象来存储解析出来的信息；5：把实例化好的beanDefinition对象put到`beanDefinitionMap`当中缓存起来，以便后面实例化bean；6：再次调用bean工厂后置处理器；7：当然spring还会干很多事情，比如国际化，比如注册BeanPostProcessor等等，如果我们只关心如何实例化一个bean的话那么这一步就是spring调用`finishBeanFactoryInitialization`方法来实例化单例的bean，实例化之前spring要做验证，需要遍历所有扫描出来的类，依次判断这个bean是否Lazy，是否prototype，是否abstract等等；8：如果验证完成spring在实例化一个bean之前需要推断构造方法，因为spring实例化对象是通过构造方法反射，故而需要知道用哪个构造方法；9：推断完构造方法之后spring调用构造方法反射实例化一个**对象**；注意我这里说的是对象、对象、对象；这个时候对象已经实例化出来了，但是并不是一个完整的bean，最简单的体现是这个时候实例化出来的对象属性是没有注入，所以不是一个完整的bean；10：spring处理合并后的beanDefinition(合并？是spring当中非常重要的一块内容，后面的文章我会分析)；11：判断是否支持循环依赖，如果支持则提前把一个工厂存入singletonFactories——map；12：判断是否需要完成属性注入13：如果需要完成属性注入，则开始注入属性14：判断bean的类型回调Aware接口15：调用生命周期回调方法16：如果需要代理则完成代理17：put到单例池——bean完成——存在spring容器当中

# Spring Bean 循环依赖

[https://juejin.im/post/6844904166351978504#h5](https://juejin.im/post/6844904166351978504#h5)

AnnotationConfigApplicationContext#AnnotationConfigApplicationContext

```
public AnnotationConfigApplicationContext(Class<?>... componentClasses) {
    this();
    register(componentClasses);
    // 关键方法
    refresh();
}
```

org.springframework.context.support.AbstractApplicationContext#refresh

```
@Override
public void refresh() throws BeansException, IllegalStateException {
    synchronized (this.startupShutdownMonitor) {
        // Prepare this context for refreshing.
        prepareRefresh();

        // Tell the subclass to refresh the internal bean factory.
        ConfigurableListableBeanFactory beanFactory = obtainFreshBeanFactory();

        // Prepare the bean factory for use in this context.
        prepareBeanFactory(beanFactory);

        try {
            // Allows post-processing of the bean factory in context subclasses.
            postProcessBeanFactory(beanFactory);

            // Invoke factory processors registered as beans in the context.
            invokeBeanFactoryPostProcessors(beanFactory);

            // Register bean processors that intercept bean creation.
            registerBeanPostProcessors(beanFactory);

            // Initialize message source for this context.
            initMessageSource();

            // Initialize event multicaster for this context.
            // 完成所有的扫描
            initApplicationEventMulticaster();

            // Initialize other special beans in specific context subclasses.
            onRefresh();

            // Check for listener beans and register them.
            registerListeners();

            // Instantiate all remaining (non-lazy-init) singletons.
            // 实例化所有没有延迟的单例类
            finishBeanFactoryInitialization(beanFactory);

            // Last step: publish corresponding event.
            finishRefresh();
        }
    }
}
```

org.springframework.context.support.AbstractApplicationContext#finishBeanFactoryInitialization

```
protected void finishBeanFactoryInitialization(ConfigurableListableBeanFactory beanFactory) {
    // ....

    // Instantiate all remaining (non-lazy-init) singletons.
    // 实例化所有单例,非lazy
    beanFactory.preInstantiateSingletons();
}
```

org.springframework.beans.factory.support.DefaultListableBeanFactory#preInstantiateSingletons

```
@Override
public void preInstantiateSingletons() throws BeansException {
    if (logger.isTraceEnabled()) {
        logger.trace("Pre-instantiating singletons in " + this);
    }

    // Iterate over a copy to allow for init methods which in turn register new bean definitions.
    // While this may not be part of the regular factory bootstrap, it does otherwise work fine.
    List<String> beanNames = new ArrayList<>(this.beanDefinitionNames);

    // Trigger initialization of all non-lazy singleton beans...
    for (String beanName : beanNames) {
        RootBeanDefinition bd = getMergedLocalBeanDefinition(beanName);
        // 验证,判断这个是是不是抽象的和是不是单例的和是不是延迟加载的
        if (!bd.isAbstract() && bd.isSingleton() && !bd.isLazyInit()) {
            if (isFactoryBean(beanName)) {
                Object bean = getBean(FACTORY_BEAN_PREFIX + beanName);
                if (bean instanceof FactoryBean) {
                    FactoryBean<?> factory = (FactoryBean<?>) bean;
                    boolean isEagerInit;
                    if (System.getSecurityManager() != null && factory instanceof SmartFactoryBean) {
                        isEagerInit = AccessController.doPrivileged(
                            (PrivilegedAction<Boolean>) ((SmartFactoryBean<?>) factory)::isEagerInit,
                            getAccessControlContext());
                    }
                    else {
                        isEagerInit = (factory instanceof SmartFactoryBean &&
                                       ((SmartFactoryBean<?>) factory).isEagerInit());
                    }
                    if (isEagerInit) {
                        getBean(beanName);
                    }
                }
            }
            else {
                // 验证一切都通过的类,开始实例化普通的bean,还不是spring bean
                getBean(beanName);
            }
        }
    }

    // ....
}
```

org.springframework.beans.factory.support.AbstractBeanFactory#getBean(java.lang.String)

```
@Override
public Object getBean(String name) throws BeansException {
    return doGetBean(name, null, null, false);
}
```

org.springframework.beans.factory.support.AbstractBeanFactory#doGetBean

这里面大部分都是验证,比如depenon,或者import

```
protected <T> T doGetBean(
   String name, @Nullable Class<T> requiredType, @Nullable Object[] args, boolean typeCheckOnly)
    throws BeansException {

    // 理解bean的名字是否非法
    String beanName = transformedBeanName(name);
    Object bean;

    // Eagerly check singleton cache for manually registered singletons.
    // 这里的方法啊
    Object sharedInstance = getSingleton(beanName);
    if (sharedInstance != null && args == null) {
        bean = getObjectForBeanInstance(sharedInstance, name, beanName, null);
    }

    else {
        // Fail if we're already creating this bean instance:
        // We're assumably within a circular reference.
        // 判断这个类是不是在创建过程中,循环依赖的时候要用
        if (isPrototypeCurrentlyInCreation(beanName)) {
            throw new BeanCurrentlyInCreationException(beanName);
        }

        // Check if bean definition exists in this factory.
        BeanFactory parentBeanFactory = getParentBeanFactory();
        if (parentBeanFactory != null && !containsBeanDefinition(beanName)) {
            // 方法注入
            // Not found -> check parent.
            String nameToLookup = originalBeanName(name);
            if (parentBeanFactory instanceof AbstractBeanFactory) {
                return ((AbstractBeanFactory) parentBeanFactory).doGetBean(
                    nameToLookup, requiredType, args, typeCheckOnly);
            }
            else if (args != null) {
                // Delegation to parent with explicit args.
                return (T) parentBeanFactory.getBean(nameToLookup, args);
            }
            else if (requiredType != null) {
                // No args -> delegate to standard getBean method.
                return parentBeanFactory.getBean(nameToLookup, requiredType);
            }
            else {
                return (T) parentBeanFactory.getBean(nameToLookup);
            }
        }

        if (!typeCheckOnly) {
            markBeanAsCreated(beanName);
        }

        try {
            RootBeanDefinition mbd = getMergedLocalBeanDefinition(beanName);
            checkMergedBeanDefinition(mbd, beanName, args);

            // Guarantee initialization of beans that the current bean depends on.
            String[] dependsOn = mbd.getDependsOn();
            if (dependsOn != null) {
                for (String dep : dependsOn) {
                    if (isDependent(beanName, dep)) {
                        throw new BeanCreationException(mbd.getResourceDescription(), beanName,
                                                        "Circular depends-on relationship between '" + beanName + "' and '" + dep + "'");
                    }
                    registerDependentBean(dep, beanName);
                    try {
                        getBean(dep);
                    }
                    catch (NoSuchBeanDefinitionException ex) {
                        throw new BeanCreationException(mbd.getResourceDescription(), beanName,
                                                        "'" + beanName + "' depends on missing bean '" + dep + "'", ex);
                    }
                }
            }

            // Create bean instance.
            // 判断类是不是单例
            if (mbd.isSingleton()) {
                // getSingleton(String,facotory) 这个方法里有正在创建中的标识设置
                sharedInstance = getSingleton(beanName, () -> {
                    try {
                        // 完成了目标对象的创建
                        // 如果需要代理,还创建了代理
                        return createBean(beanName, mbd, args);
                    }
                    catch (BeansException ex) {
                        // Explicitly remove instance from singleton cache: It might have been put there
                        // eagerly by the creation process, to allow for circular reference resolution.
                        // Also remove any beans that received a temporary reference to the bean.
                        destroySingleton(beanName);
                        throw ex;
                    }
                });
                bean = getObjectForBeanInstance(sharedInstance, name, beanName, mbd);
            }

            else if (mbd.isPrototype()) {
                // It's a prototype -> create a new instance.
                Object prototypeInstance = null;
                try {
                    beforePrototypeCreation(beanName);
                    prototypeInstance = createBean(beanName, mbd, args);
                }
                finally {
                    afterPrototypeCreation(beanName);
                }
                bean = getObjectForBeanInstance(prototypeInstance, name, beanName, mbd);
            }

            else {
                String scopeName = mbd.getScope();
                if (!StringUtils.hasLength(scopeName)) {
                    throw new IllegalStateException("No scope name defined for bean ´" + beanName + "'");
                }
                Scope scope = this.scopes.get(scopeName);
                if (scope == null) {
                    throw new IllegalStateException("No Scope registered for scope name '" + scopeName + "'");
                }
                try {
                    Object scopedInstance = scope.get(beanName, () -> {
                        beforePrototypeCreation(beanName);
                        try {
                            return createBean(beanName, mbd, args);
                        }
                        finally {
                            afterPrototypeCreation(beanName);
                        }
                    });
                    bean = getObjectForBeanInstance(scopedInstance, name, beanName, mbd);
                }
                catch (IllegalStateException ex) {
                    throw new BeanCreationException(beanName,
                                                    "Scope '" + scopeName + "' is not active for the current thread; consider " +
                                                    "defining a scoped proxy for this bean if you intend to refer to it from a singleton",
                                                    ex);
                }
            }
        }
        catch (BeansException ex) {
            cleanupAfterBeanCreationFailure(beanName);
            throw ex;
        }
    }

    // Check if required type matches the type of the actual bean instance.
    if (requiredType != null && !requiredType.isInstance(bean)) {
        try {
            T convertedBean = getTypeConverter().convertIfNecessary(bean, requiredType);
            if (convertedBean == null) {
                throw new BeanNotOfRequiredTypeException(name, requiredType, bean.getClass());
            }
            return convertedBean;
        }
        catch (TypeMismatchException ex) {
            if (logger.isTraceEnabled()) {
                logger.trace("Failed to convert bean '" + name + "' to required type '" +
                             ClassUtils.getQualifiedName(requiredType) + "'", ex);
            }
            throw new BeanNotOfRequiredTypeException(name, requiredType, bean.getClass());
        }
    }
    return (T) bean;
}
```

org.springframework.beans.factory.support.DefaultSingletonBeanRegistry#getSingleton(java.lang.String)

```
// 上个代码块第七行调用的
@Override
@Nullable
public Object getSingleton(String beanName) {
    return getSingleton(beanName, true);
}

/** Cache of singleton objects: bean name to bean instance. */
/** 缓存单例对象: bean name to bean instance. */
private final Map<String, Object> singletonObjects = new ConcurrentHashMap<>(256);

@Nullable
protected Object getSingleton(String beanName, boolean allowEarlyReference) {
    // 初始化的时候这里肯定是null,但是在初始化完成之后,再调用getBean就肯定不是null
    // isSingletonCurrentlyInCreation 这个方法很重要,说明对象是不是正在创建
    // singletonFactories 也很重要
    Object singletonObject = this.singletonObjects.get(beanName);

    // 判断循环依赖的时候
    if (singletonObject == null && isSingletonCurrentlyInCreation(beanName)) {
        synchronized (this.singletonObjects) {
            singletonObject = this.earlySingletonObjects.get(beanName);
            if (singletonObject == null && allowEarlyReference) {
                ObjectFactory<?> singletonFactory = this.singletonFactories.get(beanName);
                if (singletonFactory != null) {
                    singletonObject = singletonFactory.getObject();
                    this.earlySingletonObjects.put(beanName, singletonObject);
                    this.singletonFactories.remove(beanName);
                }
            }
        }
    }
    return singletonObject;
}
```

org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory#createBean

```
@Override
 protected Object createBean(String beanName, RootBeanDefinition mbd, @Nullable Object[] args)
   throws BeanCreationException {

  if (logger.isTraceEnabled()) {
   logger.trace("Creating instance of bean '" + beanName + "'");
  }
  RootBeanDefinition mbdToUse = mbd;

  // Make sure bean class is actually resolved at this point, and
  // clone the bean definition in case of a dynamically resolved Class
  // which cannot be stored in the shared merged bean definition.
        // 从beanDefinition对象中获取出来bean的类型
  Class<?> resolvedClass = resolveBeanClass(mbd, beanName);
  if (resolvedClass != null && !mbd.hasBeanClass() && mbd.getBeanClassName() != null) {
   mbdToUse = new RootBeanDefinition(mbd);
   mbdToUse.setBeanClass(resolvedClass);
  }

  // Prepare method overrides.
  try {
   mbdToUse.prepareMethodOverrides();
  }
  catch (BeanDefinitionValidationException ex) {
   throw new BeanDefinitionStoreException(mbdToUse.getResourceDescription(),
     beanName, "Validation of method overrides failed", ex);
  }

  try {
   // Give BeanPostProcessors a chance to return a proxy instead of the target bean instance.
            // 第一次调用个后置处理器
   Object bean = resolveBeforeInstantiation(beanName, mbdToUse);
   if (bean != null) {
    return bean;
   }
  }
  catch (Throwable ex) {
   throw new BeanCreationException(mbdToUse.getResourceDescription(), beanName,
     "BeanPostProcessor before instantiation of bean failed", ex);
  }

  try {
            // 调用方法
   Object beanInstance = doCreateBean(beanName, mbdToUse, args);
   if (logger.isTraceEnabled()) {
    logger.trace("Finished creating instance of bean '" + beanName + "'");
   }
   return beanInstance;
  }
  catch (BeanCreationException | ImplicitlyAppearedSingletonException ex) {
   // A previously detected exception with proper bean creation context already,
   // or illegal singleton state to be communicated up to DefaultSingletonBeanRegistry.
   throw ex;
  }
  catch (Throwable ex) {
   throw new BeanCreationException(
     mbdToUse.getResourceDescription(), beanName, "Unexpected exception during bean creation", ex);
  }
 }
```

org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory#doCreateBean

```
protected Object doCreateBean(String beanName, RootBeanDefinition mbd, @Nullable Object[] args)
   throws BeanCreationException {

    // Instantiate the bean.
    BeanWrapper instanceWrapper = null;
    if (mbd.isSingleton()) {
        instanceWrapper = this.factoryBeanInstanceCache.remove(beanName);
    }
    if (instanceWrapper == null) {
        // 实例化对象,里面第二次调调用后置处理器
        // 反射调用对象的构造方法
        // 这里java对象就已经有了
        instanceWrapper = createBeanInstance(beanName, mbd, args);
    }
    Object bean = instanceWrapper.getWrappedInstance();
    Class<?> beanType = instanceWrapper.getWrappedClass();
    if (beanType != NullBean.class) {
        mbd.resolvedTargetType = beanType;
    }

    // Allow post-processors to modify the merged bean definition.
    synchronized (mbd.postProcessingLock) {
        if (!mbd.postProcessed) {
            try {
                // 第三次调用后置处理器
                applyMergedBeanDefinitionPostProcessors(mbd, beanType, beanName);
            }

            mbd.postProcessed = true;
        }
    }

    // Eagerly cache singletons to be able to resolve circular references
    // even when triggered by lifecycle interfaces like BeanFactoryAware.
    // 判断是否需要循环依赖
    boolean earlySingletonExposure =
        // 到这里了,也肯定是true
        (mbd.isSingleton() &&
         // 默认值是true
         this.allowCircularReferences &&
  isSingletonCurrentlyInCreation(beanName));

    if (earlySingletonExposure) {
        // 第四次调用后置处理器,判断是否需要AOP
        addSingletonFactory(beanName, () -> getEarlyBeanReference(beanName, mbd, bean));
    }

    // Initialize the bean instance.
    Object exposedObject = bean;
    try {
        // 填充属性,也就是我们说的自动注入
        // 里面会完成第五次和第六次后置处理器的调用
        // 看这里
        populateBean(beanName, mbd, instanceWrapper);
        // 初始化spring
        // 里面会进行第七次和第八次后置处理的调用个
        exposedObject = initializeBean(beanName, exposedObject, mbd);
    }
    catch (Throwable ex) {
        if (ex instanceof BeanCreationException && beanName.equals(((BeanCreationException) ex).getBeanName())) {
            throw (BeanCreationException) ex;
        }
        else {
            throw new BeanCreationException(
                mbd.getResourceDescription(), beanName, "Initialization of bean failed", ex);
        }
    }

    if (earlySingletonExposure) {
        Object earlySingletonReference = getSingleton(beanName, false);
        if (earlySingletonReference != null) {
            if (exposedObject == bean) {
                exposedObject = earlySingletonReference;
            }
            else if (!this.allowRawInjectionDespiteWrapping && hasDependentBean(beanName)) {
                String[] dependentBeans = getDependentBeans(beanName);
                Set<String> actualDependentBeans = new LinkedHashSet<>(dependentBeans.length);
                for (String dependentBean : dependentBeans) {
                    if (!removeSingletonIfCreatedForTypeCheckOnly(dependentBean)) {
                        actualDependentBeans.add(dependentBean);
                    }
                }
                // 省略代码
            }
        }
    }

    // Register bean as disposable.
    try {
        registerDisposableBeanIfNecessary(beanName, bean, mbd);
    }
    catch (BeanDefinitionValidationException ex) {
        throw new BeanCreationException(
            mbd.getResourceDescription(), beanName, "Invalid destruction signature", ex);
    }

    return exposedObject;
}
```

org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory#createBeanInstance

```
protected BeanWrapper createBeanInstance(String beanName, RootBeanDefinition mbd, @Nullable Object[] args) {
    // Make sure bean class is actually resolved at this point.
    Class<?> beanClass = resolveBeanClass(mbd, beanName);

    if (beanClass != null && !Modifier.isPublic(beanClass.getModifiers()) && !mbd.isNonPublicAccessAllowed()) {
        throw new BeanCreationException(mbd.getResourceDescription(), beanName,
                                        "Bean class isn't public, and non-public access not allowed: " + beanClass.getName());
    }

    Supplier<?> instanceSupplier = mbd.getInstanceSupplier();
    if (instanceSupplier != null) {
        return obtainFromSupplier(instanceSupplier, beanName);
    }

    if (mbd.getFactoryMethodName() != null) {
        return instantiateUsingFactoryMethod(beanName, mbd, args);
    }

    // Shortcut when re-creating the same bean...
    boolean resolved = false;
    boolean autowireNecessary = false;
    if (args == null) {
        synchronized (mbd.constructorArgumentLock) {
            if (mbd.resolvedConstructorOrFactoryMethod != null) {
                resolved = true;
                autowireNecessary = mbd.constructorArgumentsResolved;
            }
        }
    }
    if (resolved) {
        if (autowireNecessary) {
            return autowireConstructor(beanName, mbd, null, null);
        }
        else {
            return instantiateBean(beanName, mbd);
        }
    }

    // Candidate constructors for autowiring?
    // 第二次调用后置处理器构造方法,通过反射实例化对象,这时候构造方法里有打印,就会打印出日志
    Constructor<?>[] ctors = determineConstructorsFromBeanPostProcessors(beanClass, beanName);
    if (ctors != null || mbd.getResolvedAutowireMode() == AUTOWIRE_CONSTRUCTOR ||
        mbd.hasConstructorArgumentValues() || !ObjectUtils.isEmpty(args)) {
        return autowireConstructor(beanName, mbd, ctors, args);
    }

    // Preferred constructors for default construction?
    ctors = mbd.getPreferredConstructors();
    if (ctors != null) {
        return autowireConstructor(beanName, mbd, ctors, null);
    }

    // No special handling: simply use no-arg constructor.
    return instantiateBean(beanName, mbd);
}
```

org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory#populateBean

```
protected void populateBean(String beanName, RootBeanDefinition mbd, @Nullable BeanWrapper bw) {
    if (bw == null) {
        if (mbd.hasPropertyValues()) {
            throw new BeanCreationException(
                mbd.getResourceDescription(), beanName, "Cannot apply property values to null instance");
        }
        else {
            // Skip property population phase for null instance.
            return;
        }
    }

    // Give any InstantiationAwareBeanPostProcessors the opportunity to modify the
    // state of the bean before properties are set. This can be used, for example,
    // to support styles of field injection.
    if (!mbd.isSynthetic() && hasInstantiationAwareBeanPostProcessors()) {
        for (BeanPostProcessor bp : getBeanPostProcessors()) {
            if (bp instanceof InstantiationAwareBeanPostProcessor) {
                InstantiationAwareBeanPostProcessor ibp = (InstantiationAwareBeanPostProcessor) bp;
                if (!ibp.postProcessAfterInstantiation(bw.getWrappedInstance(), beanName)) {
                    return;
                }
            }
        }
    }

    PropertyValues pvs = (mbd.hasPropertyValues() ? mbd.getPropertyValues() : null);

    int resolvedAutowireMode = mbd.getResolvedAutowireMode();
    if (resolvedAutowireMode == AUTOWIRE_BY_NAME || resolvedAutowireMode == AUTOWIRE_BY_TYPE) {
        MutablePropertyValues newPvs = new MutablePropertyValues(pvs);
        // Add property values based on autowire by name if applicable.
        if (resolvedAutowireMode == AUTOWIRE_BY_NAME) {
            autowireByName(beanName, mbd, bw, newPvs);
        }
        // Add property values based on autowire by type if applicable.
        if (resolvedAutowireMode == AUTOWIRE_BY_TYPE) {
            autowireByType(beanName, mbd, bw, newPvs);
        }
        pvs = newPvs;
    }

    boolean hasInstAwareBpps = hasInstantiationAwareBeanPostProcessors();
    boolean needsDepCheck = (mbd.getDependencyCheck() != AbstractBeanDefinition.DEPENDENCY_CHECK_NONE);

    PropertyDescriptor[] filteredPds = null;
    if (hasInstAwareBpps) {
        if (pvs == null) {
            pvs = mbd.getPropertyValues();
        }
        for (BeanPostProcessor bp : getBeanPostProcessors()) {
            if (bp instanceof InstantiationAwareBeanPostProcessor) {
                // 这里的ibp常用的有两种类型
                // 1.@Resouce 使用的是CommonAnnotationBeanPostProcessor
    // 2.@Autowire 使用的是AutoWireAnnotationBeanPostProcessor
                InstantiationAwareBeanPostProcessor ibp = (InstantiationAwareBeanPostProcessor) bp;
                // 这里会调用属性的注入,也就是在这里,碰到循环依赖的时候,就会调用个
                // org.springframework.beans.factory.support.DefaultSingletonBeanRegistry#getSingleton
                PropertyValues pvsToUse = ibp.postProcessProperties(pvs, bw.getWrappedInstance(), beanName);
                if (pvsToUse == null) {
                    if (filteredPds == null) {
                        filteredPds = filterPropertyDescriptorsForDependencyCheck(bw, mbd.allowCaching);
                    }
                    pvsToUse = ibp.postProcessPropertyValues(pvs, filteredPds, bw.getWrappedInstance(), beanName);
                    if (pvsToUse == null) {
                        return;
                    }
                }
                pvs = pvsToUse;
            }
        }
    }
    if (needsDepCheck) {
        if (filteredPds == null) {
            filteredPds = filterPropertyDescriptorsForDependencyCheck(bw, mbd.allowCaching);
        }
        checkDependencies(beanName, mbd, filteredPds, pvs);
    }

    if (pvs != null) {
        applyPropertyValues(beanName, mbd, bw, pvs);
    }
}
```

org.springframework.beans.factory.support.DefaultSingletonBeanRegistry#getSingleton(java.lang.String, org.springframework.beans.factory.ObjectFactory<?>)

```
public Object getSingleton(String beanName, ObjectFactory<?> singletonFactory) {
    Assert.notNull(beanName, "Bean name must not be null");
    synchronized (this.singletonObjects) {
        Object singletonObject = this.singletonObjects.get(beanName);
        if (singletonObject == null) {
            if (this.singletonsCurrentlyInDestruction) {
                throw new BeanCreationNotAllowedException(beanName,
                                                          "Singleton bean creation not allowed while singletons of this factory are in destruction " +
                                                          "(Do not request a bean from a BeanFactory in a destroy method implementation!)");
            }
            if (logger.isDebugEnabled()) {
                logger.debug("Creating shared instance of singleton bean '" + beanName + "'");
            }
            // 重点,如果没有获取到,就设置个标识,表示正在创建
            beforeSingletonCreation(beanName);
            boolean newSingleton = false;
            boolean recordSuppressedExceptions = (this.suppressedExceptions == null);
            if (recordSuppressedExceptions) {
                this.suppressedExceptions = new LinkedHashSet<>();
            }
            try {
                singletonObject = singletonFactory.getObject();
                newSingleton = true;
            }
            catch (IllegalStateException ex) {
                // Has the singleton object implicitly appeared in the meantime ->
                // if yes, proceed with it since the exception indicates that state.
                singletonObject = this.singletonObjects.get(beanName);
                if (singletonObject == null) {
                    throw ex;
                }
            }
            catch (BeanCreationException ex) {
                if (recordSuppressedExceptions) {
                    for (Exception suppressedException : this.suppressedExceptions) {
                        ex.addRelatedCause(suppressedException);
                    }
                }
                throw ex;
            }
            finally {
                if (recordSuppressedExceptions) {
                    this.suppressedExceptions = null;
                }
                afterSingletonCreation(beanName);
            }
            if (newSingleton) {
                addSingleton(beanName, singletonObject);
            }
        }
        return singletonObject;
    }
}
```

org.springframework.beans.factory.support.DefaultSingletonBeanRegistry#beforeSingletonCreation

```
/** Names of beans that are currently in creation. */
// 添加到这里来了之后就标识当前这个bean正在创建
private final Set<String> singletonsCurrentlyInCreation =
    Collections.newSetFromMap(new ConcurrentHashMap<>(16));

protected void beforeSingletonCreation(String beanName) {
    if (!this.inCreationCheckExclusions.contains(beanName) && !this.singletonsCurrentlyInCreation.add(beanName)) {
        throw new BeanCurrentlyInCreationException(beanName);
    }
}
```

org.springframework.beans.factory.support.DefaultSingletonBeanRegistry#addSingletonFactory

二级缓存

```
protected void addSingletonFactory(String beanName, ObjectFactory<?> singletonFactory) {
    Assert.notNull(singletonFactory, "Singleton factory must not be null");
    synchronized (this.singletonObjects) {
        if (!this.singletonObjects.containsKey(beanName)) {
            this.singletonFactories.put(beanName, singletonFactory);
            this.earlySingletonObjects.remove(beanName);
            this.registeredSingletons.add(beanName);
        }
    }
}
```

### 三个缓存

```
// singletonObjects：第一级缓存，里面存放的都是创建好的成品Bean。
private final Map<String, Object> singletonObjects = new ConcurrentHashMap<String, Object(256);

// earlySingletonObjects : 第二级缓存，里面存放的都是半成品的Bean
private final Map<String, Object> earlySingletonObjects = new HashMap<String, Object(16);

// singletonFactories ：第三级缓存， 不同于前两个存的是 Bean对象引用，此缓存存的bean 工厂对象，也就存的是 专门创建Bean的一个工厂对象。此缓存用于解决循环依赖
private final Map<String, ObjectFactory<?>> singletonFactories = new HashMap<String, ObjectFactory<?>>(16);
```

### 两个缓存能解决不

A引用创建后，提前暴露到`半成品缓存中`

依赖B，创建B ，B填充属性时发现依赖A， `先从成品缓存查找，没有,再从半成品缓存查找` 取到A的`早期引用`。

```
B顺利走完创建过程`, 将`B的早期引用从半成品缓存移动到成品缓存
```

B创建完成，A获取到B的引用，继续创建。

A创建完成，将`A的早期引用从半成品缓存移动到成品缓存`

### 为啥需要三个缓存

上面两个缓存的地方，我们只是没有考虑代理的情况。

Bean在创建的最后阶段，会检查是否需要创建代理，如果创建了代理，那么最终返回的就是代理实例的引用。我们通过beanname获取到最终是代理实例的引用

也就是说：假设A最终会创建代理，提前暴露A的引用， B填充属性时填充的是A的原始对象引用。A最终放入成品库里是代理的引用。那么B中依然是A的早期引用。这种结果最终会与我们的期望的大相径庭了。

### 完整的流程

关键点：

- A绑定到ObjectFactory 注册到`工厂缓存singletonFactory`中，
- B在填充A时，`先查成品缓存`有没有，`再查半成品缓存`有没有，`最后看工厂缓存有没有单例工厂类`，有A的ObjectFactory。调用getObject ，执行扩展逻辑，可能返回的代理引用，也可能返回原始引用。
- 成功获取到A的早期引用，将A放入到`半成品缓存`中，B填充A引用完毕。
- 代理问题， 循环依赖问题都解决了

# Spring Bean 二次开发

在实例化Bean之前,Spring会调用扩展的类,实现`BeanFactoryPostProcessor`,并且机上`@component`注解,如果没有实现,spring就不会调用

# Spring AOP

# AOP是什么

AOP的全称是Aspect Orient Programming，即面向切面编程。是对OOP（Object Orient Programming）的一种补充，战门用于处理一些具有横切性质的服务。常常用于日志输出、安全控制等。

上面说到是对OOP的一种补充，具体补充的是什么呢？考虑一种情况，如果我们需要在所有方法执行前打印一句日志，按照OOP的处理思想，我们需要在每个业务方法开始时加入一些语句，但是我们辛辛苦苦加完之后，如果又要求在这句日志打印后再打印一句，那是不是又要加一遍？这时候你一定会想到，在某个类中编写一个日志打印方法，该方法执行这些日志打印操作，然后在每个业务方法之前加入这句方法调用，这就是面向对象编程思想。但是如果要求我们在业务方法结束时再打印一些日志呢，是不是还要去每个业务方法结束时加一遍？这样始终不是办法，而且我们总是在改业务方法，在业务方法里面掺杂了太多的其他操作，侵入性太高。

这时候AOP就起到作用了，我们可以编写一个切面类（Aspect），在其中的方法中来编写横切逻辑（如打印日志），然后通过配置或者注解的方式来声明该横切逻辑起作用的位置。

# 实现技术

AOP（这里的AOP指的是面向切面编程思想，而不是Spring AOP）主要的的实现技术主要有Spring AOP和AspectJ。

1、AspectJ的底层技术。

AspectJ的底层技术是静态代理，即用一种AspectJ支持的特定语言编写切面，通过一个命令来编译，生成一个新的代理类，该代理类增强了业务类，这是在编译时增强，相对于下面说的运行时增强，编译时增强的性能更好。

2、Spring AOP

Spring AOP采用的是动态代理，在运行期间对业务方法进行增强，所以不会生成新类，对于动态代理技术，Spring AOP提供了对JDK动态代理的支持以及CGLib的支持。

JDK动态代理只能为接口创建动态代理实例，而不能对类创建动态代理。需要获得被目标类的接口信息（应用Java的反射技术），生成一个实现了代理接口的动态代理类（字节码），再通过反射机制获得动态代理类的构造函数，利用构造函数生成动态代理类的实例对象，在调用具体方法前调用invokeHandler方法来处理。

CGLib动态代理需要依赖asm包，把被代理对象类的class文件加载进来，修改其字节码生成子类。

但是Spring AOP基于注解配置的情况下，需要依赖于AspectJ包的标准注解，但是不需要额外的编译以及AspectJ的织入器，而基于XML配置不需要。

# 知识点

### PointCut

你想要去切某个东西之前总得先知道要在哪里切入是吧，切点格式如下：`execution(* com.nuofankj.springdemo.aop.*Service.*(..))`格式使用了正常表达式来定义那个范围内的类、那些接口会被当成切点

### Advice

通知,所谓的Advice其实就是定义了Aop何时被调用，确实有种通知的感觉

- Before 在方法被调用之前调用
- After 在方法完成之后调用
- After-returning 在方法成功执行之后调用
- After-throwing 在方法抛出异常之后调用
- Around 在被通知的方法调用之前和调用之后调用

### JoinPoint

JoinPoint连接点，其实很好理解，上面又有通知、又有切点，那和具体业务的连接点又是什么呢？没错，其实就是对应业务的方法对象，因为我们在横切代码中是有可能需要用到具体方法中的具体数据的，而连接点便可以做到这一点。

### Aspect

就是我们关注点的模块化。这个关注点可能会横切多个对象和模块，事务管理是横切关注点的很好的例子。它是一个抽象的概念，从软件的角度来说是指在应用程序不同模块中的某一个领域或方面。又pointcut 和advice组成。

### Weaving

把切面应用到目标对象来创建新的 advised 对象的过程。

# 原理

### 简单说说 AOP 的设计

1. 每个 Bean 都会被 JDK 或者 Cglib 代理。取决于是否有接口。
2. 每个 Bean 会有多个“方法拦截器”。注意：拦截器分为两层，外层由 Spring 内核控制流程，内层拦截器是用户设置，也就是 AOP。
3. 当代理方法被调用时，先经过外层拦截器，外层拦截器根据方法的各种信息判断该方法应该执行哪些“内层拦截器”。内层拦截器的设计就是职责连的设计。

### 流程

代理的创建（按步骤）：

- 首先，需要创建代理工厂，代理工厂需要 3 个重要的信息：拦截器数组，目标对象接口数组，目标对象。
- 创建代理工厂时，默认会在拦截器数组尾部再增加一个默认拦截器 —— 用于最终的调用目标方法。
- 当调用 getProxy 方法的时候，会根据接口数量大余 0 条件返回一个代理对象（JDK or Cglib）。
- 注意：创建代理对象时，同时会创建一个外层拦截器，这个拦截器就是 Spring 内核的拦截器。用于控制整个 AOP 的流程。

代理的调用

- 当对代理对象进行调用时，就会触发外层拦截器。
- 外层拦截器根据代理配置信息，创建内层拦截器链。创建的过程中，会根据表达式判断当前拦截是否匹配这个拦截器。而这个拦截器链设计模式就是职责链模式。
- 当整个链条执行到最后时，就会触发创建代理时那个尾部的默认拦截器，从而调用目标方法。最后返回。

# SpringMCC临时用的

[https://zhuanlan.zhihu.com/p/62562499](https://zhuanlan.zhihu.com/p/62562499)

# 设置属性

```
// 1. 设置属性
// Make web application context available
request.setAttribute(WEB_APPLICATION_CONTEXT_ATTRIBUTE, getWebApplicationContext());

// Make locale resolver available
request.setAttribute(LOCALE_RESOLVER_ATTRIBUTE, this.localeResolver);

// Make theme resolver available
request.setAttribute(THEME_RESOLVER_ATTRIBUTE, this.themeResolver);
```

# 根据 Request 请求的 URL 得到对应的 handler 执行链，其实就是拦截器和 Controller 代理对象

```java
// 2. 找 handler 返回执行链
HandlerExecutionChain mappedHandler = getHandler(request);
```

# 得到 handler 的适配器

```java
// This will throw an exception if no adapter is found
// 3. 返回 handler 的适配器
HandlerAdapter ha = getHandlerAdapter(mappedHandler.getHandler());
```

# 循环执行 handler 的 pre 拦截器

```java
// 4. 循环执行 handler 的 pre 拦截器
for (int i = 0; i < mappedHandler.getInterceptors().length; i++) {
    HandlerInterceptor interceptor = mappedHandler.getInterceptors()[i];
    // pre 拦截器
    if (!interceptor.preHandle(request, response, mappedHandler.getHandler())) {
        return;
    }
}
```

# 执行真正的 handler，并返回 ModelAndView(Handler 是个代理对象，可能会执行 AOP )

```java
// 5. 执行真正的 handler，并返回  ModelAndView(Handler 是个代理对象，可能会执行 AOP )
ModelAndView mv = ha.handle(request, response, mappedHandler.getHandler());
```

# 循环执行 handler 的 post 拦截器

```java
// 6. 循环执行 handler 的 post 拦截器
for (int i = mappedHandler.getInterceptors().length - 1; i >=0 ; i--) {
    HandlerInterceptor interceptor = mappedHandler.getInterceptors()[i];
    // post 拦截器
    interceptor.postHandle(request, response, mappedHandler.getHandler());
}

# 根据 ModelAndView 信息得到 View 实例
  View view = null;
if (mv.isReference()) {
    // We need to resolve this view name
    // 7. 根据 ModelAndView 信息得到 View 实例
    view = this.viewResolver.resolveViewName(mv.getViewName(), locale);
}

# 渲染 View 返回
// 8. 渲染 View 返回
view.render(mv.getModel(), request, response);
```

其实理解这些才是最重要的。

1. 用户发送请求至前端控制器DispatcherServlet
2. DispatcherServlet收到请求调用HandlerMapping处理器映射器。
3. 处理器映射器根据请求url找到具体的处理器，生成处理器对象及处理器拦截器(如果有则生成)一并返回给DispatcherServlet。
4. DispatcherServlet通过HandlerAdapter处理器适配器调用处理器
5. HandlerAdapter执行处理器(handler，也叫后端控制器)。
6. Controller执行完成返回ModelAndView
7. HandlerAdapter将handler执行结果ModelAndView返回给DispatcherServlet
8. DispatcherServlet将ModelAndView传给ViewReslover视图解析器
9. ViewReslover解析后返回具体View对象
10. DispatcherServlet对View进行渲染视图（即将模型数据填充至视图中）。
11. DispatcherServlet响应用户

# Springboot 启动流程

[https://juejin.im/post/6844903669998026759](https://juejin.im/post/6844903669998026759)

通过 `SpringFactoriesLoader` 加载 `META-INF/spring.factories` 文件，获取并创建 `SpringApplicationRunListener` 对象

然后由 `SpringApplicationRunListener` 来发出 starting 消息

创建参数，并配置当前 SpringBoot 应用将要使用的 Environment

完成之后，依然由 `SpringApplicationRunListener` 来发出 environmentPrepared 消息

创建 `ApplicationContext`

初始化 `ApplicationContext`，并设置 Environment，加载相关配置等

由 `SpringApplicationRunListener` 来发出 `contextPrepared` 消息，告知SpringBoot 应用使用的 `ApplicationContext` 已准备OK

将各种 beans 装载入 `ApplicationContext`，继续由 `SpringApplicationRunListener` 来发出 contextLoaded 消息，告知 SpringBoot 应用使用的 `ApplicationContext` 已装填OK

refresh ApplicationContext，完成IoC容器可用的最后一步

由 `SpringApplicationRunListener` 来发出 started 消息

完成最终的程序的启动

由 `SpringApplicationRunListener` 来发出 running 消息，告知程序已运行起来了

# 静态变量注入

```
application.properties中配置下面两个配置项
ccb.ip.address=10.25.177.31
ccb.ip.port=1600
下面问题代码中读取不到application.properties配置文件中的配置
```

```
@Component
public class BISFrontFileUtil {
    private static Logger logger = LoggerFactory.getLogger(BISFrontFileUtil.class);

    private static String CCBIPADDRESS;

    private static int CCBIPPORT;

    @Value("${ccb.ip.address}")
    public void setCCBIPADDRESS(String cCBIPADDRESS) {
        CCBIPADDRESS = cCBIPADDRESS;
    }

    @Value("${ccb.ip.port}")
    public void setCCBIPPORT(int cCBIPPORT) {
        CCBIPPORT = cCBIPPORT;
    }
}
```

注意:

1. 修正代码中的@Component不可丢掉了
2. set方法要是非静态的

# SpringBoot的注解

- [@Configuration](https://juejin.cn/post/6844903842476195848#heading-12)

# [@Configuration](notion://www.notion.so/Configuration)

### 配置并启动Spring容器

@Configuration标注在类上，相当于把该类作为spring的xml配置文件中的，作用为：配置spring容器(应用上下文)

```
import org.springframework.context.annotation.Configuration;

@Configuration
public class TestConfig {

    public TestConfig(){
        System.out.println("testconfig collection  init success");
    }
}
```

相当于

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:context="http://www.springframework.org/schema/context" xmlns:jdbc="http://www.springframework.org/schema/jdbc"
    xmlns:jee="http://www.springframework.org/schema/jee" xmlns:tx="http://www.springframework.org/schema/tx"
    xmlns:util="http://www.springframework.org/schema/util" xmlns:task="http://www.springframework.org/schema/task" xsi:schemaLocation="
        http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-4.0.xsd
        http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-4.0.xsd
        http://www.springframework.org/schema/jdbc http://www.springframework.org/schema/jdbc/spring-jdbc-4.0.xsd
        http://www.springframework.org/schema/jee http://www.springframework.org/schema/jee/spring-jee-4.0.xsd
        http://www.springframework.org/schema/tx http://www.springframework.org/schema/tx/spring-tx-4.0.xsd
        http://www.springframework.org/schema/util http://www.springframework.org/schema/util/spring-util-4.0.xsd
        http://www.springframework.org/schema/task http://www.springframework.org/schema/task/spring-task-4.0.xsd" default-lazy-init="false">

</beans>
```

主方法进行测试:

```java
import org.springframework.context.ApplicationContext;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;

public class Main {

    public static void main(String[] args) {
        // @Configuration注解的spring容器加载方式，用AnnotationConfigApplicationContext替换ClassPathXmlApplicationContext
        ApplicationContext context = new AnnotationConfigApplicationContext(TestConfig.class);

        // 如果加载spring-context.xml文件：
        // ApplicationContext context = new
        // ClassPathXmlApplicationContext("spring-context.xml");
    }
}

// 结果
WARNING: All illegal access operations will be denied in a future release
testconfig collection  init success

Process finished with exit code 0
```

### @Configuration启动容器+@Bean注册Bean

@Bean标注在方法上(返回某个实例的方法)，等价于spring的xml配置文件中的，作用为：注册bean对象

```
@Configuration
public class TestConfig {

    public TestConfig(){
        System.out.println("testconfig collection  init success");
    }

    // @Bean注解注册bean,同时可以指定初始化和销毁方法
    // @Bean(name="testBean",initMethod="start",destroyMethod="cleanup")
    //name属性相当于<bean>标签的id
    @Bean
    @Scope("prototype")
    public TestBean testBean() {
        return new TestBean();
    }
}

class TestBean {

    private String username;
    private String url;
    private String password;

    public void sayHello() {
        System.out.println("TestBean sayHello...");
    }

    public void start() {
        System.out.println("TestBean init...");
    }

    public void cleanup() {
        System.out.println("TestBean destroy...");
    }
}
```

测试类

```
public class Main {

    public static void main(String[] args) {
        // @Configuration注解的spring容器加载方式，用AnnotationConfigApplicationContext替换ClassPathXmlApplicationContext
        ApplicationContext context = new AnnotationConfigApplicationContext(TestConfig.class);
        System.out.println(context);

        // 如果加载spring-context.xml文件：
        // ApplicationContext context = new
        // ClassPathXmlApplicationContext("spring-context.xml");

        //获取bean
        TestBean testBean = (TestBean) context.getBean("testBean");
        testBean.sayHello();
    }
}

// 结果
结果：
testconfig collection  init success
TestBean sayHello...
```

- @Bean注解在返回实例的方法上，如果未通过@Bean指定bean的名称，则默认与标注的方法名相同（第一个单词转小写）
- @Bean注解默认作用域为单例singleton作用域，可通过@Scope(“prototype”)设置为原型作用域
- 既然@Bean的作用是注册bean对象，那么完全可以使用@Component、@Controller、@Service、@Ripository等注解注册bean，当然需要配置@ComponentScan注解进行自动扫描

scope属性1).  singleton属性值（掌握）：默认值，单例2). prototype属性值（掌握）：多例（原型作用域）3). request属性值(了解）：创建对象，把对象放到request域里4). session属性值(了解）：创建对象，把对象放到session域里5). globalSession属性值(了解）：创建对象，把对象放到globalSession域里

### @Bean下管理bean的生命周期

```
// 用上面的例子
//@Bean注解注册bean,同时可以指定初始化和销毁方法
@Bean(name="testBean",initMethod="start",destroyMethod="cleanUp")
@Scope("prototype")
public TestBean testBean() {
  return new TestBean();
}
```

测试类

```
// 结果
testconfig collection  init success
org.springframework.context.annotation.AnnotationConfigApplicationContext@41975e01, started on Mon Jul 19 09:51:42 PST 2021
TestBean init...
TestBean sayHello...
```

### @Configuration启动容器+@Component注册Bean

bean类

```
//添加注册bean的注解
@Component
public class TestBean {

    private String username;
    private String url;
    private String password;

    public void sayHello() {
        System.out.println("TestBean sayHello...");
    }

    public void start() {
        System.out.println("TestBean init...");
    }

    public void cleanup() {
        System.out.println("TestBean destroy...");
    }
}
```

配置类：

```
@Configuration
//添加自动扫描注解，basePackages为TestBean包路径
@ComponentScan(basePackages = "com.example.demo.spring2")
public class TestConfig {

    public TestConfig(){
        System.out.println("testconfig collection  init success");
    }

    // @Bean注解注册bean,同时可以指定初始化和销毁方法
//    @Bean(name="testBean",initMethod="start",destroyMethod="cleanup")
////    @Bean
//    @Scope("prototype")
//    public TestBean testBean() {
//        return new TestBean();
//    }
}
```

测试类:

```
public class Main {

    public static void main(String[] args) {
        // @Configuration注解的spring容器加载方式，用AnnotationConfigApplicationContext替换ClassPathXmlApplicationContext
        ApplicationContext context = new AnnotationConfigApplicationContext(TestConfig.class);

        // 如果加载spring-context.xml文件：
        // ApplicationContext context = new ClassPathXmlApplicationContext("spring-context.xml");

        //获取bean
        TestBean testBean1 = (TestBean) context.getBean("testBean");
        testBean1.sayHello();
    }
}

// 结果
testconfig collection  init success
TestBean sayHello...
```

### AnnotationConfigApplicationContext 注册 AppContext 类的两种方法

第一种:

```
public static void main(String[] args) {

  // @Configuration注解的spring容器加载方式，用AnnotationConfigApplicationContext替换ClassPathXmlApplicationContext
  ApplicationContext context = new AnnotationConfigApplicationContext(TestConfig.class);

  //获取bean
  TestBean tb = (TestBean) context.getBean("testBean");
  tb.sayHello();
}
```

第二种:

```
public class Main {
    public static void main(String[] args) {
        AnnotationConfigApplicationContext annotationConfigApplicationContext = new AnnotationConfigApplicationContext();
        annotationConfigApplicationContext.register(TestConfig.class);
        annotationConfigApplicationContext.refresh();
    }
}
```

### @Configuration组合xml

配置类

```
@Configuration
@ImportResource("classpath:configtest.xml")
public class WebConfig {

    public WebConfig(){
        System.out.println("WebConfig coolection init success");
    }
}
```

实体类

```
public class TestBean2 {

    private String username;
    private String url;
    private String password;

    public void setUsername(String username) {
        this.username = username;
    }

    public void setUrl(String url) {
        this.url = url;
    }

    public void setPassword(String password) {
        this.password = password;
    }

    public void sayHello() {
        System.out.println("TestBean2 sayHello..."+username);
    }

    public void start() {
        System.out.println("TestBean2 init...");
    }

    public void cleanUp() {
        System.out.println("TestBean2 destroy...");
    }
}
```

spring的xml配置文件

```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="testBean2" class="com.example.demo.spring3.TestBean2">
        <property name="username" value="ranjun"/>
    </bean>
</beans>
```

测试类

```
public class TestMain2 {

    public static void main(String[] args) {
        // @Configuration注解的spring容器加载方式，用AnnotationConfigApplicationContext替换ClassPathXmlApplicationContext
        ApplicationContext context = new AnnotationConfigApplicationContext(WebConfig.class);

        // 如果加载spring-context.xml文件：
        // ApplicationContext context = new ClassPathXmlApplicationContext("spring-context.xml");

        // 获取bean
        TestBean2 tb = (TestBean2) context.getBean("testBean2");
        tb.sayHello();
    }
}

// 结果
WebConfig coolection init success
TestBean2 sayHello...ranjun
```

### @Configuration组合xml和其它注解

实体类:

```
public class TestBean {

    private String username;
    private String url;
    private String password;

    public void sayHello() {
        System.out.println("TestBean sayHello...");
    }

    public void start() {
        System.out.println("TestBean init...");
    }

    public void cleanup() {
        System.out.println("TestBean destroy...");
    }
}

public class TestBean2 {

    private String username;
    private String url;
    private String password;

    public void setUsername(String username) {
        this.username = username;
    }

    public void setUrl(String url) {
        this.url = url;
    }

    public void setPassword(String password) {
        this.password = password;
    }

    public void sayHello() {
        System.out.println("TestBean2 sayHello..."+username);
    }

    public void start() {
        System.out.println("TestBean2 init...");
    }

    public void cleanUp() {
        System.out.println("TestBean2 destroy...");
    }
}
```

配置类

```
@Configuration
public class TestConfig {

    public TestConfig(){
        System.out.println("testconfig collection  init success");
    }

    @Bean
    @Scope("prototype")
    public TestBean testBean() {
        return new TestBean();
    }
}

@Configuration
@ImportResource("classpath:configtest.xml")
@Import(TestConfig.class)
public class WebConfig {

    public WebConfig(){
        System.out.println("WebConfig coolection init success");
    }
}
```

测试类:

```
public class TestMain2 {

    public static void main(String[] args) {
        // @Configuration注解的spring容器加载方式，用AnnotationConfigApplicationContext替换ClassPathXmlApplicationContext
        ApplicationContext context = new AnnotationConfigApplicationContext(WebConfig.class);

        // 获取bean
        TestBean tb = (TestBean) context.getBean("testBean");
        tb.sayHello();

        TestBean2 tb2 = (TestBean2) context.getBean("testBean2");
        tb2.sayHello();
    }
}

// 结果
WebConfig coolection init success
testconfig collection  init success
TestBean sayHello...
TestBean2 sayHello...ranjun
```

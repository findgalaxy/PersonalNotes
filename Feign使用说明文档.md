[1.EnableFeignClients配置](https://polars.info/doc/#/Feign使用说明文档?id=_1enablefeignclients配置)

[在Spring托管的类使用@EnableFeignClients](https://polars.info/doc/#/Feign使用说明文档?id=在spring托管的类使用enablefeignclients)

扫描带有@FeignClient注解的接口，并注册配置类（此时的配置类针对当前FeignClient生效）和 FeignClientFactoryBean。

如：在ServiceApplication类上使用了@EnableFeignClients

@EnableFeignClients({"com.hand.hcf.app"}) public class ServiceApplication {    public static void main(String[] args){        SpringApplication app = new SpringApplication(ServiceApplication.class);        app.run(args);    } } 

常用属性:

​    basePackages: String[]，配置FeignClient扫描范围。    value: 与basePackages用法相同。 

[2.FeignClient配置](https://polars.info/doc/#/Feign使用说明文档?id=_2feignclient配置)

[在接口上使用@FeignClient注解，实现Feign客户端](https://polars.info/doc/#/Feign使用说明文档?id=在接口上使用feignclient注解，实现feign客户端)

如：第三方接口添加@FeignClient

@FeignClient(        name = "${hcf.application.base.name:base}",        url = "${hcf.application.base.url:}" ) public interface FeignTestInterface {    @GetMapping({"/api/implement/hello"})    String hello(@RequestParam(value = "keyWord",required = false) String keyWord); } 

常用属性:

​    value: String，配置eureka注册实例。    name: 与value用法相同。    url: String，配置该接口的中的方法，指向的服务器。当配置url时，会通过url调用服务。 

注意：

\1. @FeignClient不能与@RequestMapping混用    在springMVC中，是否需要扫描至requestMapping中，根据类上是否使用特定的注解，在RequestMappingHandlerMapping类中实现方法：        @Override        protected boolean isHandler(Class<?> beanType) {            return (AnnotatedElementUtils.hasAnnotation(beanType, Controller.class) ||                    AnnotatedElementUtils.hasAnnotation(beanType, RequestMapping.class));        } 2. @FeignClient指定配置类时，切记不要被spring容器扫描到，不然会对全局生效。 3. 虽然path可以添加统一前缀，但由于在系统中，第三方接口实现采用强依赖形式，添加的path不会被springMVC扫描，所以禁止使用该属性 

思考：

​    1.注解在不使用@Inherited注解的前提下，哪些元素使用注解会被传递到子类。    2.使用@Inherited注解，哪些元素使用注解会被传递到子类。 

[3 Feign具体实现](https://polars.info/doc/#/Feign使用说明文档?id=_3-feign具体实现)

Feign是一个声明式的Web服务客户端。这使得Web服务客户端的写入更加方便，使用Feign创建一个接口并对其进行注释。它具有可插拔注释支持，还支持可插拔编码器和解码器。Spring Cloud添加了对Spring MVC注释的支持，并在Spring Web中使用默认使用的HttpMessageConverters。Spring Cloud集成Ribbon和Eureka以在使用Feign时提供负载均衡的http客户端。

在介绍Spring Cloud Feign之前，先看一下原生Feign用法，然后再看Spring Cloud Feign是如何集成原生Feign的：

[3.1 原生Feign用法](https://polars.info/doc/#/Feign使用说明文档?id=_31-原生feign用法)

- 1、依赖:

<dependency>  <groupId>io.github.openfeign</groupId>  <artifactId>feign-gson</artifactId>  <version>9.5.0</version> </dependency> 

- 2、发送GET和POST请求方式 [Feign源码](https://github.com/OpenFeign/feign)

public class FeignTest {   interface BookService{      @RequestLine("GET /book/borrow?name={name}&timeout={timeout}")      String borrow(@Param("name")String name,@Param("timeout")Integer timeout);       @RequestLine("POST /book/post")      @Headers("Content-Type: application/json")      @Body("%7B\"id\": \"{id}\", \"name\": \"{name}\"%7D")      String post(@Param("id")String id,@Param("name")String name);  }   public static void main(String[] args) {      BookService bs = Feign.builder()              .options(new Options(2000, 6000))              .target(BookService.class, "http://localhost:2001");      String result = bs.borrow("2w2w",1);      System.out.println(result);      result = bs.post("12345", "spring feign");      System.out.println(result);  } } 

feign核心类介绍

- feign.Feign.Builder设置发送http请求的相关参数，比如http客户端，重试策略，编解码，超时时间等等

- - Client 发送http请求客户端，默认实现feign.Client.Default，使用的是java.net包实现的
  - Retryer 重试，默认实现feign.Retryer.Default，超时延迟100ms开始重试，每隔1s重试一次，重试4次
  - Options 超时时间，默认连接超时10s，读超时60s
  - Encoder 编码器
  - Decoder 解码器
  - RequestInterceptor 请求拦截器，可以在发送http请求之前执行此拦截器
  - ErrorDecoder 异常处理机制，处理异常时，适配为HystrixBadRequestException，好避开circuit breaker(断路器)的统计，避免熔断服务

- HardCodedTarget 定于目标接口和url
- ReflectiveFeign 生成动态代理类，基于jdk的动态代理实现
- feign.InvocationHandlerFactory.Default 接口方法统一拦截器创建工厂
- FeignInvocationHandler 接口统一方法拦截器
- ParseHandlersByName 解析接口方法元数据
- SynchronousMethodHandler.Factory 接口方法的拦截器创建工厂
- SynchronousMethodHandler 接口方法的拦截器，真正拦截的核心，这里真正发起http请求，处理返回结果

![img](D:/Youdao/YoudaoNote/findgalaxy@163.com/8a8816c74b194f768d052b1c3d3132e0/feignflow.png)

在上述示例代码中，到底feign给我做了哪些事情呢？下面的时序图为我们展示整个处理过程

![img](D:/Youdao/YoudaoNote/findgalaxy@163.com/220c05f3cf9f46239be4ab5c06b189d8/eignsequence.png)

1、通过feign.Feign.Builder为我们设置http请求的相关参数，比如http客户端，重试策略，编解码，超时时间，这里都是面向接口编程实现的，我们很容易的进行扩展，比如http客户端，可以使用java原生的实现，也可以使用apache httpclient，亦可以使用okHttpClient，自己喜欢就好，其他属性亦是如此，由此看出feign的设计具有非常好的可扩展性。

2、ReflectiveFeign内部使用了jdk的动态代理为目标接口生成了一个动态代理类，这里会生成一个InvocationHandler(jdk动态代理原理)统一的方法拦截器，同时为接口的每个方法生成一个SynchronousMethodHandler拦截器，并解析方法上的 元数据，生成一个http请求模板。

3、当发起方法调用的时候，被统一的方法拦截器FeignInvocationHandler拦截，再根据不同的方法委托给不同的SynchronousMethodHandler拦截器处理。

4、根据每次方法调用的入参生成http请求模板，如果设置了http请求拦截器，则先经历拦截器的处理，再发起真正的http请求，得到结果后会根据方法放入返回值进行反序列化，最后返回给调用方。

5、如果发生了异常，会根据重试策略进行重试。

[3.2 Spring Cloud Feign用法](https://polars.info/doc/#/Feign使用说明文档?id=_32-spring-cloud-feign用法)

[3.2.1基本用法](https://polars.info/doc/#/Feign使用说明文档?id=_321基本用法)

参照 EnableFeignClients配置以及 FeignClient配置。

[3.2.1 开启@EnableFeignClients后台运行步骤](https://polars.info/doc/#/Feign使用说明文档?id=_321-开启enablefeignclients后台运行步骤)

1、扫描EnableFeignClients注解上的配置信息，注册默认的配置类，这个配置类是对所有FeignClient的都是生效的，即为全局的配置。

2、扫描带有@FeignClient注解的接口，并注册配置类（此时的配置类针对当前FeignClient生效）和FeignClientFactoryBean，此bean实现了FactoryBean接口，我们知道spring有两种类型的bean对象，一种是普通的bean，另一种则是工厂bean（FactoryBean），它返回的其实是getObject方法返回的对象（更多关于FactoryBean的相关信息请查看spring官方文档）。getObject方法就是集成原生feign的核心方法，当spring注入FeignClient接口时，getObject方法会被调用，得到接口的代理类。

[3.2.2 自动装载配置类](https://polars.info/doc/#/Feign使用说明文档?id=_322-自动装载配置类)

自动加载配置类FeignAutoConfiguration，FeignClientsConfiguration，FeignRibbonClientAutoConfiguration，这三个类为feign提供了所有的配置类。

例如FeignClientsConfiguration实现：

@Configuration public class FeignClientsConfiguration {     @Autowired    private ObjectFactory<HttpMessageConverters> messageConverters;     @Autowired(required = false)    private List<AnnotatedParameterProcessor> parameterProcessors = new ArrayList<>();     @Autowired(required = false)    private List<FeignFormatterRegistrar> feignFormatterRegistrars = new ArrayList<>();     @Autowired(required = false)    private Logger logger;     @Bean    @ConditionalOnMissingBean    public Decoder feignDecoder() {        return new OptionalDecoder(new ResponseEntityDecoder(new SpringDecoder(this.messageConverters)));    }     @Bean    @ConditionalOnMissingBean    public Encoder feignEncoder() {        return new SpringEncoder(this.messageConverters);    }     @Bean    @ConditionalOnMissingBean    public Contract feignContract(ConversionService feignConversionService) {        return new SpringMvcContract(this.parameterProcessors, feignConversionService);    }     @Bean    public FormattingConversionService feignConversionService() {        FormattingConversionService conversionService = new DefaultFormattingConversionService();        for (FeignFormatterRegistrar feignFormatterRegistrar : feignFormatterRegistrars) {            feignFormatterRegistrar.registerFormatters(conversionService);        }        return conversionService;    }     @Configuration    @ConditionalOnClass({ HystrixCommand.class, HystrixFeign.class })    protected static class HystrixFeignConfiguration {        @Bean        @Scope("prototype")        @ConditionalOnMissingBean        @ConditionalOnProperty(name = "feign.hystrix.enabled")        public Feign.Builder feignHystrixBuilder() {            return HystrixFeign.builder();        }    }     @Bean    @ConditionalOnMissingBean    public Retryer feignRetryer() {        return Retryer.NEVER_RETRY;    }     @Bean    @Scope("prototype")    @ConditionalOnMissingBean    public Feign.Builder feignBuilder(Retryer retryer) {        return Feign.builder().retryer(retryer);    }     @Bean    @ConditionalOnMissingBean(FeignLoggerFactory.class)    public FeignLoggerFactory feignLoggerFactory() {        return new DefaultFeignLoggerFactory(logger);    } } 

注：其中ConditionalOne相关注解使用说明

@ConditionalOnBean（仅仅在当前上下文中存在某个对象时，才会实例化一个Bean） @ConditionalOnClass（某个class位于类路径上，才会实例化一个Bean） @ConditionalOnExpression（当表达式为true的时候，才会实例化一个Bean） @ConditionalOnMissingBean（仅仅在当前上下文中不存在某个对象时，才会实例化一个Bean） @ConditionalOnMissingClass（某个class类路径上不存在的时候，才会实例化一个Bean） @ConditionalOnNotWebApplication（不是web应用） 

由此可见，需要覆盖默认配置时，只需要相应注入bean即可。
# springclouddemo
Spring Cloud 入门学习项目

SpringCloud-服务注册与实现-Eureka示例代码


SpringCloud-服务注册与实现-Eureka创建服务提供者示例源码


SpringCloud-创建服务消费者-Ribbon方式示例代码


SpringCloud-创建服务消费者-Feign方式示例代码


SpringCloud-使用熔断器防止服务雪崩-Ribbon和Feign方式示例代码

========================================================
8761 clouddemo-eureka 注册中心

8762 service-admin 服务提供者provider
 
8769 clouddemo-zuul 网关服务
      
8764 web-admin-ribbon 服务comsumer方式ribbon
8765 web-admin-feign  服务comsumer方式feign

http://localhost:8761/  管理界面

http://localhost:8769/api/a/hi?message=HelloZuulFromYw 请求方式ribbon

http://localhost:8769/api/b/hi?message=HelloZuulFromYw 请求方式feign
=============================================================================
方式ribbon 和 方式feign 区别
=============================================================================
【方式ribbon】
    @Autowired
    private RestTemplate restTemplate;

    @HystrixCommand(fallbackMethod = "hiError")
    public String sayHi(String message) {
        return restTemplate.getForObject("http://service-admin/hi?message=" + message, String.class);
    }

    public String hiError(String message) {
        return "Hi，your message is :\"" + message + "\" but request error.";
    }
=============================================================================
【方式feign】
		@FeignClient(value = "service-admin", fallback = AdminServiceHystrix.class)
		public interface AdminService {

    @RequestMapping(value = "hi", method = RequestMethod.GET)
    public String sayHi(@RequestParam(value = "message") String message);
   
		==========================================================
    @Component
		public class AdminServiceHystrix implements AdminService {

    @Override
    public String sayHi(String message) {
        return "Hi，your message is :\"" + message + "\" but request error.";
    }
    
=============================================================================
【clouddemo-zuul 网关服务】配置和作用？

zuul:
  routes:
    api-a:
      path: /api/a/**
      serviceId: web-admin-ribbon
    api-b:
      path: /api/b/**
      serviceId: web-admin-feign
		=============================================================
		public class WebAdminFeignFallbackProvider implements FallbackProvider {

    @Override
    public String getRoute() {
        // ServiceId，如果需要所有调用都支持回退，则 return "*" 或 return null
        return "web-admin-feign";
    }
==============================================================================
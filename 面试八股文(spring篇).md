## 1.Spring MVC的执行流程
  分为几个步骤:
  1) 用户发起请求,请求被servlet拦截发给spring mvc框架
  2) spring mvc里的DispatchServlet核心控制器,接收到请求并且转发给HandlerMapping
  3) HandlerMapping负责解析请求,根据请求信息和配置信息找到匹配的Controller,如果有拦截器,先执行preHandle方法
  4) 找到Controller会执行里面的方法
  5) Controller方法执行完后会返回一个ModelAndView,包含视图名称和需要的数据
  6) ViewResolver根据名称找到视图,渲染成html内容返回给客户端

## 2.对spring boot 约定优于配置的理解
 基于传统的spring web应用,我们需要做很多业务无关,而且只需要做一次的配置,比如管理jar,web.xml维护,Dispatch-servlet配置项维护,应用部署到Web容器,而spring boot不需要再做这些,已经自动帮我们做好了,我们只需要更多关注业务.
 ### spring boot 约定优于配置的体现有很多
   1) spring boot starter启动依赖,帮我们管理所有的jar版本
   2) 当前应用依赖了springmvc相关的jar,springboot自动内置Tomcat来运行web应用,不需要再单独做应用部署
   3) springboot自动装配机制的实现中,通过扫描约定路径下的spring.factories文件来识别配置类,实现Bean的自动装配
 

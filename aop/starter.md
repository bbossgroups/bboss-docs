# ioc starter的使用
```java
//将配置文件中的sql转存到数据库中
String dslpath = "esmapper/demo7.xml";

//ioc容器对象ApplicationContext增加start方法，用于根据传入的回调接口Starter运行相关的业务功能,例如：
BaseApplicationContext context = DefaultApplicationContext.getApplicationContext(dslpath);
context.start(new Starter() {
   public void start(Pro pro, BaseApplicationContext ioc) {
      Object _service = ioc.getBeanObject(pro.getName());
      if(_service == null)
         return;

   }

   public void failed(Pro bean, BaseApplicationContext baseApplicationContext,Throwable e) {
      if (logger.isErrorEnabled())
      {
         logger.error(new StringBuilder().append("Kafka Listener[name:").append(bean.getName())
                     .append(",class:").append(bean.getClazz())
                     .append("] start failed:").toString(),
               e);

      }
   }
});
```
### bboss序列化cglib代理对象方法介绍

本文介绍bboss序列化cglib代理对象方法。经过cglib代理过的po类对象实例是原来po对象的子类对象，bboss在序列化这种对象时，需要经过特殊处理，否则会出现不可预知的错误，导致序列化失败，典型的例子就是hibernate查询操作返回的po对象就是cglib代理对象。

bboss序列化cglib代理对象方法非常简单，与序列化普通对象的方法一样，使用代码如下：

Java代码 

```java
@Test  
    public void testCGlibSerial() throws Exception  
    {  
        //远程调用  
        SerialPO po = new SerialPO();  
        po.setJob("架构工程师");  
        po.setName("张三");  
        CGLibProxy proxy = new CGLibProxy(po);  
        SerialPO po2 = CGLibUtil.getBeanInstance(po.getClass(), po  
                .getClass(), proxy);  
        String xml = ObjectSerializable.toXML(po2);  
        System.out.println(xml);  
        po = ObjectSerializable.toBean(xml, SerialPO.class);  
        System.out.println("name:"+po.getName());  
        System.out.println("job:"+po.getJob());  
    }  
      
    public static class SerialPO  
    {  
        private String name;  
        private String job;  
        public String getName() {  
            return name;  
        }  
        public void setName(String name) {  
            this.name = name;  
        }  
        public String getJob() {  
            return job;  
        }  
        public void setJob(String job) {  
            this.job = job;  
        }  
    }  
```


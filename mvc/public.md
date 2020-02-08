### bboss mvc 新增特性-防止控制器中public方法成为控制器方法接收客户端请求

bboss3.6.0及后续版本增加特性-防止控制器中public方法成为控制器方法接收客户端请求，通过该特性可以使控制器层安全保障能力得到有效的增强和加固。本特征具体描述为：

1.自动排除控制器中的属性的get/set方法成为控制器方法

2.新增注解ExcludeMethod,可以通过它标注方法不作为控制器方法

org.frameworkset.web.servlet.handler.annotations.ExcludeMethod

ExcludeMethod注解的使用方法非常简单，下面说明一下：

@ExcludeMethod

public final void helloword(String name,String address)

{

​      //dosomething here.

}

本特性虽说是一个小小的改进，但是作用却非常大，感兴趣的话不妨一试，前提是你要升级到最新的[bboss3.6.0](https://github.com/bbossgroups/bbossgroups-3.5/tree/bboss3.6.0)版本。
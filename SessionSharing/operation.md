### bboss 会话管理session操作使用示例

bboss 会话管理session操作使用示例(遵循servlet标准规范)：
Java代码 

```java
HttpSession session = request.getSession();//request.getSession(true)  
  
session.setMaxInactiveInterval(180);//修改session有效期,单位秒  
  
TestVO testVO = new TestVO();  
  
testVO.setId("sessionmoitor testvoid");  
  
TestVO1 testVO1 = new TestVO1();  
  
testVO1.setName("hello,sessionmoitor test vo1");  
  
testVO.setTestVO1(testVO1);  
  
session.setAttribute("testVO", testVO);  
  
testVO = (TestVO)session.getAttribute("testVO");  
  
//修改testVO中属性的值  
  
testVO.setId("testvoidaaaaa,sessionmonitor modifiy id");  
  
//需要将修改后的对象重新设置到session中否则无法存储最新的testVO到mongodb中  
  
session.setAttribute("testVO", testVO);  
  
testVO = (TestVO)session.getAttribute("testVO");  
```

session创建、获取和失效实例：
Java代码

```java
<%@ page contentType="text/html; charset=UTF-8" session="false"%>  
  
<%  
out.println("<div>ID request.getSession(true).getId():"+request.getSession(true).getId()+"</div>");//创建session  
request.getSession().invalidate();//使session失效  
out.println("<div>after invalidate session request.getSession(false):"+request.getSession(false)+"</div>");//这里获取到session为null  
out.println("<div>after invalidate session request.getSession().getId():"+request.getSession().getId()+"</div>");//这里会重新创建新的session  
out.println("<div>ID request.getSession(true).getId():"+request.getSession(true).getId()+"</div>");  
 %>  
```

  更多使用方法参考文档：

http://yin-bp.iteye.com/category/327553  
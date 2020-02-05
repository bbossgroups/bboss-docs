### bboss session共享时序列化存储jasperreports报表对象JasperPrint方法

bboss session共享时序列化存储jasperreports报表对象JasperPrint方法[bboss session共享组件使用方法介绍](http://yin-bp.iteye.com/blog/2064662)

由于JasperPrint对象有点特殊，序列化存储到session时，必须采用java自带的序列化机来序列化和还原JasperPrint对象，否则bboss序列化机制无法实现对原始JasperPrint对象的序列化。具体的设置JasperPrint对象到共享session中的方法如下：

Java代码

```java
public static void toSession(HttpServletRequest request,String key,JasperPrint jasperPrint) throws IOException  
    {  
        java.io.ByteArrayOutputStream out = null;   
        java.io.ObjectOutputStream output = null;  
        try  
        {  
            out = new ByteArrayOutputStream();   
            output = new java.io.ObjectOutputStream(out);   
            output.writeObject(jasperPrint);  
            output.flush();  
            request.getSession().setAttribute(key, out.toByteArray());  
        }  
        finally  
        {  
            try {  
                if(out != null)  
                {  
                    out.close();  
                    out = null;  
                }  
            } catch (Exception e) {  
                // TODO Auto-generated catch block  
                e.printStackTrace();  
            }  
            try {  
                if(output != null)  
                {  
                    output.close();  
                    output = null;  
                }  
            } catch (Exception e) {  
                // TODO Auto-generated catch block  
                e.printStackTrace();  
            }  
        }  
    }  
      
    public static JasperPrint getJasperPrint(HttpServletRequest request,String key)  
    {  
        JasperPrint jasperPrint = null;  
      
        java.io.ObjectInputStream output = null;  
        java.io.ByteArrayInputStream intput = null;  
        try  
        {  
            byte[] b = (byte[])request.getSession().getAttribute(key);   
            intput = new ByteArrayInputStream(b);  
            output = new java.io.ObjectInputStream(intput);   
            jasperPrint = (JasperPrint)output.readObject();  
            return jasperPrint;  
              
        }  
        catch(Exception e)  
        {  
            e.printStackTrace();  
            return null;  
        }  
        finally  
        {  
            try {  
                if(intput != null)  
                {  
                    intput.close();  
                    intput = null;  
                }  
            } catch (Exception e) {  
                // TODO Auto-generated catch block  
                e.printStackTrace();  
            }  
            try {  
                if(output != null)  
                {  
                    output.close();  
                    output = null;  
                }  
            } catch (Exception e) {  
                // TODO Auto-generated catch block  
                e.printStackTrace();  
            }  
        }  
    }  
```

下文中提供从共享session中存储和读取jasperprint对象的另外一种更加友好的解决方案：

bboss自定义类对象序列化机制介绍
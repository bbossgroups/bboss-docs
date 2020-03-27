### ModelAndView使用方法-一个关于校验码生成的bbossmvc restful示例1

bboss mvc即将伴随bbossgroups 2.1一起发布，这里我们先看一个bboss-mvc 的restful风格的案例：

1.图形码校验控制器-ImageValidatorController

Java代码

```java
/* 
 *  Copyright 2008 biaoping.yin 
 * 
 *  Licensed under the Apache License, Version 2.0 (the "License"); 
 *  you may not use this file except in compliance with the License. 
 *  You may obtain a copy of the License at 
 * 
 *      http://www.apache.org/licenses/LICENSE-2.0 
 * 
 *  Unless required by applicable law or agreed to in writing, software 
 *  distributed under the License is distributed on an "AS IS" BASIS, 
 *  WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied. 
 *  See the License for the specific language governing permissions and 
 *  limitations under the License. 
 */  
package org.frameworkset.web.spi.validator;  
  
import javax.servlet.http.HttpServletRequest;  
import javax.servlet.http.HttpServletResponse;  
import javax.servlet.http.HttpSession;  
  
import org.frameworkset.util.annotations.Attribute;  
import org.frameworkset.util.annotations.AttributeScope;  
import org.frameworkset.util.annotations.HandlerMapping;  
import org.frameworkset.util.annotations.PathVariable;  
import org.frameworkset.util.annotations.RequestMethod;  
import org.frameworkset.util.annotations.RequestParam;  
import org.frameworkset.web.servlet.ModelAndView;  
  
/** 
 * <p>Title: ImageValidatorController.java</p>  
 * <p>Description: </p> 
 * <p>bboss workgroup</p> 
 * <p>Copyright (c) 2008</p> 
 * @Date 2010-11-13 
 * @author biaoping.yin 
 * @version 1.0 
 */  
@HandlerMapping("/rest/imagevalidator")  
public class ImageValidatorController  {  
    //http://localhost:8080/bboss-mvc/rest/imagevalidator/abcefg1234/6  
    @HandlerMapping(value="/{codelist}/{codenum}",method={RequestMethod.GET,RequestMethod.POST})  
    public void generateImageCode(  
                                  @PathVariable("codelist") String codelist,  
                                  @PathVariable("codenum") int codenum,  
                                  HttpServletRequest request,HttpServletResponse response)  
    {  
        String codekey  = "imagecodekey";  
        HttpSession session = request.getSession(true);  
        RandImgCreater rc = new RandImgCreater(response,codenum,codelist);  
  
        String rand = rc.createRandImage();  
        session.setAttribute(codekey,rand);  
  
    }  
//  http://localhost:8080/bboss-mvc/rest/imagevalidator  
    @HandlerMapping(method={RequestMethod.POST,RequestMethod.GET})  
    public ModelAndView checkImageCode(@RequestParam(name="imagecode") String imagecode,  
            @Attribute(name="imagecodekey",scope=AttributeScope.SESSION_ATTRIBUTE) String oldcode)  
    {  
        String message = "";  
        if(imagecode == null || imagecode.equals(""))  
            message = "请输入图片校验码。";  
        else if(oldcode == null)  
            message = "对不起，你已经提交过了。";  
        else if(!imagecode.equals(oldcode))  
        {  
            message = "校验码有误，请重新输入。";  
        }  
        else  
        {  
            message = "ok。";  
        }     
              
        ModelAndView view = new ModelAndView("/validate/imagevalidate","message",message);  
        return view;  
  
    }  
      
      
}  
```

2.生成图形码的组件

Java代码

```java
/* 
 *  Copyright 2008 biaoping.yin 
 * 
 *  Licensed under the Apache License, Version 2.0 (the "License"); 
 *  you may not use this file except in compliance with the License. 
 *  You may obtain a copy of the License at 
 * 
 *      http://www.apache.org/licenses/LICENSE-2.0 
 * 
 *  Unless required by applicable law or agreed to in writing, software 
 *  distributed under the License is distributed on an "AS IS" BASIS, 
 *  WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied. 
 *  See the License for the specific language governing permissions and 
 *  limitations under the License. 
 */  
package org.frameworkset.web.spi.validator;  
  
import java.awt.Color;  
import java.awt.Font;  
import java.awt.Graphics;  
import java.awt.image.BufferedImage;  
import java.io.IOException;  
import java.util.Random;  
  
import javax.imageio.ImageIO;  
import javax.servlet.http.HttpServletResponse;  
  
/** 
 * <p> 
 * Title: RandImgCreater.java 
 * </p> 
 * <p> 
 * Description: 
 * </p> 
 * <p> 
 * bboss workgroup 
 * </p> 
 * <p> 
 * Copyright (c) 2008 
 * </p> 
 *  
 * @Date 2010-11-13 
 * @author biaoping.yin 
 * @version 1.0 
 */  
public class RandImgCreater {  
    private static final String CODE_LIST = "ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz1234567890";  
    private HttpServletResponse response = null;  
    private static final int HEIGHT = 20;  
    private static final int FONT_NUM = 4;  
    private int width = 0;  
    private int iNum = 0;  
    private String codeList = "";  
    private boolean drawBgFlag = false;  
  
    private int rBg = 0;  
    private int gBg = 0;  
    private int bBg = 0;  
  
    public RandImgCreater(HttpServletResponse response) {  
        this.response = response;  
        this.width = 13 * FONT_NUM + 12;  
        this.iNum = FONT_NUM;  
        this.codeList = CODE_LIST;  
    }  
  
    public RandImgCreater(HttpServletResponse response, int iNum,  
            String codeList) {  
        this.response = response;  
        this.width = 13 * iNum + 12;  
        if(iNum > 0)  
            this.iNum = iNum;  
        if(codeList != null)  
            this.codeList = codeList;  
    }  
  
    public String createRandImage() {  
        BufferedImage image = new BufferedImage(width, HEIGHT,  
                BufferedImage.TYPE_INT_RGB);  
  
        Graphics g = image.getGraphics();  
  
        Random random = new Random();  
  
        if (drawBgFlag) {  
            g.setColor(new Color(rBg, gBg, bBg));  
            g.fillRect(0, 0, width, HEIGHT);  
        } else {  
            g.setColor(getRandColor(200, 250));  
            g.fillRect(0, 0, width, HEIGHT);  
  
            for (int i = 0; i < 155; i++) {  
                g.setColor(getRandColor(140, 200));  
                int x = random.nextInt(width);  
                int y = random.nextInt(HEIGHT);  
                int xl = random.nextInt(12);  
                int yl = random.nextInt(12);  
                g.drawLine(x, y, x + xl, y + yl);  
            }  
        }  
  
        g.setFont(new Font("Times New Roman", Font.PLAIN, 18));  
  
        String sRand = "";  
        for (int i = 0; i < iNum; i++) {  
            int rand = random.nextInt(codeList.length());  
            String strRand = codeList.substring(rand, rand + 1);  
            sRand += strRand;  
            g.setColor(new Color(20 + random.nextInt(110), 20 + random  
                    .nextInt(110), 20 + random.nextInt(110)));  
            g.drawString(strRand, 13 * i + 6, 16);  
        }  
        g.dispose();  
        try {  
            ImageIO.write(image, "JPEG", response.getOutputStream());  
        } catch (IOException e) {  
  
        }  
  
        return sRand;  
    }  
  
    public void setBgColor(int r, int g, int b) {  
        drawBgFlag = true;  
        this.rBg = r;  
        this.gBg = g;  
        this.bBg = b;  
    }  
  
    private Color getRandColor(int fc, int bc) {  
        Random random = new Random();  
        if (fc > 255)  
            fc = 255;  
        if (bc > 255)  
            bc = 255;  
        int r = fc + random.nextInt(bc - fc);  
        int g = fc + random.nextInt(bc - fc);  
        int b = fc + random.nextInt(bc - fc);  
        return new Color(r, g, b);  
    }  
  
}  
```

3.展示的jsp页面WebRoot/jsp/validate/imagevalidate.jsp

Html代码 

```html
<%@ page contentType="text/html;charset=UTF-8"  %>  
<%@ taglib uri="/WEB-INF/pager-taglib.tld" prefix="pg"%>  
<form name="frm" method="post" action="/bboss-mvc/rest/imagevalidator">  
Hello Image Test<br/>  
checkCode:<img src="/bboss-mvc/rest/imagevalidator/abcdefghijk123456/5"><br/>  
please input the checkCode:<input type="text" name="imagecode" value=""><br/>  
<input type="submit" name="btn1" value="check">  
</form>   
  
${message}  
```

4.控制器配置

5.使用方法和说明

ImageValidatorController映射的根路径为/rest/imagevalidator：

Java代码

```java
@HandlerMapping("/rest/imagevalidator")  
public class ImageValidatorController   
```

方法generateImageCode方法可以映射到以下地址，主要用来生成图形码：

Java代码

```java
http://localhost:8080/bboss-mvc/rest/imagevalidator/abcefg1234/6 
```

Java代码 

```java
@HandlerMapping(value="/{codelist}/{codenum}",method={RequestMethod.GET,RequestMethod.POST})  
public void generateImageCode(  
                              @PathVariable("codelist") String codelist,  
                              @PathVariable("codenum") int codenum,  
                              HttpServletRequest request,HttpServletResponse response) 
```

Java代码

```java
地址中的abcefg1234段对应generateImageCode方法的codelist参数，标识生成图像码的种子  
地址段中的/6对应generateImageCode方法的codenum参数，标识生成图像码位数  
这样generateImageCode方法根据这两个参数生成相应的图形码，并将图像码存放到以imagecodekey作为key的session属性中。  
以便后续校验之用。  
```

方法checkImageCode用来校验用户输入的图形码和generateImageCode方法存放在session中图像码，根据比较的结果将相应的结果信息输出到前端，比较完毕后跳转到地址：

Java代码

```java
/validate/imagevalidate  
```

bboss-mvc根据映射规则将这个地址映射到页面WebRoot/jsp/validate/imagevalidate.jsp

Java代码

```java
http://localhost:8080/bboss-mvc/rest/imagevalidator  
    @HandlerMapping(method={RequestMethod.POST,RequestMethod.GET})  
    public ModelAndView checkImageCode(@RequestParam(name="imagecode") String imagecode,  
            @Attribute(name="imagecodekey",scope=AttributeScope.SESSION_ATTRIBUTE) String oldcode)  
方法参数对应  
imagecode参数的值，  
oldcode参数的值自动从session中通过key   
imagecodekey自动获取 
```

6.映射规则配置bboss-mvc.xml

Xml代码 

```xml
<property name="viewResolver" class="org.frameworkset.web.servlet.view.InternalResourceViewResolver" singlable="true">  
        <property name="viewClass" value="org.frameworkset.web.servlet.view.JstlView"/>  
        <property name="prefix" value="/jsp/"/>  
        <property name="suffix" value=".jsp"/>  
    </property>  
```


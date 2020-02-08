### bboss libreoffice结合使用说明

bboss libreoffice结合使用说明已经文件下载插件完善

最近增加工具类[/bboss-plugin-wordpdf/src/org/frameworkset/http/converter/wordpdf/FileConvertor.java](https://github.com/bbossgroups/bboss-plugins/blob/master/bboss-plugin-wordpdf/src/org/frameworkset/http/converter/wordpdf/FileConvertor.java)

通过jodconvertor组件结合，利用libreoffice实现word模板书签和值合并功能方便地借助swftool和Flashprinter实现word向pdf，swf的转换

o 文件下载插件增加对FileBlob的支持

FileBlob增加构造函数：

public FileBlob( File data,int rendtype)

public FileBlob( String file,int rendtype)

两个构造函数含义是一致的，参数说明：

第一个参数：下载或者浏览的文件对象或者文件路径

第二个参数：标识文件是用来下载还是用来浏览，对应于FileBlob中的两个常量：FileBlob.BROWSER，FileBlob.DOWNLOAD，默认值为FileBlob.DOWNLOAD

**使用实例一，控制器下载文件方法**：

Java代码

```java
public @ResponseBody FileBlob downloadSWFTempAllUseOpenOffice() throws Exception  {  
        System.out.println("--------------程序执行到此处------------------");  
        String[] bookMarks = new String[] { "DealerName", "Name", "CgName",  
                "TypeName", "OrderQty", "CoolCode", "ChassisCode", "CustPrice",  
                "CustAmt", "sumall", "EarnestPayDays", "EarnestAmt",  
                "StageDate", "FirstAmt", "DepositPercent", "Deposit",  
                "ServiceChargePercent", "ServiceCharge", "NotarizationFee",  
                "InsuranceTerm", "Insurance", "ReinsuranceDeposit",  
                "FinanceAmt", "FinanceFC", "LackAmtPayDate",  
                "LackAmtFinalPayDate", "ReceiverName", "ReceiverID",     
                "ReceiverTel", "Insurer","authoriate" };  
        String[] mapValue = new String[] { "工程机械有限公司", "工程机械有限公司",  
                "六桥车", "xxx52E(6)", "2", "风冷", "V09660ffff", "300.00",  
                "600.00", "陆万元整", "7", "100", "2000年8月31日", "60", "5", "3",  
                "10", "6", "10", "5", "10", "21", "540", "10", "2000年8月31日",  
                "2000年8月31日", "xxx", "430111199910102121", "13800138200", "xxxxx","bboss" };  
        String hetongbianhao = "20121222";  
        String wordtemplate = "/opt/tomcat/wordpdf/anjie.doc";  
        String pdfpath = "/opt/tomcat/test/anjieswftools_" + hetongbianhao + ".pdf";  
        String wordfile = "/opt/tomcat/test/anjie_testswftools" + hetongbianhao + ".doc";  
        String toswfpath = "/opt/tomcat/test/contractswftools_" + hetongbianhao + ".swf";  
  
        String officeHome = "/opt/LibreOffice 3.6/";  
        File f = new File(toswfpath);  
        if(!f.exists())  
        {  
  
            FileConvertor.init( officeHome);  
                          
            FileConvertor.getRealWordByOpenoffice(wordtemplate, wordfile,bookMarks, bookdatas);  
            FileConvertor.wordToPDFByOpenOffice(wordfile, pdfpath);  
            FileConvertor.swftoolsConvert(swftoolWorkDir, pdfpath, toswfpath);  
              
        }         
        FileBlob fileblob = new FileBlob(toswfpath,FileBlob.DOWNLOAD);  
        return fileblob;  
          
          
  
    }     
```

**实例二，结合wordpdfswf插件生成swf文件并在界面上展示**：

Java代码 

```java
public @ResponseBody FileBlob getSWFTemp() throws Exception  {  
        System.out.println("--------------程序执行到此处------------------");  
        String[] bookMarks = new String[] { "DealerName", "Name", "CgName",  
                "TypeName", "OrderQty", "CoolCode", "ChassisCode", "CustPrice",  
                "CustAmt", "sumall", "EarnestPayDays", "EarnestAmt",  
                "StageDate", "FirstAmt", "DepositPercent", "Deposit",  
                "ServiceChargePercent", "ServiceCharge", "NotarizationFee",  
                "InsuranceTerm", "Insurance", "ReinsuranceDeposit",  
                "FinanceAmt", "FinanceFC", "LackAmtPayDate",  
                "LackAmtFinalPayDate", "ReceiverName", "ReceiverID",     
                "ReceiverTel", "Insurer","authoriate" };  
        String[] mapValue = new String[] { "工程机械有限公司", "工程机械有限公司",  
                "六桥车", "xxx", "2", "风冷", "V09660", "300.00",  
                "600.00", "陆万元整", "7", "100", "2000年8月31日", "60", "5", "3",  
                "10", "6", "10", "5", "10", "21", "540", "10", "2000年8月31日",  
                "2000年8月31日", "xxx", "430111199910102121", "13800138200", "xxx","bboss" };  
        String hetongbianhao = "20121222";  
        String wordtemplate = "/opt/tomcat/wordpdf/anjie.doc";  
        String pdfpath = "/opt/tomcat/test/anjieswftools_" + hetongbianhao + ".pdf";  
        String wordfile = "/opt/tomcat/test/anjie_testswftools" + hetongbianhao + ".doc";  
        String toswfpath = "/opt/tomcat/test/contractswftools_" + hetongbianhao + ".swf";  
  
        String officeHome = "/opt/LibreOffice 3.6/";  
        File f = new File(toswfpath);  
        if(!f.exists())  
        {  
  
            FileConvertor.init( officeHome);  
                          
            FileConvertor.getRealWordByOpenoffice(wordtemplate, wordfile,bookMarks, bookdatas);  
            FileConvertor.wordToPDFByOpenOffice(wordfile, pdfpath);  
            FileConvertor.swftoolsConvert(swftoolWorkDir, pdfpath, toswfpath);  
              
        }         
        FileBlob fileblob = new FileBlob(toswfpath,FileBlob.BROWSER);  
        return fileblob;  
          
  
    }  
```

注意我们这里使用了bboss 的word转pdf、swf插件FileConvertor

我们看看怎样通过FlashPlayer来展示生成的swf文件：

Java代码

```java
<%@ page contentType="text/html; charset=utf-8"%>  
<html>  
<head>  
<title></title>  
</head>  
<body marginwidth="0" marginheight="0">  
      
<embed height="100%" width="100%" name="plugin" src="getSWFTemp.page"  
                type="application/x-shockwave-flash">  
</body>  
</html>  
```

其中的src="getSWFTemp.page"就对应实例三中的控制器方法。
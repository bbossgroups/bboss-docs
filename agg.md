# Elasticsearch聚合查询案例分享

![bboss](https://static.oschina.net/uploads/user/47/94045_50.jpg?t=1386945037000)

 

Elasticsearch聚合查询案例分享  



# **1.案例介绍** 

本文包含三个案例:

案例1：统计特定时间范围内每个应用的总访问量、访问成功数、访问失败数,每个应用请求响应时间分段统计（1秒内，1-3秒，3-5秒，5秒以上 ）

案例2：简单的term统计

案例3：简单的cardinality统计



#   **2.准备工作** 


参考文档《[高性能elasticsearch ORM开发库使用介绍](development.md)》中的第1章节和第2章节，在自己的工程中导入bboss es依赖包和配置es参数 



#  **3.案例**



## **3.1 案例1** 



###  **3.1.1 定义统计dsl** 


在源码目录下新建文件esmapper/estrace/ESTracesMapper.xml，内容如下 

```
<properties>
    <!--
    应用汇总统计：总访问量，成功数，失败数
   bboss es dao通过名称applicationSumStatic引用脚本
    -->
    <property name="applicationSumStatic">
        <![CDATA[
        {
            "query": {
                "bool": {
                    "filter": [
                        #if($channelApplications && $channelApplications.size() > 0)
                        {
                            "terms": {
                                "applicationName.keyword": [ ##指定并统计多个应用的数据
                                #foreach($application in $channelApplications)
                                   #if($velocityCount > 0),#end $application.applicationName
                                #end
                                ]
                            }
                        },
                        #end
                        {"range": {
                                "startTime": {
                                    "gte": #[startTime],##统计开始时间
                                    "lt": #[endTime]  ##统计截止时间
                                }
                            }
                        }
                    ]
                }
            },
            "size":0,
            "aggs": {
                "applicationsums": {
                      "terms": {
                        "field": "applicationName.keyword",##按应用名称进行统计计数
                        "size":10000
                      },
                      "aggs":{
                            "successsums" : {
                                "terms" : {
                                    "field" : "err" ##按err标识统计每个应用的成功数和失败数，0标识成功，1标识失败
                                }
                            },
                            "elapsed_ranges" : {
                                "range" : {
                                    "field" : "elapsed", ##按响应时间分段统计
                                    "keyed" : true,
                                    "ranges" : [
                                        { "key" : "1秒", "to" : 1000 },
                                        { "key" : "3秒", "from" : 1000, "to" : 3000 },
                                        { "key" : "5秒", "from" : 3000, "to" : 5000 },
                                        { "key" : "5秒以上", "from" : 5000 }
                                    ]
                                }
                            }
                      }
                }
            }
        }
        ]]>
    </property>
</properties>
```



###  **3.1.2 编写统计dao及统计方法** 

Java代码 

```
public class TraceESDao {    
    public List<ApplicationStatic> getApplicationSumStatic(TraceExtraCriteria traceExtraCriteria){
    	ClientInterface clientUtil= ElasticSearchHelper.getConfigRestClientUtil("esmapper/estrace/ESTracesMapper.xml");
    	//返回json统计报文，调试用，一遍根据json报文组装统计结果列表
//		String response = clientUtil.executeRequest("trace-*/_search",
//                                  "applicationSumStatic",traceExtraCriteria);
		//根据条件进行统计，在对象traceExtraCriteria中指定开始时间和结束时间
		MapRestResponse restResponse = clientUtil.search("trace-*/_search",
				                      "applicationSumStatic",traceExtraCriteria);

		//组装统计结果
		//获取应用统计列表，包含每个应用的名称、总访问量以及成功数和失败数
		List<Map<String,Object>> appstatics = (List<Map<String,Object>>)restResponse.getAggBuckets("applicationsums");
		if(appstatics != null && appstatics.size() > 0) {
			List<ApplicationStatic> applicationStatics = new ArrayList<ApplicationStatic>(appstatics.size());
			ApplicationStatic applicationStatic = null;
			for (int i = 0; i < appstatics.size(); i++) {
				applicationStatic = new ApplicationStatic();
				Map<String, Object> map = appstatics.get(i);
				//应用名称
				String appName = (String) map.get("key");
				applicationStatic.setApplicationName(appName);
				//应用总访问量
				Long totalsize = ResultUtil.longValue( map.get("doc_count"),0l);
				applicationStatic.setTotalSize(totalsize);
				//获取成功数和失败数
				List<Map<String, Object>> appstatic = (List<Map<String, Object>>)ResultUtil.getAggBuckets(map, "successsums");

				/**
				 "buckets": [
				 {
				 "key": 0,
				 "doc_count": 30
				 }
				 ]
				 */
				//key 0
				Long success = 0l;//成功数
				Long failed = 0l;//失败数
				for (int j = 0; j < appstatic.size(); j++) {
					Map<String, Object> stats = appstatic.get(j);
					Integer key = (Integer) stats.get("key");//成功和错误标识
					if (key == 0)//成功
						success = ResultUtil.longValue( stats.get("doc_count"),0l);
					else if (key == 1)//失败
						failed = ResultUtil.longValue( stats.get("doc_count"),0l);
				}
				applicationStatic.setSuccessCount(success);
				applicationStatic.setFailCount(failed);
				List<ApplicationPeriodStatic> applicationPeriodStatics = new ArrayList<ApplicationPeriodStatic>(4);
				ApplicationPeriodStatic applicationPeriodStatic = null;
				//获取响应时间分段统计信息
				Map<String, Map<String, Object>> appPeriodstatic = (Map<String, Map<String, Object>>)ResultUtil.getAggBuckets(map, "elapsed_ranges");
				//1秒
				Map<String, Object> period = appPeriodstatic.get("1秒");
				applicationPeriodStatic = new ApplicationPeriodStatic();
				applicationPeriodStatic.setPeriod("1秒");
				applicationPeriodStatic.setDocCount(ResultUtil.longValue(period.get("doc_count"),0l));
				applicationPeriodStatic.setTo(ResultUtil.intValue(period.get("to"),1000));
				applicationPeriodStatics.add(applicationPeriodStatic);

				//3秒
				period = appPeriodstatic.get("3秒");
				applicationPeriodStatic = new ApplicationPeriodStatic();
				applicationPeriodStatic.setPeriod("3秒");
				applicationPeriodStatic.setDocCount(ResultUtil.longValue(period.get("doc_count"),0l));
				applicationPeriodStatic.setFrom(ResultUtil.intValue(period.get("from"),1000));
				applicationPeriodStatic.setTo(ResultUtil.intValue(period.get("to"),3000));
				applicationPeriodStatics.add(applicationPeriodStatic);

				//5秒
				period = appPeriodstatic.get("5秒");
				applicationPeriodStatic = new ApplicationPeriodStatic();
				applicationPeriodStatic.setPeriod("5秒");
				applicationPeriodStatic.setDocCount(ResultUtil.longValue(period.get("doc_count"),0l));
				applicationPeriodStatic.setFrom(ResultUtil.intValue(period.get("from"),3000));
				applicationPeriodStatic.setTo(ResultUtil.intValue(period.get("to"),5000));
				applicationPeriodStatics.add(applicationPeriodStatic);

				//5秒以上
				period = appPeriodstatic.get("5秒以上");
				applicationPeriodStatic = new ApplicationPeriodStatic();
				applicationPeriodStatic.setPeriod("5秒以上");
				applicationPeriodStatic.setDocCount(ResultUtil.longValue(period.get("doc_count"),0l));
				applicationPeriodStatic.setFrom(ResultUtil.intValue(period.get("from"),5000));
				applicationPeriodStatics.add(applicationPeriodStatic);

				applicationStatic.setApplicationPeriodStatics(applicationPeriodStatics);
				applicationStatics.add(applicationStatic);

			}
			//返回统计结果
			return applicationStatics;
		}
		return null;
	}
}
```



###  **3.1.3 执行测试用例** 

Java代码 

```
@Test
	public void testAppStatic(){
		TraceExtraCriteria traceExtraCriteria = new TraceExtraCriteria();
		traceExtraCriteria.setStartTime(1516304868072l);
		traceExtraCriteria.setEndTime(1516349516377l);
		ClientInterface clientUtil = ElasticSearchHelper.getConfigRestClientUtil("esmapper/estrace/ESTracesMapper.xml");
		//通过下面的方法先得到查询的json报文，然后再通过MapRestResponse查询遍历结果，调试的时候打开String response的注释
		//String response = clientUtil.executeRequest("trace-*/_search","applicationSumStatic",traceExtraCriteria);
		//System.out.println(response);
		MapRestResponse restResponse = clientUtil.search("trace-*/_search","applicationSumStatic",traceExtraCriteria);

		List<Map<String,Object>> appstatics = restResponse.getAggBuckets("applicationsums",new ESTypeReference<List<Map<String,Object>>>(){});
		int doc_count_error_upper_bound = restResponse.getAggAttribute("applicationsums","doc_count_error_upper_bound",int.class);
		int sum_other_doc_count = restResponse.getAggAttribute("applicationsums","sum_other_doc_count",int.class);
		System.out.println("doc_count_error_upper_bound:"+doc_count_error_upper_bound);
		System.out.println("sum_other_doc_count:"+sum_other_doc_count);
		for(int i = 0; i < appstatics.size(); i ++){
			Map<String,Object> map = appstatics.get(i);
			//应用名称
			String appName = (String)map.get("key");
			//应用总访问量
			int totalsize =  (int)map.get("doc_count");
			//获取成功数和失败数
			List<Map<String,Object>> appstatic = ResultUtil.getAggBuckets(map ,"successsums",new ESTypeReference<List<Map<String,Object>>>(){});
			  doc_count_error_upper_bound = ResultUtil.getAggAttribute(map ,"successsums","doc_count_error_upper_bound",int.class);
			  sum_other_doc_count = ResultUtil.getAggAttribute(map ,"successsums","sum_other_doc_count",int.class);
			System.out.println("doc_count_error_upper_bound:"+doc_count_error_upper_bound);
			System.out.println("sum_other_doc_count:"+sum_other_doc_count);
			/**
			"buckets": [
			{
				"key": 0,
					"doc_count": 30
			}
                        ]
			 */
			//key 0
			int success = 0;//成功数
			int failed = 0;//失败数
			for(int j = 0; j < appstatic.size(); i ++){
				Map<String,Object> stats = appstatic.get(i);
				int key = (int) stats.get("key");//成功和错误标识
				if(key == 0)
                	success = (int)stats.get("doc_count");
				else if(key == 1)
					failed = (int)stats.get("doc_count");
			}

		}


	}
```



###   **3.1.4 获取元数据信息的测试方法** 

java代码 

```
@Test
	public void testAppStatic(){
		TraceExtraCriteria traceExtraCriteria = new TraceExtraCriteria();
		traceExtraCriteria.setStartTime(1516304868072l);
		traceExtraCriteria.setEndTime(1516349516377l);
		ClientInterface clientUtil = ElasticSearchHelper.getConfigRestClientUtil("esmapper/estrace/ESTracesMapper.xml");
		//通过下面的方法先得到查询的json报文，然后再通过MapRestResponse查询遍历结果，调试的时候打开String response的注释
		//String response = clientUtil.executeRequest("trace-*/_search","applicationSumStatic",traceExtraCriteria);
		//System.out.println(response);
		MapRestResponse restResponse = clientUtil.search("trace-*/_search","applicationSumStatic",traceExtraCriteria);

		List<Map<String,Object>> appstatics = restResponse.getAggBuckets("applicationsums",new ESTypeReference<List<Map<String,Object>>>(){});
		int doc_count_error_upper_bound = restResponse.getAggAttribute("applicationsums","doc_count_error_upper_bound",int.class);
		int sum_other_doc_count = restResponse.getAggAttribute("applicationsums","sum_other_doc_count",int.class);
		System.out.println("doc_count_error_upper_bound:"+doc_count_error_upper_bound);
		System.out.println("sum_other_doc_count:"+sum_other_doc_count);
		for(int i = 0; i < appstatics.size(); i ++){
			Map<String,Object> map = appstatics.get(i);
			//应用名称
			String appName = (String)map.get("key");
			//应用总访问量
			int totalsize =  (int)map.get("doc_count");
			//获取成功数和失败数
			List<Map<String,Object>> appstatic = ResultUtil.getAggBuckets(map ,"successsums",new ESTypeReference<List<Map<String,Object>>>(){});
			  doc_count_error_upper_bound = ResultUtil.getAggAttribute(map ,"successsums","doc_count_error_upper_bound",int.class);
			  sum_other_doc_count = ResultUtil.getAggAttribute(map ,"successsums","sum_other_doc_count",int.class);
			System.out.println("doc_count_error_upper_bound:"+doc_count_error_upper_bound);
			System.out.println("sum_other_doc_count:"+sum_other_doc_count);
			/**
			"buckets": [
			{
				"key": 0,
					"doc_count": 30
			}
                        ]
			 */
			//key 0
			int success = 0;//成功数
			int failed = 0;//失败数
			for(int j = 0; j < appstatic.size(); i ++){
				Map<String,Object> stats = appstatic.get(i);
				int key = (int) stats.get("key");//成功和错误标识
				if(key == 0)
                	success = (int)stats.get("doc_count");
				else if(key == 1)
					failed = (int)stats.get("doc_count");
			}

		}


	}
```



## 3.2 案例2 简单的term统计



### 3.2.1 定义dsl

建立dsl配置文件esmapper/testagg.xml，定义termAgg：

```
    <property name="termAgg">
        <![CDATA[
        {
            ## 设置查询条件
            #* 注释掉统计条件  *#
            "query": {
                "bool": {
                    "filter": [
                        {
                            "term": {
                                "applicationName.keyword": #[application]
                            }
                        },
                         {
                            "term": {
                                "rpc.keyword": #[rpc]
                            }
                        },
                        {"range": {
                                "startTime": {
                                    "gte": #[startTime],
                                    "lt": #[endTime]
                                }
                            }
                        }
                    ]
                }
            },

            "size":0,
            ## 聚合查询
            "aggs": {
                "traces": {
                      "terms": {
                        "field": "rpc.keyword",
                        "size":10000
                      }
                }
            }
        }
        ]]>
    </property>
```



### 3.2.2 执行dsl

```
	@Test
	public void termAgg(){
		ClientInterface clientInterface = ElasticSearchHelper.getConfigRestClientUtil("esmapper/testagg.xml");
		//ESDatas<Map> traces = clientInterface.searchAll("trace-*",1000,Map.class);//获取总记录集合
		Map params = new HashMap();//聚合统计条件参数
		params.put("application","testweb");
		SimpleDateFormat format = new SimpleDateFormat("yyyy-MM-dd HH:mm:dd");
		try {
			params.put("startTime",format.parse("1999-01-01 00:00:00").getTime());
			params.put("endTime",new Date().getTime());
			params.put("rpc","/testweb/jsp/logoutredirect.jsp");
		} catch (ParseException e) {
			e.printStackTrace();
		}
		//一行代码，执行每个服务的访问量总数统计
		ESAggDatas<LongAggHit> response = clientInterface.searchAgg("trace-*/_search",//从trace-开头的索引表中检索数据
																	"termAgg", //配置在esmapper/testagg.xml中的dsl语句
																	params,    //dsl语句termAgg中需要的查询参数
																	LongAggHit.class,  //封装聚合统计中每个服务地址及服务访问量的地址
																	"traces");  //term统计桶的名称，参见dsl语句
		List<LongAggHit> aggHitList = response.getAggDatas();//每个服务的访问量
		long totalSize = response.getTotalSize();//总访问量

	}
```



## 3.3 案例3 简单的cardinality统计



### 3.3.1 定义dsl

建立dsl配置文件esmapper/testagg.xml，定义candicateAgg：

```
    <property name="candicateAgg">
        <![CDATA[
        {
            ## 设置查询条件
            #* 注释掉统计条件
            "query": {
                "bool": {
                    "filter": [
                        {
                            "term": {
                                "applicationName.keyword": #[application]
                            }
                        },
                         {
                            "term": {
                                "rpc.keyword": #[rpc]
                            }
                        },
                        {"range": {
                                "startTime": {
                                    "gte": #[startTime],
                                    "lt": #[endTime]
                                }
                            }
                        }
                    ]
                }
            },
            *#
            "size":0,
            ## 聚合查询
            "aggs": {
                "traces": {
                      "cardinality" : {
                            "field" : "rpc.keyword",
                            "precision_threshold": 100
                        }
                }
            }
        }
        ]]>
    </property>
```



### 3.3.2 执行dsl

```
	@Test
	public void candicateAgg(){
		ClientInterface clientInterface = ElasticSearchHelper.getConfigRestClientUtil("esmapper/testagg.xml");
		Map params = null;//单值聚合统计条件参数
		//一行代码，执行服务基数统计
		ESAggDatas<SingleLongAggHit> response = clientInterface.searchAgg("trace-*/_search","candicateAgg",params,SingleLongAggHit.class,"traces");
		SingleLongAggHit aggHitList = response.getSingleAggData();
		long value = aggHitList.getValue();
		long totalSize = response.getTotalSize();//总访问量

	}
```



#  **4.开发交流** 



bboss elasticsearch交流：166471282

**bboss elasticsearch微信公众号：**

<img src="https://static.oschina.net/uploads/space/2017/0617/094201_QhWs_94045.jpg"  height="200" width="200">



# 支持我们

<div align="left"></div>
<img src="images/alipay.png"  height="200" width="200">


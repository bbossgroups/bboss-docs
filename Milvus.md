# 向量数据库Milvus客户端组件使用介绍

bboss提供一个简单的向量数据库Milvus操作组件，基于Milvus java客户端进行封装

```java
org.frameworkset.nosql.milvus.MilvusHelper
```

MilvusHelper提供单例静态方法,来获取Milvus实例，支持多Milvus数据源

```java
Milvus milvus = MilvusHelper.getMilvus(String name);//获取指定名称的Milvus数据源

//通过以下方法执行所有的Milvus操作，通过MilvusFunction接口封装处理逻辑
Object result = MilvusHelper.executeRequest(String name,  MilvusFunction milvusFunction)
```

MilvusFunction接口定义如下：

```java
public interface MilvusFunction<T> {
    public T execute(MilvusClientV2 milvusClientV2);

}
```

## 1.导入bboss Milvus组件

gradle

```java
compile 'com.bbossgroups:bboss-data:6.2.6' 
```

maven


```xml
<dependency>  
    <groupId>com.bbossgroups</groupId>  
    <artifactId>bboss-data</artifactId>  
    <version>6.2.6</version>  
</dependency>  
```

## 2.bboss Milvus组件使用

### 2.1 初始化Milvus数据源

通过以下代码初始化了一个名称为chan_fqa的Milvus数据源，后续的用该名称来引用对应的Milvus数据源来执行各种Milvus操作。

```java
        //1. 初始化milvus数据源chan_fqa，用来操作向量数据库，一个milvus数据源只需要定义一次即可，后续通过名称chan_fqa反复引用，多线程安全
        // 可以通过以下方法定义多个Milvus数据源，只要name不同即可，通过名称引用对应的数据源
        MilvusConfig milvusConfig = new MilvusConfig();
        milvusConfig.setName("chan_fqa");//数据源名称
        milvusConfig.setDbName("chan_fqa");//Milvus数据库名称
        milvusConfig.setUri("http://172.24.176.18:19530");//Milvus数据库地址
        milvusConfig.setToken("");//认证token：root:xxxx

        MilvusHelper.init(milvusConfig);//启动初始化Milvus数据源
```

### 2.2 使用数据源创建向量表

判断向量表是否存在，如果不存在则创建collectionName对应的向量表

```java
//如果向量表不存在，则创建向量表
MilvusHelper.executeRequest("chan_fqa", new MilvusFunction<Void>() {
    @Override
    public Void execute(MilvusClientV2 milvusClientV2) {
        if(!milvusClientV2.hasCollection(HasCollectionReq.builder()//判断向量表是否存在，如果不存在则创建向量表collectionName
                .collectionName(collectionName)
                .build())) {
            ;
            // create a collection with schema, when indexParams is specified, it will create index as well
            CreateCollectionReq.CollectionSchema collectionSchema = milvusClientV2.createSchema();
            collectionSchema.addField(AddFieldReq.builder().fieldName("log_id").dataType(DataType.Int64).isPrimaryKey(Boolean.TRUE).autoID(Boolean.FALSE).build());
            collectionSchema.addField(AddFieldReq.builder().fieldName("content").dataType(DataType.FloatVector).dimension(1024).build());
            collectionSchema.addField(AddFieldReq.builder().fieldName("collecttime").dataType(DataType.Int64).build());

            IndexParam indexParam = IndexParam.builder()
                    .fieldName("content")
                    .metricType(IndexParam.MetricType.COSINE)
                    .build();
            CreateCollectionReq createCollectionReq = CreateCollectionReq.builder()
                    .collectionName(collectionName)
                    .collectionSchema(collectionSchema)
                    .indexParams(Collections.singletonList(indexParam))
                    .build();
            milvusClientV2.createCollection(createCollectionReq);
        }
        return null;
    }
});
```

### 2.3 添加或者修改向量数据

在数据源chan_fqa上执行批量添加或者修改向量数据：

```java
Object result = MilvusHelper.executeRequest("chan_fqa", new MilvusFunction<Object>() {
    @Override
    public Object execute(MilvusClientV2 milvusClientV2) {
        // get the collection detail
       
         
        Map<String,Object> collectionSchemaIdx = getCollectionSchemaIdx(  milvusClientV2);
        
        if (milvusOutputConfig.isUpsert()) { //存在则修改，不存在则插入
            UpsertReq.UpsertReqBuilder<?, ?> upsertReqBuilder = UpsertReq.builder();
            upsertReqBuilder.collectionName(milvusOutputConfig.getCollectionName());
            if (SimpleStringUtil.isNotEmpty(milvusOutputConfig.getPartitionName())) {
                upsertReqBuilder.partitionName(milvusOutputConfig.getPartitionName());
            }
            upsertReqBuilder.data(_buildDatas(collectionSchemaIdx));
            UpsertResp upsertResp = milvusClientV2.upsert(upsertReqBuilder.build());
            return upsertResp;
        } else { //只做插入处理
            InsertReq.InsertReqBuilder<?, ?> insertReqBuilder = InsertReq.builder();
            insertReqBuilder.collectionName(milvusOutputConfig.getCollectionName());
            if (SimpleStringUtil.isNotEmpty(milvusOutputConfig.getPartitionName())) {
                insertReqBuilder.partitionName(milvusOutputConfig.getPartitionName());
            }
            insertReqBuilder.data(_buildDatas(collectionSchemaIdx));
            InsertResp insertResp = milvusClientV2.insert(insertReqBuilder.build());
            return insertResp;
        }
    }
});       
```

构建数据列表方法：

```java
 private List<JsonObject> _buildDatas(Map<String,Object> fidx){
        Gson gson = new Gson();
        if(SimpleStringUtil.isNotEmpty(records)){
            List<JsonObject> jsonObjects = new ArrayList<>(records.size());
            for(CommonRecord record:records) {

                Map<String, Object> datas = record.getDatas();
                Iterator<Map.Entry<String, Object>> iterator = datas.entrySet().iterator();
                JsonObject dict1 = new JsonObject();
                while (iterator.hasNext()){
                    Map.Entry<String, Object> entry = iterator.next();
                    String key  = entry.getKey();
                    if(!fidx.containsKey(key)){
                        continue;
                    }
                    Object value = entry.getValue();
                   
                    if(value == null){
                        dict1.add(key, JsonNull.INSTANCE);
                    }
                    else if(isArray(value)){

                        dict1.add(key, gson.toJsonTree(value));//向量float数组，可以调用向量模型服务进行文本向量化处理
                    }
                    else{
                        if(value instanceof String) {
                            dict1.addProperty(key,
                                    (String) value);
                        }
                        else if(value instanceof Number) {
                            dict1.addProperty(key,
                                    (Number) value);
                        }
                        else if(value instanceof Boolean) {
                            dict1.addProperty(key,
                                    (Boolean) value);
                        }
                        else if(value instanceof Character) {
                            dict1.addProperty(key,
                                    (Character) value);
                        }
                        else{
                            dict1.add(key, gson.toJsonTree(value));
                        }
                        
                    }
                   
                }
                jsonObjects.add(dict1);

            }
            return jsonObjects;
        }
        return null;
    }
```

### 2.4 获取向量表结构

```java
List<String> fields = MilvusHelper.loadCollectionSchema("chan_fqa",collectionName);
```

## 3.数据向量化

下面介绍两种模式来实现数据向量化处理：

调用的Langchain-Chatchat封装的xinference发布的模型服务

直接调用的xinference发布的模型服务来实现数据向量化

代码如下：

```java
String content = "admin(系统管理员) 登陆[公共开发平台]";
//初始化向量模型服务
Map properties = new HashMap();

//定义两个为的向量模型服务数据源：embedding_model_xinference,embedding_model_lanchat
properties.put("http.poolNames","embedding_model_xinference,embedding_model_lanchat");

properties.put("embedding_model_xinference.http.hosts","172.24.176.18:9997");//设置向量模型服务地址，这里调用的xinference发布的模型服务

properties.put("embedding_model_xinference.http.timeoutSocket","60000");
properties.put("embedding_model_xinference.http.timeoutConnection","40000");
properties.put("embedding_model_xinference.http.connectionRequestTimeout","70000");
properties.put("embedding_model_xinference.http.maxTotal","100");
properties.put("embedding_model_xinference.http.defaultMaxPerRoute","100");

properties.put("embedding_model_lanchat.http.hosts","127.0.0.1:7861");//设置向量模型服务地址，这里调用的xinference发布的模型服务

properties.put("embedding_model_lanchat.http.timeoutSocket","60000");
properties.put("embedding_model_lanchat.http.timeoutConnection","40000");
properties.put("embedding_model_lanchat.http.connectionRequestTimeout","70000");
properties.put("embedding_model_lanchat.http.maxTotal","100");
properties.put("embedding_model_lanchat.http.defaultMaxPerRoute","100");
HttpRequestProxy.startHttpPools(properties);

Map params = new HashMap();
        params.put("text",content);
        //调用的Langchain-Chatchat封装的xinference发布的模型服务，将LOG_CONTENT转换为向量数据---lanchat返回结果
        BaseResponse baseResponse = HttpRequestProxy.sendJsonBody("embedding_model_lanchat",params,"/py-api/knowledge_base/kb_embedding_textv1",BaseResponse.class);
   if(baseResponse.getCode() == 200){
            float[] embedding = baseResponse.getData();//获取向量数据
        }
//设置Xinference向量化模型参数
params = new HashMap();
        params.put("input",content);//需要向量化的文本
        params.put("model","custom-bge-large-zh-v1.5");//设置Xinference部署的向量模型名称
//调用的xinference发布的模型服务
XinferenceResponse result = HttpRequestProxy.sendJsonBody("embedding_model_xinference",params,"/v1/embeddings",XinferenceResponse.class);//-------与lanchat返回结果一致
        float[] embedding = result.getData().get(0).getEmbedding();
        
```

## 4.向量检索

采用余弦相似度实现向量检索功能：

```java
        /**
         * 参考文档：https://milvus.io/api-reference/java/v2.4.x/v2/Vector/search.md
         */
        //1. 初始化milvus数据源chan_fqa，用来操作向量数据库，一个milvus数据源只需要定义一次即可，后续通过名称chan_fqa反复引用，多线程安全
        // 可以通过以下方法定义多个Milvus数据源，只要name不同即可，通过名称引用对应的数据源
        MilvusConfig milvusConfig = new MilvusConfig();
        milvusConfig.setName("chan_fqa");//数据源名称
        milvusConfig.setDbName("ucr_chan_fqa");//Milvus数据库名称
        milvusConfig.setUri("http://172.24.176.18:19530");//Milvus数据库地址
        milvusConfig.setToken("");//认证token：root:xxxx

        MilvusHelper.init(milvusConfig);//启动初始化Milvus数据源
        
       
        
        //2. 初始化Xinference向量模型服务embedding_model，一个服务组只需要定义一次即可，后续通过名称embedding_model反复引用，多线程安全
        // 可以通过以下方法定义多个服务，只要name不同即可，通过名称引用对应的服务
         
        Map properties = new HashMap();

        //定义Xinference数据向量化模型服务，embedding_model为的向量模型服务数据源名称
        properties.put("http.poolNames","embedding_model");

        properties.put("embedding_model.http.hosts","172.24.176.18:9997");//设置向量模型服务地址，这里调用的xinference发布的模型服务

        properties.put("embedding_model.http.timeoutSocket","60000");
        properties.put("embedding_model.http.timeoutConnection","40000");
        properties.put("embedding_model.http.connectionRequestTimeout","70000");
        properties.put("embedding_model.http.maxTotal","100");
        properties.put("embedding_model.http.defaultMaxPerRoute","100");
        //启动Xinference向量模型服务
        HttpRequestProxy.startHttpPools(properties);
        
        String collectionName = "demo";//向量表名称
        //3. 在向量数据源chan_fqa的向量表demo上执行向量检索
        List<List<SearchResp.SearchResult>> searchResults = MilvusHelper.executeRequest("chan_fqa", milvusClientV2 -> {
            Map eparams = new HashMap();
            eparams.put("input","新增了机构");//content向量字段查询条件转换为向量
            eparams.put("model","custom-bge-large-zh-v1.5");//指定Xinference向量模型名称

            //调用的 xinference 发布的向量模型模型服务，将查询条件转换为向量
            XinferenceResponse result = HttpRequestProxy.sendJsonBody("embedding_model",eparams,"/v1/embeddings",XinferenceResponse.class);
            if(result != null){
                List<Data> data = result.getData();
                if(data != null && data.size() > 0 ) {
                    //获取条件转换的向量数据
                    float[] embedding = data.get(0).getEmbedding();

                    //构建检索参数
                    Map searchParams = new LinkedHashMap();
                    searchParams.put("metric_type","COSINE");//采用余弦相似度算法
                    searchParams.put("radius",0.85);//返回content与查询条件相似度为0.85以上的记录
                    String[] array = {"log_id","collecttime","log_content"};//定义要返回的字段清单
                    SearchResp searchR = milvusClientV2.search(SearchReq.builder()
                            .collectionName(collectionName)
                            .data(Collections.singletonList(new FloatVec(embedding)))
                             .annsField("content")//指定向量字段
                            .filter("log_id < 100000")//指定过滤条件，可以进行条件组合，具体参考文档：https://milvus.io/api-reference/java/v2.4.x/v2/Vector/search.md
                             .searchParams(searchParams)
                            .topK(10)
                            .outputFields(Arrays.asList(array))
                            .build());
                    return searchR.getSearchResults();//返回检索结果
                    
                }
            }
            
            return null;
        });
        //打印结果
        System.out.println("\nSearch results:");
        if(searchResults != null) {
            for (List<SearchResp.SearchResult> results : searchResults) {
                for (SearchResp.SearchResult searchResult : results) {
                    System.out.printf("ID: %d, Score: %f, %s\n", (long) searchResult.getId(), searchResult.getScore(), searchResult.getEntity().toString());
                }
            }
        }
```

代码运行结果：

```json
Search results:
ID: 10015, Score: 0.893054, {log_id=10015, collecttime=1730728149266, log_content=系统管理员 新增了机构 aaa}
ID: 10013, Score: 0.893054, {log_id=10013, collecttime=1730728148165, log_content=系统管理员 新增了机构 aaa}
ID: 10018, Score: 0.893054, {log_id=10018, collecttime=1730728155011, log_content=系统管理员 新增了机构 aaa}
ID: 10017, Score: 0.893054, {log_id=10017, collecttime=1730728153214, log_content=系统管理员 新增了机构 aaa}
ID: 10016, Score: 0.893054, {log_id=10016, collecttime=1730728151482, log_content=系统管理员 新增了机构 aaa}
ID: 5, Score: 0.893054, {log_id=5, collecttime=1730728153539, log_content=系统管理员 新增了机构 aaa}
ID: 2206, Score: 0.893054, {log_id=2206, collecttime=1730728312139, log_content=系统管理员 新增了机构 aaa}
ID: 1115, Score: 0.893054, {log_id=1115, collecttime=1730728163302, log_content=系统管理员 新增了机构 aaa}
ID: 66, Score: 0.871805, {log_id=66, collecttime=1730728257720, log_content=系统管理员新增子机构3eqr}
```

完整源码地址：

https://gitee.com/bboss/bboss-datatran-demo/blob/main/src/main/java/org/frameworkset/datatran/imp/milvus/MilvusTest.java

## 5.参考资料

Milvus java客户端官方文档

https://milvus.io/api-reference/java/v2.4.x/v2/Client/MilvusClientV2Pool.md

https://milvus.io/api-reference/java/v2.4.x/v2/Vector/search.md

bboss datatran [Milvus输出插件使用文档](https://esdoc.bbossgroups.com/#/datatran-plugins?id=_212-milvus%e5%90%91%e9%87%8f%e6%95%b0%e6%8d%ae%e5%ba%93%e8%be%93%e5%87%ba%e6%8f%92%e4%bb%b6)
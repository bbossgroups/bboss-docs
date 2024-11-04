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

通过以下代码初始化了一个名称为ucr_chan_fqa的Milvus数据源，后续的用该名称来引用对应的Milvus数据源来执行各种Milvus操作。

```java
  //初始化milvus数据源服务，用来操作向量数据库
                        MilvusConfig milvusConfig = new MilvusConfig();
                        milvusConfig.setName("ucr_chan_fqa");//数据源名称
                        milvusConfig.setDbName("ucr_chan_fqa");//Milvus数据库名称
                        milvusConfig.setUri("http://172.24.176.18:19530");//Milvus数据库地址
                        milvusConfig.setToken("");//认证token：root:xxxx
                        ResourceStartResult resourceStartResult =  MilvusHelper.init(milvusConfig);//加载配置初始化Milvus数据源
```

### 2.2 使用数据源创建向量表

判断向量表是否存在，如果不存在则创建collectionName对应的向量表

```java
//如果向量表不存在，则创建向量表
MilvusHelper.executeRequest("ucr_chan_fqa", new MilvusFunction<Void>() {
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

在数据源ucr_chan_fqa上执行批量添加或者修改向量数据：

```java
Object result = MilvusHelper.executeRequest("ucr_chan_fqa", new MilvusFunction<Object>() {
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
List<String> fields = MilvusHelper.loadCollectionSchema("ucr_chan_fqa",collectionName);
```

## 3.参考资料

Milvus java客户端官方文档

https://milvus.io/api-reference/java/v2.4.x/v2/Client/MilvusClientV2Pool.md

bboss datatran [Milvus输出插件使用文档](https://esdoc.bbossgroups.com/#/datatran-plugins?id=_212-milvus%e5%90%91%e9%87%8f%e6%95%b0%e6%8d%ae%e5%ba%93%e8%be%93%e5%87%ba%e6%8f%92%e4%bb%b6)
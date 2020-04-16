### 使用bboss persistent框架实现数据库的插入操作

4.3.1预编译插入

​        PreparedDBUtil preDBUtil = new PreparedDBUtil(); ----------------->定义预编组件实例

​       Action action = (Action)vo;

​       String sRecord = action.getRecord();

​       Timestamp   tsOperTime = (Timestamp)action.getOperTime();

​       int    iTypeId = action.getActionTypeID();

​       int    iDocInfoId = action.getDocInfoId();

​       int    iStatus = action.getStatus();

​       int    iActionId = 0;

​       String activityID = action.getActivityID();

​       String userId = action.getActorID();

​       Id = “0”;

​       String sqlstr = "INSERT INTO OFFICE_DOCACTION (id,RECORD, OPERTIME, TYPE_ID, ACTDOCINFO_ID, STATUS,ACTIVITYID, ACTIONUSER_ID) " +"VALUES(?,?,?,?,?,?,?,?)";

​       try {

​           preDBUtil.preparedInsert(sqlstr); ----------------->预编sql语句

​           preDBUtil.setString(1,Id);preDBUtil.setString(2,sRecord);

​          

​           preDBUtil.setTimestamp(3,tsOperTime);

​           preDBUtil.setInt(4,iTypeId);

​           preDBUtil.setInt(5,iDocInfoId);

​           preDBUtil.setInt(6,iStatus);

​           preDBUtil.setString(7,activityID);

​           preDBUtil.setString(8,userId);

​          } catch (SQLException e) {

​           e.printStackTrace();

​           throw new DataAccessException("",e);

​       }

​       。。。。。

4.3.1普通插入

DBUtil dbUtil = new DBUtil();

String insert = “insert into test (id,name) values(0,’test’)”

dbUtil.executeInsert(insert);//在默认的数据库上执行，在指定的数据库上面使用以下方法：

dbUtil.executeInsert(dbName,insert);

4.3.2 blob字段的插入

void prepareDefaultInsertBlob()

​       {

​           for(int i = 0; i < 1; i ++)

​           {

​              PreparedDBUtil p = new PreparedDBUtil();            

​              try {

​              //content字段类型为blob字段

​                  p.preparedInsert("insert into test(name,content) values(?,?)");

​                  p.setPrimaryKey(1,"biaoping.yin1","name");//设定唯一标识字段

​                  p.setBlob(2,new File("D:/workspace/shark-1.1-2.src.zip"),"content");//将文件插入blob字段

//                p.setBlob(2,"asdfasdf".getBytes(),"content");//将二进制流插入blob字段 

//                p.getString(1,"content");

​                  p.executePrepared();

​              } catch (SQLException e) {

​                  // TODO Auto-generated catch block

​                  e.printStackTrace();

​              } catch (Exception e) {

​                  // TODO Auto-generated catch block

​                  e.printStackTrace();

​              }

​           }

​       }

4.3.3 clob字段的插入

void prepareDefaultInsertClob()

​       {

​           for(int i = 0; i < 1; i ++)

​           {

​              PreparedDBUtil p = new PreparedDBUtil();

​              try {

​              //content字段类型为clob

​                  p.preparedInsert("insert into test(name,content) values(?,?)");

​                  p.setPrimaryKey(1,"biaoping.yin1","name");//设定唯一标识字段

​                  p.setClob(2,"阿斯顿发生的飞","content");

​                  p.executePrepared();

​              } catch (SQLException e) {

​                  // TODO Auto-generated catch block

​                  e.printStackTrace();

​              } catch (Exception e) {

​                  // TODO Auto-generated catch block

​                  e.printStackTrace();

​              }

​           }

​       }

4.5.3 预编译插入和普通插入操作的不同点

预编译插入和普通插入都是在表中添加一条记录，普通插入操作编码比较方便，程序可读性强一些，但是存在sql注入的风险，预编译插入能够有效地防止sql注入，执行的效率比普通插入的效率要高一些，但是编写的代码不是很直观，考虑到系统的安全和性能应该优先考虑预编译插入操作
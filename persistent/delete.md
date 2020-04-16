### bboss persistent 实现数据库删除操作

4.6 删除操作

4.6.1    普通删除操作

​    DBUtil dbUtil = new DBUtil();

​       try {

​           dbUtil.executeDelete(sqlstr);

//dbUtil.executeDelete(‘bspf’,sqlstr);

​       } catch (SQLException e) {

​           e.printStackTrace();

​    throw new DataAccessException(e.getMessage(),e);

}

4.6.2    预编译删除操作

​        PreparedDBUtil preDBUtil = new PreparedDBUtil();

​       int pk = 0;

​       String sqlstr = "delete from OFFICE_DOCINFO where DOCID=?”

​       String sDocId = “1”;

​           try {

​              preDBUtil.preparedDelete(sqlstr); //在缺省的数据库上操作

​                 //preDBUtil .preparedDelete (“bspf”,sqlstr);在指定的数据库中，执行sql语句。

​              preDBUtil.setString(1,sDocId);

​              Object obj = preDBUtil.executePrepared();

​                         } catch (SQLException e) {

​              e.printStackTrace();

​              throw new DataAccessException("向OFFICE_DOCINFO删除记录错:"+e.getMessage(),e);

​           }

4.6.3    预编译删除操作和普通删除操作的不同点

jdbc规范中提供了两种不同的删除操作：预编译和普通两种。

和插入操作一样，预编译删除的效率比普通删除的效率要高一些，预编译删除能够有效地防止sql注入问题，编码和调试没有普通删除那么方便，综合各种因素，推荐使用预编译删除操作。
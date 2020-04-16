### bboss persistent 实现数据库更新操作

4.5 更新操作代码段

4.5.1预编译更新操作

​        PreparedDBUtil preDBUtil = new PreparedDBUtil();

​       int pk = 0;

​       String sqlstr = "update OFFICE_DOCINFO set DOCID=?, GETDOCID=?, " +

​       "DOCDATE=?, RECIVEDATE=?, DOCTITLE=?, DOCUNIT=?, DOCURGENT=?, DOCKIND=?, " +

​       "DOCCLASS=?, SECRET=?, DOCSUBJECT=?, DOCTYPE=?, DOCNUM=?, DOCOFFICE=?, " +

​       "DOCINFO=?, DOCRESULT=?, DOCTRANSACT=?, DOCREMARK=?, DOCMODE=?, WORKLEVEL=?, " +

​       "RESERVETERM=?, IDENTITY=?, DOCSUM=?, OLD_NUMBER=?, DOCKIND_ID=?, PROCESSID=?, " +

​       "DOCTYPE_ID=?, CLASSID=?,isSend=? where DOCINFO_ID=?";

​       DocInfo docInfo = (DocInfo)vo;

​       String sDocId = docInfo.getDocId();

​       String sGetDocId = docInfo.getGetDocID();

​       Timestamp tsDocDate = (Timestamp) docInfo.getDocDate();

​       Timestamp tsReceiveDate = (Timestamp) docInfo.getReciveDate();

​       String sDocTitle = docInfo.getDocTitle();

​       String sDocUnit = docInfo.getDocUnit();

​       int    iDocUrgent = docInfo.getDocUrgent();

​       String sDocClass = docInfo.getDocClass();

​       String sDocKind = docInfo.getDocKind();

​       String sSecret = docInfo.getSecret();

​       String sDocSubject = docInfo.getDocSubject();

​       String sDocType = docInfo.getDocType();

​       String sDocNum = docInfo.getDocNum();

​       String sDocOffice = docInfo.getDocOffice();

​       String sDocInfo = docInfo.getDocInfo();

​       String sDocResult = docInfo.getDocResult();

​       String sDocTransact = docInfo.getDocTransact();

​       String sDocRemark = docInfo.getDocRemark();

​       int    iDocMode = docInfo.getDocMode();

​       String sWorkLevel = docInfo.getWorkLevel();

​       String sReserveTerm = docInfo.getReserveTerm();

​       int    iIdentity = docInfo.getIdentity();

​       String   iDocSum = docInfo.getDocSum();

​       String sOld_number = docInfo.getOld_number();

​       int    iDocKindId = docInfo.getDocKindId();

​       //int     iProcessId = docInfo.getProcessId();

​       String sProcessId = docInfo.getProcessId();

​       int    iDocTypeId = docInfo.getDocTypeId();

​       int    iDocClassId = docInfo.getDocClassId();

​       int isSend = docInfo.getIsSend();

​        try {

​              preDBUtil.preparedUpdate(sqlstr); //在缺省的数据库上操作

​                 //preDBUtil.preparedUpdate(“bspf”,sqlstr);在指定的数据库中，执行sql语句。

​              preDBUtil.setString(1,sDocId);

​              preDBUtil.setString(2,sGetDocId);

​              preDBUtil.setTimestamp(3,tsDocDate);

​              preDBUtil.setTimestamp(4,tsReceiveDate);

​              preDBUtil.setString(5,sDocTitle);

​              preDBUtil.setString(6,sDocUnit);

​              preDBUtil.setInt(7,iDocUrgent);

​              preDBUtil.setString(8,sDocClass);

​              preDBUtil.setString(9,sDocKind);

​              preDBUtil.setString(10,sSecret);

​              preDBUtil.setString(11,sDocSubject);

​              preDBUtil.setString(12,sDocType);

​              preDBUtil.setString(13,sDocNum);

​              preDBUtil.setString(14,sDocOffice);

​              preDBUtil.setString(15,sDocInfo);

​              preDBUtil.setString(16,sDocResult);

​              preDBUtil.setString(17,sDocTransact);

​              preDBUtil.setString(18,sDocRemark);

​              preDBUtil.setInt(19,iDocMode);

​              preDBUtil.setString(20,sWorkLevel);

​              preDBUtil.setString(21,sReserveTerm);

​              preDBUtil.setInt(22,iIdentity);

​              preDBUtil.setString(23,iDocSum);

​              preDBUtil.setString(24,sOld_number);

​              preDBUtil.setInt(25,iDocKindId);

​              preDBUtil.setString(26,sProcessId);

​              preDBUtil.setInt(27,iDocTypeId);

​              preDBUtil.setInt(28,iDocClassId);

​              preDBUtil.setInt(29,isSend);

​              preDBUtil.setInt(30,docInfo.getDocInfoId());

​              Object obj = preDBUtil.executePrepared();

​              return docInfo.getDocInfoId();

​           } catch (SQLException e) {

​              e.printStackTrace();

​              throw new DataAccessException("向OFFICE_DOCINFO表插入记录错:"+e.getMessage(),e);

​           }

4.5.2一般更新操作 

​        String sqlstr = "UPDATE OFFICE_DOCINFO SET DOCMODE = " + newStatus +" WHERE DOCINFO_ID = "+docInfoId;

​       DBUtil dbUtil = new DBUtil();

​       try {

​           dbUtil.executeUpdate(sqlstr);

// dbUtil.executeUpdate(“bspf”,sqlstr);;在指定的数据库中，执行sql语句。

 

​       } catch (SQLException e) {

​           e.printStackTrace();

​           throw new DataAccessException("更新公文状态错:"+e.getMessage(),e);

​       }

4.5.3 CLOB字段的更新操作

PreparedDBUtil db = new PreparedDBUtil();

​       StringBuffer sql = new StringBuffer();

​       sql.append("update TD_CMS_DOCUMENT set TITLE=?,SUBTITLE=?,")     

​           .append("AUTHOR=?,CONTENT=?,KEYWORDS=?,")      //dockind

​           .append("DOCABSTRACT=?,")

​           .append("TITLECOLOR=?,")

​           .append("DOCSOURCE_ID=?,")

​           .append("DETAILTEMPLATE_ID=?,")

   .append("LINKTARGET=?,Doc_level=?,PARENT_DETAIL_TPL=?,PIC_PATH=?,mediapath=?,publishfilename=?,")

​           .append("docwtime=" + DBUtil.getDBDate(doc.getDocwtime()))

​           .append(",secondtitle=?,isnew=?,newpic_path=?,ordertime=? where document_id=?") ;

​       try

​       {

​           db.preparedUpdate(sql.toString());

​           //db.preparedUpdate(“bspf”,sql.toString());

​           db.setString     (1,doc.getTitle());

​           db.setString     (2,doc.getSubtitle());

​           db.setString     (3,doc.getAuthor());

​           db.setClob       (4,doc.getContent(),"content");

​           db.setString     (5,doc.getKeywords());

​           db.setString     (6,doc.getDocabstract()==null?"无":doc.getDocabstract());

​           db.setString     (7,doc.getTitlecolor());

​           db.setInt        (8,doc.getDocsource_id());

​           db.setInt        (9,doc.getDetailtemplate_id());

​           db.setString     (10,doc.getLinktarget());

​           db.setInt        (11,doc.getDoc_level());

​           db.setString     (12,doc.getParentDetailTpl());

​           db.setString     (13,doc.getPicPath());

​           db.setString     (14,doc.getMediapath());

​           db.setString     (15,doc.getPublishfilename());

​           db.setString     (16,doc.getSecondtitle());

​           db.setInt        (17,doc.getIsNew());

​           db.setString     (18,doc.getNewPicPath());

​           db.setTimestamp  (19,new Timestamp(doc.getOrdertime().getTime()));

​           db.setPrimaryKey (20,doc.getDocument_id());

​           db.executePrepared();

​           b = true;

​       }

​       catch (SQLException e)

​       {

​           e.printStackTrace();

​           throw new DocumentManagerException(e.getMessage());

​       }

​       return b;

4.5.3 BLOB字段的更新操作

PreparedDBUtil db = new PreparedDBUtil();

​       StringBuffer sql = new StringBuffer();

​       sql.append("update TD_CMS_DOCUMENT set TITLE=?,SUBTITLE=?,")     

​           .append("AUTHOR=?,CONTENT=?,KEYWORDS=?,")      //dockind

​           .append("DOCABSTRACT=?,")

​           .append("TITLECOLOR=?,")

​           .append("DOCSOURCE_ID=?,")

​           .append("DETAILTEMPLATE_ID=?,")

   .append("LINKTARGET=?,Doc_level=?,PARENT_DETAIL_TPL=?,PIC_PATH=?,mediapath=?,publishfilename=?,")

​           .append("docwtime=" + DBUtil.getDBDate(doc.getDocwtime()))

​           .append(",secondtitle=?,isnew=?,newpic_path=?,ordertime=? where document_id=?") ;

​       try

​       {

​           db.preparedUpdate(sql.toString());

​           //db.preparedUpdate(“bspf”,sql.toString());

​           db.setString     (1,doc.getTitle());

​           db.setString     (2,doc.getSubtitle());

​           db.setString     (3,doc.getAuthor());

​           db.setBlob       (4,doc.getContent(),"content");

​           db.setString     (5,doc.getKeywords());

​           db.setString     (6,doc.getDocabstract()==null?"无":doc.getDocabstract());

​           db.setString     (7,doc.getTitlecolor());

​           db.setInt        (8,doc.getDocsource_id());

​           db.setInt        (9,doc.getDetailtemplate_id());

​           db.setString     (10,doc.getLinktarget());

​           db.setInt        (11,doc.getDoc_level());

​           db.setString     (12,doc.getParentDetailTpl());

​           db.setString     (13,doc.getPicPath());

​           db.setString     (14,doc.getMediapath());

​           db.setString     (15,doc.getPublishfilename());

​           db.setString     (16,doc.getSecondtitle());

​           db.setInt        (17,doc.getIsNew());

​           db.setString     (18,doc.getNewPicPath());

​           db.setTimestamp  (19,new Timestamp(doc.getOrdertime().getTime()));

​           db.setPrimaryKey (20,doc.getDocument_id());

​           db.executePrepared();

​           b = true;

​       }

​       catch (SQLException e)

​       {

​           e.printStackTrace();

​           throw new DocumentManagerException(e.getMessage());

​       }

 4.5.4 预编译更新操作和普通更新操作的不同点

jdbc规范提供了两种不同的更新操作模式：预编译和普通两种，二者的区别为：

预编译更新的操作效率高，有效防止sql注入问题，编码和调试没有普通更新操作方便，综合各种因素推荐使用预编译更新操作。
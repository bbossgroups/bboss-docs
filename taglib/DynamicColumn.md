### bboss标签实现列表中的动态列数据展示方法

借助bboss标签库提供的list标签，我们可以非常方便地实现列表中的动态列数据的展示。

假设现在list中存放的是map类型的记录，map中有部分key/value是确定的，有部分key是不固定的，同时会把这不固定的列的key放在另一个list里，这样在遍历第一个list中的map数据时，直接输出固定的key/value数据后，再通过结合存放动态key的list来循环输出这些动态的key/value数据。

bboss在cell标签中提供了usecurrentCellValuetoCellName和currentcelltoColName两个属性来支撑这个功能特性。

**usecurrentCellValuetoCellName**为cell标签特有属性,功能说明：与index标签结合使用，usecurrentCellValuetoCellName对应当前list的当前记录的属性名称，将这个属性对应的值作为index索引对应的外围容器对象记录的属性名称，cell标签输出这个名称对应的外围记录的属性值

**currentcelltoColName**为cell标签特有属性，为boolean类型，默认为false，为true时标识生效，功能说明：与index标签结合使用，currentcelltoColName标识将当前list的当前记录对应的对象作为index索引对应的外围容器对象记录的属性名称，cell标签输出这个名称对应的外围记录的属性值。

currentcelltoColName属性适用于List String场景，也就是说将key直接存储在List中。

usecurrentCellValuetoCellName适用于List PO类型，也就是说key作为PO对象的一个属性，然后这个PO对象存放在List中。

下面的代码演示了两个属性的使用方法：

Html代码  

```html
<%  
List rooms = new ArrayList();  
for (int i =0 ; i < 10; i ++) {  
Map map=new HashMap();  
map.put("rommType", "麻将室"+i);  
map.put("rommTypeID", "mj"+i);  
map.put("overNum", (100+i)+"");  
for (int j = 0; j < 10 ; j ++) {//设置动态属性  
  
    map.put("day "+j+"剩余房间数:", (10+j)+"");//map增加时间段内的数据  
}  
rooms.add(map);  
}  
List<String> days=new ArrayList<String>();  
for (int j = 0; j < 10 ; j ++) {  
    days.add("day "+j+"剩余房间数:");  
}  
  
  
List<RoomDay> roomDays=new ArrayList<RoomDay>();  
for (int j = 0; j < 10 ; j ++) {  
    RoomDay roomDay = new RoomDay();  
    roomDay.setDay("day "+j+"剩余房间数:");  
    roomDays.add(roomDay);  
}  
request.setAttribute("rooms", rooms);  
request.setAttribute("days", days);  
request.setAttribute("roomDays", roomDays);  
%>  
<div>  
<p>currentcelltoColName属性演示</p>  
<div>  
<pg:list requestKey="rooms">  
 <p> <pg:cell colName="rommType"/></p>  
  <p><pg:cell colName="rommTypeID"/></p>  
  <p><pg:cell colName="overNum"/></p>  
  <p><pg:list requestKey="days">  
        <p><pg:cell/><pg:cell index="0" currentcelltoColName="true"/></p>  
  </pg:list></p>  
</pg:list>  
</div>  
</div>  
  
<div>  
<p>usecurrentCellValuetoCellName属性演示</p>  
<div>  
<pg:list requestKey="rooms">  
  <p><pg:cell colName="rommType"/></p>  
  <p><pg:cell colName="rommTypeID"/></p>  
  <p><pg:cell colName="overNum"/></p>  
  <p><pg:list requestKey="roomDays">  
        <p><pg:cell colName="day"/><pg:cell index="0" usecurrentCellValuetoCellName="day"/></p>  
  </pg:list></p>  
</pg:list>  
</div>  
</div>  
```

参考文档：

[bboss中的map标签结合list标签/cell标签展示复杂数据结构案例](http://yin-bp.iteye.com/blog/1668729)
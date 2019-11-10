1.添加字段

oracle：alter table tra_report_header add address varchar(100) null;

对应changelog.xml的数据集如下：

<changeSet id="2019012420040001" author="meng.li">

<addColumn tableName="tra_report_header">

<column name="address" type="varchar(100)" />

</addColumn>

</changeSet>

2.给列设置默认值

oracle：alter table tra_report_header modify address default "上海";

对应changelog.xml的数据集如下：

<changeSet id="2019012420040002" author="meng.li">

<addDefaultValue columnDataType="varchar"

columnName="address"

defaultValue="上海"

tableName=“tra_report_header”/>

</changeSet>

3.增加非空约束

oracle：alter table tra_report_header modify address not null;

对应changelog.xml的数据集如下：

<changeSet id="2019012420040003" author="meng.li">

<addNotNullConstraint columnDataType="varchar"

columnName="address"

tableName="tra_report_header"/>

</changeSet>

4.增加主键

oracle：alter table tra_report_header add primary key id;

对应changelog.xml的数据集如下：

<changeSet id="2019012420040004" author="meng.li">

<addPrimaryKey columnNames="id"

constraintName="pk_tra_report_header"

tableName="tra_report_header"/>

</changeSet>

5.删除主键

oracle：alter table tra_report_header drop constraint pk_tra_report_header cascade;

对应changelog.xml的数据集如下：

<changeSet id="2019012420040005" author="meng.li">

<dropPrimaryKey constraintName="pk_tra_report_header"

tableName="tra_report_header"/>

</changeSet>

6.创建索引

oracle：create unique index tra_report_header_u1 on tra_report_header(name);

对应changelog.xml的数据集如下：

<changeSet id="2019012420040006" author="meng.li">

<createIndex indexName="tra_report_header_u1"

tableName="tra_report_header"

unique=“true”>

<column name="name" type="varchar(100)"/>

</createIndex>

</changeSet>

7.创建sequence

oracle：create sequence tra_report_header_s start with 1 increment by 1;

对应changelog.xml的数据集如下：

<changeSet id="2019012420040007" author="meng.li">

<createSequence startValue="1"

sequenceName="tra_report_header_s"

incrementBy="1"/>

</changeSet>

8.修改sequence

oracle：alter sequence tra_report_header_s increment by 1111;

对应changelog.xml的数据集如下：

<changeSet id="2019012420040008" author="meng.li">

<alterSequence incrementBy="1"

equenceName="tra_report_header_s"/>

</changeSet>

9.创建表

oracle: create table tra_report_header(name varchar2(100));

comment on table tra_report_header is "报销单头标";

comment on column tra_report_header.name is "姓名";

对应changelog.xml的数据集如下：

<changeSet id="2019012420040009" author="meng.li">

<createTable tableName="tra_report_header">

<column name="name" type="varchar2(100)" remark="姓名"/>

</createTable>

</changeSet>

10.创建视图

oracle：create or replace view tra_report_header_v as

select * from tra_report_header t where id > 10;

对应changelog.xml的数据集如下：

<changeSet id="2019012420040010" author="meng.li">

<createView replaceIfExists="true" viewName="tra_report_header_v">

select * from tra_report_header t where t.id > 10

</createView>

</changeSet>

11.删除列

oracle：alter table tra_report_header drop column attribute;

对应changelog.xml的数据集如下：

<changeSet id="2019012420040011" author="meng.li">

<dropColumn column="attribute" tableName="tra_report_header"/>

</changeSet>

12.删除索引

oracle：drop index tra_report_header_n1;

对应changelog.xml的数据集如下：

<changeSet id="2019012420040013" author="meng.li">

<dropIndex indexName="tra_report_header_n1" tableName="tra_report_header"/>

</changeSet>

13.删除非空约束

oracle:alter table tra_report_header modify name null;

对应changelog.xml的数据集如下：

<changeSet id="2019012420040013" author="meng.li">

<dropNotNullConstraint columnDataType="varchar(100)"

columnName="name"

tableName="tra_report_header"/>

</changeSet>

14.删除sequence

oracle:drop sequence tra_report_header_s;

对应changelog.xml的数据集如下：

<changeSet id="2019012420040014" author="meng.li">

<dropSequence sequenceName="tra_report_header_s"/>

</changeSet>

15.删除表

oracle:drop table tra_report_header;

对应changelog.xml的数据集如下：

<changeSet id="2019012420040015" author="meng.li">

<dropTable tableName="tra_report_header" cascadeConstraints="true"/>

</changeSet>

16.删除唯一索引

对应changelog.xml的数据集如下：

<changeSet id="2019012420040016" author="meng.li">

<dropUniqueContraint constraintsName="tra_report_header_u1"

tableName="tra_report_header"

uniqueColumns="id"/>

</changeSet>

17.删除视图

oracle:drop view tra_report_header_v;

对应changelog.xml的数据集如下：

<changeSet id="2019012420040017" author="meng.li">

<dropView viewName="tra_report_header" />

</changeSet>

18.插入数据

对应changelog.xml的数据集如下：

<changeSet id="2019012420040018" author="meng.li">

<insert tableName="tra_report_header">

<column name="id" type="number" value="1"/>

</insert>

</changeSet>

19.修改字段类型

oracle:alter table tra_report_header modify id int;

对应changelog.xml的数据集如下：

<changeSet id="2019012420040019" author="meng.li">

<modifyDataType columnName="id"

newDataType="int"

tableName="tra_report_header"/>

</changeSet>

20.修改列名

alter table tra_report_header rename id to id1;

对应changelog.xml的数据集如下：

<changeSet id="2019012420040020" author="meng.li">

<renameColumn columnDataType="number"

newColumnName="id1"

oldColumnName="id"

tableName="tra_report_header"/>

</changeSet>

21.rename表

oracle:alter table tra_report_header rename tra_report_header1;

对应changelog.xml的数据集如下：

<changeSet id="2019012420040021" author="meng.li">

<renameTable newTableName="tra_report_header1"

oldTableName="tra_report_header"/>

</changeSet>

22.rename视图

对应changelog.xml的数据集如下：

<changeSet id="2019012420040022" author="meng.li">

<renameView newViewName="tra_report_header_v1"

oldViewName=“tra_report_header_v”/>

</changeSet>

23.update数据

对应changelog.xml的数据集如下：

<changeSet id="2019012420040023" author="meng.li">

<update tableName="tra_report_header">

<column name="name" type="varchar(100)" value="ryan"/>

<where>id=1</where>

</changeSet>

 

 

 

 

 

 

 

 

 

 

 
**添加必要依赖pom.xml**

​    <dependency>             <groupId>org.springframework.boot</groupId>             <artifactId>spring-boot-starter-jdbc</artifactId>         </dependency>     <dependency>             <groupId>mysql</groupId>             <artifactId>mysql-connector-java</artifactId>             <scope>runtime</scope>         </dependency>     <dependency>             <groupId>org.projectlombok</groupId>             <artifactId>lombok</artifactId>             <version>1.18.4</version>             <scope>provided</scope>         </dependency>     <!--liquibase-->             <dependency>             <groupId>org.springframework.boot</groupId>             <artifactId>spring-boot-starter-web</artifactId>         </dependency>         <dependency>             <groupId>org.liquibase</groupId>             <artifactId>liquibase-core</artifactId>         </dependency>     <!--mybatis plus-->         <dependency>             <groupId>org.mybatis.spring.boot</groupId>             <artifactId>mybatis-spring-boot-starter</artifactId>             <version>2.1.0</version>         </dependency>         <dependency>             <groupId>com.baomidou</groupId>             <artifactId>mybatis-plus-boot-starter</artifactId>             <version>3.0-RELEASE</version>         </dependency>         <dependency>             <groupId>org.freemarker</groupId>             <artifactId>freemarker</artifactId>             <version>2.3.23</version>         </dependency>         <dependency>             <groupId>com.alibaba</groupId>             <artifactId>fastjson</artifactId>             <version>1.2.47</version>         </dependency>

**application.properties**

server.port=8080 #mysql spring.datasource.url=jdbc:mysql://localhost:3306/train?useUnicode=true&characterEncoding=utf-8&useSSL=false&serverTimezone=GMT%2B8 spring.datasource.username=root spring.datasource.password=root1234 spring.datasource.driver-class-name=com.mysql.jdbc.Driver spring.liquibase.change-log=classpath:db/master.xml spring.liquibase.drop-first=false spring.liquibase.enabled=true #mybatis-plus mybatis-plus.mapper-locations=classpath:com/lqy/liquibase/mapper/xml/*.xml mybatis-plus.type-aliases-package=com.lqy.liquibase.entity

**liquibase**

**master.xml**

<?xml version="1.0" encoding="UTF-8"?> <databaseChangeLog        xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"        xmlns="http://www.liquibase.org/xml/ns/dbchangelog"        xsi:schemaLocation="http://www.liquibase.org/xml/ns/dbchangelog http://www.liquibase.org/xml/ns/dbchangelog/dbchangelog-3.1.xsd">    <include file="classpath:db/changelog/release.1.0.0.xml" relativeToChangelogFile="false"/> </databaseChangeLog>

**release.xml**

<?xml version="1.0" encoding="utf-8"?> <databaseChangeLog         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"         xmlns="http://www.liquibase.org/xml/ns/dbchangelog"         xsi:schemaLocation="http://www.liquibase.org/xml/ns/dbchangelog                       http://www.liquibase.org/xml/ns/dbchangelog/dbchangelog-3.4.xsd">     <property name="now" value="now()" dbms="mysql,h2"/>     <property name="now" value="sysdate" dbms="oracle"/>     <property name="autoIncrement" value="true" dbms="mysql,h2,postgresql,oracle"/>     <property name="amount" value="decimal(20,2)"/>      <changeSet id="201907181418002" author="qinye.lin">         <createTable tableName="sys_user" remarks="用户表">             <column name="id" type="bigint">                 <constraints nullable="false" primaryKey="true" primaryKeyName="pk_sys_user"/>             </column>             <column name="username" type="varchar2(50)" remarks="账号"/>             <column name="password" type="varchar2(50)" remarks="密码"/>         </createTable>     </changeSet> </databaseChangeLog>

**domain**

@Data @AllArgsConstructor @NoArgsConstructor @TableName(value = "sys_user") public class User implements Serializable {    @TableId    protected Long id;    @TableField(value = "username")    private String username;    @TableField(value = "password")    private String password; } 

**mybatis plus**

**Mapper接口**

@Repository public interface UserMapper extends BaseMapper<User> { } 

**Service**

@Service public class UserService {    @Autowired    private UserMapper userMapper;     public User findByUsername(User user) {        return userMapper.selectOne(new QueryWrapper<User>()                .eq("username", user.getUsername()));    }     public User findUserById(Long id){        return userMapper.selectById(id);    }     public void insertUser(User user){        userMapper.insert(user);    } } 

说明：mybatis-plus已经为我们将基本的crud操作封装以待，在代码生成的过程中我们也已经看到UserMapper接口自动继承了BaseMapper接口，它里面有丰富的接口方法且已经按照常规的开发习惯实现完毕，虽然我们的Mapper接口中一个方法都没有，却可以实现大部分crud操作。

![img](D:/Youdao/YoudaoNote/findgalaxy@163.com/a576976411c44352a0e9d5b7366410cc/edece93b7f17.png)

![img](D:/Youdao/YoudaoNote/findgalaxy@163.com/e87e8634074840eb91162190e6b73d89/743897d9cc35.png)
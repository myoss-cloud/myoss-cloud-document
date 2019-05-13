# About

- `myoss-datasource-routing` 组件用于单一项目中需要支持多套数据源，只需要做简单的配置，即可支持在同一方法调用堆栈中，非常方便的切换不同的数据源。
- [GitHub Site](https://github.com/myoss-cloud/myoss-datasource-routing)

# Features

- 支持 `spring boot/cloud` 项目的自动配置，只需在 `application.yml/application.properties` 中设置好数据源的属性，就能开箱使用。
- 支持 `spring项目` 和 `非spring项目`，需要自己显示的调用创建数据源的方法，适用那些非 `spring boot/cloud` 项目。
- 支持 `JdbcTemplate`。
- 支持将多个数据源分成不同的组，可以实现一主多从、读写分离等不同使用场景。
- 支持在同一方法调用堆栈中，不同方法使用不同的数据源。
- 支持 `spring cloud` 项目，热更新数据源的配置属性（比如：密码需要变更、数据库连接池大小调整）。
- 可以使用 `@app.myoss.cloud.datasource.routing.annotation.DataSource` 和 `定义Aop切面规则` 标记方法使用不同的数据源。

# Quick Start

## Requirements

- JDK 8 (or newer) 

## Add dependency

自己管理版本

```xml
<dependency>
    <groupId>app.myoss.cloud.jdbc</groupId>
    <artifactId>myoss-datasource-routing</artifactId>
    <version>1.0.0.RELEASE</version>
</dependency>
```

或者使用 [myoss组件进行依赖版本管理](/artifact-version/manage-dependencies.md)

```xml
<project>
    <dependencies>
        <dependency>
            <groupId>app.myoss.cloud.jdbc</groupId>
            <artifactId>myoss-datasource-routing</artifactId>
        </dependency>
    </dependencies>

    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>app.myoss.cloud</groupId>
                <artifactId>myoss-cloud-dependencies</artifactId>
                <version>${myoss-cloud-dependencies.version}</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>
</project>
```

## Configuration

### Configure the database in `application.yml`

```yaml
myoss-cloud:
  datasource-routing:
    group-data-source-config:
      load-balancer: app.myoss.cloud.datasource.routing.jdbc.loadbalancer.impl.RandomDataSourceLoadBalanced
    databases:
      - name: master
        describe: 我是主数据源，我只是描述信息，无其它作用
        groupName: groupMaster
        primary: true
        type: com.zaxxer.hikari.HikariDataSource
        driver-class-name: org.h2.Driver
        url: jdbc:h2:mem:db_test_hikari_case1;DB_CLOSE_DELAY=-1
        username: sa
        password: sa
        properties:
          # com.zaxxer.hikari.HikariDataSource 自己的属性
          connection-test-query: SELECT 'X' FROM DUAL
          maximum-pool-size: 20
          data-source-properties:
            # 设置可以获取remarks信息
            remarks: true
            # 设置可以获取tables remarks信息
            useInformationSchema: true
        methodPointcuts:
          - type: AspectJExpression
            expressions:
              # 此表达式可以匹配到 app.myoss.cloud.datasource.routing.testcase.hikari.user 这个package及所有子package下任何类的任何方法
              - execution(* app.myoss.cloud.datasource.routing.testcase.hikari.user..**.*(..))
      - name: slave
        describe: 我是从数据源，我只是描述信息，无其它作用
        groupName: groupSlave
        primary: false
        type: com.alibaba.druid.pool.DruidDataSource
        driver-class-name: org.h2.Driver
        url: jdbc:h2:mem:db_test_druid_case1;DB_CLOSE_DELAY=-1
        username: sa
        password: sa
        properties:
          # com.alibaba.druid.pool.DruidDataSource 自己的属性
          validation-query: SELECT 'X' FROM DUAL
          max-active: 30
        methodPointcuts:
          - type: JdkRegexpMethod
            expressions:
              # 此表达式可以匹配到 app.myoss.cloud.datasource.routing.testcase.hikari.history 这个package及所有子package下任何类的任何方法
              - app\.myoss\.cloud\.datasource\.routing\.testcase\.hikari\.history\..*
```

### Use `@DataSource`

add `@app.myoss.cloud.datasource.routing.annotation.DataSource` annotation in the class

```java
@DataSource(name = "slave")
@Service
public class UserHistoryService4Slave {
    @Autowired
    private JdbcTemplate jdbcTemplate;

    public List<UserHistory> findList() {
        String sql = "SELECT id, employee_number, name FROM t_sys_user_history";
        log.info(sql);
        return jdbcTemplate.query(sql, (rs, rowNum) -> {
            UserHistory user = new UserHistory();
            user.setId(rs.getLong("id"));
            user.setName(rs.getString("name"));
            user.setEmployeeNumber(rs.getString("employee_number"));
            return user;
        });
    }
}
```

add `@app.myoss.cloud.datasource.routing.annotation.DataSource` annotation in the method

```java
@Service
public class UserServiceImpl extends BaseCrudServiceImpl<UserMapper, User> implements UserService {
    @DataSource(name = DataSourceRoutingConstants.MASTER_DATA_SOURCE_NAME)
    @Override
    public <I> Result<I> create(User record) {
        return super.create(record);
    }
}
```
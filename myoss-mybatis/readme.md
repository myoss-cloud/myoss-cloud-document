# About

- `myoss-mybatis` 组件是在 [MyBatis 3](http://www.mybatis.org/mybatis-3/) 的基础上进行了功能扩展，简化了字段映射和通用增删改查的配置，提高了开发效率。
- [GitHub Site](https://github.com/myoss-cloud/myoss-mybatis)

# Features

- 支持 `spring boot/cloud` 项目的自动配置，只需在 `application.yml/application.properties` 中进行简单的配置，就能开箱使用。
- 提供 `常用的CRUD方法`，提供了 `CrudService` 和 `CrudMapper`，可以很方便的进行扩展，用于支持不同的场景。
- 提供了 `Sequence` 序列生成器接口，可以实现自定义的序列生成，灵活适应业务的主键生成。
- 提供代码生成插件，可以生成 `Entity/Mapper/Service/Controller` 代码，可以很方便的进行扩展，实现不同的模版代码生成器。

# Quick Start

## Requirements

- JDK 8 (or newer) 
- Mybatis 3.4.6 (or newer)
- Spring 5.1.0 (or newer)

## Add dependency

自己管理版本

```xml
<dependency>
    <groupId>app.myoss.cloud.mybatis</groupId>
    <artifactId>myoss-mybatis</artifactId>
    <version>2.1.2.RELEASE</version>
</dependency>
```

或者使用 [myoss组件进行依赖版本管理](/artifact-version/manage-dependencies.md)

```xml
<project>
    <dependencies>
        <dependency>
            <groupId>app.myoss.cloud.mybatis</groupId>
            <artifactId>myoss-mybatis</artifactId>
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

### Configure the mybatis in `application.yml`

```yaml
mybatis:
  mapper-locations: classpath*:mybatis/**/*Mapper.xml
  mapper-scanner:
    base-package: app.myoss.cloud.test.component.mybatis
```

### Create Table

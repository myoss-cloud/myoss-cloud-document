# About

- [GitHub Site](https://github.com/myoss-cloud/spring-boot-build)

# Usage

## 最新的版本号

```xml
    <properties>
        <myoss-cloud-dependencies.version>2.1.5.RELEASE</myoss-cloud-dependencies.version>
    </properties>
```

## `pom.xml` 继承的方式

```xml
    <parent>
        <groupId>app.myoss.cloud</groupId>
        <artifactId>myoss-spring-boot-parent</artifactId>
        <version>${myoss-cloud-dependencies.version}</version>
    </parent>
```

## `pom.xml` 使用 `import` 的方式

```xml
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
```

## 版本更新说明

**2.1.5.RELEASE**

- 新增 "动态数据源路由" 组件: `myoss-datasource-routing`
- 升级 `myoss-mybatis to 2.1.2.RELEASE`
- 升级 `java-code-style to 2.1.1.RELEASE`, 去掉 `spring` 三元表达式的代码校验

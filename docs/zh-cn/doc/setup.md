使用TestableMock
---

`TestableMock`是基于源码和字节码增强的Java单元测试辅助工具，包含以下功能：

- [访问被测类私有成员](zh-cn/doc/private-accessor.md)：使单元测试能直接调用和访问被测类的私有成员，解决私有成员初始化和私有方法测试的问题
- [快速Mock任意调用](zh-cn/doc/use-mock.md)：使被测类的任意方法调用快速替换为Mock方法，实现"指哪换哪"，解决传统Mock工具使用繁琐的问题
- [辅助测试void方法](zh-cn/doc/test-void-method.md)：利用Mock校验器对方法的内部逻辑进行检查，解决无返回值方法难以实施单元测试的问题

## 在Maven项目中使用

在项目`pom.xml`文件中，增加`testable-processor`依赖和`maven-surefire-plugin`配置，具体方法如下。

建议先添加一个标识TestableMock版本的`property`，便于统一管理：

```xml
<properties>
    <testable.version>0.3.1</testable.version>
</properties>
```

在`dependencies`列表添加`testable-processor`依赖：

```xml
<dependencies>
    <dependency>
        <groupId>com.alibaba.testable</groupId>
        <artifactId>testable-processor</artifactId>
        <version>${testable.version}</version>
        <scope>provided</scope>
    </dependency>
</dependencies>
```

最后在`build`区域的`plugins`列表里添加`maven-surefire-plugin`插件（如果已有此插件则只需添加`<argLine>`部分配置）：

```xml
<build>
    <plugins>
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-surefire-plugin</artifactId>
            <configuration>
                <argLine>-javaagent:${settings.localRepository}/com/alibaba/testable/testable-agent/${testable.version}/testable-agent-${testable.version}.jar</argLine>
            </configuration>
        </plugin>
    </plugins>
</build>
```

若项目同时还使用了`Jacoco`的`on-the-fly`模式（默认模式）统计单元测试覆盖率，则需在`<argLine>`配置中添加一个`@{argLine}`参数，添加后的配置如下：

```xml
<argLine>@{argLine} -javaagent:${settings.localRepository}/com/alibaba/testable/testable-agent/${testable.version}/testable-agent-${testable.version}.jar</argLine>
```

## 在Gradle项目中使用

在`build.gradle`文件中添加`testable-processor`依赖：

```groovy
dependencies {
    testCompile('com.alibaba.testable:testable-processor:0.3.1')
}
```

然后在测试配置中添加javaagent：

```groovy
test {
    jvmArgs "-javaagent:${classpath.find { it.name.contains("testable-agent") }.absolutePath}"
}
```

> 该配置尚未在Gradle项目上经过实际验证，可行性待确认。
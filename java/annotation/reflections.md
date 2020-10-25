# reflections 工具类

[GitHub](https://github.com/ronmamo/reflections)

反射扫描您的类路径，索引元数据，允许您在运行时查询它，并可能为您的项目中的许多模块保存和收集信息。

利用反射你可以:

1. 获取某个类型的所有子类型
2. 使所有类型/成员都带有一些注解
3. 获取与正则表达式匹配的所有资源
4. 获取所有具有特定签名的方法，包括参数、参数注释和返回类型

maven

```xml
<dependency>
    <groupId>org.reflections</groupId>
    <artifactId>reflections</artifactId>
    <version>0.9.12</version>
</dependency>
```

### 全包扫描

将会扫描指定的包下的所有的类

```java
Reflections reflections = new Reflections("my.project");
```

### 使用 ConfigurationBuilder

```java
ConfigurationBuilder configurationBuilder = new ConfigurationBuilder()
                // 扫描包的前缀
                .forPackages("my.package")
                // 添加子类扫描工具
                .addScanners(new SubTypesScanner())
                // 添加 方法注解扫描工具
                .addScanners(new MethodAnnotationsScanner())
    			// 过滤包
                .filterInputsBy(new FilterBuilder().include(""));
Reflections reflections = new Reflections(configurationBuilder);
```

### 扫描工具

1.  FieldAnnotationsScanner
    
	- 扫描字段的注释
      (scans for field's annotations)
      
      ```java
      Set<Field> ids = 
          reflections.getFieldsAnnotatedWith(javax.persistence.Id.class);
      ```
2. MemberUsageScanner
   
	- 扫描方法/构造体/字段的使用情况
     (scans methods/constructors/fields usage)
    
     ```java
     Set<Member> usages = 
         reflections.getMethodUsages(Method.class)
     ```
   
3. MethodAnnotationsScanner

   - 扫描方法注解

     ```java
     Set<Method> resources =
         reflections.getMethodsAnnotatedWith(javax.ws.rs.Path.class);
     Set<Constructor> injectables = 
         reflections.getConstructorsAnnotatedWith(javax.inject.Inject.class);
     ```

4. MethodParameterScanner

   - 扫描方法/构造函数并索引参数、返回类型和参数注解。
     (scans methods/constructors and indexes parameters, return type and parameter annotations)
     
     ```java
     Set<Method> someMethods =
         reflections.getMethodsMatchParams(long.class, int.class);
     Set<Method> voidMethods =
         reflections.getMethodsReturn(void.class);
     Set<Method> pathParamMethods =
         reflections.getMethodsWithAnyParamAnnotated(PathParam.class);
     ```

5. MethodParameterNamesScanner

   - 扫描方法/构造函数并索引参数名(scans methods/constructors and indexes parameter names)

     ```java
     List<String> parameterNames = 
         reflections.getMethodParamNames(Method.class)
     ```

6. ResourcesScanner

   - 收集所有静态的资源。(collects all resources that are not classes in a collection)

     key: value - {web.xml: WEB-INF/web.xml}
     
     ```java
     Set<String> properties = 
         reflections.getResources(Pattern.compile(".*\\.properties"));
     ```

7. SubTypesScanner

   - 扫描一个类的超类和接口，允许反向查找子类型
     (scans for superclass and interfaces of a class, allowing a reverse lookup for subtypes)
     
     ```java
     Set<Class<? extends Module>> modules = 
         reflections.getSubTypesOf(com.google.inject.Module.class);
     ```

8. TypeAnnotationsScanner

   - 扫描注解的父注解
     (scans for class's annotations, where @Retention(RetentionPolicy.RUNTIME))
     
     ```java
     Set<Class<?>> singletons = 
         reflections.getTypesAnnotatedWith(javax.inject.Singleton.class);
     ```

9. TypeElementsScanner

   - 扫描字段和方法，并将fqn作为键和元素作为值存储。
     (scans fields and methods and stores fqn as key and elements as values)

#### ps

- 如果没有配置扫描器，则会使用默认值--SubTypesScanner和TypeAnnotationsScanner。
  If no scanners are configured, the default will be used - `SubTypesScanner` and `TypeAnnotationsScanner`.
- 还可以配置类加载器，它将用于从名称解析运行时类。
  Classloader can also be configured, which will be used for resolving runtime classes from names.

- Reflections默认会扩展超级类型。这就解决了一些转折性的urls不被扫描的问题。
  Reflections expands super types by default. This solves some problems with transitive urls are not scanned.

### ReflectionUtils

```java
import static org.reflections.*;

Set<Method> getters = ReflectionUtils.getAllMethods(someClass,
  withModifier(Modifier.PUBLIC), withPrefix("get"), withParametersCount(0));

//or
Set<Method> listMethodsFromCollectionToBoolean = 
  ReflectionUtils.getAllMethods(List.class,
                                withParametersAssignableTo(Collection.class), 		                                           withReturnType(boolean.class));

Set<Field> fields = ReflectionUtils
    .getAllFields(SomeClass.class, withAnnotation(annotation), withTypeAssignableTo(type));
```


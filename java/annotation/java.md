## Java 注解

Annotation 是从 JDK5.0 开始引入的新技术

使用 `@interface` 自定义注解时，自动继承 `java.lang.annotation.Annotation`

### Annotation的作用

- 可以被其他程序( 编译器) 读取
- 不是程序本身，可以对程序作出解释
- 可以通过反射机制实现对这些元数据的访问

### 内置注解

> @Override 重写父类中的另一个方法的声明
>
> @Deprecated 已过时 标识不推荐使用的方法、对象及属性
>
> @SuppressWarnings 用来抑制编译时的警告信息

### 元注解

元注解的作用: 负责注解其他注解

>@Target 用于描述注解使用范围
>
>@Retention 注解的声明周期(source < class < runtime)
>
>@Documented 文档
>
>@Inherited 子类可以继承父类

### 自定义注解

使用 `@interface` 自定义注解时，自动继承 `java.lang.annotation.Annotation` 接口

格式: ` public @interface 注解名 {定义内容}`

1. 参数类型只能是 基本类型、Class、String、Enum
2. 方法名称就是参数名称
3. 如果只有一个参数成员，一般参数名为 value
4. 注解元素必须要有值，我们定义注解元素时，经常使用 空字符串 或者 0 作为默认值




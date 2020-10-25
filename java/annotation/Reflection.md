# 反射

## 简介

Reflection (反射)

反射方式 `实例化对象 -> getClass()方法 -> 得到完整"包类"名称`



## Class 类

### 介绍

1. Class 本身也是一个类。
2. Class 对象只能由系统建立对象。
3. 一个加载的类在JVM中只会有一个 Class 实例所生成。
4. 一个 Class 对象对应的是一个加载到JVM中的一个 .class 文件。
5. 每个类的实例会记得自己是由哪个 Class 实例所生成的。
6. 通过 Class 可以完整地得到一个类中所有被加载的结构。
7. Class 类是Reflection的根源，针对任何动态加载、运行的类，只有先得到相应的 Class 对象。

### Class 类 常用方法

| 方法名                                     | 功能说明                                       |
| ------------------------------------------ | ---------------------------------------------- |
| static ClassForName(String name)           | 返回指定类名 name 的 class 对象                |
| Object newInstance()                       | 调用默认构造函数返回 Class 对象一个实例        |
| getName()                                  | 返回此 Class 对象所表示的实体名称              |
| Class getSuperClass()                      | 返回当前 Class 对象的父类的 Class              |
| Class[]getInterfaces()                     | 返回当前 Class 对象的所有接口                  |
| ClassLoader getClassLoader()               | 返回该类的类加载器                             |
| Constructor[] getConstructors()            | 返回该类 Constructor                           |
| Method getMothed(String name,  Class... T) | 返回 Method 对象, 此对象的形参类型为 ParamType |
| Field[] getDeclaredFields()                | 返回 Field 对象的一个数组                      |

#### 获取 Class 类的实例

1. Class clazz = Person.class;
2. Class clazz = person.getClass();
3. Class clazz = Class.forName("xiaou.Person");


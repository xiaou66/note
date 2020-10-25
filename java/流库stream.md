# java8 流库（Stream）
---
# 引入
  如果我们想对一个字符串数组或者集合统计其总长度一般情况下是这么写的
  ```java
  long count = 0;
  for(String w : words)
    count += w.length();
  ```

  而`流库`的出现我们可以这样写

  ```java
  long count = words.stream()
              .collect(collectors.summarizingInt(String::length))
              .getSum()
  ```

# 流的概念
## 流的概念
* 目的

  >做什么而非什么做
* 特点
  >1.Stream 不是集合元素
  >
  >2.Stream 就如同一个迭代器（`Iterator`）,单向，不可往复，数据只能遍历一次
  >
  >3.Stream 可以并行化操作
  >
  >4.Stream 数据源本身可以是无限的
* 流分类 (操作类型)
  >1.Intermediate（转换操作）
  >
  >2.erminal（终止操作）

Intermediate（`转换操作`）:一个流可以后面跟随零个或多个 intermediate 操作。其目的主要是打开流，做出某种程度的数据映射/过滤，然后返回一个新的流，交给下一个操作使用。这类操作都是惰性化的（lazy）,就是说是仅仅调用这类方法，并未进行真正开始流

Terminal（`终止操作`）:当这个操作执行后，流就被使用“光”了，无法再被操作。所以这必定是流的最后一个操作。
## 流和集合的差异
> - 流是不存储其元素
> - 流的操作是不会修改其数据源
> - 流的操作是尽可能惰性执行
>

## 流的基本流程
> 1. 创建一个流
> 2. 指定将初始流转换为其他中间流的操作
> 3. 流终止操作，从而产生结果 这个操作会强制执行之前的惰性操作

## 流的创建
```java
// 方式一
//  利用数组创建流
Stream<String> words = Stream.of(content.split);
// 方式二
// 创建一个空流
Stream<String> words = Stream.empty();
// 方式三
// 创建无限流 --generate
Stream.generate(()->"hello world");
//static<T> Stream<T> iterate(T seed,UnaryOperator<T> f);
Stream.iterate(0,n->n+1);
// 或者无限数字流
Stream.generate(Math::random);
// 在java API 中也有很多方式可以创建流
Arrays.Stream();
Files.lines();
//等等
```
## filter map flatMap 方法
### filter
使用`filter` 转换会产生一个流，它的元素与某种条件相匹配
```java
// 使用 filter 过滤小于等于 12 的字符串
Stream<String> longWords = wordList.stream()
                        .filter(w -> w.length() > 12);

```
如果想要按照某种方式转换流中的值，使用`map`
```java
Stream<String> lowercaseWords = words.stream()
                             .map(s -> s.substring(0,1));
```
在使用map的时候会发现一个函数会应用到每一个元素上并且其结果是包含了应用该函数后所产生的结果流，
如果现在有一个函数他不是返回一个流而是返回众多值的流这时，
使用map只能返回 `[[1,2,3],[4,5,6],...]` 可能更合适的是使用`flatmap`
```java
private Stream<String> upper(String w ){
        List<String> strs = new ArrayList<>();
        for (int i = 0; i < w.length(); i++) {
            strs.add(w.substring(i,i+1));
        }
        return strs.stream();

  }
 @Test
 public void streamMapAndFlatMap(){
     List<String> list = new ArrayList<>();
     list.add("hello");
     list.add("world");
     list.add("xiaou");
     // flatMap
    List<String> collect1 = list.stream()
                          .flatMap(w -> upper(w))
                          .collect(Collectors.toList());

    for (String str : collect1){
        System.out.print(str);
    }
 }
```
## 简单的约简
约简 一种终结操作，可以将流变成在程序中使用的非流值。
常见的约简操作有
```java
public static void main(String[] args) {
        List<String> str = new ArrayList<>();
        str.add("12");
        str.add("23");
        str.add("345");
        // 统计流中的总数
        long count = str.stream().count();
        System.out.println(count);

        // 输出最长字符串
        Optional<String> max = str.stream()
                .max(String::compareToIgnoreCase);

        max.ifPresent(System.out::println);

        // 查找以第一个开头为 3 的字符串
        str.stream()
                .filter(s -> s.startsWith("3"))
                .findFirst()
                .ifPresent(System.out::println);

        // 并不想按顺序查找，随便哪个只要含有就返回
        str.stream()
                .parallel()
                .filter(s -> s.contains("2"))
                .findAny()
                .ifPresent(System.out::println);

        // 只是想判断是否含有
        str.stream()
                .parallel()
                .anyMatch(s -> s.contains("2"));
    }
```
# 收集结果
当处理完流后，通常想查看其元素。我们这时候可以调用`1iterator`的方法产生可以用来访问元素的迭代器
也可以调用forEach方法将某个函数应用到每个元素
> 在并行流上，`forEach` 方法会以任意顺序遍历各个元素。如果想按照流的顺序处理元素，可以调用 `forEachOrdered` 方法
> 当然这个方法会让并行流丧失并行处理的部分甚至全部的优势

如果想把如果收集到数据结构中可以使用`toArray`来获得用流构成的数组
如果想把流中的元素收集到集合中有一个便捷的方法`collect`可用，会接收一个`Collector`接口的实例。
`Collector`类提供了大量的生成公共收集器的工厂方法。
为了流中的元素收集到列表或集中，可以直接调用
```java

    @Test
    public void stearmSet(){
        Set<String> set = list.stream()
                           .collect(Collectors.toSet());
        show("toSet()",set);
    }
    // 转换到指定的集的种类
    @Test
    public void steamTreeSet(){
        TreeSet<String> collect = list.stream()
                            .collect(Collectors.toCollection(TreeSet::new));
        show("treeSet:",collect);
    }
    // 收集流中所有的字符串
    @Test
    public void streamToString(){
        String collect = list.stream()
                      .collect(Collectors.joining());
        System.out.println(collect);
    }
    // 在元素之间添加分隔符
    @Test
    public void streamToStringDelimiter(){
        String collect = list.stream()
                      .collect(Collectors.joining("|"));
        System.out.println(collect);
    }  
    // 将字符串以外的替它对象转换为字符串
    @Test
    public void  StringObjectToString(){
        String collect = list.stream()
                .map(Object::toString)
                .collect(Collectors.joining(","));
        System.out.println(collect);
    }
```
其他的转换，把流的价格简约为总和、平均值、最大值或者最小值，可以使用`summarizing(Int|Long|Double)`
方法的其中一个，这些函数会将流映射成为数据的函数，同时这些方法会产生类型`(Int|Long|Double)SummaryStatistics`
的结果可以计算总和、平均值、最小值、最大值
```java
   @Test
    public void streamAggfun(){
        IntSummaryStatistics collect = list.stream()
                              .collect(
                                  Collectors.summarizingInt(String::length));

        System.out.println("总数" + collect.getCount());
        System.out.println("平均数" + collect.getAverage());
        System.out.println("总和" + collect.getSum());
        System.out.println("最大值" + collect.getMin());
        System.out.println("最小值" + collect.getMin());
        System.out.println("foreach");
        list.stream().forEach(System.out::println);
    }
```
## 将流的结果映射表中
如果我们有一个Stream<Student>的流然后想把学生的id和Student对象用键值对的形式关联起来
```java
map<Integer,Student> map = stu.collect(
  Collectors.toMap(Student::getId,Student));
```
上面的情况如果出现相同的键，收集器则会抛出一个`IllegalStateException`异常对象。
可以通过提供第三个参数来解决这一问题，这个函数会针对给定的已有的值和新值来解决冲突并确定键对应的值
这个函数返回的是应有的值、新值、或者他们的组合
```java
// 放回新值或现存值
map<Integer,Student> map = stu.collect(
          Collectors.toMap(Student::getId,
                   Student,
                   (existingValue,newValue) -> existingValue));
// 返回新值和现存值的集合
map<Integer,Set<Student>> map = stu.collect(
          Collectors.toMap(Student::getId,
                   Student,
                   (existingValue,newValue) -> {
                     Set<Student> union = new HashMap<>(existingValue);
                     union.addAll(newValue
                     return union;}));
```
如果想要得到一个指定的`map`类型,指定第四个参数
例如：
```java
map<Integer,Student> map = stu.collect(
          Collectors.toMap(Student::getId,
                   Student,
                   (existingValue,newValue) -> existingValue),
                   TreeMap::new);
```
## 群组和分区

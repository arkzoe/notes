- JDK8新增，用于操作集合或数组
- 大量结合Lambda风格，简洁
###### 步骤
1. 获取Stream流
2. 调用方法
3. 获取处理结果
### 获取Stream流
- 获取集合的stream流

| Collection提供的如下方法           |                  |
| --------------------------- | ---------------- |
| default Stream< E> stream() | 获取当前集合对象的stream流 |
- 获取数组的Stream流

| Arrays类提供的如下 方法                         |                            |
| ----------------------------------------------- | -------------------------- |
| public static < T> Stream< T> stream(T[] array) | 获取当前数组的stream流     |
| Stream类提供的如下方法                          |                            |
| public static< T> Stream< T> of(T...values)     | 获取当前接收数据的stream流 |

```java
Map Stream流
//键流
Stream<String> s = map.keySet().stream();
//值流
Stream<String> s = map.values().stream();
//键值对流
Stream<Map.Entry<K,V>> s = map.entrySet().stream();
```
### 中间方法
- 调用完会返回新的Stream流，可以继续使用（链式编程）

| Stream提供的常用中间方法                                              |                  |
| ------------------------------------------------------------ | ---------------- |
| Stream< T> filter(Predicate< ? super T> predicate)           | 用于对流中的数据进行过滤     |
| Stream< T> filter(Predicate< ? super T> predicate)           | 对元素进行升序排序        |
| Stream< T> sorted(Comparator< ? super I> comparator)         | 按照指定规则排序         |
| Stream< T> limit(long maxSize)                               | 获取前几个元素          |
| Stream< T> skip(long n)                                      | 跳过前几个元素          |
| Stream< T> distinct()                                        | 去除流中重复的元素        |
| < R> Stream< R> map(Function< ? super I,? extends R> mapper) | 对元素进行加工，并返回对应的新流 |
| static < T> Stream< T> concat(Stream a, Stream b)            | 合并a和b两个流为一个流     |

### 终结方法
- 调用完不返回新的Stream流

| Stream提供的常用终结方法                                     |               |
| --------------------------------------------------- | ------------- |
| void forEach(Consumer action)                       | 对此流运算后的元素执行遍历 |
| long count()                                        | 统计此流运算后的元素个数  |
| Optional< T> max(Comparator< ? super I> comparator) | 获取此流运算后的最大值元素 |
| Optional< T> min(Comparator< ? super I> comparator) | 获取此流运算后的最小值元素 |

#### 收集Stream流
- 把Stream流操作后的结果转回到集合或者数组中返回
- Stream流：手段，数组/集合：目的

| Stream提供的常用终结方法                                                          |                      |
| ------------------------------------------------------------------------ | -------------------- |
| R collect(Collector collector)                                           | 把流处理后的结果收集到一个指定的集合中去 |
| Object[]  toArray()                                                      | 把流处理后的结果收集到一个数组中去    |
| Collectors工具类提供了具体的收集方式                                                  |                      |
| public static < T> Collector toList()                                    | 把元素收集到List集合中        |
| public static < T> Collector toSet()                                     | 把元素收集到Set集合中         |
| public static Collector toMap（Function keyMapper , Function valueMapper) | 把元素收集到Map集合中         |

- 流只能收集一次
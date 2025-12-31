- JDK8新增，用于操作集合或数组
- 大量结合Lambda风格，简洁
###### 步骤
1. 获取Stream流
2. 调用方法
3. 获取处理结果
### 获取Stream流
![[获取Stream.png]]
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
![[中间方法.png]]
### 终结方法
- 调用完不返回新的Stream流
![[终结方法.png]]
#### 收集Stream流
- 把Stream流操作后的结果转回到集合或者数组中返回
- Stream流：手段，数组/集合：目的
![[收集.png]]
- 流只能收集一次
![[Map.png]]
- 健不能重复
- 存一一对应的数据
#### 特点
- 由键决定特点
- 添加元素是无序、不重复、无索引
	- LinkedHashMap:==有序==
	- TreeMap:==按大小默认升序排序==
### 常用方法
![[Map方法.png]]
### 遍历方法
#### 健找值
- 先获取Map集合全部的键，再遍历键
```java
public Set <K> keySet()
public V get(Object key)
```
#### 键值对
- 看成一个整体
```java
Set<Map.Entry<K,V>> entrySet() //获取所有键值对集合
Set<Map.Entry<K,V>> entries = map.entrySet();
for(Map.Entry<K,V> entry : entries){
	key = entry.getKey()
	value = entry.getValue()
}
```
- Map换成Set集合，Entry对象
#### Lambda表达式
![[Map-Lambda.png]]
```java
map.forEach(new BiConsumer<K,V>(){
	public void accpet(K k,V v){
		System.out.println();
	}
});
map.forEach((k,v) -> System.out.println());
```
增强for实现
### 实现类
![[Map-实现类.png]]
- Set基于Map实现--Set原理就是Map原理
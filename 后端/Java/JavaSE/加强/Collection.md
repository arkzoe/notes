![[单列.png]]
#### [[List]]系列
- 添加元素是有序、可重复、有索引
#### [[Set]]系列
- 添加元素是无序、不重复、无索引
	- LinkedHashSet:==有序==
	- TreeSet:==按大小默认升序排序==
## Collection 常用功能
![[Collection常用功能.png]]
## 遍历方式
### 迭代器遍历
- 迭代器是专门用于遍历集合的方法，代表Iterator
#### 遍历一：迭代器
![[迭代器遍历-C.png]]
```java
while(it.hasNext()){
	String name = it.next();
	...
}
```
#### 遍历二：for
```java
for (元素类型 变量名：数组/集合){

}
```
#### 遍历三：Lambda
![[Lambda遍历-C.png]]
底层for实现
### 区别
- 并发修改异常--遍历时又增删
1. 迭代器遍历--用迭代器方法删除
2. for/Lambda--无法解决
- 支持索引 for
- 否迭代器
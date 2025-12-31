![[Set.png]]
- 添加元素是无序、不重复、无索引
	- LinkedHashSet:==有序==
	- TreeSet:==按大小默认升序排序==
- 常用功能Collection
## 原理
### HashSet
#### 哈希值
- java中每个对象都有一个哈希值
- 调用Object的hashCode方法，返回哈希值
- public int hashCode()
#### 哈希表
- JDK8之前，数组+链表
	1. 创建默认长度16的数组，默认加载因子0.75，数组名table
	2. 用==哈希值==对==数组长度做运算==计算出位置
	3. 判断位置是否为null，null存入
	4. ==不是null，用equals比较，相等不存；不相等，存
		- 链地址法 
		- JDK8 前，新元素占老元素位置，老元素挂下面
		- JDK8 后，新元素挂老元素下面
- JDK8开始，数组+链表+红黑树--==链表长度超过8，数组长度>=64，将链表转成红黑树==
	- 二叉排序树
		- 小左，大右
	- 平衡二叉树
		- 满足查找二叉树规则下，让树尽可能矮
	- 红黑树-自平衡的二叉树
		- 增删改查性能好
#### 去重操作
- 重写 hashCode()和equals()
### LinkedHashSet原理
- 基于哈希表（数组+链表+红黑树）实现
- 每个元素多了双链表记录前后元素的位置
### TreeSet原理
- 红黑树
- 对象类实现 Comparable 比较接口，重写 compareTo 方法，指定比较规则
	- 左大正，右大负，等为0--默认升序
- public TreeSet (Compartor c) 自带 Comparable 对象，指定比较规则
	- Double.compare()
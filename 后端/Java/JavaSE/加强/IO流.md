# 分类
1. 流的方向
	1. 输入流
	2. 输出流
2. 流的内容
	1. 字节流--适合所有类型
	2. 字符流--纯文本
## 体系
![[IO流体系.png]]
### 字节流
#### FIleInputStream--文件字节输入流
- 以内存为基础把磁盘文件中的数据以字节形式读取到内存中
![[FileInput.png]]
- read()，读取性能差，汉字乱码
- buffer，减少硬盘内存交互次数，提升性能，无法避免汉字乱码
```java
byte[] buffer = new byte[3];
int len;//每次读的个数
while((len = is.read(buffer)) != -1){
	String str = new String(buffer);
	String str = new String(buffer,0,len);//读从0到len
	print
}
```
- 一次性读全部字节--public byte[] readAllBytes() throws IOException
- 读文本适合用字符流，字节流适合数据转移
#### FIleOutputStream--文件字节输出流
![[FileOutput.png]]
- 用完要关
#### 资源释放
- try-with-resoure
	- JDK7提供
	```java
	try(定义资源1;定义资源。。。){
		//资源使用完毕会自动调用close()
	}catch(Exception e){
		e.printStackTrace();
	}
	```
- try-catch-finally==废弃==
	- finally一定执行
	- ==一般用于程序执行完成后进行资源释放--专业==
	```java
	InputStream fis = null;
	OutputStream fos = null;
	try{
		fis = ...;
		fos = ...;
	}catch(Exception e){
		e.printStackTrace();
	}finally{
		try{
			if(fos != null)fos.close();
		}catch(Exception e){
			e.printStackTrace();
		}
		try{
			if(fis != null)fis.close();
		}catch(Exception e){
			e.printStackTrace();
		}
	}
	```
	资源一般指最终实现AutoCloseable接口
### 字符流
#### FileReader--文件字符输入流
- 文件以字符的形式读入到内存中
![[FileReader.png]]
- 一次读多个字符，性能较好
#### FileWriter--文件字符输出流
- 以内存为基准，把内存中的数据以字符形式写出到文件中
![[FileWriter.png]]
- 覆盖管道
- 写出数据后，必须刷新/关闭流，数据才能生效
![[刷新流.png]]
- 管道内缓存
- 刷新后，流可继续使用，关不行
### 缓冲流
#### 字节流
- 8kb 缓冲池
![[缓冲字节流.png]]
#### 字符流
- 8k
##### 输入流
![[缓冲字符输入流.png]]
- 性能好，不乱码
##### 输出流
![[缓冲字符输出流.png]]
## 性能分析
- 字节数组快，桶加大能达到缓冲性能
- 桶最大32kb，过大占性能
### 其他流
#### 字符输入转换流--InputStreamReader
- 不同编码读取乱码问题
- 先获取文件原始字节流，按真实字符集编码转换成字符输入流
![[字符输入转换流.png]]
#### 打印流--PrintStream/PrintWriter
- 实现打印啥就是啥
- 方便、高效
![[PrintStream.png]]
- 高级管道不能追加，需内包低级管道
#### 特殊数据流--DataInputStream/
##### DataOutputStream
- 允许数据和类型一并写出
![[数据输出流.png]]
- 怎么写怎么读
## IO框架--commons-io
- 类、接口等编译成class形式，压缩成jar
![[IO框架.png]]
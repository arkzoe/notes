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

| 构造器                                     |                                               |
| --------------------------------------- | --------------------------------------------- |
| public FileInputStream(File file)       | 创建字节输入流管道与源文件接通                               |
| public FileInputStream(String pathname) | 创建字节输入流管道与源文件接通                               |
|                                         |                                               |
| 方法名                                     |                                               |
| public int read()                       | 每次读取一个字节返回，如果发现没有数据可读会返回-1                    |
| public int read(byte[] buffer)          | 每次用一个字节数组去读取数据，返回字节数组读取了多少个字节，如果发现没有数据可读会返回-1 |

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
- 一次性读全部字节--`public byte[] readAllBytes() throws IOException`
- 读文本适合用字符流，字节流适合数据转移
#### FIleOutputStream--文件字节输出流
- 作用：以内存为基准，把内存中的数据以字节的形式写出到文件中去

| 构造器                                                      |                         |
| -------------------------------------------------------- | ----------------------- |
| public FileOutputStream(File file)                       | 创建字节输出流管道与源文件对象接通       |
| public FileoutputStream(String filepath)                 | 创建字节输出流管道与源文件路径接通       |
| public FileOutputStream(File file, boolean append)       | 创建字节输出流管道与源文件对象接通，可追加数据 |
| public FileOutputStream(String filepath, boolean append) | 创建字节输出流管道与源文件路径接通，可追加数据 |
|                                                          |                         |
| 方法名称                                                     |                         |
| public void write(int a)                                 | 写一个字节出去                 |
| public void write(byte[] buffer)                         | 写一个字节数组出去               |
| public void write(byte[] buffer , int pos , int len)     | 写一个字节数组的一部分出去           |
| public void close() throws IOException                   | 关闭流                     |

- 用完要关
#### 资源释放
- try-with-resoure
	- JDK7提供
	```java
	try(定义资源1;定义资源){
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

| 构造器                                |                                               |
| ---------------------------------- | --------------------------------------------- |
| public FileReader(File file)       | 创建字符输入流管道与源文件接通                               |
| public FileReader(String pathname) | 创建字符输入流管道与源文件接通                               |
|                                    |                                               |
| 方法                                 |                                               |
| public int read()                  | 每次读取一个字符返回，如果发现没有数据可读会返回-1                    |
| public int read(char[] buffer)     | 每次用一个字符数组去读取数据，返回字符数组读取了多少个字符，如果发现没有数据可读会返回-1 |

- 一次读多个字符，性能较好
#### FileWriter--文件字符输出流
- 以内存为基准，把内存中的数据以字符形式写出到文件中

| 构造器                                                |                         |
| -------------------------------------------------- | ----------------------- |
| public FileWriter(File file)                       | 创建字节输出流管道与源文件对象接通       |
| public FileWriter(String filepath)                 | 创建字节输出流管道与源文件对象接通       |
| public FileWriter(File file,boolean append)        | 创建字节输出流管道与源文件对象接通，可追加数据 |
| public FileWriter(String filepath, boolean append) | 创建字节输出流管道与源文件路径接通，可追加数据 |
|                                                    |                         |
| 方法                                                 |                         |
| void write(int c)                                  | 写一个字符                   |
| void write(String str)                             | 写一个字符串                  |
| void write(String str, int off, int len)           | 写一个字符串的一部分              |
| void write(char[] cbuf)                            | 写入一个字符数组                |
| void write(char[] cbuf, int off int len)           | 写入字符数组的一部分              |

- 覆盖管道
- 写出数据后，必须刷新/关闭流，数据才能生效

| 方法名                                 |                                                      |
| -------------------------------------- | ---------------------------------------------------- |
| public void flush() throws IoException | 刷新流，就是将内存中缓存的数据立即写到文件中去生效！ |
| public void close() throws IoException | 关闭流的操作，包含了刷新！                           |

- 管道内缓存
- 刷新后，流可继续使用，关不行
### 缓冲流
#### 字节流
- 8kb 缓冲池

| 构造器                                          |                                     |
| -------------------------------------------- | ----------------------------------- |
| public BufferedInputStream(InputStream is)   | 把低级的字节输入流包装成一个高级的缓冲字节输入流，从而提高读数据的性能 |
| public BufferedoutputStream(OutputStream os) | 把低级的字节输入流包装成一个高级的缓冲字节输入流，从而提高读数据的性能 |

#### 字符流
- 8k
##### 输入流

| 构造器                             |                                         |
| ------------------------------- | --------------------------------------- |
| public BufferedReader(Reader r) | 把低级的字符输入流包装成字符缓冲输入流管道，从而提高字符输入流读字符数据的性能 |
|                                 |                                         |
| 按照行读取字符                         |                                         |
| 方法                              |                                         |
| public String readLine()        | 读取一行数据返回，如果没有数据可读了，会返回nu11              |

- 性能好，不乱码
##### 输出流

| 构造器                             |                                            |
| ------------------------------- | ------------------------------------------ |
| public BufferedWriter(Writer r) | 把低级的字符输出流包装成一个高级的缓冲字符输出流管道，从而提高字符输出流写数据的性能 |
|                                 |                                            |
| 换行                              |                                            |
| public void newLine()           | 换行                                         |

## 性能分析
- 字节数组快，桶加大能达到缓冲性能
- 桶最大32kb，过大占性能
### 其他流
#### 字符输入转换流--InputStreamReader
- 不同编码读取乱码问题
- 先获取文件原始字节流，按真实字符集编码转换成字符输入流

| 构造器                                                       |                                                |
| --------------------------------------------------------- | ---------------------------------------------- |
| public InputStreamReader(InputStream is)                  | 把原始的字节输入流，按照代码默认编码转成字符输入流（与直接用FileReader的效果一样） |
| public InputStreamReader(InputStream is , String charset) | 把原始的字节输入流，按照指定字符集编码转成字符输入流（重点）                 |

#### 打印流--PrintStream/PrintWriter
- 实现打印啥就是啥
- 方便、高效

| 构造器                                                                     |                      |
| ----------------------------------------------------------------------- | -------------------- |
| public PrintStream(OutputStream/File/String)                            | 打印流直接通向字节输出流/文件/文件路径 |
| public PrintStream(String fileName, Charset charset)                    | 可以指定写出去的字符编码         |
| public PrintStream(OutputStream out, boolean autoFlush)                 | 可以指定实现自动刷新           |
| public PrintStream(OutputStream out,boolean autoFlush, String encoding) | 可以指定实现自动刷新，并可指定字符的编码 |
|                                                                         |                      |
| 方法                                                                      |                      |
| public void println(Xxx xx)                                             | 打印任意类型的数据出去          |
| public void write(int/byte[]/byte[]—部分)                                 | 可以支持写字节数据出去          |

- 高级管道不能追加，需内包低级管道
#### 特殊数据流--DataInputStream
##### DataOutputStream
- 允许数据和类型一并写出

| 构造器                                                        |                             |
| ---------------------------------------------------------- | --------------------------- |
| public DataoutputStream(outputStream out)                  | 创建新数据输出流包装基础的字节输出流          |
|                                                            |                             |
| 方法                                                         |                             |
| public final void writeByte(int v) throws IoException      | 将byte类型的数据写入基础的字节输出流        |
| public final void writeInt(int v) throws IOException       | 将int类型的数据写入基础的字节输出流         |
| public final void writeDouble(Double v) throws IOException | 将double类型的数据写入基础的字节输出流      |
| public final void writeUTF(String str) throws IoException  | 将字符串数据以UTF-8编码成字节写入基础的字节输出流 |
| public void write(int/byte[]/byte[]—部分)                    | 支持写字节数据出去                   |

- 怎么写怎么读
## IO框架--commons-io
- 类、接口等编译成class形式，压缩成jar

| FileUtils类提供的部分方法展示                                                                           |       |
| --------------------------------------------------------------------------------------------- | ----- |
| public static void copyFile(File srcFile, File destFile)                                      | 复制文件 |
| public static void copyDirectory(File srcDir, File destDir)                                   | 复制文件夹 |
| public static void deleteDirectory(File directory)                                            | 删除文件夹 |
| public static String readFileTostring(File file, String encoding)                             | 读数据   |
| public static void writeStringToFile(File file, String data, String charname, boolean append) | 写数据   |
|                                                                                               |       |
| IOUtils类提供的部分方法展示                                                                             |       |
| public static int copy(InputStream inputStream, OutputStream outputStream)                    | 复制文件 |
| public static int copy(Reader reader, Writer writer)                                          | 复制文件 |
| public static void write(String data, OutputStream output, String charsetName)                | 写数据   |

# File
## 对象代表文件/文件夹，调用提供的方法对其操作

| 构造器                                      |                         |
| ---------------------------------------- | ----------------------- |
| public File(String pathname)             | 根据文件路径创建文件对象            |
| public File(String parent, String child) | 根据父路径和子路径名字创建文件对象       |
| public File(File parent, String child)   | 根据父路径对应文件对象和子路径名字创建文件对象 |
## 判断文件类型、获取文件信息功能

| 方法名称                            |                                 |
| ------------------------------- | ------------------------------- |
| public boolean exists()         | 判断当前文件对象，对应的文件路径是否存在，存在返回true   |
| public boolean isFile()         | 判断当前文件对象指代的是否是文件，是文件返回true，反之  |
| public boolean isDirectory()    | 判断当前文件对象指代的是否是文件夹，是文件夹返回true，反之 |
| public String getName()         | 获取文件的名称（包含后缀）                   |
| public long length()            | 获取文件的大小，返回字节个数                  |
| public long lastModified()      | 获取文件的最后修改时间                    |
| public String getPath()         | 获取创建文件对象时，使用的路径                 |
| public String getAbsolutePath() | 获取绝对路径                          |

- 相对路径：默认在IDEA工程里找
## file提供的创建和删除文件的方法

| File类创建文件的功能           |                      |
| ------------------------------ | -------------------- |
| public boolean createNewFile() | 创建一个新的空的文件 |
| public boolean mkdir()         | 只能创建一级文件夹   |
| public boolean mkdirs()        | 可以创建多级文件夹   |
| File类删除文件的功能           |                      |
| public boolean delete()        | 删除文件、空文件夹   |

- 只能删除空文件夹
## file提供的遍历文件夹的方法

| File类提供的遍历文件夹的功能 |                                                              |
| ---------------------------- | ------------------------------------------------------------ |
| public String[] list()       | 获取当前目录下所有的”一级文件名称“到一个字符串数组中去返回 |
| public File[] listFiles()    | 获取当前目录下所有的”一级文件名称“到一个字符串数组中去返回 |
## file提供的遍历文件夹的方法

| File类提供的遍历文件夹的功能          |                                     |
| ------------------------- | ----------------------------------- |
| public String[] list()    | 获取当前目录下所有的”一级文件名称“到一个字符串数组中去返回     |
| public File[] listFiles() | 获取当前目录下所有的”一级文件对象“到一个文件对象数组中去返回（重点） |

### 注意事项
- 主调是文件，或路径不存在时，返回null
- 主调是空文件夹时，返回长度为0的数组
- ==主调是有内容的文件夹时，将里面所有一级文件和文件夹的路径放在File数组中返回==
- 主调是文件夹，里面有隐藏文件时，将里面所有文件和文件夹的路径放在File数组中返回，包含隐藏文件
- 主调是一个文件夹，没有访问权限时，返回null
# 字符集
## 常见字符集
### ASCII
- 一个字节存，首位0
### GBK
- 中文字符编码成两个字节存储
- 汉字第一个字节第一位必须是1
- 16位
### Unicode
- 统一码，万国码
- 容纳所有文字、符号
- 32位
### UTF-8
- 编码方案，可变长
- 前缀码
	- 0
	- 110 10
	- 1110 10 10
	- 11110 10 10 10
## 编码、解码

| String方法                                 |                                        |
| ---------------------------------------- | -------------------------------------- |
| 字符编码                                     |                                        |
| byte[] getBytes()                        | 使用平台默认字符集将String编码为一系列字节，将结果存储到新的字节数组中 |
| byte[] getBytes(String charsetName)      | 使用指定的字符集将该String编码为一系列字节，将结果存储到新的字节数组中 |
|                                          |                                        |
| 字符解码                                     |                                        |
| String(byte[] bytes)                     | 通过使用平台的默认字符集解码指定的字节数组来构造新的String       |
| String(byte[] bytes, String charsetName) | 通过指定的字符集解码指定的字节数组来构造新的String           |

# 单元测试

- 针对最小的功能单元：方法，编写测试代码对其进行正确性测试
## Junit单元测试框架
- 第三方框架
优：
- 灵活编写测试代码
- 不需要分析测试结果，自动生成报告
### 步骤
1. 导包
2. 为需要测试的业务类，定义对应的测试类，为每个业务方法编写对应的测试方法（必须：公共、无参、无返回值）放在test/java目录下
3. ==测试方法必须声明@Test注解==，在测试方法中，编写代码调用被测试的业务方法进行测试
4. ==开始测试：==选中测试方法，右键选则“JUnit”，如果测试通过是绿色；测试失败是红色
### 断言
- 确定被测试方法是否按照预期的效果正常工作

| 方法                                                                   | 描述                       |
| -------------------------------------------------------------------- | ------------------------ |
| Assertions.assertEquals(Object exp, Object act, String msg)          | 检查两个值是否相等,不相等就报错         |
| Assertions.assertNotEquals(Object unexp, Object act, String msg)     | 检查两个值是否不相等,相等就报错         |
| Assertions.assertNull(Object act, String msg)                        | 检查对象是否为null,不为null,就报错   |
| Assertions.assertNotNull(Object act, String msg)                     | 检查对象是否不为null,为null,就报错   |
| Assertions.assertTrue(boolean condition, String msg)                 | 检查条件是否为true,不为true,就报错   |
| Assertions.assertFalse(boolean condition, String msg)                | 检查条件是否为false,不为false,就报错 |
| Assertions. assertThrows(Class expType, Executable exec, String msg) | 检查两个对象引用是否相等,不相等,就报错     |
msg表示错误提示信息，可以不指定
### 常见注解

| 注解                 | 说明                                  | 注                      |
| ------------------ | ----------------------------------- | ---------------------- |
| @Test              | 测试类中的方法用它修饰才能成为测试方法,才能启动执行          | 单元测试                   |
| @ParameterizedTest | 参数化测试的注解(可以让单个测试运行多次,每次运行时仅参数不同)    | ==用了该注解,就不需要aTest注解了== |
| @ValueSource       | 参数化测试的参数来源,赋予测试方法参数                 | 与参数化测试注解配合使用           |
| @DisplayName       | 指定测试类、测试方法显示的名称 (默认为类名、方法名)         |                        |
| @BeforeEach        | 用来修饰一个实例方法,该方法会在==每一个==测试方法执行之前执行一次 | 初始化资源(准备工作)            |
| @AfterEach         | 用来修饰一个实例方法,该方法会在==每一个==测试方法执行之后执行一次 | 释放资源(清理工作)             |
| @BeforeAll         | 用来修饰一个静态方法,该方法会在所有测试方法之前==只执行一次==   | 初始化资源(准备工作)            |
| @AfterAll          | 用来修饰一个静态方法,该方法会在所有测试方法之后==只执行一次==   | 释放资源(清理工作)             |
### 开发规范
- 原则：尽可能覆盖方法中所有出现的可能--边界值
# 反射
- 加载类，允许以编程的方式解剖类中的各种成分
1. 加载类，获取类的字节码：Class对象
	- 三种方式
	1. Class c1 = 类名.class
	2. 调用Class提供方法：public static Class forName(String package)
	3. Object提供的方法：public Class getClass();Class c3 = 对象.getClass();
2. 获取类的构造器：Construcror对象

| 方法                                                                |                                  |
| ----------------------------------------------------------------- | -------------------------------- |
| Constructor< ?>[] getconstructors()                               | 说明获取全部构造器（只能获取public修饰的）         |
| Constructor< ?>[] getDeclaredConstructors()                       | 获取全部构造器（只要存在就能拿到）                |
| Constructor<T> getconstructor(class< ?>... parameterTypes)        | 获取某个构造器（只能获取public修饰的）           |
| Constructor<T> getDeclaredconstructor（Class< ?>..：parameterTypes） | 获取某个构造器（只要存在就能拿到）                |
|                                                                   |                                  |
| 获取类构造器的作用：初始化对象返回                                                 |                                  |
| 获Constructor提供的方法                                                 |                                  |
| T newInstance(object... initargs)                                 | 调用此构造器对象表示的构造器，并传入参数，完成对象的初始化并返回 |
| public void  setAccessible(boolean flag)                          | 设置为true，表示禁止检查访问控制（暴力反射）         |

3. 获取类的成员变量：Field对象

| 方法                                                                 |                                  |
| ------------------------------------------------------------------ | -------------------------------- |
| Constructor< ?>[] getconstructors()                                | 获取全部构造器（只能获取public修饰的）           |
| Constructor< ?>[] getDeclaredconstructors()                        | 获取全部构造器（只要存在就能拿到）                |
| Constructor< T> getConstructor(Class< ?>... parameterTypes)        | 获取某个构造器（只能获取public修饰的）           |
| Constructor< T> getDeclaredConstructor（class< ?>..·parameterTypes） | 获取某个构造器（只要存在就能拿到）                |
|                                                                    |                                  |
| 获取类构造器的作用：依然是初始化对象返回                                               |                                  |
| Constructor提供的方法                                                   |                                  |
| T newInstance(object... initargs)                                  | 调用此构造器对象表示的构造器，并传入参数，完成对象的初始化并返回 |
| public void setAccessible(boolean flag)                            | 设置为true，表示禁止检查访问控制（暴力反射）         |

4. 获取类的成员方法：Method对象

| 方法                                                                 |                           |
| ------------------------------------------------------------------ | ------------------------- |
| Method[] getMethods()                                              | 获取类的全部成员方法（只能获取public修饰的） |
| Method[] getDeclaredMethods()                                      | 获取类的全部成员方法（只要存在就能拿到）      |
| Method getMethod(String name, Class< ?>...parameterTypes)          | 获取类的某个成员方法（只能获取public修饰的） |
| Method getDeclaredMethod(String name, Class< ?>... parameterTypes) | 获取类的某个成员方法（只要存在就能拿到）      |
|                                                                    |                           |
| 成员方法的作用：依然是执行                                                      |                           |
| Method提供的方法                                                        |                           |
| public Object invoke(object obj, object... args)                   | 触发某个对象的该方法执行              |
| public void setAccessible(boolean flag)                            | 设置为true，表示禁止检查访问控制（暴力反射）  |

```java
//获取类对象
Class c1 = Dog.class;
//获取构造器对象--创建对象
Constructor[] con = c1.getDeclaredConstructors();
	//暴力反射
	con.setAccessible(true);//绕过访问权限
	Object o1 = (强转)con.newInstance();
//获取成员变量--赋值和取值
Field[] fileds = c1.getDeclaredFields();
	//暴力反射
	fields.setAccessible(true);//绕过访问权限
	Object o1 = (强转)con.newInstance();
	field.set(o1,"");
//获取成员方法--调用方法
Method[] methods = c1.getDeclaredMethods();
	//暴力反射
	methods.setAccessible(true);//绕过访问权限
	methods.invoke();
```
## 作用
- 基本作用：获取一个类的全部成分然后操作
- 可以破坏封装性
- 绕过泛型约束
- 做框架
# 注解
- 特殊标记，让其他程序根据注解信息决定怎么执行
- 类、构造器、方法、成员变量、参数等
- 自定义注解
- 如果只有一个value值，使用时value可以不写
```java
public @interface 注解名称{
	public 属性类型 属性名() default 默认值;
	String value();
}
```
## 原理
- 本质接口继承注解类
- @注解(...);：实现类对象，实现注解及Annotation接口
## 源注解
- 注解注解的注解

| @Target                   | @Retention                          |
| ------------------------- | ----------------------------------- |
| 声明被修饰的注解只能在哪些位置使用         | 明注解的保留周期                            |
| @Target(ElementType.TYPE) | @Retention(RetentionPolicy.RUNTIME) |
| 1.TYPE，类，接口               | 1.SOURCE                            |
| 2.FIELD，成员变量              | 只作用在源码阶段，字节码文件中不存在                  |
| 3.METHOD，成员方法             | 2.CLASS（默认值）                        |
| 4.PARAMETER，方法参数          | 保留到字节码文件阶段，运行阶段不存在                  |
| 5.CONSTRUCTOR，构造器         | 3.RUNTIME（开发常用）                     |
| 6.LOCAL_VARIABLE，局部变量     | 一直保留到运行阶段                          |

## 解析注解
- 判断类上、方法上、成员变量是否存在注解，把注解里的内容解析出来
### 如何解析
- 要解析谁上的注解，就因该先拿到谁
- 解析类上的，就先获取到类的Class对象，再通过Class对象解析注解
- Class、Method、Filed、Constructor都有解析注解的能力

| AnnotatedElement接口提供了解析注解的方法                                           |                 |
| ---------------------------------------------------------------------- | --------------- |
| public Annotation[] getDeclaredAnnotations()                           | 获取当前对象上面的注解     |
| public T getDeclaredAnnotation(Class< T> annotationClass)              | 获取指定的注解对象       |
| public boolean isAnnotationPresent(Class< Annotation> annotationclass) | 判断当前对象上是否存在某个注解 |

## 作用、应用
- 注解控制执行，属性控制怎么执行
# 动态代理
- 设计模式
- 转移职责
- java.lang.reflect.Prosy类：提供为对象产生代理对象的方法
![[代理.png]]
```java
public static <T>(T obj){
	T proxy = (T)Proxy.newProxyInstance(ProxyUtil.class.getClassLoader(),obj.getClass().getInterfaces(),new InvocationHandler(){
		public Object invoke(Object proxy,Method method,Object[] args){
			//procy接受到代理对象本身
			//method被代表的方法
			//args被代理的方法的参数
			String methodName = method.getName();
			if(){
			}else{
			}
			Object result = method.invoke(s,args);
			return result;
		}
	});
	return porxy;
}
```
### 作用
- 函数经过代理统计时间，简化开发
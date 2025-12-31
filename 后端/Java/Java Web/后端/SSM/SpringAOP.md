- 面向切片/方面编程
- 在不改动原始设计的基础上进行功能增强
- 无入侵式/无侵入式
1. 引入依赖
2. 编写AOP程序：针对特定方法根据业务需要编程
- ==概念==
	- 连接点（JoinPoint）：程序执行过程中的任意位置，粒度为执行方法、抛出异常、设置变量，可以被AOP控制的方法(暗含方法执行时的相关信息)
		- 理解为方法的执行
	- 切入点（PointCut）：匹配连接点的条件，通知仅会在切入点方法执行时被应用
		- 一个切入点只描述一个具体的方法，或匹配多个方法
			- 一个具体的方法：com.example.dao包下的BookDao接口中的无形参无返回值的save方法
			- 匹配多个：所有的save方法，所有的get开头的方法，所有的以DA结尾的接口中的任意方法
	- 通知（Advice）：指那些重复的逻辑，也就是共性功能(最终体现为一个方法)
		- 切入点执行的操作
		- 功能最终以方法的形式呈现
	- 通知类：定义通知的类
	- 切面（Aspect）：描述通知与切入点的对应关系(通知+切入点)
	- 目标对象（Target）：通知所应用的对象

```java
1.导入aop坐标
<dependency>
	<groupId>org.aspectj</groupId>
	<artifactId>aspectjweaver</artifactId>
	<version>1.9.4</version>
</dependency>

2.定义接口与实现类
public interface BookDao {
	public void save();
	public void update();
}

@Repository
public class BookDaoImpl implements BookDao {
	public void save() {
		System.out.println(System.currentTimeMillis());
		System.out.println("book dao save ... ");
	}
	public void update(){
		System.out.println("book dao update ... ");
	}
}

3.定义通知类
public class MyAdvice {
	public void before(){
		System.out.println(System.currentTimeMillis());
	}
}

4.定义切入点
public class MyAdvice {
	@Pointcut("execution(void com.itheima.dao.BookDao.update())")
	private void pt(){}//啥也不写
}
//切入点定义依托于不具有事件意义的方法执行，无参，无返回值，方法体无实际逻辑

5.绑定切入点与通知关系，指定通知添加到原始连接点的具体执行位置
public class MyAdvice {
	@Pointcut("execution(void com.itheima.dao.BookDao.update())")
	private void pt(){}
	@Before("pt()")//
	public void before(){
		System.out.println(System.currentTimeMillis());
	}
}

6.定义通知类收受Spring容器管理，并定义当前类为切面类
@Component
@Aspect
public class MyAdvice {
	@Pointcut("execution(void com.itheima.dao.BookDao.update())")
	private void pt(){}
	@Before("pt()")
	public void before(){
		System.out.println(System.currentTimeMillis());
	}
}

7.开启Spring对AOP注解驱动支持
@Configuration
@ComponentScan("com.itheima")
@EnableApectJAutoProxy
public class SpringConfig {
}
```
## 工作流程
1. Spring容器启动
2. 读取所有切片配置中的切入点
3. 初始化bean，判定bean对应的类中的方法是否匹配到任意切入点
	- 失败，创建对象
	- 成功，创建原始对象（==目标对象==）的==代理==对象
4. 获取bean执行方法
	- 获取bean，调用方法并执行，完成
	- 获取bean的代理对象，根据代理对象的运行模式运行原始方法与增强内容，完成操作
- 目标对象：原始功能去掉共性功能对应的类产生的对象，无法完成最终功能
- 代理：目标对象无法直接完成工作，需对其进行功能回填，通过原始对象的代理对象实现
## 通知类型
1. ==@Around==：环绕通知，此注解标注的通知方法在目标方法前、后都被执行
	- 需要对原始操作调用：ProceedingJoinPoint：程序连接点
	- 有返回值设置成Object
2. @Before：前置通知，此注解标注的通知方法在目标方法前被执行
3. @After：后置通知，此注解标注的通知方法在目标方法后被执行，无论是否有异常都会执行
4. @AfterReturning：返回后通知，此注解标注的通知方法在目标方法后被执行，有异常不会执行
5. @AfterThrowing ：异常后通知，此注解标注的通知方法发生异常后执行
- @PointCut 
	- 将公共的切点表达式抽取出来，需要时引用
## 顺序
- 不同切面类中,默认按照切面类的类名字母排序:
	- 目标方法前的通知方法:字母排名靠前的先执行
	- 目标方法后的通知方法:字母排名靠前的后执行
- @Order(数字)控制顺序
	- 数字小的先执行
## 切入点表达式
- 描述切入点方法的一种表达式
- 决定项目中的那些方法需要加入通知
### 常见形式:
1. execution( ...... ):根据方法的签名来匹配
	- 根据方法的返回值、包名、类名、方法名、方法参数等匹配
	- execution(访问修饰符? 返回值 包名.类名.?方法名(方法参数) throws 异常?)
	- ？可省
		1. 访问修饰符
		2. 包名.类名
		3. throws 异常：方法上声明抛出的异常，不是实际抛出的异常
	- 通配符
```java
1 *: 单个独立的任意符号,可以通配任意返回值、包名、类名、方法名、任意类型的一个参数,也可以通配包、类、方法名的一部分
execution(* com. *. service .*. updat*))
2 ..: 多个连续的任意符号,简化包名与参数,可以通配任意层级的包,或任意类型、任意个数的参数
execution(* com.itheima .. DeptService .* ( .. ))
3.+：专用于匹配子类类型
exexution(* *..*Service.*(..))
```
2. @annotation( ...... ):根据注解匹配
	- 匹配有特定注解的方法（自定义注解）
## 连接点
- 在Spring中用JoinPoint抽象了连接点,用它可以获得方法执行时的相关信息,如目标类名、方法名、方法参数等
	- 对于 @Around 通知,获取连接点信息只能使用 ProceedingJoinPoint
	- 对于其它四种通知,获取连接点信息只能使用 JoinPoint,它是 ProceedingJoinPoint 的父类型
## AOP通知获取数据
### 获取参数
- JoinPoint：适用于前置、后置、返回后、抛出异常后通知
- ProceedJoinPoint：环绕通知
### 获取返回值
- 返回后通知
- 环绕通知
### 获取异常
- 抛出异常后通知
- 环绕通知

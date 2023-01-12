# Lambda表达式

简介： lambda表达式是JDK8.0的新特性，是一个匿名函数，用于简化开发

优劣势: 普通方式会出现代码冗余 ，但Lambda表达式阅读能力相对7.0及之前较低

Demo1 创建线程案例

```java
public static void main(String[] args) {
	/*
	 * 匿名内部类存在的问题 (代码冗余)
	 * 匿名内部类做了哪些事情
	 * 1 定义了一个没有名字的类并实现了Runnable接口
	 * 2 创建了这个类的对象
	 * 我们的核心其实是Runnable接口中的run方法
	 * 因此 前边的这些就显得冗余了 但易读
	 */
	new Thread(new Runnable() {

		@Override
		public void run() {
			System.out.println("匿名内部类方式创建线程");
		}
	}).start();
	/*
	 * Lambda表达式体现的是函数式编程思想  只需要将要执行的代码放到函数中即可(函数就是方法)
	 * Lambda是一个匿名函数
	 * 使用Lambda表达式可以简化匿名内部类 让代码变得更加精简 但不易读
	 */
	new Thread(()->{
		System.out.println("Lambda方式创建线程");
	}).start();
}
```

## Lambda语法格式

### Lambda使用注意事项

Lambda只能用于函数式接口，普通接口无法使用Lambda表达式

函数式接口规范是有且仅有一个抽象方法 建议在接口上添加@FunctionalInterface进行代码的语法检查

### 无参 无返回值

```
() -> {}
() 参数列表
{} 方法体
-> 箭头操作符 连接参数列表与方法体
```

### demo

定义接口

```java
@FunctionalInterface
public interface Swimmable {
	void swimming();
}
```

测试无参无返回值

```java
public class Lambda_02 {

	public static void main(String[] args) {
		// 匿名内部类方式实现swimming方法
		goSwimming(new Swimmable() {
			@Override
			public void swimming() {
				System.out.println("匿名内部类方式实现swimming方法");
			}
		});
		
		// lambda表达式方式实现swimming方法
		// 如果方法的参数为接口参数时 就可以考虑使用Lambda方式实现 需要注意 接口必须是函数式接口
		// 函数式接口有且仅有一个抽象方法
		goSwimming(()->{
			System.out.println("Lambda表达式方式实现swimming方法");
		});
		
	}
	
	// 无参无返回值Lambda
	public static void goSwimming(Swimmable s) {
		s.swimming();
	}
}
```

### 无参 有返回值

demo1

使用Lambda表达式时 参数类型可以忽略不写

```java
public class Lambda_03 {

	public static void main(String[] args) {
		
		// 匿名内部类方式
		rabbitRun(new Car() {
			@Override
			public void run(String carName) {
				System.out.println("匿名内部类方式 :"+carName);
			}
		});
		
		// lambda表达式的参数类型可以忽略不写 实际的参数类型就是接口中需要传入的参数类型
		rabbitRun((s) -> {
			System.out.println("Lambda表达式方式 "+s);
		});
	}
	
	public static void rabbitRun(Car c) {
		c.run("Benz");
	}
}

@FunctionalInterface
interface Car{
	void run(String carName);
}
```

demo2 遍历集合

```java
public class Lambda_04 {

	public static void main(String[] args) {
		List<User> list = new ArrayList<>();
		list.add(new User("Tom",12,22000.00));
		list.add(new User("Jerry",13,23000.00));
		list.add(new User("Andy",15,500000.00));
		// sort 排序
		// 匿名内部类方式
		//Collections.sort(list, new Comparator<User>() {
		//	@Override
		//	public int compare(User o1, User o2) {
		//		return o2.age-o1.age; // 按年龄降序
		//	}
		//});
		
		
	//	Collections.sort(list,(o1,o2) -> {
	//		return o1.age - o2.age; // 按年龄升序
	//	});
		
		// 进一步简化 
		// 有且仅有一条返回语句时 方法体的{}可以省略 如果是return时 return语句也必须省略
		Collections.sort(list, (o1,o2) -> o1.age - o2.age);// 按年龄升序

		for (User user : list) {
			System.out.println(user);
		}
	}
}

class User{
	String name;
	int age;
	double sal;
	
	public User(String name,int age,double sal) {
		this.name = name;
		this.age = age;
		this.sal = sal;
	}
	@Override
	public String toString() {
		return "User [name=" + name + ", age=" + age + ", sal=" + sal + "]";
	}
}
```

### 有参 有返回值

```java
public class Lambda_06 {

	public static void main(String[] args) {
		
		cal(new Calc2() {
			@Override
			public int math(int a, int b) {
				return a*b;
			}
		});
		
		// Lambda表达式
		// 参数 接口中math方法的参数
		// 返回值 接口中math方法的返回值 可根据类型自行定义
		cal((a,b) -> 5*3);
		
	}
	
	public static int cal(Calc2 c) {
		int res = c.math(3, 2);
		System.out.println("返回值 "+res);
		return res;
	}
}

@FunctionalInterface
interface Calc2{
	int math(int a,int b);
}
```

### 小结

```java
Lambda表达式 是匿名函数 用来解决代码冗余问题，但只能解决函数式接口 也就是说Lambda是基于函数式接口编程的
Lambda表达式相当于重写函数式接口中的方法 关注点在方法的参数列表及返回值上
Lambda表达式格式如下
1. 无参无返回值
() -> {} 
() 表示参数列表
{} 表示方法体
-> 表示连接参数列表与方法体
简化写法
()-> 1+1 ; 如果方法体中有且仅有一条返回语句时 {}可以省略 return语句必须省略
2. 有参有返回值
(a,b) -> {//...}
参数列表中的可以省略参数类型 其实际类型与函数式接口中的方法参数列表中参数类型一致
参数列表中可自定义参数名称
如果仅有一条返回语句{}可以忽略 同时return语句不需要再写
例如 集合排序sort(list,comparator<T>) 中comparator接口的compare方法需要两个对象参数


public class Demo2 {

	public static void main(String[] args) {
		List<Student> list = new ArrayList<>();
		list.add(new Student("Tom",12));
		list.add(new Student("Jerry",13));
		list.add(new Student("Lily",12));
		/*
		Collections.sort(list, (o1,o2) -> o1.age - o2.age);
		相当于下面的实现方式
		Collections.sort(list,new Comparator<Student>() {
			@Override
			public int compare(Student o1, Student o2) {
				return o1.age-o2.age;
			}
		});
		*/
		Collections.sort(list, (o1,o2) -> o1.age - o2.age);
		
		for (Student student : list) {
			System.out.println(student.age);
		}
	}
}

class Student{
	String name;
	int age;
	public Student(String name,int age) {
		this.name = name;
		this.age = age;
	}
}
```

## Lambda运行原理

Lambda底层还是将表达式转成一个匿名内部类方式来运行的

### 匿名内部类运行原理刨析

源码

```java
public class Demo3 {

	public static void main(String[] args) {
		swimming(new Swimmable() {
			@Override
			public void swimming() {
				System.out.println("匿名内部类方式实现swimming方法");
			}
		});
	}
	
	public static void swimming(Swimmable s) {
		s.swimming();
	}
}
```

class字节码

![image](https://user-images.githubusercontent.com/47961027/212056525-f38809f3-d916-44aa-a6b6-867408c6bc39.png)

![image](https://user-images.githubusercontent.com/47961027/212056415-001d0829-3f7d-4ef5-b1b2-19e7e7375d0c.png)

### Lambda运行原理刨析

源码

```java
public class Demo3 {

	public static void main(String[] args) {
		swimming(()->{
			System.out.println("Lambda表达式方式实现swimming方法");
		});
	}
	
	public static void swimming(Swimmable s) {
		s.swimming();
	}
}
```

class字节码

会发现lambda表达式并没有生成匿名内部类的字节码文件

![image-20230112194044776](C:\Users\etjav\AppData\Roaming\Typora\typora-user-images\image-20230112194044776.png)

没有生成匿名内部类的字节码文件 可以使用javap指令查看字节码文件

```
javap -c -p Demo3.class
-c 表示对代码进行反汇编
-p 显示所有的类和成员
```

反编译后如下

```java
C:\Users\etjav\Desktop\springcloud\JDK8\bin\com\etjava\test>javap -c -p Demo3.class
Compiled from "Demo3.java"
public class com.etjava.test.Demo3 {
  public com.etjava.test.Demo3();
    Code:
       0: aload_0
       1: invokespecial #8                  // Method java/lang/Object."<init>":()V
       4: return

  public static void main(java.lang.String[]);
    Code:
       0: invokedynamic #16,  0             // InvokeDynamic #0:swimming:()Lcom/etjava/lambda/Swimmable;
       5: invokestatic  #20                 // Method swimming:(Lcom/etjava/lambda/Swimmable;)V
       8: return

  public static void swimming(com.etjava.lambda.Swimmable);
    Code:
       0: aload_0
       1: invokeinterface #25,  1           // InterfaceMethod com/etjava/lambda/Swimmable.swimming:()V
       6: return
  // 自动生成了一个lambda$0 私有静态方法
  private static void lambda$0();
    Code:
       0: getstatic     #32                 // Field java/lang/System.out:Ljava/io/PrintStream;
       3: ldc           #38                 // String Lambda表达式方式实现swimming方法
       5: invokevirtual #40                 // Method java/io/PrintStream.println:(Ljava/lang/String;)V
       8: return
}
```

private static void lambda$0(); 什么时候被调用？

Lambda在运行时会生成一个内部类

可通过java -Djdk.internal.lambda.dumpProxyClasses 将运行时生成的内部类class字节码输出到指定的文件中

```
java -Djdk.internal.lambda.dumpProxyClasses 包名.类名
```

注意： 运行时需要退出到根目录下进行运行

![image-20230112195415133](C:\Users\etjav\AppData\Roaming\Typora\typora-user-images\image-20230112195415133.png)

运行后会发现生成了一个匿名内部类

![image-20230112195457082](C:\Users\etjav\AppData\Roaming\Typora\typora-user-images\image-20230112195457082.png)

```java
C:\Users\etjav\Desktop\springcloud\JDK8\bin\com\etjava\test>javap -c -p Demo3$$Lambda$1.class
// lambda在运行时会生成yi
final class com.etjava.test.Demo3$$Lambda$1 implements com.etjava.lambda.Swimmable {
  private com.etjava.test.Demo3$$Lambda$1();
    Code:
       0: aload_0
       1: invokespecial #10                 // Method java/lang/Object."<init>":()V
       4: return

  public void swimming();
    Code:
       0: invokestatic  #17                 // Method com/etjava/test/Demo3.lambda$0:()V
       3: return
}
```




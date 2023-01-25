# Optional类使用

## 介绍

Optional类是JDK8提供的新特性，用来解决处理null问题

## 基本使用

创建对象 of ，ofNullable ，empty三种方式

判断是否为空 isPresent

获取Optional中值 get 需要注意 如果为空不能获取 会抛出未找到元素异常

```java
public class Demo1 {

	public static void main(String[] args) {
		// 1. 创建Optional对象方式
		// of方法只能传入非null参数
		Optional<Object> op1 = Optional.of(12);
		// ofNullable方法可以传入空值也可以传入具体值
		Optional<Object> op2 = Optional.ofNullable(null);
		Optional<String> op3 = Optional.ofNullable("aa");
		//	empty 存入的是null
		Optional<Object> empty = Optional.empty();
		
		// 2. isPresent 判断Optional中是否有值 存在值返回true
		boolean f = op1.isPresent();
		System.out.println(f);
		
		// 3. 获取Optional中的值
		// 注意 get方法获取Optional中值的时候 如果没有值会抛出NoSuchElementException异常
		Object o = op3.get();
		System.out.println(o);
		
		// 4. 通过get与isPersent组合使用
		if(op2.isPresent()) {
			System.out.println(op2.get());
		}else {
			System.out.println("op2 is null");
		}
	}
}
```

## 高级使用

orElse 如果Optional中有值就取出Optional中的值 如果没有值就使用orElse指定的值

ifPresent 如果存在值就调用参数

ifPersentOrElse JDK9提供的 操作类似if else 相比要简单很多

```java
public class Demo2 {

	public static void main(String[] args) {
		Optional<String> op1 = Optional.of("AAA");
//		Optional<String> op1 = Optional.empty();
		
		// orElse 如果Optional中有值就取出Optional中的值 如果没有值就使用orElse指定的值
		String name = op1.orElse("DDD");
		System.out.println(name);
		
		// ifPresent 如果存在值就调用参数
		op1.ifPresent(s ->{
			System.out.println("有值 "+s);
		});
		
		User u = new User("Tom",12);
//		System.out.println(nameToUpperCase1(u));
		Optional<User> op = Optional.of(u);
		System.out.println(nameToUpperCase2(op));
		
	}
	
	// Optional方式将用户名转成大写
	private static String nameToUpperCase2(Optional<User> op) {
		String name = op.map(u ->u.name)
			.map(n -> n.toUpperCase())
			.orElse("用户名为空");
		return name;
	}
	
	// 普通方式将用户名转成大写
	private static String nameToUpperCase1(User u) {
		if(u!=null) {
			String name = u.name;
			if(name!=null) {
				return name.toUpperCase();
			}
		}
		return null;
	}
}

class User{
	String name;
	int age;
	public User(String name,int age) {
		this.name = name;
		this.age = age;
	}
	@Override
	public String toString() {
		return "User [name=" + name + ", age=" + age + "]";
	}
}
```


# Stream流

## 介绍

Stream流是JDK8提供用来循环遍历集合使用的，使用Stream流方式遍历集合中的数据会更加简便

注意：Stream流与IO流没有任何关系

## 遍历List集合

传统方式遍历与使用Stream流方式遍历

```java
public class Demo0 {

	public static void main(String[] args) {
		ArrayList<String> list = new ArrayList<>();
		Collections.addAll(list, "张三","张无忌","张翠山","张三丰","周芷若","赵敏");
		// 需求 遍历list 获取名字以张开头且长度为3个字的
		// 获取名字以张开头并且长度为3个字的
		List<String> zhangList = new ArrayList<>();
		for (String name : list) {
			if(name.startsWith("张") && name.length()==3) {
				zhangList.add(name);
			}
		}
		// 打印集合
		for(String name:zhangList) {
			System.out.println(name);
		}
		
		System.out.println("-----------");
		
		list.stream()
				.filter(s ->{
					return s.startsWith("张") && s.length()==3;
				})
				.forEach(s -> System.out.println(s));
	}
}
```

## 获取Stream流方式

获取stream流常用方式有两种

通过Collection接口中的默认方法获取stream流  default Stream<E> stream()

通过Stream接口中的静态方法of 来获取stream流，注意 该方式不适应基本数据类型

```java

public class Demo1 {

	public static void main(String[] args) {
		// 通过Collection方式获取stream流  Collection接口中有一个默认方法 default Stream<E> stream()
		// List
		List<String> list = new ArrayList<>();
		Stream<String> stream = list.stream();
		
		// set
		Set<String> set = new HashSet<>();
		Stream<String> stream2 = set.stream();
		// Map接口不能直接获取stream
		// 但Map接口中的Keyset方法会返回Set结合 Values会返回Collection集合 EntrySet也会返回Set集合
		Map<String,Object> map = new HashMap<>();
		Stream<Entry<String, Object>> stream3 = map.entrySet().stream();
		Stream<String> stream4 = map.keySet().stream();
		Stream<Object> stream5 = map.values().stream();
		
		// 通过Stream静态方法of获取stream流
		// public static<T> Stream<T> stream(T... values)
		Stream<String> stream6 = Stream.of("aa","bb","cc");
		
		String[] arr = {"aa","bb"};
		Stream<String> stream7 = Stream.of(arr);
		
		// 基本数据类型的数组不可以使用Stream.of方法获取stream流 因为会将整个数组看作一个元素进行操作 而不是数组中的元素内容
		int[] data = {1,2,3,4,5};
		Stream<int[]> stream8 = Stream.of(data);
	}
}
```

## Stream流中的常用方法和注意事项

### Stream常用API

| 方法名  | 作用           | 返回值类型 | 方法种类   |
| ------- | -------------- | ---------- | ---------- |
| count   | 统计元素个数   | long       | 终结       |
| forEach | 遍历集合中元素 | void       | 终结       |
| filter  | 过滤条件       | Stream     | 非终结方法 |
| limit   | 获取前几个元素 | Stream     | 非终结方法 |
| skip    | 跳过前几个元素 | Stream     | 非终结方法 |
| map     | 映射           | Stream     | 非终结方法 |
| concat  | 组合           | Stream     | 非终结方法 |

终结方法：返回值类型不在是Stream流 不可在进行链式调用

非终结方法：返回的还是Stream流 可以继续进行链式调用

### 使用Stream接口获取流的注意事项

Stream只能操作一次

Stream方法返回的是新的流

Stream不调用终结方法 中间的操作不会执行

```java
public class Demo2 {

	public static void main(String[] args) {
		Stream<String> stream = Stream.of("aa","bb");
		// 1 stream中的方法只能操作一次
		// stream has already been operated upon or closed
		//long count = stream.count();
		//long count2 = stream.count();
		
		// 2 Stream方法返回的是新的流
		//Stream<String> limit = stream.limit(1);
		//System.out.println(stream==limit);
		
		// 3 stream不调用终结方法之前 中间的操作不会执行
		stream.filter(s ->{
			System.out.println(s);
			return true;
		}).count();// 返回非Stream类型即为终结方法
	}
}
```

### forEach方法

foreach方法是用来遍历集合 

void forEach 

```java
public class Demo3 {

	public static void main(String[] args) {
		List<String> list = new ArrayList<>();
		Collections.addAll(list, "aa","bb","cc");
		// 可以使用lambda表达式
		list.stream().forEach(s -> System.out.println(s));
		// 方法引用
		list.stream().forEach(System.out::println);
	}
}
```

### count方法

count方法是用来统计元素个数

long count

```java
public class Demo4 {

	public static void main(String[] args) {
		List<String> list = new ArrayList<>();
		Collections.addAll(list, "aa","bb","cc");
		
		long count = list.stream().count();
		System.out.println(count);
		long count2 = list.stream().count();
		System.out.println(count2);
	}
}
```

### filter方法

filter方法用来过滤数据 返回符合条件的元素

```java
public class Demo5 {

	public static void main(String[] args) {
		List<String> list = new ArrayList<>();
		Collections.addAll(list, "汪精卫","袁世凯","老头子","孙中山","xx");
		// 获取长度为3个字符的元素
		list.stream().filter(s->{
			return s.length()==3;
		}).forEach(System.out::println);
		// 简化写法
		list.stream().filter(s -> s.length()==3).forEach(System.out::println);
	}
}
```

### limit方法

截取结合中前N个元素

Stream limit(int index)

```java
public class Demo6 {

	public static void main(String[] args) {
		List<String> list = new ArrayList<>();
		Collections.addAll(list, "汪精卫","袁世凯","老头子","孙中山","xx");
		limit(list);
	}
	
	private static void limit(List<String> list) {
		list.stream().limit(3).forEach(System.out::println);
		// 带过滤条件的
		//list.stream().filter(s -> s.length()==3).limit(3).forEach(System.out::println);
	}
}
```

### skip方法

跳过前N个元素

Stream skip(int index)

```java
public class Demo7 {

	public static void main(String[] args) {
		List<String> list = new ArrayList<>();
		Collections.addAll(list, "汪精卫","袁世凯","老头子","孙中山","xx");
		// skip 从1开始
		list.stream().skip(2).forEach(System.out::println);
	}
}
```

### map方法

把一种类型的流映射成另一种类型的流

Stream map

```
public class Demo8 {

	public static void main(String[] args) {
		List<String> list = new ArrayList<>();
		Collections.addAll(list, "1","2","3");
		// map方法可以将一种类型的流转换成另一种类型的流
		// Stream<Integer> stream = list.stream().map(s -> Integer.parseInt(s));
		// stream.forEach(System.out::println);
		
		list.stream().map(s -> Integer.parseInt(s)).forEach(System.out::println);
	}
}
```

### sorted方法

sorted方法用来对集合进行排序

Stream<T> stored()

Stream<T> stored(Comparator<? super T> comparator)

```java
public class Demo9 {

	public static void main(String[] args) {
		List<Integer> list = new ArrayList<>();
		Collections.addAll(list, 3,2,4,5,1,6);
		// 默认 升序排序
		list.stream().sorted().forEach(System.out::println);
		System.out.println("-------------------");
		// 降序排序
		list.stream().sorted((o1,o2) -> o2-o1).forEach(System.out::println);
	}
}
```

### distinct方法

distinct方法用来对元素进行去重操作

Stream<T> distinct()

```java
public class Demo10 {

	public static void main(String[] args) {
		Stream<Integer> stream = Stream.of(1,1,2,2,3,3,4,5,6);
		stream.distinct().forEach(System.out::println);
		
		Stream<String> stream2 = Stream.of("aa","dd","aa","dd");
		stream2.distinct().forEach(System.out::println);
		
		// 自定义类型去重 需要重写equals和hashCode方法
		List<User> list = new ArrayList<>();
		Collections.addAll(list, 
				new User("Tom",12),
				new User("Jerry",13),
				new User("Jerry",13),
				new User("Lily",15));
		list.stream().distinct().forEach(System.out::println);
	}
}

class User{
	String name;
	int age;
	public User() {}
	public User(String name,int age) {
		this.name = name;
		this.age = age;
	}
	@Override
	public String toString() {
		return "User [name=" + name + ", age=" + age + "]";
	}
	@Override
	public int hashCode() {
		int res = name != null?name.hashCode():0;
		res = 31*res+age;
		return res;
	}
	@Override
	public boolean equals(Object obj) {
		if(this == obj) return true;
		if(obj == null || getClass() != obj.getClass()) return false;
		User u = (User) obj;
		if(age!=u.age) return false;
		return name!=null ? name.equals(u.name) : u.name==null;
	}
}
```

### match方法

match方法是用来判断是否匹配指定条件的数据

boolean allMatch(Predicate<? super T> perdicate); 全部匹配返回true 若有不匹配的元素则返回false

boolean anyMatch(Predicate<? super T> perdicate); 只要有一个元素匹配则返回true

boolean noneMatch(Predicate<? super T> perdicate); 所有元素都不匹配返回true

```java
public class Demo11 {

	public static void main(String[] args) {
		List<Integer> list = new ArrayList<>();
		list.add(1);
		list.add(2);
		list.add(3);
		// allMatch 全部满足返回true 否则false
		boolean f = list.stream().allMatch(e -> e>2);
		System.out.println(f);
		
		// anyMatch 只要有一个匹配则返回true
		boolean f2 = list.stream().anyMatch(e -> e>2);
		System.out.println(f2);
		
		// noneMatch 没有匹配的返回true
		boolean f3 = list.stream().noneMatch(e -> e>2);
		System.out.println(f3);
	}
}
```

### find方法

find方法用来查找集合中第一个元素

Optional<T> findFirst(); // 返回集合中第一个元素

Optional<T> findAny(); // 返回集合中第一个元素

```java
public class Demo12 {

	public static void main(String[] args) {
		List<Integer> list = new ArrayList<>();
		list.add(1);
		list.add(2);
		list.add(3);
		
		Integer i = list.stream().findFirst().get();
		System.out.println(i);
		
		Integer i2 = list.stream().findAny().get();
		System.out.println(i2);
	}
}
```

### max和min方法

获取集合中最大值和最小值

注意 在使用max或min时 必须指定元素的比较规则(比较器) 最大值就是获取排序后的最后一个元素 最小值就是获取排序后的第一个值

Optional<T> max(Compatator<? super T> comparator); 获取最大值

Optional<T> min(Compatator<? super T> comparator); 获取最小值

```
public class Demo13 {

	public static void main(String[] args) {
		List<Integer> list = new ArrayList<>();
		list.add(1);
		list.add(2);
		list.add(3);
		
		Optional<Integer> max = list.stream().max((o1,o2)->o1-o2);
		System.out.println(max.get());
		
		Optional<Integer> min = list.stream().min((o1,o2)->o1-o2);
		System.out.println(min.get());
	}
}
```

### reduce方法

reduce方法用来归纳集合中元素 例如 将集合中元素全部加起来

T reduce(T identity, BinaryOperator<T> accumulator);

```java
public class Demo14 {

	public static void main(String[] args) {
		List<Integer> list = new ArrayList<>();
		Collections.addAll(list, 1,2,3,4);
		
		// T reduce(T identity,BinaryOperator<T> accumulator)
		// T identity 设置默认值
		// BinaryOperator<T> accumulator 对数据进行处理的方式
		// reduce方法执行顺序
		// 第一次将默认值赋值给x 然后将第一个元素赋值给y 后边都是将上一次的结果赋值给x 然后将下一个元素赋值给y
		// 第一次 将默认值赋值给x 将第一个元素赋值给y
		// 第二次 将第一次得到的结果赋值给x 将下一个元素赋值给y
		// 第三次 将第二次得到的结果赋值给x 将下一个元素赋值给y
		// 第四次 将第三次得到的结果赋值给x 将下一个元素赋值给y
		// list.stream().reduce(0,(x,y)->x+y);
		Integer i = list.stream().reduce(0,(x,y)->{
			System.out.println("x="+y+",y="+y);
			return x+y;
		});
		System.out.println(i);
		
		// reduce还可以用来获取集合中最大或最小元素
		// list.stream().reduce(0,(x,y)-> x > y ? x : y);
		Integer max = list.stream().reduce(0,(x,y)->{
			return  x > y ? x : y;
		});// 获取集合中最大值
		System.out.println(max);
		// 获取集合中最大值
		Optional<Integer> max2 = list.stream().max((o1,o2) -> o1-o2);
		System.out.println(max2.get());
	}
}
```

### map和reduce组合使用

map方法可以将一个类型的数据映射成另一个类型的数据

reduce方法是用来归纳总结结合中元素

```java
public class Demo15 {

	public static void main(String[] args) {
		List<Person> list = new ArrayList<>();
		Collections.addAll(list, 
				new Person("Tom",12),
				new Person("Jerry",13),
				new Person("Andy",14),
				new Person("Judy",15),
				new Person("Jack",16));
		
		// 求出所有人的年龄总和
		Integer total = list.stream()
			.map(p -> p.age) // 类型转换 只映射年龄
			//.reduce(0,(x,y)->x+y); // 归纳所有年龄总和
			.reduce(0,Integer::sum); // 方法引用方式
		
		System.out.println(total);
		// 找出最大年龄的人
		Integer maxAge = list.stream()
			.map(p -> p.age)
			// .reduce(0,(x,y)-> x>y ? x:y);
			.reduce(0,Math::max); // 方法引用方式 
	
		System.out.println(maxAge);
		
		// 统计a出现的次数
		Integer totalA = Stream.of("a","c","d","a","e")
				.map(s -> s == "a"?1:0) // 类型转换 
				//.reduce(0,(x,y) -> x+y);
				.reduce(0,Integer::sum);// 方法引用
		
		System.out.println(totalA);
	}
}

class Person{
	String name;
	int age;
	public Person(String name,int age) {
		this.name = name;
		this.age = age;
	}
	@Override
	public String toString() {
		return "Person [name=" + name + ", age=" + age + "]";
	}
}
```

### mapToInt方法

将集合中元素转换成基本数据类型int

int是基本数据类型 占用内存较Integer少

注意 转成成的是基本数据类型 非包装类类型

IntStream mapToInt(ToIntFunction<? super T> mapper);

```java
public class Demo16 {

	public static void main(String[] args) {
		List<String> list = new ArrayList<>();
		Collections.addAll(list, "1","22","33");
		
		Stream<Integer> stream = list.stream().map(e -> Integer.parseInt(e));
		//stream.mapToInt(n -> n.intValue())
		//		.filter(e -> e>22)
		//		.forEach(System.out::println);
		// 简化写法
		// IntStream mapToInt(ToIntFunction<? super T> mapper);
		// IntStream底层操作的是int类型 可以节省内存 减少自动装箱和拆箱
		stream.mapToInt(Integer::intValue).filter(e -> e>22).forEach(System.out::println);
	}
}
```

### concat方法

合并两个stream流(合并集合)

concat方法是Stream接口中的静态方法

注意 Stream接口中的concat方法 只能将两个流进行合并 不能直接合并三个或以上的流

如果需要合并多个 需要两两合并后在进行合并操作

```java
public class Demo17 {

	public static void main(String[] args) {
		List<String> list1 = new ArrayList<>();
		List<String> list2 = new ArrayList<>();
		
		Collections.addAll(list1, "aa","bb","cc");
		Collections.addAll(list2, "dd","ee","cc");
		
		Stream<String> concat = Stream.concat(list1.stream(), list2.stream());
		concat.forEach(System.out::println);
        // 注意 如果使用Stream接口中的of方法 在合并流操作之后 不能再次操作这个stream流了
		list1.stream().forEach(System.out::println);
	}
}
```

### 综合案例

```java
public class Demo18 {

	public static void main(String[] args) {
		List<String> list1 = new ArrayList<>();
		Collections.addAll(list1, "郑板桥","宋远桥","苏星河","洪七公","赵敏","紫薇");
		List<String> list2 = new ArrayList<>();
		Collections.addAll(list2, "刘备","赵子龙","关云长","张飞","张三","张xxx");
		
		// 1 list1中只要名字为3个字符的
		list1.stream().filter(s -> s.length()==3).forEach(System.out::println);
		System.out.println("----------------");
		// 2 list1中 筛选之后只要前三个人
		list1.stream().filter(s -> s.length()==3).limit(3).forEach(System.out::println);
		System.out.println("----------------");
		// 3 list2中只要张姓的成员
		list2.stream().filter(s -> s.startsWith("张")).forEach(System.out::println);
		System.out.println("----------------");
		// 4 list中筛选之后不要前两个人
		list2.stream().filter(s -> s.startsWith("张")).skip(2).forEach(System.out::println);
		System.out.println("----------------");
		// 5 将两个集合合并为一个
		Stream<String> streamA = list1.stream().filter(s -> s.length()==3).limit(3);
		Stream<String> streamB = list2.stream().filter(s -> s.startsWith("张"));
		// Stream.concat(streamA, streamB).forEach(System.out::println);
		Stream<String> concat = Stream.concat(streamA, streamB);
		System.out.println("----------------");
		// 6 根据姓名创建User对象
//		concat.map(u -> {
//			return new User2(u);
//		});
		// 方法引用方式
		// concat.map(User2::new);
		// 7 打印合并后集合的User信息
		concat.map(User2::new).forEach(System.out::println);
	}
}

class User2{
	String name;
	public User2(String name) {
		this.name=name;
	}
	@Override
	public String toString() {
		return "User2 [name=" + name + "]";
	}
}
```

### collect方法 收集Stream流中的结果

将stream流中的数据保存到集合或数组中

```java
public class Demo19 {

	public static void main(String[] args) {
		Stream<String> stream = Stream.of("a","b","c");
		
		// 将stream流中的数据保存到List集合中
		//List<String> list = stream.collect(Collectors.toList());
		//System.out.println(list);
		
		// 将stream流中数据收集到Set集合中
		//Set<String> set = stream.collect(Collectors.toSet());
		//System.out.println(set);
		
		// 将stream流中数据收集到指定集合中 例如ArrayList
		//ArrayList<String> arrayList = stream.collect(Collectors.toCollection(ArrayList::new));
		//System.out.println(arrayList);
	
		// 将stream流中数据收集到数组中
		//Object[] array = stream.toArray();
		//System.out.println(array);
		// 将stream流中数据收集到String[]中
		String[] array = stream.toArray(String[]::new);
		for (String s : array) {
			System.out.println(s);
		}
	}
}
```

### stream流中数据聚合计算

我们可以像操作数据库中字段一样 对数据进行聚合计算 例如最大 最小值，统计数量等

```java
public class Demo20 {

	public static void main(String[] args) {
		List<Student2> list = new ArrayList<>();
		Collections.addAll(list, 
				new Student2("Tom",12,98),
				new Student2("Jerry",13,99),
				new Student2("Andy",15,100),
				new Student2("Lily",16,97));
		
		// 获取分数最高的学生信息
		maxValue(list.stream());
		// 获取分数最低的学生信息
		minValue(list.stream());
		// 所有学生的成绩总和
		totalScore(list.stream());
		// 所有学生的平均成绩
		avgScore(list.stream());
		// 统计99分以上的学生数量
		count99(list.stream());
		// 统计集合中元素个数
		count(list.stream());
	}
	// 统计集合中元素个数
	private static void count(Stream<Student2> stream) {
		Long count = stream.collect(Collectors.counting());
		System.out.println(count);
	}
	// 统计99分以上的学生数量
	private static void count99(Stream<Student2> stream) {
		Long total = stream.filter(s -> s.score>99).collect(Collectors.counting());
		System.out.println(total);
	}
	// 所有学生的平均成绩
	private static void avgScore(Stream<Student2> stream) {
		Double avg = stream.collect(Collectors.averagingInt(s -> s.score));
		System.out.println(avg);
	}
	// 所有学生的成绩总和
	private static void totalScore(Stream<Student2> stream) {
		Integer totalScore = stream.collect(Collectors.summingInt(s -> s.score));
		System.out.println(totalScore);
	}
	// 获取分数最低的学生信息
	// 升序 取第一个
	private static void minValue(Stream<Student2> stream) {
		//Optional<Student2> min = stream.min((s1,s2)-> s1.score-s2.score);
		//System.out.println(min.get());
		Optional<Student2> min = stream.collect(Collectors.minBy((s1,s2)-> s1.score-s2.score));
		System.out.println(min.get());
		
	}
	// 获取分数最高的学生信息
	// 升序 取最后一个
	private static void maxValue(Stream<Student2> stream) {
		Optional<Student2> max = stream.collect(Collectors.maxBy((s1,s2)-> s1.score-s2.score));
		System.out.println(max.get());
		// Optional<Student2> max2 = stream.max((s1,s2) -> s1.score-s2.score);
	}
}

class Student2{
	String name;
	int age;
	int score;
	public Student2(String name,int age,int score) {
		this.name = name;
		this.age = age;
		this.score = score;
	}
	@Override
	public String toString() {
		return "Student2 [name=" + name + ", age=" + age + ", score=" + score + "]";
	}
}
```

### stream流中的数据进行分组

groupingBy方法对数据进行分组

```java
public class Demo21 {

	public static void main(String[] args) {
		List<Student2> list = new ArrayList<>();
		Collections.addAll(list, 
				new Student2("Tom",12,98),
				new Student2("Jerry",13,99),
				new Student2("Andy",12,100),
				new Student2("Lily",13,97));
		
		// 按年龄分组
		Map<Integer, List<Student2>> map = list.stream().collect(Collectors.groupingBy(s ->{
			return s.age;
		}));
		map.forEach((k,v) -> {
			System.out.println(k+"::"+v);
		});
		
		// 按成绩分组 大于99 优  小于99良好
		Map<String, List<Student2>> map2 = list.stream().collect(Collectors.groupingBy(s ->{
			return s.score>=99? "优":"良好";
		}));
		
		map2.forEach((k,v) -> {
			System.out.println(k+"="+v);
		});
	}
}
```

### stream流中数据多级分组

groupingBy方法对数据进行分组

分组条件可以有多个 即 先根据一个字段进行分组 然后再根据另个字段进行二次分组

```java
public class Demo22 {

	public static void main(String[] args) {
		List<Student2> list = new ArrayList<>();
		Collections.addAll(list, 
				new Student2("Tom",12,98),
				new Student2("Jerry",13,99),
				new Student2("Andy",12,100),
				new Student2("Lily",13,97));
		
		// 先根据年龄分组 每组中再根据成绩分组
		Map<Integer, Map<String, List<Student2>>> map = list.stream().collect(Collectors.groupingBy(s -> {
			return s.age;
		},Collectors.groupingBy(sc ->{
			return sc.score>=99 ?"优":"良好";
		})));
		
		map.forEach((k,v)->{
			System.out.println(k);
			// value仍然是一个map
			v.forEach((k2,v2)->{
				System.out.println("\t"+k2+"=="+v2);
			});
		});
	}
}
```

### stream流中数据进行分区

partitioningBy方法对数据进行分区

与分组类似 不同的是 返回的map集合的键是boolean类型

分组 可以根据不同条件分成多个组 但分区只有两个 一个true,一个false

```java
public class Demo23 {

	public static void main(String[] args) {
		List<Student2> list = new ArrayList<>();
		Collections.addAll(list, 
				new Student2("Tom",12,98),
				new Student2("Jerry",13,99),
				new Student2("Andy",12,100),
				new Student2("Lily",13,97));
		
		Map<Boolean, List<Student2>> map = list.stream().collect(Collectors.partitioningBy(s -> {
			return s.score>=99;
		}));
		map.forEach((k,v) -> {
			System.out.println(k+"--"+v);
		});
	}
}
```

### stream流中数据拼接

joining方法进行数据拼接

```java
public class Demo24 {

	public static void main(String[] args) {
		List<Student2> list = new ArrayList<>();
		Collections.addAll(list, 
				new Student2("Tom",12,98),
				new Student2("Jerry",13,99),
				new Student2("Andy",12,100),
				new Student2("Lily",13,97));
		// 拼接学生名称
		// 根据一个字符串进行拼接
		String names = list.stream().map(s -> s.name).collect(Collectors.joining("-"));
		System.out.println(names); // Tom-Jerry-Andy-Lily
		// 根据三个字符串进行拼接
		// 第一个参数表示分隔符，第二个参数表示前缀，第三个参数表示后缀
		String names2 = list.stream().map(s -> s.name).collect(Collectors.joining("-","^_^","V_V"));
		System.out.println(names2); // ^_^Tom-Jerry-Andy-LilyV_V
	}
}
```

小结：

收集stream流中的结果

收集到List集合中 Collectors.toList()

收集到Set集合中 Collectors.toSet()

收集到指定的集合中 Collectors.toCollection(ArrayList::new)

聚合计算

获取最大值 Collectors.maxBy

获取最小值 Collectors.minBy

获取平均值 Collectors.averagingInt

获取总数 Collectors.summingInt

统计数量 Collectors.countting

分组

Collectors.groupingBy若多个分组 则在collect方法中使用多个Collectors.groupingBy

分区

Collectors.partitioningBy

分组和分区的区别 分组可以有多个条件 可以分为多个组 ，分区只能分为两个区

拼接

Collectors.joining 对字段进行拼接 添加拼接符号等

## 并行的stream流

上述案例中均为串行stream流 即 只有一个线程从创建到销毁

```java
public class Demo25 {

	public static void main(String[] args) {
		Stream.of(1,2,3,4).filter(s -> {
			System.out.println(Thread.currentThread()+"--"+s);
			return s>3;
		}).count();
	}
}
----------------------------------
Thread[main,5,main]--1
Thread[main,5,main]--2
Thread[main,5,main]--3
Thread[main,5,main]--4
```

### 创建并行stream流的两种方式

直接调用parallelStream()获取并行的stream流

已有的串行stream流调用parallel()获取并行stream流

```java
public class Demo25 {

	public static void main(String[] args) {
		List<Integer> list = new ArrayList<>();
		Collections.addAll(list, 1,2,3,4,5);
		// 直接获取并行的stream流
		list.parallelStream();
		// 将已有的串行stream流转成并行的stream流
		list.stream().parallel().filter(s -> {
			System.out.println(Thread.currentThread()+"--"+s);
			return s>3;
		}).count();
	}
}
---------------
Thread[ForkJoinPool.commonPool-worker-4,5,main]--4
Thread[main,5,main]--3
Thread[ForkJoinPool.commonPool-worker-2,5,main]--5
Thread[ForkJoinPool.commonPool-worker-1,5,main]--2
Thread[ForkJoinPool.commonPool-worker-3,5,main]--1
```

### 串行流和并行流效率对比

分别使用普通for循环,串行stream流和并行的stream流对5亿个数字求和

```java
public class Demo26 {
	
	private static final int nums = 500000000;

	public static void main(String[] args) {
		sum3();
	}
	
	// 并行stream流对5亿个数字求和
	private static void sum3() {
		long start = System.currentTimeMillis();
		long sum = LongStream.rangeClosed(0, nums).parallel().reduce(0,Long::sum);
		System.out.println("sum = "+sum);
		long end = System.currentTimeMillis();
		System.out.println("并行stream流耗时: "+(end - start));// 并行stream流耗时: 129
	}
	// 串行stream流对5亿个数字求和
	private static void sum2() {
		long start = System.currentTimeMillis();
		// 得到5亿个数字 
		// rangeClosed 参数1 起始值，参数2终止值
		long sum = LongStream.rangeClosed(0, nums)
					.reduce(0, Long::sum);
		System.out.println("sum = "+sum);
		long end = System.currentTimeMillis();
		System.out.println("串行stream流耗时: "+(end - start));// 串行stream流耗时: 253
		
	}
	
	// 普通for循环对5亿个数字求和效率
	private static void sum1() {
		int sum = 0;
		long start = System.currentTimeMillis();
		for(int i = 0;i<nums;i++) {
			sum+=i;
		}
		System.out.println("sum = "+sum);
		long end = System.currentTimeMillis();
		System.out.println("普通for循环耗时: "+(end - start));// 普通for循环耗时: 147
	}
}
```

### 并行stream流的线程安全问题

解决并行流的线程安全问题

加锁，使用线程安全的集合接收数据，调用stream流的toArray或collect操作满足线程安全

```java
public class Demo27 {
	

	public static void main(String[] args) {
		List<Integer> list = new ArrayList<>(1000);
		//初始化一千个元素添加到list中
//		IntStream.rangeClosed(1, 1000)
//				 .parallel()
//				 .forEach(e ->{
//					list.add(e);
//				});
//		System.out.println(list.size());// 927
		
		// 解决并行stream流线程安全问题 方式1 同步锁
//		Object o = new Object();
//		IntStream.rangeClosed(1, 1000)
//		 .parallel()
//		 .forEach(e ->{
//			synchronized (o) {
//				list.add(e);
//			}
//		});
//		System.out.println(list.size());// 1000
		
		// 解决并行stream流线程安全问题 方式2 使用线程安全的集合
		Vector<Integer> v = new Vector<>();
		IntStream.rangeClosed(1, 1000)
		 .parallel()
		 .forEach(e ->{
			v.add(e);
		});
		System.out.println(v.size());// 1000
		
		// 解决并行stream流线程安全问题 方式3  将ArrayList转成线程安全的集合
		List<Integer> synchronizedList = Collections.synchronizedList(list);
		IntStream.rangeClosed(1, 1000)
		 .parallel()
		 .forEach(e ->{
			 synchronizedList.add(e);
		});
		System.out.println(synchronizedList.size()); // 1000
		
		// 解决并行stream流线程安全问题 方式4 使用stream流的collect收集 会将集合转成线程安全的
		List<Integer> collect = IntStream.rangeClosed(1, 1000)
		 		.parallel()
		 		.boxed()
		 		.collect(Collectors.toList());
		System.out.println(collect.size());// 1000
		
	}
}
```

### ForkJoin框架

#### 介绍

并行流(parallelStream)底层采用的是ForkJoin框架，ForkJoin框架是JDK7引入的新特性

ForkJoin框架可以将一个大的任务拆分成多个小的任务来异步执行

ForkJoin框架主要包含三个模块

线程池：ForkJoinPool

任务对象：ForkkJoinTask

执行任务的线程：ForkJoinWorkerThread

#### 原理

ForkJoin工作原理

分治法

ForkJoin分为两个阶段 Fork阶段会将一个大的任务拆分成多个小的任务，Join阶段会将每个小的任务进行汇总并最后返回结果

工作窃取算法

当前线程执行完任务后会窃取其它线程的任务继续执行(只窃取其它线程最后要执行的任务 不会窃取其它线程正在或即将要执行的任务)

这种算法可以更好的利用CPU

### 案例

需求：计算1到10000之间的和

```java
public class ForkJoinDemo {

	public static void main(String[] args) {
		long start = System.currentTimeMillis();
		// 创建线程池
		ForkJoinPool pool = new ForkJoinPool();
		// 绑定要执行的任务
		SumRecursiveTask task = new SumRecursiveTask(1L,10000L);
		Long res = pool.invoke(task);
		System.out.println("res = "+res);
		long end = System.currentTimeMillis();
		System.out.println("耗时: "+(end-start)+" ms");// 耗时: 4 ms
	}
}

// 创建求和的任务类
class SumRecursiveTask extends RecursiveTask<Long>{
	/**
	 * 
	 */
	private static final long serialVersionUID = 1L;
	// 是否需要拆分的临界值 当达到该值时 需要将任务进行拆分
	private static final Long THRESHOLD = 3000L;
	// 计算的起始值
	private final Long start ;
	// 计算的结束值
	private final Long end;
	
	public SumRecursiveTask(Long start, Long end) {
		super();
		this.start = start;
		this.end = end;
	}



	@Override
	protected Long compute() {
		
		Long len = end-start;
		if(len<THRESHOLD) {// 是否达到拆分的临界值
			// 计算
			Long sum = 0L;
			for (Long i = start;i<=end;i++) {
				sum+=i;
			}
			return sum;
			
		}else {
			// 拆分任务
			// 计算需要拆分的中间值
			Long middle = (start+end) >> 1;
			// 创建一个新的任务进行拆分
			SumRecursiveTask left = new SumRecursiveTask(start,middle);
			// fork 表示新建一个任务进行拆分
			left.fork();
			
			// 再创建一个新的任务进行拆分
			SumRecursiveTask right = new SumRecursiveTask(middle+1,end);
			// fork 表示新建一个任务进行拆分
			right.fork();
			
			return left.join()+right.join();
		}
	}
}
```
![9fa2735f80364b46e8f232004ccfb09](https://user-images.githubusercontent.com/47961027/214361257-c8474250-127e-4d3f-bf5f-ac1b32287a8e.png)


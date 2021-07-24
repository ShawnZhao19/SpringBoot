[TOC]

# JDK 1.8 新特性-lambda、stream流及方法引用

## 1. JDK 1.8 新特性的特征

```text
1. 速度更快 
2. 代码更少（增加了新的语法 Lambda 表达式） 
3. 强大的 Stream API 
4. 便于并行计算 
5. 最大化减少空指针异常 Optional
6. 其中最为核心的为 Lambda 表达式与 Stream API
```

## 2. Lambda 表达式

### 2.1 为什么使用 lambda 表达式

```text
Lambda 是一个匿名函数，我们可以把 Lambda 表达式理解为是一段可以传递的代码（将代码像数据一样进行传递）。可以写出更简洁、更灵活的代码。作为一种更紧凑的代码风格，使 Java 的语言表达能力得到了提升
```

### 2.2 lambda 的经典引入 （匿名内部类到 lambda 的转换）

```java
    @Test
    public void test4() {
        new Thread(new Runnable() {
            @Override
            public void run() {
                System.out.println("aaaaaaaaaaaa");
            }
        }).start();
    }
```

```java
    @Test
    public void test4() {
        
        new Thread(() -> {
            System.out.println("666666666666");
        }).start();
    }

	甚至于 {} () 在满足条件时都可以进行省略
```

### 2.3 lambda 表达式的语法

```text
Lambda 表达式在 Java 语言中引入了一个新的语法元素和操作符。这个操作符为 "->" ,该操作符被称为 Lambda 操作符或剪头操作符。它将 Lambda 分为两个部分： 
	左侧：指定了 Lambda 表达式需要的所有参数 
	右侧：指定了 Lambda 体，即 Lambda 表达式要执行的功能
```

### 2.4 lambda 表达式的类型推断

```text
Lambda 表达式中的参数类型都是由编译器推断得出的. Lambda 表达式中无需指定类型，程序依然可以编译，这是因为 javac 根据程序的上下文，在后台推断出了参数的类型。Lambda 表达式的类型依赖于上下文环境，是由编译器推断出来的。这就是所谓的“类型推断”
```

## 3. 函数式接口

### 3.1 什么是函数式接口

```text
1. 只包含一个抽象方法的接口，称为函数式接口
2. 你可以通过 Lambda 表达式来创建该接口的对象
	若 Lambda 表达式抛出一个受检异常，那么该异常需要在目标接口的抽象方法上进行声明
3. 我们可以在任意函数式接口上使用 @FunctionalInterface 注解， 这样做可以检查它是否是一个函数式接口，同时 javadoc 也会包 含一条声明，说明这个接口是一个函数式接口
```

### 3.2 自定义函数式接口

```text
lambda 表达式的本质是接口的实例对象
	为了将 lambda 表达式作为参数来进行传递，接收 lambda 表达式的参数类型必须是与该 lambda 表达式兼容的函数式接口的类型
	将函数式接口作为参数来进行传递
```

```java
package com.shawn.lambda;

/**
 * Created with IntelliJ IDEA.
 *
 * @Author: shawnzhao
 * @Date: 2021/07/16/18:58
 * @Description:
 */

@FunctionalInterface
public interface MyFunctionInterface {

    String getValue(String str);
}
```

```java
![image-20201113233427321](E:\千峰的学习\千峰的自己笔记\2106班二阶段\Day23-lambda及方法引用\assets\image-20201113233427321.png)package com.shawn.lambda;

/**
 * Created with IntelliJ IDEA.
 *
 * @Author: shawnzhao
 * @Date: 2021/07/16/19:06
 * @Description:
 */

@FunctionalInterface
public interface MyFunctionInterface2<T,R> {

    R calculate(T t1, T t2);
}
```

```java
    public String toUpperCase(String str,MyFunctionInterface myFunctionInterface) {
        return myFunctionInterface.getValue(str);
    }

    public Long calculate(Long a, Long b, MyFunctionInterface2<Long,Long> myFunctionInterface2) {

        return myFunctionInterface2.calculate(a,b);
    }
```

### 3.3 java 内置的四大核心函数式接口

![](E:\千峰的学习\千峰的自己笔记\2106班二阶段\Day23-lambda及方法引用\assets\image-20201113233427321.png)

```java
package com.shawn.lambda;

import org.junit.jupiter.api.Test;

import java.util.ArrayList;
import java.util.List;
import java.util.Random;
import java.util.function.Consumer;
import java.util.function.Function;
import java.util.function.Predicate;
import java.util.function.Supplier;

/**
 * Created with IntelliJ IDEA.
 *
 * @Author: shawnzhao
 * @Date: 2021/07/19/15:38
 * @Description: java8 四大函数式接口
 *
 * Consumer<T> 消费型接口，参数类型为泛型 T 返回值类型为 void
 * Supplier<T> 供给型接口，无参数 返回值为泛型 T
 * Function<T,R> 功能型接口，参数类型为泛型 T 返回值类型为泛型 R
 * Predicate<T> 断言型接口，参数类型为泛型 T 返回值类型为布尔类型 boolean
 */
public class Demo2 {

    @Test
    public void test1() {

        // 1. 调用方法，消费型接口 Consumer 作为参数来传递数据
        method1("时子贺是个nt", System.out::println);
    }

    public void method1(String str, Consumer<String> consumer) {
        consumer.accept(str);
    }

    @Test
    public void test2() {

        List<Integer> list = method2(5, () ->
                new Random().nextInt(1000)
        );

        System.out.println(list);
    }

    public List<Integer> method2(int number, Supplier<Integer> supplier) {

        List<Integer> list = new ArrayList<>();

        for (int i = 0; i < number; i++) {
            list.add(supplier.get());
        }

        return list;
    }

    @Test
    public void test3() {

        String str = method3("时子贺是个nt", (s) -> s.substring(1, 6));
        System.out.println(str);
    }

    public String method3(String str, Function<String,String> function) {

        return function.apply(str);
    }

    @Test
    public void test4() {

        Boolean flag = method4("时子贺可真牛啊", (s) -> s.contains("时"));

        System.out.println(flag);
    }

    public Boolean method4(String str, Predicate<String> predicate) {

        return predicate.test(str);
    }
}

```

### 3.4 java 的一些其他接口

![](E:\千峰的学习\千峰的自己笔记\2106班二阶段\Day23-lambda及方法引用\assets\image-20201113233451067.png)

## 4. 方法引用

### 4.1 方法引用

```text
当要传递给 lambda 体的操作已经有实现的方法时，可以使用方法引用
	注意：实现抽象方法的参数列表，必须与方法引用方法的参数列表保持一致
		方法引用使用操作符 "::"
			对象::实例方法
			类::静态方法
			类::实例方法
```

```java
String str = toUpperCase("dasjlkdajskld", String::toUpperCase);
	目的是要调用方法，结果是转换大小写，参数是 String 字符串，因此目的就是 String 类的 toUpperCase 方法
        
Long sum = calculate(4L,6L, Long::sum);
	目的就是 Long 类的 sum 求和方法
```

```java
compare("abcd","abcd",(x,y) -> x.equals(y));
	目的是为了比较字符串是否相等，是两个 String 字符串比较，用的是 equals 方法
    	所以： compare("abcd","abcd",String :: equals);
			注意：当需要引用方法的第一个参数是调用对象，并且第二个参数是需要引用方法的第二个参数(或无参数)时
                ClassName::methodName
```

```java
    @Test
    public void test1() {

        // 对象 :: 实例方法
        Supplier<String> supplier = "szhszh" ::toUpperCase;

        String str = supplier.get();
        System.out.println(str);

        Student student = new Student(1, "szh", 21, 99.9);
        Supplier<String> supplier1 = student :: getName;
        System.out.println(supplier1.get());
    }
```

```java
    @Test
    public void test3() {

        // 类名 :: 实例方法
        Function<Student,String> function = Student::getName;
        String s = function.apply(new Student("szh"));
        System.out.println(s);

        // 类名 :: 实例方法名
        // 当第一个参数是方法的调用者,第二个参数是方法的参数时（或者无参数）可以使用类名 :: 实例方法名
        BiPredicate<String,String> biPredicate = String::endsWith;
        boolean b = biPredicate.test("zh", "jh");
        System.out.println(b);

    }
```

```java
    @Test
    public void test2() {

        // 类名 :: 静态方法
        Comparator<Integer> comparator = Integer::compare;
        int i = comparator.compare(31,28);
        System.out.println(i);

    }
```

### 4.2 构造器引用

```text
格式：
	ClassName :: new
		与函数式接口相结合，自动与函数式接口中的方法兼容，可以把构造器引用赋值给定义的方法，构造器参数列表要与接口中抽象方法的参数列表一致
		
例如：
	Function<Integer,MyClass> fun = MyClass :: new
```

```java
    @Test
    public void test4() {

        // 类名 :: 构造方法
        Supplier<Student> supplier = Student::new;
        Student student = supplier.get();
        System.out.println(student);

        Consumer<String> consumer = Student :: new;
        consumer.accept("szh");

        Function<String,Student> function = Student::new;
        Student student1 = function.apply("szh");
        System.out.println(student1);

        BiFunction<Integer,String,Student> biFunction = Student :: new;
        Student student2 = biFunction.apply(6, "lhz");
        System.out.println(student2);

    }
```

### 4.3 数组引用

```text
格式：
	type[] :: new
例如：
	Function<Integer,Integer[]> fun = Integer[] :: new
```

## 5. Stream 流式计算

### 5.1 Stream 流的概念

```text
Java 1.8 中有两大最为重要的改变。第一个是 Lambda 表达式；另外一个则是 Stream API(java.util.stream.*)。 Stream 是 Java8 中处理集合的关键抽象概念，它可以指定你希望对集合进行的操作，可以执行非常复杂的查找、过滤和映射数据等操作。 使用 Stream API 对集合数据进行操作，就类似于使用 SQL 执行的数据库查询。也可以使用 Stream API 来并行执行操作。简而言之， Stream API 提供了一种高效且易于使用的处理数据的方式
```

### 5.2 什么是 Stream 流

```text
流(Stream)是数据渠道，用于操作数据源(集合、数组等)所生成的元素序列
	集合重要的是数据，流重要的是计算
	
注意：
	a. Stream 不会自己存储元素
	b. Stream 不会改变源对象，会返回一个新的 Stream
	c. Stream 操作是延迟执行的
```

### 5.3 Stream 操作的三个步骤

![](E:\千峰的学习\千峰的自己笔记\2106班二阶段\Day23-lambda及方法引用\assets\image-20201113233937694.png)

### 5.4 创建 Stream 流

#### 5.4.1 集合创建流

```text
Java8 中的 Collection 接口被扩展，提供了两种获取流的方法：
	default Stream<E> stream() 返回一个顺序流
	default Stream<E> parallelStream() 返回一个并行流
```

#### 5.4.2 数组创建流

```text
Java8 中的 Arrays 的静态方法 stream() 可以获取数组流：
	static<T> Stream<T> stream(T[] array)
		返回一个流
```

#### 5.4.3 值创建流

```text
可以使用静态方法 Stream.of() 通过显示值创建一个流。它可以接受任意数量的参数

public static<T> Stream<T> of(T ... values)
```

#### 5.4.4 函数创建流：无限流

```text
可以使用静态方法 Stream.iterate() 与 Stream.generate() 来创建无限流

迭代:
public static<T> Stream<T> iterate(final T seed,final UnaryOperator<T> f)

生成:
public static<T> Stream<T> generate(Supplier<T> s)
```

### 5.5 Stream 流的中间操作

![](E:\千峰的学习\千峰的自己笔记\2106班二阶段\Day23-lambda及方法引用\assets\image-20201113234345519.png)

![](E:\千峰的学习\千峰的自己笔记\2106班二阶段\Day23-lambda及方法引用\assets\image-20201113234407479.png)

![](E:\千峰的学习\千峰的自己笔记\2106班二阶段\Day23-lambda及方法引用\assets\image-20201113234418305.png)

```java
package com.shawn.stream;

import com.shawn.pojo.Student;
import org.junit.jupiter.api.Test;

import java.util.Arrays;
import java.util.List;
import java.util.stream.Collectors;
import java.util.stream.Stream;

/**
 * Created with IntelliJ IDEA.
 *
 * @Author: shawnzhao
 * @Date: 2021/07/19/16:16
 * @Description: Stream 流式中间操作
 */
public class Demo1 {

    List<Student> list = Arrays.asList(
            new Student(1,"zs",18,98.5),
            new Student(2,"ls",19,89.5),
            new Student(3,"ww",20,87.5),
            new Student(4,"zl",16,96.5),
            new Student(5,"tq",21,98.5),
            new Student(6,"mb",17,95.5)
    );

    @Test
    public void test1() {

        list.stream()
                .filter((s) -> s.getId() > 3)
                .forEach(System.out :: println);
    }

    @Test
    public void test2() {

        list.stream()
                .filter((s) -> s.getAge() > 18)
                .map(Student::getName)
                .forEach(System.out :: println);
    }

    @Test
    public void test3() {

        list.stream()
                .filter(s -> s.getId() > 2)
                .map(Student::getName)
                .collect(Collectors.toList())
                .forEach(System.out :: println);
    }

    @Test
    public void test4() {

        //Collection 接口提供的方法
        Stream<Student> stream = list.stream();

        List<String> list = Arrays.asList("lhz", "szh", "wbw");

        Stream<String> stringStream = list.stream();

    }

    @Test
    public void test5() {

        // 数组转 Stream 流
        Student[] students = new Student[10];

        Stream<Student> stream = Arrays.stream(students);

    }

    @Test
    public void test6() {

        // Stream 流的 iterate
        Stream<Integer> iterate = Stream.iterate(0, x -> x + 3);

        iterate
                .limit(10)
                .forEach(System.out :: println);
    }

    @Test
    public void test7() {

        // Stream 流的 iterate
        Stream<Double> generate = Stream.generate(Math::random);

        generate
                .limit(5)
                .forEach(System.out :: println);
    }

    @Test
    public void test8() {
        // 自然排序看 Comparable 接口实现
        // list.stream().sorted().forEach(System.out::println);
        list.stream()
                .sorted((s1,s2) -> s1.getAge() > s2.getAge() ? 1 : -1)
                .forEach(System.out::println);
    }
}
```

### 5.6 Stream 流的终止操作

```text
终端操作会从流的流水线生成结果。其结果可以是任何不是流的 值，例如：List、Integer，甚至是 void 
```

![](E:\千峰的学习\千峰的自己笔记\2106班二阶段\Day23-lambda及方法引用\assets\image-20201113234506940.png)

![](E:\千峰的学习\千峰的自己笔记\2106班二阶段\Day23-lambda及方法引用\assets\image-20201113234525389.png)

![](E:\千峰的学习\千峰的自己笔记\2106班二阶段\Day23-lambda及方法引用\assets\image-20201113234541250.png)

![](E:\千峰的学习\千峰的自己笔记\2106班二阶段\Day23-lambda及方法引用\assets\image-20201113234606341.png)

![](E:\千峰的学习\千峰的自己笔记\2106班二阶段\Day23-lambda及方法引用\assets\image-20201113234634022.png)

![](E:\千峰的学习\千峰的自己笔记\2106班二阶段\Day23-lambda及方法引用\assets\image-20201113234651448.png)

```java
package com.shawn.stream;

import com.shawn.pojo.Student;
import org.junit.jupiter.api.Test;

import java.util.Arrays;
import java.util.List;
import java.util.Optional;
import java.util.stream.Collectors;

/**
 * Created with IntelliJ IDEA.
 *
 * @Author: shawnzhao
 * @Date: 2021/07/19/16:41
 * @Description: Stream 流式终结操作
 */
public class Demo3 {

    List<Student> list = Arrays.asList(
            new Student(1,"zs",18,98.5),
            new Student(2,"ls",19,89.5),
            new Student(3,"ww",20,87.5),
            new Student(4,"zl",16,96.5),
            new Student(5,"tq",21,98.5),
            new Student(6,"mb",17,95.5),
            new Student(6,"mb",17,95.5)
    );
    @Test
    public void test1() {

        list.stream()
                .filter((s) -> s.getScore() > 90)
                .map(Student::getName)
                .collect(Collectors.toList())
                .forEach(System.out::println);

        long l = list.stream()
                .filter((s) -> s.getScore() > 90)
                .map(Student::getName)
                .count();

        System.out.println(l);

    }

    @Test
    public void test2() {

        boolean b = list.stream()
                .allMatch((s) -> s.getScore() > 90);
        boolean b1 = list.stream()
                .anyMatch((s) -> s.getScore() > 90);
        System.out.println(b);
        System.out.println(b1);

        Optional<Student> first = list.stream()
                .findFirst();

        System.out.println(first.get());

        Optional<Student> max = list.stream()
                .max((s1, s2) -> s1.getAge() > s2.getAge() ? 1 : -1);

        System.out.println(max.get());
    }

    @Test
    public void test3() {
        Optional<Double> reduce = list.stream()
                .map(Student::getScore)
                .reduce(Double::sum);

        System.out.println(reduce.get());

        List<Integer> integers = Arrays.asList(1, 2, 3, 4, 5, 6, 7, 8, 9, 10);
        Integer reduce1 = integers.stream()
                .reduce(0, Integer::sum);

        System.out.println(reduce1);
    }
}
```

笔记地址：https://blog.didispace.com/books/java8-tutorial/ch1.html

视频讲解：https://www.bilibili.com/video/BV1T54y1q7W2

# Lambda与函数式接口

## 接口中有默认方法实现

Java 8 允许我们使用default关键字，为接口声明添加非抽象的方法实现。这个特性又被称为`扩展方法`。

```java
interface Formula {
    double calculate(int a);
    default double sqrt(int a) {
        return Math.sqrt(a);
    }
}
```

## Lambda表达式

Java 8的一个大亮点是引入Lambda表达式，使用它设计的代码会更加简洁。通过Lambda表达式，可以替代我们以前经常写的匿名内部类来实现接口。

Lambda表达式本质是一个匿名函数，因为普通函数一般要先声明，然后定义返回值、方法名、参数列表、方法体，但是Lambda表达式只有参数列表和方法体

### Lambda表达式组成

Lambda表达式由三部分组成：

- 小括号，里面是参数列表
- 箭头（goes to）
- 花括号（方法体）

参数列表的参数类型可以省略，方法体中只有一行，花括号可以省略，如果只有一行return，也要省掉return关键字。只有一个参数，小括号也可以省略

### 初体验Lambda表达式

这个是我们以前的实现，匿名内部类，然后调用执行；

```java
public class Demo {
    interface Test{
        int add(int a,int b);
    }
    public static void main(String[] args) {
        Test test = new Test() {
            @Override
            public int add(int a, int b) {
                return a + b;
            }
        };
        int c = test.add(1,2);
        System.out.println(c);
    }
}
```

我们现在用Lambda表达式改写下：

```java
public class Demo {
    interface Test{
        int add(int a,int b);
    }
    public static void main(String[] args) {
        Test test = (a,b)->{
            return a+b;
        };
        int c = test.add(1,2);
        System.out.println(c);
    }
}
```

### 方法引用

语法：对象::方法，假如是static方法，可以直接类名::方法

```java
@FunctionalInterface
interface Converter<F, T> {
    T convert(F from);
}

Converter<String, Integer> converter = Integer::valueOf;
Integer converted = converter.convert("123");
System.out.println(converted);    // 123
```

### 构造函数引用

如果函数式接口的实现恰好可以通过调用一个类的构造方法来实现，那么就可以使用构造方法引用

语法：类名::new

```java
public class Program3 {
    public static void main(String[] args) {
        // 普通方式
        DogService dogService=()->{
            return new Dog();
        };
        dogService.getDog();
        // 简化方式
        DogService dogService2=()->new Dog();
        dogService2.getDog();
        // 构造方法引用
        DogService dogService3=Dog::new;
        dogService3.getDog();
        // 构造方法引用 有参
        DogService2 dogService21=Dog::new;
        dogService21.getDog("小米",11);
    }
}
```

### 综合案例

```java
public class Demo {
    public static void main(String[] args) {
        List<Dog> list=new ArrayList<>();
        list.add(new Dog("aa",1));
        list.add(new Dog("bb",4));
        list.add(new Dog("cc",3));
        list.add(new Dog("dd",2));
        list.add(new Dog("ee",5));
        // 排序
        System.out.println("lambda集合排序");
        list.sort((o1,o2)->o1.getAge()-o2.getAge());
        System.out.println(list);
        // 遍历集合
        System.out.println("lambda遍历集合");
        list.forEach(System.out::println);
    }
}
```

## 函数式接口

@FunctionalInterface注解：这个注解是函数式接口注解，所谓的函数式接口，当然首先是一个接口，然后就是在这个接口里面只能有一个抽象方法。这种类型的接口也称为SAM(Single Abstract Method interfaces)接口。

不管加不加这个@FunctionalInterface注解，如果以函数式接口的形式使用，都是可以的，但如果你的接口中定义了第二个抽象方法的话，编译器会直接抛出异常。

函数式接口特点：

- 接口有且仅有一个抽象方法
- 允许定义静态方法
- 允许定义默认方法
- 允许java.lang.Object中的public方法

> 我理解的函数式接口是一个接口，定义一个方法，通过引入其他方法的引用，将这个功能进行实现

```java
@FunctionalInterface
interface Converter<F, T> {
    T convert(F from);
}

Converter<String, Integer> converter = Integer::valueOf;
Integer converted = converter.convert("123");
System.out.println(converted);    // 123
```

### 系统内置的函数式接口

Java8的推出，是以Lambda重要特性，一起推出的，其中系统内置了一系列函数式接口。在JDK的`java.util.function`包下，有一系列的内置函数式接口：

### Lambda作用域

- 访问外部局部变量：只可读，不可写，编译时会隐式编译成final，不过访问的变量可以不加final
- 访问成员变量和静态方法：可读可写
- 访问默认接口方法：不可读，更不可用

## Map的Lambda

正如前面已经提到的那样，map是不支持流操作的。而更新后的map现在则支持多种实用的新方法，来完成常规的任务。

```java
public class Demo {
    public static void main(String[] args) {
        Map<Integer, String> map = new HashMap<>();
        for (int i = 0; i < 10; i++) {
            map.putIfAbsent(i, "val" + i);
        }
        
        //循环遍历打印，如果lambda只写一个参数默认为key
        map.forEach((id, val) -> System.out.println(val));
        
        //对 hashMap 中指定 key 的值进行重新计算，前提是该 key 存在于 hashMap 中
        //如果重新计算的值为null则自动剔除
        map.computeIfPresent(3, (num, val) -> val + num);//val3->val33
        map.computeIfPresent(9, (num, val) -> null);//val9->删除
        
        //对 hashMap 中指定 key 的值进行重新计算，如果不存在这个 key，则添加到 hasMap 中，如果存在则不进行操作
        map.computeIfAbsent(23, num -> "val" + num);//无->val23
        map.computeIfAbsent(3, num -> "bam"+num);//val33->val33

        //删除指定k、v，如果v和写的不一样就不会删除
        map.remove(3, "val3");
        System.out.println(map.get(3));
        map.remove(3, "val33");
        System.out.println(map.get(3));

        //如果有就获取，没有就返回默认值
        System.out.println(map.getOrDefault(42, "not found"));

        //合并数据，会先判断指定的 key 是否存在，如果不存在，则添加键值对到 hashMap 中，如果存在就进行操作。
        map.merge(9, "val9", (value, newValue) -> value.concat(newValue));
        map.merge(9, "concat", (value, newValue) -> value.concat(newValue));
    }
}
```



# Java中的流

## Stream流

Stream操作可以是中间操作，也可以是完结操作。完结操作会返回一个某种类型的值，而中间操作会返回流对象本身，并且你可以通过多次调用同一个流操作方法来将操作结果串起来。

Stream是在一个源的基础上创建出来的，例如java.util.Collection中的list或者set（map不能作为Stream的源）。Stream操作往往可以通过顺序或者并行两种方式来执行。

Java 8中的Collections类的功能已经有所增强，你可以之直接通过调用Collections.stream()或者Collection.parallelStream()方法来创建一个流对象。

```java
public class Demo {
    public static void main(String[] args) {
        List<Integer> list = Arrays.asList(2,4,5,5,5,1,3,6,7);
        Optional<Integer> reduce = list.stream()
                .filter(item -> item+1 > 3)//中间操作，做一个映射
                .map(item->item*10)//映射
                .sorted((num1, num2) -> num1 - num2)//升序
                .distinct()//去重
                .reduce((num1, num2) -> num1 + num2);//结束操作，经过上面处理的值求和
        System.out.println(reduce.get());
    }
}
```

结束的操作示例：

- match：返回布尔值，有anyMatch、allMatch、noneMatch等操作
- count：返回一个数值，用来标识当前流对象中包含的元素数量
- reduce：定义一个方法，对元素依次进行重复操作，最终结果会放在一个Optional变量里返回

## ParallelStream并行流

流操作可以是顺序的，也可以是并行的。顺序操作通过单线程执行，而并行操作则通过多线程执行。我们可以用并行流进行操作来提高运行效率，所有的代码段几乎都相同，唯一的不同就是把stream()改成了parallelStream()，来看一下顺序排序和并行排序的对比：

```java
public class Demo {
    public static void main(String[] args) {
        int max = 1000000;
        List<String> values = new ArrayList<>(max);
        for (int i = 0; i < max; i++) {
            UUID uuid = UUID.randomUUID();
            values.add(uuid.toString());
        }
        //顺序排序
        long t0 = System.nanoTime();
        long count = values.stream().sorted().count();
        long t1 = System.nanoTime();
        long millis = TimeUnit.NANOSECONDS.toMillis(t1 - t0);
        System.out.println(String.format("sequential sort took: %d ms", millis));
        /**
         * sequential sort took: 766 ms
         */
    }
}
```

并行排序能节约一半的时间：

```java
public class Demo {
    public static void main(String[] args) {
        int max = 1000000;
        List<String> values = new ArrayList<>(max);
        for (int i = 0; i < max; i++) {
            UUID uuid = UUID.randomUUID();
            values.add(uuid.toString());
        }
        long t0 = System.nanoTime();
        long count = values.parallelStream().sorted().count();
        long t1 = System.nanoTime();
        long millis = TimeUnit.NANOSECONDS.toMillis(t1 - t0);
        System.out.println(String.format("parallel sort took: %d ms", millis));
        /**
         * parallel sort took: 411 ms
         */
    }
}
```

## Collectors

Collectors是一个工具类，是JDK预实现Collector的工具类，它内部提供了多种Collector，我们可以直接拿来使用，非常方便。

它提供了许多操作，让我们调用起来十分方便，例如：

- toCollection：将流中的元素全部放置到一个集合中返回，这里使用Collection，泛指多种集合。
- toList/toSet：流中的元素放置到一个列表集合/无序集set中去，默认为ArrayList和HashSet。
- joining：将流中的元素全部以字符序列的方式连接到一起，可以指定连接符和结果的前后缀
- mapping：对流中的每个元素进行映射，即类型转换，然后再将新元素以给定的Collector进行归纳
- collectingAndThen：该方法是在归纳动作结束之后，对归纳的结果进行再处理
- counting：该方法用于计数
- minBy/maxBy：生成一个用于获取最小/最大值的Optional结果的Collector
- summingInt/summingLong/summingDouble：生成一个用于求元素和的Collector，首先通过给定的mapper将元素转换类型，然后再求和
- averagingInt/averagingLong/averagingDouble：生成一个用于求元素平均值的Collector，首选通过参数将元素转换为指定的类型。求平均值涉及到除法操作，结果一律为Double类型。
- reducing：reducing方法有三个重载方法，其实是和Stream里的三个reduce方法对应的，二者是可以替换使用的，作用完全一致，也是对流中的元素做统计归纳作用。
- groupingBy：这个方法是用于生成一个拥有分组功能的Collector，它也有三个重载方法
- partitioningBy：将流中的元素按照给定的校验规则的结果分为两个部分，放到一个map中返回，map的键是Boolean类型，值为元素的列表List
- toMap：根据给定的键生成器和值生成器生成的键和值保存到一个map中返回，键和值的生成都依赖于元素，可以指定出现重复键时的处理方案和保存结果的map。
- summarizingInt/summarizingLong/summarizingDouble：这三个方法适用于汇总的，返回值分别是IntSummaryStatistics，LongSummaryStatistics，DoubleSummaryStatistics。在这些返回值中包含有流中元素的指定结果的数量、和、最大值、最小值、平均值。所有仅仅针对数值结果。

```java
class Person {
    String name;
    String sex;
    Integer serlary;
    public Person(String name, String sex, Integer serlary) {
        this.name = name;
        this.sex = sex;
        this.serlary = serlary;
    }
}
public class Demo {
    public static void main(String[] args) {
        ArrayList<Person> list = new ArrayList<>();
        list.add(new Person("小红","女",1000));
        list.add(new Person("小芳","女",2000));
        list.add(new Person("小张","男",3000));
        list.add(new Person("小王","男",4000));
        Map<String, List<Person>> collect = list.stream().collect(Collectors.groupingBy(p -> p.sex));
        collect.forEach((k,v)-> {
            System.out.println(k);
            v.stream().forEach(p-> System.out.println(p.name));
        });
    }
}
```

> https://www.jianshu.com/p/7eaa0969b424

## 合并集合/数组

```java
class Person {
    String name;
    public String sex;
    Integer serlary;
    public Person(String name, String sex, Integer serlary) {
        this.name = name;
        this.sex = sex;
        this.serlary = serlary;
    }
}
public class Demo {
    public static void main(String[] args) {
        ArrayList<Person> list = new ArrayList<>();
        list.add(new Person("小红","女",1000));
        list.add(new Person("小芳","女",2000));
        list.add(new Person("小张","男",3000));
        list.add(new Person("小王","男",4000));
        ArrayList<Person> list2 = new ArrayList<>();
        list2.add(new Person("小红","女",1000));
        list2.add(new Person("小芳","女",2000));
        list2.add(new Person("小张","男",3000));
        list2.add(new Person("小王","男",4000));
        //如果是数组就用Arrays.stream的方法转化成流
        List<Person> collect = Stream.concat(list.stream(), list2.stream()).collect(Collectors.toList());
        System.out.println(collect);
    }
}
```

# 时间日期API

- 和SimpleDateFormat相比， DateTimeFormatter是线程安全的
- Instant的精确度更高，可以精确到纳秒级，但是Date只能到毫秒
- Duration可以便捷得到时间段内的天数、小时数等
- LocalDatetime能够快速地获取年、月、日、下一月等。
- TemporalAdjusters类中包含许多常用的静态方法，避免自己编写工具类。

# Nashron

Nashorn 一个 javascript 引擎，Nashorn JavaScript Engine 在 Java 15 已经不可用了。
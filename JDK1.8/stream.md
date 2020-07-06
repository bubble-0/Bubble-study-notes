# stream
Java 8 API添加了一个新的抽象称为流Stream，可以让你以一种声明的方式处理数据。

Stream 使用一种类似用 SQL 语句从数据库查询数据的直观方式来提供一种对 Java 集合运算和表达的高阶抽象。

Stream API可以极大提高Java程序员的生产力，让程序员写出高效率、干净、简洁的代码。

这种风格将要处理的元素集合看作一种流， 流在管道中传输， 并且可以在管道的节点上进行处理， 比如筛选， 排序，聚合等。
## 简介
Stream（流）是一个来自数据源的元素队列并支持聚合操作

元素是特定类型的对象，形成一个队列。 Java中的Stream并不会存储元素，而是按需计算。
数据源 流的来源。 可以是集合，数组，I/O channel， 产生器generator 等。
聚合操作 类似SQL语句一样的操作， 比如filter, map, reduce, find, match, sorted等。
和以前的Collection操作不同， Stream操作还有两个基础的特征：

Pipelining: 中间操作都会返回流对象本身。 这样多个操作可以串联成一个管道， 如同流式风格（fluent style）。 这样做可以对操作进行优化， 比如延迟执行(laziness)和短路( short-circuiting)。
内部迭代： 以前对集合遍历都是通过Iterator或者For-Each的方式, 显式的在集合外部进行迭代， 这叫做外部迭代。 Stream提供了内部迭代的方式， 通过访问者模式(Visitor)实现。

## 特点
Java中的Stream并不能直接存储数据，通常由集合容器或数组转化而来。当我们在Stream上进行聚合操作时（过滤、排序、去重等方式），Stream会根据过聚合操作的内容对数据进行筛选，每次聚合操作都会得到一个新的Stream，筛选的过程被称为中间操作(Intermediate)。在Stream的一系列操作中，中间操作返回的类型都是Stream，并且可以链式的执行多次中间操作，每一次中间操作产生的Stream都会被记录，由于中间操作没有产生新的类型或者集合，这些操作也叫做惰性求值方法。

每一个阶段的Stream都可以通过终端操作得到一个结果信息，终端操作将是我们最终获取数据的节点。要注意执行过终端操作的Stream将自动关闭，所以终端的操作也称为最终操作(Terminal)。Stream类型不同，最终操作的手段也不同。如果是数值流，我们可以计算和、平均值等数值操作。最终操作中我们获取了新的对象，也标志Stream操作的完结，最终操作只能进行一次。由于最终操作会产生新的数据，最终操作方法也称为及早求值方法。

## 总结
分为三个部分：1.数据源到Stream的转化，Java8中的集合容器都提供了向Stream转化的功能。2. Stream的聚合操作，聚合操作就是筛选的过程。聚合操作中可以与函数式接口高效的融合，使整个操作更为简洁。3.结果操作，结果操作就是终端操作，将整个Stream计算出一个结果，也可以将Stream再次转化成集合

## 使用

### Stream的创建
Stream是不能像普通实例通过new关键子来创建，它的创建方法主要有两种，第一种是利用Stream接口中的静态方法来创建。另外一种是由其它数据集合或者类创建Stream实例。Stream提供的静态方法如下
- concat(a,b) 将两个流ab连接成一个新流
- empty() 返回一个空的Stream
- generate(Supplier<T> s) 返回无限长度的stream流,其中每个元素由提供的Suppler.
- iterate() 返回一个无限长度的stream
- of(T ...values) 返回有序流
- of(T t) 返回包含单个元素的顺序流
``` java
   /**
    * 1.创建一个有限的Integer流
    */
    Stream<Integer> s1 = Stream.of(1, 2, 3, 4, 5);
    System.out.println("of(T ...value)");
    s1.forEach(System.out::print);
    System.out.println();

    /**
    * 2.创建一个无限长度Integer流,generate 参数是一个supplier接口,可以使用lambda表达式
    */
    //Stream<Integer> s2 = Stream.generate(()->new Integer(11));
    //第二种写法
    Stream<String> s2 = Stream.generate(String::new);

    //创建一个无限长度的stream
    Stream<Integer> s3 = Stream.generate(new Random()::nextInt);
    s3.limit(5).forEach(System.out::print);
    System.out.println();

    /**
    * 3.创建一个无限长度的stream
    */
    Stream<Integer> s4 = Stream.iterate(0, i -> i + 1);
    s4.limit(5).forEach(System.out::print);
    System.out.println();

    /**
    * 4.合并形成新的流
    */
    Stream<Integer> s6 = Stream.concat(Stream.of(1, 2, 3, 4, 5), Stream.of(6, 7, 8, 9));
    s6.forEach(System.out::print);
    System.out.println();

    /**
    * 5.单个元素
    */
    Stream<Integer> s7 = Stream.of(1);
    s7.forEach(System.out::print);

    /**
    * 6.返回空的流
    */
    Stream<Integer> s8 = Stream.empty();
    System.out.println(s8.count());
```

### stream中间函数使用
``` java
filter   过滤方法,参数是一个predicate函数接口,返回boolean值

map   元素转换方法,参数是一个Funcation<T, R>, map方法可以将Stream<T>流转换成一个Stream<R>流
与此相似的还有mapToDouble、mapToInt、mapToLong方法

flatMap   flatMap是将T类型元素计算并转化成Stream<R>类型元素，并将多个Stream<R>合并成一个Stream。也就是说flatMap可以将原Stream中的元素进行全新的展开计算，最终归纳成一个新的流

peek   peek方法用于加工Stream中的元素，map和peek都可以进行数据加工，但peek加工后，不会改变数据的类型。peek方法的参数是Consumer，这个接口是我们比较熟悉的一个函数接口，forEach方法中的参数就是这个接口对象。Consumer它是一个消费接口，消费接口方法"void accept(T t)"的目的是处理和使用元素。通过这个接口过程我们使用Stream中的参数执行各种操作，有可能会修改Stream中元素的内容

skip   丢弃流中的前n个元素，返回剩余的流，如果此流中的元素少于n个，则返回一个空Stream。

limit   表示将当前的流截断，返回一个最多含有maxSize个元素的Stream。limit方法比较特殊，它虽然是一个有状态中间方法，但它同时也是一个短路状态中间方法。短路操作我们并不陌生（例如运算符"&&"和"||"的短路操作），在Stream中，短路操作一旦成立，后续的元素不会被处理。

distinct   distinct方法对重复元素只保留一个，它的验证元素是否重复使用Object的equals方法进行验证。

sorted 如果Stream中的元素具备自然排序的能力，可以使用sorted方法进行排序，排序后的Stream是有序的，如果Stream中的元素不具备自然排序的能力，调用该方法会抛出ClassCastException异常。
备注: 如不支持自然排序，可使用sorted(Comparator<? super T> comparator)重载方法进行排序。

```

使用示例

``` java

    Integer[] arr = {61, 24, 33, 41, 35, 26, 17, 328, 19, 10, 100, 102, 103};

    /**
        * 过滤掉大于100的元素,转化成String
        */
    //求数量
    List<Integer> s = Arrays.asList(arr);
    System.out.println("count " + s.stream().filter(i -> i < 100 ? true : false).map(i -> String.valueOf(i)).count());

    //排序
    Arrays.stream(arr).filter(i -> i < 100 ? true : false).sorted().forEach(i -> System.out.print(i + ","));

    System.out.println();

    //limit
    Stream.of(61, 24, 33, 41, 35, 26, 17, 328, 19, 10, 100, 102, 103).filter(i -> i > 100 ? false : true).sorted().limit(5).forEach(i -> System.out.print(i + ","));
    System.out.println();

    //skip
    Stream.of(61, 24, 33, 41, 35, 26, 17, 328, 19, 10, 100, 102, 103).filter(i -> i < 100 ? true : false).sorted().skip(5).forEach(i -> {
        System.out.print(i + ",");
    });

    System.out.println();

    //peek
    Stream.of(61, 24, 33, 41, 35, 26, 17, 328, 19, 10, 100, 102, 103).sorted().peek(i -> {
        if (i > 100) {
            System.out.print("大于100的有" + i);
        }
    }).forEach(i -> {
        System.out.print(i + ",");
    });

    System.out.println();

    //flatMap
    //flatMap是将T类型元素计算并转化成Stream<R>类型元素，并将多个Stream<R>合并成一个Stream。
    //也就是说flatMap可以将原Stream中的元素进行全新的展开计算，最终归纳成一个新的流
    Stream.of(1, 2, 3, 4, 5).flatMap(e -> Stream.of(e, e)).forEach(i -> {
        System.out.print(i + ",");
    });

```

### stream最终方法
```
count、max、min 统计方法,max和min方法需要我们提供一个比较器用于元素之间的比较。

allMatch、anyMatch、noneMatch 短路匹配,分别验证Stream中的元素是否全部匹配、任意一个匹配以及没有匹配，返回结果是一个boolean值。这三个方法的参数是一样的，都是函数接口Predicate，也正是filter方法参数使用的接口对象。

collect 收集方法,将Stream中的元素收集到目标容器中，常常用于Stream向目标容器进行转化而使用。Stream在收集转化的过程中，还可以对Stream中的元素经过加工，将最后加工的数据收集到目标容器中。

reduce 归纳方法,将Stream中的元素归纳成一个对象，在归纳的过程中也可以对Stream中的元素进行操作
```

使用示例

``` java
public static void main(String[] args) {

        Integer[] arr = {61, 24, 33, 41, 35, 26, 17, 328, 19, 10, 100, 102, 103};

        /**
         * 过滤掉大于100的元素,转化成String
         */

        //排序
        Arrays.stream(arr).filter(i -> i < 100 ? true : false).sorted().forEach(i -> System.out.print(i + ","));

        System.out.println();

        //limit
        Stream.of(61, 24, 33, 41, 35, 26, 17, 328, 19, 10, 100, 102, 103).filter(i -> i > 100 ? false : true).sorted().limit(5).forEach(i -> System.out.print(i + ","));
        System.out.println();

        //skip
        Stream.of(61, 24, 33, 41, 35, 26, 17, 328, 19, 10, 100, 102, 103).filter(i -> i < 100 ? true : false).sorted().skip(5).forEach(i -> {
            System.out.print(i + ",");
        });

        System.out.println();

        //peek
        Stream.of(61, 24, 33, 41, 35, 26, 17, 328, 19, 10, 100, 102, 103).sorted().peek(i -> {
            if (i > 100) {
                System.out.print("大于100的有" + i);
            }
        }).forEach(i -> {
            System.out.print(i + ",");
        });

        System.out.println();

        //flatMap
        //flatMap是将T类型元素计算并转化成Stream<R>类型元素，并将多个Stream<R>合并成一个Stream。
        //也就是说flatMap可以将原Stream中的元素进行全新的展开计算，最终归纳成一个新的流
        Stream.of(1, 2, 3, 4, 5).flatMap(e -> Stream.of(e, e)).forEach(i -> {
            System.out.print(i + ",");
        });

        System.out.println();

        /**
         * 最终方法
         */

        Comparator<Integer> co = (e1, e2) -> e1 - e2;

        //min
        List<Integer> s = Arrays.asList(arr);
        System.out.println("最小元素 " + s.stream().sorted().min(co).get());

        //max
        List<Integer> s2 = Arrays.asList(arr);
        System.out.println("最大元素 " + s2.stream().sorted().max(co).get());

        //count
        List<Integer> s3 = Arrays.asList(arr);
        System.out.println("count " + s3.stream().filter(i -> i < 100 ? true : false).map(i -> String.valueOf(i)).count());

        //Match
        System.out.println("是否全部小于10? " + Stream.of(1, 2, 3, 4, 5, 6, 7, 8, 9).allMatch(i -> i < 10 ? true : false));

        System.out.println("是否有一个大于10? " + Stream.of(1, 2, 3, 4, 5, 10).anyMatch(i -> i > 10));

        System.out.println("是否全部都不大于10? " + Stream.of(1, 2, 3, 4, 5, 6).noneMatch(i -> i > 10));

        /**
         * 将map的age转换成一个集合
         */

        /**
         * 简单使用
         * <R> R collect(Supplier<R> supplier, BiConsumer<R,? super T> accumulator, BiConsumer<R,R> combiner)
         *
         * Supplier<R> supplie：该函数接口的参数类型"R"与collect返回的类型是一致的，
         * 方法"T get()"的目的就是返回一个新对象，如果我们想将Stream转化成List，
         * get方法内应该返回一个List实例（如果想将Stream转化成String，get方法返回一个String实例）。一般来说，该方法是构造方法引用的最佳实践，如"ArrayList::new"等
         *
         * BiConsumer<R,? super T> accumulator：该函数接口参数类型是二元组泛型，
         * 其中"R"表示返回类型，"T"表示Stream的元素类型。
         * 接口方法"accept(R r, T t)"的目的是建立目标容器与Stream元素之间的收集关系。
         * 例如我们要将Stream转化成List，则accept方法内部应该是将Stream中的元素加入到List中。
         *
         *  BiConsumer<R,R> combiner：该函数接口的目的是将多个容器内的元素叠加到一起。
         *
         *  在单线程的情况下，参数supplier和accumulator就可以实现collect到目标容器的收集转化操作。
         *  最后一个参数combiner起到了多个容器累加的作用, 一般情况下我们创建或者获取的Stream是一个顺序流，该参数是没有效果的，
         *  我们是可以忽略该参数的。如果Stream是一个通过集合类的parallelStream方法生成的并行流，则该参数是有效的。
         */
        //简单使用
        Integer[] arrs = {12, 123, 12, 434, 65, 65, 22, 31, 21, 3, 12};

        List<Integer> lists = Stream.of(arrs).collect(ArrayList::new, ArrayList::add, ArrayList::addAll);
        System.out.println("lists" + lists);

        /**
         * 把Stream<Integer> 转换为String
         * 需要注意String类型的值不能修改,值修改意味着重新引用,所以不要使用不能修改的类型作为返回容器
         */
        String str = Stream.of(arrs).collect(StringBuffer::new,
                (e1, e2) -> e1.append(e2).append(","),
                (e3, e4) -> e3.append(e4)).toString();
        System.out.println("转换为String: " + str);

        /**
         * collect 实现filter
         */
        //判断是否为偶数
        Predicate<Integer> pre = (i) -> i % 2 == 0 ? true : false;
        List<Integer> result3 = Stream.of(arrs).collect(ArrayList::new, (e1, e2) -> {
            if (pre.test(e2)) {
                e1.add(e2);
            }
        }, (ls1, ls2) -> ls1.addAll(ls2));
        System.out.println("collect 实现filter" + result3);

        /**
         * collect 实现map
         */
        List<Integer> result4 = Stream.of(arrs).collect(ArrayList::new, (e1, e2) ->
                        e1.add(e2 * e2)
                , (ls1, ls2) -> ls1.addAll(ls2));
        System.out.println("平方 " + result4);

        List<Map<String, String>> mapList = new ArrayList<Map<String, String>>();
        Map<String, String> map1 = new HashMap<String, String>();
        map1.put("age", "11");
        map1.put("sex", "0");

        Map<String, String> map2 = new HashMap<String, String>();
        map2.put("age", "12");
        map2.put("sex", "1");

        Map<String, String> map3 = new HashMap<String, String>();
        map3.put("age", "13");
        map3.put("sex", "1");

        Map<String, String> map4 = new HashMap<String, String>();
        map4.put("age", "14");
        map4.put("sex", "0");

        mapList.add(map1);
        mapList.add(map2);
        mapList.add(map3);
        mapList.add(map4);

        mapList.stream().collect(ArrayList::new,
                (ls, e) -> ls.add(" new map " + e.get("age")),
                ArrayList::addAll).forEach(System.out::print);


        System.out.println();

        /**
         * Stream默认为顺序流（单线程梳理流），collect方法的第三个参数combiner并没有实际的意义，
         * 但在并行流的环境下，该参数必须实现容器累加的算法，
         * 否则最终收集的元素的容器会出现丢失元素的情况，如下面示例所示。
         * 如果写成 (ls1, ls2)->{} 则元素显示不全 (ls1, ls2)->{ls1.addAll(ls2)}
         */
        List<Integer> demoList = Arrays.asList(1, 2, 3, 4, 5, 6, 7, 8, 9);
        List<Integer> newdemoList = demoList.parallelStream().collect(ArrayList::new, (ls, e) -> ls.add(e * e), (ls1, ls2) -> {
        });
        newdemoList.forEach(System.out::println);
        System.out.println(newdemoList.size());


        /**
         * 归纳操作reduce
         * reduce方法也称归纳方法，它可以将Stream中的元素归纳成一个对象，
         * 在归纳的过程中也可以对Stream中的元素进行操作，从方法描述上来看，
         * reduce是可以代替collect方法。Stream中的reduce方法共有三个重载方法
         * 1.reduce(BinaryOperator<T> accumulator)
         *
         * 2.reduce(T identity, BinaryOperator<T> accumulator)
         *
         * 3.reduce(U identity, BiFunction<U,? super T,U> accumulator, BinaryOperator<U> combiner)
         */
        Optional<Integer> optional = demoList.stream().reduce((e1, e2) -> {
            System.out.println(" e1= " + e1);
            System.out.println(" e2= " + e2);
            return e1 + e2;
        });

        System.out.println(optional.get());


        Integer sum = demoList.stream().reduce(-10, (e1, e2) -> e1 + e2);
        System.out.println(sum);

        ArrayList<Integer> newlist = demoList.stream().reduce(new ArrayList<Integer>(), (ls, e) -> {
            ls.add(e*e);
            return ls;
        }, (ls1, ls2) -> {
            ls1.addAll(ls2);
            return ls1;
        });

        newlist.forEach(System.out::println);

        /**
         * Collectors 实现了接口 Collector<T,A,R>
         * T: 需要进行reduce操作的元素类型
         * A:reduce操作的动态集合类型
         * R:reduce操作的结果类型
         */

        //将map中的年龄转换为list
        List<String> ageList = mapList.stream().map(e->e.get("age")).collect(Collectors.toList());
        ageList.forEach(System.out::println);

        //将集合中的性别转换为set
        Set<String> sexSet = mapList.stream().map(e->e.get("sex")).collect(Collectors.toCollection(TreeSet::new));
        sexSet.forEach(System.out::println);

        //将年龄拼接成一个字符串
        String joinStr = mapList.stream().map(e->e.get("age")).collect(Collectors.joining("+"));
        String joinStr2 = mapList.stream().map(e->e.get("age")).collect(Collectors.joining(""));
        System.out.println(joinStr);
        System.out.println(joinStr2);

        //计算年龄总和
        int total = mapList.stream().collect(Collectors.summingInt(e->Integer.parseInt(e.get("age"))));
        System.out.println(total);

        //根据性别分组
        Map<String, List<Map<String, String>>> sexGroup = mapList.stream().collect(Collectors.groupingBy(e-> e.get("sex")));
        System.out.println(sexGroup);

        //计算不同性别的年龄
        Map<String, Integer> sexGroupSumAge = mapList.stream().collect(Collectors.groupingBy(e->e.get("sex"), Collectors.summingInt(e-> Integer.parseInt(e.get("age")))));
        System.out.println(sexGroupSumAge);

    }
```

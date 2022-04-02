[TOC]



# 集合框架

![image-20210722165807460](C:\Users\PC\AppData\Roaming\Typora\typora-user-images\image-20210722165807460.png)

## Collection

### Collection的部分方法

|                                  方法 |                                       描述 |
| ------------------------------------: | -----------------------------------------: |
|                        int **size()** |                           获取**集合长度** |
|                 boolean **isEmpty()** |                       判断**集合是否为空** |
|        boolean **contains(Object o)** |             判断**集合中是否存在某个对象** |
|               Iterator **iterator()** |           实例化Iterator接口，**遍历集合** |
|                Object[] **toArray()** |             **将集合转换为一个Object数组** |
|                T[] **toArray(T[] a)** |     **将集合转换为一个指定数据类型的数组** |
|                  boolean **add(E e)** |                       向集合中**添加元素** |
|          boolean **remove(Object o)** |                       从集合中**删除元素** |
| boolean **containsAll(Collection c)** | 判断**集合中是否存在另一个集合的所有元素** |
|      boolean **addAll(Collection c)** |         **向集合中添加某个集合的所有元素** |
|   boolean **removeAll(Collection c)** |         **从集合中删除某个集合的所有元素** |
|                      void **clear()** |                   **清除集合中的所有元素** |
|      boolean **equals(Collection c)** |                   **判断两个集合是否相等** |
|                    int **hashCode()** |                       返回**集合的哈希值** |





### List	有序	可重复	可实现堆、栈、队列

#### ArrayList	数组	非线程安全

实现了长度可变的**数组**	**存储空间连续** -> 读取快，增删慢	**非线程安全**

```java
ArrayList<T> list = new ArrayList<>();
```

|                                         方法 |                                             描述 |
| -------------------------------------------: | -----------------------------------------------: |
|                         T **get(int index)** |             通过下标**返回集合中对应位置的元素** |
|              T **set(int index, T element)** |                   **在集合中的指定位置存入对象** |
|                    int **indexOf(Object o)** |           **从前向后查找某个对象在集合中的位置** |
|                int **lastIndexOf(Object o)** |           **从后向前查找某个对象在集合中的位置** |
|              ListIterator **listIterator()** |     实例化ListIterator接口，用来**遍历List集合** |
| List **subList(int fromIndex, int toIndex)** | **通过下标截取List集合**，从fromIndex到toIndex-1 |



##### int[] 与 ArrayList 互相转换

```java
int[] temp={73,74,75,71,69,72,76,73};
//将 int[] 转换为 ArrayList
ArrayList<Integer> list1 = (ArrayList<Integer>) Arrays.stream(temp).boxed().collect(Collectors.toList());
//将 ArrayList 转换
int[] temp1=list1.stream().mapToInt(Integer::valueOf).toArray();
```



#### Vector	数组	线程安全

实现了长度可变的**数组**	**线程安全**，效率低



#### Stack	栈

实现了**栈**的数据结构	Vector的子类

+ **push**  入栈
+ **peek**  取出栈顶元素，但不删除栈顶元素，而是**复制**一份取出
+ **pop**    取出栈顶元素，并将栈顶元素**删除**

使用Deque实现堆栈

```java
Deque<T> stack=new LinkedList<T>();					//使用双端队列，将Deque用作栈
//push		boolean offerFirst(E e)		或		void addFirst(E e)
//pop		E pollFirst(E e)			或		E removeFirst()
//peek		E peekFirst(E e)			或		E getFirst()
```

使用LinkedList实现堆栈

```java
LinkedList<T> stack = new LinkedList<T>();			//使用双向链表，将LinkedList用作栈
//push		void addFirst(E e)			或		boolean offerFirst()
//pop		E removeFirst() 			或 		E pollFirst()
//peek		E getFirst() 				或 		E peekFirst()
```



#### LinkedList	链表

采用**链表**的形式存储	实现了队列、堆栈、双端队列	**存储空间不连续** -> 增删快，读取慢

|                                                方法 |                         描述 |
| --------------------------------------------------: | ---------------------------: |
| void **addFirst(E e)**  /  void **offerFirst(E e)** |       **添加元素到起始位置** |
|   void **addLast(E e)**  /  void **offerLast(E e)** |       **添加元素到结束位置** |
|           E **removeFirst()**  /  E **pollFirst()** | **移除并返回起始位置的元素** |
|             E **removeLast()**  /  E **pollLast()** | **移除并返回结束位置的元素** |
|              E **getFirst()**  /  E **peekFirst()** |       **获取起始位置的元素** |
|                E **getLast()**  /  E **peekLast()** |       **获取结束位置的元素** |

+ LinkedList的pop方法是先进先出，pop取出的是第一个元素





### Queue	队列	有序	可实现堆、栈

Queue中应该使用专门的方法来进行元素操作，在操作失败时会弹出错误

|         功能         | Queue中应该使用的 | Collection中提供的 |
| :------------------: | :---------------: | :----------------: |
|     **添加元素**     |    **offer()**    |       add()        |
|     **移除元素**     |    **poll()**     |      remove()      |
| **取出元素但不删除** |   **element()**   |       peek()       |



#### LinkedList实现队列

LinkedList实现了Queue接口，因此可以将Linked当作Queue来用

```java
Queue<T> queue = new LinkedList<T>();
```

#### Deque	双端队列	可用作栈	可用作队列

```java
Deque<T> stack=new LinkedList<T>();					//使用双向链表，将Deque用作栈
Deque<T> queue=new ArrayDeque<T>();					//使用双端队列，将Deque用作队列
```

#### PriorityQueue	优先队列	堆排序	默认升序

默认排序为升序，每次poll()或peek()出一个最小值

```java
PriorityQueue<T> queue = new PriorityQueue<T>();
```

通过实现 Comparable接口 并且重写 compareTo方法 来改变排序顺序

```java
PriorityQueue<Integer> priorityQueueDown =new PriorityQueue<>(new Comparator<Integer>() {
    @Override
    public int compare(Integer o1, Integer o2) {
       	return o2-o1;									//改为降序，其他排序方式也可按此自定义
    }
});
```

或使用Collections工具类

```java
PriorityQueue<T> priorityQueueDown =new PriorityQueue<>(Collections.reverseOrder());
```



### Set	无序	唯一	无索引

#### HashSet	无序

```java
HashSet<T> set = new HashSet();
```

#### LinkedHashSet	有序

```java
LinkedHashSet<T> linkedHashSet = new LinkedHashSet();
```

#### TreeSet	有序	默认升序

```java
TreeSet<T> treeSet = new TreeSet();
```

通过实现 Comparable接口 并且重写 compareTo方法 来改变排序顺序

```java
TreeSet<Integer> integerTreeSet=new TreeSet<>(new Comparator<Integer>() {
    @Override
    public int compare(Integer o1, Integer o2) {
        return o2-o1;									//改为降序，其他排序方式也可按此自定义
    }
});
```

或使用collections工具类

```java
TreeSet<T> integerTreeSet=new TreeSet<>(Collections.reverseOrder());
```





## Map

### Map	无序	键值对	Key唯一

### Map的部分方法

|                                    方法 |                                             描述 |
| --------------------------------------: | -----------------------------------------------: |
|                          int **size()** |                                 获取**集合长度** |
|                   boolean **isEmpty()** |                             判断集合**是否为空** |
|     boolean **containsKey(Object key)** |                    判断集合中**是否存在某个Key** |
| boolean **containsValue(Object value)** |                  判断集合中**是否存在某个Value** |
|                   V **get(Object key)** |                     取出集合中**Key对应的Value** |
|               V **put(K key, V value)** | **存入一组key-value**的元素，如果 key 相同则替换 |
|                V **remove(Object key)** |                     **删除**集合中key对应的value |

#### HashMap	非线程安全

非线程安全，但性能较高

```java
HashMap<T> hashMap = new HashMap<>();
```

#### Hashtable	线程安全

线程安全，但性能较低

```java
Hashtable<T> hashtable = new Hashtable<>();
```

#### TreeMap	有序	默认升序

有序	默认升序	

```java
TreeMap<T> treeMap = new TreeMap();
```

使用collections工具类进行反序

```java
TreeMap treeMap =new TreeMap(Collections.reverseOrder());
```



### entrySet

entrySet 是 java 中键值对的集合，Set 中的类型是 Map.Entry，一般可以通过 map.entrySet()得到

entrySet 实现了 Set接口，里面存放的是键值对，一个 K 对应一个 V



### keySet

另一种是 keySet，它是键的集合，Set 里面的类型即 key 的类型



### 4种遍历Map的方式

#### 1.普遍使用，二次取值

```java
for (String key : map.keySet()) {
	System.out.println("key= "+ key + " and value= " + map.get(key));
}
```



#### 2.通过 Map.entrySet 使用 iterator 遍历

```java
Iterator<Map.Entry<String, String>> it = map.entrySet().iterator();
while (it.hasNext()) {
	Map.Entry<String, String> entry = it.next();
	System.out.println("key= " + entry.getKey() + " and value= " + entry.getValue());
}
```

 

#### 3.通过 Map.entrySet 的 getKey() 和 getValue()

```java
for (Map.Entry<String, String> entry : map.entrySet()) {
	System.out.println("key= " + entry.getKey() + " and value= " + entry.getValue());
}
```



#### 4.Map.values()遍历所有的value，但不能遍历key

```java
for (String v : map.values()) {
	System.out.println("value= " + v);
}
```





## Collections	针对集合的工具类

|                                                         方法 |                                        描述 |
| -----------------------------------------------------------: | ------------------------------------------: |
|                             static void  **sort(List list)** |                          对集合进行**排序** |
|             static int **binarySearch(List list, Object v)** | **查找v在list中的位置，集合必须是升序排列** |
|                         static **get(List list, int index)** |                 **返回list中index位置的值** |
|                           static void **reverse(List list)** |                      对list进行**反序输出** |
|                static void **swap(List list, int i, int j)** |            **交换**集合中指定位置的两个元素 |
|                  static void **fill(List list, Object obj)** |               **将集合中所有元素替换成obj** |
|                             static Object **min(List list)** |                      返回集合中的**最小值** |
|                             static Object **max(List list)** |                      返回集合中的**最大值** |
| static boolean **replaceAll(List list, Object oldVal, Object newVal)** |    在list集合中**用newVal替换全部的oldVal** |
|          static boolean **allAll(List list, Object... obj)** |                        向集合中**添加元素** |



### 使 Collections.sort 能够自定义排序

```java
Collections.sort(goodsList, new Comparator<ArrayList<Integer>>() {
    @Override
    public int compare(ArrayList<Integer> o1, ArrayList<Integer> o2) {
        if (o2.get(0)-o1.get(0)<0){
            return -1;
        }else if (o2.get(0)-o1.get(0)==0){
            return o2.get(1)-o1.get(1);
        }else {
            return 1;
        }
    }

});
```





## Arrays	针对数组的工具类

|                                                      方法 |                                                         描述 |
| --------------------------------------------------------: | -----------------------------------------------------------: |
|                             static void **sort(array[])** |         对传入数组进行**排序**，默认从小到大，原数组发生改变 |
|                        static List<T> **asList(array[])** |                 把数组或多个数字（用，分隔）**转成list集合** |
|                       static String **toString(array[])** |                                         **方便打印数组内容** |
| static String **deepToString(array&#91;&#93;&#91;&#93;)** |                               **方便打印二维、多维数组内容** |
|       static T[] **copyOf(T[] original, int new length)** | **以original数组为基础，返回一个新数组，大小为length**，多的补0，少的截断 |
|             static void **fill(*long* [] a, *long* val)** |                              **将数组中所有的元素替换为val** |
|      static int **binarySearch(*long* [] a, *long* key)** |              **查找key在数组中的位置，数组必须经过升序排序** |





## 使一个对象具有排序功能

### 对象实现 Comparable接口

```java
public class User implements Comparable
```

### 重写 compareTo方法

```java
@Override
public int compareTo(Object o) {						//以User的age为例
    if (o instanceof User){						
        User user=(User) o;
        if (this.age>user.age){
            return 1;
        }else if (this.age==user.age){					//通过compareTo的返回值大于1、等于1、小于1来呈现结果
            return 0;
        }else {
            return -1;
        }
    }
}
```



### 对象实现Comparator接口

### 重写 compare 方法

```java
Collections.sort(goodsList, new Comparator<ArrayList<Integer>>() {
    @Override
    public int compare(ArrayList<Integer> o1, ArrayList<Integer> o2) {
        if (o2.get(0)-o1.get(0)<0){
            return -1;
        }else if (o2.get(0)-o1.get(0)==0){
            return o2.get(1)-o1.get(1);
        }else {
            return 1;
        }
    }
    
});
```



# 泛型

## 不确定的类型	使用泛型

当有数据类型、返回值类型、参数类型不确定时，**定义时使用泛型，在创建对象时赋给类型**

```java
public class A<E, T, C> {							//创建对象时定义泛型
	private E value;
	public T getValue(){
		return value;
	}
	public void setValue(C value){}
}
```

```java
A<Integer, String, Float> a = new A<>();			//在创建对象时赋给类型
```

### 使用泛型通配符	<?>

泛型通配符表示可以使用任意的泛型类型对象，例如：

```java
public static void test(ArrayList<?> list){
	System.out.println(list);
}
```

### 泛型上限和下限

泛型上限使泛型的传入类型只能是限定类型它本身或者它的子类，如：

```java
public static void test(Time<? extends 上限类名> time){}
```

泛型下限使泛型的传入类型只能是限定类型它本身或者它的父类，如：

```java
public static void test(Time<? super 下限类名> time){}
```





# 实用类

## 枚举类	可以用来限定值的范围

```java
public enum Week {				//定义这个枚举相当于创建了7个成员变量，然后将它们装在一个数组中
    MONDAY,
    TUESDAY,
    WEDNESDAY,
    THURSDAY,
    FRIDAY,
    SATURDAY,
    SUNDAY
}
```



## Math	数学方法类

math为开发者提供了一系列数学方法，同时还提供了两个静态常量E（自然对数的底数）和Pi（圆周率）

|                                   方法 |                                                    描述 |
| -------------------------------------: | ------------------------------------------------------: |
|       static double **sqrt(double a)** |                                       计算a的**平方根** |
|       static double **cbrt(double a)** |                                       计算a的**立方根** |
|            static double **pow(a, b)** |                                        计算**a的b次方** |
| static *int* **max(*int* a, *int* b)** |                                  在a和b中返回**最大值** |
| static *int* **min(*int* a, *int* b)** |                                  在a和b中返回**最小值** |
|             static double **random()** | 返回一个**随机数**，其范围是 **0 =< Math.random < 1.0** |
|       static double **ceil(double a)** |  **返回大于等于（>=）给定参数的最小整数，类型为double** |
|      static double **floor(double a)** |  **返回小于等于（<=）给定参数的最大整数，类型为double** |
|       static double **rint(double a)** |            **返回与参数最接近的整数。返回类型为double** |
|          static *int* **abs(*int* a)** |                                       返回a的**绝对值** |



## Random	随机数方法类

使用Random类来生成随机数，可以指定区间

|                             方法 |                                                         描述 |
| -------------------------------: | -----------------------------------------------------------: |
|              public **Random()** | 创建一个无参的随机数**构造器**，**使用系统时间作为默认种子** |
|     public **Random(long seed)** |           **使用long数据类型的种子**创建一个随机数**构造器** |
| public boolean **nextBoolean()** |                              返回一个**boolean类型**的随机数 |
|   public double **nextDouble()** | 返回一个**double类型**的随机数，范围在 **[ 0.0 - 1.0 )** 之间 |
|     public float **nextFloat()** | 返回一个**float类型**的随机数，范围在 **[ 0.0 - 1.0 )** 之间 |
|         public int **nextInt()** |                                  返回一个**int类型**的随机数 |
|        public int **nextInt(n)** |       返回一个**int类型**的随机数，其范围在 **[ 0-n )** 之间 |
|       public long **nextLong()** |  返回一个**long类型**的随机数，范围在 **[ 0.0 - 1.0 )** 之间 |

### 使用时间戳+随机数的编号

```java
System.currentTimeMillis()+random.nextInt(100000)+100000
```





## String类

string的两种创建方式：

1. 直接赋值

```java
String str1 = "Hello";
String str2 = "Hello";
//str1 == str2 的返回值将会是 true
//原因是直接赋值会先在堆内存的字符串常量池中寻找值
```

<img src="C:\Users\PC\AppData\Roaming\Typora\typora-user-images\image-20210718102815049.png" alt="image-20210718102815049" style="zoom:67%;" />

2. 使用构造器

```java
String str3 = new String("World");
String str3 = new String("World");
//使用构造器将会创建新的空间
```

<img src="C:\Users\PC\AppData\Roaming\Typora\typora-user-images\image-20210718103204205.png" alt="image-20210718103204205" style="zoom:67%;" />

### String常用方法

|                                                      方法 |                                                         描述 |
| --------------------------------------------------------: | -----------------------------------------------------------: |
|                                       public **String()** |                                   **创建一个空的字符串对象** |
|                           public **String(String value)** |                            **创建一个值为value的字符串对象** |
|                           public **String(char value[])** |                           **将一个char数组转换为字符串对象** |
|    public **String(char value[], int offset, int count)** | **将一个指定范围的char数组转换为字符串对象（起始下标+长度）** |
|                           public **String(byte value[])** |                           **将一个byte数组转换为字符串对象** |
|    public **String(byte value[], int offset, int count)** | **将一个指定范围的byte数组转换为字符串对象（起始下标+长度）** |
|                                   public int **length()** |                                         获取字符串的**长度** |
|                              public boolean **isEmpty()** | 判断字符串**是否为空**（null指对象不存在，而空是指对象没有内容、长度为0） |
|                         public char **charAt(int index)** |                                       **返回指定下标的字符** |
|                              public byte[] **getBytes()** |                                 **返回字符串对应的byte数组** |
|                public boolean **equals(Object anObject)** |                                 判断两个字符串值**是否相等** |
|      public boolean **equalsIgnoreCase(Object anObject)** |                   判断两个字符串值**是否相等（忽略大小写）** |
|                    public int **compareTo(String value)** | **比较两个字符串的大小**，返回正数为大于、返回0为等于、返回负数为小于 |
|          public int **compareToIgnoreCase(String value)** | **忽略大小写，比较两个字符串的大小**，返回正数为大于、返回0为等于、返回负数为小于 |
|               public boolean **startsWith(String value)** |                                  判断字符**是否以value开头** |
|                 public boolean **endsWith(String value)** |                                  判断字符**是否以value结尾** |
|                                 public int **hashCode()** |                                       返回字符串的**hash值** |
|                        public int **indexOf(String str)** |                                返回**str在字符串的首个位置** |
|         public int **indexOf(String str, int fromIndex)** |                    从指定位置**查找**str在字符串中的收个位置 |
|               public String **subString(int beginIndex)** |                               **截取**从指定位置开始的字符串 |
| public String **subString(int beginIndex, int endIndex)** |               **截取**区间 [ beginIndex, endIndex ) 的字符串 |
|                      public String **concat(String str)** |                                            **追加**字符串str |
|          public String **replaceAll(String o, String n)** |                              将字符串中所有的 o **替换**为 n |
|                   public String[] **split(String regex)** |                   用指定的字符串对目标进行**分割**，返回数组 |
|                                  public String **trim()** |                                   **删除字符串的头尾空白符** |
|                           public String **toLowerCase()** |                                                 **转为小写** |
|                        public String to **toUpperCase()** |                                                 **转为大写** |
|                           public char[] **toCharArray()** |                                   **将字符串转换为字符数组** |
|                   public boolean **mathes(String regex)** |                       判断字符串**是否匹配给定的正则表达式** |





## StringBuffer类	线程安全

**String对象一旦创建，值就不能修改了**（原来的值不能修改，一旦修改就是一个新的对象了。只要有改动，就会创建一个新的对象。根本原因在于String底层是用数组来存储的，数组长度一旦创建就不可修改）

**StringBuffer可以解决String频繁修改造成的空间资源浪费的问题**

**StringBuffer底层也是使用数组来存储**

+ 使用无参构造函数来创建对象，StringBuffer数组的默认长度为16
+ 使用有参构造创建对象，StringBuffer数组长度 = 值的长度 + 16

StringBuffer在超过了预留空间后不会重新开辟一块新的内存区域，而是在原有的基础上进行扩容，ensureCapacity()方法通过调用父类方法ensureCapacityInternal()方法对底层数组进行扩容，保持引用不变

### StringBuffer常用方法

其中返回值类型为StringBuffer的会直接修改对象本身

|                                                         方法 |                                        描述 |
| -----------------------------------------------------------: | ------------------------------------------: |
|                                    public **StringBuffer()** |            **创建**一个空的StringBuffer对象 |
|                          public **StringBuffer(String str)** |       **创建**一个值为str的StringBuffer对象 |
|                         public synchronized int **length()** |              返回StringBuffer的值的**长度** |
|               public synchronized char **charAt(int index)** |                      返回**指定位置的字符** |
|      public synchronized StringBuffer **append(String str)** |                             **追加**内容str |
| public synchronized StringBuffer **delete(int start, int end)** |            **删除**区间 [start, end) 的字符 |
| public synchronized StringBuffer **deleteCharAt(int index)** |                      **删除**指定位置的字符 |
| public synchronized StringBuffer **replace(int start, int end, String str)** |      将区间 [start, end) 的值**替换**为 str |
|           public synchronized String **subsring(int start)** |            **截取**从指定位置到结尾的字符串 |
| public synchronized String **substring(int start, int end)** |        **截取**区间 [start, end) 中的字符串 |
| public synchronized StringBuffer **insert(int offset, String str)** |                       在指定位置**插入**str |
|                           public int **indexOf(String str)** |      在字符串中**查找指定字符（串）的位置** |
|            public int **indexOf(String str, int fromIndex)** | 从fromIndex开始**查找指定字符（串）的位置** |
|               public synchronized StringBuffer **reverse()** |                              **反转字符串** |
|                    public synchronized String **toString()** |                将StringBuffer**转为String** |





## StringBuilder类	非线程安全

功能同早期版本的StringBuffer一样，只是StringBuilder是非线程安全的





## Date类

**Date对象表示当前的系统时间**

使用SimpleDateFormat进行格式化：

```java
Date date = new Date();
SimpleDateFormat simpleDateFormat = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
       String str = simpleDateFormat.format(date);			

//h代表12小时制的，H代表24小时制
//输出为2021-07-15 14:36:26
```





## Calendar类

**Calendar用来完成日期数据的逻辑运算，相当于一个日期计算器**

方法：

1. 将日期数据传给Calendar

```java
Calendar calendar = Calendar.getInstance();			//获取一个Calendar实例，默认为当前时间
calendar.set(Calendar.YEAR,2020);					//传入年	
calendar.set(Calendar.MONTH,3);						//传入月
calendar.set(Calendar.DAY_OF_MONTH,9);				//传入日
```

2. 调用相关方法进行运算



### Calendar的常量

|                                 常量 |                 描述 |
| -----------------------------------: | -------------------: |
|         public static final int YEAR |                   年 |
|        public static final int MONTH |                   月 |
| public static final int DAY_OF_MONTH | 天，一个月中的第几天 |
|  public static final int DAY_OF_YEAR |   天，一年中的第几天 |
|  public static final int HOUR_OF_DAY |                 小时 |
| public static final int WEEK_OF_YEAR |   周，一年中的第几周 |
|       public static final int MINUTE |                 分钟 |
|       public static final int SECOND |                   秒 |
|  public static final int MILLISECOND |                 毫秒 |

### Calendar的相关方法

|                                  方法 |                     描述 |
| ------------------------------------: | -----------------------: |
|  public static Calendar getInstance() |   获取Calendar实例化对象 |
| public void set(int field, int value) |           给静态常量赋值 |
|             public int get(int field) |         获取静态常量的值 |
|           public final Data getTime() | 将Calendar转换为Date对象 |



# 文件操作

## File类操作文件

java.io.File，使用该类的构造函数就可以创建文件对象，将硬盘中的一个具体的文件以java对象的形式来表示

### File类常用方法

|                                   方法 |                               描述 |
| -------------------------------------: | ---------------------------------: |
|       public **File(String pathname)** |               根据路径**创建对象** |
|            public String **getName()** |                     **获取文件名** |
|          public String **getParent()** |             **获取文件所在的目录** |
|        public File **getParentFIle()** | **获取文件所在目录对应的File对象** |
|            public String **getPath()** |                   **获取文件路径** |
|            public boolean **exists()** |               判断**文件是否存在** |
|       public boolean **isDirectory()** |             判断**对象是否为目录** |
|            public boolean **isFile()** |             判断**对象是否为文件** |
|               public long **length()** |                 获取**文件的大小** |
|     public boolean **createNewFile()** |         根据当前对象**创建新文件** |
|            public boolean **delete()** |                   **删除文件对象** |
|             public boolean **mkdir()** |         根据当前对象**创建新目录** |
| public boolean **renameTo(File file)** |           为已存在的对象**重命名** |

创建文件对象

```java
File file=new File("C:/Users/PC/Desktop/test111.txt");			
//要写绝对路径
//file对象无法判断文件是否存在，可以使用 file.exist() 进行判断
System.out.println(file.exists());
try {
	System.out.println(file.createNewFile());
} catch (IOException e) {
    e.printStackTrace();
    System.out.println("创建文件失败！");
}
```

删除文件对象

```java
file.delete();
```

重命名文件对象

```java
file.renameTo(new File("C:/Users/PC/Desktop/test112.txt"));
```





## 输入输出I/O流	传递文件

+ 按照方向分——输入流和输出流
+ 按照单位分——字节流和字符流
+ 按照功能分——节点流和处理流



### 字节流	非文本类型数据只能使用字节流

按照方向分为输入字节流和输出字节流，每个单位时间内处理一个字节的数据

InputStream——将数据写入到java程序中

OutputStream——从java程序向外写出数据

**在UTF-8编码下：**

**英文 —— 1个字符 = 1个字节**

**中文 —— 1个字符 = 3个字节**

#### InputStream常用方法

使用抽象类InputStream的子类FileInputStream

```java
InputStream inputStream = new FileInputStream(url);
```



|                                     方法 |                                                         描述 |
| ---------------------------------------: | -----------------------------------------------------------: |
|                           int **read()** | 以字节为单位**读取数据**，返回字节的int值，到文件结尾则返回-1 |
|                   int **read(byte b[])** | **将数据存入byte类型的数组中**，返回读取到的字节数，到文件结尾则返回-1 |
| int **read(byte b[], int off, int len)** | **将数据从 off 开始的 len 个字节存入byte数组**，返回读取到的字节数，到文件结尾则返回-1 |
|                byte[] **readAllBytes()** |               **将输入流中剩余的所有数据存入byte数组**并返回 |
|                      int **available()** |                           返回**当前数据流未读取的数据个数** |
|                         void **close()** |                                               **关闭数据流** |

#### OutputStream常用方法

使用抽象类OutputStream的子类FileOutputStream

```java
OutputStream outputStream = new FileOutputStream(url);
```

注：输出方法每次都会覆盖文件中的内容

|                                       方法 |                                             描述 |
| -----------------------------------------: | -----------------------------------------------: |
|                      void **write(int b)** |                     将**单个字节**输出数据到文件 |
|                   void **write(byte b[])** |                 将**byte数组中的字节**输出到文件 |
| void **write(byte b[], int off, int len)** | 将**byte数组中 off 开始的 len 个字节**输出到文件 |
|                           void **close()** |                                   **关闭数据流** |
|                           void **flush()** |     **将缓冲区的内容立刻写入文件，并清空缓冲区** |



### 字符流	文本类型数据可以使用字符流

按照方向分为输入字符流和输出字符流，每个单位时间内处理一个字符的数据

Reader——将数据写入到java程序中

Writer——从java程序向外写出数据

**在UTF-8编码下：**

**英文 —— 1个字符 = 1个字节**

**中文 —— 1个字符 = 3个字节**

#### Reader常用方法

使用的FileReader的继承关系是：FileReader -> InputStreamReader -> Reader

```java
Reader reader=new FileReader("C:/Users/PC/Desktop/test111.txt");
```

|                                     方法 |                                                         描述 |
| ---------------------------------------: | -----------------------------------------------------------: |
|                           int **read()** | **以字符为单位读取数据**，返回读取到的字符的int值，到文件结尾则返回-1 |
|                   int **read(char[] c)** | **以字符为单位读取数据到数组c中**，返回读取到的字符个数，到文件结尾则返回-1 |
| int **read(char[] c, int off, int len)** | **将数据从 off 开始的 len 个字符读取到数组c中**，返回读取到的字符个数，到文件结尾则返回-1 |

#### Writer常用方法

使用FileWriter的继承关系是：FileWriter -> OutputStreamWriter -> Writer

```java
Writer writer=new FileWriter("C:/Users/PC/Desktop/test222.txt");
```

|                                       方法 |                                            描述 |
| -----------------------------------------: | ----------------------------------------------: |
|                      void **write(int c)** |                       将**单个字符c**写入到文件 |
|                   void **write(char[] c)** |                   将**c数组中的字符**写入到文件 |
| void **write(char[] c, int off, int len)** |   将**c数组中 off 开始的 len 个字符**写入到文件 |
|                   void **write(String s)** |                 将**字符串s中的字符**写入到文件 |
| void **write(String s, int off, int len)** | 将**字符串s中 off 开始的 len 个字符**写入到文件 |
|                           void **flush()** |    **将缓冲区的内容立刻写入文件，并清空缓冲区** |



### 处理流

#### InputStreamReader

InputStreamReader介于InputStream和Reader之间

```java
InputStream inputStream=new FileInputStream("C:/Users/PC/Desktop/test111.txt");
InputStreamReader inputStreamReader=new InputStreamReader(inputStream);		//使用处理流
char[] chars = new char[1024];
inputStreamReader.read(chars);
inputStreamReader.close();
```

#### OutputStreamWriter

OutputStreamWriter介于OutputStream和Writer之间

```java
String str="哈哈哈哈";
OutputStream outputStream =new FileOutputStream("C:/Users/PC/Desktop/test222.txt");
OutputStreamWriter outputStreamWriter =new OutputStreamWriter(outputStream);	//使用处理流
outputStreamWriter.write(str);
outputStreamWriter.flush();
outputStreamWriter.close();
```



### 缓冲流

无论是字节流还是字符流，使用的时候对硬盘的频繁访问会对硬盘造成一定的损伤，同时效率也不高，使用缓冲流可以一次性从硬盘读取部分数据存入缓冲区，再写入内存，这样就可以有效减少对硬盘的直接访问

**缓冲流属于处理流**，节点流和处理流的区别是：

+ 节点流使用的时候可以直接对接到文件对象File
+ 处理流使用的时候不可以直接对接到文件对象File，要建立在字节流的基础上才能创建

<img src="C:\Users\PC\AppData\Roaming\Typora\typora-user-images\image-20210719152945326.png" alt="image-20210719152945326" style="zoom:67%;" />

**一般字节流不使用缓冲流（BufferedInputStream和BufferedOutputStream），而字符流用缓冲流读写**

#### BufferInputStream

```java
InputStream inputStream=new FileInputStream("C:/Users/PC/Desktop/test111.txt");
BufferedInputStream bufferedInputStream=new BufferedInputStream(inputstream);
String str=bufferedInputStream.read();
bufferedInputStream.close();
inputStream.close();
```

#### BufferOutputStream

```java
OutputStream outputStream=new FileOutputStream("C:/Users/PC/Desktop/test111.txt");
BufferedOutputStream bufferedOutputStream=new BufferedOutputStream(inputstream);
String str=null;
bufferedOutputStream.write(str);
bufferedOutputStream.flush()
bufferedOutputStream.close();
outputStream.close();
```

#### BufferedReader

```java
Reader reader=new FileReader("C:/Users/PC/Desktop/test111.txt");
BufferedReader bufferedReader=new BufferedReader(reader);
String str=null;
System.out.println(bufferedReader.readLine());				//使用 readLine() 读取一行
bufferedReader.close();
reader.close();
```

#### BufferedWriter

**实际使用中在关闭输出缓冲流之前，需要调用flush方法！！！**

```java
Writer writer=new FileWriter("C:/Users/PC/Desktop/test222.txt");
BufferedWriter bufferedWriter=new BufferedWriter(writer);
String str1="Product Collection\n" +
        "Intel® Killer™ Wireless Products\n" +
        "\n" +
        "Code Name\n" +
        "Products formerly Typhoon Peak\n" +
        "\n" +
        "Status\n" +
        "Launched\n" ;
bufferedWriter.write(str1);
bufferedWriter.flush();						//将缓冲区的内容立刻写入文件，并清空缓冲区
bufferedWriter.close();
writer.close();
```



### 序列化和反序列化

#### 序列化

序列化就是将内存中的对象输出到硬盘文件中保存

1. 实体类需要实现序列化接口 Serializable

```java
public class Person implements Serializable{...}
```

2. 将实体类对象进行序列化处理，通过数据流写入到文件中，使用 ObjectOutputSream

```java
Person person=new Person(20, "zhangsan");
OutputStream outputStream =new FileOutputStream("C:/Users/PC/Desktop/person.txt");
ObjectOutputStream objectOutputStream=new ObjectOutputStream(outputStream);
objectOutputStream.writeObject(person);			//输出对象，内容将会是16进制字符的文件
objectOutputStream.flush();
objectOutputStream.close();
outputStream.close();
```



#### 反序列化

反序列化就是从文件中读取数据并还原成内存中的对象

```java
InputStream inputStream=new FileInputStream("C:/Users/PC/Desktop/person.txt");
ObjectInputStream objectInputStream=new ObjectInputStream(inputStream);
Person sperson= (Person) objectInputStream.readObject();	//将拿到的对象强制类型转换并赋值
```



## String类型转换成UTF-8

```java
String fileName=fileItem.getName();
byte[] utf8Bytes = fileName.getBytes("UTF-8");
String utf8FileName=new String(utf8Bytes,"UTF-8");
```



## 传送一张图片

使用带有缓冲区的传输流传输图片文件

```java
InputStream inputStream=new FileInputStream("C:\\Users\\PC\\OneDrive\\万树彬个人简历及附件\\万树彬照片.jpg");
OutputStream outputStream = new FileOutputStream("C:/Users/PC/Desktop/万树彬照片.jpg");
/*int temp=0;
while ((temp=inputStream.read())!=-1){
    outputStream.write(temp);
}*/


BufferedInputStream bufferedInputStream=new BufferedInputStream(inputStream);
BufferedOutputStream bufferedOutputStream=new BufferedOutputStream(outputStream);
int temp1=0;
while ((temp1=bufferedInputStream.read())!=-1){
    bufferedOutputStream.write(temp1);
}
bufferedInputStream.close();
inputStream.close();
bufferedOutputStream.flush();
bufferedOutputStream.close();
outputStream.close();
```





# 异常

### Exception

存在这类异常时，**编译无法通过**



### RuntimeException

**编译可以通过**，但是在执行中可能会存在一些错误

其子类有：

- **ArithmeticException										//数学运算异常**
- ClassNotFoundException                               //类没有找到
- IllegalArggumentException                            //非法的参数
- **ArrayIndexOutOfBoundsException              //数组下标越界**
- **NullPointerException                                      //空指针异常**
- NoSuchMethodException                              //没有这个方法
- NumberFormatException                              //数值格式异常



### 异常处理

#### try-catch	作用于代码段

**自动捕获** try代码块中 的异常，并运行 catch块中 的代码来处理异常

**try-catch-finally** 结构中，无论是否捕获到异常，**finally一定会执行**



#### throw	直接抛出异常

```java
public static void test(int[] array,int index) {
    if(index >= 3 || index < 0) {						//进行逻辑判断
        try {
            throw new Exception();						//在错误的逻辑中主动抛出异常
        } catch (Exception e) {
            // TODO Auto-generated catch block
            e.printStackTrace();
        }
    }else {
        System.out.println(array[index]);
    }
}
```



#### throws	作用于方法

方法定义时throws的异常如果直接**继承自Exception**，实际调用的时候需要**手动处理（捕获异常/抛给虚拟机去处理）**

方法定义时throws的异常如果**继承自RuntimeException**，调用的时候**不需要处理**

```
public static void test(String str) throws Exception {		//方法定义时抛出可能的异常
    int num = Integer.parseInt(str);
}
```





# 多线程

一个进程调度多个线程，多个线程共享资源



## 线程和任务

**线程是去抢占CPU资源，任务去执行具体业务逻辑的，线程内部会包含一个任务，线程启动（start），当抢占到资源之后，任务开始执行（run）**



## 线程的状态

线程有5种状态，在特定的情况下，县城可以在不同的状态之间切换

+ **创建状态** —— 实例化一个新的线程对象，还未启动
+ **就绪状态** —— 创建好的线程对象调用start方法完成启动，进入线程池等待抢占CPU资源
+ **运行状态** —— 线程对象获取了CPU资源，在一定时间内执行任务
+ **阻塞状态** —— 正在运行的线程暂停执行任务，释放所占用的CPU资源，并**在解除 阻塞状态 之后也不能直接回到 运行状态 ，而是重新回到 就绪状态 ，等待获取CPU资源**
+ **终止状态** —— 线程运行完毕或因为异常导致该线程终止运行

<img src="C:\Users\PC\AppData\Roaming\Typora\typora-user-images\image-20210720131909127.png" alt="image-20210720131909127" style="zoom: 80%;" />

![image-20210720163828198](C:\Users\PC\AppData\Roaming\Typora\typora-user-images\image-20210720163828198.png)



## 实现多线程的方式

**两种方式的区别：**

+ 自定义类继承 Thread类 的方式，直接在类中重写 run方法 ，使用的时候，直接实例化自定义类即可，因为 Thread类 内部存在 Runnable接口
+ 自定义类实现 Runnable接口 的方式，在实现类中重写 run 方法，使用的时候需要先创建 Thread对象 ，并将自定义类注入到 Thread对象 中，然后 Thread对象 调用 start方法



### 继承Thread类

1. 创建自定义类并继承 Thread类
2. 重写 Thread类 的 run方法，编写该线程的业务逻辑代码

```java
public class MyThread extends Thread{						//继承 Thread类
    @Override
    public void run() {										//在 run方法内写逻辑代码
        for (int i=0;i<100;i++){
            System.out.println("---mythread---");
        }
    }
}
```

3. 创建一个对象并调用 start方法 启动线程

**应使用 start方法 启动线程；run方法 调用相当于普通对象的执行，并不会抢占CPU资源**

```java
MyThread myThread1=new MyThread();
MyThread myThread2=new MyThread();
myThread1.start();									
myThread2.start();
```



### 实现Runnable接口	低耦合	推荐方式

**使用这种方式创建多线程能够降低耦合度**

1. 创建自定义类并实现Runnable接口

2. 重写 Runnable接口 的 run方法，编写该线程的业务逻辑代码

```java
public class MyRunnable implements Runnable{
    @Override
    public void run() {
        for (int i=0;i<100;i++){
            System.out.println("===myrunnable===");
        }
    }
}
```

3. 创建一个对象并将其传入 Thread类 对象的构造器中

4. thread对象 调用 start方法

```java
MyRunnable myRunnable1=new MyRunnable();
Thread thread1=new Thread(myRunnable1);
MyRunnable myRunnable2=new MyRunnable();
Thread thread2=new Thread(myRunnable2);
thread1.start();
thread2.start();
```

**或使用匿名内部类	推荐这种方式**	

```java
Thread thread=new Thread(new Runnable() {
    @Override
    public void run() {
        System.out.println("===a===");
    }
});


//也可直接创建
new Thread(new Runnable() {
            @Override
            public void run() {
                System.out.println("---innerClass---");
            }
        }).start();
```

#### 使用lambda表达式	推荐方式

```java
new Thread(()->{
    System.out.println("---lambda---");
}).start();
```



## 实现Callable接口

**与 Runnable 相比，Callable 的 call方法 有返回值，而且能抛出异常**

1. 自定义类实现 Callable接口

```java
public class CallablePrac implements Callable<String> {
    @Override
    public String call() throws Exception {
        System.out.println("callable");
        return "hello";
    }
}
```

2. 使用 FutureTask 创建线程 Thread对象

```java
public class MainClass {
    public static void main(String[] args) {
        CallablePrac callablePrac =new CallablePrac();
        FutureTask<String> stringFutureTask=new FutureTask<>(callablePrac);
        Thread thread=new Thread(stringFutureTask);
        
        thread.start();
        
        try {
            System.out.println(stringFutureTask.get());		//get方法 会使 call方法阻塞
        } catch (InterruptedException e) {						
            e.printStackTrace();
        } catch (ExecutionException e) {
            e.printStackTrace();
        }
    }
}
```

<img src="C:\Users\PC\AppData\Roaming\Typora\typora-user-images\image-20210724170226468.png" alt="image-20210724170226468" style="zoom: 80%;" />



## 线程调度

### 线程休眠	sleep()	wait()

让当前线程**暂停执行，从运行状态进入阻塞状态**，将CPU资源让给其他线程，通过 sleep() 方法来实现

**sleep方法 可以通过类调用，也可以通过对象调用，调用sleep并不会释放锁**，方法抛出 InterruptedException -> Exception，因此在外部调用时必须手动处理异常

|                                      方法 |                                         描述 |
| ----------------------------------------: | -------------------------------------------: |
| static native void **sleep(long millis)** | 使**线程休眠**，millis为休眠时间，单位为毫秒 |



**wait方法 需要通过对象来调用，调用wait将会暂时释放锁**

**wait()、notify()方法需要配合 synchronized关键字 使用，在Lock中应使用 Condition接口**

|                                     方法 |             描述 |
| ---------------------------------------: | ---------------: |
|             public final void **wait()** |     线程**等待** |
|    public final native void **notify()** | **唤醒**一个线程 |
| public final native void **notifyAll()** | **唤醒所有**线程 |





### 线程合并	join()

将指定的某个线程加入到当前线程中，**合并为一个线程，两个线程交替执行变成一个线程中的两个线程顺序执行**

通过调用 join 方法来实现合并

线程A 和 线程B， 线程A 执行到某个时间点的时候调用 线程B 的join方法 —— 表示从当前时间点开始CPU资源被 线程B 独占， 线程A 进入阻塞状态，直到 线程B 执行完毕， 线程A 进入就绪状态，等待获取CPU资源进入运行状态

**即 线程A 等待 线程B 的终止**

|                                          方法 |                                                         描述 |
| --------------------------------------------: | -----------------------------------------------------------: |
|                         final void **join()** | **使线程合并到另一个线程**，执行join的线程先独占资源，**执行完毕后释放资源** |
| final synchronized void **join(long millis)** | **使线程合并到另一个线程**，millis决定了执行join方法的线程**独占资源的最长时间** |



### 线程礼让	yield()	wait()

线程礼让是指在某个特定的时间点，让线程暂停抢占CPU资源的行为，运行状态/就绪状态 -> 阻塞状态，将CPU资源让给其他线程使用

通过调用 yield 方法来实现礼让

**线程A 和 线程B 交替执行，在某个时间点 线程A 做出了礼让，那么这个时间点 线程B 就拥有了CPU资源，执行任务，但不代表 线程A 一直暂停执行**

**线程A 只是在特定的时间节点礼让，过了时间节点， 线程A 再次进入就绪状态，和 线程B 争夺CPU资源**

|                           方法 |                                 描述 |
| -----------------------------: | -----------------------------------: |
| static native void **yield()** | **暂停当前线程**，将资源让给其他线程 |



**因为 wait() 方法能够释放锁，所以 wait() 也能够用来做线程礼让**





### 线程中断	interrupted()

线程停止运行的原因：

+ 线程执行完毕自动停止
+ 线程执行过程中遇到错误抛出异常并停止
+ **线程在执行过程中根据需求手动停止 —— 线程中断**

线程中断的常用方法：

|                               方法 |                                                         描述 |
| ---------------------------------: | -----------------------------------------------------------: |
|             public void **stop()** |                                     直接停止，**不推荐使用** |
|      public void **interrupted()** |                                     **中断**调用此方法的线程 |
| public boolean **isInterrupted()** | **获取**当前线程对象的**中断标志位**，true表示已清除标志位，当前对象已中断；false表示没有清除标志位，当前对象没有中断 |

每个线程对象都是通过一个标志位来判断当前是否为中断状态，当一个线程对象处于不同状态时，中断机制也是不同的



## 线程同步、锁

java中运行多线程并行访问，同一时间段内多个线程同时完成各自的操作

多个线程同时操作一个共享数据时，可能会导致数据不准确的问题



### synchronized关键字	锁定的是什么？

1. synchronized修饰非静态方法，锁定方法的调用者**（以对象作为区分）**，与 **synchronized(this){ }** 代码块作用相同
2. synchronized修饰静态方法，锁定的是类**（以类作为区分）**，与 **synchronized(类名.class){ }** 代码块的作用相同
3. synchronized静态方法和实例方法同时存在，静态方法锁定的是类，实例方法锁定的是对象



#### synchronize 和 ReentrantLock

+ ReentrantLock 是一个**类**，synchronized 是一个**关键字**
+ ReentrantLock 是 **JDK** 实现，synchronized 是 **JVM** 实现
+ ReentrantLock 需要**手动释放**，synchronized 可以**自动释放锁**



#### synchronized 关键字修饰方法实现线程同步

每个java对象都有一个内置锁，内置锁会保护使用synchronized关键字修饰的方法，要调用该方法就必须先获得锁，否则就处于阻塞状态

```java
public class MyRunnable implements Runnable{
    @Override
    public synchronized void run() {						//使用synchronized关键字
        for (int i=0;i<100;i++){
            System.out.println("===myrunnable===");
        }
    }
}
```

synchronized方法可以修饰实例方法，也可以修饰静态方法，但是给修饰实例方法时并不能对其实现线程同步，原因是实例方法会创建多个单独对象，它们并不共享资源



#### 使用 synchronized代码块 实现线程同步

必须锁定唯一的元素才能实现同步

```java
public static void test(){
    synchronized (ThreadPrac.class){						//锁定ThreadPrac类
        System.out.println("synchronized");
    }
}
```



### volatile关键字

volatile使用内存屏障来保证**避免指令重排序**以及保证**内存可见性**（**越过工作内存，直接访问主内存**，这样一来所有线程都能看到共享内存的最新状态）



### Lock实现线程同步

Lock是一个接口，用来实现线程同步，功能和synchronized一样

Lock使用频率最高的实现类	**ReentrantLock	重入锁**，可以重复上锁

```java
public class LockPrac implements Runnable{
    private static int num;
    private Lock lock=new ReentrantLock();				//创建Lock对象

    @Override
    public void run() {
        lock.lock();									//调用lock()方法上锁
        lock.lock();									//重复上锁
        num++;
        System.out.println(num);
        lock.unlock();									//调用unlock()方法解锁
        lock.unlock();									//解锁重复上锁
    }
}
```



#### Lock 和 synchronized 

1. synchronized **自动上锁，自动释放锁**，Lock **手动上锁，手动释放锁**
2. synchronized **无法判断是否获取到了锁**，Lock **可以判断是否获取到了锁**
3. synchronized 拿不到锁就**会一直等待**，Lock **不一定会一直等待**
4. synchronized 是java**关键字**，Lock 是**接口**
5. synchronized 是**非公平锁**，Lock **可以设置**是否为公平锁

实际开发中推荐使用 Lock



#### Lock中不能使用wait()、notify()，应使用 Condition 接口

**wait()、notify() 需要搭配 synchronized关键字 使用，Lock 中应该使用 Condition接口**

```java
Condition condition=lock.newCondition();
```

|                 方法 |             描述 |
| -------------------: | ---------------: |
|     void **await()** |     线程**等待** |
|    void **signal()** | **唤醒**一个线程 |
| void **signalAll()** | **唤醒所有**线程 |



### 公平锁和非公平锁

+ 公平锁 —— 线程同步时，多个线程排队，依次执行

+ 非公平锁 —— 线程同步时，可以插队，默认为非公平锁



### 重入锁

JUC	java.util.concurrent	java并发编程工具包

重入锁ReentranLock 是 JUC 使用频率非常高的一个类，它就是对synchronized的升级，目的也是为了实现线程同步

**ReentrantLock 和 synchronized：**

+ ReentrantLock 是一个**类**，synchronized 是一个**关键字**
+ ReentrantLock 是 **JDK** 实现，synchronized 是 **JVM** 实现
+ ReentrantLock 需要**手动释放**，synchronized 可以**自动释放锁**



#### 可重复上锁

**ReentrantLock	重入锁**，可以重复上锁

```java
public class LockPrac implements Runnable{
    private static int num;
    private Lock lock=new ReentrantLock();				//创建Lock对象

    @Override
    public void run() {
        lock.lock();									//调用lock()方法上锁
        lock.lock();									//重复上锁
        num++;
        System.out.println(num);
        lock.unlock();									//调用unlock()方法解锁
        lock.unlock();									//解锁重复上锁
    }
}
```



#### 可中断	lockInterruptibly()

|                                方法 |       描述 |
| ----------------------------------: | ---------: |
| public void **lockInterruptibly()** | 可中断的锁 |

```java
public class StopLock {
    ReentrantLock reentrantLock=new ReentrantLock();

    public void service(){
        try {
            reentrantLock.lockInterruptibly();					//上锁，可中断锁

            System.out.println(Thread.currentThread().getName());
            try {
                TimeUnit.SECONDS.sleep(5);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        } catch (InterruptedException e) {
            e.printStackTrace();
        }finally {
            reentrantLock.unlock();								//解锁
        }

    }
}
```



```java
StopLock stopLock=new StopLock();
Thread t1=new Thread(() -> {
    stopLock.service();
},"A");
Thread t2=new Thread(()->{
    stopLock.service();
},"B");
t1.start();
t2.start();
try {
    TimeUnit.SECONDS.sleep(1);
    t2.interrupt();									//t2线程启动1秒后，没有拿到锁就中断
} catch (InterruptedException e) {
    e.printStackTrace();
}
```



#### 限时性	tryLock()

ReentrantLock具备限时性的特点，可以判断某个线程在一定的时间段内能否获取到锁，使用 tryLock() 方法，返回值是boolean类型，true表示可以获取到锁，而false表示无法获取到锁

|                                           方法 |                           描述 |
| ---------------------------------------------: | -----------------------------: |
| boolean **tryLock(long time, TimeUnit unit )** | **判断在time时间内能否拿到锁** |



#### 判断锁是否被当前线程占用	isHeldByCurrentThread()

|                                       方法 |                       描述 |
| -----------------------------------------: | -------------------------: |
| public boolean **isHeldByCurrentThread()** | 判断**当前线程是否占用锁** |



### 读写锁

#### 读写锁使用方法

接口 ReadWriteLock ，实现类是 **ReentrantReadWriteLock** ，**可以多线程同时读，但同一时间内只能有一个线程进行写入操作**

读写锁也是为了实现线程同步，但是它的粒度更细，可以分别给读和写的操作设置不同的锁

```java
public class Cache {
    private Map<Integer,String> integerStringMap =new HashMap<>();
    private ReadWriteLock readWriteLock=new ReentrantReadWriteLock();

    public void write(Integer key,String value){
        readWriteLock.writeLock().lock();
        System.out.println(key+"开始写入");
        integerStringMap.put(key, value);
        System.out.println(key+"写入完毕");
        readWriteLock.writeLock().unlock();
    }

    public void read(Integer key){
        readWriteLock.readLock().lock();
        System.out.println(key+"开始读取");
        integerStringMap.get(key);
        System.out.println(key+"读取完毕");
        readWriteLock.readLock().unlock();
    }
}
```

#### 独占锁、共享锁

**写入锁也叫独占锁，只能被一个线程占用，读取锁也叫共享锁，多个线程可以同时占用**



### 死锁

死锁是指多个线程各自需要的资源都得不到满足，他们各自不能完成任务然后释放自己所占有的资源去供其它线程使用，因而所有的线程都将处于无限等待的状态

#### 解决死锁的办法

使程序变成单线程运行，可以采用**主线程休眠**的办法实现，主线程休眠后，将不再调度子线程，因此程序将暂时变为单线程



### ConcurrentModificationException	并发修改异常

多个线程同时修改数据会造成并发修改异常

如对非线程安全的ArrayList同时进行读写等情况

#### 解决并发修改异常的方法

+ 使用线程安全的 **Vector**

+ 使用 **Collection.synchronizedList**

```java
List<String> list= Collections.synchronizedList(new ArrayList<>());
```

+ List类型 使用 JUC 的 **CopyOnWriteArrayList**，Set 类型的可以使用 **CopyOnWriteArraySet**，Map 类型的可以使用 **ConcurrentHashMap**

  **写时复制**，当向一个容器中添加元素时，不直接添加到容器，而是先将当前容器复制一份，然后向复制的容器中添加数据，添加完成后再将原容器的引用指向复制的容器

```java
List<String> list = new CopyOnWriteArrayList<>();
Set<String> set = new CopyOnWriteArraySet<>();
Map<String,String> map = new ConcurrentHashMap<>();
```





## ThreadLocal

ThreadLocal 的作用是：提供线程内的局部变量，不同的线程之间不会相互干扰，像这种变量在线程的生命周期内起作用，减少同一个线程内多个函数或组件之间一些公共变量传递的复杂度

+   多线程并发场景
+   传递数据 —— 可以通过 ThreadLocal 在同一线程内，不同组件中传递公共变量
+   线程隔离 —— 每个线程的变量都是独立的，不会互相影响



### 内部结构

在 JDK 1.8 以前，每个 `ThreadLocal` 都创建一个 `Map`，然后用 `Thread` 作为 `Map` 的 `key`，要存储的 `局部变量` 作为 `Map` 的 `value`

<img src="C:\Users\PC\AppData\Roaming\Typora\typora-user-images\image-20220316190729845.png" alt="image-20220316190729845" style="zoom:80%;" />

自 JDK 8 以后，每个 `Thread` 维护一个 `ThreadLocalMap`，这个 `Map` 的 `key` 是 `ThreadLocal` 实例本身，`value` 才是真正要存储的值的 `Object`

具体的过程是：

1.   每个 Thread 内部都有一个 Map（ThreadLocalMap）
2.   Map 里面存储 ThreadLocal 对象（key）和线程的变量副本（value）
3.   Thread 内部的 Map 是由 ThreadLocal 维护的，由 ThreadLocal 负责向 map 获取和设置线程的变量值
4.   对于不同的线程，每次获取副本值时，别的线程并不能获取到当前线程的副本值，形成了副本的隔离，互不干扰

<img src="C:\Users\PC\AppData\Roaming\Typora\typora-user-images\image-20220316190705749.png" alt="image-20220316190705749" style="zoom:80%;" />



Entry 中的 key 为弱引用，其原因是在 ThreadLocalMap中的 set/getEntry 方法中，会对 key 为 null （或 ThreadLocal 为 null）进行判断，如果为 null 的话，那么是会对 value 置为 null 的。

这就意味着使用完 ThreadLocal，在 CurrentThread 依然运行的前提下，就算忘记调用 remove 方法，弱引用比强引用可以多一层保障——弱引用的 ThreadLocal 会被回收，对应的 value 在下一次 ThreadLocalMap 调用 set、get、remove 三者任一方法的时候会被清除，从而避免内存泄漏



### 常用方法

| 方法声明                 | 描述                         |
| ------------------------ | ---------------------------- |
| ThreadLocal()            | 创建 ThreadLocal 对象        |
| protect T intialValue()  | 返回当前线程局部变量的初始值 |
| public void set(T value) | 设置当前线程绑定的局部变量   |
| public T get()           | 获取当前线程绑定的局部变量   |
| public void remove()     | 移除当前线程绑定的局部变量   |

**为了可能发生的避免内存泄漏，请在使用完 ThreadLocal 后，调用其 remove 方法将 Entry 删除！**



### 简单示例

```java
/*
*   需求： 线程隔离
*       在多线程并发的场景下，每个线程中的变量都是相互独立的
*       线程A：    设置（变量1） 获取（变量1）
*       线程B：    设置（变量2） 获取（变量2）
*
*   ThreadLocal：
*       1. set()：   将变量绑定到当前线程中
*       2. get()：   获取当前线程绑定的变量
* */

public class Demo01 {
    ThreadLocal<String> threadLocal=new ThreadLocal<>();

    private String content;

    public String getContent() {
        //return content;
        //返回当前线程绑定的变量
        return threadLocal.get();
    }

    public void setContent(String content) {
        //this.content = content;
        //将变量content绑定到当前线程
        threadLocal.set(content);
    }

    public static void main(String[] args) {
        Demo01 demo01=new Demo01();

        for (int i=0;i<5;i++){
            Thread thread=new Thread(new Runnable() {
                @Override
                public void run() {
                    demo01.setContent(Thread.currentThread().getName()+" 的数据");
                    System.out.println("---------------------------");
                    System.out.println(Thread.currentThread().getName()+"----->"+demo01.getContent());
                }
            });

            thread.setName("线程 "+i);
            thread.start();
        }
    }
}
```



### ThreadLocal 和 synchronized 的区别

虽然 ThreadLocal 模式和 synchronized 关键字都用于处理多线程并发访问变量的问题，不过两者处理问题的角度和思路不同

|        | synchronized                                                 | ThreadLocal                                                  |
| ------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 原理   | 同步机制采用“以时间换空间”的方式，只提供了一份变量，让不同的线程排队访问 | ThreadLocal采用“以空间换时间”的方式，为每一个线程都提供了一份变量的副本，从而实现同时访问而相互不干扰 |
| 侧重点 | 多个线程之间访问资源的同步                                   | 多线程中让每个线程之间的数据相互隔离                         |

使用 ThreadLocal 和 synchronized 都能够解决线程隔离问题，但是使用 ThreadLocal 是更为合适的，因为这样可以使程序拥有更高的并发性，而 synchronized 一般用于多个线程共享数据的互斥操作



## JUC 工具类

### CountDownLatch	减法计数器

可以用来设定值并做减法计数，当两个线程同时执行时，如果要确保一个线程优先执行，可以使用计数器，当计数器清零时，可以让另一个线程执行

```java
CountDownLatch countDownLatch =new CountDownLatch(100);			//设定初始数
```

|                        方法 |                                       描述 |
| --------------------------: | -----------------------------------------: |
| public void **countDown()** |                         做一次**减法计数** |
|     public void **await()** | 计数器停止后，使用这个方法**唤醒其他线程** |

**new CountDownLatch( int count )、countDown() 和 await() 必须配合使用，而且 调用countDown() 的次数必须大于或等于初始计数次数，否则即便使用 await() 也不能唤醒其他线程**

```java
public class CountDownLatchPrac {
    public static void main(String[] args) {
        CountDownLatch countDownLatch =new CountDownLatch(100);		//初始计数100

        new Thread(() -> {
            for (int i=0;i<100;i++){
                System.out.println("T");
                countDownLatch.countDown();							//减法计数
            }
        }).start();

        try {
            countDownLatch.await();									//唤醒其他线程
        } catch (InterruptedException e) {
            e.printStackTrace();
        }

        for (int i=0;i<100;i++){
            System.out.println("M");
        }
    }
}
```



### CyclicBarrier	加法计数器

可以用来设定上限值并做加法计数，当两个线程同时执行时，如果要确保一个线程优先执行，可以使用计数器，当计数达到上限值时，可以让另一个线程执行

```java
CyclicBarrier cyclicBarrier=new CyclicBarrier(100, ()->{
    System.out.println("完成");								//设定临界值和计数器任务
```

|                   方法 |                               描述 |
| ---------------------: | ---------------------------------: |
| public int **await()** | 在其他线程中试图**唤醒计数器线程** |

await() 方法的作用是在其他线程中试图唤醒计数器线程，当其他线程的执行次数达到计数器的临界值时，唤醒计数器线程。**计数器是可以重复使用的**，当计数器的线程执行完成一次之后，计数器自动清零并等待下一次执行

```java
public class CyclicBarrierPrac {
    public static void main(String[] args) {
        CyclicBarrier cyclicBarrier=new CyclicBarrier(100, ()->{   //设置上限、计数器任务
            System.out.println("放行");
        });

        for (int i=0;i<100;i++){
            final int temp=i;
            new Thread(()->{
                System.out.println("==>"+temp);
                try {
                    cyclicBarrier.await();					//达到设定次数后唤醒当前线程
                } catch (InterruptedException e) {
                    e.printStackTrace();
                } catch (BrokenBarrierException e) {
                    e.printStackTrace();
                }
            }).start();
        }
    }
}
```



### Semaphore	计数信号量

使用它来完成限流操作，限制可以访问某些资源的线程数量

```java
Semaphore semaphore =new Semaphore(5);					//设访问上限为5
```

Semaphore的三个操作：

+ 初始化
+ 获得许可
+ 释放

|                                            方法 |                                                         描述 |
| ----------------------------------------------: | -----------------------------------------------------------: |
|               public **Semaphore(int permits)** |                              **初始化信号量，permits为上限** |
| public **Semaphore(int permits, boolean fair)** | **初始化信号量**，permits为上限，fair为是否公平锁，默认为非公平 |
|                       public void **acquire()** |                                               **获取信号量** |
|                       public void **release()** |                                               **释放信号量** |

每个线程在执行的时候，**需要先获取信号量**，获取到资源之后才可以执行，并且**在执行完毕之后需要释放资源**，留给其他线程

```java
public class SemaphorePrac {
    public static void main(String[] args) {
        Semaphore semaphore =new Semaphore(5);			//初始化上限
        for (int i=0;i<15;i++){
            new Thread(()->{
                try {
                    semaphore.acquire();				//获得许可
                    System.out.println(Thread.currentThread().getName()+"进店");
                    TimeUnit.SECONDS.sleep(2);
                    System.out.println(Thread.currentThread().getName()+"出店");
                } catch (InterruptedException e) {
                    e.printStackTrace();
                } finally {
                    semaphore.release();				//释放
                }
            }).start();
        }
    }
}
```



## 多线程下的单例模式	双重检测

```java
public class SingletonDemo {
    private volatile static SingletonDemo singletonDemo;	//添加volatile关键字

    private SingletonDemo(){
        System.out.println("create SingletonDemo");
    }

    public static SingletonDemo getInstance(){
        if (singletonDemo==null){							//检测对象是否为空
            synchronized (SingletonDemo.class){
                if (singletonDemo==null){					//检测对象是否为空，针对于同步队列内的状态更新
                    singletonDemo=new SingletonDemo();
                }
            }
        }
        return singletonDemo;
    }
}
```

**多个线程只会创建一个对象**

```java
public class SingletonPrac {
    public static void main(String[] args) {
        new Thread(new Runnable(){
            @Override
            public void run() {
                SingletonDemo singletonDemo=SingletonDemo.getInstance();
            }
        }).start();

        new Thread(new Runnable() {
            @Override
            public void run() {
                SingletonDemo singletonDemo=SingletonDemo.getInstance();
            }
        }).start();

    }
}
```



## 生产者-消费者模式

**在一个生产环境中，生产者和消费者在同一时间段内共享同一块缓冲区**，生产者负责向缓冲区添加数据，消费者负责从缓冲区取出数据

```java
public class Container {
    private Lock lock=new ReentrantLock();
    private Condition condition=lock.newCondition();
    private LinkedList<Hamburger> cupboard= new LinkedList<>();

    public void push(Hamburger hamburger){
        lock.lock();
        cupboard.offer(hamburger);
        System.out.println("生产了一个汉堡，橱窗里有 "+cupboard.size()+" 个汉堡");
        condition.signal();					//生产出汉堡后唤醒消费线程
        lock.unlock();
    }

    public Hamburger popH(){
        lock.lock();
        while (cupboard.isEmpty()){			//如果橱柜中没有汉堡则使消费线程等待
            try {
                condition.await();
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
        System.out.println("消费了一个汉堡");
        lock.unlock();
        return cupboard.pollLast();
    }
}
```



##  线程池

预先创建好一定数量的线程对象，存入缓冲池中，需要用的时候直接从缓冲池中取出，用完之后不要销毁，还要回到缓冲池中，为了提高资源的利用率

### 使用线程池的优势

+ 提高线程的利用率
+ 提高响应速度
+ 便于统一管理线程对象
+ 可以控制最大的并发数

### 线程池的逻辑

1. 线程池初始化的时候创建一定数量的线程对象
2. 如果缓冲池没有空闲的线程对象，则新来的任务进入等待队列
3. 如果缓冲池中没有空闲的线程对象，等待队列也已经填满，可以申请再创建一定数量的新线程对象，直到达到线程池的最大值，这时候如果还有新的任务进来，只能选择拒绝



### 使用工具类创建线程池

#### 单例线程池

```java
ExecutorService executorService= Executors.newSingleThreadExecutor();
```

实现源码参数

```java
public static ExecutorService newSingleThreadExecutor() {
        return new FinalizableDelegatedExecutorService
            (new ThreadPoolExecutor(1, 1,
                                    0L, TimeUnit.MILLISECONDS,
                                    new LinkedBlockingQueue<Runnable>()));
}
```

#### 指定线程数量的线程池

```java
ExecutorService executorService=Executors.newFixedThreadPool(5);
```

实现源码参数

```java
public static ExecutorService newFixedThreadPool(int nThreads, ThreadFactory threadFactory) {
    return new ThreadPoolExecutor(nThreads, nThreads,
                                  0L, TimeUnit.MILLISECONDS,
                                  new LinkedBlockingQueue<Runnable>(),
                                  threadFactory);
}
```

#### 缓冲线程池

```java
ExecutorService executorService=Executors.newCachedThreadPool();
```

实现源码参数

```java
public static ExecutorService newCachedThreadPool() {
    return new ThreadPoolExecutor(0, Integer.MAX_VALUE,
                                  60L, TimeUnit.SECONDS,
                                  new SynchronousQueue<Runnable>());
}
```

### 工具类线程池对象的使用

```java
for (int i=0;i<1000;i++){
    final int temp=i;
    executorService.execute(()->{						//编写任务
        System.out.println(Thread.currentThread().getName()+"---"+temp);
    });
}

executorService.shutdown();								//结束后关闭线程池
```



### 手动创建线程池	推荐

无论哪种线程池，底层都是通过创建ThreadPoolExecutor对象来完成线程池的创建

其构造函数

```java
public ThreadPoolExecutor(int corePoolSize,
                          int maximumPoolSize,
                          long keepAliveTime,
                          TimeUnit unit,
                          BlockingQueue<Runnable> workQueue,
                          ThreadFactory threadFactory,
                          RejectedExecutionHandler handler) {
    if (corePoolSize < 0 ||
        maximumPoolSize <= 0 ||
        maximumPoolSize < corePoolSize ||
        keepAliveTime < 0)
        throw new IllegalArgumentException();
    if (workQueue == null || threadFactory == null || handler == null)
        throw new NullPointerException();
    this.corePoolSize = corePoolSize;
    this.maximumPoolSize = maximumPoolSize;
    this.workQueue = workQueue;
    this.keepAliveTime = unit.toNanos(keepAliveTime);
    this.threadFactory = threadFactory;
    this.handler = handler;
}
```

参数

+ **corePoolSize** —— **核心池大小**，也就是初始化的线程数量
+ **maximumPoolSize** —— **线程池最大线程数**，它决定了线程池容量的上限，等待队列满时再来新的任务请求就会创建新的线程对象

corePoolSize是线程池大小，而maximumPoolSize是一种补救措施，它用来应对任务量突然激增的情况

+ **keepAliveTime** —— **线程对象的存活时间**，线程池中的线程对象数量大于核心池大小时生效
+ **unit** —— **线程对象存活时间单位**
+ **workQueue** —— **等待队列、阻塞队列**
+ **threadFactory** —— **线程工厂**，用来创建线程对象
+ **handler** —— **拒绝策略**，当等待队列满时对新的任务请求的处理办法

#### 手动创建线程池的实现方法

```java
public class ThreadPoolExecutorPrac {
    public static void main(String[] args) {
        ExecutorService executorService=null;

        try {
            executorService=new ThreadPoolExecutor(			//创建线程，填入参数
                	2,		
                    3,
                    1L,
                    TimeUnit.SECONDS,
                    new ArrayBlockingQueue<>(2),
                    Executors .defaultThreadFactory(),
                    new ThreadPoolExecutor.DiscardOldestPolicy()
            );

            for (int i=0;i<6;i++){
                executorService.execute(()->{
                    try {
                        TimeUnit.MILLISECONDS.sleep(100);
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                    System.out.println(Thread.currentThread().getName()+"---办理业务");
                });
            }
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            executorService.shutdown();
        }
    }
}
```

#### 4种拒绝策略

+ **AbortPolicy** —— 直接抛出异常
+ **DiscardPolicy** —— 放弃任务，不抛出异常
+ **DiscardOldesPolicy** —— 尝试与等待队列中最前面的任务去争夺，不抛出异常
+ **CallerRunsPolicy** —— 谁调用谁处理，例如一般情况下主线程调度其他线程，当队列满且新任务到来时，这个新任务就由主线程执行

#### 阻塞队列 workQueue

+ **ArrayBlockingQueue** —— 基于数组的先进先出队列，创建时必须指定大小
+ **LinkedBlockingQueue** —— 基于链表的先进先出队列，创建时可以不指定大小，默认值为 Integer.MAX_VALUE
+ **SynchronousQueue** —— 它不会保存提交的任务（没有队列），而是直接新建一个线程来执行新来的任务
+ **PriorityBlockingQueue** —— 具有优先级的阻塞队列



## 并发编程

### 并发的标准

+ **QPS** —— 每秒查询率，是每秒响应的HTTP请求数量

+ **吞吐量** —— 单位时间内处理的请求数，由QPS和并发数来决定

+ **平均响应时间** —— 系统对一个请求做出响应的评价时间

  ​	**QPS = 并发数 / 平均响应时间**

+ **并发用户数** —— 同时承载正常使用系统的用户人数



## ForkJoin 多线程框架

ForkJoin使用多个类同时完成某项工作

ForkJoin是对线程池的一种补充，对线程池功能的一种扩展，是基于线程池的，它的核心思想就是将一个大型的任务拆分成很多个小任务，分别执行，最终将小任务的结果进行汇总，生成最终的结果

![image-20210726175728051](C:\Users\PC\AppData\Roaming\Typora\typora-user-images\image-20210726175728051.png)

如A、B两个线程同时执行，A的任务比较多，B的任务相对较少，B先执行完毕，这时候B去帮助A完成任务（将A的一部分任务拿过来替A执行，执行完毕之后再把结果进行汇总），从而提高效率

B执行完再帮A执行任务的过程也叫**工作窃取**



# 递归

## 递归的三个要素

+ 一个**父问题可以拆分成若干个子问题**，并且若干子问题的结果汇总起来就是父问题的答案
+ 父问题和子问题，**解题思路必须完全一致**，只是数据规模不同
+ 存在**终止条件**


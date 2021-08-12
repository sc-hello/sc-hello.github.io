@[TOC](目录)
# String
## String截取元素

```java
1. String s = s.substring(beginIndex)：截取从beginIndex至字符串末尾的字符串；
2. String s = s.substring(beginindex，endIndex)：截取从beginIndex至endindex-1的字符串；
```

## 数字转字符串

```java
String s = String.valueOf(i);
```
或

```java
String s = i + “”;
```

## 字符数组转字符串

```java
String s = String.valueOf(charArray)；
```

## 字符串转字符数组

```java
char[] charArray = string.toCharArray();
```

## 以.切割字符串

```java
String[] strs = s.split("\\.");
```
## 字符串转数字
```java
int i = Integer.parseInt(s)；
```
## 删除字符串的头尾空白符

```java
String s = s.trim();
```

## StringBuilder转String

```java
String s = stringBuilder.toString();
```

## 字符串比较大小


```java
// public boolean equals(Object anObject)：字符序列相等返回true，否则false
boolean judge = str1.equals(str2);
```

```java
// public boolean equalsIgnoreCase(String anotherString)：忽略大小写字符序列相等返回true，否则false
boolean judge = str1.equalsIgnoreCase(str2);
```

```java
// public int compareTo(String anotherString)：按字典顺序比较两个字符串，如果String对象按字典顺序排列在参数字符串之前，结果为负整数，如果在之后结果为正整数；如果两字符串相等，结果为0
int judge = str1.compareTo(str2);
```

## 测试字符串是否以指定前缀开头/后缀结尾

```java
// 测试此字符串是否以指定的前缀开头
public boolean startsWith(String prefix)
// 测试在指定索引处开始的此字符串的子字符串是否以指定的前缀开头
public boolean startsWith(String prefix, int toffset)
// 测试此字符串是否以指定的后缀结尾
public boolean endsWith(String suffix)
```

## 返回指定字符在字符串中第一次出现的索引

```java
int indexOf(String str): 返回指定字符在字符串中第一次出现处的索引，如果此字符串中没有这样的字符，则返回-1。

int indexOf(String str, int fromIndex): 返回从 fromIndex 位置开始查找指定字符在字符串中第一次出现处的索引，如果此字符串中没有这样的字符，则返回-1。
```

# StringBuilder
## StringBuider删除对应索引位置的元素

```java
StringBuider.deleteCharAt(index)；
```

## StringBuider添加元素

```java
StringBuider.append(element)；
```

## StringBuider获取长度

```java
StringBuider.length()；
```
## 创建StringBuilder时添加元素

```java
StringBuilder res = new StringBuilder("[");
```

# char

## char转大小写

```java
//转大写
char c = Character.toUpperCase(c);
//转小写
char c = Character.toLowerCase(c);
```
## 判断char是否是数字

```java
boolean judge = Character.isDigit(c);
```

## char转int

```java
int i = Integer.parseInt(c + "");
```

# 数据类型转换
## 数据类型范围从小到大：自动转换

```java
System.out.println(1024);// 一个整数，默认int类型
System.out.println(3.14);// 一个浮点数，默认double类型
// int --> long,符合数据范围从小到大的要求；
long num1 = 100;
System.out.println(num1);// 100
// float --> double,符合从小到大的要求；
double num2 = 2.5F;
System.out.println(num2);// 2.5
// long --> float,由于float范围更大，符合从小到大的规则；
float num3 = 30L;
System.out.println(num3);// 30.0
```
## 数据类型范围从大到小：强制类型转换（显式）

```java
// 需要强制类型转换。
int num = (int) 100L;
System.out.println(num);//100

// 假设从大到小不进行强制类型转换
// int num = 100L;
// System.out.println(num);//报错：从long转换到int可能会有损失
```

 - **数据类型范围从小到大**：byte、short、char --> int --> long --> float --> double，其中byte、short、char在运算时自动提升为int；
 - boolean类型不能发生数据类型转换；

## int和char自动转换

```java
int i = 'a' + 1;
System.out.println(i);
char c = 'a' + 1;
System.out.println(c);
```

```java
98
b
```

# Array
## 新建Array

```java
// 1 动态初始化（指定长度）
String[] aArray = new String[5];
// 2 静态初始化的标准格式（指定内容，不可以指定长度，自动推算长度）
String[] bArray = new String[]{"a","b","c","d","e"};
// 3 静态初始化的省略格式
String[] cArray = {"a","b","c", "d", "e"};
```

## Array的String形式

```java
Arrays.toString(array)
```
例：
```java
String[] array = new String[]{"a","b","c","d","e"};
String arrayString = Arrays.toString(array);
// 直接打印数组名将输出地址
System.out.println(array);
// [I@7150bd4d
System.out.println(arrayString);
// [a, b, c, d, e]
```
## 判断Array中是否包含某个值

```java
boolean b = Arrays.asList(aArray).contains("a");
```

## 复制Array
1
```java
System.arraycopy(int[] arr1, int start1,int[] arr2, int start2, length);
```
arr1：被复制数组
start1：arr1的开始下标
arr2：要复制数组
start2：arr2的开始下标
length：从arr1中拿length个元素放到arr2中

2
```java
int[] new = Arrays.copyOfRange(int[] original, int from, int to);//左闭右开
```

## 某几个元素组成一个Array

```java
new int[]{a1, a2, a3,......};
```
## 对二维Array，以其中的每个数组的首个元素进行升序排序

```java
Arrays.sort(nums, new Comparator<int[]>() { // <T> T必须是引用类型
	public int compare(int[] num1, int[] num2) {
		return num1[0] - num2[0];
	}
});
```
或

```java
Arrays.sort(nums, (num1, num2) -> num1[0] - num2[0]);
```

## 空Array

```java
new int[0];
```
## 对Array排序

 - Arrays.sort(Object[] a)：根据其元素的自然顺序，按照升序排列指定的对象数组；（同样适用于int、double等）
 - Arrays.sort(T[] a, Comparator<? super T> c)：根据指定的比较器引发的顺序对指定的对象数组进行排序；（T必须为引用类型）

## 比较Array内容是否相等

 - Arrays.equals(Object[] a1, Object[] a2)：如果两个数组在相同的顺序中包含相同的元素，则它们是相等的。 另外，如果两个数组引用都是null，则两个数组引用被认为是相等的；（同样适用于int、double等）

## 让Array所有元素都为某个值

```java
int[] array = new int[10];
Arrays.fill(array, 1);
```
# List和Set
## Collection包括List和Set，其添加、删除、清空、判存、判空、查长度的方法
1. 添加：`add(E e)`
2. 删除：`remove(E e)` 
3. 清空：`clear()`
4. 判存：`boolean contains(E e)`
5. 判空：`boolean isEmpty()`
6. 查长度：`int size()`

## List删除元素的陷阱
List删除元素时传入数字时，默认按索引删除。如果需要删除Integer对象，调用remove(object)方法，需要传入Integer类型，代码如下：

```java
list.remove(new Integer(2));
```

## 将List中指定index位置的对象修改为指定对象

 - `public E set(int index, E element)`：用指定的元素替换此列表中指定位置的元素，并返回原来的元素。

## 某几个元素组成一个List

```java
List<T> list = Arrays.asList(T[] a);//返回由指定数组支持的固定大小的列表
```

```java
List<T> list = Arrays.asList(T a1, T a2, T a3....);//返回一个初始化为包含几个元素的固定大小的列表
```

## 根据List构建Set

```java
Set<T> set = new HashSet<>(list);
```
## 根据Set构建List

```java
List<T> list = new ArrayList<>(set);
```

## List添加元素

 - add(E e)：将指定的元素追加到此列表的末尾；
 - add(int index, E element)：在此列表中的指定位置插入指定的元素；

## 对List排序

 - Collections.sort(List<T> list)：根据元素的自然顺序，按照升序排列指定的列表；
 - Collections.sort(List< T> list, Comparator<? super T> c)`：根据指定的比较器对指定列表进行排序；

## List尾加元素和头加元素

 - 尾加：`list.add(t);`
 - 头加：`list.add(0, t);`

## 交换指定List中指定位置的元素

```java
Collections.swap(List<?> list, int i, int j);
```

## String是否包含指定的char序列

```java
String string = "aba";
System.out.println(string.contains("ba"));
```

```java
true
```

# List和Array转换
## List转Array
**1 引用数据类型**
```java
List<String> list = new ArrayList<>();
list.add("abc");
String[] array = list.toArray(new String[]{});
```
**2 基本数据类型**

 - List转基本类型Array只能先定义Array再循环赋值。

## Array转List
**1 引用数据类型**

```java
String[] array = {"java", "c"};
List<String> list = new ArrayList<>(Arrays.asList(array));
```

**2 基本数据类型**

 - 基本类型Array转List只能先定义List再循环赋值。

## 增强for循环
增强for循环专门用来遍历Array和Collection，它的内部原理其实是个Iterator迭代器，所以在遍历的过程中，不能对元素进行增删操作。

**格式**：

```java
for(元素的数据类型 变量 : Collection或Array){ 
  	//操作代码
}
```
# Map
## Map添加、删除、清空、判存、判空、查长度、获取key集合、获取value集合
1. 添加：`put(K key, V value)`
2. 删除：`remove(key)`
3. 清空：`clear()`
4. 判存：`boolean containsKey(key)`、`boolean containsValue(value)`
5. 判空：`boolean isEmpty()`
6. 查长度：`int size()`
7. 获取key集合：`Set<K> keySet()`
8. 获取value集合：`Collection<V> values()`

## 获取键映射的值
`V get(Object key)`：返回哈希表中指定键映射的值，如果不包含此键的映射，返回null。

## Map的getOrDefault(K key, V defaultValue)
如果Map中存在对应的映射，返回key对应的value，否则返回defaultValue。

## Map创建同时put元素
```java
Map<Character, String> dic = new HashMap<Character, String>() {{
	put('2', "abc");
	put('3', "def");
	put('4', "ghi");
	put('5', "jkl");
	put('6', "mno");
	put('7', "pqrs");
	put('8', "tuv");
	put('9', "wxyz");
}};
```

# 数字
## 字符串转数字
```java
int i = Integer.parseInt(s)；
```

## 0，'0'，'\0'
0表示整数,'0'表示0字符,'\0'表示ASCII码值为0的字符

## Integer最大最小值表示，Double最大最小值表示，无穷大无穷小表示

 - Integer最大值：Integer.MAX_VALUE
 - Integer最小值：Integer.MIN_VALUE
 - Double最大值：Double.MAX_VALUE
 - Double最小值：Double.MIN_VALUE
 - 无穷大：Double.POSITIVE_INFINITY
 - 无穷小：Double.NEGATIVE_INFINITY

## 位运算中<<、>>、>>>的区别
1. **<<** 表示左移，不分正负数，低位补0；
2. **>>** 表示右移，若该数为正，高位补0；若该数为负，高位补1；
3. **>>>** 表示无符号右移，也称逻辑右移，不分正负数，高位补0；

# 栈、队列、堆
## 栈和队列的插删规则
 - 栈：头插头删
 - 队列：尾插头删
## 双端队列Deque用法
 1. 向队头插入元素：`public void offerFirst(E e)`
 2. 向队尾插入元素：`public void offerLast(E e)  `
 3. 获取并移除队头元素：`public E pollFirst() `
 4. 获取并移除队尾元素：`public E pollLast()  `
 5. 获取队头元素：`public E peekFirst() `
 6. 获取队尾元素：`public E peekLast()  `
 7. 获取队列中的元素个数：`public int size()  `

## 队列创建同时插入元素
```java
Deque<TreeNode> queue = new LinkedList<TreeNode>() {{ offer(root); }};
```

## 最小堆最大堆

 - 最小堆：`PriorityQueue<Integer> heap = new PriorityQueue<>();`
 - 最大堆：`PriorityQueue<Integer> heap = new PriorityQueue<>((o1, o2) -> o2 - o1);`

## 获取堆顶元素以及向堆中插入元素

 - 获取堆顶元素：`heap.poll();`
 - 向堆中插入元素：`heap.offer(T t);`


# Scanner输入输出

```java
Scanner scan = new Scanner(System.in);
```

1 通过 Scanner 类的 next() 与 nextLine() 方法可以获取输入的字符串，在读取前一般需要使用 hasNext() 与 hasNextLine() 判断是否还有输入的数据；如果要输入int或double类型的数据，在Scanner 类中同样有支持，但是在输入之前最好先使用 hasNextXxx() 方法验证是否还有输入以及输入类型是否正确，再使用 nextXxx() 来读取。

2 next()以及nextInt()等 与 nextLine() 区别：


**next()以及nextInt()等:**

 1. 一定要读取到有效字符后才可以按Enter键结束输入；
 2. 对输入有效字符之前遇到的空白，next()以及nextInt()等自动将其去掉；
 3. 输入有效字符后，如果遇到空白，next()以及nextInt()等将从第一个空白处截断并去掉之后所有字符;
 4. next()以及nextInt()等，不读取“\n”，并将cursor放在本行中（所以可以在一行以空格分隔多个输入），因此如果想在next()以及nextInt()等后读取一行nextLine()，就必须在next()以及nextInt()等之后加上scan.nextLine();

**nextLine()：**

 1. 不一定读取到有效字符，按Enter就可以结束输入；
 2. 以"\n"为结束符，读完后cursor在下一行，返回的是按Enter键结束输入之前的所有字符；

*next() 不能得到带有空白的字符串，nextLine() 能得到带有空白的字符串。*

3 **Scanner.close()**的必要性：使用Scanner(System.in)时，使用完毕后，一定要关闭扫描器，因为System.in属于IO流，一旦打开，它将一直占用资源，因此使用完毕后切记要关闭。

# 缓冲流读文本文件

```java
public class BufferedReaderDemo {
    public static void main(String[] args) throws IOException {
      	 // 创建流对象
        BufferedReader br = new BufferedReader(new FileReader("in.txt"));
		// 定义字符串,保存读取的一行文字
        String line = null;
      	// 循环读取,读取到最后返回null
        while ((line = br.readLine())!=null) {
            System.out.print(line);
            System.out.println("------");
        }
		// 释放资源
        br.close();
    }
}
```

```java
aaa------
bbb------
ccc------
ddd------
```

# 位运算
## | 和 ||，& 和 && 的区别
1. || 和 && 是逻辑运算符，而 | 和 & 是位运算符；
2. | 和 &两边的操作数既可以是数字又可以是true或false，但两边必须一致。如果是数字，按位运算获得结果（数字），如果是true或false，按逻辑运算获得结果（true或false）；|| 和 &&两边的操作数必须是true或false；
3. 可以有`judge |= true`这种表达，但`judge ||= true`不合法；
4. **作逻辑运算时**：|| 和 &&具有短路功能，即如果第一个表达式可以判定结果，就不再计算第二个表达式；但 | 和 &不具有短路功能，两个表达式都要计算：

```java
if(A && B)  // 若 A 为 false ，则 B 的判断不会执行（即短路），直接判定 A && B 为 false

if(A || B) // 若 A 为 true ，则 B 的判断不会执行（即短路），直接判定 A || B 为 true
```
例

```java
int i = 1;
if (true || ++i > 10) {
    System.out.println(i);
}
if (true | ++i > 10) {
    System.out.println(i);
}
```

```java
1
2
```

# 快速排序
- 如果基准值选最左边的元素，先右--再左++；
- 如果基准值选最右边的元素，先左++再右--；

# Random:

```java
Random random = new Random();
int num1 = random.nextInt();//产生任意int类型随机数
int num2 = random.nextInt(20);//产生[0,20)int类型随机数
```




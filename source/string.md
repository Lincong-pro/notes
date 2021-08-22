# 字符串相关的类

## 一、String

### 1.1String特性

- ==**String声明：final类，不可被继承，不可变的字符序列**==
- ==**String实现了Serializable接口：表示字符串是支持序列化的**==
- ==**String实现了Comparable接口：表示String可以比较大小**==
- **字符串是常量，用双引号引起来表示。它们的值在创建之后不能更改**
- String：private final char value[]：String对象的字符内容是存储在一个字符数组value[]

### 1.2不可变性

1. 当对字符串重新赋值时，需要重写指定内存区域赋值，不能使用原有的value进行赋值
2. 当对现有的字符串进行连接操作时，也需要重写指定内存区域赋值，不能使用原有的value进行赋值
3. 当调用String的replace()方法修改指定内容时，也需要重写指定内存区域赋值，不能使用原有的value进行赋值

- 通过字面量的方法给一个字符串赋值，此时的字符串值声明在字符串常量池中
- 字符串常量池是不会存储相同内容的字符串的

```java
String str = "hello";//字面量赋值
//本质上this.value = new char[0];
String s1 = new String(); 
//this.value = original.value;
String s2 = new String(String original); 
//this.value = Arrays.copyOf(value, value.length);
String s3 = new String(char[] a); 
String s4 = new String(char[] a,int startIndex,int count);
```

![image-20210811173007599](D:\桌面\数学建模笔记\数学建模笔记图片\image-20210811173007599.png)

### 1.3String的实例化方式

1. 一：字面量
2. 二：new+构造器

- <img src="D:\桌面\数学建模笔记\数学建模笔记图片\image-20210811171728733.png" alt="image-20210811171728733" style="zoom: 33%;" />

- <img src="D:\桌面\数学建模笔记\数学建模笔记图片\image-20210811172053437.png" alt="image-20210811172053437" style="zoom: 50%;" />

```java
例题：String s = new String("abc");在内存创建了几个对象
	：俩个，一个是堆空间中的new结构，一个是char[]对象的常量池中的数据“abc”
```

### 1.4结论

1. ==**常量与常量的拼接结果在常量池。且常量池中不会存在相同内容的常量。**==
2. ==**只要其中有一个是变量，结果就在堆中**==
3. ==**如果拼接的结果调用intern()方法，返回值就在常量池中**==

<img src="D:\桌面\数学建模笔记\数学建模笔记图片\image-20210811173330337.png" alt="image-20210811173330337" style="zoom:50%;" />

### 1.5String使用陷阱

```java
 String s1 = "a"; 
说明：在字符串常量池中创建了一个字面量为"a"的字符串。
 s1 = s1 + "b"; 
说明：实际上原来的“a”字符串对象已经丢弃了，现在在堆空间中产生了一个字符串s1+"b"（也就是"ab")。如果多次执行这些改变串内容的操作，会导致大量副本字符串对象存留在内存中，降低效率。如果这样的操作放到循环中，会极大影响程序的性能。 
 String s2 = "ab";
说明：直接在字符串常量池中创建一个字面量为"ab"的字符串。
 String s3 = "a" + "b";
说明：s3指向字符串常量池中已经创建的"ab"的字符串。
 String s4 = s1.intern();
说明：堆空间的s1对象在调用intern()之后，会将常量池中已经存在的"ab"字符串赋值给s4
```

面试题：

```java
public class StringTest {
	String str = new String("good");
	char[] ch = { 't', 'e', 's', 't' };
	public void change(String str, char ch[]) {
		str = "test ok";
		ch[0] = 'b'; }
	public static void main(String[] args) {
		StringTest ex = new StringTest();
		ex.change(ex.str, ex.ch);
		System.out.print(ex.str + " and ");//good and best
		System.out.println(ex.ch);
	} 
}
```

### 1.6字符串方法

- int **length()：**返回字符串的长度： return value.length
- char **charAt(int index)**： 返回某索引处的字符return value[index] 
- boolean isEmpty()：判断是否是空字符串：return value.length == 0 
- String toLowerCase()：使用默认语言环境，将 String 中的所有字符转换为小写
- String toUpperCase()：使用默认语言环境，将 String 中的所有字符转换为大写
- String trim()：返回字符串的副本，忽略前导空白和尾部空白
- boolean **equals(Object obj)：**比较字符串的内容是否相同
- boolean equalsIgnoreCase(String anotherString)：与equals方法类似，忽略大小写
- String **concat(String str)：**将指定字符串连接到此字符串的结尾。 等价于用“+” 
- **==int compareTo(String anotherString)：比较两个字符串的大小,返回数字，涉及到字符串排序==**
- **String substring(int beginIndex)：**返回一个新的字符串，它是此字符串的从beginIndex开始截取到最后的一个子字符串。 
- **String substring(int beginIndex, int endIndex) ：**返回一个新字符串，它是此字符串从beginIndex开始截取到endIndex(不包含)的一个子字符串。

```java
@Test
    public void test1() {
        String s1 = "Hello World!";
        //int length()
        int length = s1.length();
        System.out.println(s1.length() + "长度" + length);
        //char charAt(int index)
        char charat = s1.charAt(0);
        System.out.println(s1.charAt(11) + "返回索引的字符" + charat);
        //boolean isEmpty()
        //s1 = "";
        boolean isempty = s1.isEmpty();
        System.out.println(s1.isEmpty() + "判断字符串是否为空" + isempty);
        //String toLowerCase()/toUpperCase()
        String tolowercase = s1.toLowerCase();
        System.out.println(s1.toLowerCase()+"转换小写"+tolowercase);
        System.out.println(s1.toUpperCase()+"转换大写");
        //String trim()
        String trimtest = "    "+s1 +"     ";
        String trim = trimtest.trim();
        System.out.println(trimtest.trim()+"移除空格"+trimtest+trim);
        //boolean equals(Object obj)
        String equalstest = "Hello world!";
        boolean isequals = s1.equals(equalstest);
        System.out.println(s1.equals(equalstest)+"判断是否相等"+isequals);
        //boolean equalsIgnoreCase(String anotherString)
        String equalsIgnoreCasetest = "hello world!";
        boolean isequalsIgnoreCase = s1.equalsIgnoreCase(equalsIgnoreCasetest);
        System.out.println(s1.equalsIgnoreCase(equalsIgnoreCasetest)+"忽略大小写判断相等"+isequalsIgnoreCase);
        //String concat(String str)
        String concattest = s1.concat(s1);
        System.out.println(s1.concat("china")+"连接"+concattest);
        //int compareTo(String anotherString)
        int compareTotest = s1.compareTo("Hello world");
        System.out.println(s1.compareTo(s1)+"比较大小，返回数字"+compareTotest);
        //String substring(int beginIndex)
        String substringtest = s1.substring(2);
        System.out.println(s1.substring(0)+"从数字下标处输出后面的"+substringtest);
        //String substring(int beginIndex, int endIndex)
        String substringtest2 = s1.substring(0,5);
        System.out.println(s1.substring(2,3)+"切片"+substringtest2);
    }
```

- boolean endsWith(String suffix)：测试此字符串是否以指定的后缀结束
- boolean startsWith(String prefix)：测试此字符串是否以指定的前缀开始
- boolean startsWith(String prefix, int toffset)：测试此字符串从指定索引开始的子字符串是否以指定前缀开始，**测试指定位置的内容**

- ==boolean contains(CharSequence s)：当且仅当此字符串包含指定的 char 值序列时，返回 true==
- ==**int indexOf(String str)：**返回指定子字符串在此字符串中第一次出现处的索引，**没有返回-1**==
- ==**int indexOf(String str, int fromIndex)：返回指定子字符串在此字符串中第一次出现处的索引，从指定的索引开始**==
- **int lastIndexOf(String str)：返回指定子字符串在此字符串中最右边出现处的索引**
- int lastIndexOf(String str, int fromIndex)：返回指定子字符串在此字符串中最后一次出现处的索引，从指定的索引开始反向搜索
  	==注：indexOf和lastIndexOf方法如果未找到都是返回-1==

```java
@Test
    public void test() {
        String s1 = new String("Hello World!");
        String s2 = new String("Hello World!china");
        //boolean contains(CharSequence s)
        boolean iscontains = s1.contains(s2);
        System.out.println(s2.contains(s1)+"判断是否有子串"+iscontains+s1.contains("Hello"));
        //int indexOf(String str)
        int indexoftest = s1.indexOf(s1);
        System.out.println(s2.indexOf(s1)+"返回子串索引"+indexoftest+s1.indexOf("W")+s1.indexOf('w'));
        //int indexOf(String str, int fromIndex)
        System.out.println(s2.indexOf("o",2)+"从index处返回子串索引"+s2.indexOf("l",5));
        int index = s1.indexOf("l");
        System.out.println(s1.indexOf("l",index+1)+":"+index);
        System.out.println(s1.indexOf("l",s1.indexOf("l",index+1)+1)+":"+index);
    }
```

- ==String replace(char oldChar, char newChar)：返回一个新的字符串.它是通过用newChar替换此字符串中出现的所有oldChar得到的。== 

```java
String s1 = new String("Hello World!");
String s2 = new String("China");
String s3 = new String("Hello");
//String replace(char oldChar, char newChar)
String replacetest  = s1.replace(" ",s2);
System.out.println(s1.replace(s3,replacetest)+"转换"+replacetest);
char c1 = s3.charAt(2);
System.out.println(c1);
System.out.println(s1.replace("l",s2));
//System.out.println(s2.replace(s2.substring(0,2),s1)+"转换"+s2.replace(c1,"s"));
```

- String replace(CharSequence target, CharSequence replacement)：使用指定的字面值替换序列替换此字符串所有匹配字面值目标序列的子字符串。 
- String replaceAll(String regex, String replacement)：使用replacement替换此字符串所有匹配给定的正则表达式的子字符串。 
- String replaceFirst(String regex,String replacement)使用replacement替换此字符串匹配给定的正则表达式的第一个子字符串。
- boolean matches(String regex)：告知此字符串是否匹配给定的正则表达式。
- String[] split(String regex)：根据给定正则表达式的匹配拆分此字符串。 
- String[] split(String regex, int limit)：根据匹配给定的正则表达式来拆分此字符串，最多不超过limit个，如果超过了，剩下的全部都放到最后一个元素中。
- 替换

```java
String str = "12hello34world5java7891mysql456";
//把字符串中的数字替换成,，如果结果中开头和结尾有，的话去掉
String string = str.replaceAll("\\d+", ",").replaceAll("^,|,$", "");
System.out.println(string);
```

- 判断

```Java
String str = "12345";
//判断str字符串中是否全部有数字组成，即有1-n个数字组成
boolean matches = str.matches("\\d+");
System.out.println(matches);
String tel = "0571-4534289";
//判断这是否是一个杭州的固定电话
boolean result = tel.matches("0571-\\d{7,8}");
System.out.println(result);
```

- 切片

```
String str = "hello|world|java";
String[] strs = str.split("\\|");
for (int i = 0; i < strs.length; i++) {
	System.out.println(strs[i]);
}
System.out.println();
String str2 = "hello.world.java";
String[] strs2 = str2.split("\\.");
for (int i = 0; i < strs2.length; i++) {
	System.out.println(strs2[i]);
}
```

### 1.7数据转换

- 字符串 转基本数据类型、包装类**parse**

```java
Integer包装类的public static int parseInt(String s)：可以将由“数字”字符组成的字符串转换为整型
int num  = Integer.parseInt(str);
```

- 基本数据类型、包装类转字符串

```java 
调用String类的public String valueOf(int n)可将int型转换为字符串
String str = String.valueOf(num);
```

- 字符数组转字符串

```java
String 类的构造器String(char[])和String(char[],int offset,int length)分别用字符数组中的全部字符和部分字符创建字符串对象。
String str = new String(arr);
```

- 字符串转字符数组

```java
public char[] toCharArray()：将字符串中的全部字符存放在一个字符数组中的方法。
public void getChars(int srcBegin, int srcEnd, char[] dst, int dstBegin)：提供了将指定索引范围内的字符串存放到数组中的方法。
char[] chararray = str.toCharArray();//将str转换成char数组
String str = new String("helloworld");
       //public void getChars(int srcBegin, int srcEnd, char[] dst, int dstBegin)
       char[] arr = new char[10];
       str.getChars(2,6,arr,0);
       System.out.println(Arrays.toString(arr));
       System.out.println(str);
```

- 字节数组   转    字符串

```java
String(byte[])：通过使用平台的默认字符集解码指定的 byte 数组，构造一个新的 String。 
String(byte[]，int offset，int length) ：用指定的字节数组的一部分，即从数组起始位置offset开始取length个字节构造一个字符串对象。
```

- 字符串 转 字节数组

```java
public byte[] getBytes() ：使用平台的默认字符集将此 String 编码为byte 序列，并将结果存储到一个新的 byte 数组中。 
public byte[] getBytes(String charsetName) ：使用指定的字符集将 此 String 编码到 byte 序列，并将结果存储到新的 byte 数组。
```

- 案例

```java
String str = "中";
System.out.println(str.getBytes("ISO8859-1").length);// -128~127
System.out.println(str.getBytes("GBK").length);
System.out.println(str.getBytes("UTF-8").length);
System.out.println(new String(str.getBytes("ISO8859-1"), "ISO8859-1"));// 乱码，表示不了中文
System.out.println(new String(str.getBytes("GBK"), "GBK"));
System.out.println(new String(str.getBytes("UTF-8"), "UTF-8"));
```

- 练习

```java
1. 模拟一个trim方法，去除字符串两端的空格。
public String myTrim(String str) {
		if (str != null) {
			int start = 0;// 用于记录从前往后首次索引位置不是空格的位置的索引
			int end = str.length() - 1;// 用于记录从后往前首次索引位置不是空格的位置的索引
			while (start < end && str.charAt(start) == ' ') {
				start++;
			}
			while (start < end && str.charAt(end) == ' ') {
				end--;
			}
			if (str.charAt(start) == ' ') {
				return "";
			}
			return str.substring(start, end + 1);
		}
		return null;
	}    
2. 将一个字符串进行反转。将字符串中指定部分进行反转。比如“abcdefg”反转为”abfedcg”
	// 方式一：
	public String reverse1(String str, int start, int end) {// start:2,end:5
		if (str != null) {
			// 1.
			char[] charArray = str.toCharArray();
			// 2.
			for (int i = start, j = end; i < j; i++, j--) {
				char temp = charArray[i];
				charArray[i] = charArray[j];
				charArray[j] = temp;
			}
			// 3.
			return new String(charArray);

		}
		return null;

	}

	// 方式二：
	public String reverse2(String str, int start, int end) {
		// 1.
		String newStr = str.substring(0, start);// ab
		// 2.
		for (int i = end; i >= start; i--) {
			newStr += str.charAt(i);
		} // abfedc
			// 3.
		newStr += str.substring(end + 1);
		return newStr;
	}
	// 方式三：推荐 （相较于方式二做的改进）
	public String reverse3(String str, int start, int end) {// ArrayList list = new ArrayList(80);
		// 1.
		StringBuffer s = new StringBuffer(str.length());
		// 2.
		s.append(str.substring(0, start));// ab
		// 3.
		for (int i = end; i >= start; i--) {
			s.append(str.charAt(i));
		}

		// 4.
		s.append(str.substring(end + 1));

		// 5.
		return s.toString();
	}
	@Test
	public void testReverse() {
		String str = "abcdefg";
		String str1 = reverse3(str, 2, 5);
		System.out.println(str1);// abfedcg
	}    
3. 获取一个字符串在另一个字符串中出现的次数。比如：获取“ ab”在 “abkkcadkabkebfkabkskab” 中出现的次数
// 判断str2在str1中出现的次数
	public int getCount(String mainStr, String subStr) {
		if (mainStr.length() >= subStr.length()) {
			int count = 0;
			int index = 0;
			// while((index = mainStr.indexOf(subStr)) != -1){
			// count++;
			// mainStr = mainStr.substring(index + subStr.length());
			// }
			// 改进：
			while ((index = mainStr.indexOf(subStr, index)) != -1) {
				index += subStr.length();
				count++;
			}

			return count;
		} else {
			return 0;
		}

	}

	@Test
	public void testGetCount() {
		String str1 = "cdabkkcadkabkebfkabkskab";
		String str2 = "ab";
		int count = getCount(str1, str2);
		System.out.println(count);
	}

	@Test
	public void testMyTrim() {
		String str = "   a   ";
		// str = " ";
		String newStr = myTrim(str);
		System.out.println("---" + newStr + "---");
	}    
4.获取两个字符串中最大相同子串。比如：
str1 = "abcwerthelloyuiodef“;str2 = "cvhellobnm"
提示：将短的那个串进行长度依次递减的子串与较长的串比较。
// 如果只存在一个最大长度的相同子串
	public String getMaxSameSubString(String str1, String str2) {
		if (str1 != null && str2 != null) {
			String maxStr = (str1.length() > str2.length()) ? str1 : str2;
			String minStr = (str1.length() > str2.length()) ? str2 : str1;

			int len = minStr.length();

			for (int i = 0; i < len; i++) {// 0 1 2 3 4 此层循环决定要去几个字符

				for (int x = 0, y = len - i; y <= len; x++, y++) {

					if (maxStr.contains(minStr.substring(x, y))) {

						return minStr.substring(x, y);
					}

				}

			}
		}
		return null;
	}

	// 如果存在多个长度相同的最大相同子串
	// 此时先返回String[]，后面可以用集合中的ArrayList替换，较方便
	public String[] getMaxSameSubString1(String str1, String str2) {
		if (str1 != null && str2 != null) {
			StringBuffer sBuffer = new StringBuffer();
			String maxString = (str1.length() > str2.length()) ? str1 : str2;
			String minString = (str1.length() > str2.length()) ? str2 : str1;

			int len = minString.length();
			for (int i = 0; i < len; i++) {
				for (int x = 0, y = len - i; y <= len; x++, y++) {
					String subString = minString.substring(x, y);
					if (maxString.contains(subString)) {
						sBuffer.append(subString + ",");
					}
				}
				System.out.println(sBuffer);
				if (sBuffer.length() != 0) {
					break;
				}
			}
			String[] split = sBuffer.toString().replaceAll(",$", "").split("\\,");
			return split;
		}

		return null;
	}
	// 如果存在多个长度相同的最大相同子串：使用ArrayList
//	public List<String> getMaxSameSubString1(String str1, String str2) {
//		if (str1 != null && str2 != null) {
//			List<String> list = new ArrayList<String>();
//			String maxString = (str1.length() > str2.length()) ? str1 : str2;
//			String minString = (str1.length() > str2.length()) ? str2 : str1;
//
//			int len = minString.length();
//			for (int i = 0; i < len; i++) {
//				for (int x = 0, y = len - i; y <= len; x++, y++) {
//					String subString = minString.substring(x, y);
//					if (maxString.contains(subString)) {
//						list.add(subString);
//					}
//				}
//				if (list.size() != 0) {
//					break;
//				}
//			}
//			return list;
//		}
//
//		return null;
//	}

	@Test
	public void testGetMaxSameSubString() {
		String str1 = "abcwerthelloyuiodef";
		String str2 = "cvhellobnmiodef";
		String[] strs = getMaxSameSubString1(str1, str2);
		System.out.println(Arrays.toString(strs));
	}    
    

5.对字符串中字符进行自然顺序排序。
提示：
1）字符串变成字符数组。
2）对数组排序，选择，冒泡，Arrays.sort();
3）将排序后的数组变成字符串。
@Test
	public void testSort() {
		String str = "abcwerthelloyuiodef";
		char[] arr = str.toCharArray();
		Arrays.sort(arr);
		String newStr = new String(arr);
		System.out.println(newStr);
	}    
```

## 二、StringBuffer、StringBuilder

### 2.1StringBuffer、StringBuilder

- 可变的字符序列
- 改变值，不会产生新的对象
- 作为参数传递时，方法内部可以改变值
- 必须new初始化，

```java
StringBuffer()：初始容量为16的字符串缓冲区，长度为0
StringBuffer(int size)：构造指定容量的字符串缓冲区
StringBuffer(String str)：将内容初始化为指定字符串内容，一直都有16个字符的空区域
```

```java
String str = null;
StringBuffer sb = new StringBuffer();
sb.append(str);
System.out.println(sb.length());//4
System.out.println(sb);//"null"
StringBuffer sb1 = new StringBuffer(str);
System.out.println(sb1);//报错
```

### 2.2StringBuffer

- 线程安全，效率低

### 2.3StringBuilder

- 线程不安全，效率高

### 2.4方法

- ==StringBuffer **append**(xxx)：提供了很多的append()方法，用于进行字符串拼接==

- StringBuffer **delete**(int start,int end)：删除指定位置的内容

- StringBuffer **replace**(int start, int end, String str)：把[start,end)位置替换为str

- StringBuffer **insert**(int offset, xxx)：在指定位置插入xxx

- StringBuffer **reverse**() ：把当前字符序列逆转

  - 当append和insert时，如果原来value数组长度不够，可扩容。 

  - 如上这些方法支持方法链操作

- public int **indexOf**(String str)，第一个子串的索引
- public String **substring**(int start,int end)，切片，返回指定位置的字符串
- public int **length**()
- public char **charAt**(int n )
- public void **setCharAt**(int n ,char ch)，指定字符换成新的

### 2.5总结方法

- 增：append，
- 删：delete，
- 改：setCharAt，replace
- 查：charAt，
- 插：insert，
- 长度：length，

# 日期时间

## 一、JDK8之前的日期时间

- 可变性：像日期和时间这样的类应该是不可变的。
- 偏移性：Date中的年份是从1900开始的，而月份都从0开始。
- 格式化：格式化只对Date有用，Calendar则不行。
- 此外，它们也不是线程安全的；不能处理闰秒等。

![java.date](D:\桌面\数学建模笔记\数学建模笔记图片\image-20210812113604564.png)

### 1.1 java.lang.System类

- 时间标准：UTC(Coordinated Universal Time)

  GMT(Greenwich Mean Time)

  CST(Central Standard Time)

- 计算时间差

```java
//返回当前时间与1970年1月1日0时0分0秒之间以毫秒为单位的时间差
public static long currentTimeMillis()//时间戳
long starttime = System.currentTimeMillis();
long endtime = System.currentTimeMillis();
```

### 1.2java.util.Date类

- 表示特定的瞬间，精确到毫秒

```Java
//构造器
Date()：使用无参构造器创建的对象可以获取本地当前时间。
    Date date = new Date();
Date(long date)
    Date date1 = new Date(date.getTime());
//常用方法
getTime():返回自1970年1月1日00:00:00GMT以来此Date对象表示的毫秒数。
    System.out.println(date.getTime());//1628740065462
toString():把此Date对象转换为以下形式的 String：dow mon dd hh:mm:ss zzz yyyy 其中：dow 是一周中的某一天 (Sun, Mon, Tue, Wed, Thu, Fri, Sat)，zzz是时间标准
    System.out.println(date1.toString());//Thu Aug 12 11:47:45 CST 2021
```

```java
import java.util.Date;
Date date = new Date();
System.out.println(date);//Thu Aug 12 11:47:45 CST 2021
System.out.println(System.currentTimeMillis());//1628740065489
System.out.println(date.getTime());//1628740065462
Date date1 = new Date(date.getTime());
System.out.println(date1.getTime());//1628740065462
System.out.println(date1.toString());//Thu Aug 12 11:47:45 CST 2021
```

```java
//Java.sql.Date类
实例化：Java.sql.Date datesql = new Java.sql.Date(64654654546465L);//有toString和getTime方法
//将Java.util.Date对象转换为Java.sql.Date对象
//多态
Date datesql = new java.sql.Date(55656565656L);
java.sql.Date datesql1 = (java.sql.Date) datesql;
//转换
Date date = new Date();
java.sql.Date datesql2 = new java.sql.Date(date.getTime());
.System.out.println(datesql2);
```

### 1.3java.text.SimpleDateFormat类

- Date类的API不易于国际化，大部分被废弃了，java.text.SimpleDateFormat类是一个不与语言环境有关的方式来格式化和解析日期的具体类。
- 它允许进行**格式化**：日期->文本、**解析**：文本->日期

```java
//格式化
SimpleDateFormat() ：默认的模式和语言环境创建对象
public SimpleDateFormat(String pattern)：该构造方法可以用参数pattern指定的格式创建一个对象，该对象调用：
public String format(Date date)：方法格式化时间对象date
//解析
public Date parse(String source)：从给定字符串的开始解析文本，以生成一个日期
```

```java
Date date = new Date(); // 产生一个Date实例
// 产生一个formater格式化的实例
SimpleDateFormat formater = new SimpleDateFormat();
System.out.println(formater.format(date));// 打印输出默认的格式
SimpleDateFormat formater2 = new SimpleDateFormat("yyyy年MM月dd日 EEE HH:mm:ss");
System.out.println(formater2.format(date));
try {
// 实例化一个指定的格式对象
	Date date2 = formater2.parse("2008年08月08日 星期一 08:08:08");
// 将指定的日期解析后格式化按指定的格式输出
	System.out.println(date2.toString());
} catch (ParseException e) {
	e.printStackTrace();
}
```



- <img src="D:\桌面\数学建模笔记\数学建模笔记图片\image-20210812115334700.png" alt="image-20210812115334700" style="zoom:67%;" />
- 三天打鱼俩天晒网

```

```

### 1.4==java.util.Calendar(日历)类==

- Calendar是一个**抽象基类**，主用用于完成日期字段之间相互操作的功能

```Java
//获取Calendar实例的方法
使用Calendar.getInstance()方法//静态方法
调用它的子类GregorianCalendar的构造器
//一个Calendar的实例是系统时间的抽象表示，通过get(int field)方法来取得想要的时间信息。比如YEAR、MONTH、DAY_OF_WEEK、HOUR_OF_DAY 、MINUTE、SECOND
public void set(int field,int value)
public void add(int field,int amount)
public final Date getTime()
public final void setTime(Date date)
//注意:
获取月份时：一月是0，二月是1，以此类推，12月是11
获取星期时：周日是1，周二是2 ， 。。。。周六是7
```

```java
Calendar calendar = Calendar.getInstance();
int days = calendar.get(Calendar.DAY_OF_MONTH);//找今天是今年的天数
//System.out.println(Calendar.getClass());//查看类
// 从一个 Calendar 对象中获取 Date 对象
Date date = calendar.getTime();
// 使用给定的 Date 设置此 Calendar 的时间
date = new Date(234234235235L);
calendar.setTime(date);
calendar.set(Calendar.DAY_OF_MONTH, 8);
System.out.println("当前时间日设置为8后,时间是:" + calendar.getTime());
calendar.add(Calendar.HOUR, 2);
System.out.println("当前时间加2小时后,时间是:" + calendar.getTime());
calendar.add(Calendar.MONTH, -2);
System.out.println("当前日期减2个月后,时间是:" + calendar.getTime());
```

## 二、JDK8之后的日期时间

### 2.1新java.time包

- java.time – 包含值对象的基础包
  - ==**本地日期（LocalDate）类**==
  - ==**本地时间（LocalTime）类**==
  - ==**本地日期时间（LocalDateTime）类**==
  - 时区（ZonedDateTime）类
  - 持续时间（Duration）类
- java.time.chrono – 提供对不同的日历系统的访问
- ==java.time.format – 格式化和解析时间和日期==
- java.time.temporal – 包括底层框架和扩展特性
- java.time.zone – 包含时区支持的类

### 2.2LocalDate，LocalTime，==LocalDateTime==

- 实例是不可变的对象

- 本地日期（LocalDate）代表IOS格式（yyyy-MM-dd）的日期,可以存储 生日、纪念日等日期

- 本地时间（LocalTime）表示一个时间，而不是日期

- ==本地日期时间（LocalDateTime）==是用来表示日期和时间的，这是一个最常用的类之一

- ```java
  //now():获取当前的日期，时间，日期+时间
  LocalDate localDate = LocalDate.now();
  LocalTime localTime = LocalTime.now();
  LocalDateTime localDateTime = LocalDateTime.now();
  System.out.println(localDate);
  System.out.println(localTime);
  System.out.println(localDateTime);
  //of():设定指定的年月日分秒，没有偏移量
  LocalDateTime localDateTime1 = LocalDateTime.of(2020, 10, 6, 13, 13, 43);
  System.out.println(localDateTime1);
  //getXXX()：获取相关信息
  System.out.println(localDateTime.getDayOfMonth());
  System.out.println(localDateTime.getDayOfWeek());
  System.out.println(localDateTime.getDayOfYear());
  System.out.println(localDateTime.getHour());
  //withXXX():设置相关信息
  LocalDate localDate1 = localDate.withDayOfMonth(22);
  System.out.println(localDate);//体现了不可变性
  System.out.println(localDate1);
  //plusXXX():加上相关信息
  LocalDateTime localDateTime2 = localDateTime.plusMonths(3);
  System.out.println(localDateTime);
  System.out.println(localDateTime2);
  //minusXXX():减去相关信息
  ```

| **方法**                                                     | **描述**                                                     |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| **now() / \* now(ZoneId zone)**                              | 静态方法，根据当前时间创建对象/指定时区的对象                |
| **of()**                                                     | 静态方法，根据指定日期/时间创建对象                          |
| **getDayOfMonth()/getDayOfYear()**                           | 获得月份天数(1-31) /获得年份天数(1-366)                      |
| **getDayOfWeek()**                                           | 获得星期几(返回一个 DayOfWeek 枚举值)                        |
| **getMonth()**                                               | 获得月份, 返回一个 Month 枚举值                              |
| **getMonthValue() / getYear()**                              | 获得月份(1-12) /获得年份                                     |
| **getHour()/getMinute()/getSecond()**                        | 获得当前对象对应的小时、分钟、秒                             |
| **withDayOfMonth()/withDayOfYear()/**withMonth()/withYear()  | 将月份天数、年份天数、月份、年份修改为指定的值并返回新的对象 |
| **plusDays(), plusWeeks(),** **plusMonths(),plusYears(),plusHours()** | 向当前对象添加几天、几周、几个月、几年、几小时               |
| **minusMonths() / minusWeeks()/minusDays()/minusYears()/minusHours()** | 从当前对象减去几月、几周、几天、几年、几小时                 |

### 2.3瞬时：Instant类

- Instant：时间线上的一个瞬时点。时间戳。时间戳是指格林威治时间1970年01月01日00时00分00秒(北京时间1970年01月01日08时00分00秒)起至现在的总秒数

-  (1 ns = 10-9 s) 1秒 = 1000毫秒 =10^6微秒=10^9纳秒,Instant的精度可以达到纳秒级

- ```java
  //now():获取本初子午线对应的标准时间
  Instant instant = Instant.now();
  System.out.println(instant);
  //添加时间的偏移量
  OffsetDateTime offsetDateTime = instant.atOffset(ZoneOffset.ofHours(8));
  System.out.println(offsetDateTime);
  //toEpochMilli():获取毫秒数
  long l = instant.toEpochMilli();
  System.out.println(l);
  //ofEpochMilli()通过给定的毫秒数获取Instant实例
  Instant instant1 = Instant.ofEpochMilli(1550475314878L);
  System.out.println(instant1);
  ```

| **方法**                          | **描述**                                                     |
| --------------------------------- | ------------------------------------------------------------ |
| **now()**                         | 静态方法，返回默认UTC时区的Instant类的对象                   |
| **ofEpochMilli(long epochMilli)** | 静态方法，返回在1970-01-01 00:00:00基础上加上指定毫秒数之后的Instant类的对象 |
| **atOffset(ZoneOffset offset)**   | 结合即时的偏移来创建一个 OffsetDateTime                      |
| **toEpochMilli()**                | 返回1970-01-01 00:00:00到当前时间的毫秒数，即为时间戳        |

### 2.4**格式化与解析日期或时间**

- java.time.format.==DateTimeFormatter==
- 提供了三种格式化方法：
  1. 预定义的标准格式。如：**ISO_LOCAL_DATE_TIME;ISO_LOCAL_DATE;ISO_LOCAL_TIME**
  2. 本地化相关的格式。如：ofLocalizedDateTime(FormatStyle.LONG)
  3. ==自定义的格式。如：ofPattern(“yyyy-MM-dd hh:mm:ss”)==

```java
//方式一
DateTimeFormatter formatter = DateTimeFormatter.ISO_LOCAL_DATE_TIME;
//格式化：日期->字符串
LocalDateTime localDateTime = LocalDateTime.now();
String str1 = formatter.format(localDateTime);
System.out.println(localDateTime);
System.out.println(str1);
//解析 字符串->日期
TemporalAccessor parse = formatter.parse("2021-08-13T16:43:51.929");
System.out.println(parse);
//方式二
DateTimeFormatter formatter1 = DateTimeFormatter.ofLocalizedDateTime(FormatStyle.SHORT);
//格式化
String str2 = formatter1.format(localDateTime);
System.out.println(str2);
//自定义
DateTimeFormatter formatter2 = DateTimeFormatter.ofPattern("yyyy-MM-dd hh:mm:ss");
//格式化
String str4 = formatter2.format(localDateTime.now());
System.out.println(str4);//2021-08-13 04:52:07
//解析
TemporalAccessor accessor = formatter2.parse("2021-08-13 04:52:07");
System.out.println(accessor);
```

| **方 法**                      | **描 述**                                                    |
| ------------------------------ | ------------------------------------------------------------ |
| **ofPattern(String pattern)**  | 静态方法 ， 返 回 一 个 指 定 字 符 串 格 式 的DateTimeFormatter |
| **format(TemporalAccessor t)** | 格式化一个日期、时间，返回字符串                             |
| **parse(CharSequence text)**   | 将指定格式的字符序列解析为一个日期、时间                     |

### 2.5其他API

- 

# Java比较器（对象排序）

## 1.1自然排序：java.lang.==Comparable接口==

- Comparable接口强行对实现它的每个类的对象进行整体排序。这种排序被称为类的自然排序
- 实现 Comparable 的类必须实现 ==compareTo==(Object obj) 方法，两个对象即通过 compareTo(Object obj) 方法的返回值来比较大小。==如果当前对象this大 于形参对象obj，则返回正整数，如果当前对象this小于形参对象obj，则返回负整数，如果当前对象this等于形参对象obj，则返回零==
- ==实现Comparable接口的对象列表（和数组）==可以通过 ==Collections.sort 或Arrays.sort==进行自动排序。实现此接口的对象可以用作有序映射中的键或有序集合中的元素，无需指定比较器
- 对于类 C 的每一个 e1 和 e2 来说，当且仅当 e1.compareTo(e2) == 0 与e1.equals(e2) 具有相同的 boolean 值时，类 C 的自然排序才叫做与 equals 一致。建议（虽然不是必需的）最好使自然排序与 equals 一致。
- **Comparable** **的典型实现**：(默认都是从小到大排列的)
  - **String**：按照字符串中字符的Unicode值进行比较
  - Character：按照字符的Unicode值来进行比较
  - 数值类型对应的**包装类**以及BigInteger、BigDecimal：按照它们对应的数值大小进行比较
  - Boolean：**true 对应的包装类实例大于 false 对应的包装类实例**
  - Date、Time等：**后面的日期时间比前面的日期时间大**

```Java
class Goods implements Comparable {
	private String name;
	private double price;
	//按照价格，比较商品的大小
	@Override
	public int compareTo(Object o) {
		if(o instanceof Goods) {
			Goods other = (Goods) o;
			if (this.price > other.price) {
				return 1;
			} else if (this.price < other.price) {
				return -1;
			}
			return 0;
            //return Double.compare(this.price,other.prace)//替换上面的代码
            //二级排序：
            //return this.name.compareTo(other.name);
		}
		throw new RuntimeException("输入的数据类型不一致");
	}
	//构造器、getter、setter、toString()方法略
}
public class ComparableTest{
	public static void main(String[] args) {
		Goods[] all = new Goods[4];
		all[0] = new Goods("《红楼梦》", 100);
		all[1] = new Goods("《西游记》", 80);
		all[2] = new Goods("《三国演义》", 140);
		all[3] = new Goods("《水浒传》", 120);
		Arrays.sort(all);
		System.out.println(Arrays.toString(all));
	} 
}
```



## 1.2定制排序：java.util.Comparator

- 当元素的类型没有实现java.lang.Comparable接口而又不方便修改代码，或者实现了java.lang.Comparable接口的排序规则不适合当前的操作，那么可以考虑使用Comparator的对象来排序，强行对多个对象进行整体排序的比较。
- 重写compare(Object o1,Object o2)方法，比较o1和o2的大小：如果方法返回正整数，则表示o1大于o2；如果返回0，表示相等；返回负整数，表示o1小于o2。
- 可以将 Comparator 传递给 sort 方法（如 Collections.sort 或 Arrays.sort），从而允许在排序顺序上实现精确控制
- 还可以使用 Comparator 来控制某些数据结构(如有序 set或有序映射)的顺序.或者为那些没有自然顺序的对象 collection 提供排序。

```java
Goods[] all = new Goods[4];
all[0] = new Goods("War and Peace", 100);
all[1] = new Goods("Childhood", 80);
all[2] = new Goods("Scarlet and Black", 140);
all[3] = new Goods("Notre Dame de Paris", 120);
Arrays.sort(all, new Comparator() {
	@Override
	public int compare(Object o1, Object o2) {
        if(o1 instanceof Goods&&o2 instanceof Goods){
            Goods g1 = (Goods) o1;
			Goods g2 = (Goods) o2;
			return g1.getName().compareTo(g2.getName());
            //return Integer.compare(g1.getAge(),g2.getAge())
        }
		throw new RuntimeException("输入的数据类型不一致");
	}
});
System.out.println(Arrays.toString(all));
//比较次数较少
Comparator com = new Comparator(){
    @Override
	public int compare(Object o1, Object o2) {
        //
    }
};
```

# System类

- System类代表系统，系统级的很多属性和控制方法都放置在该类的内部。该类位于java.lang包。

- 由于该类的构造器是private的，所以无法创建该类的对象，也就是无法实例化该类。其内部的成员变量和成员方法都是static的，所以也可以很方便的进行调用

- 成员变量

  - System类内部包含==in.out和err==三个成员变量，分别代表标准输入流(键盘输入).标准输出流(显示器)和标准错误输出流(显示器)。

- 成员方法

  - **native long currentTimeMillis()：**该方法的作用是返回当前的计算机时间，时间的表达格式为当前计算机时间和GMT时间(格林威治时间)1970年1月1号0时0分0秒所差的毫秒数

  - **void exit(int status)：**该方法的作用是退出程序。==其中status的值为0代表正常退出，非零代表异常退出==。**使用该方法可以在图形界面编程中实现程序的退出功能**等

  - **void gc()：**该方法的作用是请求系统进行垃圾回收。至于系统是否立刻回收，则取决于系统中垃圾回收算法的实现以及系统执行时的情况

  - ==**String getProperty(String key)==：**该方法的作用是获得系统中属性名为key的属性对应的值。系统中常见的属性名以及属性的作用如下表所示

    - | 属性名       | 属性说明           |
      | ------------ | ------------------ |
      | java.version | java运行时环境版本 |
      | java.home    | java安装目录       |
      | os.name      | 操作系统的名称     |
      | os.version   | 操作系统的版本     |
      | user.name    | 用户的账户名称     |
      | user.home    | 用户的主目录       |
      | user.dir     | 用户的当前工作目录 |

  ```java
  String javaVersion = System.getProperty("java.version");
  System.out.println("java的version:" + javaVersion);
  String javaHome = System.getProperty("java.home");
  System.out.println("java的home:" + javaHome);
  String osName = System.getProperty("os.name");
  System.out.println("os的name:" + osName);
  String osVersion = System.getProperty("os.version");
  System.out.println("os的version:" + osVersion);
  String userName = System.getProperty("user.name");
  System.out.println("user的name:" + userName);
  String userHome = System.getProperty("user.home");
  System.out.println("user的home:" + userHome);
  String userDir = System.getProperty("user.dir");
  System.out.println("user的dir:" + userDir);
  ```

# math类

- **java.lang.Math**提供了一系列静态方法用于科学计算。其方法的参数和返回
- **值类型一般为**double型。
- **abs** 绝对值
- **acos,asin,atan,cos,sin,tan** **三角函数**
- **sqrt** **平方根**
- **pow(double a,doble b) a**的b次幂
- **log** **自然对数**
- **exp e**为底指数
- **max(double a,double b)**
- **min(double a,double b)**
- ==**random()**== **返回**0.0到1.0的随机数
- **long round(double a) double**型数据a转换为long型（四舍五入）
- **toDegrees(double angrad)** **弧度**—>角度
- **toRadians(double angdeg)** **角度**—>弧度

# BigInteger与BigDecimal

## 一、BigInteger

- Integer类作为int的包装类，能存储的最大整型值为2 31-1，Long类也是有限的，最大为2 63-1。如果要表示再大的整数，不管是基本数据类型还是他们的包装类都无能为力，更不用说进行运算了
- java.math包的**BigInteger**可以表示不可变的任意精度的整数。BigInteger 提供所有 Java 的基本整数操作符的对应物，并提供java.lang.Math 的所有相关方法。另外，BigInteger 还提供以下运算：模算术、GCD 计算、质数测试、素数生成、位操作以及一些其他操作
- 构造器
  - **BigInteger**(String val)：根据字符串构建BigInteger对象
- **常用方法**
  1. public BigInteger **abs**()：返回此 BigInteger 的绝对值的 BigInteger。
  2. BigInteger **add**(BigInteger val) ：返回其值为 (this + val) 的 BigInteger
  3. BigInteger **subtract**(BigInteger val) ：返回其值为 (this - val) 的 BigInteger
  4. BigInteger **multiply**(BigInteger val) ：返回其值为 (this * val) 的 BigInteger
  5. BigInteger **divide**(BigInteger val) ：返回其值为 (this / val) 的 BigInteger。整数相除只保留整数部分
  6. BigInteger **remainder**(BigInteger val) ：返回其值为 (this % val) 的 BigInteger。
  7. BigInteger[] **divideAndRemainder**(BigInteger val)：返回包含 (this / val) 后跟(this % val) 的两个 BigInteger 的数组。
  8. BigInteger **pow**(int exponent) ：返回其值为 (thisexponent) 的 BigInteger。

## 二、BigDecimal

- 一般的Float类和Double类可以用来做科学计算或工程计算，但在商业计算中，要求数字精度比较高，故用到java.math.BigDecimal类。
- BigDecimal类支持不可变的、任意精度的有符号十进制定点数
- 构造器 
  - public BigDecimal(double val) 
  - public BigDecimal(String val) 
- 常用方法
  1. public BigDecimal **add**(BigDecimal augend)
  2. public BigDecimal **subtract**(BigDecimal subtrahend)
  3. public BigDecimal **multiply**(BigDecimal multiplicand)
  4. public BigDecimal **divide**(BigDecimal divisor, int scale, int roundingMode)

```java
public void testBigInteger() {
	BigInteger bi = new BigInteger("12433241123");
	BigDecimal bd = new BigDecimal("12435.351");
	BigDecimal bd2 = new BigDecimal("11");
	System.out.println(bi);
	// System.out.println(bd.divide(bd2));
	System.out.println(bd.divide(bd2, BigDecimal.ROUND_HALF_UP));
	System.out.println(bd.divide(bd2, 15, BigDecimal.ROUND_HALF_UP));//保留15位小数
}
```
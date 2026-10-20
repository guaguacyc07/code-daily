### 一、核心概述

`StringBuffer` 是 Java 中**线程安全的可变字符序列**，底层是可自动扩容的字符数组（JDK9 后优化为 byte 数组），适合频繁拼接、修改字符串的场景。

- 所有修改操作直接修改自身对象，不会像 `String` 一样生成新对象
- 核心方法均被 `synchronized` 修饰，多线程环境安全；单线程场景优先使用 `StringBuilder`（API 完全一致，无锁性能更高）

------

### 二、常用方法分类

#### 1. 追加内容：`append()`

最核心、最常用的方法，在字符序列末尾追加内容，支持所有基本类型、字符串、字符数组、对象等，**支持链式调用**。

```java
StringBuffer sb = new StringBuffer("Hello");
sb.append(" World");    // 追加字符串
sb.append('!');         // 追加单个字符
sb.append(123);         // 追加数字
sb.append(new char[]{'a','b'}); // 追加字符数组
// 最终结果：Hello World!123ab
```

#### 2. 指定位置插入：`insert()`

在指定下标位置插入内容，下标从 0 开始计数。

```java
StringBuffer sb = new StringBuffer("Hello");
sb.insert(2, "ABC"); 
// 结果：HeABCllo
```

#### 3. 删除内容

- `delete(int start, int end)`：删除 `[start, end)` 区间的字符，左闭右开
- `deleteCharAt(int index)`：删除指定下标位置的单个字符

```java
StringBuffer sb = new StringBuffer("abcdef");
sb.delete(1, 3);     // 删除下标1、2的字符 → adef
sb.deleteCharAt(0);  // 删除下标0的字符 → def
```

#### 4. 修改与替换

- `setCharAt(int index, char ch)`：修改指定下标位置的单个字符
- `replace(int start, int end, String str)`：把 `[start, end)` 区间的内容替换为指定字符串
- `reverse()`：反转整个字符序列

```java
StringBuffer sb = new StringBuffer("abcde");
sb.setCharAt(0, 'A');    // Abcde
sb.replace(1, 3, "XY");  // AXYde
sb.reverse();            // edYXA
```

#### 5. 查询与截取

- `charAt(int index)`：获取指定下标的字符
- `indexOf(String str)`：查找字符串第一次出现的下标，找不到返回 `-1`
- `lastIndexOf(String str)`：查找字符串最后一次出现的下标
- `substring(int start)` / `substring(int start, int end)`：截取子串，返回新的 `String`，不影响原对象
- `length()`：返回当前字符序列的实际长度

```java
StringBuffer sb = new StringBuffer("abcabc");
char c = sb.charAt(2);        // 'c'
int idx = sb.indexOf("bc");   // 1
String sub = sb.substring(2,4); // "ca"
int len = sb.length();        // 6
```

#### 6. 容量控制

`StringBuffer` 存在「容量（capacity）」概念，指底层数组的总大小，无参构造默认初始容量为 16；当字符长度超过容量时会自动扩容。

- `capacity()`：返回当前底层数组的总容量
- `ensureCapacity(int minimumCapacity)`：手动确保容量至少为指定值，避免频繁扩容
- `setLength(int newLength)`：设置字符序列长度，超出部分截断，不足部分补空字符 `\0`
- `trimToSize()`：将容量压缩到与实际长度一致，节省内存

```java
StringBuffer sb = new StringBuffer();
int cap = sb.capacity();  // 无参构造默认容量 16
sb.ensureCapacity(100);   // 扩容到至少 100
sb.setLength(5);          // 长度设为 5
sb.trimToSize();          // 容量收缩为 5
```

#### 7. 最终转换

- `toString()`：将 `StringBuffer` 转为 `String` 对象，是最常用的最终输出方式

```java
StringBuffer sb = new StringBuffer("hello");
String result = sb.toString(); // "hello"
```

------

### 三、补充说明

1. **链式调用**：`append`、`insert`、`delete`、`reverse` 等方法返回值都是 `StringBuffer` 本身，可以连续链式书写：

   ```java
   sb.append("a").append("b").insert(1, "c").reverse();
   ```

2. **空值处理**：调用 `append(null)` 时，会追加字符串 `"null"`，不会抛出空指针异常。

3. **构造初始化**：传入字符串初始化时，初始容量 = 字符串长度 + 16。
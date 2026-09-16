# Stack 常用 API（栈）

> 栈是 **LIFO**（后进先出），只能在**栈顶**进出。
>
> ⚠️ 刷题**一律用 `ArrayDeque` 代替 `Stack`**：`Stack` 是早期的线程安全类（每个方法都加锁），慢且已不推荐；官方文档也建议用 `Deque` 实现栈。
>
> 队列相关内容见 [Queue常用API.md](Queue常用API.md)（`Deque` 既能当队列也能当栈）。
>
> AI生成+人工优化

## 〇、先记住：别用 Stack，用 ArrayDeque

| 操作 | ❌ `Stack`（别用） | ✅ `Deque<Integer> st = new ArrayDeque<>()` |
| ---- | ------------------ | ------------------------------------------- |
| 压栈 | `st.push(x)` | `st.push(x)`（= `addFirst`） |
| 弹栈 | `st.pop()` | `st.pop()`（= `removeFirst`） |
| 看栈顶 | `st.peek()` | `st.peek()`（= `peekFirst`） |
| 判空 | `st.empty()` | `st.isEmpty()` |
| 大小 | `st.size()` | `st.size()` |
| 查找 | `st.search(x)` | 不支持，要自己遍历 |

> 方法名几乎一样，所以**换成 `Deque` 成本极低**，记住改掉 `empty()` → `isEmpty()`、别写 `search` 即可。

## 一、创建与初始化

```java
// ✅ 刷题标准写法
Deque<Integer> st = new ArrayDeque<>();

// 存字符（括号匹配等字符串题）
Deque<Character> stc = new ArrayDeque<>();

// 存下标（单调栈必须存下标，见"高频套路"）
Deque<Integer> idx = new ArrayDeque<>();

// ❌ 遗留写法，别用
// Stack<Integer> bad = new Stack<>();
```

## 二、增（压栈）

```java
st.push(1);         // 压栈，O(1) 均摊（Deque 里等于 addFirst）
st.addFirst(1);     // 同上，语义更"Deque"
st.offerFirst(1);   // 同上，失败返回 false（ArrayDeque 不会失败）
```

## 三、删（弹栈）

```java
st.pop();           // 弹出并返回栈顶，O(1)；⚠️ 空栈抛 NoSuchElementException
st.poll();          // 弹出并返回栈顶（= pollFirst），空栈返回 null
st.removeFirst();   // 同上
```

> 刷题里栈一般都是**先判空再弹**：`while (!st.isEmpty()) { ... st.pop(); }`

## 四、查（看栈顶，不弹出）

```java
st.peek();          // 看栈顶（不弹出），空栈返回 null
st.peekFirst();     // 同上
st.element();       // 看栈顶，空栈抛异常（少用）
st.size();          // 元素个数
st.isEmpty();       // 是否为空 ✅（注意不是 empty()）
```

## 五、改

栈只能栈顶进出，**没有"按下标改"**。`st.remove(x)` 能删掉第一个出现的 x，但要 O(n) 遍历，刷题基本用不到。

## 六、遍历

```java
// 1. for-each：⚠️ 顺序是【栈底 → 栈顶】，和弹栈顺序相反！
for (int x : st) { ... }

// 2. 边弹边遍历（顺序 = 栈顶 → 栈底，会清空栈）
while (!st.isEmpty()) {
    int x = st.pop();
    ...
}

// 3. 只读地从栈顶往下看（不清空，可间断）
for (int x : st) { ... }        // 想要栈顶优先就先把栈倒到另一个栈
```

> ⚠️ 想按"栈顶 → 栈底"顺序读又不想破坏栈：弹到临时栈再弹回来。

## 七、转换

```java
// Deque<Integer> → int[]
int[] arr = st.stream().mapToInt(Integer::intValue).toArray();

// Deque<Character> → String（字符栈常见）
StringBuilder sb = new StringBuilder();
for (char c : st) sb.append(c);        // 注意顺序是栈底→栈顶
String s = sb.reverse().toString();    // 需要反序时

// 其他集合 → 栈
Deque<Integer> st2 = new ArrayDeque<>(list);
```

## 八、复杂度对照

| 操作 | ArrayDeque | LinkedList | Stack（遗留） |
| ---- | ---------- | ---------- | ------------- |
| push / pop / peek | O(1) 均摊 | O(1) | O(1)（但有锁开销） |
| 判空 / size | O(1) | O(1) | O(1) |
| 查找（contains / remove） | O(n) | O(n) | O(n) |
| 按下标访问 | 不支持 | O(n) | O(n) |

## 九、刷题高频套路

### 1. 括号匹配（20. 有效的括号）

```java
Deque<Character> st = new ArrayDeque<>();
for (char c : s.toCharArray()) {
    if (c == '(') st.push(')');
    else if (c == '[') st.push(']');
    else if (c == '{') st.push('}');
    // 右括号：栈空 或 栈顶不匹配 → 直接 false
    else if (st.isEmpty() || st.pop() != c) return false;
}
return st.isEmpty();     // 左括号多了也不合法
```

### 2. 单调栈（739. 每日温度 / 84. 柱状图中最大的矩形）

**栈里存下标**，保证栈内对应的值单调（递增或递减）：

```java
// 找每个元素"右边第一个更大"的距离（每日温度）
int n = nums.length;
int[] ans = new int[n];
Deque<Integer> st = new ArrayDeque<>();   // 存下标，对应的值从栈底到栈顶递减

for (int i = 0; i < n; i++) {
    while (!st.isEmpty() && nums[st.peek()] < nums[i]) {
        int j = st.pop();                 // j 的右边第一个更大值就是 nums[i]
        ans[j] = i - j;
    }
    st.push(i);
}
```

> 口诀：**新元素比栈顶大（或小）就一直弹，弹出来的元素在这里找到了答案**。
> 84 题"柱状图最大矩形"是同一模板 + 首尾加哨兵 `0`。

### 3. 表达式求值（150. 逆波兰表达式）

```java
Deque<Integer> st = new ArrayDeque<>();
for (String t : tokens) {
    if (t.length() == 1 && "+-*/".indexOf(t.charAt(0)) >= 0) {
        int b = st.pop(), a = st.pop();          // ⚠️ 先弹出的是右操作数
        st.push(t.equals("+") ? a + b
              : t.equals("-") ? a - b
              : t.equals("*") ? a * b : a / b);
    } else {
        st.push(Integer.parseInt(t));
    }
}
return st.pop();
```

### 4. 迭代版二叉树遍历（用栈替代递归）

```java
// 中序遍历
Deque<TreeNode> st = new ArrayDeque<>();
TreeNode cur = root;
while (cur != null || !st.isEmpty()) {
    while (cur != null) {        // 一路向左压栈
        st.push(cur);
        cur = cur.left;
    }
    cur = st.pop();              // 弹出 = 访问
    ans.add(cur.val);
    cur = cur.right;             // 转向右子树
}
```

### 5. 最小栈（155. 最小栈）—— 辅助栈

```java
class MinStack {
    Deque<Integer> data = new ArrayDeque<>();
    Deque<Integer> min  = new ArrayDeque<>();   // 栈顶 = 当前最小值

    public void push(int val) {
        data.push(val);
        min.push(min.isEmpty() ? val : Math.min(min.peek(), val));
    }
    public void pop()   { data.pop(); min.pop(); }
    public int top()    { return data.peek(); }
    public int getMin() { return min.peek(); }
}
```

> 变体：**946. 验证栈序列**——按 pushed 压栈，栈顶等于 popped 就一直弹，最后看栈是否为空。

## 十、常见坑

1. **别用 `Stack`**：线程安全 = 有锁开销；用 `Deque<Integer> st = new ArrayDeque<>()`。
2. **空栈 `pop` / `peek` 抛异常**：`pop()` 抛 `NoSuchElementException`、`peek()` 返回 `null`；先 `isEmpty()` 判断，或者用 `poll()`。
3. **`empty()` 是 `Stack` 的老方法**：`Deque` 用 `isEmpty()`。
4. **`ArrayDeque` 不能存 `null`**：`push(null)` 抛 `NullPointerException`。
5. **for-each 顺序是栈底 → 栈顶**：和"弹栈顺序"相反，别搞混。
6. **单调栈存下标不存值**：存下标才能算距离、处理重复值。
7. **逆波兰求值注意操作数顺序**：先弹出的是**右**操作数，`a op b` 里 `a` 是后弹出的那个。

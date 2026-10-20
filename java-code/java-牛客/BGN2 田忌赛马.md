# **BGN2 田忌赛马**

- **题目 : [BGN2 田忌赛马](https://www.nowcoder.com/practice/49d799f65a0749588e9cd7e6135a4a9a)**

> 难度: 中等
>
> 标签: 暴力枚举

## 解法一

**AC代码:**

```java
import java.util.*;

public class Main {
    public static void main(String[] args){
        Scanner sc = new Scanner(System.in);
        int v1 = sc.nextInt();
        int v2 = sc.nextInt();
        int v3 = sc.nextInt();

        int a1 = sc.nextInt();
        int a2 = sc.nextInt();
        int a3 = sc.nextInt();

        int vMax = Math.max(v1, Math.max(v2, v3));
        int vMin = Math.min(v1, Math.min(v2, v3));
        int vMid = v1 + v2 + v3 - vMax -vMin;

        int aMax = Math.max(a1, Math.max(a2, a3));
        int aMin = Math.min(a1, Math.min(a2, a3));
        int aMid = a1 + a2 + a3 -aMax - aMin;

        if(aMax > vMid && aMid > vMin) {
            System.out.print("Yes");
        } else {
            System.out.print("No");
        }
    } 
}
```

- **时间复杂度：O(1)**

  只读入并处理固定的 3+3 个数字，`Math.max`/`Math.min` 都是常数时间。

- **空间复杂度：O(1)**

  只用了常数个变量。


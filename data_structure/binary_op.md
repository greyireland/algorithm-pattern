# 二进制

## 常见二进制操作

### 基本操作

a=0^a=a^0

0=a^a

由上面两个推导出：a=a^b^b

### 交换两个数

a=a^b

b=a^b

a=a^b

### 移除最后一个 1

a=n&(n-1)

### 获取最后一个 1

diff=(n&(n-1))^n

## 常见题目

[single-number](https://leetcode-cn.com/problems/single-number/)

> 给定一个**非空**整数数组，除了某个元素只出现一次以外，其余每个元素均出现两次。找出那个只出现了一次的元素。

```c++
int singleNumber(vector<int>& nums) {
    // 10 ^10 == 00
    // 两个数异或就变成0
    auto result = 0;
    for (const auto &num : nums) {
        result ^= num;
    }
    return result;
}
```

[single-number-ii](https://leetcode-cn.com/problems/single-number-ii/)

> 给定一个**非空**整数数组，除了某个元素只出现一次以外，其余每个元素均出现了三次。找出那个只出现了一次的元素。

```c++
// 思路：每一位，统计有多少个1，除以3取余数
// 出现三次的，取余数为0，被过滤掉，剩下的都是只出现一次的那个数
int singleNumber(vector<int>& nums) {
    auto ret = 0;
    for (int i = 0; i < 32; ++i) {
        auto sumOne = 0;
        for (const auto &item : nums) {
            sumOne += (item >> i) & 1;
        }
        ret ^= (sumOne % 3) << i;
    }
    return ret;
}
```

[single-number-iii](https://leetcode-cn.com/problems/single-number-iii/)

> 给定一个整数数组  `nums`，其中恰好有两个元素只出现一次，其余所有元素均出现两次。 找出只出现一次的那两个元素。

```c++
vector<int> singleNumber(vector<int>& nums) {
    auto diff = 0;
    // 按位与，出现两次的数会被剔除掉
    for (const auto &item : nums) {
        diff ^= item;
    }
    vector<int> result(2);
    // 只出现一次的两个数不想等，则其异或的结果必然至少有一位为 1
    // 找到最后一个 '1'
    diff &= (-diff);
    // 再按该位上的值为1还是为0将所有的数分成两组进行异或把两个数分开
    for (const auto &num : nums) {
        if ((diff & num) == 0) {
            result[0] ^= num;
        } else {
            result[1] ^= num;
        }
    }
    return result;
}
```
> 关于 x & (-x)  
> 在计算机系统中，数值一律用补码来表示和存储  
> 一个正数 x 与其相反数 -x，只有一位同时为 '1'，并且总为 x 中的最后一位 '1'  
> 正数的补码与原码相同  
> 负数的补码，将其原码除符号位外的所有位取反后加1  
> 10和-10用8位表示为：  
>  10: 0000 1010  
> -10: 1111 1010
> 因此，x & (-x) 可用于求出x中最后一个'1'

[number-of-1-bits](https://leetcode-cn.com/problems/number-of-1-bits/)

> 编写一个函数，输入是一个无符号整数，返回其二进制表达式中数字位数为 ‘1’  的个数（也被称为[汉明重量](https://baike.baidu.com/item/%E6%B1%89%E6%98%8E%E9%87%8D%E9%87%8F)）。

```c++
int hammingWeight(int n) {
    auto ret = 0;
    while (n != 0) {
        n = n & (n - 1);
        ++ret;
    }
    return ret;
}
```

[counting-bits](https://leetcode-cn.com/problems/counting-bits/)

> 给定一个非负整数  **num**。对于  0 ≤ i ≤ num  范围中的每个数字  i ，计算其二进制数中的 1 的数目并将它们作为数组返回。

```c++
vector<int> countBits(int num) {
    vector<int> ret(num+1);
    for (int i = 0; i <= num; ++i) {
        ret[i] = hammingWeight(i);
    }
    return ret;
}

int hammingWeight(int n) {
    auto ret = 0;
    while (n != 0) {
        n = n & (n - 1);
        ++ret;
    }

    return ret;
}
```

另外一种动态规划解法

```c++
vector<int> countBits(int num) {
    vector<int> ret(num + 1);
    for (int i = 1; i <= num; ++i) {
        // 对于 i，其二进制1的个数 = 移除其最后一个1剩余的1的个数 + 1
        // i & (i - 1)移除最后一个1
        ret[i] = ret[i & (i - 1)] + 1;
    }
    return ret;
}
```

[reverse-bits](https://leetcode-cn.com/problems/reverse-bits/)

> 颠倒给定的 32 位无符号整数的二进制位。

思路：依次颠倒即可

```c++
uint32_t reverseBits(uint32_t n) {
    uint32_t ret = 0; // 记得初始化，否则通不过
    auto pow = 31;
    while(n != 0) {
        // 取最后一位进行左移
        ret += (n & 1) << pow;
        n >>= 1;
        --pow;
    }
    return ret;
}
```

[bitwise-and-of-numbers-range](https://leetcode-cn.com/problems/bitwise-and-of-numbers-range/)

> 给定范围 [m, n]，其中 0 <= m <= n <= 2147483647，返回此范围内所有数字的按位与（包含 m, n 两端点）。

```c++
/*
 * 思路：
 * 只要有一位不同时位1，则按位与结果为0
 * 因此，从m和n最左边一个不同时为1的位起，其右边的位都将被剔除，如：
 * m =  5    0101
 * n = 15    1111
 * 后面2位最终会变为0
 * 所以，掐头去尾取中间（左边起第二个1）么？
 * 不，n再大也没用，m跟n最左边的1必须在同一位，否则结果直接为0
 * 毕竟终归是会有一个中间的数，10000，按位与完，m就没了
 * 
 * 所以，m、n同时右移，直到二者相等（剩下最左边的若干个1，或者为0）
 * 再进行左移
 */
int rangeBitwiseAnd(int m, int n) {
    auto zeroNum = 0;
    while (m != n) {
        m >>= 1;
        n >>= 1;
        ++zeroNum;
    }
    return m << zeroNum;
}
```

## 练习

- [ ] [single-number](https://leetcode-cn.com/problems/single-number/)
- [ ] [single-number-ii](https://leetcode-cn.com/problems/single-number-ii/)
- [ ] [single-number-iii](https://leetcode-cn.com/problems/single-number-iii/)
- [ ] [number-of-1-bits](https://leetcode-cn.com/problems/number-of-1-bits/)
- [ ] [counting-bits](https://leetcode-cn.com/problems/counting-bits/)
- [ ] [reverse-bits](https://leetcode-cn.com/problems/reverse-bits/)

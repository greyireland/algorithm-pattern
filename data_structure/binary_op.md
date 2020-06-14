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

```go
func singleNumber(nums []int) int {
    // 10 ^10 == 00
    // 两个数异或就变成0
    result:=0
    for i:=0;i<len(nums);i++{
        result=result^nums[i]
    }
    return result
}
```

[single-number-ii](https://leetcode-cn.com/problems/single-number-ii/)

> 给定一个**非空**整数数组，除了某个元素只出现一次以外，其余每个元素均出现了三次。找出那个只出现了一次的元素。

```go
func singleNumber(nums []int) int {
	// 统计每位1的个数
	var result int
	for i := 0; i < 64; i++ {
		sum := 0
		for j := 0; j < len(nums); j++ {
			// 统计1的个数
			sum += (nums[j] >> i) & 1
		}
		// 还原位00^10=10 或者用| 也可以
		result ^= (sum % 3) << i
	}
	return result
}
```

[single-number-iii](https://leetcode-cn.com/problems/single-number-iii/)

> 给定一个整数数组  `nums`，其中恰好有两个元素只出现一次，其余所有元素均出现两次。 找出只出现一次的那两个元素。

```go
func singleNumber(nums []int) []int {
    // a=a^b
    // b=a^b
    // a=a^b
    // 关键点怎么把a^b分成两部分,方案：可以通过diff最后一个1区分

    diff:=0
    for i:=0;i<len(nums);i++{
        diff^=nums[i]
    }
    result:=[]int{diff,diff}
    // 去掉末尾的1后异或diff就得到最后一个1的位置
    diff=(diff&(diff-1))^diff
    for i:=0;i<len(nums);i++{
        if diff&nums[i]==0{
            result[0]^=nums[i]
        }else{
            result[1]^=nums[i]
        }
    }
    return result
}
```

[number-of-1-bits](https://leetcode-cn.com/problems/number-of-1-bits/)

> 编写一个函数，输入是一个无符号整数，返回其二进制表达式中数字位数为 ‘1’  的个数（也被称为[汉明重量](https://baike.baidu.com/item/%E6%B1%89%E6%98%8E%E9%87%8D%E9%87%8F)）。

```go
func hammingWeight(num uint32) int {
    res:=0
    for num!=0{
        num=num&(num-1)
        res++
    }
    return res
}
```

[counting-bits](https://leetcode-cn.com/problems/counting-bits/)

> 给定一个非负整数  **num**。对于  0 ≤ i ≤ num  范围中的每个数字  i ，计算其二进制数中的 1 的数目并将它们作为数组返回。

```go
func countBits(num int) []int {
    res:=make([]int,num+1)

    for i:=0;i<=num;i++{
        res[i]=count1(i)
    }
    return res
}
func count1(n int)(res int){
    for n!=0{
        n=n&(n-1)
        res++
    }
    return
}
```

另外一种动态规划解法

```go
func countBits(num int) []int {
    res:=make([]int,num+1)
    for i:=1;i<=num;i++{
        // 上一个缺1的元素+1即可
        res[i]=res[i&(i-1)]+1
    }
    return res
}
```

[reverse-bits](https://leetcode-cn.com/problems/reverse-bits/)

> 颠倒给定的 32 位无符号整数的二进制位。

思路：依次颠倒即可

```go
func reverseBits(num uint32) uint32 {
    var res uint32
    var pow int=31
    for num!=0{
        // 把最后一位取出来，左移之后累加到结果中
        res+=(num&1)<<pow
        num>>=1
        pow--
    }
    return res
}
```

[bitwise-and-of-numbers-range](https://leetcode-cn.com/problems/bitwise-and-of-numbers-range/)

> 给定范围 [m, n]，其中 0 <= m <= n <= 2147483647，返回此范围内所有数字的按位与（包含 m, n 两端点）。

```go
func rangeBitwiseAnd(m int, n int) int {
    // m 5 1 0 1
    //   6 1 1 0
    // n 7 1 1 1
    // 把可能包含0的全部右移变成
    // m 5 1 0 0
    //   6 1 0 0
    // n 7 1 0 0
    // 所以最后结果就是m<<count
    var count int
    for m!=n{
        m>>=1
        n>>=1
        count++
    }
    return m<<count
}
```

## 练习

- [ ] [single-number](https://leetcode-cn.com/problems/single-number/)
- [ ] [single-number-ii](https://leetcode-cn.com/problems/single-number-ii/)
- [ ] [single-number-iii](https://leetcode-cn.com/problems/single-number-iii/)
- [ ] [number-of-1-bits](https://leetcode-cn.com/problems/number-of-1-bits/)
- [ ] [counting-bits](https://leetcode-cn.com/problems/counting-bits/)
- [ ] [reverse-bits](https://leetcode-cn.com/problems/reverse-bits/)

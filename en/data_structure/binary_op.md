# Binary

## Common Binary Operations

### Basic Operations

a=0^a=a^0

0=a^a

From the two above we can derive: a=a^b^b

### Swap Two Numbers

a=a^b

b=a^b

a=a^b

### Remove the Last 1

a=n&(n-1)

### Get the Last 1

diff=(n&(n-1))^n

## Common Problems

[single-number](https://leetcode-cn.com/problems/single-number/)

> Given a **non-empty** integer array, every element appears twice except for one. Find that single element.

```go
func singleNumber(nums []int) int {
    // 10 ^10 == 00
    // XOR of two identical numbers gives 0
    result:=0
    for i:=0;i<len(nums);i++{
        result=result^nums[i]
    }
    return result
}
```

[single-number-ii](https://leetcode-cn.com/problems/single-number-ii/)

> Given a **non-empty** integer array, every element appears three times except for one. Find that single element.

```go
func singleNumber(nums []int) int {
	// Count the number of 1s in each bit
	var result int
	for i := 0; i < 64; i++ {
		sum := 0
		for j := 0; j < len(nums); j++ {
			// Count the number of 1s
			sum += (nums[j] >> i) & 1
		}
		// Restore the bit 00^10=10, or you can also use |
		result ^= (sum % 3) << i
	}
	return result
}
```

[single-number-iii](https://leetcode-cn.com/problems/single-number-iii/)

> Given an integer array `nums`, in which exactly two elements appear only once and all the other elements appear exactly twice. Find the two elements that appear only once.

```go
func singleNumber(nums []int) []int {
    // a=a^b
    // b=a^b
    // a=a^b
    // The key point is how to split a^b into two parts. Approach: distinguish by the last 1 of diff

    diff:=0
    for i:=0;i<len(nums);i++{
        diff^=nums[i]
    }
    result:=[]int{diff,diff}
    // After removing the trailing 1, XOR with diff to get the position of the last 1
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

> Write a function that takes an unsigned integer and returns the number of '1' bits in its binary representation (also known as the [Hamming weight](https://baike.baidu.com/item/%E6%B1%89%E6%98%8E%E9%87%8D%E9%87%8F)).

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

> Given a non-negative integer **num**. For every number i in the range 0 ≤ i ≤ num, calculate the number of 1s in its binary representation and return them as an array.

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

Another dynamic programming solution

```go
func countBits(num int) []int {
    res:=make([]int,num+1)
    for i:=1;i<=num;i++{
        // Just take the previous element that is missing one 1 and add 1
        res[i]=res[i&(i-1)]+1
    }
    return res
}
```

[reverse-bits](https://leetcode-cn.com/problems/reverse-bits/)

> Reverse the binary bits of a given 32-bit unsigned integer.

Idea: reverse bit by bit

```go
func reverseBits(num uint32) uint32 {
    var res uint32
    var pow int=31
    for num!=0{
        // Take out the last bit, left-shift it, and accumulate into the result
        res+=(num&1)<<pow
        num>>=1
        pow--
    }
    return res
}
```

[bitwise-and-of-numbers-range](https://leetcode-cn.com/problems/bitwise-and-of-numbers-range/)

> Given a range [m, n] where 0 <= m <= n <= 2147483647, return the bitwise AND of all numbers in this range (including both endpoints m and n).

```go
func rangeBitwiseAnd(m int, n int) int {
    // m 5 1 0 1
    //   6 1 1 0
    // n 7 1 1 1
    // Right-shift away all the parts that may contain 0, turning into
    // m 5 1 0 0
    //   6 1 0 0
    // n 7 1 0 0
    // So the final result is m<<count
    var count int
    for m!=n{
        m>>=1
        n>>=1
        count++
    }
    return m<<count
}
```

## Exercises

- [ ] [single-number](https://leetcode-cn.com/problems/single-number/)
- [ ] [single-number-ii](https://leetcode-cn.com/problems/single-number-ii/)
- [ ] [single-number-iii](https://leetcode-cn.com/problems/single-number-iii/)
- [ ] [number-of-1-bits](https://leetcode-cn.com/problems/number-of-1-bits/)
- [ ] [counting-bits](https://leetcode-cn.com/problems/counting-bits/)
- [ ] [reverse-bits](https://leetcode-cn.com/problems/reverse-bits/)

# Go Quick Start

## Basic Syntax

[The Go Programming Language (Chinese)](https://books.studygolang.com/gopl-zh/)

## Common Libraries

### Slices

Go simulates stacks and queues using slices.

Stack

```go
// Create a stack
stack:=make([]int,0)
// push
stack=append(stack,10)
// pop
v:=stack[len(stack)-1]
stack=stack[:len(stack)-1]
// Check if the stack is empty
len(stack)==0
```

Queue

```go
// Create a queue
queue:=make([]int,0)
// enqueue
queue=append(queue,10)
// dequeue
v:=queue[0]
queue=queue[1:]
// A length of 0 means empty
len(queue)==0
```

Things to note

- When passing as a parameter, you can only modify the data; you cannot add to or delete from the original data.
- By default s=s[0:len(s)], which includes the lower bound but excludes the upper bound, mathematically expressed as: [)

### Maps

Basic usage

```go
// Create
m:=make(map[string]int)
// Set a key-value pair
m["hello"]=1
// Delete a key
delete(m,"hello")
// Iterate
for k,v:=range m{
    println(k,v)
}
```

Things to note

- Map keys must be comparable; they cannot be a slice, map, or function.
- Map values always have a default value, which you can operate on directly, e.g. m[age]++ changes the value from 0 to 1.
- Comparing two maps requires iterating to check whether their key-value pairs are the same. Because of the default-value behavior, you need to check both the val and ok values.

### Standard Library

sort

```go
// Sort ints
sort.Ints([]int{})
// Sort strings
sort.Strings([]string{})
// Custom sort
sort.Slice(s,func(i,j int)bool{return s[i]<s[j]})
```

math

```go
// int32 max and min values
math.MaxInt32 // Actual value: 1<<31-1
math.MinInt32 // Actual value: -1<<31
// int64 max and min values (int defaults to int64)
math.MaxInt64
math.MinInt64

```

copy

```go
// To delete a[i], use copy to overwrite position i with values from i+1 to the end, then shrink by 1
copy(a[i:],a[i+1:])
a=a[:len(a)-1]

// If make creates a length, assign by index
a:=make([]int,n)
a[n]=x
// If make has length 0, assign using append()
a:=make([]int,0)
a=append(a,x)
```

### Common Tricks

Type conversion

```go
// byte to number
s="12345"  // s[0] is of type byte
num:=int(s[0]-'0') // 1
str:=string(s[0]) // "1"
b:=byte(num+'0') // '1'
fmt.Printf("%d%s%c\n", num, str, b) // 111

// string to number
num,_:=strconv.Atoi()
str:=strconv.Itoa()

```

## Notes for Solving Problems

- On LeetCode, do not use a global variable as a return value, otherwise the problem checker will report an error.

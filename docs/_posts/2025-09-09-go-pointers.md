---
layout: post
title: "Learn Pointers In Golang | Easy To Understand"
categories: blog programming golang
---

# Understanding Pointers in Go: A Complete Beginner's Guide

This article will demonstrate the fundamentals of using pointers in Go and explain how they work at a conceptual level.

## What Is a Pointer?

A pointer in Go is essentially a special type of variable that holds the memory location (address) of a piece of data stored in RAM. You can think of a pointer as a variable that says: "I'm the address where the actual data is stored. When you use me directly, you'll only get back a memory address."

## Understanding Memory Storage

Before we explore creating and using pointers, let's briefly examine how RAM conceptually stores data. To visualise what RAM memory looks like, consider this example:

```
RAM ---->>> [][][][][][][]
            [][][][][][][]
            [][][][][][][]
            [][][][][][][50] <<<<----- 0x00002345BDa (somepointervar)
```

Data in RAM is stored in address-like locations. Imagine a street with houses, where each house can accommodate residents. RAM operates similarly: each cell in memory can store data, and all your application's data structures use RAM for short-term storage.

Each memory cell has its own unique address, allowing us to navigate to that cell and examine its contents. This is analogous to houses having street addresses that identify them for postal delivery.

## Creating and Using Pointers

### Defining a Standard Variable

To define a standard integer variable in Go:

```go
var num int = 10
```

When you print `num`, you'll see `10` displayed in the terminal because it's a standard variable that doesn't store a memory address. Using `num` retrieves the value of the data stored within it.

### Declaring a Pointer Variable

Here, we set the variable to be of type "pointer to an int value". We use `*` to denote and dereference a pointer:

```go
var memorylocation *int
```

### Getting a Pointer to a Variable

To obtain a pointer to `num` and assign it to `memorylocation`, we use the `&` operator:

```go
memorylocation = &num
```

The `&` operator essentially says: "Take the variable on my right-hand side (`num`) and get its memory address." We then assign that memory address to `memorylocation`.

### Displaying the Memory Address

When we print `memorylocation`, we get the hexadecimal value that points to the address of the `num` variable in memory:

```go
fmt.Println(memorylocation)
```

**Terminal output:**
```
0x00002345BDa
```

## Dereferencing Pointers

What if we want to retrieve the actual value stored at the memory location? We need to **dereference** the pointer using the `*` operator:

```go
var valueofnum int = *memorylocation
```

This syntax essentially instructs: "Navigate to the memory address stored in `memorylocation` and retrieve the actual value from that location in RAM." We then assign that value to `valueofnum`.

Now we can print the value stored at the `0x00002345BDa` RAM address:

```go
fmt.Println(valueofnum)
```

**Terminal output:**
```
10
```

Excellent! We now understand how to create a pointer using `&` and how to dereference it using `*`.

## Modifying Values Through Pointers

For demonstration purposes, if we want to change the value at the memory location, we must use the `*` dereference operator rather than just the variable name (which only holds the memory address):

```go
*memorylocation = 20
```

The actual value at `memorylocation` has now been changed to `20` because we dereferenced the `memorylocation` variable before assigning the new value. Dereferencing essentially means: "Navigate to the memory address of the data stored at `memorylocation` so we can manipulate it."

When we print `num` again, we'll see the updated value:

```go
fmt.Println(num)
```

**Terminal output:**
```
20
```

## Key Takeaways

Pointers in Go provide a powerful mechanism for:
- **Memory efficiency**: Instead of copying large data structures, you can pass their memory addresses
- **Direct manipulation**: Modify values at specific memory locations
- **Shared access**: Multiple variables can reference the same memory location

The concepts demonstrated in this article apply to other data types as well, such as `string`, `struct`, and custom types. The fundamental principles of addressing (`&`), dereferencing (`*`), and memory management remain consistent across all Go data types.

## Complete Working Example

Here's a complete example that demonstrates all the concepts covered:

```go
package main

import "fmt"

func main() {
    // Create a standard variable
    var num int = 10
    fmt.Printf("Original value: %d\n", num)
    
    // Create a pointer to the variable
    var memorylocation *int = &num
    fmt.Printf("Memory address: %p\n", memorylocation)
    
    // Dereference to get the value
    var valueofnum int = *memorylocation
    fmt.Printf("Dereferenced value: %d\n", valueofnum)
    
    // Modify the value through the pointer
    *memorylocation = 20
    fmt.Printf("Modified value: %d\n", num)
}
```

Understanding pointers is fundamental to becoming proficient in Go, as they form the backbone of many advanced concepts including interfaces, slices, and efficient memory management.

For a visual demonstration of these concepts, check out my YouTube video: [Learn Pointers In Golang | Easy To Understand](https://www.youtube.com/watch?v=Z8SIbjncLzc), which complements this article.

Thanks for reading,

Mike.

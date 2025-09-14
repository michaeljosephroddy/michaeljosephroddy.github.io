---
layout: post
title: "Learn Pointers In Golang | Easy To Understand"
categories: blog programming golang
---

Hi, this article will show you the fundamentals of using pointers in Go and how they work.

So a pointer in Go, is essentially a special type of variable that holds the memory location for a piece of data in RAM.
You can imagine a pointer as being a variable that says "hey! I'm the address at which the data in said variable is stored. 
If you use me you will get back only a memory address."

Before we take a look at creating and using pointers, we will briefly touch on RAM and how it conceptually stores data.
To imagine what RAM memory actually looks like, see the example below:

```bash
RAM ---->>>> [][][][][][][]
             [][][][][][][]
             [][][][][][][]
             [][][][][][][50] <<<<------ x00002345BDa somepointervar
```

Data in RAM is stored in address like locations, imagine you have a bunch of houses on a street and each one can house some people.
The same is said for RAM. Each cell in memory can store some data and all of your applications data structures use RAM for short term storage.

Also each cell has its own address, so we can go to that cell and check out its contents, much like the house analagy above, where each
house would have a its own street address to idetnify it for the postman who wants to put some stuff inside.

To demonstrate this, see the example below:

To define a standard int variable in go, we can do the following:
```golang
var num int = 10 
```
If you print `num` you will just get `10` printed to the terminal because its a standard varible and it doesnt store a memory address for the data.
So when you use `num` it will just get the value of the data stored in it, not anyhting else.

Here I've set the variable memory location to be of type "pointer to an int value", we use `*` to denote and also dereference a pointer.
We will use this variable to store the address of `num` from above, see the example below:

```golang
var memorylocation *int 
```
Now to get a pointer of `num` and assign it to memorylocation, we need to use the `&` operator.
The `&` operator is basically saying "take the variable on my righthand side which is `num` and get its memory address!".
And then we assign that memory address to memorylocation. 
```Golang
memorylocation = &num 
```



Now if we print `memorylocation`, that is exactly what we will get! Some hexadecimal value that points to the addess of the `num` variable in memory. Go ahead a try it, you should get a similar ouput.
```golang
fmt.Println(memorylocation)
```

Terminal output
```bash
x00002345BDa
```

Ok so what if we want to get the actual value at the memory location? 
Well then we need to `dereference` the pointer using the `*` operator, see the example below:

This syntax essentially says, "go to the memory address that is stored in memorylocation and pull out the actual value from that location in RAM".
Then we assign that value to `valueofnum`.
```golang
var valueofnum int = *memorylocation
```

So now we can print the value that is stored at `x00002345BDa` RAM address.

Terminal output
```bash
10
```

Great, so now we have an idea of how to create a pointer using `&` and how to dereference it using `*`.

And another thing for demonstration purposes, if we want to change the value at memorylocation, we will need to use the `*` dereference operator and not just the variable name itself, because that only holds the memory address, see below:

```golang
*memorylocation = 20
```

Now the actual value at `memorylocation` has been changed to `20` becuase we dereferenced the `memorylocation` variable before assigning the new vlaue of `20`.
So now when we print `num` we will get `20`.
And as we said beofore, dereferencing is like saying "go to the memory address of the data that is stored at memorylocation so we can manipulate it in some way".

And here is what we get when we print `num` again, the value is changed to `20`. See the terminal output below:

Terminal output
```bash
20
```
Awesome! Hopefully now you have a better understanding of how pointers work in Go and how data is conceptually stoed in RAM. 
The information in this article applies to other data types too like `string` for instance as
the same concepts apply. Please checkout my YouTube video [Learn Pointers In Golang | Easy To Understand](https://www.youtube.com/watch?v=Z8SIbjncLzc) that complements this article for your understanding.

Thanks for reading, Mike.



# Knowledge Base

This document contains the Knowledge Base from `Go Lang`.

**Reference(s):**

> 1. <https://www.markdownguide.org/basic-syntax/>

## Topics

> 1. [What is the difference in `&` operator VS `new(string)` in golang?](#1-what-is-the-difference-in--operator-vs-newstring-in-golang)
> 1. [Default `nil` value for variables](#2-default-nil-value-for-variables)

## 1. What is the difference in `&` operator VS `new(string)` in golang?

In Go, the & operator and the new() function serve different purposes, but they can be used to achieve similar outcomes in certain scenarios.

### 1.1. `&` Operator

The & operator is used to obtain the memory address of a variable.
When used with a variable, it returns a pointer to the variable's memory address.
It is commonly used when you want to pass a variable by reference or when you need to work with pointers directly.

```go
package main

import "fmt"

func main() {
    x := 42
    ptr := &x // Get the memory address of x
    fmt.Println(ptr) // Output: 0xc0000a4000 (example address)
}
```

### 1.2. `new()` Function

The new() function is used to allocate memory for a new value of a specified type and returns a pointer to it.
It initializes the allocated memory with the zero value of the specified type.
It is useful when you need to allocate memory for a new variable, especially for composite types like structs, slices, or maps.

```go
package main

import "fmt"

func main() {
    ptr := new(int) // Allocate memory for a new integer
    fmt.Println(*ptr) // Output: 0 (zero value of int)
}
```

While both & and new() can be used to obtain pointers, & is primarily used to obtain the address of an existing variable, while new() is used to allocate memory for a new variable. The choice between them depends on the specific use case and programming requirements.

The use cases for & operator and new() function in Go differ based on their functionality and intended purpose. Here are the common use cases for each:

### 1.3. Use Cases for `&` Operator

Passing by Reference: When you need to pass a variable to a function and modify its value within the function, you can pass a pointer to the variable using the & operator. This allows the function to directly access and modify the variable's value in memory.

```go
package main

import "fmt"

// Function to increment a value using a pointer
func incrementByReference(x *int) {
    *x++
}

func main() {
    x := 42
    incrementByReference(&x) // Pass a pointer to x
    fmt.Println(x) // Output: 43
}
```

Working with Pointers: When you need to work with pointers directly, such as accessing fields or methods of a struct, or when interfacing with system-level operations that require memory addresses.

### 1.4. Use Cases for `new()` Function

Dynamic Memory Allocation: When you need to allocate memory dynamically for a new variable, especially when the size or type of the variable is determined at runtime.

```go
package main

import "fmt"

func main() {
    ptr := new(int) // Allocate memory for a new integer
    *ptr = 42 // Assign a value to the allocated memory
    fmt.Println(*ptr) // Output: 42
}
```

Allocating Composite Types: When you need to allocate memory for composite types like structs, slices, or maps, new() can be used to initialize a new instance of the type with zero values for its fields or elements.

```go
package main

import "fmt"

type Person struct {
    Name string
    Age  int
}

func main() {
    ptr := new(Person) // Allocate memory for a new Person struct
    fmt.Println(*ptr) // Output: {"" 0} (zero values for Name and Age)
}
```

Overall, the & operator is commonly used for passing variables by reference and working with pointers, while new() is primarily used for dynamic memory allocation and initializing new instances of types. The choice between them depends on the specific requirements of your program and the intended use of pointers and dynamically allocated memory.

## 2. Default `nil` value for variables

In Go, the default nil value for variables depends on the type of the variable. Here are some examples:

### 2.1. Pointers

The default value for pointers is nil. If a pointer variable is declared without being initialized, it will automatically be assigned the nil value, indicating that it does not point to any memory address.

```go
   var ptr *int
   fmt.Println(ptr) // Output: nil
```

### 2.2. Slices, Maps, and Channels

Similarly, slices, maps, and channels are all reference types, and their zero value is also nil. If they are declared without initialization, they will have a nil value.

```go
   var slice []int
   fmt.Println(slice) // Output: nil

   var mp map[string]int
   fmt.Println(mp) // Output: nil

   var ch chan int
   fmt.Println(ch) // Output: nil
```

### 2.3. Interfaces

The zero value of an interface type is also nil. If an interface variable is declared without being initialized, it will have a nil underlying value.

```go
   var intf interface{}
   fmt.Println(intf) // Output: nil
```

### 2.4. Function Pointers

Like regular pointers, function pointers also default to nil if not initialized.

```go
   var fnPtr func(int) int
   fmt.Println(fnPtr) // Output: nil
```

For other types such as numeric types (int, float64, etc.) and boolean types (bool), their default zero values are 0 and false respectively, not nil. It's important to note the distinction between nil and the zero value in Go.

# Knowledge Base

This document contains the Knowledge Base from different sources.

**Reference(s):**

> 1. <https://www.markdownguide.org/basic-syntax/>

## Topics

> 1. [BPE and WordPiece encoding](#bpe-and-wordpiece-encoding)
>    - [Byte Pair Encoding (BPE)](#byte-pair-encoding-bpe)
>    - [WordPiece Encoding](#wordpiece-encoding)
> 1. [What is "node -r ts-node/register"](#what-is-node--r-ts-noderegister)
> 1. [What is the difference in `&` operator VS `new(string)` in golang?](#what-is-the-difference-in--operator-vs-newstring-in-golang)

## BPE and WordPiece encoding

Byte Pair Encoding (BPE) and WordPiece encoding are both subword tokenization techniques commonly used in natural language processing tasks, particularly in neural language models like BERT and GPT.

### Byte Pair Encoding (BPE)

BPE is a data compression technique originally proposed by Sennrich et al. (2016) for machine translation. It has since been widely adopted in NLP tasks.

In BPE, the basic idea is to iteratively merge the most frequent pairs of characters or bytes in a corpus. This merging process gradually builds a vocabulary of subword units, where each unit represents a sequence of characters or bytes.

By breaking words into smaller subword units, BPE can handle unknown words and rare words more effectively, allowing models to generalize better and improve performance on various tasks.

### WordPiece Encoding

WordPiece encoding is a variant of BPE introduced by Google as part of the BERT model.

Similar to BPE, WordPiece also iteratively merges subword units based on frequency, but it differs in that it starts with a predefined vocabulary of whole words.

During training, WordPiece may split words into smaller subword units if doing so improves the model's performance. This flexibility allows WordPiece to handle rare and out-of-vocabulary words effectively while maintaining compatibility with a predefined vocabulary.

Both BPE and WordPiece encoding are widely used in state-of-the-art NLP models and have proven effective in tasks such as machine translation, text classification, and language modeling. The choice between them often depends on factors such as the specific task, dataset characteristics, and model architecture.

### Code Sample

```typescript
async function encodePrompt() {
    const prompt = "How are you today?";
    
    // Encode the prompt
    const encoder = encoding_for_model('gpt-3.5-turbo');
    const words = encoder.encode(prompt);
    console.log("Encode Prompt: ", words);

    // Decode the prompt
    const encoding = get_encoding("cl100k_base");
    const decodedBytes = encoding.decode(words);

    // Convert bytes to string
    const decodedPrompt = Buffer.from(decodedBytes).toString('utf-8');
    console.log("Decoded Prompt: ", decodedPrompt);
}
```

## What is "node -r ts-node/register"?

The command node -r ts-node/register is used to run TypeScript files (*.ts) directly with Node.js using the ts-node runtime compiler. Here's what each part of the command does:

node: This is the command to run JavaScript files with the Node.js runtime.

-r: This flag is used to preload modules before executing the script. In this case, -r is followed by ts-node/register, which tells Node.js to preload the ts-node/register module before running the script.

ts-node/register: This module is provided by ts-node, which is a runtime compiler for TypeScript. When ts-node/register is loaded, it registers the TypeScript compiler (tsc) to compile TypeScript files (*.ts) on-the-fly when they are imported or required by the Node.js application.

So, when you run node -r ts-node/register, Node.js will preload ts-node/register, allowing you to execute TypeScript files directly without having to compile them to JavaScript first. This is convenient during development and testing as it eliminates the need for a separate build step.

## What is the difference in `&` operator VS `new(string)` in golang?

In Go, the & operator and the new() function serve different purposes, but they can be used to achieve similar outcomes in certain scenarios.

### & Operator

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

### new() Function

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

### Use Cases for & Operator

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

### Use Cases for new() Function

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

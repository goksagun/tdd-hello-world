Hello, World!
=============

It is traditional for your first program in a new language to be [Hello, World][1].
- Create a folder wherever you like
- Put a new file in it called hello.go and put the following code inside it

```go
package main

import "fmt"

func main() {
	fmt.Println("Hello, world")
}
```

To run it, type

```shell
go run hello.go
```

---
How it works
------------
When you write a program in Go you will have a `main` package defined with a `main` func inside it. Packages are ways of grouping up related Go code together.
The `func` keyword is how you define a function with a name and a body.
With `import "fmt"` we are importing a package which contains the `Println function that we use to print. 

---


[1]: https://en.m.wikipedia.org/wiki/%22Hello,_World!%22_program
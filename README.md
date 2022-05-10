# Hello, World!

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
## How it works
When you write a program in Go you will have a `main` package defined with a `main` func inside it. Packages are ways of grouping up related Go code together.

The `func` keyword is how you define a function with a name and a body.

With `import "fmt"` we are importing a package which contains the `Println function that we use to print. 

---
## How to test

How do you test this? It is good to separate your "domain" code from the outside world (side-effects). The `fmt.Println` is a side effect (printing to stdout) and the string we send in is our domain.

So let's separate these concerns so it's easier to test

```go
package main

import "fmt"

func Hello() string {
	return "Hello, world"
}

func main() {
	fmt.Println(Hello())
}
```

We have created a new function again with `func` but this time we've added another keyword `string` in the definition. This means this function returns a `string`. 

Now create a new file called hello_test.go where we are going to write a test for our Hello function

```go
package main

import "testing"

func TestHello(t *testing.T) {
	got := Hello()
	want := "Hello, world"

	if got != want {
		t.Errorf("got %q want %q", got, want)
	}
}
```

---
## Go modules?

The next step is to run the tests. Enter `go test`in your terminal. If the tests pass, then you are probably using an earlier version of Go. However, if you are using Go 1.16 or later, then the tests will likely not run at all. Instead, you will see an error message like this in the terminal:

```shell
$ go test
go: cannot find main module, but found .git/config in /path/to/your-project
        to create a module there, run:
        go mod init
```

What's the problem? In a word, [modules][2]. Luckily, the problem is easy to fix. Enter `go mod init hello` in your terminal. That will create a new file with the following contents:

```
module hello

go 1.18
```

This file tells the `go` tools essential information about your code If you planned to distribute your application, you would include where the code was available for download as well as information about dependencies. For now, your module file is minimal, and you can leave it that way. To read more about modules, [you can check out the reference iin the Golang documentation][3]. We can get back to testing and learning Go now since tests should run, even on Go 1.16.

In future chapters you will need to run `go mod init SOMEONE` in each new folder before running commands like `go test` or `go build`.

---
## Back to Testing

To run tests, in your terminal type

```shell
go test
```

It should've passed! Just to check, try deliberately breaking the test by changing the `want` string.

```
PASS
ok      hello   0.107s
```

### Writing tests

Writing a test is just like writing a function, with a few rules

- It needs to be in a file with a name like `xxx_test.go`
- The test function must start with the word `Test`
- The test function takes one argument only `t *testing.T`
- In order to use the `*testing.T` type, you need to `import "testing"`, like we did with `fmt`in the other file

For now, it's enough to know that your `t` of type `*testing.T` is your "hook" into the testing framework so you can do things like `t.Fail()` when you want to fail.

We've covered some new topics:

#### `if`

If statements in Go are very much like other programing languages.

#### Declaring variables

We're declaring some variables with the syntax `varName := value`, which lets us re-use some values in our test for readability.

#### `t.Errorf`

We are calling the `Errorf` method on our `t` which will print out a message and fail the test. The `f` stands for format which allows us to build a string with values inserted into placeholder values `%q` is very useful as it wraps your values in double quotes.

You can read more about the placeholder strings in the [fmt go doc][4].

[1]: https://en.m.wikipedia.org/wiki/%22Hello,_World!%22_program
[2]: https://blog.golang.org/go116-module-changes
[3]: https://golang.org/doc/modules/gomod-ref
[4]: https://golang.org/pkg/fmt/#hdr-Printing
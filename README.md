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

## Hello, YOU

Now that we have a test we can iterate on our software safely.

In the last example we wrote the test *after* the code had been written just so you could get an example of how to write a test and declare a function. From this point on we will be *writing test first*.

Our next requirement is to let us specify the recipient of the greeting.

Let's start by capturing these requirements in a test. This is basic test driven development and allow us to make sure our test is *actually* testing what we want. When you retrospectively write tests there is the risk that your test may continue to pass even if the code doesn't as intended.

```go
package main

import "testing"

func TestHello(t *testing.T) {
	got := Hello("Chris")
	want := "Hello, Chris"

	if got != want {
		t.Errorf("got %q want %q", got, want)
	}
}
```

Now run `go test`, you should have a compilation error

```
# hello [hello.test]
./hello_test.go:6:15: too many arguments in call to Hello
        have (string)
        want ()
FAIL    hello [build failed]
```

When using a statically typed language like Go it is important to *listen to compiler*. The compiler understands how your code should snap together and work, so you don't have to.

In this case the compiler is telling you what you need to do to continue. We have to change our function `Hello` to accept an argument.

Edit the ``Hello`` function to accept an argument of type string

```go
func Hello(name string) string {
	return "Hello, world"
}
```

If you try and run your tests again your ``hello.go`` will fail to compile because you are not passing an argument. Send in "world" to make it compile.

```go
func main() {
	fmt.Println(Hello("world"))
}
```

Now when you run your tests you should see something like

```
--- FAIL: TestHello (0.00s)
    hello_test.go:10: got "Hello, world" want "Hello, Chris"
FAIL
exit status 1
FAIL    hello   0.294s
```

We finally have a compiling program but it is not meeting our requirements according the test.

Let's make the test pass by using the name argument and concatenate it with ``Hello,``

```go
func Hello(name string) string {
	return "Hello, " + name
}
```

When you run the tests they should now pass. Normally as part of the TDD cycle we should now *refactor*.

### A note on source control

At this point, if you are using a source control (which you should!) I would ``commit``the code as it is. We have working software backed by a test.

I *wouldn't* to push master (main) though, because I plan to refactor next. It is nice to commit at this point in case you somehow get into a mess with refactoring - you can always go back to the working version.

There's not a lot to refactor here, we can introduce another language feature, *constants*.

### Constants

Constants are defined like so

```go
const englishHelloPrefix = "Hello, "
```

We can now refactor our code

```go
const englishHelloPrefix = "Hello, "

func Hello(name string) string {
	return englishHelloPrefix + name
}
```

After refactoring, re-run your tests to make sure haven't broken anything.

Constants should improve performance of your application as it saves you creating the ``"Hello, "`` string instance every time ``Hello`  is called.

To be clear, the performance boost is incredibly negligible for this example! But it's wroth thinking about creating constants to capture the meaning of values and sometimes to aid performance. 

[1]: https://en.m.wikipedia.org/wiki/%22Hello,_World!%22_program
[2]: https://blog.golang.org/go116-module-changes
[3]: https://golang.org/doc/modules/gomod-ref
[4]: https://golang.org/pkg/fmt/#hdr-Printing
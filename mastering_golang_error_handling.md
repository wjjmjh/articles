## Mastering Golang Error Handling: A Journey from Frustration to Elegance

### Introduction

As programmers, we've all encountered the dreaded `if err != nil` statement that seems to overflow everywhere in our
codebase, leading to spaghetti-like structures and countless programmer complaints :-(. Error handling has
its own set of challenges, including the potential to ignore errors generated in functional processes or business
logic (many developers deliberately doing so)
and the uncertainty of what actions should be taken when an error occurs. In this article, we will explore the
philosophy behind error handling, the importance of error handling in programming, and how Go provides elegant solutions
to these challenges.

### The Philosophy of Error Handling

Error handling serves several essential purposes in programming, and understanding its philosophy is crucial for writing
clean, robust, and maintainable code. Let's delve into three fundamental principles of error handling.

- Clear Signal:
  Error handling should provide a clear signal to the developer that something has gone wrong and what has gone error.
  By checking for errors explicitly, we ensure that we are aware of potential issues and can take appropriate action.
- Separation of logic:
  Error handling logic should be separated from the normal flow of the code. This separation allows us to focus on the
  primary logic while handling errors separately, resulting in cleaner and more readable code.
- Proper Logging and Resource Deallocation:
  Logging errors in a meaningful way provides valuable information for debugging and troubleshooting. Additionally, when
  errors occur, it is crucial to consider deallocate any acquired resources properly to prevent resource leaks and
  maintain the
  stability of the system.

### The "Try Catch Finally" Block Inspirations

One of the most common error handling constructs in other programming languages is the `try catch finally` block (surely
you know, my Java friends). This construct provides a clear and concise syntax for handling errors.
Let's explore its benefits and how it can be leveraged in Go.
1, Clear Syntax: The "try catch finally" block provides a well-defined syntax for handling errors, making the code more
readable and easier to understand.
2, Separation of Logic: By separating the normal logic from the error handling logic, developers can focus on the core
functionality without the distractions of error handling cluttering their code.
3, Error Awareness: With the `try catch finally` block, errors cannot be ignored. Developers are forced to think about
how to handle errors explicitly, promoting more robust error handling practices.
4, Function Chaining: The `try catch finally` block allows developers to chain multiple functions together, enhancing
code readability and reducing redundancy. e.g.

```
Car car = CarBuilder().BuildWheels(wheels).BuildEngine(engine)...;
```

### Go's Approach to Error Handling

Go takes a unique approach to error handling, treating errors as values rather than exceptions. This design decision
aligns with Go's overall philosophy of simplicity and readability. Let's explore some of the key aspects of Go's error
handling approach and how they contribute to writing clean and efficient code.

- Error is Value: This is the mindset you need to have as a programmer. In Go, errors are values, just like
  any other input parameter. This perspective encourages programmers to handle errors explicitly and integrates error
  handling seamlessly into the programming flow. You should treat errors just as valuable as any other data.
- Simplicity: Go promotes simplicity and conciseness in its design. If you find yourself writing verbose or tedious
  error handling code, chances are you might be approaching it incorrectly (Hello World newbie!). Embrace the simplicity
  of Go's error handling mechanism.
- Multiple Returns: Go's support for multiple return values enables clear separation between the expected result and the
  potential error. This separation enhances code readability and makes error handling more straightforward.
- Error Interface: Go's error interface allows for flexible error handling approaches. Developers can handle errors
  based on their specific types, enabling targeted error handling logic. e.g.

 ```
if err != nil {
  switch err.(type) {
    case *ThisError:
      ...
    case *ThatError:
      ...
    case *MyLifeSucksError:
      ...
    default:
      ...
  }
}
```

- Defer for Resource Deallocation: Go's defer statement provides a convenient way to ensure that resources are properly
  deallocated, even in the presence of errors. It enhances code clarity and reduces the risk of resource leaks. e.g.

```
func ConnectDatabase() error {
    db, err := sql.Open("postgres", "my_connection_string")
    if err != nil {
        return err
    }
    defer db.Close() // closes the database connection on function exit
}

```

### Refining Error Handling in Go

Now, let's dive into some practical examples of how you can refine your error handling code in Go to achieve cleaner and
more maintainable code.

#### Functional Programming:

```
func parse(r io.Reader) (*Point, error) {
    var p Point
    var err error
    
    read := func(data interface{}) {
        if err != nil {
            return
        }
        err = binary.Read(r, binary.BigEndian, data)
    }
    
    read(&p.attr1)
    read(&p.attr2)
    read(&p.attr3)
    read(&p.attr4)
    read(&p.attr5)
    
    if err != nil {
        return nil, err
    }
    
    return &p, nil
}
```

We encapsulated the error handling into `read` function, and any read errors occurred previously, the rest won't read,
due to

```
if err != nil {
   return
}
```

By employing functional programming techniques, we can achieve cleaner code that is easier to reason about. The read
function encapsulates the repetitive error-checking logic, reducing redundancy and improving code readability. But this
is not clean enough.

#### Package Structuring:

If errors are values, why not it can be a part of struct?

e.g.

```
type Reader struct {
    r   io.Reader
    err error
}

func (r *Reader) read(data interface{}) {
    if r.err == nil {
        r.err = binary.Read(r.r, binary.BigEndian, data)
    }
}

func parse(input io.Reader) (*Point, error) {
    var p Point
    r := Reader{r: input}

    r.read(&p.attr1)
    r.read(&p.attr2)
    r.read(&p.attr3)
    r.read(&p.attr4)
    r.read(&p.attr5)

    if r.err != nil {
        return nil, r.err
    }

    return &p, nil
}

```

Again, if any read errors occurred previously, the rest won't read, as `func read` checks if error already occurred
before next read.

By structuring our code with packages and encapsulating related functionality, we can achieve better organization and
separation of concerns. The Reader struct and its associated methods provide a cleaner interface for reading data,
reducing code duplication and improving code maintainability :).

#### Fluent Interface:

After `Package Structuring` I gotta show you something cooler:

```

import (
	"errors"
	"fmt"
)

type ShoppingCart struct {
	Items []string
	err   error
}

func NewShoppingCart() *ShoppingCart {
	return &ShoppingCart{}
}

func (sc *ShoppingCart) AddItem(item string) *ShoppingCart {
	if sc.err == nil {
		sc.Items = append(sc.Items, item)
	}
	return sc
}

func (sc *ShoppingCart) RemoveItem(item string) *ShoppingCart {
	if sc.err == nil {
		index := sc.findIndex(item)
		if index == -1 {
			sc.err = errors.New("item not found in the cart")
		} else {
			sc.Items = append(sc.Items[:index], sc.Items[index+1:]...)
		}
	}
	return sc
}

func (sc *ShoppingCart) Clear() *ShoppingCart {
	if sc.err == nil {
		sc.Items = nil
	}
	return sc
}

func (sc *ShoppingCart) findIndex(item string) int {
	for i, cartItem := range sc.Items {
		if cartItem == item {
			return i
		}
	}
	return -1
}

func (sc *ShoppingCart) Print() *ShoppingCart {
	if sc.err == nil {
		fmt.Println("Shopping Cart:")
		for _, item := range sc.Items {
			fmt.Println("-", item)
		}
	}
	return sc
}

func main() {
	cart := NewShoppingCart()
	cart.AddItem("Shirt").AddItem("Pants").AddItem("Shoes").RemoveItem("Pants").Print()

	if cart.err != nil {
		fmt.Println("Error:", cart.err)
	}
}
```

Using a fluent interface approach, we can chain method calls together, resulting in more expressive and readable code.
This technique improves code clarity and reduces the need for repetitive error checks.

#### Wrapping Errors and Identifying Causes:

It's racky if you directly return the raw error to upper level, won't it be nicer if you add bit context?

```
if err != nil {
    return fmt.Errorf("something went wrong: %v", err)
}

type authorizationError struct {
    operation string
    err       error // original error
}

func (e *authorizationError) Error() string {
    return fmt.Sprintf("authorization failed when %s: %v", e.operation, e.err)
}
```

To provide better context and information about errors, Go allows us to wrap errors using the `fmt.Errorf` function.
This
wrapping technique helps create more informative error messages. Additionally, by defining custom error types, such as
the authorizationError struct, we can add additional context to the error, making it easier to understand and handle
specific error scenarios.

Furthermore, the `github.com/pkg/errors` package can be utilized to wrap errors and retrieve the original error using
the
`errors.Cause(err)` function. This capability enables us to identify the specific type of error and handle it
accordingly,
improving error handling precision. e.g.

```
import "github.com/pkg/errors"

// wrap errors
if err != nil {
    return errors.Wrap(err, "my life sucks")
}

// Cause interface
switch err := errors.Cause(err).(type) {
case *MyLifeSucksError:
    // handle my life sucks
default:
    // unknown error
}
```

## Conclusion:

In this article, we explored the philosophy behind error handling and why it is crucial in programming. We discussed the
challenges faced in error handling and how Go provides elegant solutions to these challenges. By embracing Go's error
handling approach, we can write cleaner, more maintainable code. Techniques such as functional programming, package
structuring, fluent interfaces, and error wrapping contribute to code clarity and efficiency.

Remember, Again, error handling is not just about handling exceptions; it is an integral part of building robust and
reliable software systems. With Go's error handling mechanisms and best practices, you can transform your error-prone
code into technical art. Happy error handling!

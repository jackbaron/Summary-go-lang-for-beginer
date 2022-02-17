1. Variable
	- Declaration
		- var foo int
		- var foot int = 42
		- foo := 42
	- Can't  redeclare variables, but can shadow them
	- All variables must be used
	- Visibility
		- lower case first letter for package scope
		- upper case first letter to export
		- no  private scope
	- Naming conventions
		- Pascal or camelCase
			- Capitalize acronyms (HTTP, URL)
		- As short as reasonable
			- longer names for longer live
		- Type conversions
			- destination Type (variable)
			- use strconv package for strings
2. Primitives
	- Boolean type
		- Values are true or false
		- Not an alias for other types (eg. int)
		- Zero value is false
	- Numeric type
		- Integers
			- Signed integers
				-  int type has varying size, but min 32 bits
				- 8 bit (int8) through 64 bit (int64)
			- Unsigned integers
				- 8 bit (byte and unit8) through 32 bit(unit32)
3.  Constants
	- Immutable, but can be shadowed
4.  Array
	- Collection of items with same type
	- Fixed size
	- Declarations styles
		- a := [3]int{1, 2, 3}
		- a := [..]int{1, 2, 3}
		- var a [3]int
	- Access via zero-based index
		- a :=  3[int] {1, ,2 ,3} // a[1] == 3
	- len function returns size of array
	- Copies refer to different underlying data
		- Example:
```
        a := [3]int{1, 2, 3} 
	b := a 
	b[0] = 0 
	fmt.Println(a)  // 1, 2, 3  
	fmt.Println(b) // 0, 2, 3
```
5. Slices
	- Backed by array
	- Creation styles
		- Slice existing array or slice
		- Literal style //var s = []int{3, 5, 7, 9, 11, 13, 17}
		- Via make function
			- a := make([]int, 10) // create slice with capacity and length = 10
			- a := make([]int, 10, 100) // slice with capacity  = 100 and length = 10
				- // The capacity of a slice is the number of elements in the underlying array, counting from the first element in the slice. https://go.dev/tour/moretypes/11#:~:text=The%20capacity%20of%20a%20slice,provided%20it%20has%20sufficient%20capacity.
		- len function returns length of slice
		- cap function returns length of underlying array
			- May cause expensive copy operation if underlying array is too small
		- Copies refer to same underlying array
			- Example:
```
        a := [3]int{1, 2, 3} 
	b := a[0:2] 
	b[0] = 0 
	fmt.Println(a)  // 0, 2, 3
	fmt.Println(b) // 0, 2
```
6. Maps
	- Collections of value types that are accessed via keys
	- Created via literals or via make function
	- Members accessed via [key] syntax
		- myMap["key"] = "value"
	- Check for presence with "value, ok" form of result
		- Example
```
        a := map[string]int{ 
		"one": 1, 
		"two": 2, 
    	} 
    	_, ok := a["one"] 
    	fmt.Println(ok)  // true
    	_, ok1 := a["one1"] 
    	fmt.Println(ok1) // false
```
	- Multiple assignments refer to same underlying data
```
        a := map[string]int{ 
		"one": 1, 
		"two": 2, 
	} 
	b := a 
	b["one"] = 0 
	fmt.Println(a)  // map[one:0 two:2]
	fmt.Println(b) // map[one:0 two:2]
```
7. Structs
	- Collections of disparate data types that describe a single concept
	- Keyed by named fields
	- Normally created as types, but anonymous structs are allowed
		- An anonymous struct in Go is just like a normal struct, but it is defined without a name and therefore cannot be referenced anywhere else in your codebase. Anonymous structs are super useful if you only need to use them once.
		- Declare anonymous struct
```
newCar := struct { 
    make    string 
    model   string 
    mileage int 
}{ 
    make:    "Ford", 
    model:   "Taurus", 
    mileage: 200000, 
}
```
		- Structs are value types
		- No inheritance, but can use composition  via embedding
```
// declaring a struct  
type Comic struct{ 
    Universe string 
}
// declaring a struct 
type Marvel struct{ 
    // the struct is embedded 
    Comic 
}
```
		- Tags can be added to struct fields to describe  field
```
type User struct { 
	Name          string    `json:"name"` 
	Password      string    `json:"password"` 
	PreferredFish []string  `json:"preferredFish"` 
	CreatedAt     time.Time `json:"createdAt"` 
}
```
8. Defer
	- Used to delay execution of a statement until  function exits
	- Useful to group "open" and "close" functions  together
		- Be careful in loops
	- Run in LIFO (last-in, first-out) order
```
for i := 0; i < 4; i++ { 
    defer fmt.Print(i) 
}
// 3
// 2
// 1
// 0 
```
	-  Arguments evaluated at time defer is executed, not at time of called  function execution
9. Panic
	- Occur when program cannot continue at all
		- Don't use when file can't be opened, unless it is critical
		- Use for unrecoverable events - cannot obtain TCP port for web server
	- Function will stop executing
		- Deferred functions will still fire
	- If nothing handles panic, program will exit
10. Recover
	- Used to recover from panics
	- Only useful in deferred functions
	- Current function will not attempt to continue, but higher functions in call stack will continue as if nothing went wrong
11. Pointer
	- Pointer types use an asterisk (*) as a prefix to type pointed to be a pointer to that underlying data type.
		- *int - a pointer to an integer
	- Use the address of operator (&) to get address of variable
	- Dereferencing pointers
		- Dereferencing a pointer by preceding with an asterisk (*)
		- Complex types (e.g structs) are automatically dereferenced
	- Create pointer to objects
		- Can use the address of operator (&) if value  already exist
			- ms := myStruct{foo : 42}
			- p : = &ms
		- Use address of operator before initializer
			- ms := &myStruct{foo : 42}
		- Use the new keyword
			- Can't initialize fields at the same time
	- Types with internal pointers
		- All assignment operations in Go are copy operations
		- Slices and maps contain internal pointers, so copies point to same  underlying data
12.  Functions
	- Basic syntax
		- func foo() {.....}
	- Paramerters
		- Comma delimited list of variables and types
			- func foo(bar string, baz int)
		- Parameters of same type list type once
			- func foo(bar, baz int)
		- When pointers are passed in, the function can change the value in the caller
			- This is always true for data of slice and maps
		- Use variadic paramerters to send list of same type in
			- Must be last parameter
			- Received as a slice
			- func foo (bar string, baz ...int) // foo("bar is string", 1,2,3,4,5) -> baz -> [1,2,3,4,5]
	- Return values
		- Single return value just list type
			- func foo() int 
		- Multiple return value list types surrounded by parentheses
			- func foo() (int, error)
			- The (result type, error) paradigm is a very common idiom
		- Can use named return values
			- Initializes returned variable
			- Return using return keyword on its own
		- Can return addresses of local variables
			- Automatically promoted from local memory (stack) to shared memory (heap)
13.  Interface
	- Use many, small interface
		- Single method interface are some of the most powerful and flexible
			- io.Writer, io.Reader, interface{}
	- Don't export interfaces for types that will be  consumed
	- Do export interfaces for types that will be used by package
	- Design functions and methods to receive interfaces whenever possible
	- Basic
	- Composing interfaces


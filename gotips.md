#### Go tips to remember

1. To copy a slice, use builtin func `copy(a,b)` which doea a deep copy of b into a. If you onlu assign `b` to `a`, only the direct part of `b` will be copied,
meaning that any other variable that references the old `a` will remain unchanged because a shallow copy of `b` into them during assignment would have separated them from 
`b`. 
2. To create a composite literal, write the type signature, followed by curly braces.
  e.g.:
  - struct: 
      ```
      s := struct {
        field_1 string
        field_2 string
      } {
        "firstname",
        "lastname",
      }
      //s is an unnamed struct
      ```
   - a slice of structs:
      ```
      type structure struct {
        a string
        b string
      }
      
      slice := []structure {
        structure{
          "all", "one",
        }, 
        structure{
          "me", "you",
        },
      }
      ```

   - a map of structs:
        ```
        m := map[string]structure {
          "one": structure{
            "first",
            "second",
          },
          "two": structure{},
        }
        ```
3. swapping: to swap:
```
  a := 1
  b := 2
  a,b = b,a
```  
  this works because the values on th right are first stored in a temporary variable before being moved into the values on the right.
  remember that go always does a shallow copy in assignments and function arguments

4. Comparing structs: some types are not comparable: (e.g. `maps`, `slices`, and `func`s). Once structs do not contain these fields, they are simply comparable with `==`. pointers are comparable. `interface`s, if their underlying types do not contain any of (map, slice, function), can also be compared with `==`.
But structs and interfaces can be compared with `reflect.DeepEqual` even if they contain slices and maps, but not functions.

5. A method defined on a value works for both value and pointer,, while a method defined on a pointer works only for pointers. This is easy to understand.
Think about it. If you define a method for a value, the value itself is left unchanged at the end of the method, it therefore does not matter whether it is a value or a pointer. But if a method is defined for a pointer, its likely because you want to change the internal state of the object. to allow such operation on a value that isn't a pointer should be illegal. 

6. In go, strings are non-modifiable. Look at this:
  ```
  s := "abc"
  s += "def" //this would not modify s, it would rather reallocate s every time, and reassign a concatenation of a  temp-stored value of the old s and the new       //value to it. It is abetter to use a byte slice in these places.
  ```
7. To run through a map in a particular order, store the keys in a slice, and use `sort.Slice` to sort it. You can then run through the keys.
  e.g.
  ```
      m := map[string]int{
        "one":   1,
        "two":   2,
        "three": 3,
        "four":  4,
      }

      keys := []string{}

      for k, _ := range m {
        keys = append(keys, k)
      }

      sort.Strings(keys)

      for _, key := range keys {
        fmt.Println(m[key])
      }
  ```
  8. Between `var a []int` and `a := []int{}`, thich is better? The first one, because though both could be filled up anf become useful using `append`, or assigned a literal slice value, the first one being just a declaration will not be allocated if the slice is never used.

9. An empty struct has size zero.
    ```
    a := struct{}{}
    println(unsafe.Sizeof(a))
    ```
10. A `seen` map is a useful hack needed in go when running through a tree or graph of objects to keep trcak of what you've already seen'
    e.g.
    ```
    seen := make(map[string]struct{}{}) //empth struct literal struct{}{} is used because of its zero value. we don't need its value
    _, exists := seen[key] // exists tells us if the map has this entry
    ```
11. To ensure globally that a type implements an interface, create a package-level blank variable assigning a (non-defined) literal of that type to value of that  interface. e.g. 
    ```
    var _ UserServiceInterface = (*UserServiceStruct)(nil) //checks if the pointer to the struct UserServiceStruct implements UserServiceInterface. This is           //possible because nil is value assignable to a pointer
    //if OTOH, the value is to be checked against a struct value, and not its poinnter, then use an empth struct:
    var _ UserServiceInterface = UserServiceStruct{};
    ```
    
  

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

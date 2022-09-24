## Static Dispatch Vs Dynamic Dispatch

#### What exactly is dispatch?
It simply refers to how methods are called. Types exists in programming. Values (which have types) are what functions or objects act upon. Functions read values. 
They mutate values. hey do everyting with values. If a method is attached to a type, assembly does not understand that. All it knows is basic arithmetic and 
logicl opps and whatnot. Primitive types and functions are much more closer to how assembly works. So, complex types like structs and classes (called composite 
types i hink) are just these primitive types laid out in memory. Methods therefore are just procedures which have value receivers in their definition. That is 
at last hpw i understand it. 

> Dispatch determines which function body gets executed when i call a method

> Static dispatch days i know which function body to execute at compile time. Dynamic dispatch (or late binding) says i don't know until runtime

In Rust's case there are two: static and dynamic

Static dispatch in rust: if the method is non-generic, its easy. The receiver is that value that calles it. The method is defined in its `impl`. The compiler knows
the function body to call. The receiver at the call site of course. If the method is generic, Rust does what is called monomorphization. It creates as many 
different concrete implementations of the function as needed based on the different concrete types that call it. In such a case, the penalty is code bloat.
But performance is not affected.

Dynamic Dispatch in rust: Things are different here. And for you to use dynamic dispatch in rust you must ask for it. Because theres penalty for it. How does it 
work? It's implemented using trait objects. A trait object is a fat pointer. Its not exactly the concrete type you have at the call site. Instead its two things.
It's a pointer to that value and also a pointer to the v-table that contains mappings. 
I'll explain. 
So, you wich to have dynamic dispatch to enjoy a better taste of polymorphism. Lets say you have a function `do_someting` that has formal parameter `Box<dyn Debug>`.
The Debug trait must be implemented by any value that will be supplied as an argument to our `do_something(with: Box<dyn Debug>)`. That value is the `dyn Debug`,
we still have to wrap it in a Box before using i in the function.  When, in the function, we wish to call the debug associated  method on the `with` value, what happens? 
This is dynamic dispatch. Originally, the value supplied becomes a fat pointer containing two pointers. In x86_64, that means it has size 16, meaning two pointers.
The first is a pointer to the value itself. The second is a pointer to a v-table matching the associated methods of the debug trait to where in memory the
values implementations are found. Seems cool, right? The indirection is the problem. Its less efficient than static dispatch, even with monomorphization in generic methods.

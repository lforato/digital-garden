---
title: Structs in Rust
publish: "true"
---
## Structs in Rust

Defining Structs is pretty easy, 
```rust
struct Car {
	wheel_count: i32,
	is_red: bool
}
```

Implementing Methods
impl Car {     pub fn move(&self, ...) {         // implementation     } }
Method Types
•	Instance Methods:
•	First argument is always self
•	Called on an instance of the struct
•	Static Methods:
•	No self argument
•	Called using namespace syntax (e.g., Car::new())
•	Part of the struct's namespace
Key Points
•	Use struct keyword to define
•	Use impl block to add methods
•	self is the first argument for instance methods
•	Without self, method behaves like a static method
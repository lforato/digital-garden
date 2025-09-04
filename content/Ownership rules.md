---
title: Ownership rules
publish: "true"
---
Understanding the differences between stack and heap memory, and how to define and use structs in Rust are crucial for effective memory management and object-oriented programming.

## Stack vs Heap

![[Pasted image 20250903221610.png]]

---
### Stack Characteristics

You can think of a stack as a **stack of plates** where the plates, if you want to remove a plate from the stack, you first remove the plate at the top, which is also the last plate that was included to the stack, that is known as **LIFO** (Last-in, First-out).

Here are some characteristics of the stack:

- Much more organized.
- Is much faster for writing and reading data since the data location is predictable at all times.

Limitations of the stack:

•	Much smaller in size than the heap.
•	Cannot grow or shrink dynamically because of how the stack works.

---
### Heap Characteristics

Image the heap as a big group of boxes that are placed next to each other.

Also imagine that the boxes are managed by a keeper, who is responsible for pointing where you are going to store your data because he is the only one who knows which boxes are not filled by other data.

Once your data is stored, the keeper will give you the key to access that data, which we call a **pointer**.

You can already see from my analogy that the **heap** is much less organized and difficult to use, but it is essential for data that the compiler cannot define the size at compile time.

Here are some characteristics of the heap:

- Much bigger memory space.
- Requires manual memory management.
- Enables us to work with data that grows and shrinks.
- Size of data does not need to be known at compile time.

---
### Ownership Rules in Rust

#### 1. **Every value has an owner**

```rust
fn main() {
	let s = String::from("hello"); // `s` owns the String value
	println!("{}", s);
}
```

Here, the variable `s` is the **owner** of the `String`. When `s` goes out of scope, Rust automatically drops the value.

#### 2. **There can be only one owner**

```rust
fn main() {
	let s1 = String::from("hello");
	let s2 = s1; // ownership moves from `s1` to `s2`
	// println!("{}", s1); // ❌ Error: `s1` no longer owns the value
	println!("{}", s2); // ✅ Works
}
```

Ownership of the `String` moved from `s1` to `s2`. After the move, `s1` is invalid.

#### 3. **There can be only one mutable borrow to a value**

```rust
fn main() {
	let mut s = String::from("hello");
	let r1 = &mut s; // first mutable borrow
	// let r2 = &mut s; // ❌ Error: cannot borrow `s` as mutable more than once
	r1.push_str(", world!");
	println!("{}", r1);
}
```

Rust prevents having multiple mutable references at the same time, avoiding data races.

#### 4. **There can be unlimited immutable borrows to a value**

```rust
fn main () {
	let s = String::from("hello");
	let r1 = &s;
	let r2 = &s;     
	let r3 = &s;
	// All immutable borrows can coexist
	println!("{}, {}, {}", r1, r2, r3);
}
```

You can have as many immutable borrows as you want, as long as there are no mutable ones at the same time.
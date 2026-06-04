# Memory Management

This document summarizes what I have learned about memory management.

## What Is a Variable?

Let's start with the relationship between variables and memory.

Even before studying memory management, I roughly understood that a variable is a label that refers to data stored in memory.

```rust
let x = 10;
```

You can imagine it like this:

```text
Data (10) at memory address 1000 -> x
```

The location in memory is called an **address**.

A value that stores an address is called a **pointer**.

---

## Stack and Heap

Values stored in variables are placed somewhere in memory.

In Rust, memory is primarily divided into two areas:

* Stack
* Heap

### Stack

The stack mainly stores small values whose size is known at compile time.

The stack has less capacity than the heap, but it can be accessed much faster.

However, the important point is that the size is fixed. It is not limited to only small values.

For example:

```rust
let big = [0u8; 100000];
```

Even though a single `u8` is small, a large fixed-size array can still be placed on the stack.

#### Stack Layout Example

```rust
let x = 5;
let y = 10;
```

The stack may look like this:

```text
┌───────┐
│y = 10 │
├───────┤
│x = 5  │
└───────┘
```

Values are pushed onto the stack in order.

When they are no longer needed, they are removed from the top in reverse order.

This behavior is called **LIFO**:

```text
Last In, First Out
```

#### Why Is the Stack Fast?

The stack is fast because memory can be allocated and deallocated simply by moving the stack pointer.

No complex memory search is required.

---

## Heap

The heap is a memory area that can grow dynamically.

It is mainly used for data types such as `String` and `Vec`, whose size can change at runtime.

The heap generally has more available space than the stack, but accessing it is slower.

#### Heap Layout Example

Not all of a variable's data is stored directly on the heap.

For types such as `String` and `Vec`, the actual data is stored on the heap, while information about that data is stored on the stack.

For example:

```rust
let x = String::from("Hello World");

println!("{}", x);
```

The memory layout can be imagined like this:

```text
[Stack]
┌─────────────────────────┐
│Pointer to heap data     │
│Current length           │
│Allocated capacity       │
└─────────────────────────┘

[Heap]
┌───────────────────┐
│H e l l o W o r l d│
└───────────────────┘
```

When:

```rust
println!("{}", x);
```

is executed, the program first reads the information stored on the stack, then follows the pointer to the heap and accesses the actual string data.

---

## How Is Memory Allocated?

When a program starts, the operating system provides memory to it.

A simplified memory layout looks like this:

```text
High Address
┌───────────────┐
│Stack          │ (grows downward)
├───────────────┤
│               │
│   Free Space  │
│               │
├───────────────┤
│Heap           │ (grows upward)
├───────────────┤
│Global Data    │
├───────────────┤
│Code Segment   │
└───────────────┘
Low Address
```

---

## Stack Frames

Earlier, we said that fixed-size values are mainly stored on the stack.

These values are stored inside units called **stack frames**.

A stack frame is a small region of the stack created for each function call.

```rust
fn main() {
    let x = 10;
    let y = 10;
}

fn add() {
    let x = 10;
    let y = 10;
}
```

In this example, a stack frame is created for `main`, and another stack frame is created for `add`.

Each frame contains its own local variables such as `x` and `y`.

---

## Heap Memory Allocation

Because the heap is dynamic, it can request additional memory when more space is needed.

If memory allocation fails, the program may return an error or terminate.

In Rust's standard environment, allocation failure often results in program termination.

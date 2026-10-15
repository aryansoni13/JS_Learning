# JavaScript Basics — Lesson 1

## JavaScript and Its Runtime

React and Next JS are the framework for the JS. They compile into HTML, CSS and JS in the end.

If someone asked what languages can be interpreted by web browsers?

- HTML, CSS, JS and Web Assembly

### Properties of JavaScript

- JIT in time
- **Interpreted** — Means the language is executed from line to line.
- **Dynamically typed** — In JS the variables are not bound to specific datatypes. Types are determined at runtime and can be changed at the time of execution.

```js
var a = 1
a = "Aryan"
a = True
console.log(a)
```

- **Single threaded** — executes code in single threaded environment (one task at a time but internally it can interlink between tasks (event loop?) with the V8)

**Single threaded** means the execution environment has mainly one call stack and one memory heap. It executes instructions sequentially ensuring strict predictability and eliminating classic concurrency bugs like race conditions and deadlocks.

### Scaling a Single-Threaded App

Since we can't do it vertically by adding more cores to it, instead we use horizontal approach for the same where we add up more instances using a process manager like PM2 or tools like Node's cluster module. Then we put a load balancer in front of them to spread the incoming traffic evenly among all the clones.

### How a Single Thread Can Be Blocked

Let's imagine we have website that triggers how a massive, complex loop like processing huge array of 10M users.

Until that loop finishes running the entire UI freezes. If the user tries to click another button or do something else then the browser won't respond. bcz single thread is busy doing the heavy maths.

To fix it, you either have to move heavy calculation into separate background worker thread or break it into smaller asynchronous chunks.

### Difference Between Asynchronous and Multithreading

Multithreading means you hire more workers to do multiple tasks at the same time. It's literally parallelism. If three heavy maths, then three threads will be solved simultaneously.

Asynchronous means one worker can start a task, and instead of sitting doing nothing while waiting for it to finish, when that task is finally done, the worker comes back to finish it up.

## Memory Management

### Garbage Collector

Automatically manages memory allocation and deallocation through garbage collection to prevent memory leaks by automatically reclaiming memory used by objects & no longer referenced.

### Memory Management — 3 Ways

- **Garbage collector**
  - [unclear] maintains a master list of [unclear] using your program is correctly interacting with at any given second.
- **Manual management**
  - We allocate and deallocate memory ourselves.
- **Normal management**
  - We allocate and deallocate memory ourselves.
- **Swift way**
  - System enforces structure: every piece has exactly one owner.

### Memory Heap

Large unstructured region of memory.

We use heap to store large complex pieces of data that don't have a fixed size like objects, arrays and functions.

Allocates a random memory heap location → dumps the text blueprint of your function → [unclear]

Because the space is random creates a specific memory address → creates a variable named greet that holds the tracking number.

### Call Stack

Fast, highly organized, vertical stack of memory.

## JavaScript Syntax

### Variables

```js
let name = "John"  // variables can be reassigned
const age = 30;    // can't change the value
var isStudent = true;
```

var is function scope not block scope don't use much.

### Data Types

- Number — `let number = 1`
- String — `let string = "Hello World"`
- Boolean — `let isActive = True`
- Array & let aggregate data together?

### Operators

- Arithmetic — Add subtract
- Comparison — Compare
- Logical — True and false `&&`

### Difference Between `==` and `===`

`==` converts types before comparing while `===` compares both the value and the datatype directly without converting anything.

## Functions

Function defines that is supposed to do something.

Reusable block of code designed to perform a specific task.

```js
function greet(name) {
    return "Hello, " + name;
}
```

```js
let message = greet("John")
```

### How This Function Works

**Memory phase**

Engine creates a small container in the storage box (Memory Heap). It saves the create instruction of your function.

This memory stack knows how to greet now.

**Call stack**

This step creates a temporary call for it greet and slip it into call stack.

**Processing phase**

Engine reads the instructions: `return "Hello " + name;`

**Final step**

Throw the final product out of the room and close it down.

## Conditionals in JavaScript

### If Statement

Runs a code only if the specified condition is true.

```js
if (score > 85) {
    console.log("Great Job!")
}
```

### Else Statement

Follows an if statement and runs a fallback block of code if the main condition is false.

### Else If Statement

Allows you to test multiple conditions in a sequence if the first if statement fails.

### Switch

Compares a single value against multiple possible case matches.

Clear alternative way to writing many if blocks for the same variable.

### Ternary Operator

Shortened one line version of an if-else statement. It uses a question mark `?` and colon `:`.

```js
let isRaining = true;
let message = isRaining ? "Take an umbrella" : "Enjoy the sun";
```

## Loops

```js
for (let i = 0; i < 5; i++) {
    console.log(i);  // Output 0 to 5
}
```

```js
let j = 0
while (j < 5) {
    console.log(j);  // output 0 to 5
    j++;
}
```

### How Loops Work Under the Hood

At hardware level (CPU and assembler) don't understand the concept of loop. JS compiles loops into the raw bytecode using labels and conditionals jumps.

**Example**

```text
[Memory address: 0x0] -> START LABEL:
[Memory Address: 0x02] IF (condition is FALSE) -> JUMP to end label
[Memory Address: 0x03] [--- Run loop body ---]
[Memory Address: 0x04] [--- Run Increment/Update ---]
[Memory Address: 0x05] Jump to start label
[Memory Address: 0x06] -> END LABEL
```

CPU moves sequentially through memory. When it hits bottom of the loop body, a physical instruction pointer forces jumps backward in memory to evaluate the start label.

JS is just in time compiled. Loops trigger the engine's optimization pipeline because they represent repetitive logic.

### Interpretation (Ignition)

The engine runs the loop line by line initially using an interpreter.

It monitors variables and collects "Type Feedback".

### Profiling

A background thread counts how many times the loop runs. If it passes a threshold, the engine marks it as "Hot Code" (thousands of operations).

### Optimization

The hot loop is handed to optimizing compiler — TurboFan.

It strips away JS's dynamic type-checking overhead and generates highly optimized machine code that executes directly on the CPU silicon.

### Lexical Scope and Memory Allocation in Loops

How loop interacts with memory allocation depends entirely on how variables are scoped.

**The `var` loop (Function Scope)**

Engine creates exactly one single variable slot in the parent execution context.

Every single iteration mutates that exact same memory address.

**The `let`/`const` loop (Block Scope)**

Engine spins up scope block for every single iteration of the loop.

Under the hood:

- Iteration N finished.
- Engine captures the final value of loop counter.
- Engine allocates a fresh block of memory for iteration N+1.
- The value is copied into the new slot.

### Why It Matters

This is the underlying mechanism that allow closures (like setTimeout or event listeners inside the loop) to capture unique, individual values per iteration instead of sharing the final mutated value.

### Concurrency Phase — Call Stack vs Event Loop

JS operates on a single threaded event loop model, which heavily dictates loop performance constraints.

When a loop runs, it monopolizes the call stack. While the engine is executing those CPU "jumps" commands back and forth, nothing else can touch the thread → This creates a problem! The event loop (which handles click, rendering, and API fetches) is completely frozen.

If loop takes 10 sec to finish, the browser cannot redraw the screen or respond to user clicks, resulting in the notorious "page unresponsive" error.

To JS engine, a loop is a single, massive synchronous block of code that must be cleared from the Call Stack entirely before anything in the app is allowed to run.

## Objects and Arrays

### Objects

Object is unordered group of properties. Each property consists of key (string name) and value (which can be any datatype including functions, arrays or other objects).

```js
const user = {
    firstName: "Aryan",
    age: 22
    isAdmin: true
};
```

### Arrays

Array is ordered list where item is mapped to a numeric index starting in 0. They are dynamic, meaning they can grow or shrink automatically.

### Common Array Methods

- `push()` / `pop()`: Adds or removes the items from the end of the array.
- `unshift()` / `shift`: Adds or removes item from the beginning of the array.
- `forEach()`: Loops through each element in the array.
- `map()`: Transforms the items into a brand new array.
- `filter()`: Creates a subset of the array based on conditional test.

### Array of Objects

Array of object is a structure where a list (array) contains multiple structured profile of the items (the objects).

```js
const products = [
    {id: 101, name: "Wireless Mouse"},
    {id: 102, name: "4K Monitor"}
]
```

### Object of Object

Object of object is a collection where a parent object contains other objects as its values.

```js
const users1 = {
    name: "Haskirat",
    age: 19,
    address: {
        city: "Delhi",
        country: "India",
        address: "1122 DLF"
    }
}
```

```js
const city = users1.address.city
```

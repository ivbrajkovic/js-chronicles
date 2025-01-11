# Currying: Unlocking the Modular Superpower of JavaScript

## **Table of Contents**  
- [Introduction](#intro)  
- [What the Heck is Currying?](#what-is-currying)  
- [Step-by-Step Guide to Currying](#step-by-step)  
- [Why Should You Care About Currying?](#why-currying)  
- [Automating Currying (Because Life is Too Short)](#automating-curry)  
- [TypeScript and Currying: Friends Forever](#typescript)  
- [A Quick Note on Pitfalls](#pitfalls)  
- [Conclusion](#conclusion)  

---

<a id="intro"></a>
## **Introduction**  
Ever looked at your code and thought, _“Wow, this is about as organized as my sock drawer?”_ Well, you’re not alone. JavaScript, for all its quirks and charms, can get messy. Enter currying—a magical technique from functional programming that makes your code modular, reusable, and dare I say, elegant. If this sounds like wizardry, buckle up. We’re about to dive in.

---

<a id="what-is-currying"></a>
## **What the Heck is Currying?**  
Currying sounds like something you'd do in the kitchen, but in JavaScript, it’s the art of transforming a function with multiple arguments into a sequence of functions, each taking a single argument. Think of it as breaking down a complicated recipe into bite-sized steps.

Here’s a simple example:

```typescript
function multiply(a: number, b: number) {
  return a * b;
}
```

Cool, right? But this function demands both arguments upfront. If you’re not ready to commit to both (who is, really?), currying lets you call it like this instead:

```typescript
const curriedMultiply = (a: number) => (b: number) => a * b;

// Create reusable specialized functions
const double = curriedMultiply(2);
const triple = curriedMultiply(3);

console.log(double(4)); // Output: 8
console.log(triple(4)); // Output: 12
```

Boom! Now you can pass one argument at a time, like assembling a sandwich. Now that you’ve seen a simple curried function, let’s learn how to build one step by step.

---

<a id="step-by-step"></a>
## **Step-by-Step Guide to Currying**  

1. **Start with a Multi-Argument Function**
    Imagine you have a function that takes several arguments:  

    ```typescript
    function add(a: number, b: number, c: number) {
      return a + b + c;
    }
    ```  

2. **Wrap It in Layers**
    Transform it into a sequence of functions:  

    ```typescript
    const curriedAdd = 
      (a: number) => (b: number) => (c: number) => a + b + c;
    ```

3. **Pass Arguments One at a Time**
    Call each layer one at a time:  

    ```typescript
    const step1 = curriedAdd(2); // Fixes the first argument (a = 2)
    const step2 = step1(3);      // Fixes the second argument (b = 3)
    const result = step2(4);     // Fixes the third argument (c = 4)

    console.log(result);         // Output: 9
    ```

4. **Profit from Flexibility**
    Now you have small, reusable steps that make your code more modular.

---

<a id="why-currying"></a>
## **Why Should You Care About Currying?**  
Currying might sound fancy, but it has real-world perks:  

- **Modularity**: Break big functions into small, testable pieces.  
- **Reusability**: Use existing code to create specialized function versions.  
- **Readability**: Your code becomes self-explanatory, like a well-written novel (or at least a decent one).  

Let’s take a moment to remember why readability matters. Code isn’t just written for machines—they’ll understand anything you throw at them. It’s written for humans—**your colleagues, your future self, and the next person who needs to modify it**. Good code should aim to be easy to understand, test, and modify. Currying helps achieve this by breaking logic into smaller, clear pieces that make sense at a glance.

Let’s say you’re filtering a list of objects:  

```typescript
const filterByKey = (key: string) => (value: any) => (data: any[]) =>
  data.filter((item) => item[key] === value);

// Create a reusable filter for colors
const filterByColor = filterByKey("color");
const redItems = filterByColor("red");

console.log(redItems([{ color: "red" }, { color: "blue" }])); 
// Output: [{ color: "red" }]
```

Or calculating taxes:  

```typescript
const taxCalculator = 
  (rate: number) => (amount: number) => amount * (1 + rate / 100);

// Create specialized calculators
const calculateVAT = taxCalculator(25); 
const calculateIncomeTax = taxCalculator(18);

console.log(calculateVAT(100)); // Output: 125
console.log(calculateIncomeTax(200)); // Output: 236
```

Each function does one thing, making your code easier to read, understand, and test. By creating smaller, specialized functions, currying makes complex logic simple, reusable, and maintainable for the humans who will work on it later.

---

<a id="automating-curry"></a>
## **Automating Currying (Because Life is Too Short)**  

Typing all those nested functions manually? No thanks. Let’s automate currying:

```typescript
function curry(func: (...args: any[]) => any) {
  return function curried(...args: any[]) {
    if (args.length >= func.length) {
      // `func.length` gives the expected number of arguments
      return func(...args); 
    }
    return (...nextArgs: any[]) => curried(...args, ...nextArgs); 
  };
}
```

Here’s how it works:  

1. If the number of arguments matches the original function, it calls the function.  
2. Otherwise, it returns a new function that waits for more arguments.  

Curry function example—adding three numbers:

```typescript
const addThreeNumbers = (a: number, b: number, c: number) => a + b + c;
const curriedAddThreeNumbers = curry(addThreeNumbers);

console.log(curriedAddThreeNumbers(1)(2)(3)); // Output: 6
console.log(curriedAddThreeNumbers(1, 2)(3)); // Output: 6
console.log(curriedAddThreeNumbers(1)(2, 3)); // Output: 6
```

It’s like a vending machine for arguments: put in one at a time or all at once.

---

<a id="typescript"></a>
## **TypeScript and Currying: Friends Forever**  

Take currying to the next level with type safety in TypeScript:  

```typescript
function curry<T1, R>(func: (arg1: T1) => R): (arg1: T1) => R;
function curry<T1, T2, R>(func: (arg1: T1, arg2: T2) => R): 
  (arg1: T1) => (arg2: T2) => R;
function curry<T1, T2, T3, R>(func: (arg1: T1, arg2: T2, arg3: T3) => R): 
  (arg1: T1) => (arg2: T2) => (arg3: T3) => R;

function curry(func: any): any {
  return function curried(...args: any[]) {
    if (args.length >= func.length) {
      return func(...args);
    }
    return (...nextArgs: any[]) => curried(...args, ...nextArgs);
  };
}
```

Example with TypeScript:

```typescript
const add = (a: number, b: number) => a + b;
const curriedAdd = curry(add);

const addFive = curriedAdd(5);
console.log(addFive(10)); // Output: 15
```

Your IDE will guide you every step of the way.

---

<a id="pitfalls"></a>
## **A Quick Note on Pitfalls**  
Curried functions lose their original `this` context. If you need to use a method of a class with currying, use `.bind(this)` or arrow functions.

---

<a id="conclusion"></a>
## **Conclusion 🧩**  

Currying is like upgrading your coding game with cheat codes. By breaking down functions into smaller, manageable pieces, you gain flexibility, readability, and a sense of accomplishment (or at least fewer debugging headaches). Whether you automate it or write it by hand, currying turns your code into a clean, modular powerhouse.  

As Haskell Curry (the man behind the name) might’ve said: _“Write less, do more… one argument at a time!”

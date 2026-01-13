# MondotSyn — README

This repository contains information about the syntax of a programming language I designed and planned.

MondotSyn follows an **imperative style with light-weight object support and function overloading**. It mixes procedural programming with rich data structures and object-like methods so you can organize code in a modular, reusable way.

## Table of contents

* [Core language structures](#core-language-structures)
* [Statements](#statements)
* [Operators](#operators)
* [Language notes (diagnosis)](#language-notes-diagnosis)
* [Specifications](#specifications)

## Core language structures

### Units

Code is organized into **units**, which work like modules. A Mondot project or bytecode program can start from a given unit. Each unit may contain:

* Function declarations
* Property/type declarations (composite types / objects / values)
* The entry point `main`

Simple Mondot example:

```mon
unit myproj.file.program:

on void main()
    number x = 100000
    print("start")
    
    while (x > 0)
        print(x)
        x = x - 1
    end

    print("end")
end
```

# Statements

* **units**
  ```
  unit file.unitname:
  body
  ```
  or
  ```
  unit file.unitname{
      body
  }
  ```

* **variables**
  ```
  T name = value
  ```

* **conditionals**
  ```
  if (cond)
      body-if
  or (cond2)
      body-else-if
  or
      body-else
  end
  ```

* **loops**
  ```
  while (cond)
      body
  end

  for (iterator: T in values)
      body
  end
  ```

* **error handling**
  ```
  try
      danger-body
  catch error-type
      handle-body
  finally
      defer-body
  end
  ```

* **item declaration**
  ```
  item name
      T value
      T value2
      ...
  end
  ```

* **functions**
  ```
  on T funcname(args: T)
      body
  end

  funcname() -- call
  ```

# Operators

* Arithmetic
  * `+` `-` `*` `/` `%`
* Logical / comparison
  * `!` `==` `!=` `<=` `>=` `|` `&` `<` `>`
* Assignment operators
  * `+=` `-=` `|=` `&=`

---

# Language notes (diagnosis)

Mondot was designed with **simplicity, predictability and performance** in mind — it’s a good fit for embedded scenarios and game engines where you need tight control over memory and deterministic behavior.

# Specifications

## Type system

* **Typing**: Static, with controlled runtime conversions when `any` is involved.
* **Special type**: `any` — resolved at runtime and contagious (any expression involving `any` yields `any`).
* **Primitive types**: `number`, `string`, `void`, `bool` (implicit)
* **Composite types**: `table` (heterogeneous list), `list`
* **Lightweight record**: `item` — a mutable, lightweight record passed by implicit reference.
* **Function type**: `function`, first-class values (can be stored and invoked), **no capture** of outer variables.

> **Note:** `item` is intended as a lightweight, predictable record, while `table` is a heavier, dynamic structure suited for heterogeneous data and scripting use.

## Block syntax

* Blocks are closed with `end`.
* There are no required `{}` or indentation rules for delimiting blocks; `{}` may be used only to delimit units.

## Units and visibility

* All symbols declared inside a `unit` are **public**.
* By convention, names that start with `_` are considered internal (tools can emit warnings), but this has no runtime effect.

## Memory semantics

* **Scope-based lifetime** — anything allocated inside a scope is destroyed when that scope ends.
* Implementations can use stack frames + per-scope arenas to realize this model.

## Parameter passing

* `number`, `string`, and other primitives are **passed by value**.
* `list`, `table` and `item` are **mutable and passed by implicit reference**.

## Functions and overloading

* Overload resolution is done **at compile time** based on signatures (unless `any` is involved).
* Calling a function where `any` is present uses dynamic dispatch at runtime.

## Calling convention

* Parameters up to N are passed via registers (`r0..rN`); extras go on the stack frame.
* Return value is in `r0`.
* `item`/`list`/`table` are passed as references (the register holds a pointer/handle to the scope arena).

## Overload resolution

* Exact type-list matching is used to pick an overload.
* If multiple overloads match, prefer the most specific one (the one that uses fewer `any`/coercions).
* If ambiguity remains, it’s a compile-time error.

## `any` semantics

* `any` carries runtime type tags (e.g. `number`, `string`, `table`, `item`, `function`, `nil`, …).
* Operations on `any` use dispatch functions (e.g. `+` becomes `dyn_add(tag1, tag2)` under the hood).
* Any expression involving `any` yields `any`.

## Exceptions

* `try` / `catch (error e)` / `finally` are supported.
* Stack unwinding guarantees destruction of scope-local values.

## Native libraries

* `std`
  * `print()` — console output
  * `input()` — console input
  * `flush()` — flush console output
  * …
* `std.extensions`
  * `append()` — for lists and tables
  * `insert()` — for tables
  * `len()` — length of lists and tables
  * `create()` — constructor for `item` and collections
  * …
* `std.math`
  * `sin()`
  * `cos()`
  * `tg()`
  * …

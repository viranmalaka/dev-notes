# Typescript

(using the sysco udemy caurse)

## Core Types
 - number
 - string
 - boolean

 Type of variable cannot be changed during runtime.

 Type Inference
  - variable type is getting from it's value
  
 - Object
 - Array
    - `number[]`
 - Tuples
    - fixed length and fixed type array
    - `role: [number, string]`
    - tuples dosne't support `.push()` operation
 - Enums
    - `enum Role  { NEW, OLD }`
    - can set the stating values for the values
        - `enum { ADMIN = 4, AUTHOR }`
 - any

 - Union types
   - define with two differnet types
     - `input: number | string`
 - Literal Types
    - define type with specific values
        - `input1 : 'as-int' | 'as-str'`
 - function return type
    - void - return nothing
 - function as a type
    - can be usefull for callback
        - `const fn: (a: number, b: number) => number`
 - unknown
    - this is better than `any`. we cannot assing unknow to any string value but inside a `typeof` check if block, we can assing the above correctly
 - never
    - this can be used as the return type of a function which throw an error always. (that means the function is crashing in the middle way)

## Typescript compiler configuration
 - `-w` watch mode
 - `tsc --init` to create typescirpt project folder and after that just running `tsc` will compile all the `.ts` file inside the dir.
 - exclude
 - target
 - lib
    - define what are the libraries availabe in the env. like `dom` for `document / console`
    - `dom, es6, dom.iterable, scripthost` are the default

## classes
 - private
 - public
 - readonly
 - `constructor (private id: string, private name: string)` this will create two local variable as well.
 - inheritance - `extends`
 - protected modifier
 - getter setters
    - `get` and `set`
 - static properites and methods
 - abstract classes
 
 ## Interface
 - Interface and types are different
    - calss can be extend only from interfaces
 - `readonly` only once that field can be assigned
 - define a function type as interface
    - this will equall to `type AddFn: (a: number, b: number) => number;`
    ```
        interface AddFn {
            (a: number, b: number): number;
        } 
    ```
 - optional property
    - `name ?: string`

## Advanced Typescript Types
- combining two types
    - `type newType = Admin & Employee`
- type cguards
    - adding if condition with `typeof` keywoard to differentiate the actions depending on the type
    - `if ('something' in variable)`
    - `instanceof` for classes
- Discriminated Union
    - define a common property to keep track of the type of the object.
    ```
        interface Bird { type: 'bird', flyingSpeed: number }
        interface Hourse { type : 'hours', runningSpeed: number }
    ```
- type casting
    - `const Variable = a as TypeA`
    - `const Variable = <TypeA>a`
- not `null`
    - `const Variable = a!`
- In case we don't know the exact property name
    - use index properites (following example can contain any string key with string value)
    ```
        interface ErrorContainer {
            [prop: string]: string
        }
    ```
- Function overload
    - define different return type depending on the arts
    ```
    type Com = string | number;

    function add(a: number, b: number): number;
    function add(a: string, b: string): string;
    function add(a: Com, b: Com) {
        if (type of a === 'string' || type of b === 'string') {
            return a.toString() + b.toString();
        }
        return a + b
    }
    ```
- typescritp 3 <
    - safely access innter properties without null checks
- Nullish Coalescing
    - fallback the values if the variable is only when `null` or `undefined`. this is not work for empty string or 0.
        - `const data = input || 'default';`
        - `const data = input ?? 'default';`

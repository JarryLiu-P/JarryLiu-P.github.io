---
title: TypeScript-基本类型
date: 2019-04-30 09:42:41
tags: TypeScript
---
### 基本类型
#### 布尔（Boolean）
```
let isDone: boolean = false;
```
#### 数字（Number）
除了十六进制和十进制之外，TypeScript还支持ECMAScript 2015中引入的二进制和八进制。
```
let decimal: number = 6;
let hex: number = 0xf00d;
let binary: number = 0b1010;
let octal: number = 0o744;
```
#### 字符串（String）
像JavaScript一样，TypeScript也使用双引号（"）或单引号（'）来包围字符串数据。
```
let color: string = "blue";
color = 'red';

let fullName: string = `Bob Bobbington`;
let age: number = 37;
let sentence: string = `Hello, my name is ${ fullName }.

I'll be ${ age + 1 } years old next month.`;
```
#### 数组（Array）
```
let list: number[] = [1, 2, 3];
let list: Array<number> = [1, 2, 3];
```
#### 元组（Tuple）
元组类型允许表示一个数组，其中为已知固定数量和类型的元素。
```
/ Declare a tuple type
let x: [string, number];
// Initialize it
x = ["hello", 10]; // OK
// Initialize it incorrectly
x = [10, "hello"]; // Error
```
访问具有已知索引的元素时，将检索正确的类型：
```
console.log(x[0].substr(1)); // OK
console.log(x[1].substr(1)); // Error, 'number' does not have 'substr'
```
访问已知索引集之外的元素时，将使用联合类型：
```
x[3] = "world"; // OK, 'string' can be assigned to 'string | number'

console.log(x[5].toString()); // OK, 'string' and 'number' both have 'toString'

x[6] = true; // Error, 'boolean' isn't 'string | number'
```
#### 枚举（Enum）
```
enum Color { Red, Green, Blue }
let c: Color = Color.Green;
```
默认情况下，枚举开始为其成员编号0。您可以通过手动设置其中一个成员的值来更改此设置。
```
enum Color { Red = 1, Green = 2, Blue = 4 }
let c: Color = Color.Green;

let colorName: string = Color[2];
console.log(colorName); // Displays 'Green' as its value is 2 above
```
#### 任意（Any）
我们可能需要描述在编写应用程序时我们不知道的变量类型。这些值可能来自动态内容，例如来自用户或第三方库。在这些情况下，我们希望选择退出类型检查，并让值通过编译时检查。
```
let notSure: any = 4;
notSure = "maybe a string instead";
notSure = false; // okay, definitely a boolean

let list: any[] = [1, true, "free"];
list[1] = 100;
```
#### 对象（Object）
object是一种类型的，它表示非原始型的，即任何不是number，string，boolean，symbol，null，或undefined的类型。
```
declare function create(o: object | null): void;

create({ prop: 0 }); // OK
create(null); // OK

create(42); // Error
create("string"); // Error
create(false); // Error
create(undefined); // Error
```
#### 无返回类型（Void）
```
function warnUser(): void {
    console.log("This is my warning message");
}
```
声明类型的变量void没有用，因为您只能分配undefined或null给它们：
```
let unusable: void = undefined;
```
#### 空和未定义（undefined/null）
```
// Not much else we can assign to these variables!
let u: undefined = undefined;
let n: null = null;
```
#### 无法到达的终点（Never）
The never type is a subtype of, and assignable to, every type; however, no type is a subtype of, or assignable to, never (except never itself). Even any isn’t assignable to never.
```
// Function returning never must have unreachable end point
function error(message: string): never {
    throw new Error(message);
}

// Inferred return type is never
function fail() {
    return error("Something failed");
}

// Function returning never must have unreachable end point
function infiniteLoop(): never {
    while (true) {
    }
}
```

### 类型断言
类型断言就是告诉编译器“信任我，我知道我在做什么。”
```
let someValue: any = "this is a string";
let strLength: number = (<string>someValue).length;
```
```
let someValue: any = "this is a string";
let strLength: number = (someValue as string).length;
```
#### When using TypeScript with JSX, only as-style assertions are allowed.


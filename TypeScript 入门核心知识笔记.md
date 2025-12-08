
## 📘 TypeScript 入门核心知识笔记

---

### 一、基础概念与类型入门

#### 1. TypeScript 简介

- **是什么？** TypeScript 是 JavaScript 的 **超集**，它为 JavaScript 添加了 **静态类型系统**。
- **为什么用？** 可以在 **编写代码阶段**（编译时）发现和捕除类型错误，而不是等到代码运行时。
- **最终结果：** 所有的 TypeScript 代码最终都会被 **编译（Transpiled）** 成浏览器可识别的普通 JavaScript 代码。

#### 2. 基础类型

TypeScript 强制要求变量必须是指定的类型，从而提高代码的健壮性。

| **类型**        | **描述**                | **示例**                          |
| ------------- | --------------------- | ------------------------------- |
| **`number`**  | 任何数字（整数或浮点数）。         | `let age: number = 30;`         |
| **`string`**  | 任何文本数据。               | `let name: string = "小明";`      |
| **`boolean`** | 逻辑值，`true` 或 `false`。 | `let isActive: boolean = true;` |

**示例：**

``` TypeScript
let isLearning: boolean = true; // 学习时你正确写出的布尔值声明
```

---

### 二、对象结构与函数类型

#### 1. 变量声明与类型注解

- **语法：** `let 变量名: 类型 = 值;`
- **注意：** 变量一旦声明了类型，就不能再赋予其他类型的值。

#### 2. 接口（Interfaces）

- **作用：** 用来定义 **对象** 的 **“形状”** 或 **“结构”**，确保对象拥有特定的属性和类型。
- **示例：**

``` TypeScript
interface Student {
    name: string;
    age: number;
    isEnrolled: boolean;
}

const student1: Student = {
    name: "王小帅",
    age: 18,
    isEnrolled: true
};
```

#### 3. 函数类型

- **作用：** 为函数的 **参数** 和 **返回值** 添加类型约束，确保输入和输出的类型安全。
- **`void` 类型：** 如果函数没有返回值（例如，只执行打印操作），返回值类型应设置为 `void`。
- **语法示例：**

``` TypeScript
// 参数和返回值都必须是 number
function add(num1: number, num2: number): number {
    return num1 + num2;
}

// 没有返回值的函数
function greet(name: string): void {
    console.log(`Hello, ${name}!`);
}
```

---

### 三、中级类型处理与数据结构

#### 1. 联合类型 (Union Types) 与字面量类型 (Literal Types)

- **联合类型 (`|`)：** 允许一个变量可以拥有多种类型中的 **任意一种**。
    - `let id: number | string = 101;`

- **字面量类型：** 允许变量只能取 **特定的几个精确值**。
    - `type Direction = "up" | "down";`

#### 2. 类型别名 (Type Aliases) 与交叉类型 (Intersection Types)

- **类型别名 (`type`)：** 为任何类型（包括联合类型、对象结构等）创建新的名字，便于重用和阅读。
    - `type ID = number | string;`

- **交叉类型 (`&`)：** 将多个类型 **合并** 成一个新类型，新类型拥有所有被合并类型的 **所有特性**（常用于合并对象的属性）。
    - `type FullUser = User & TimeStamp;`

#### 3. 数组 (Arrays) 与 元组 (Tuples)

- **数组：** 存储一组 **同类型** 的数据。
    - `let scores: number[] = [90, 85, 92];`

- **元组：** 存储 **类型固定、数量固定** 的序列数据。
    - `let productSku: [number, string, boolean] = [9527, "苹果", true];`

---

### 四、高级概念：灵活性与封装

#### 1. 泛型 (Generics)

- **作用：** 编写 **可重用**、**灵活** 且 **类型安全** 的代码，将数据类型作为 **参数** 传递进去。
- **语法：** 使用尖括号 `<T>` 定义类型变量 `T`。
- **示例：**

``` TypeScript
// T 是一个类型变量。函数接收 T[] 数组，并返回 T 类型元素
function getFirstElement<T>(arrays: T[]): T {
    return arrays[0];
}

let first = getFirstElement(["a", "b"]); // T 被推断为 string
```

#### 2. 类 (Classes) 与访问修饰符

- **类：** 对象的蓝图，是面向对象编程的基础。
- **访问修饰符：** 控制属性和方法的可见性。

|**修饰符**|**描述**|**访问范围**|
|---|---|---|
|**`public`**|公有，默认。|任何地方|
|**`private`**|私有。|仅在定义它的类内部|
|**`protected`**|受保护。|类内部和子类内部|

- **示例：**

``` TypeScript
class Employee {
    private secretCode: string = "TS999"; // 私有属性
    
    constructor(public name: string) { /* 构造函数 */ }

    public getSecret(): string {
        // 在类内部访问私有属性必须使用 this
        return this.secretCode; 
    }
}
```

---

### 五、疑问与关键对比总结

#### 疑问 1：变量赋值时是否重复使用 `let`？（中级阶段）

- **结论：** 变量一旦通过 `let`（或 `const`/`var`）**声明** 之后，后续进行 **赋值** 时，**不需要** 再使用 `let` 关键字。`let` 仅用于变量的声明和初始化。
- **示例：**

``` TypeScript
let score: number | string = 99; // 声明和初始化
score = "loading";              // 赋值，无需 let
```

#### 疑问 2：Interface 与 Type 的区别？（中级阶段）

| **特性**    | **Interface (接口)**                   | **Type (类型别名)**                      |
| --------- | ------------------------------------ | ------------------------------------ |
| **定义范围**  | 主要用于定义 **对象** 的形状。                   | 可以定义 **任何** 类型（对象、联合类型、字面量等）。        |
| **扩展/合并** | 支持 **声明合并**（自动合并同名接口）和 `extends` 继承。 | 不支持自动合并。扩展需要使用 **交叉类型 (`&`)**。       |
| **选择建议**  | **定义对象结构，且未来可能被扩展时**，优先使用 Interface。 | **定义联合类型、字面量类型或复杂非对象类型时**，必须使用 Type。 |

#### 疑问 3：类方法中如何访问属性？（高级阶段）

- **结论：** 在类的 **方法内部** 访问该类的任何 **属性**（无论是 `public`、`private` 还是 `protected`），都必须使用 **`this` 关键字** 来引用当前对象实例的属性。
- **示例：**

``` TypeScript
// ❌ 错误：logCount++;
// ✅ 正确：this.logCount++; 
```

## Change log

2025-12-8 15:40:11 创建初稿

# 🚀 JavaScript 学习笔记

---

## I. 基础概念 (初级阶段)

### 1. 什么是 JavaScript？

- **定义：** JavaScript (JS) 是赋予网页**行为和交互能力**的脚本语言。
    - HTML 负责结构（骨架）。
    - CSS 负责样式（外观）。
    - **JS 负责行为**（大脑和神经系统）。

### 2. 如何引入 JavaScript？

|**方式**|**描述**|**示例**|**推荐？**|
|---|---|---|---|
|**内部 JS**|将代码写在 HTML 文件中的 `<script>` 标签内。|`<script> console.log("Hello!"); </script>`|否 (代码分离差)|
|**外部 JS**|将代码写在独立的 `.js` 文件中，然后在 HTML 中通过 `src` 属性链接。|`<script src="app.js"></script>`|**是** (利于重用和维护)|

### 3. 变量声明与数据类型

#### A. 变量声明 (`let` 和 `const`)

变量是存储数据的“盒子”。

|**关键字**|**含义**|**用途/可变性**|**示例**|
|---|---|---|---|
|**`const`**|Constant（常量）|**值不可变**，推荐用于不需更改的值（如出生年份、圆周率、函数）。|`const PI = 3.14;`|
|**`let`**|Variable（变量）|**值可变**，用于需要修改的值（如分数、步数、计数器）。|`let score = 100;`|
|`var`|Variable（历史）|**不推荐**。存在作用域问题，现代代码应避免使用。|`var old = 1;`|

#### B. 基本数据类型

|**类型**|**描述**|**示例**|
|---|---|---|
|**Number**|整数或浮点数（小数）。|`100`, `3.14`|
|**String**|文本数据，必须用引号包裹。|`"Hello"`, `'你好'`|
|**Boolean**|逻辑值，只有 `true`（真）或 `false`（假）。|`true`, `isLoggedOut`|

### 4. 操作符和流程控制

#### A. 算术操作符

| **符号** | **描述**       | **示例**   | **结果** |
| ------ | ------------ | -------- | ------ |
| `+`    | 加法           | `10 + 5` | `15`   |
| `-`    | 减法           | `10 - 5` | `5`    |
| `*`    | 乘法           | `10 * 5` | `50`   |
| `/`    | 除法           | `10 / 5` | `2`    |
| `%`    | **取余** (求余数) | `10 % 3` | `1`    |

#### B. 比较操作符

|**符号**|**描述**|**示例**|**结果**|
|---|---|---|---|
|`>`|大于|`5 > 3`|`true`|
|`<`|小于|`5 < 3`|`false`|
|`==`|**等于** (只比较值)|`5 == "5"`|`true`|
|`===`|**严格等于** (比较值和类型)|`5 === "5"`|`false`|

#### C. 条件语句 (`if...else`)

根据条件执行代码：

``` JavaScript
if (userAge < 17) {
    // 条件 (userAge < 17) 为 true 时执行
    console.log("抱歉，年龄不足。");
} else {
    // 否则执行
    console.log("欢迎观看。");
}
```

## II. 结构化编程 (中级阶段)

### 1. 数组（Arrays）

数组是存储**有序列表**的集合。

- **创建：** 使用方括号 `[]`。 `const fruits = ["苹果", "香蕉", "橙子"];`
- **索引：** 数组编号**从 0 开始**。`fruits[0]` 是 `"苹果"`。
- **添加元素：** 使用 `.push()` 方法在末尾添加。 `fruits.push("西瓜");`

#### 💡 关键知识点：`const` 数组的可变性

- **`const` 保证变量引用不可变：** 你不能将 `fruits` 重新赋值给一个新的数组或数字（例如 `fruits = 1`）。
- **数组内容可变：** 你可以修改数组内部的元素或改变数组的长度（例如 `fruits[1] = "鸭梨"` 或使用 `push()`）。

### 2. 函数（Functions）

函数是执行特定任务的“机器”，用于代码重用和组织。

- **声明：** 使用 `function` 关键字。
- **参数：** 函数需要的输入值。
- **返回值：** 使用 `return` 关键字返回结果。

``` JavaScript
function addNumbers(a, b) {
    let sum = a + b;
    return sum; // 返回 
}
let result = addNumbers(5, 3); // 调用函数，result = 8
```

#### 🚨 关键知识点：`return` 的作用

一旦执行到 `return` 语句，函数会**立即终止**，并将结果返回给调用者。`return` 后面的任何代码都不会被执行。

### 3. 循环（Loops）

用于重复执行代码块，解决重复性任务。
- **`for` 循环语法：** `for (初始化; 条件判断; 迭代更新)`

#### 遍历数组示例

``` JavaScript
const fruits = ["苹果", "香蕉", "橙子"];

// 循环从索引 0 开始，到数组长度结束
for (let i = 0; i < fruits.length; i++) {
    console.log(fruits[i]); // 使用 i 作为索引访问每个元素
}
```

## III. 高级概念入门 (高级阶段)

### 1. 对象（Objects）

对象用于存储复杂的、**无序的键值对**数据，描述一个实体。
- **创建：** 使用花括号 `{}`。

``` JavaScript
const student = {
    name: "小李",
    age: 20
};
```

**访问属性：**
- **点表示法（推荐）：** `student.name`，键名必须是标准变量名。
- **方括号表示法：** `student["age"]`，键名必须是字符串（用引号包裹）。

**必须使用方括号的情况：**

1. **键名包含特殊字符（如连字符 `-` 或空格）：**

``` JavaScript
const movie = {
    "release-year": 2009, // 键名包含连字符
    "main actor": "杰克"     // 键名包含空格
};

// ✅ 必须使用方括号访问：
console.log(movie["release-year"]); // 输出 2009
console.log(movie["main actor"]);    // 输出 杰克

// ❌ 尝试使用点表示法会出错：
// movie.release-year
```

2. **键名是一个变量时：**

``` JavaScript
const user = { name: "小红", email: "xiaohong@example.com" };
let keyToFind = "email";

// ✅ 必须使用方括号和变量名：
console.log(user[keyToFind]); // 输出 xiaohong@example.com

// ❌ 错误使用：这会查找一个名为 "keyToFind" 的键，而不是 "email"：
// console.log(user.keyToFind);
```

### 2. 事件处理（Event Handling）

让 JavaScript 响应用户操作（事件）。
- **核心方法：** `addEventListener()`

``` JavaScript
// 1. 找到按钮元素（这一步在实际代码中会用到特殊方法，我们暂时跳过细节）
// let myButton = document.getElementById("my-btn"); 

// 2. 添加一个“点击”事件的监听器
myButton.addEventListener('click', function() {
    // 当按钮被点击时，执行这里的代码（这是一个函数！）
    alert("你点击了按钮！"); 
});
```

**常见的用户事件包括：**

- **`click`：** 用户点击了鼠标按钮。
- **`mouseover`：** 鼠标移动到了某个元素上方。
- **`submit`：** 用户提交了表单。
- **`keydown`：** 用户按下了键盘上的键。

### 3. 箭头函数（Arrow Functions）

现代、简洁的函数声明方式，使用 `=>` 符号。
- **最佳实践：** 推荐使用 `const` 声明箭头函数。

|**特性**|**传统函数**|**箭头函数 (简洁版)**|
|---|---|---|
|**基础版**|`function add(a, b) { return a + b; }`|`const add = (a, b) => { return a + b; };`|
|**单行返回**|`function sq(x) { return x * x; }`|`const sq = x => x * x;` **(隐式返回)**|

## Change log

2025-12-4 11:04:08 创建初稿
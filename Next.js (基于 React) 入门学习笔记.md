
## ⭐️ Next.js (基于 React) 入门学习笔记 ⭐️

---
### 一、 核心基础与 Next.js 简介 (初级水平)

#### 1. React 是什么？

- **定义**：**React** 是一个用于构建用户界面 (UI) 的 **JavaScript 库**。
- **核心概念——组件 (Component)**：React 将 UI 拆分成一个个独立、可复用、像 **乐高积木** 一样的小单元，称为组件（例如：按钮、导航栏、商品评论区）。

#### 2. Next.js 是什么？

- **定义**：**Next.js** 是一个基于 React 的 **Web 框架**，它为 React 应用解决了在生产环境中遇到的性能和 SEO 问题。
    
- **核心优势**：
    - **性能优化**：通过预渲染机制实现超快加载。
    - **SEO 友好**：搜索引擎能轻松读取内容，利于网站排名。

#### 3. 预渲染 (Pre-rendering)：Next.js 的性能基石

预渲染是指在内容到达用户浏览器之前，就将完整的 HTML 页面准备好。

- **SSR (Server-Side Rendering - 服务器端渲染)**
    - **工作方式**：在 **用户请求时**，服务器实时生成 HTML。
    - **特点**：内容总是最新的，但服务器压力大，如果服务器性能不好，可能会变慢。
    - **适用场景**：**内容实时变化**的页面（如用户订单、新闻头条）。

- **SSG (Static Site Generation - 静态网站生成)**
    - **工作方式**：在 **项目构建时**（部署前），提前生成完整的 HTML 文件。
    - **特点**：速度极快，性能最好，因为它只是发送预存的文件。
    - **适用场景**：**内容不常变化**的页面（如博客文章、关于我们）。

### 二、 路由、结构与交互 (中级水平)

#### 4. 应用结构与启动

| **功能** | **Node.js (使用 npm/npx)**            | **Bun**                                 |
| ------ | ----------------------------------- | --------------------------------------- |
| 创建项目   | `npx create-next-app@latest my-app` | **`bun create next-app my-app`**        |
| 指定版本   | `npx create-next-app@13.5.6 my-app` | **`bun create next-app@13.5.6 my-app`** |
| 安装依赖   | `npm install`                       | `bun install`                           |
| 启动开发   | `npm run dev`                       | `bun run dev`                           |

#### 5. 路由 (Routing) 基础

Next.js 使用基于**文件系统**的路由（App Router 模式）：

- **路径即文件夹**：`app/products/` 文件夹对应 `/products` 路径。

- **页面文件**：一个文件夹中的 **`page.js`** 文件对应实际的页面内容。`app/page.js` 对应网站的**根路径** `/`，即主页。

- **文件命名**：在 App Router 中，页面文件必须是 `page.js` (或 `.jsx/.tsx`)，不能使用 `index.js`。

#### 6. 布局 (Layouts) 和共享 UI

- **`layout.js`**：用于定义一个文件夹及其子路径下的所有页面都共享的公共界面（如导航栏、页脚）。

- **嵌套机制**：布局是**嵌套**的，父级布局包裹子级布局。例如，`app/layout.js` 总是包裹整个应用。

#### 7. Server Components vs. Client Components

Next.js 强制区分了两种组件类型：

| **组件类型** | **Server Component (服务器组件) 🖥️** | **Client Component (客户端组件) 🌐**                        |
| -------- | -------------------------------- | ------------------------------------------------------ |
| **运行环境** | **服务器 (Server)**                 | **浏览器 (Client)**                                       |
| **关键功能** | 数据获取、访问数据库、SEO、**默认类型**。         | **用户交互**、使用 Hooks (`useState`, `useEffect`)、访问浏览器 API。 |
| **切换方式** | **默认**就是 Server Component        | 文件顶部添加 **`"use client";`** 指令。                         |

**Server Component (数据获取与渲染)：**

**Server Component** 默认是异步的，可以直接使用 `async/await` 来获取数据，这是它的核心职责。它在服务器上运行，所以没有交互功能。

``` JavaScript
// app/posts/page.js (默认为 Server Component)

async function getPosts() {
  // ⚠️ 这里是安全的！API 密钥等信息不会暴露给浏览器
  const response = await fetch('https://api.blog.com/posts'); 
  return response.json();
}

export default async function PostsPage() {
  const posts = await getPosts(); // 在服务器上等待数据

  return (
    <div>
      <h1>最新博客文章</h1>
      {posts.map(post => (
        <div key={post.id}>
          <h2>{post.title}</h2>
          <p>{post.summary}</p>
        </div>
      ))}
      {/* 无法在这里使用 useState 或 onClick */}
    </div>
  );
}
```

**Client Component (交互与状态管理)：**

**Client Component** 必须在文件顶部添加 `"use client";` 指令，才能使用 Hooks 来实现交互功能。

``` JavaScript
// components/CounterButton.js (Client Component)

"use client"; // 👈 必须添加这行指令

import React, { useState } from 'react';

export default function CounterButton() {
  // ✅ 可以使用 useState 管理状态
  const [count, setCount] = useState(0); 

  return (
    <div>
      <p>当前计数: {count}</p>
      {/* ✅ 可以响应用户交互 */}
      <button onClick={() => setCount(count + 1)}>
        点击增加
      </button>
    </div>
  );
}
```

**最小化原则**：尽可能使用 **Server Component** 来处理数据和渲染，只在需要用户交互（如计数器、展开/折叠菜单）时，才将该部分 UI 封装成 **Client Component**。这样做能最大限度地提高应用的性能和 SEO。

#### 8. 状态管理 (`useState`)

**用途**：用于在 **Client Component** 中添加**状态 (State)**，即随着时间或用户操作而变化的数据。

**副作用**：编程中的“副作用”不是指不好的作用，而是指改变了组件自身作用域以外的外部世界的行为（如网络请求、DOM 操作）。`useEffect` 就是用来处理这些副作用的 Hook。

**语法与解构**：`useState(initialValue)` 返回一个包含两个元素的数组。

| **数组索引** | **变量名** | **作用描述**                       | **对应到 useState(0) 的返回值** |
| -------- | ------- | ------------------------------ | ------------------------ |
| `[0]`    | 当前状态值   | 存储组件当前的状态数据。                   | 你传入的参数（第一次渲染时是 `0`）。     |
| `[1]`    | 更新状态的函数 | 一个专门用来更新状态并通知 React 重新渲染组件的函数。 | 一个函数（`setItemCount`）。    |

``` JavaScript
const [itemCount, setItemCount] = useState(0); 
// itemCount 接收了传入的初始值 0 (或更新后的值)
// setItemCount 接收了那个用于更新状态的函数
```

### 三、 高级功能与优化 (高级水平)

#### 9. 动态路由 (Dynamic Routing)

用于创建灵活的 URL 路径（例如文章详情页）。

- **普通动态段**：使用方括号 `[]`。
    - 结构：`app/blog/[postId]/page.js`
    - 获取参数：`params.postId`

- **多重动态段**：通过嵌套文件夹实现。
    - 结构：`app/news/[city]/[category]/page.js`
    - 获取参数：`params.city` 和 `params.category`

- **可选动态段/捕获所有**：使用双层方括号和三点 `[[...slug]]`。
    - **作用**：允许路径段**可选**或**捕获所有后续路径**为一个数组。
    - **获取参数**：假设目录结构为`app/docs/[[...slug]]/page.js`
        - 访问 `/docs` 时，`params.slug` 是 **`undefined`**。
        - 访问 `/docs/api/v1/auth` 时，`params.slug` 是数组 `['api', 'v1', 'auth']`。你可以使用数组解构 `const [a, b, c] = params.slug;` 来获取这些值。

#### 10. 错误处理与加载界面

用于提升用户体验，避免白屏。

- **加载中 (`loading.js`)**：
    - **作用**：在同级或子级 `page.js` **数据加载期间**自动显示，提供即时反馈。
    - **类型**：**Client Component**。

- **错误 (`error.js`)**：
    - **作用**：捕获同级及子级组件抛出的错误。
    - **隔离机制**：`error.js` 仅替换出错的子级内容，**父级布局（如导航栏）保持可见**，提高应用健壮性。
    - **必须是 Client Component** ：因为它需要处理用户交互 (`onClick` 事件) 并提供 **`reset`** 函数，而这些功能都依赖于客户端环境。

- **优先级**：从内向外查找，**只使用最近的那一个** `loading.js`或`error.js` 文件来处理。

#### 11. 高级数据获取：缓存与重验证

通过扩展 `fetch` API 来控制数据的速度和新鲜度。

- **默认/静态缓存 (SSG 模式)**：**永久缓存**。数据只在构建时或首次请求时获取一次，除非重新部署，否则不会再次获取。适用于几乎不变的数据。

``` JavaScript
fetch('API_URL'); 
// ⚠️ 默认行为，等同于 { cache: 'force-cache' }
```

- **实时数据 (SSR 模式)**：禁用缓存，强制每次获取最新数据。

``` JavaScript
fetch('API_URL', { cache: 'no-store' });
```

- **定时更新 (ISR 模式)**：使用 **`revalidate`** 实现增量静态再生。

``` JavaScript
fetch('API_URL', { next: { revalidate: 3600 } }); 
// 缓存 3600 秒（1小时），过期后在后台重新获取，保持高性能。
```

### Change log

2025-12-7 15:54:37 创建初稿
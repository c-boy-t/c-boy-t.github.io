---
title: Vue3 和 Ajax 入门笔记
date: 2026-05-06 20:18:29
tags:
  - Vue3
  - Ajax
  - 前端
categories:
  - 学习笔记
index_img: /img/3.png
description: 本文记录了 Vue3 的核心概念、常用指令、生命周期、组件编写风格（组合式 API / 选项式 API）、响应式原理与数据定义规范，以及 Ajax 异步交互与 Axios 的使用方法。
---

# Vue3 和 Ajax 入门

> 本文是我学习 Vue3 和 Ajax 过程中的笔记总结，内容涵盖 Vue3 的核心概念、常用指令、生命周期、组件编写风格、响应式原理，以及基于 Axios 的异步请求方法。希望能帮助到同样在入门阶段的小伙伴。

---

## Vue3 基础

### 什么是 Vue？

- Vue 是一款用于**构建用户界面**的**渐进式** **JavaScript 框架**。
- [官方文档](https://cn.vuejs.org/)
- **框架**：一套完整的**项目解决方案**，用于快速构建项目。
- **优点**：大大**提升**前端项目的**开发效率**。
- **缺点**：需要理解记忆框架的使用规则（参照官网即可）。

### Vue 快速入门

1. **引入 Vue 模块**（官方提供）
2. **创建 Vue 应用实例**，控制 HTML 元素
3. **准备被 Vue 控制的 div 元素**

**核心概念：数据驱动视图**

- 准备数据
- 通过**插值表达式** `{{ }}` 渲染页面  
  ⚠️ 注意：插值表达式**不能**用在标签内部（例如作为属性值）。

---

## ⚙️ Vue 常用指令

指令：HTML 标签上带有 `v-` 前缀的特殊属性，不同的指令实现不同的**功能**。

| 指令                            | 说明                                          |
| ------------------------------- | --------------------------------------------- |
| `v-for`                         | 列表渲染，遍历容器元素或对象属性              |
| `v-bind` (简写 `:`)             | 动态绑定 HTML 标签属性，如 `href`、`style` 等 |
| `v-if` / `v-else-if` / `v-else` | 条件渲染（元素会被添加/移除）                 |
| `v-show`                        | 条件展示（通过切换 `display` 属性）           |
| `v-model`                       | 表单元素上的双向数据绑定                      |
| `v-on` (简写 `@`)               | 绑定事件监听器                                |

### `v-for` 详解

```vue
<tr v-for="(item, index) in items" :key="item.id">
  {{ item }}
</tr>
```

- `items`：要遍历的数组
- `item`：当前遍历的元素
- `index`：索引（从 0 开始，可省略）
- `:key`：唯一标识，帮助 Vue 高效复用和排序
  ✅ 推荐使用 `id` 作为 key
  ❌ 不推荐使用 `index`（会变化，导致渲染问题）

###  `v-bind`（简写 `:`）

动态绑定属性值：

```
<img :src="imageUrl" />
<a :href="link">官网</a>
```

### `v-if` vs `v-show`

| 特性 | `v-if`                        | `v-show`                |
| :--- | :---------------------------- | :---------------------- |
| 原理 | 创建/移除 DOM 元素            | 切换 `display` CSS 属性 |
| 场景 | 不频繁切换（如首次加载判断）  | 频繁切换（如折叠面板）  |
| 注意 | 可配合 `v-else-if` / `v-else` | -                       |

###  `v-model`（双向绑定）

```html
<input v-model="username" />
```

- 变量必须在 `data` 中声明
- 自动同步表单值与数据

### `v-on`（简写 `@`）

```html
<button @click="handleClick">点击</button>
```

- 选项式API：`methods` 中的函数通过 `this` 访问 `data` 中的数据。

## Vue 生命周期

生命周期：一个对象从创建到销毁的整个过程。Vue 提供了 8 个阶段对应的**钩子函数**（自动调用）：

| 阶段钩子        | 阶段说明   |
| :-------------- | :--------- |
| `beforeCreate`  | 创建前     |
| `created`       | 创建后     |
| `beforeMount`   | 挂载前     |
| `mounted`       | 挂载后     |
| `beforeUpdate`  | 数据更新前 |
| `updated`       | 数据更新后 |
| `beforeUnmount` | 组件销毁前 |
| `unmounted`     | 组件销毁后 |

> **典型应用**：在 `mounted` 钩子中发起异步请求，加载数据并渲染页面。

## Vue 组件编写风格与响应式原理

### 两种组件编写风格：组合式 API vs 选项式 API

Vue 组件有两种不同的编写风格：**组合式 API（Composition API）** 和 **选项式 API（Options API）**。

#### 组合式 API（推荐 Vue 3 使用）

组合式 API 是 Vue 3 提供的一种**基于函数**的组件编写方式，通过**使用函数来组织和复用组件的逻辑**。它提供了更灵活、更可组合的代码组织方式。

```vue
<!-- 组合式 API 写法 -->
<script setup>
import { ref, onMounted } from 'vue';

const count = ref(0); // 声明响应式变量

function increment() { // 声明函数
  count.value++;
}

onMounted(() => { // 声明钩子函数
  console.log('Vue Mounted ...');
});
</script>

<template>
  <button @click="increment">count: {{ count }}</button>
</template>
```

- **`setup`**：是一个标识，告诉 Vue 需要进行一些处理，让我们可以更简洁地使用组合式 API。
- **`ref()`**：接收一个内部值，返回一个响应式的 ref 对象，此对象只有一个指向内部值的属性 `.value`。
- **`onMounted()`**：组合式 API 中的生命周期钩子，注册一个回调函数，在组件挂载完成后执行。
- **优点**：定义的响应式数据和函数不需要包裹在某个属性（如 `data()`、`methods`）之内；逻辑复用更加方便；没有 `this` 对象，所有响应式数据和函数都需要显式导入并使用。。

#### 选项式 API（Vue 2 经典写法）

选项式 API 使用包含多个选项的对象来描述组件的逻辑，如 `data`、`methods`、`mounted` 等。选项定义的属性都会暴露在函数内部的 `this` 上，它会指向当前的组件实例。

```vue
<template>
  <button @click="increment">count: {{ count }}</button>
</template>

<script>
export default {
  data() {
    return {
      count: 0
    };
  },
  methods: {
    increment() {
      this.count++;
    }
  },
  mounted() {
    console.log('Vue Mounted ...');
  }
};
</script>
```

### Vue 3 响应式原理：`ref` vs `reactive`

| 特性         | `ref`                                         | `reactive`                                   |
| :----------- | :-------------------------------------------- | :------------------------------------------- |
| **适用类型** | 基本类型（string、number、boolean）或简单对象 | 复杂对象、数组                               |
| **访问方式** | 脚本中需要用 `.value`，模板中自动解包         | 直接访问属性，无需 `.value`                  |
| **替换能力** | 可以整体替换（`obj.value = newObj`）          | 整体替换会丢失响应式（需用 `Object.assign`） |

**实际项目中的经验选择**：

- 列表数据（如 `deptList`）、表单对象（如 `deptForm`）、状态标志（如 `showDialog`）**通常使用 `ref`**，因为它们经常被整体替换（例如 `deptList.value = result.data`）。
- 如果是一个深层次嵌套且不会整体替换的复杂对象，可以使用 `reactive`。

Vue 官方 + 社区主流：优先用 ref！

#### 响应式数据定义规范（推荐）

按用途选择最合适的 `ref` 初始值，让代码语义清晰，避免 Vue 响应式异常：

| 用途                        | 推荐写法  | 说明                              |
| :-------------------------- | :-------- | :-------------------------------- |
| 列表 / 数组（员工、部门等） | `ref([])` | 便于整体赋值 `list.value = [...]` |
| 查询表单对象                | `ref({})` | 便于整体替换表单数据              |
| 单个字符串（如输入框值）    | `ref("")` | 简单清晰                          |
| 数字（如计数器、页码）      | `ref(0)`  | 保证类型安全                      |

> **重要规则**：
>
> - **script 中必须写 `.value`**（`count.value++`）
> - **template 中不用写 `.value`**（`{{ count }}`）

#### “宽松”赋值规则

JavaScript 允许将“多属性对象”赋值给“少属性对象”变量。例如：

```javascript
// deptForm 初始化为 { name: '' }
deptForm.value = result.data;
```

赋值后，`deptForm.value` 会自动拥有 `result.data` 中的所有属性（包括 `id`、`updateTime` 等），且**保持响应式**。这是 Vue 3 响应式系统的特点，无需手动扩展。

## Ajax 与异步交互

### 什么是 Ajax？

- **全称**：Asynchronous JavaScript and XML（异步的 JavaScript 和 XML）
- **作用**：
  1. **数据交换**：向服务器发送请求，获取响应数据。
  2. **异步交互**：**不重新加载整个页面**的情况下，更新部分网页（如搜索联想、用户名校验）。

### Axios 库

Axios 对原生 Ajax 进行了封装，简化书写，快速开发。[官网链接](https://www.axios-http.cn/)

#### 基本步骤

1. 引入 Axios 的 JS 文件
2. 使用 Axios 发送请求，获取响应结果

**基于Axios发送异步请求**

- 格式：axios.请求方式(url [,data [,config]]) 
- Axios 常用请求方式（GET / POST / PUT / DELETE）
- 示范如下

1. GET（获取数据）

```javascript
// 带参数
const res = await axios.get('/api/user', {
  params: { id: 1, name: '张三' }
})
```

2. POST（提交数据）

```javascript
// 因为 POST 必须传请求体 data，Axios 为了方便，直接把第二个参数设计成 data，这就是语法糖
// 语法糖 = 懒人简写语法，只是帮你少写代码，底层逻辑不变，没有新增功能。
const res = await axios.post('/api/user', {
  username: 'test',
  password: '123456'
})
```



**async /await 写法：**

```javascript
// 1. 先导入 axios
import axios from 'axios'

// 2. 写在 async 函数里
async function fetchData() {
  try {
    // 发送请求 → 等待结果
    const res = await axios.get('https://api.example.com/data')
    
    // 成功：拿到数据
    console.log('请求成功', res.data)
  } 
  catch (error) {
    // 失败：捕获错误
    console.error('请求失败', error)
  }
}

// 调用
fetchData()
```

## 常见问题：如何在页面加载后自动发起请求？

✅ **答案**：在 Vue 应用实例中，创建一个与 `data` 同级的 `mounted()` 方法（选项式 API）或使用 `onMounted()`（组合式 API）。

**选项式 API 示例**：

```vue
<script>
export default {
  data() {
    return { list: [] };
  },
  mounted() {
    axios.get('/api/list').then(res => {
      this.list = res.data;
    });
  }
};
</script>
```

**组合式 API 示例**：

```vue
<script setup>
import { ref, onMounted } from 'vue';
import axios from 'axios';

const list = ref([]);

onMounted(async () => {
  const res = await axios.get('/api/list');
  list.value = res.data;
});
</script>
```

## 补充知识点：JavaScript 分号机制 (ASI)

在 JavaScript 中，分号 (`;`) 用于终止语句。但 JS 引擎有一个 **自动分号插入（Automatic Semicolon Insertion，ASI）** 机制：当解析器遇到换行且语句可以合法结束时，会自动补充分号。

```javascript
// 以下代码不会报错（ASI 自动插入分号）
function hello() {
  return
  'hello'
}
// 实际返回 undefined，因为 return 后自动加了分号，'hello' 变成了独立语句

// 如果把多个语句写在同一行，必须用分号分隔！
const a = 1; const b = 2; console.log(a + b);

// 主流规范推荐不加分号
import { ref } from 'vue'
export const name = "张三"

```

> [!TIP]
>
> ### 推荐的代码风格（新手友好）
>
> 作为新手，不用纠结 “极致精简”，遵循以下规则即可：
>
> 1. **所有独立语句末尾都加分号**（如变量声明、函数调用、赋值语句）；
> 2. **代码块 `{}` 后不加**（if/for/ 函数体后）；
> 3. **`return/break/continue` 后如果换行，务必把内容和关键字写在一行**；

**最佳实践**：

- **显式加上分号**，避免 ASI 导致的意外行为（尤其是 return、break、continue 等关键字后跟换行时）。
- 团队统一风格即可，但推荐使用分号提高代码可读性。

## 总结

- Vue3 通过**指令**和**数据驱动**极大提升了前端开发效率。
- **生命周期钩子**（特别是 `mounted` / `onMounted`）是实现异步数据加载的关键。
- **组合式 API** 是 Vue 3 推荐的组件编写方式，更灵活、更利于逻辑复用。
- **响应式数据**应选择合适的 API (`ref` / `reactive`) 和初始值，保持代码清晰可靠。
- **Ajax + Axios** 使得前后端交互变得简洁、高效。

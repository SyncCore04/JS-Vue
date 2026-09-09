# JS & Vue 学习笔记

> 学习路径：JavaScript 基础 → DOM 与事件 → Vue 3 → Axios 异步交互
> 案例主线：「Tlias 智能学习辅助系统 · 员工列表」贯穿全程，从纯 JS 实现一步步演进到 Vue + 异步数据加载。
> 对应文件：01 ~ 16 号 HTML 练习 + `js/` 目录（demo.js、eventDemo.js、utils.js、axios.js、vue.esm-browser.js）

---

## 一、JS 引入方式（01）

两种方式：

```html
<!-- 1. 内嵌式：写在 <script> 标签中 -->
<script>
  alert('Hello world');
</script>

<!-- 2. 外部式：通过 src 引入 .js 文件 -->
<script src="./js/demo.js"></script>
```

- `alert()`：弹窗；`console.log()`：F12 控制台输出；`document.write()`：写入页面。

---

## 二、基础语法（02）

### 变量声明

| 关键字 | 特点 | 使用建议 |
|--------|------|----------|
| `var` | 老写法，作用域混乱，可重复声明 | **实际开发不用** |
| `let` | 块级作用域，可重新赋值 | 声明变量的首选 |
| `const` | 常量，声明后不可重新赋值 | 优先使用 |

### 字符串拼接

```js
let name = '张三', age = 18;
console.log('我是' + name + '，我今年' + age + '岁');
```

---

## 三、数据类型与 typeof（03）

JS 是弱类型语言，用 `typeof` 查看类型：

```js
typeof 10        // "number"（整数和小数都是 number）
typeof true      // "boolean"
typeof "Hello"   // "string"（单引号、双引号、反引号都可以）
typeof null      // "object"（历史遗留的坑，null 并不是对象）
typeof a         // "undefined"（变量声明未赋值）
```

⚠️ 记住两个易错点：`typeof null` 返回 `"object"`；未赋值变量是 `"undefined"`。

---

## 四、函数（04）

```js
// 1. 具名函数
function add(a, b) { return a + b; }

// 2. 箭头函数（ES6，推荐）
let add1 = (a, b) => a + b;

// 3. 把函数调用结果赋给变量（本质是赋值返回值，不是"匿名函数"）
let add2 = add(100, 200);
```

> 📌 真正的"匿名函数"是 `let f = function(a,b){...}` 或直接作为回调传入，如 `addEventListener('click', () => {...})`。

---

## 五、自定义对象与 JSON（05）

### 对象字面量

```js
let person = {
  name: '张三',
  age: 18,
  sayName: function() { console.log(this.name); }  // 写法一
};
let person1 = {
  name: '李四',
  sayName() { console.log(this.name); }            // 写法二（ES6 简写，常用）
};
person.sayName();      // 调用方法
console.log(person.age); // 访问属性
```

### JSON 与对象的互相转换

```js
JSON.stringify(person);            // 对象 → JSON 字符串（序列化）
JSON.parse('{"name":"王五"}');      // JSON 字符串 → 对象（反序列化）
```

> 📌 JSON 字符串的 key 必须用**双引号**包裹，这是它和 JS 对象字面量的区别。前后端数据交互就是靠 JSON 序列化/反序列化。

---

## 六、DOM 操作（07）

DOM = 文档对象模型，JS 通过它操作页面元素：

```js
// 1. 定位单个元素（CSS 选择器语法，匹配第一个）
let h1 = document.querySelector('#title1');
h1.innerHTML = '新内容';

// 2. 定位所有元素（返回 NodeList 数组）
let hs = document.querySelectorAll('h1');
hs[1].innerHTML = '第2个标题';   // 下标从 0 开始
```

---

## 七、事件监听（08、10、11）

### 两种绑定方式

```js
// 方式一：addEventListener（推荐，可绑定多个监听器）
btn1.addEventListener('click', () => { console.log('点击了按钮1'); });

// 方式二：on事件属性（多次绑定会覆盖！）
btn2.onclick = () => { console.log('点击了按钮2'); };
```

### 常见事件一览（10）

| 事件 | 触发时机 |
|------|----------|
| `click` | 鼠标点击 |
| `mouseenter` / `mouseleave` | 鼠标移入 / 移出 |
| `keydown` / `keyup` | 键盘按下 / 抬起 |
| `focus` / `blur` | 获得焦点 / 失去焦点 |
| `input` | 用户输入时实时触发 |
| `submit` | 表单提交 |

### 案例：员工表格隔行换色 / 鼠标高亮（09）

```JavaScript
const rows = document.querySelectorAll('tr');
for (let i = 0; i < rows.length; i++) {
  rows[i].addEventListener('mouseenter', function() {
    this.style.backgroundColor = '#f2e2e2';   // 鼠标进入变色
  });
  rows[i].addEventListener('mouseleave', function() {
    this.style.backgroundColor = '#fff';      // 鼠标离开还原
  });
}
// 等价写法：rows.forEach(row => { ... })
```

### 模块化拆分（11）

事件代码抽到外部 `js/eventDemo.js`，通过 ES Module 导入导出：

```js
// utils.js —— 导出
export function printLog(msg) { console.log(msg); }

// eventDemo.js —— 导入
import { printLog } from "./utils.js";
```

```html
<script src="./js/eventDemo.js" type="module"></script>
```

⚠️ **坑**：`type="module"` 受浏览器同源策略限制，直接双击打开 HTML 文件会报 CORS 错误，**必须通过 HTTP 服务器访问**（如 VSCode 的 Live Server）。

---

## 八、Vue 3 快速入门（12）

Vue 是响应式前端框架：**数据变了，页面自动跟着变**，不用再手动操作 DOM。

```html
<div id="app">
  <h1>{{ msg }}</h1>          <!-- 插值表达式 -->
  <p>当前计数: {{ count }}</p>
</div>

<script type="module">
  import { createApp } from 'https://unpkg.com/vue@3/dist/vue.esm-browser.js'

  createApp({
    data() {                 // 定义响应式数据
      return { msg: 'hello vue', count: 100, name: '张三' }
    }
  }).mount('#app')           // 挂载到 #app 容器
</script>
```

---

## 九、Vue 常用指令（13）

以员工列表案例为载体，四大常用指令：

### 1. `v-model` —— 表单双向绑定

数据变 → 表单变；表单输入 → 数据也变。

```html
<input type="text" v-model="searchForm.name">
```

查询条件封装在 `data` 的 `searchForm` 对象中，F12 的 Vue 插件可直接查看其值。

### 2. `v-on` / `@` —— 事件绑定

```html
<button v-on:click="search">查询</button>
<button @click="clear">清空</button>   <!-- @ 是 v-on: 的简写 -->
```

方法写在 `methods` 中，方法内通过 `this.searchForm` 访问数据。

### 3. `v-bind` / `:` —— 属性绑定

```html
<a :href="logoutUrl">退出登录</a>
<img :src="item.image">
```

> 📌 插值表达式 `{{ }}` **只能用在标签体中，不能出现在标签属性内部**——属性里要用 `v-bind`。

### 4. `v-for` —— 列表渲染

```html
<tr v-for="(e, index) in empList" :key="e.id">
  <td>{{ index + 1 }}</td>
  <td>{{ e.name }}</td>
</tr>
```

`:key` 绑定唯一 id，帮助 Vue 高效更新列表。

### 5. `v-if` / `v-show` —— 条件渲染

```html
<!-- v-if：条件为假时元素直接不存在（DOM 里没有） -->
<span v-if="e.job == 1">班主任</span>
<span v-else-if="e.job == 2">讲师</span>
<span v-else>其他</span>

<!-- v-show：通过 display:none 控制显示隐藏，元素一直存在 -->
<span v-show="e.job == 1">班主任</span>
```

**选型**：不频繁切换用 `v-if`（本案例场景）；频繁切换显示/隐藏用 `v-show`（省去反复创建销毁的开销）。

---

## 十、Axios 与异步交互（14、15）

Axios 是 Ajax 请求库，用于向服务器异步发请求、拿数据。

### 通用写法（14）

```js
axios({
  url: 'https://mock.apifox.cn/m1/.../emps/list',
  method: 'GET'
}).then(result => {
  console.log(result.data);   // 响应数据在 result.data 中
}).catch(err => {
  console.log(err);
});
```

### 请求方式别名（15，更简洁）

```js
// GET
axios.get('.../emps/list').then(result => { ... }).catch(err => { ... });

// POST（第二个参数是请求体数据）
axios.post('.../emps/update', { id: '01' }).then(result => { ... });
```

### ⚠️ 关键概念：异步

```js
axios.get(url).then(result => console.log(result.data));
console.log('GET请求发送完成');   // ⬅ 这行先输出！
```

axios 请求是**异步**的：浏览器不会卡在这等响应，而是先继续往下执行，等服务器返回后再执行 `then()` 里的回调。所以 `then` 外面的代码先打印。

---

## 十一、终极案例：Vue + Axios 完整员工列表（16）

前面所有知识点在这里汇合——页面加载时自动从服务器拉取数据，表单输入即可按条件查询：

```js
createApp({
  data() {
    return {
      searchForm: { name: '', gender: '', job: '' },  // 查询条件
      empList: []                                      // 表格数据
    }
  },
  methods: {
    async search() {
      // await 等待异步结果，代码写法像同步一样直观
      let result = await axios.get(
        `https://web-server.itheima.net/emps/list?name=${this.searchForm.name}&gender=${this.searchForm.gender}&job=${this.searchForm.job}`
      );
      this.empList = result.data.data;   // 数据一变，表格自动刷新
    },
    clear() {
      this.searchForm = { name: '', gender: '', job: '' };
      this.search();
    }
  },
  mounted() {          // 钩子函数：页面加载完成后自动执行
    this.search();
  }
}).mount('#container')
```

对应模板（摘要）：

```html
<tr v-for="(e, index) in empList" :key="e.id">
  <td>{{ index + 1 }}</td>
  <td>{{ e.name }}</td>
  <td>{{ e.gender == 1 ? '男' : '女' }}</td>
  <td><img class="avatar" v-bind:src="e.image" :alt="e.name"></td>
  <td>
    <span v-if="e.job == 1">班主任</span>
    <span v-else-if="e.job == 2">讲师</span>
    <span v-else>其他</span>
  </td>
</tr>
```

**两个新知识点：**
1. `async / await`：用同步的写法处理异步请求，避免回调嵌套；`await` 只能用在 `async` 函数中。
2. `mounted()` 钩子：Vue 实例挂载完成后自动调用，是**首屏加载数据的标准位置**。

---

## 十二、案例演进主线（回顾）

同一个「员工列表」页面，四轮迭代，体现技术栈升级的价值：

| 版本 | 文件 | 技术手段 | 痛点 |
|------|------|----------|------|
| ① 静态页面 | 06 | 纯 HTML+CSS，数据写死 | 数据硬编码，无法交互 |
| ② JS 增强 | 09 | DOM + 事件（隔行换色、鼠标高亮） | 手动操作 DOM，代码繁琐 |
| ③ Vue 版 | 13 | v-model / v-for / v-if / @click，数据驱动视图 | 数据仍写死在前端 |
| ④ 异步版 | 16 | Axios + async/await + mounted 钩子 | ✅ 数据来自服务器，完整闭环 |

**核心心智转变**：从「拿到元素 → 改它」（DOM 思维）变成「改数据 → 页面自动更新」（Vue 响应式思维）。

---

## 十三、易错点与经验汇总

1. **`var` 不用，用 `let` / `const`**；`typeof null === 'object'` 是历史遗留坑。
2. **`onclick` 多次绑定会覆盖**，用 `addEventListener` 可叠加。
3. **`type="module"` 必须走 HTTP 服务器**，双击打开文件会因 CORS 报错。
4. **插值表达式 `{{}}` 不能写在标签属性里**，属性绑定用 `:attr`。
5. **axios 是异步的**：`then` 之后的同步代码先执行；要"看起来同步"就用 `async/await`。
6. **JSON 字符串 key 必须双引号**；`JSON.stringify` / `JSON.parse` 是对象与 JSON 互转的两把钥匙。
7. **`v-if` vs `v-show`**：前者移除 DOM 节点，后者只是 `display:none`；频繁切换用后者。
8. **首屏数据加载放 `mounted()` 钩子**里，不要写在 `data()` 中。

---

*整理于 2026-09-10，基于 D:\JavaWeb\study\JS-Vue 目录下 01~16 号练习文件。*

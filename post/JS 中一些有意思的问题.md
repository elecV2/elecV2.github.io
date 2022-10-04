### 暂时性死区

``` JS
// #1
console.log(typeof value);

// #2
console.log(typeof value);
let value = 2;  // const value = 2;

// #3
console.log(typeof value);
var value = 2;
```

### 符点型运算

``` JS
console.log(0.1 + 0.2)

console.log(1 + 1e20 - 1e20)
console.log(1 +(1e20 - 1e20))
```

### 其他

``` JS
// # 1
8.toString(2)
(8).toString(2)

// # 2
new Promise((a,b)=>{
  b(1);
  console.log(2);
  a(3);
  console.log(4);
}).then(console.log).catch(console.log)
// 输出结果

// # 3
for (let i=0;i<3;i++) {}
for (let i=0;i<3;i+1) {}

// # 5
let n = [1]
if (1 in n) {
  console.log(true)
} else {
  console.log(false)
}
console.log(0 in n)

// ?. ??
const e = {e:2}
console.log(e?.l??e?.e)
```

### API 相关

``` JS
// window.open vs a.click()
```

### 优化建议（maybe

函数传入参数事先进行处理。比如

``` JS
function etest(a=>typeof a === 'string' ? a : String(a)) {
  console.log(a, 'typeof :', typeof a)
}

etest(12389)
etest('345')
// typescript ? 否，typescript 只会报错
// 此种模式下方便统一传入参数类型
// 需修改 JS 底层代码让些函数运行
```

### 问题记录

sw preload 问题，打开新标签或刷新页面时会出现错误信息：The service worker navigation preload request was cancelled before 'preloadResponse' settled. If you intend to use 'preloadResponse', use waitUntil() or respondWith() to wait for the promise to settle.


### CSS 相关

- 滚动条是否显示的问题

body overflow auto
子元素 z-index

### 碎碎念 - 一些可能过激的言论

- 优化是没有尽头的
- 所谓的规则、逻辑、模式，不是为了限制你，而是为了帮助你
- 所有语言的终点的 JS，所有数据类型的终点是 JSON
- 没有坏的函数（代码），只有坏的用法

一个不能自动对数据类型进行判断的语言，绝对不能称之为高级语言，或智能语言，甚至可以叫做弱智语言，或垃圾语言。比如：我给你一个帽子或大衣，你需要知道帽子的尺寸，存放时间，甚至颜色和价格等其他数据，才能知道存放到哪里，那么只能说这个衣帽间还停留在初始阶段，毫无智能可言。

用浏览器是为了不安装应用，而网页老是弹出安装的界面。 - 关于 PWA

Service workers 将成为浏览器上最大的毒瘤。  - sw 注入可实现任意网页的 MITM，而用户基本无察觉
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
8.toString(2)
(8).toString(2)

new Promise((a,b)=>{
  b(1);
  console.log(2);
  a(3);
  console.log(4);
}).then(console.log).catch(console.log)
// 输出结果
```

### 后话

**所谓的规则、逻辑、模式，不是为了限制你，而是为了帮助你。**

所有语言的终点的 JS，所有数据类型的终点是 JSON。

一个不能自动对数据类型进行判断的语言，绝对不能称之为高级语言，或智能语言，甚至可以叫做弱智语言，或垃圾语言。比如：我给你一个帽子或大衣，你需要知道帽子的尺寸，存放时间，甚至颜色和价格等其他数据，才能知道存放到哪里，那么只能说这个衣帽间还停留在初始阶段，毫无智能可言。
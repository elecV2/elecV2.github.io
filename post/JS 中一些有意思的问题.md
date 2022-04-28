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
new Promise((a,b)=>{
  b(1);
  console.log(2);
  a(3);
  console.log(4);
}).then(console.log).catch(console.log)
// 输出结果
```
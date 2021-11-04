```
一个思路，不一定对，欢迎探讨。
```

## 简述

假如有一个简单的 array 数组，比如 [1, 2, 3, 5, 8, ...]，在 vue 中通过 v-for 的方式呈现，该数组需要进行频繁的增删操作，问：如何合理设置 :key 值？

## 分析

需要给每个元素设置一个独一无二的的 **key**，为了简化，直接使用原数组的 index，但在删除时保留之前的 index，防止 key 值变动。

可以把原始数组元素分为 data 和 index 两项，用一个变量进行保存。进一步，增加一个参数 status 表示当前元素是否更改（增/删/改）。

``` JS
const lists = [1, 2, 3, 5, 8, 10]
const orgli = lists.map((data, index)=>({ data, index, status: 0 }))

// key 值可以使用一个关键值+index，比如 'lists1'
// 如果不想使用 index，也可以设置其他任意随机数

// status 表示元素是否有更改。（方便通过 computed filter 对应项
// 0:  无更改
// 1:  新增元素
// 2:  修改元素
// -1: 删除元素
```

### 删除及添加

删除时直接修改 orgli 对应项的 status 即可。原来的数据继续保留
新增元素时，直接使用 orgli.push() 函数。

``` JS
// 删除元素
orgli[2].status = -1
// orgli[2].data = null

// 新增元素
orgli.puhs({ data: 4, index: orgli.length, status: 1 })
```

## 具体使用

```
// 示例代码，仅供参考
<li v-for="{data, index} in compli" :key="'lists' + index">
  <span>{{ data }}</span>
  <span @click="$set(orgli[index], 'status', -1)">X</span>
</li>
<li @click="orgli.push({ data: '', index: orgli.length, status: 1 }">新增</li>

<script>
data(){
  return {
    lists: [1, 2, 3, 5, 8, 10],        // 在实际使用中这个数据可能是异步获取的，无需保留
    orgli: this.lists.map((data, index)=>({ data, index, status: 0 }))
  }
},
computed: {
  comli(){
    return this.orgli.filter(li=>li.status !== -1)
  }
}
</script>
```

## 优缺点

优点：

- 每一项都有独一无二的 key（主要目的
- 可保留数组原始值
- 方便查看增删改的元素

缺点：

- 占用稍多一点内存
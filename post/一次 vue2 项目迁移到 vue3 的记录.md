### 前言

记录怎么把一个 vue2.0 项目升级到 vue3.0，只是某个特定项目的一次升级尝试，仅供参考。

*原项目并没有使用 vue-cli 脚手架，而是原生的 webpack。*

### 准备工作

1. 把原 vue2.0 项目文件复制到新文件夹
2. 修改 package.json 和 webpack config 文件
  - 根据 https://v3-migration.vuejs.org/migration-build.html 修改配置文件
  - 完成后执行 yarn 或 npm install 命令安装依赖
3. 然后执行 webpack 命令 

### 出现的错误

- the >>> and /deep/ combinators have been deprecated. Use :deep() instead.
- .sync modifier for v-bind has been removed. Use v-model with argument instead.
- Platform-native elements with "is" prop will no longer be treated as components in Vue 3 unless the "is" value is explicitly prefixed with "vue:".
- Vue.prototype

### 一些难题

- 子组件使用了 **Vue.prototype**
- 使用了第三方 vue2.0 组件
```
更新日期: 2021-06-10

本文并非 puppeteer 使用教程，只是本人学习的一些记录。避免踩坑，以及方便以后复习。
```

## 安装篇

首先简单说明一下本人的使用环境:
- 系统 win10 x64
- Node v15.6.0
- yarn v1.22.5

关联测试项目: [elecV2P](https://github.com/elecV2/elecV2P)。即：以下所有命令是在该项目的目录下执行的

按照 [puppeteer](https://github.com/puppeteer/puppeteer) 官方教程直接使用下面的命令进行安装。

``` sh
yarn add puppeteer
```

然后执行出现以下错误

```
Could not find expected browser (chrome) locally. Run `npm install` to download the correct Chromium revision (884014).
```

最后直接在网站 https://download-chromium.appspot.com/ 下载了最新最新的 chromium，并解压到目录 puppeteer/.local-chromium

最终目录结构: puppeteer/.local-chromium/win64-884014/chrome-win


## 简单运行

waitUntil 有以下值可选：

load: 当 load 事件触发时;
domcontentloaded: 当 DOMContentLoaded 事件触发时;
networkidle0: 页面加载后不存在 0 个以上的资源请求，这种状态持续至少 500 ms;
networkidle2: 页面加载后不存在 2 个以上的资源请求，这种状态持续至少 500 ms。
*所需环境：nodejs (平台不限)*

## 简述

``` sh
git clone https://github.com/elecV2/elecV2P  // 或者手动下载解压
cd elecV2P    // cmd 进入项目目录

yarn
yarn start    // or npm install --production && npm start
```

然后打开后台管理页面：**http://127.0.0.1/**

在 JSMANAGE 页面添加 Cookie

![](https://raw.githubusercontent.com/elecV2/elecV2P-dei/master/examples/res/cookieadd.png)

然后在 TASK 页面设置运行时间及 JS 地址，点击运行即可。

![](https://raw.githubusercontent.com/elecV2/elecV2P-dei/master/examples/res/taskrun.png)

** 测试脚本为 [NobyDa](https://github.com/NobyDa) 的 https://raw.githubusercontent.com/NobyDa/Script/master/JD-DailyBonus/JD_DailyBonus.js*

## 详细说明

关于 nodejs 的安装，根据使用平台搜索相关教程吧，通常几条命令就搞定了。如果是 Linux 平台，推荐同时安装 Git。

然后下载 [elecV2P](https://github.com/elecV2/elecV2P) 项目文件。如果已安装好 Git，直接通过 git clone 命令获取，如果没有，可以通过浏览器打开项目地址，打包下载整个项目，再解压即可。

获取到项目文件后，在 shell 窗口进入 elecV2P 目录，执行命令 **yarn** 或者 **npm install**，安装项目依赖库。这一步可能需要稍微等待一下，等待时长由网络环境和机器性能决定。

安装完成后，执行命令 **yarn start** 或者 **npm start**，启动程序。然后就可以打开后台管理页面，添加 cookie 和定时任务了。

管理页面默认使用 **80** 端口。如使用 **yarn dev** or **npm dev** 命令启动程序，则默认端口为 **12521**。

如需使用其他端口，可以在 package.json 或 config.js 中进行手动修改。修改后，重启服务，即可通过新端口进行访问。

** * 如在 VPS 等远程服务器中运行，请不要随意暴露 IP 地址及端口**
** * 如果当时服务器是其他时区，cron 定时应进行相应调整**

查看运行日志：http://127.0.0.1/logs/all

获取通知信息：http://127.0.0.1/feed

更多关于通知的设置参考：https://github.com/elecV2/elecV2P-dei/tree/master/docs/07-feed&notify.md

如使用 Docker, 参考：https://github.com/elecV2/elecV2P-dei/tree/master/docs/02-Docker.md

### 其他参考说明

elecV2P 完整说明文档：https://github.com/elecV2/elecV2P-dei

> 挖个坑，以后有时间，再写一个通过代理端口 + 脚本实现自动获取 cookie 的教程。
## 下载

安装包地址：[https://github.com/coreybutler/nvm-windows/releases](https://github.com/coreybutler/nvm-windows/releases)

下载地址提供了两种安装包：

-   nvm-noinstall.zip：绿色免安装版，但使用时需进行配置。
-   nvm-setup.zip：安装版


# 配置

进入nvm安装路径，打开settings.txt，添加淘宝镜像url：

```
node_mirror: https://npm.taobao.org/mirrors/node/
npm_mirror: https://npm.taobao.org/mirrors/npm/
```

# 执行命令

- nvm list
- nvm list available
- nvm install [version]
- nvm use [version]

# 错误排查
1. 执行use报错
![](https://blog-1258875084.cos.ap-guangzhou.myqcloud.com/picgo-levono/202210112240964.png)
解决：
使用管理员身份打开cmd
![a|500](https://blog-1258875084.cos.ap-guangzhou.myqcloud.com/picgo-levono/202210112250520.png)


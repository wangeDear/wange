## Icarus主题配置

[Icarus用户指南 - 主题配置 - Icarus (ppoffice.github.io)](https://ppoffice.github.io/hexo-theme-icarus/Configuration/icarus%E7%94%A8%E6%88%B7%E6%8C%87%E5%8D%97-%E4%B8%BB%E9%A2%98%E9%85%8D%E7%BD%AE/)

## hexo-enhancer插件重复目录问题

[关于产生重复categories的问题 · Issue #18 · sisyphsu/hexo-enhancer (github.com)](https://github.com/sisyphsu/hexo-enhancer/issues/18)

## ~~托管服务~~
https://vercel.com/

## 配置webhook实现提交自动发布

### github webhook配置

![](https://blog-1258875084.cos.ap-guangzhou.myqcloud.com/picgo-eve202209290907914.png)

### Vecel配置自动deploy webhook

![](https://blog-1258875084.cos.ap-guangzhou.myqcloud.com/picgo-eve202209290909168.png)


## 提交git命令

```shell
git commit -a -m "webhook"
git push -u origin main
```

### Git http配置免密码

#### 增加远程地址的时候带上密码也是可以的。(推荐)
```
# githu不能使用，参考官方gh cli
http://yourname:password@git.oschina.net/name/project.git
```

github不支持，需要使用gh cli，具体查看官方文档。

## hexo实现obsidian双链笔记

- hexo-obsidian-link
利用hexo-obsidian-link插件将[[]]转为hexo能识别的{% post_link %}标志，不会对现有日志文件做任何改动。

- hexo-enhancer
自动生成title、categories、date等hexo元标签，避免在日志文件生成多余内容

其他配置依赖如下：
```json
{  
  "name": "hexo-site",  
  "version": "0.0.0",  
  "private": true,  
  "scripts": {  
    "build": "hexo generate",  
    "clean": "hexo clean",  
    "deploy": "hexo deploy",  
    "server": "hexo server"  
  },  
  "hexo": {  
    "version": "6.3.0"  
  },  
  "dependencies": {  
    "hexo": "^6.3.0",  
    "hexo-abbrlink": "^2.2.1",  
    "hexo-auto-excerpt": "^1.1.2",  
    "hexo-backlink": "^0.1.4",  
    "hexo-enhancer": "file:hexo-enhancer",  
    "hexo-generator-archive": "^1.0.0",  
    "hexo-generator-category": "^1.0.0",  
    "hexo-generator-index": "^2.0.0",  
    "hexo-generator-tag": "^1.0.0",  
    "hexo-obsidian-link": "file:hexo-obsidian-link",  
    "hexo-renderer-ejs": "^2.0.0",  
    "hexo-renderer-marked": "^5.0.0",  
    "hexo-renderer-stylus": "^2.1.0",  
    "hexo-server": "^3.0.0",  
    "hexo-submit-urls-to-search-engine": "^2.1.0",  
    "hexo-theme-icarus": "^5.1.0",  
    "hexo-theme-landscape": "^0.0.3"  
  }  
}
```


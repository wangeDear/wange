# linux上tcp抓包

```shell
tcpdump -vvv -i ens192 host 152.136.112.83
```

发现服务端会给客户端发送RST数据包

![](https://blog-1258875084.cos.ap-guangzhou.myqcloud.com/picgo-eve202210170854188.png)

结合实际项目排查**责任链中业务代码处理异常**
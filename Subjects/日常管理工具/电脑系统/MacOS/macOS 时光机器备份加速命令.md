# macOS 时光机器备份加速命令
 
#解决 

[[Subjects/日常管理工具/电脑系统/MacOS/MacOS]]   

macOS 时光机器备份加速命令

如果你真的打算让时光机器全力全速[工作](https://www.iplaysoft.com/tag/工作)，那也是有办法的，就是通过命令行，用命令强制关闭系统对时光机器的限流，俗称“解除封印”。打开终端，输入以下命令：

```
sudo sysctl debug.lowpri\_throttle\_enabled=0
```

这时你就会发现时光机器的备份速度变快很多很多了！！基本能达到网络和硬盘读写的应有的速度了。等它完成了首次的备份之后，你可以再执行下面的命令，恢复到原本限流的状态，以保证日后使用电脑时不被时光机器备份占去太多的资源导致变卡。

```
sudo sysctl debug.lowpri\_throttle\_enabled=1
```

当然了，如果你的时光机器备份是保存在 [NAS](https://www.iplaysoft.com/go/nas) 或[路由器](https://www.iplaysoft.com/go/router)上网络存储的，都推荐连接“网线”进行首次备份，比起 [WiFi](https://www.iplaysoft.com/tag/wifi) 的速度很稳定性都是要高很多。另外，如果你使用 [MacBook](https://www.iplaysoft.com/go/mac) 的话，记得还要接通电源再备份才能获得最好的速度哦。
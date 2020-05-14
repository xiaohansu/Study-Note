# oss对接gb28181，推送rtmp
## 参考链接

https://mp.weixin.qq.com/s/VIPSPaBB5suUk7_I2oOkMw

MAC 环境下无法执行

## 配置摄像头，打开平台对接功能
设置数值，打开服务

##  git下载oss开源
https://github.com/ossrs/srs.git

```bash
git clone https://gitee.com/winlinvip/srs.oschina.git srs &&
cd srs/trunk && git remote set-url origin https://github.com/ossrs/srs.git && git pull
```

## 切换分支到0.4
这里不使用教程推荐的developer分支，makefile异常编译失败
## 编译完成后
修改配置文件`push.gb28181.conf` </br>
主要修改`host`地址，即服务器运行主机地址，且保证摄像设备可以该地址连接（比如在同一个网络）</br>

注意几个数值要和镜头设置一致

## 摄像头接入网络

需要发送注册信息，先启动服务，在将摄像头上电，接入网络

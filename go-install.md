这里是 [Golang 学习教程系列](https://github.com/LeaningGo/go-learn)的第一部分。

##### step 1：获取 golang 二进制文件 
```
wget https://storage.googleapis.com/go1.11.4.linux-amd64.tar.gz
```
第二种途径，直接前往下载
```
https://studygolang.com/dl
```

##### step 2：解压并且创建工作目录
```
tar -zxf go1.7.3.linux-amd64.tar.gz -C /usr/local/
mkdir /Golang
```

##### step 3：进入工作目录
```
cd /Golang
mkdir src && mkdir pkg && mkdir bin
```

##### step 4：设置环境变量并使其生效
```
vim /etc/profile
```
```
export GOROOT=/usr/local/go 
export GOBIN=$GOROOT/bin
export GOPKG=$GOROOT/pkg/tool/linux_amd64 
export GOARCH=amd64
export GOOS=linux
export GOPATH=/Golang
export PATH=$PATH:$GOBIN:$GOPKG:$GOPATH/bin
```
```
source /etc/profile
```

##### step 5：验证成功
```
root@ubuntumaster001:/home/liudui# go version
go version go1.11.4 linux/amd64
```

**安装成功**
**其他版本同理，只需要修改一下 wget 的路径即可。**

## 下一个教程 - [helloworld](https://github.com/LeaningGo/go-learn/blob/master/go-helloworld.md)

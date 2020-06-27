Title: Go语言学习
Date: 2017-08-29 22:23
Category: Go
Tags: Go
Author: 刘理想

[TOC]

## 1. 环境变量

### 1.1 GOROOT

GOROOT就是go的安装路径
在`~/.bash_profile`中添加下面语句:

```bash
GOROOT=/usr/local/go
export GOROOT

# 添加到PATH
export PATH=$PATH:$GOROOT/bin
```

### 1.2 GOPATH

`go install/get/run`会用到的目录。它的下面有3个文件夹：

- bin: golang编译可执行文件存放路径，可自动生成。通常把它也假如到PATH中，用来执行安装的第三方工具
- pkg: golang编译的.a中间文件存放路径，可自动生成
- src: 源码路径。按照golang默认约定，go run，go install等命令的当前工作路径（即在此路径下执行上述命令）。

可以设置多个子目录到GOPATH中

```
export GOPATH=~/golib:~/goproject
```

### 1.3 GOBIN

想存放GO程序的可执行程序的地方

```
export GOBIN=~/gobin
```



### 1.4 目录结构

```
$GOPATH/
    src/
        github.com/
            username/
                useless/
                    .git/
                    useless.go
                    useless_test.go
                    README.md
                uselessd/
                    .git/
                    uselessd.go
                    uselessd_test.go
                    README.md
```

编译：

```
go install useless 或 go get useless 
go install uselessd 或 go get uselessd 
```

详细请参考：https://github.com/golang/go/wiki/GithubCodeLayout

**go get [main.go所在路径]**

参数 [main.go所在路径]：可选。相对GOPATH/src路径。 缺省是.(src自己)。可指定src下面的子文件夹路径。 
go get会做2件事：1. 从远程下载需要用到的包。2.执行go install。（从这里也可以看出golang处处为了简洁而遵循的“约定优于配置”原则）

**go install [main.go所在路径]**

参数 [main.go所在路径]：可选。 相对GOPATH/src路径。缺省是.(即当前所在目录或工作目录)。可指定src下面的子文件夹。 
go install编译生成名称为[main.go父文件夹名]的可执行文件，放到GOBIN路径下。当GOBIN为空时，默认约定是：生成的可执行文件放到GOPATH/bin文件夹中。产生的中间文件（.a）放在project/pkg中（没有变化时，不重新生成.a）。
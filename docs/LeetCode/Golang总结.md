---
title: Golang总结
date: 2019-08-15 10:00:23
tags: [golang]
category: [golang学习]
---

## 编译问题

go build；go install； go getValue

<!--more-->

- go run: 运行 命令源码文件

- go build： **在当前目录下(compile+link)生成可执行文件**,注意: go build指令会调用所有引用包的源码，重新编译，而不是直接使用pkg里的编译后的文件，如果在`$GOROOT`或者`$GOPATH`下没有找到import引入的项目源码，就会报错
- go install: **编译源代码**，编译并安装代码包或者源码文件; **go install 命令只比 go build 命令多做了一件事，即：安装编译后的结果文件到指定目录**。如果可执行文件(package ''mian''且包含main方法),则会编译生成可执行文件到`$GOPATH/bin`目录下，可执行文件import引入其他包，就会被编译. 到`$GOPATH/pkg/$GOOS_$GOARCH`目录下. 1. **安装代码包**会在当前工作区的 pkg 的平台相关目录下生成**归档文件**（即 .a 文件）。 2.**安装命令源码文件**会在当前工作区的 bin 目录（如果 GOPATH 下有多个工作区，就会放在 GOBIN 目录下）生成可执行文件。
- go get： git clone到 `$GOPATH\src` + go install


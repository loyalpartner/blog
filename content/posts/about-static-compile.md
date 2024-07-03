+++
title = 'About Static Compile'
date = 2024-06-19T09:25:53+08:00
draft = true
+++

## 关于静态编译的一点经验

静态编译是一种将程序的所有依赖项打包到一个二进制文件中的技术。这种技术的优点是，可以将程序的所有依赖项打包到一个文件中，这样就不需要在运行时加载依赖项。这样可以减少程序的启动时间，并且可以减少程序的内存占用。另外，静态编译还可以减少程序的依赖性，使得程序更容易部署和维护。


### meson

1. executable 增加 link_args: ['-static'] 选项
2. dependency 增加 static: true  选项

### autoconf

1. ./configure LDFLAGS=-static
2. ./configure --enable-static


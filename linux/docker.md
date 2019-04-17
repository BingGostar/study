### 相关概念
```
LXC:linux容器3个核心技术

Namespace：Namespace又称为命名空间，它主要做访问隔离，不同的容器间 进程pid可以相同，进程并不冲突影响；但可以共享底层的计算和存储

Cgroups：Cgroups是Linux内核功能，它让两件事情变成可能：限制Linux进程组的资源占用（内存、CPU）；为进程组制作 PID、UTS、IPC、网络、用户及装载命名空间。

union文件系统：在union文件系统里，文件系统可以被装载在其他文件系统之上，其结果就是一个分层的积累变化。

```

### 镜像与容器
```
镜像是docker生命周期的构建和打包阶段，容器是docker的启动和执行阶段
```

### 资源隔离
```
文件系统隔离
进程隔离
网络隔离

使用cgroups分离
```

### 运行一个容器的时候，docker会
```
拉取镜像，若本地已经存在该镜像，则不用到网上去拉取
创建新的容器
分配union文件系统并且挂着一个可读写的层，任何修改容器的操作都会被记录在这个读写层上，你可以保存这些修改成新的镜像，也可以选择不保存，那么下次运行改镜像的时候所有修改操作都会被消除
分配网络\桥接接口，创建一个允许容器与本地主机通信的网络接口
设置ip地址，从池中寻找一个可用的ip地址附加到容器上，换句话说，localhost并不能访问到容器
运行你指定的程序
捕获并且提供应用输出，包括输入、输出、报错信息
```



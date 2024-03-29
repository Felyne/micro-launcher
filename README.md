### 微服务进程管理工具

#### 说明

微服务多的时候要一个个手动启动比较麻烦，该工具作为管理进程，把某个目录下的程序作为  
子进程启动起来，并对目录进行监控。当增加，移动或删除目录下的程序文件时，会自动启动，  
重启或停止相关子进程

#### 安装
```shell
export GO111MODULE=on
go get github.com/Felyne/micro-launcher
```
#### 使用

```shell
# 先用admin-tool上传相关配置到etcd
# /tmp/test目录下放程序文件
nohup ./micro-launcher dev /tmp/test localhost:2379 &
```

#### 注意

程序作为管理进程，对部分信号做了处理，当它收到不同信号，子进程的表现有所不同

  - `SIGTERM`|`SIGQUIT`|`SIGINT`:管理进程和子进程都退出
  
  - `SIGKILL`:管理进程被杀死，子进程不退出，父进程变为1号进程
    
  - `SIGTSTP`:管理进程被挂起(`jobs`命令查看)，子进程暂停服务，执行`bg %N`或者`fg %N`恢复

#### 进程关系

linux默认情况下，当进程收到不同信号，子进程的表现不同

  - `SIGTERM`|`SIGQUIT`:比如kill $pid，进程退出，子进程不退出，父进程变为1号进程

  - `SIGINT`:比如ctrl + c，子进程也会收到信号，和进程一起退出

  - `SIGKILL`:比如kill -9 $pid，信号不能被捕获和忽略，进程被杀死，子进程不退出，父进程变为1号进程
  
  - `SIGTSTP`:比如ctrl + z，进程被挂起(`jobs`命令查看)，子进程也受到影响，执行`bg %N`或者`fg %N`恢复
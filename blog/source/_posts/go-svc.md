---
title: 使用go-svc安全退出程序
date: 2018-06-26 15:24:02
tags: [go,go-svc,signal]
categories: go
---
阅读 [NSQ](https://github.com/nsqio/nsq) 源码时，看到NSQ使用了 go-svc 来启动nsq的相关程序,使得在程序退出的时候可以做一些释放资源等收尾工作。

使用起来非常简单,定义一个Service,实现其 svc.Service中的接口即可。

<!--more-->
### 简单使用

```
package main

import (
	"fmt"
	"github.com/judwhite/go-svc/svc"
	"log"
	"syscall"
	"time"
)

type program struct {
}

func main() {
	prg := &program{}
	if err := svc.Run(prg, syscall.SIGUSR1, syscall.SIGINT, syscall.SIGTERM); err != nil {
		log.Fatal(err)
	}
}

func (program) Init(env svc.Environment) error {

	fmt.Println("init.....")
	return nil
}

func (program) Start() error {

	fmt.Println("start.....")
	fmt.Println(syscall.Getpid())
	go func() {
		ticker := time.NewTicker(2 * time.Second)
		for t := range ticker.C {
			fmt.Println("tick at", t)
		}
	}()
	return nil
}
func (program) Stop() error {

	fmt.Println("stop.....")
	return nil
}

```

### 注意事项

1.Start方法中不能只直接阻塞，需要在Start方法中新开goroutine去写需要阻塞的代码。
2.svc.Run()方法的第二个参数可以指定需要程序监听的信号，默认情况下不指定的话，默认会监听 SIGINT和 SIGTERM两个。根据具体需要进行指定，比如本例中还监听了SIGUSR1.

### 实现原理
其实go-svc代码实现很简单，使用了标准库中的`os/signal`的`Notify`方法。
下面的代码截取于`judwhite/go-svc/svc/svc-other.go`：
```
// Run runs your Service.
//
// Run will block until one of the signals specified in sig is received.
// If sig is empty syscall.SIGINT and syscall.SIGTERM are used by default.
func Run(service Service, sig ...os.Signal) error {
	env := environment{}
	if err := service.Init(env); err != nil {
		return err
	}

	if err := service.Start(); err != nil {
		return err
	}

	if len(sig) == 0 {
		sig = []os.Signal{syscall.SIGINT, syscall.SIGTERM}
	}

	signalChan := make(chan os.Signal, 1)
	signalNotify(signalChan, sig...)
	//signalNotify方法其实就是 signal.Notify 方法
	//var signalNotify = signal.Notify
	<-signalChan

	return service.Stop()
}
```
标准库里面的 `os/signal`中的 `Notfiy`方法签名如下：
```
func Notify(c chan<- os.Signal, sig ...os.Signal) {}
```

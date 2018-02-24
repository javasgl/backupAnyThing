---
title: golang同时监听TCP、HTTP端口提供服务
date: 2018-02-24 11:45:30
tags: [go,http]
categories: go
---

之前一个golang写的服务，是使用 TCP 方式进行通信的，但是并没很好的处理粘包问题(其实是根本就没有处理粘包问题)。项目需要添加新功能后迅速上线,所以准备先采用http来通信，避免粘包问题。


项目入口 main.go 中，之前是监听 TCP 连接，代码大致如下：
```
func main(){
	server,err:= net.Listen("tcp","host:port")
	if err!=nil{
		return
	}
	defer server.Close()

	for{
		conn,err:= server.Accept() 
		if err!=nil{
			continue
		}
		go handleConn(conn)
	}

	func handleConn(conn net.Conn){
		//do somethings
	}
}
```
现在需要在此基础之上监听 http 连接,由于 http.ListenAndServe() 方法是阻塞的，所以需要新开goroutine进行监听，代码示意如下：
```
func main(){

	http.HandleFunc("/hi", Router)
	//因为会阻塞，所以需要新开goroutine进行监听
	go http.ListenAndServe("host:http_port", nil)

	server,err:= net.Listen("tcp","host:tcp_port")
	if err!=nil{
		return
	}
	defer server.Close()

	for{
		conn,err:= server.Accept() 
		if err!=nil{
			continue
		}
		go handleConn(conn)
	}

	func handleConn(conn net.Conn){
		//do somethings
	}
}

```

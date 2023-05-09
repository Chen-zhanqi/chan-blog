---
title: Golang 接口请求高并发
date: 2022-06-20
tags:
- golang
categories:
- 2022
- 技术
---

>采用两级 channel，一级是将用户请求数据放入到 chan Job 中，这个 channel job 相当于待处理的任务队列。
另一级用来存放可以处理任务的 work 缓存队列，类型为 chan chan Job。调度器把待处理的任务放入一个空闲的缓存队列当中，work 会一直处理它的缓存队列。通过这种方式，实现了一个 worker 池。

## 请求: 把任务放入JobQueue
```
package main

import (
"fmt"
"log"
"net/http"
"time"
)

const (
	MaxWorker = 100 // 随便设置值
	MaxQueue  = 200 // 随便设置值
)

// 一个可以发送工作请求的缓冲 channel
var JobQueue chan Job

func init() {
	JobQueue = make(chan Job, MaxQueue)
}

type Payload struct{}

type Job struct {
	PayLoad Payload
}

// 接收请求，把任务筛入JobQueue。
func payloadHandler(w http.ResponseWriter, r *http.Request) {
	work := Job{PayLoad: Payload{}}
	JobQueue <- work
	_, _ = w.Write([]byte("操作成功"))
}

func main() {
	// 通过调度器创建worker，监听来自 JobQueue的任务
	d := NewDispatcher()
	d.Run()
	http.HandleFunc("/payload", payloadHandler)
	log.Fatal(http.ListenAndServe(":8099", nil))
}
```

## Dispatcher调度器：循环读取JobQueue
一个Dispatcher 管理 多个Worker

```
package main
// 初始化操作
type Dispatcher struct {
	WorkerPool chan chan Job
}

func NewDispatcher() *Dispatcher {
	pool := make(chan chan Job, MaxWorker)
	return &Dispatcher{WorkerPool: pool}
}

func (d *Dispatcher) Run() {
	// 1. 开始运行 n 个 worker
	for i := 0; i < MaxWorker; i++ {
		worker := NewWorker(d.WorkerPool)
		worker.Start()
	}
	// 2. 循环读取JobQueue，随机选取一个Worker执行任务
	go d.dispatch()  
}

func (d *Dispatcher) dispatch() {
	for {
		select {
		case job := <-JobQueue:
			go func(job Job) {
				// 阻塞直到获取一个可用的worker job channel
				jobChannel := <-d.WorkerPool
				// 分发任务到 worker job channel 中
				jobChannel <- job
			}(job)
		}
	}
}
```

## Worker: 从Dispatcher获取任务
```
type Worker struct {
	WorkerPool chan chan Job
	JobChannel chan Job
	quit       chan bool
}

func NewWorker(workerPool chan chan Job) Worker {
	return Worker{
		WorkerPool: workerPool,
		JobChannel: make(chan Job),
		quit:       make(chan bool),
	}
}

// 开启一个 worker 循环监听或退出channel
func (w Worker) Start() {
	go func() {
		for {
			// 将当前的 worker 注册到 worker 队列中
			w.WorkerPool <- w.JobChannel
			select {
			case job := <-w.JobChannel:
				// 	真正业务的地方,模拟操作耗时
				time.Sleep(500 * time.Millisecond)
				fmt.Printf("上传成功:%v\n", job)
			case <-w.quit:
				return
			}
		}
	}()
}

func (w Worker) stop() {
	go func() {
		w.quit <- true
	}()
}
```

[来源](https://cloud.tencent.com/developer/article/1831381)
---
title: "Go语言RESTful API开发实战"
date: 2019-09-01T22:17:30+08:00
lastmod: 2019-09-01T22:17:30+08:00
draft: true
keywords: []
description: ""
tags: [翻译,Golang,教程]
categories: [Golang]
author: "panxl6"

# You can also close(false) or open(true) something for this content.
# P.S. comment can only be closed
comment: true
toc: false
autoCollapseToc: false
postMetaInFooter: false
hiddenFromHomePage: false
# You can also define another contentCopyright. e.g. contentCopyright: "This is another copyright."
contentCopyright: false
reward: false
mathjax: false
mathjaxEnableSingleDollar: false
mathjaxEnableAutoNumber: false

# You unlisted posts you might want not want the header or footer to show
hideHeaderAndFooter: false

# You can enable or disable out-of-date content warning for individual post.
# Comment this out to use the global config.
#enableOutdatedInfoWarning: false

flowchartDiagrams:
  enable: false
  options: ""

sequenceDiagrams: 
  enable: false
  options: ""

---
如今微服务、无服务器架构大行其道。而API开发是这些话题的主角。 Go语言开发效率要比Java Spring要快一些，性能上比PHP高出一个数量级。尤其是Go语言在并发方便非常的优秀，是2017年值得关注的一门语言。 本文通过一个经典的Todo应用来介绍使用Go语言开发API。
<!--more-->


>如今微服务、无服务器架构大行其道。而API开发是这些话题的主角。
> Go语言开发效率要比Java Spring要快一些，性能上比PHP高出一个数量级。尤其是Go语言在并发方便非常的优秀，是2017年值得关注的一门语言。
**本文通过一个经典的Todo应用来介绍使用Go语言开发API。**    
>  [配套的演示代码](https://github.com/panxl6/golang-todo)

### 主要涉及的内容：
- API开发框架`gin-gonic`
- ORM框架`gorm`
- Go语言mysql驱动

### 依赖包
```go
$ go get gopkg.in/gin-gonic/gin.v1
$ go get -u github.com/jinzhu/gorm
$ go get github.com/go-sql-driver/mysql
```

### [接口文档](http://www.panxl.cn/swagger)：
![接口文档](https://imgconvert.csdnimg.cn/aHR0cDovL2ltZy5ibG9nLmNzZG4ubmV0LzIwMTcxMjA5MTIzNTUwOTI1?x-oss-process=image/format,png)


## 一、Hello World
```go
package main

import "github.com/gin-gonic/gin"

func main() {
	r := gin.Default()
	r.GET("/ping", func(c *gin.Context) {
		c.JSON(200, gin.H{
			"message": "pong",
		})
	})
	r.Run() // listen and serve on 0.0.0.0:8080
}
```


## 二、路由设计

```go
package main

import "github.com/gin-gonic/gin"

var router = gin.Default()

func init()  {
	router := gin.Default()

	v1 := router.Group("/api/v1/todos")
	{
		v1.POST("/", createTodo)
		v1.GET("/", fetchAllTodo)
		v1.GET("/:id", fetchSingleTodo)
		v1.PUT("/:id", updateTodo)
		v1.DELETE("/:id", deleteTodo)
	}
}
```

## 三、设计数据库
```sql
drop database if exists demo;
create database demo charset='utf8';

use demo;

drop table if exists todo;
create table todo (

	primary key(id),
	id int not null auto_increment,
	title varchar(256) not null default '待办事项',
	completed bool not null default 0,

	created_at timestamp not null default current_timestamp,
	updated_at timestamp not null default current_timestamp,
	deleted_at timestamp not null default current_timestamp
) Engine=Innodb;
```

## 四、连接数据库
```
package main

import (
	"github.com/jinzhu/gorm"
	_ "github.com/jinzhu/gorm/dialects/mysql"
)

var (
	db *gorm.DB
	sqlConnection = "golang:1234567890@(114.115.136.205)/demo?charset=utf8&parseTime=True&loc=Local"
)

func init() {
	//打开数据库连接
	var err error
	db, err = gorm.Open("mysql", sqlConnection)
	if err != nil {
		panic("failed to connect database")
	}

	db.AutoMigrate(&todoModel{})
}
```

## 五、模型设计
```go
package main

import "github.com/jinzhu/gorm"

type (
	// entity类
	todoModel struct {
		gorm.Model
		Title     string `json:"title"`
		Completed int    `json:"completed"`
	}

	// response entity
	transformedTodo struct {
		ID        uint   `json:"id"`
		Title     string `json:"title"`
		Completed bool   `json:"completed"`
	}
)
```

## 六、CRUD
```go
package main

import (
	"github.com/gin-gonic/gin"
	"strconv"
	"net/http"
)

// createTodo add a new todo
func createTodo(c *gin.Context) {
	completed, _ := strconv.Atoi(c.PostForm("completed"))
	todo := todoModel{Title: c.PostForm("title"), Completed: completed}
	db.Save(&todo)
	c.JSON(http.StatusCreated, gin.H{"status": http.StatusCreated, "message": "Todo item created successfully!", "resourceId": todo.ID})
}

// fetchAllTodo fetch all todos
func fetchAllTodo(c *gin.Context) {
	var todos []todoModel
	var _todos []transformedTodo

	db.Find(&todos)

	if len(todos) <= 0 {
		c.JSON(http.StatusNotFound, gin.H{"status": http.StatusNotFound, "message": "No todo found!"})
		return
	}

	//transforms the todos for building a good response
	for _, item := range todos {
		completed := false
		if item.Completed == 1 {
			completed = true
		} else {
			completed = false
		}
		_todos = append(_todos, transformedTodo{ID: item.ID, Title: item.Title, Completed: completed})
	}
	c.JSON(http.StatusOK, gin.H{"status": http.StatusOK, "data": _todos})
}

// fetchSingleTodo fetch a single todo
func fetchSingleTodo(c *gin.Context) {
	var todo todoModel
	todoID := c.Param("id")

	db.First(&todo, todoID)

	if todo.ID == 0 {
		c.JSON(http.StatusNotFound, gin.H{"status": http.StatusNotFound, "message": "No todo found!"})
		return
	}

	completed := false
	if todo.Completed == 1 {
		completed = true
	} else {
		completed = false
	}

	_todo := transformedTodo{ID: todo.ID, Title: todo.Title, Completed: completed}
	c.JSON(http.StatusOK, gin.H{"status": http.StatusOK, "data": _todo})
}

// updateTodo update a todo
func updateTodo(c *gin.Context) {
	var todo todoModel
	todoID := c.Param("id")

	db.First(&todo, todoID)

	if todo.ID == 0 {
		c.JSON(http.StatusNotFound, gin.H{"status": http.StatusNotFound, "message": "No todo found!"})
		return
	}

	db.Model(&todo).Update("title", c.PostForm("title"))
	completed, _ := strconv.Atoi(c.PostForm("completed"))
	db.Model(&todo).Update("completed", completed)
	c.JSON(http.StatusOK, gin.H{"status": http.StatusOK, "message": "Todo updated successfully!"})
}

// deleteTodo remove a todo
func deleteTodo(c *gin.Context) {
	var todo todoModel
	todoID := c.Param("id")

	db.First(&todo, todoID)

	if todo.ID == 0 {
		c.JSON(http.StatusNotFound, gin.H{"status": http.StatusNotFound, "message": "No todo found!"})
		return
	}

	db.Delete(&todo)
	c.JSON(http.StatusOK, gin.H{"status": http.StatusOK, "message": "Todo deleted successfully!"})
}
```

## 七、编译打包

将多个go文件打包为一个可执行文件
```
go build main.go router.go controller.go config.go model.go
```
如果把所有的代码放在一个文件里面，直接运行即可`go build main.go`。
为了方便展示，这里给出了[已经打包好的文件](https://github.com/panxl6/golang-todo/tree/master/dist/windows)

## 八、单元测试（略）

## 九、运维上线
```bash
nohup ./main &
```

## 十、效果展示
**创建:**
![创建](https://imgconvert.csdnimg.cn/aHR0cDovL2ltZy5ibG9nLmNzZG4ubmV0LzIwMTcxMjE2MjAxMjQ5NDMy?x-oss-process=image/format,png)

**获取：**
![获取](https://imgconvert.csdnimg.cn/aHR0cDovL2ltZy5ibG9nLmNzZG4ubmV0LzIwMTcxMjE2MjAxNTA4ODQ2?x-oss-process=image/format,png)

**更新：**
![更新](https://imgconvert.csdnimg.cn/aHR0cDovL2ltZy5ibG9nLmNzZG4ubmV0LzIwMTcxMjE2MjAxMzE3ODQz?x-oss-process=image/format,png)

**删除：**
![删除](https://imgconvert.csdnimg.cn/aHR0cDovL2ltZy5ibG9nLmNzZG4ubmV0LzIwMTcxMjE2MjAxMzA2NzM0?x-oss-process=image/format,png)


> 本文的原始项目是来自[原文地址](https://medium.com/@thedevsaddam/build-restful-api-service-in-golang-using-gin-gonic-framework-85b1a6e176f3)。
> 但是由于一些本土化的需要（不用翻墙），做了较大的改动。
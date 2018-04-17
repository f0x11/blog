+++
date = "2016-09-07T13:48:27+08:00"
draft = false
title = "Golang学习问题1"

+++

*go编译出的程序有系统依赖吗?*

    有,go是编译性语言.


*slice内存,引用数组的内存*

    package main

    import "fmt"

    func main() {
        d := []int{1, 2, 3, 4}
        e := d[2:]
        f := e
        fmt.Println("d1 =", d)
        fmt.Println("e1 =", e)
        fmt.Println("f1 =", f)
        e = append(e, 5, 6)
        e[1] = 7
        fmt.Println("d2 =", d)
        fmt.Println("e2 =", e)
        fmt.Println("f2 =", f)
    }

```
e在append前指向d[2:], append后超出cap, 新分配cap*2的内存并copy原数据
d在append前,对e进行操作d会受到影响,append后不再受影响.
f并没有受e的append影响,仍然保留原值.
```

*nil slice和empty slice的append内存扩展机制*
```
var slice []int

fmt.Println("nil len =", len(slice))
fmt.Println("nil cap =", cap(slice))

newSlice := append(slice, 5, 99)

fmt.Println("newSlice len =", len(newSlice))
fmt.Println("newSlice cap =", cap(newSlice))

newSlice = append(newSlice, 6)

fmt.Println("newSlice len =", len(newSlice))
fmt.Println("newSlice cap =", cap(newSlice))

newSlice = append(newSlice, 7)

fmt.Println("newSlice len =", len(newSlice))
fmt.Println("newSlice cap =", cap(newSlice))
```
```
初始len和cap为0
第一次append n个数据后, len和cap为n
之后规则和正常数组相同
```

*不要通过共享来通信,而要通过通信来共享.*

*所有进程都阻塞后会报dead lock.*

    package main

    import "fmt"

    func main() {
        c := make(chan int, 1)
        c <- 1
        c <- 2
        fmt.Println(<-c)
        fmt.Println(<-c)
    }


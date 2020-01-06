---
layout:     post
title:      JS Promise
subtitle:   Promise 是异步编程的一种解决方案，比传统的解决方案——回调函数和事件——更合理且更强大
date:       2020-01-06
author:     Aisopos
header-img: img/post-bg-js-version.jpg
catalog: true
tags:
    - 前端
    - javascript
---

## 什么是Promise

> （1）对象的状态不受外界影响，promise对象代表一个异步操作，有三种状态，pending（进行中）、fulfilled（已成功）、rejected（已失败）。只有异步操作的结果，可以决定当前是哪一种状态，任何其他操作都无法改变这个状态，这也是promise这个名字的由来“承诺”；

> （2）一旦状态改变就不会再变，任何时候都可以得到这个结果，promise对象的状态改变，只有两种可能：从pending变为fulfilled，从pending变为rejected。这时就称为resolved（已定型）。如果改变已经发生了，你再对promise对象添加回调函数，也会立即得到这个结果，这与事件（event）完全不同，事件的特点是：如果你错过了它，再去监听是得不到结果的。


## Promise的作用

1、主要用于异步计算

2、可以将异步操作队列化，按照期望的顺序执行，返回符合预期的结果

3、可以在对象之间传递和操作promise，帮助我们处理队列

## 为什么会有Promise

> 为了避免界面冻结（任务）

【同步】：假设你去了一家饭店，找个位置，叫来服务员，这个时候服务员对你说，对不起我是“同步”服务员，我要服务完这张桌子才能招呼你。那桌客人明明已经吃上了，你只是想要个菜单，这么小的动作，服务员却要你等到别人的一个大动作完成之后，才能再来招呼你，这个便是同步的问题：也就是“顺序交付的工作1234，必须按照1234的顺序完成”。

【异步】：则是将耗时很长的A交付的工作交给系统之后，就去继续做B交付的工作，。等到系统完成了前面的工作之后，再通过回调或者事件，继续做A剩下的工作。
AB工作的完成顺序，和交付他们的时间顺序无关，所以叫“异步”。

在nodeJS出来之前PHP、Java、python等后台语言已经很成熟了，nodejs要想能够有自己的一片天，那就得拿出点自己的绝活：
无阻塞高并发，是nodeJS的招牌，要达到无阻塞高并发异步是其基本保障
举例：查询数据从数据库，PHP第一个任务查询数据，后面有了新任务，那么后面任务会被挂起排队；而nodeJS是第一个任务挂起交给数据库去跑，然后去接待第二个任务交给对应的系统组件去处理挂起，接着去接待第三个任务...那这样子的处理必然要依赖于异步操作

#### 异步回调的问题：

之前处理异步是通过纯粹的回调函数的形式进行处理

很容易进入到回调地狱中，剥夺了函数return的能力

问题可以解决，但是难以读懂，维护困难

稍有不慎就会踏入回调地狱 - 嵌套层次深，不好维护

## Promise详解

        new Promise(
            function (resolve, reject) {
                // 一段耗时的异步操作
                resolve('成功') // 数据处理完成
                // reject('失败') // 数据处理出错
            }
        ).then(
            (res) => {console.log(res)},  // 成功
            (err) => {console.log(err)} // 失败
        )

resolve作用是，将Promise对象的状态从“未完成”变为“成功”（即从 pending 变为 resolved），在异步操作成功时调用，并将异步操作的结果，作为参数传递出去；
reject作用是，将Promise对象的状态从“未完成”变为“失败”（即从 pending 变为 rejected），在异步操作失败时调用，并将异步操作报出的错误，作为参数传递出去。

promise有三个状态：
1、pending[待定]初始状态
2、fulfilled[实现]操作成功
3、rejected[被否决]操作失败
当promise状态发生改变，就会触发then()里的响应函数处理后续步骤；

Promise对象的状态改变，只有两种可能：
从pending变为fulfilled，
从pending变为rejected。
promise状态一经改变，不会再变。

最简单示例：

        new Promise(resolve => {
            setTimeout(() => {
                resolve('hello')
            }, 2000)
        }).then(res => {
            console.log(res)
        })

分两次，顺序执行：

        new Promise(resolve => {
            setTimeout(() => {
                resolve('hello')
            }, 2000)
        }).then(val => {
            console.log(val) // 参数val = 'hello'
            return new Promise(resolve => {
                setTimeout(() => {
                    resolve('world')
                }, 2000)
            })
        }).then(val => {
            console.log(val) // 参数val = 'world'
        })

promise完成后then()：

        let pro = new Promise(resolve => {
            setTimeout(() => {
                resolve('hello world')
            }, 2000)
        })
        setTimeout(() => {
            pro.then(value => {
                console.log(value) // hello world
            })
        }, 2000)

    结论：promise作为队列最为重要的特性，我们在任何一个地方生成了一个promise队列之后，我们可以把他作为一个变量传递到其他地方。

#### Promise.all() 批量执行：

Promise.all([p1, p2, p3])用于将多个promise实例，包装成一个新的Promise实例，返回的实例就是普通的promise
它接收一个数组作为参数
数组里可以是Promise对象，也可以是别的值，只有Promise会等待状态改变
当所有的子Promise都完成，该Promise完成，返回值是全部值得数组
有任何一个失败，该Promise失败，返回值是第一个失败的子Promise结果

        // 切菜
        function cutUp(){
            console.log('开始切菜。');
            var p = new Promise(function(resolve, reject){        //做一些异步操作
                setTimeout(function(){
                    console.log('切菜完毕！');
                    resolve('切好的菜');
                }, 1000);
            });
            return p;
        }

        // 烧水
        function boil(){
            console.log('开始烧水。');
            var p = new Promise(function(resolve, reject){        //做一些异步操作
                setTimeout(function(){
                    console.log('烧水完毕！');
                    resolve('烧好的水');
                }, 1000);
            });
            return p;
        }

        Promise.all([cutUp(), boil()])
            .then((result) => {
                console.log('准备工作完毕');
                console.log(result);
            })

Promise.race() 类似于Promise.all() ，区别在于它有任意一个完成就算完成

        let p1 = new Promise(resolve => {
            setTimeout(() => {
                resolve('I\`m p1 ')
            }, 1000)
        });
        let p2 = new Promise(resolve => {
            setTimeout(() => {
                resolve('I\`m p2 ')
            }, 2000)
        });
        Promise.race([p1, p2])
            .then(value => {
                console.log(value)
            })

#### 实例：

        // Promise 写法
        // 第一步：获取城市列表
        const cityList = new Promise((resolve, reject) => {
            $.ajax({
                url: 'https://www.easy-mock.com/mock/5a52256ad408383e0e3868d7/lagou/city',
                success (res) {
                    resolve(res)
                }
            })
        })

        // 第二步：找到城市是北京的id
        cityList.then(res => {
            let findCityId = res.filter(item => {
                if (item.id == 'c1') {
                    return item
                }
            })[0].id
            
            findCompanyId().then(res => {
                // 第三步（2）：根据北京的id -> 找到北京公司的id
                let findPostionId = res.filter(item => {
                    if(item.cityId == findCityId) {
                        return item
                    }
                })[0].id

                // 第四步（2）：传入公司的id
                companyInfo(findPostionId)
            })
        })

        // 第三步（1）：根据北京的id -> 找到北京公司的id
        function findCompanyId () {
            let aaa = new Promise((resolve, reject) => {
                $.ajax({
                    url: 'https://www.easy-mock.com/mock/5a52256ad408383e0e3868d7/lagou/position-list',
                    success (res) {
                        resolve(res)
                    }
                })
            })
            return aaa
        }

        // 第四步：根据上一个API的id(findPostionId)找到具体公司，然后返回公司详情
        function companyInfo (id) {
            let companyList = new Promise((resolve, reject) => {
                $.ajax({
                    url: 'https://www.easy-mock.com/mock/5a52256ad408383e0e3868d7/lagou/company',
                    success (res) {
                        let comInfo = res.filter(item => {
                            if (id == item.id) {
                            return item
                            }
                        })[0]
                        console.log(comInfo)
                    }
                })
            })
        }
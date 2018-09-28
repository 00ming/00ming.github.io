---
title: Test-driven development
date: 2018-01-09 15:59:00
tags: test
---
# Test-driven development
之前在知乎上看到一篇文章 [如何说服你的同事使用TDD](https://zhuanlan.zhihu.com/p/31662844)

TDD（Test-driven development），也就是我们常说的“测试驱动开发”,先不说有没有被说服.我就想到前端能不能用这么一种开发模式,
用这种开发方式有没有好处.
我试着在js中试着敲上面文章中的例子.先去找了下js的测试框架
JsUnit J3Unit YUI Test Mocha.js qunit 等等一大堆 有兴趣和有水平的同学可以自己去看看 因为基本的是一大堆英文
我就用了webstrom款ide 里面有JsTestDriver 这个一个插件 用了以后会跟上文接近(在代码下面有红有绿~)
![image](http://pbyegcye6.bkt.clouddn.com/TDD1.png)
<!-- more -->
## 简介
文中的例子来自  [UncleBob.TheBowlingGameKata](http://butunclebob.com/ArticleS.UncleBob.TheBowlingGameKata)
经常用js去操作DOM元素填充模版,拼接字符串,做做表单验证什么的,写这种还有些变扭.
### 需求
Bob大叔的保龄球训练
这是一道计算保龄球比赛一局总得分的编程题，保龄球的计分规则非常简单：
每一局总共有十轮，每轮一开始会有十支球瓶，球手可以扔两次球，目标就是用尽量少的球把全部球瓶击倒。
如果第一球就把全部的球瓶都击倒了，也就是STRIKE，画面出现“X”，就算完成一轮了，所得分数是10分再加后面两球的倒瓶数，
如果第一球没有全倒，就要再打一球，如果第二球将剩下的球瓶全都击倒，也就是SPARE，画面出现“/”，也算完成一格，所得分数为10分再加下一格第一球的倒瓶数，
如果第二球也没有把球瓶全部击倒的话，那分数就是第一球加第二球倒的瓶数，没有奖励（bonus），再接着打下一格。依此类推。
第十轮有机会扔三次球。如果在第十轮出现STRIKE或者SPARE，则球手可再加打第三球。
全部十轮的得分相加就等于这一局的总得分。
题目要求我们提供一个名字为Game的类，这个类有两个方法：
roll(pins : int)：每次球员扔球后执行这个方法，入参是此次扔球击倒的球瓶数量。
score()：每局比赛结束时执行的方法，返回这局比赛的总得分。

### 过程
先上Demo:https://github.com/00ming/TheBowlingGameKata
怕你们看不完
老规矩一开始先写测试用例,先开始一场游戏开始是0分
```js
var g = new game();
assertEquals(0, g.score())
```
再写代码
```js
var game = function() {
    this.score = function () {
    var score = 0
    return score
    }
}
```
测试一下  ok了
再改测试用例
```js
var g = new game();
for(var i = 0;i < 20;i ++) {
    g.roll(0)
}
assertEquals(0, g.score());
```
保龄球一局有10格,一格可以扔2次
再改代码.........
经过一个漫长的过程
最后的测试用例
```js
TestCase("GameTest", {
    "test ": function() {
        function rollMany (roll,pins) {
            for(var i = 0;i < roll;i ++) {
                g.roll(pins)
            }
        }
        var g = new game();
        rollMany(20,0)
        assertEquals(0, g.score());
    },
    "test all one": function() {
        function rollMany (roll,pins) {
            for(var i = 0;i < roll;i ++) {
                g.roll(pins)
            }
        }
        var g = new game();
        rollMany(20,1)
        assertEquals(20, g.score());
    },
    "test profect game": function() {
        function rollMany (roll,pins) {
            for(var i = 0;i < roll;i ++) {
                g.roll(pins)
            }
        }
        var g = new game();
        rollMany(12,10)
        assertEquals(300, g.score());
    },
    "test one strike": function() {
        function rollMany (roll,pins) {
            for(var i = 0;i < roll;i ++) {
                g.roll(pins)
            }
        }
        var g = new game();
        g.roll(10)
        g.roll(3)
        g.roll(4)
        rollMany(17,0)
        assertEquals(24, g.score());
    },
    "test one spare": function() {
        function rollMany (roll,pins) {
            for(var i = 0;i < roll;i ++) {
                g.roll(pins)
            }
        }
        var g = new game()
        g.roll(3)
        g.roll(7)
        g.roll(7)
        rollMany(17,0)
        assertEquals(24, g.score());
    },
});
最后的代码:
var game = function() {
    var score = 0
    var rolls = new Array()
    var currentRoll = 0
    // 全中
    var isStrike = function (frameIndex) {
        return (rolls[frameIndex] === 10)
    }
    // 全中奖励2球
    var strikeBonus = function (frameIndex) {
        return rolls[frameIndex + 1] + rolls[frameIndex + 2]
    }
    // 补中
    var isSpare = function (frameIndex) {
        return (rolls[frameIndex] + rolls[frameIndex+1] === 10)
    }
    this.roll = function (pins) {
        rolls[currentRoll++] = pins
    }
    this.score = function () {
        var score = 0
        var frameIndex = 0
        for (var frame = 0;frame < 10;frame ++) {
            if (isStrike(frameIndex)) {
                score += 10 + strikeBonus(frameIndex)
                frameIndex ++
            } else if (isSpare(frameIndex)) {
                score += 10 + rolls[frameIndex + 2]
                frameIndex += 2
            } else {
                score += rolls[frameIndex] + rolls[frameIndex + 1]
                frameIndex += 2
            }
        }
        return score
    }
}
```
过程bob大叔那都有,不过是java的我这也不一步一步写,因为真的好烦.
经过这么一轮下来,代码写得不咋的却清楚了解了保龄球的计分方式.
写这么一小段代码我已经重构的N遍,有因为方法写的不好的,也有在没完全了解保龄球几分方式下写出错误的测试用例导致代码错误的,总之自己把自己坑得不要不要的.....
# 总结
没有全能的技术也没有全能的开发方式,TDD带来了最大好处是提高了单元测试的覆盖率,但是写测试用例的过程也是非常耗时费脑子的事情.
## 优点
TDD写出来的代码比起平时拿到需求就直接写程序,边写边想写出来的代码要准确很多,考虑到更多的情况,代码更加健壮这是不可否认的.
## 缺点
要想做好TDD，必须掌握好单元测试、重构等技能，还要能够写出整洁代码,如果写出来的单元测试本来就是错的,那代码肯定也是错的(刚刚经历完).而且这种思维模式也难以适应,在写这小段代码的时候我差点控制不住自己要把函数写完再改测试用例,最后还是坚持下来了.如果这是一个更负责的函数的话对测试用例的要求会更高,会有更多的测试用例.

要开发人员花费额外的时间写测试用例,但代码效率有提高这点不知道算有点还算缺点了.
回到最初的讨论”到前端能不能用这么一种开发模式,用这种开发方式有没有好处” 前端的操作相对简单需要的测试用例也不多,有尝试的可能,如果能开展下去代码的质量有一定的提升.在逻辑稍微复杂的函数上使用效果更为明显.
以上均是我个人观点,不代表实际情况
 
#### 题外话
这是很久以前的文章,后来整理的找出来了,还是用word文档写的这次移到markdown上.这应该是第一篇参考别人写的文章,迁移感触很深.虽然写的真的很烂,没有什么结构可言,逻辑也不清晰,还不是原创的,缺点多到数不完.但是现在看起来还是那么亲切,相比之下感受到自己的进步,还是蛮欣慰的.
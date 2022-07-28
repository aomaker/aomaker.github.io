---
layout: home
title: AoMaker简介
permalink: /
---

![jRL5nO.md.png](https://s1.ax1x.com/2022/07/13/jRL5nO.md.png)

> Quickly Arrange,Quickly Test!

[![pyversions](https://img.shields.io/pypi/pyversions/httprunner.svg)](https://pypi.python.org/pypi/httprunner)

# 核心思想

**AoMaker**，即 Api object Maker，那什么是**API Object**呢？

**API Object**是**Page Object**设计模式在接口测试上的一种延伸，顾名思义，这里是将各种基础接口进行了一层抽象封装，将其作为 object，通过不同的 API 对象调用来组装成不同的业务流场景。

举个简单例子：

有一个购物车功能，它有如下接口：

- add，将物品添加到购物车
- list，查看购物车内所有物品
- delete，清空购物车内所有物品

那么，我们通过`ao` 的思想来封装这个功能所有接口：

```python
    class ShoppingTrolley:
        def add(self):
            """添加物品到购物车"""
            pass

        def delete(self):
            """清空购物车"""
            pass

        def list(self):
            """查看购物车内所有物品"""
            pass
```

当我们需要测试购物车的增-查-删流程时，只需要实例化`ShoppingTrolley` 类，通过调用对应的接口对象，即可完成业务场景的组装：

```python
    class TestShoppingTrolley:
        def test_shopping(self):
            """测试购物车增-查-删流程"""
            st = ShoppingTrolley()
            # 1.添加物品到购物车
            st.add()
            # 2.查看购物车内物品
            st.list()
            # 3.清空购物车
            st.delete()
```

解释了**API Object**，那**Maker**又怎么理解呢？**Maker**其实就是框架本身提供各种辅助手段帮助快速的去编排`ao` 和`case` 。

# 定位

一款基于`pytest` ，将接口通过**ao 对象**方式来编排管理的接口自动化框架，核心定位就是基于`ao` 思想，让测试人员能快速完成自动化项目搭建和脚本的编写，快速开始测试。

# 特性

- 提供 CLI 命令
- 提供脚手架一键安装，开箱即用
- 变量管理简单
- 可使用 HAR/Swagger 等一键生成 ao 和 case
- 三种方式快速编写 ao 和 case
- 支持多进程和多线程
- 丰富的断言
- 支持流量录制
- 支持 pytest 所有用法和插件
- allure 报告优化
- 测试报告消息通知
- ...

# 交流

如果对该框架感兴趣以及有更多好的想法，欢迎交流探讨~

[![v9XorQ.jpg](https://s1.ax1x.com/2022/07/28/v9XorQ.jpg)](https://imgtu.com/i/v9XorQ)

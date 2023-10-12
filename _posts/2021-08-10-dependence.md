---
title: 🤖dependence装饰器用法
author: Ancient One
date: 2021-08-10
category: Jekyll
layout: post
---

# 当该接口有依赖接口，参数中有依赖上个接口的返回怎么办？

`aomaker`提供了一个依赖接口装饰器：`@dependence`，只需要标记依赖哪个接口，就会在请求该接口前，先去请求依赖接口，并将依赖的返回保存在`cahce`中，具体用法：

```python
import json


from aomaker.base.base_api import BaseApi
# 从aomaker导入depedence装饰器
from aomaker.aomaker import dependence
# 导入依赖接口的接口对象
from apis.cluster import cluster


class Job(BaseApi):
    # dependence标记依赖参数
    @dependence(cluster.get_cluster_list, 'hpc_cluster_id', cluster_type='hpc')
    def submit_hpc_job(self, test_data):
        body = {
            # 从cache中获取依赖参数
            "cluster_id": self.cache.get_by_jsonpath('hpc_cluster_id', jsonpath_expr='$..cluster_id'),
            "hpcqueue_id": self.cache.get_by_jsonpath('hpc_queue_id', jsonpath_expr='$..hpcqueue_id'),
            "scheduler_queue_name": "medium",
            "cmd_line": test_data["cmd_line"]
        }
        http_data = {
            "method": "post",
            "api_path": "/portal_api",
            "params": {'action': 'job/submitJob'},
            "data": {
                'params': json.dumps(body)
            }
        }
        resp = self.send_http(http_data)
        return resp
```

**使用步骤：**

1. `from aomaker.aomaker import dependence`导入装饰器
2. 导入依赖接口对象
3. 在模板接口上使用`@dependence` 装饰器，该装饰器接收2个必传参数：
   1. 第一个参数，依赖接口对象
   1. 第二个参数，需要从依赖接口响应中提取的参数名
   1. 如果依赖接口本身需要从外部传入参数，那么可以以关键字参数的形式传入
4. 在body中，当需要引用依赖接口的参数时，直接调用`self.cache.get_by_jsonpath`方法，该方法接收2个必传参数：
   1. 第一个参数，依赖参数的参数名，即`cache`表中的`key`名
   1. 第二个参数，从依赖接口的响应中，提取出依赖参数值的`jsonpath`表达式
   1. 非必填，`jsonpath`表达式提取出的值是`list`，可以根据自身需求指明需要提取哪个，默认值为`0`

​	    如果要提取的值，不需要使用`jsonpath` 来提取，也可以使用`self.cache.get(key_name)` 提取。

# 如果依赖的接口，是同一个类的方法怎么办？ 

`@dependence`的第一个参数需要以字符串的形式传入，如`"Job.job_list"`，同时，还需要告诉装饰器，接口对象来自哪个模块，传入`imp_module="apis.job"`。

```python
class Job(BaseApi):
    # dependence标记依赖参数
    @dependence("Job.job_list", 'job_id',imp_module="apis.job")
    def submit_hpc_job(self, test_data):
```

# 如果有多个依赖接口，怎么办？

只需要继续加“帽子”即可，需要注意装饰器的执行顺序是**从上往下**执行。

```python
class Job(BaseApi):
    # 依赖从上往下执行
    @dependence(cluster.get_cluster_list, 'hpc_cluster_id', cluster_type='hpc')
    @dependence(queue.get_queue_list, 'hpc_queue_id')
    def submit_hpc_job(self, test_data):
        ...
        return resp
```
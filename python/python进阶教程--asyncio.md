| operator | createtime | updatetime |
| ---- | ---- | ---- |
| shenx | 2024-4月-17 | 2024-4月-17  |
| ... | ... | ... |
---
# python进阶教程--asyncio.md

[toc]

## 同步io与异步io
同步io 是指io操作开始后，线程必须等待io操作结束后拿到结果。
异步io 是指io操作开始后，线程不必等待io操作的结果，而是等io操作结束后由系统通知io操作结束，再回来处理结果。

为什么要使用asyncio?
web -- Django、Flask
爬虫 -- Scrapy

使用ayncio来定义协程
* 基于@asyncio.corontine装饰器来定义协程
```python
import asyncio

# 使用装饰器定义协程只是将函数对象标记为协程
# 实际上还是一个生成器，但是可以当做一个生成器来使用
@asyncio.coroutine
def hello():
    pass

loop = asyncio.get_event_loop()
tasks = [hello(), hello()]
loop.run_until_complete(asyncio.wait(tasks))
loop.close()
```

* 使用asyncio 远程协议定义

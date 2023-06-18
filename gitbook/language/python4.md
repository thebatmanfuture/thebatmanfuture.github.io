### tornado

###### 第一次代码

[有关python3.8报错解决](https://blog.csdn.net/wh8876/article/details/106887196/)

~~~python
import tornado.ioloop
import tornado.web

# windows 系统下 tornado 使用SelectorEventLoop
import platform
import asyncio

print(platform.system())
asyncio.set_event_loop_policy(asyncio.WindowsSelectorEventLoopPolicy())


class MainHandler(tornado.web.RequestHandler):
    def get(self):
        self.write("123")


def make_app():
    return tornado.web.Application([
        (r"/", MainHandler)
    ])


if __name__ == "__main__":
    app = make_app()
    app.listen(8888)
    tornado.ioloop.IOLoop.current().start()
~~~
































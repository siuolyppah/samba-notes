# usage

```C
struct tevent_context *ev = /* ... */;
ret = tevent_loop_once(ev);
if (ret != 0) {
	return false;
}
```

# 原理

在`tevent_context`的`additional_data`属性，声明类型为`void*`，但实际上
是`struct epoll_event_context*`(跟踪该函数即可发现), 而该结构体则保存着一个epoll_fd属性。

> 可参照底层函数`epoll_event_loop`(tevent_epoll.c)

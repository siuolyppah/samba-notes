# usage

```C
struct tevent_req *req, *subreq;
/* 初始化`req`和`subreq`, 参照echo_server.c中`echo_server_send`函数*/
tevent_req_set_callback(subreq, echo_server_accepted, req);
```

# macro `tevent_req_set_callback` in detail

```C
#define tevent_req_set_callback(req, fn, pvt) \
	_tevent_req_set_callback(req, fn, #fn, pvt)
```

# function `_tevent_req_set_callback` in detail

## Declaration

```C
void _tevent_req_set_callback(
    struct tevent_req *req,     // subreq
    tevent_req_fn fn,           // echo_server_accepted
    const char *fn_name,        // "echo_server_accepted"
    void *pvt                   // req
);
```

## Definition

```C
void _tevent_req_set_callback(struct tevent_req *req,
			      tevent_req_fn fn,
			      const char *fn_name,
			      void *pvt)
{
	req->async.fn = fn;
	req->async.fn_name = fn_name;
	req->async.private_data = pvt;
}
```

# usage

```C
	struct tevent_req *req;
	struct echo_server_state *state;

	req = tevent_req_create(mem_ctx, &state,
				struct echo_server_state);

```

# macro `tevent_req_create` definition

```C
#define tevent_req_create(_mem_ctx, _pstate, _type) \
	__tevent_req_create((_mem_ctx),             \
			    (_pstate),              \
			    sizeof(_type),          \
			    #_type,                 \
			    __func__,               \
			    __location__)
#endif
```

# function `__tevent_req_create` in detail

## Declaration

```C
struct tevent_req *__tevent_req_create(
    TALLOC_CTX *mem_ctx,
    void *pdata,             // &state // state has type `struct xxx_state*`
    size_t data_size,        // sizeof(struct xxx_state)
    const char *type,        // "strcut xxx_state"
    const char *func,        // __func__
    const char *location     // __location__
);
```

## Create and Schedule Immediate event

For creating an immediate event there is a small different, which lies in the fact that the creation of such event is done in 2 steps.

1. One represents the creation (memory allocation),
2. the second one represents registering as the event within some tevent context.

```C
#include <stdio.h>
#include <unistd.h>
#include <tevent.h>
struct info_struct {
    int counter;
};
static void foo(struct tevent_context *ev, struct tevent_immediate *im,
                void *private_data)
{
    struct info_struct *data = talloc_get_type_abort(private_data, struct info_struct);
    printf("Data value: %d\n", data->counter);
}
int main (void) {
    struct tevent_context *event_ctx;
    TALLOC_CTX *mem_ctx;
    struct tevent_immediate *im;
    printf("INIT\n");
    mem_ctx = talloc_new(NULL);
    event_ctx = tevent_context_init(mem_ctx);
    struct info_struct *data = talloc(mem_ctx, struct info_struct);
    // setting up private data
    data->counter = 1;
    // first immediate event
    im = tevent_create_immediate(mem_ctx);
    if (im == NULL) {
        fprintf(stderr, "FAILED\n");
        return EXIT_FAILURE;
    }
    tevent_schedule_immediate(im, event_ctx, foo, data);
    tevent_loop_wait(event_ctx);
    talloc_free(mem_ctx);
    return 0;
}
```

## Definition

```C

struct tevent_req *__tevent_req_create(/* ... */, void* pdata){
    struct tevent_req *req = talloc_pooled_object(...);
    *req = (struct tevent_req) {
		.internal = {
			.private_type		= type,
			.create_location	= location,
			.state			= TEVENT_REQ_IN_PROGRESS,
			.trigger		= tevent_create_immediate(req), // type `struct tevent_immediate *`
		},
	};

	void *data = talloc_zero_size(req, data_size);
	req->data = data;
	void **ppdata = (void **)pdata;
	*ppdata = data;

	// ...
	return req;
}
```

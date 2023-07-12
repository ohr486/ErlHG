
## エントリーポイント

erts/emulator/sys/unix/erl_main.c

```c
int
main(int argc, char **argv)
{
    sys_init_signal_stack();
    
    erl_start(argc, argv);
    return 0;
}
```

### erl_start

erts/emulator/beam/erl_init.c

```c
void
erl_start(int argc, char **argv)
{

    ...

    erts_start_schedulers();
    
    erts_sys_main_thread();
}
```

### erts_start_schedulers

erts/emulator/beam/erl_process.c

```c
void
earts_start_schedulers(void)
{
    ...
    
    if (erts_runq_supervision_interval) {
        ...
        res = ethr_thr_create(&runq_supervisor_tid,
                              runq_supervisor,
                              NULL,
                              &opts);
        ... 
    }
    
    ...
    
    for (ix = 0; ix < erts_no_schedulers; ix++) {
        ...
        res = ethr_thr_create(&esdp->tid, sched_thread_func, (void *)esdp, &opts);
        ...
    }

    {
        for (ix = 0; ix < erts_no_dirty_cpu_schedulers; ix++) {
            ...
            res = ethr_thr_create(&esdp->tid, sched_dirty_cpu_thread_func, (void *)esdp, &opts);
            ... 
        }
        for (ix = 0; ix < erts_no_dirty_io_schedulers; ix++) {
            ...
            res = ethr_thr_create(&esdp->tid, sched_dirty_io_thread_func, (void *)esdp, &opts);
            ... 
        }
    }
    
    ix = 0;
    while (ix < erts_no_aux_work_threads) {
        ...
        res = ethr_thr_create(&tid, aux_thread, (void *) (Sint) ix, &opts);
        ...
    }
    
    ...
    
    for (ix = 0; ix < erts_no_poll_threads; ix++) {
        ...
        res = ethr_thr_create(&tid, poll_thread, (void *) bpt, &opts);
        ...
    }
}
```

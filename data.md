データ構造
==========

Process
-------

erts/emulator/beam/erl_process.h

```c
struct process {
    ErtsPTabElementCommon common; /* *Need* to be first in struct */

    /* These are paired to exploit the STP instruction in the ARM JIT. */
    Eterm *htop;                /* Heap top */
    Eterm *stop;                /* Stack top */

    ...

    ProcDict *dictionary;        /* Process dictionary, may be NULL */

    ...

    const ErtsCodeMFA* current; /* Current Erlang function, part of the
                                 * funcinfo:
                                 *
                                 * module(0), function(1), arity(2)
                                 *
                                 * (module and functions are tagged atoms;
                                 * arity an untagged integer).
                                 */

    ...

    ErtsMessage *msg_frag;	/* Pointer to message fragment list */

    ...

    ErtsSchedulerData *scheduler_data;

    ...
}
```

ErtsPTabElementCommon
---------------------

erts/emulator/beam/erl_ptab.h

```c
typedef struct {
    Eterm id;
    union {
    	erts_atomic_t atmc;
       	Sint sint;
    } refc;
    erts_atomic_t timer;
    union {
    	/* --- While being alive --- */
    	struct {
    	    Uint64 started_interval;
    	    struct reg_proc *reg;
    	    ErtsLink *links;
             /* Local target monitors, double linked list
                contains the remote part of local monitors  */
            ErtsMonitor *lt_monitors;
             /* other monitors, rb tree */
    	    ErtsMonitor *monitors;
    	} alive;
    	/* --- While being released --- */
    	ErtsThrPrgrLaterOp release;
    } u;
    ErtsTracer tracer;
    Uint32 trace_flags;
} ErtsPTabElementCommon;
```

Eterm
------


```c
```

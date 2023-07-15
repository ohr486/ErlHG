データ構造
==========

Process
-------
erts/emulator/beam/erl_process.h
```plantuml
@startjson
#highlight "struct"
#highlight "ProcDict": "struct"
{
    "struct": "process",
    "ErtsPTabElementCommon": "common",
    "Eterm": {"*htop": ""},
    "Eterm": {"*stop": ""},
    "-": "...",
    "ProcDict": {"*current": ""},
    "-": "...",
    "const ErtsCodeMFA": {"*current": ""},
    "-": "...",
    "ErtsMessage": {"*msg_frag": ""},
    "-": "...",
    "ErtsSchedulerData": {"*scheduler_data": ""},
    "-": "..."
}
@endjson
```
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
```plantuml
@startjson
#highlight "struct"
#highlight "union" / "0" / "struct"
{
    "struct": "ErtsPTabElementCommon",
    "Eterm": "id",
    "union": [ 
            {
                "refc": "",
                "erts_atomic_t": "atmc"
            },
            {
                "refc": "",
                "Sint": "sint"
            }
    ],
    "erts_atomic_t": "timer",
    "union": [{
        "u": "",
        "struct": "alive",
        "Uint64": "started_interval",
        "struct reg_proc": {"*reg": ""},
        "ErtsLink": {"*links": ""},
        "ErtsMonitor": {"*lt_monitors": ""},
        "ErtsMonitor": {"*monitors": ""}
    },
    {
        "u": "",
        "ErtsThrPrgrLaterOp": "release"
    }],
    "ErtsTracer": "tracer",
    "Uint32": "trace_flags"
}
@endjson
```
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

ErtsMessage
-----------
erts/emulator/beam/erl_message.h
```plantuml
@startjson
#highlight "struct"
#highlight "ErtsMessage" / "struct"
#highlight "ErlHeapFragment" / "struct"
{
    "struct": "ErtsMessage",
    "ErtsMessage": {"*next": ""},
    "union": [
        {
            "data": "",
            "ErlHeapFragment": {"*heap_frag": ""}
        },
        {
            "data": "",
            "void": {"*attached": ""}
        }
    ],
    "Eterm": "m[ERL_MESSAGE_REF_ARRAY_SZ]",
    "ErlHeapFragment": "heap_frag"
}
@endjson
```
```c
typedef struct erl_mesg ErtsMessage;

...

#define ERL_MESSAGE_REF_FIELDS__			\
    ErtsMessage *next;	/* Next message */		\
    union {						\
	    ErlHeapFragment *heap_frag;			\
	    void *attached;					\
    } data;						\
    Eterm m[ERL_MESSAGE_REF_ARRAY_SZ]

...

struct erl_mesg {
    ERL_MESSAGE_REF_FIELDS__;

    ErlHeapFragment hfrag;
};
```

ErlHeapFragment
---------------
erts/emulator/beam/erl_message.h
```plantuml
@startjson
#highlight "struct"
{
    "struct": "ErlHeapFragment",
    "ErlHeapFragment": {"*next": ""},
    "ErlOffHeap": "off_heap",
    "Uint": "alloc_size",
    "Uint": "used_size",
    "Eterm": "mem[1]"
}
@endjson
```
```c
/*
 * This struct represents a heap fragment, which is used when there
 * isn't sufficient room in the process heap and we can't do a GC.
 */

typedef struct erl_heap_fragment ErlHeapFragment;
struct erl_heap_fragment {
    ErlHeapFragment* next;	/* Next heap fragment */
    ErlOffHeap off_heap;	/* Offset heap data. */
    Uint alloc_size;		/* Size in words of mem */
    Uint used_size;		/* With terms to be moved to heap by GC */
    Eterm mem[1];		/* Data */
};
```

Eterm
------

erts/emulator/beam/sys.h

```c
/*
** Data types:
**
** Eterm: A tagged erlang term (possibly 64 bits)
...
*/

#if SIZEOF_VOID_P == SIZEOF_LONG
typedef unsigned long Eterm erts_align_attribute(sizeof(long));
...
#elif SIZEOF_VOID_P == SIZEOF_INT
typedef unsigned int Eterm erts_align_attribute(sizeof(int));
...
#elif SIZEOF_VOID_P == SIZEOF_LONG_LONG
typedef unsigned long long Eterm erts_align_attribute(sizeof(long long));
...
#else
#error Found no appropriate type to use for 'Eterm', 'Uint' and 'Sint'
#endif
```


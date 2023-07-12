マクロ
======

ASSERT
------

erts/emulator/beam/sys.h

```c
#ifdef DEBUG
#  define ASSERT(e) ERTS_ASSERT(e)
#else
#  define ASSERT(e) ((void) 1)
#endif
```

ERTS_ASSERT
-----------

erts/emulator/beam/sys.h

```c
#define ERTS_ASSERT(e) \
    ((void) ((e) ? 1 : (erl_assert_error(#e, __func__, __FILE__, __LINE__), 0)))
```

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

}
```

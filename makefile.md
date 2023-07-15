Makeのダイアグラム
==================

#### make

| ENV              | VAL                               | source                           |
|------------------|-----------------------------------|----------------------------------|
| TARGET           | aarch64-apple-darwin22.5.0        | $(ERL_TOP)/make/target.mk        |
| TYPES            | opt debug lcnt valgrind asan gcov | $(ERL_TOP)/make/$(TARGET)/otp.mk |
| DEFAULT_TYPES    | opt                               | $(ERL_TOP)/make/$(TARGET)/otp.mk |
| FLAVORS          | emu jit                           | $(ERL_TOP)/make/$(TARGET)/otp.mk |
| PRIMARY_FLAVORS  | jit                               | $(ERL_TOP)/make/$(TARGET)/otp.mk |
| EMULATOR         | beam                              |                                  |
| PROFILE          | blank                             | Makefile                         |
| PROFILE_EMU_DEPS | blank                             | Makefile                         |

```plantuml
@startwbs
title make
* all
** bootstrap
*** depend
*** all_bootstraps
**** erl_interface
**** emulator
***** $(PROFILE_EMU_DEPS)
****** cd erts && make NO_START_SCRIPTS=true FLAVOR= PROFILE=
**** bootstrap_setup
**** secondary_bootstrap_copy
**** tertiary_bootstrap_copy
** libs
** local_setup
** check_dev_rt_dep
@endwbs
```

#### cd erts && make NO_START_SCRIPTS=true FLAVOR= PROFILE=

| ENV            | VAL   | source                           |
|----------------|-------|----------------------------------|
| FLAVOR         | blank | ?                                |
| PRIMARY_FLAVOR | jit   | $(ERL_TOP)/make/$(TARGET)/otp.mk |
| PROFILE        | blank | ?                                |

```plantuml
@startwbs
title cd erts && make NO_START_SCRIPTS=true FLAVOR= PROFILE=
* all
** jit
*** cd emulator && make FLAVOR=jit opt
@endwbs
```

#### cd erts/emulator && make FLAVOR=jit opt

```plantuml
@startwbs
title cd erts/emulator && make FLAVOR=jit opt
* opt
** make -f $(TARGET)/Makefile TYPE=opt
@endwbs
```

#### cd erts/emulator && make -f $(TARGET)/Makefile TYPE=opt

| ENV                 | VAL                                  | source                             |
|---------------------|--------------------------------------|------------------------------------|
| FLAVOR              | blank                                |                                    |
| TYPEMAKER           | blank                                |                                    |
| BINDIR              | $(ERL_TOP)/bin/$(TARGET)             |                                    |
| EMULATOR_EXECUTABLE | beam = $(FLAVOR_EXECUTABLE)          | $(ERL_TOP)/erts/$(TARGET)/Makefile |
| EMULATOR_LIB        | libbeam.a                            |                                    |
| PRIMARY_EXECUTABLE  | beam.smp                             |                                    |
| FLAVOR_EXECUTABLE   | beam                                 |                                    |
| INSTALL_PROGRAM     | $(INSTALL)                           | $(ERL_TOP)/make/$(TARGET)/otp.mk   |
| INSTALL             | /opt/homebrew/bin/ginstall -c        | $(ERL_TOP)/make/$(TARGET)/otp.mk   |
| INIT_OBJS           | $(OBJDIR)/erl_main.o $(PRELOAD_OBJS) |                                    |
| OBJS                | $(PROF_OBJS)                         |                                    |

`ginstall (source) (dest)` : `source` を `dest` にコピーする
`-c`オプション: 無視する

```plantuml
@startwbs
title cd erts/emulator && make -f $(TARGET)/Makefile TYPE=opt
* all
** $(BINDIR)/$(EMULATOR_EXECUTABLE) = $(BINDIR)/$(FLAVOR_EXECUTABLE)
*** $(INIT_OBJS)
**** erl_main.o
***** erts/emulator/sys/unix/erl_main.c
*** $(OBJS)
*** $(DEPLIBS)
*** $(EMUL_LD) -o $(BINDIR)/$(FLAVOR_EXECUTABLE) $(PFOFILE_LDFLAGS) $(LDFLAGS) $(INIT_OBJS) $(OBJS) $(STATIC_NIF_LIBS) $(STATIC_DRIVER_LIBS) $(LIBS)
** $(BINDIR)/$(EMULATOR_LIB)
** $(UNIX_ONLY_BUILDS)
** $(INSTALL_PROGRAM) $(BINDIR)/$(FLAVOR_EXECUTABLE) $(BINDIR)/$(PRIMARY_EXECUTABLE)
@endwbs
```
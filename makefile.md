Makeのダイアグラム
==================

#### make

| 環境変数  | 値                          |
|-----------|-----------------------------|
| TARGET    | aarch64-apple-darwin22.5.0  |
| TYPES     | opt debug ... |
| FLAVORS   | emu jit                     |
| EMULATOR  | beam                        |

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
****** cd erts && make
**** bootstrap_setup
**** secondary_bootstrap_copy
**** tertiary_bootstrap_copy
** libs
** local_setup
** check_dev_rt_dep
@endwbs
```

#### cd erts && make

```plantuml
@startwbs
title cd erts && make
* all
** opt
*** cd emulator && make opt
@endwbs
```

#### cd erts/emulator && make opt

```plantuml
@startwbs
title cd erts/emulator && make opt
* opt
** make -f $(TARGET)/Makefile TYPE=opt
@endwbs
```

#### cd erts/emulator && make -f $(TARGET)/Makefile TYPE=opt

| 環境変数            | 値                                   |
|---------------------|--------------------------------------|
| FLAVOR              | TODO                                 |
| TYPEMAKER           | blank                                |
| BINDIR              | $(ERL_TOP)/bin/$(TARGET)             |
| EMULATOR_EXECUTABLE | beam.smp                             |
| EMULATOR_LIB        | libbeam.a                            |
| PRIMARY_EXECUTABLE  | beam.smp                             |
| FLAVOR_EXECUTABLE   | beam                                 |
| INSTALL_PROGRAM     | $(INSTALL)                           |
| INSTALL             | /opt/homebrew/bin/ginstall -c        |
| INIT_OBJS           | $(OBJDIR)/erl_main.o $(PRELOAD_OBJS) |
| OBJS                | $(PROF_OBJS)                         |

`ginstall (source) (dest)` : `source` を `dest` にコピーする
`-c`オプション: 無視する

```plantuml
@startwbs
title cd erts/emulator && make -f $(TARGET)/Makefile TYPE=opt
* all
** $(BINDIR)/$(EMULATOR_EXECUTABLE) = $(BINDIR)/$(PRIMARY_EXECUTABLE)
*** $(BINDIR)/$(FLAVOR_EXECUTABLE)
**** $(INIT_OBJS)
***** erl_main.o
****** erts/emulator/sys/unix/erl_main.c
**** $(OBJS)
**** $(DEPLIBS)
**** $(EMUL_LD) -o $(BINDIR)/$(FLAVOR_EXECUTABLE) $(PFOFILE_LDFLAGS) $(LDFLAGS) $(INIT_OBJS) $(OBJS) $(STATIC_NIF_LIBS) $(STATIC_DRIVER_LIBS) $(LIBS)
*** $(INSTALL_PROGRAM) $(BINDIR)/$(FLAVOR_EXECUTABLE) $(BINDIR)/$(PRIMARY_EXECUTABLE)
** $(BINDIR)/$(EMULATOR_LIB)
** $(UNIX_ONLY_BUILDS)
@endwbs
```
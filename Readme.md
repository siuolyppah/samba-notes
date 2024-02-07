# How To Debug `tevent`

1. download [tevent source code](https://www.samba.org/ftp/tevent/)
2. append below code to `def build(bld)` in file wscript:
   ```py
    bld.SAMBA_BINARY('echo_server',
                     source='echo_server.c',
                     deps='cmocka tevent',
                     install=True)
   ```

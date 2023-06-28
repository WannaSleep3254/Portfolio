
# Linux 시스템 종료
## Shell Command
``bash
    halt
    poweroff
    init 0
    shutdown -h now
```
## C / C++
```cpp
int (main) {
    system("/bin/sh -c shutdown -P now");
}
```
```cpp
int (main) {
    reboot(LINUX_REBOOT_CMD_POWER_OFF);
}
```
## Python

## Qt


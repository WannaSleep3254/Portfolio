
# Linux 시스템 종료
## Shell Command
### 시스템 종료
    ```bash
    halt
    poweroff
    init 0
    shutdown -h now
    ```
### 재부팅
    ```bash
    reboot
    init 6
    shutdown -r now
    ```
### shutdown 명령어 옵션

옵션|설명
------|-------
-k| 경고메시지 출력
-h| System shutdown
-r|
-f|

## 코드 
### C / C++
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
### Python
```python
import os
os.system("shutdown -r -t")
```
### Qt


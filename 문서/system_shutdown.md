# Linux 시스템 종료 - 수정 중
## 쉘 스크립트
### 시스템 종료
```Bash
halt
poweroff
init 0
shutdown -h now
```
### 재부팅
```Bash
reboot
init 6
shutdown -r now
```
### shutdown 명령어 옵션

옵션|설명
------|-------
-k| 경고 메시지만 출력하고 실제 shutdown은 하지 않음
-h| shutdown이 완료된 후 시스템을 종료
-r| 시스템 종료 후 재부팅
-f| 재부팅할 때 fsck 명령을 건너뛰고 빠르게 진행

## 코드 내 사용
### C / C++
```cpp
#include <stdlib.h>
int (main) {
    system("/bin/sh -c shutdown -P now");
}
```
```cpp
#include <stdlib.h>
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
```python
#include <QProcess>
QProcess process;
process.startDetached("shutdown -P now");
```


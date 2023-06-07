
## 시리얼 라이브러리
[https://github.com/wjwwood/serial.git](https://github.com/wjwwood/serial.git)

## 파일경로
### Default rules
Path: ``` /lib/udev/rules.d ```
### Temporary rules
Path: ``` /dev/.udev/rules.d ```
### user-cumstom ruels (*)
Path: ``` /etc/udev/rules.d ```
## 포트 권한설정
### sol-1) su 권한으로 실행
```Bash
sudo 
```

### sol-2) 포트 사용권한 허용
```Bash
sudo chmod 666 /dev/ttyACM0
```
### sol-3) 유저그룹 추가 [RECOMMAND]
```Bash
sudo usermod -a -G dialout $USER
```



## USB 포트 고정 or simulink 연결
 ### step-1) check VID & PID of device
```Bash
lsbusb
```
 ### step-2) check UID of device
```Bash
udevadm info -a -n /dev/ttyUSB* | grep '{serial}' | head -n1
```
 ### step-3) make RULE of simulink
1. 파일 생성

```Bash
cd /etc/udev/rules.d
sudo nano 99-usb-serial.rules
```
</br>
2. 포트입력

```Bash
SUBSYSTEM=="tty", ATTRS{idVendor}=="2341", ATTRS{idProduct}=="0043", ATTRS{serial}=="8573531333335161B142", SYMLINK+="arduino"
```
### step-4) rload rules
```Bash
udevadm control --reload-rules && udevadm trigger
```

### step-5) replug USB PORT & check result
 ```Bash
 ls -l /dev/arduino
 ```

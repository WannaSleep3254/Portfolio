
## 라이브러리 설치
[https://github.com/wjwwood/serial.git](https://github.com/wjwwood/serial.git)

## 포트 권한
### sol-1) su 권한으로 실행
    $ sudo
### sol-2) 포트 사용권한 허용
    $ sudo chmod 666 /dev/ttyACM0
### sol-3) 유저그룹 추가 [RECOMMAND]
    $ sudo usermod -a -G dialout $USER



## USB 포트 고정 or simulink 연결
 ### step-1) check VID & PID of device
    $ lsbusb
 ### step-2) check UID of device
    $ udevadm info -a -n /dev/ttyUSB* | grep '{serial}' | head -n1
 ### step-3) make RULE of simulink
    $ cd /etc/udev/rules.d
    $ nano 99-usb-serial.rules
    
    ```
    SUBSYSTEM=="tty", ATTRS{idVendor}=="2341", ATTRS{idProduct}=="0043", ATTRS{serial}=="8573531333335161B142", SYMLINK+="arduino"
    ```
 ### step-4) replug USB PORT & check result
         - ex) ls -l /dev/arduino

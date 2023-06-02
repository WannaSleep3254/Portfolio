
## 라이브러리 설치
[https://github.com/wjwwood/serial.git)](https://github.com/wjwwood/serial.git)

## 포트 권한
 * sol-1) excute by su permision
        - ex) $ sudo
 * sol-2) chage permission
        - ex) $ sudo chmod 666 /dev/ttyACM0
 * sol-3) *Add user-group* [RECOMMAND]
        - ex) $ sudo usermod -a -G dialout $USER



## USB 포트 고정 or simulink 연결
 * step-1) check device
         - ex) $ lsbusb
 * step-2) check UID
         - ex) $ udevadm info -a -n /dev/ttyUSB* | grep '{serial}' | head -n1
 * step-3) make RULE_simulink
         - ex) $ cd /etc/udev/rules.d
               $ nano 99-usb-serial.rules
                 SUBSYSTEM=="tty", ATTRS{idVendor}=="2341", ATTRS{idProduct}=="0043", ATTRS{serial}=="8573531333335161B142", SYMLINK+="arduino"
 * step-4) replug USB PORT & check result
         - ex) ls -l /dev/arduino

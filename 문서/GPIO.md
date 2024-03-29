# GPIO on ROS2
## 개발환경
|항목|내용|
|---|---|
|Host Board|OrangePI 5|
|OS|Ubuntu22.04|
|Library|wiringOP, lgGPIO|

## ISSUE
### permission denied
At use [https://github.com/orangepi-xunlong/wiringOP](wiringOp) (custom package of `wiringPi` package),
`wiringPiSetup()` required `sudo` permission.  
In the Forum, Several people have the same problem. https://github.com/orangepi-xunlong/wiringOP/issues/28  
Not Only Ubuntu, also Armbian and other OS.

## What is the Ploblem ??
wiringPi.h
```c
extern void wiringPiVersion	(int *major, int *minor) ;
extern int  wiringPiSetup       (void) ;
extern int  wiringPiSetupSys    (void) ;
extern int  wiringPiSetupGpio   (void) ;
extern int  wiringPiSetupPhys   (void) ;
```
wiringPi.c
```c

extern int  wiringPiSetup       (void){
...
    if ((fd = open ("/dev/mem", O_RDWR | O_SYNC | O_CLOEXEC)) < 0){
        if ((fd = open ("/dev/gpiomem", O_RDWR | O_SYNC | O_CLOEXEC) ) >= 0){  // We're using gpiomem
                  piGpioBase = 0 ;
                  usingGpioMem = TRUE ;
    }
    else
        return wiringPiFailure (WPI_ALMOST, "wiringPiSetup: Unable to open /dev/mem or /dev/gpiomem: %s.\n"
        " Aborting your program because if it can not access the GPIO\n"
        " hardware then it most certianly won't work\n"
        " Try running with sudo?\n", strerror (errno)) ;
    }
}
...
```
![image](https://github.com/WannaSleep3254/Portfolio/assets/31496296/c696b119-4533-4bca-9375-773472006aaa)

### mMap matching
```c
int physToGpio_5[64] =
{
    -1,       // 0
    -1, -1,   // 1, 2
    47, -1,   // 3, 4
    46, -1,   // 5, 6
    54, 131,   // 7, 8
    -1, 132,   // 9, 10
    138, 29,   // 11, 12
    139, -1,   // 13, 14
    28, 59,   // 15, 16
    -1, 58,   // 17, 18
    49, -1,   // 19, 20
    48, 92,   // 21, 22
    50, 52,   // 23, 24
    -1, 35,   // 25, 26
    -1, -1,   // 27, 28
    -1, -1,   // 29, 30
    -1, -1,   // 31, 32
    -1, -1,   // 33, 34
    -1, -1,   // 35, 36
    -1, -1,   // 37, 38
    -1, -1,   // 39, 40

//Padding:
  -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1,   // ... 56
  -1, -1, -1, -1, -1, -1, -1,   // ... 63
};

int pinToGpio_5[64] =
{
    47,  46,      // 0, 1
    54, 131,      // 2, 3
    132,138,      // 4  5
    29, 139,      // 6, 7
    28,  59,      // 8, 9
    58,  49,      //10,11
    48,  92,      //12,13
    50,  52,      //14,15
    35,  -1,      //16,17
    -1,  -1,      //18,19
    -1,  -1,      //20,21
    -1,  -1,      //22,23
    -1,  -1,      //24,25
    -1,  -1,      //26,27
    -1,  -1,      //28,29
    -1,  -1,      //30,31

    -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, // ... 47
    -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1,// ... 63
};

static int ORANGEPI_PIN_MASK_5[5][32] =  //[BANK]	[INDEX]
{
    {-1,-1,-1,-1,-1,-1,-1,-1, -1,-1,-1,-1,-1,-1,-1,-1, -1,-1,-1,-1,-1,-1,-1,-1, -1,-1,-1,-1, 4, 5,-1,-1,},//GPIO0
    {-1,-1,-1, 3,-1,-1,-1,-1, -1,-1,-1,-1,-1,-1, 6, 7,  0, 1, 2,-1, 4,-1, 6,-1, -1,-1, 2, 3,-1,-1,-1,-1,},//GPIO1
    {-1,-1,-1,-1,-1,-1,-1,-1, -1,-1,-1,-1,-1,-1,-1,-1, -1,-1,-1,-1,-1,-1,-1,-1, -1,-1,-1,-1, 4,-1,-1,-1,},//GPIO2
    {-1,-1,-1,-1,-1,-1,-1,-1, -1,-1,-1,-1,-1,-1,-1,-1, -1,-1,-1,-1,-1,-1,-1,-1, -1,-1,-1,-1,-1,-1,-1,-1,},//GPIO3
    {-1,-1,-1, 3, 4,-1,-1,-1, -1,-1, 2, 3, 4,-1,-1,-1, -1,-1,-1,-1,-1,-1,-1,-1, -1,-1,-1,-1,-1,-1,-1,-1,},//GPIO4
};

```
## try using GPIOD
1. C++
```Bash
sudo apt install gpiod
cd Gcc_ws/
g++ -o blink blink.cpp -lgpiodcxx
```
2. Python
```Bash
sudo apt install  python3-pip
python3 -m pip install -U --user pip gpiod
python3 gpioget.py 
```

## try using PIGPIO
1. Downliad library
```Bash
wget https://github.com/joan2937/pigpio/archive/master.zip
unzip master.zip
```
2. Build
```Bash
cd pigpio-master/
make
sudo make install 
```

## try using lgGPIO - lgpio library
### Obtaining permission for /dev/gpiochip[0~4]
1. create group `gpio` & add group permission
```Bash
sudo groupadd gpio
sudo usermod -a -G gpio $USER
```
2.  add rule for `/dev/gpiochip[0~4]` of group `gpio`
```Bash
cd /etc/udev/rules.d/
sudo nano 60-gpiod.rules
```
* /etc/udev/rules.d/60-gpiod.rules
```Bash
# udev rules for gpio port access through libgpiod
SUBSYSTEM=="gpio", KERNEL=="gpiochip[0-4]", GROUP="gpiod", MODE="0660"
```
3. reload rules & check permission
```Bash
udevadm control --reload-rules && udevadm trigger
sudo udevadm control --reload-rules && udevadm trigger
# reboot
```
## Reference
https://askubuntu.com/questions/1352726/how-do-i-use-pi4s-gpio-pins-with-ubuntu-20-04  
</br>
https://waldorf.waveform.org.uk/2021/the-pins-they-are-a-changin.html  
</br>
https://forum.armbian.com/topic/26302-wiringpi-gpio-for-orange-pi-5-armbian/  
</br>
https://blog.mbedded.ninja/programming/operating-systems/linux/linux-serial-ports-using-c-cpp/#writing  
</br>
http://wiringpi.com/reference/serial-library/  
</br>
http://abyz.me.uk/lg/index.html  
http://abyz.me.uk/lg/lgpio.html  
https://github.com/joan2937/lg  
</br>
https://git.kernel.org/pub/scm/libs/libgpiod/libgpiod.git/about/  

# GPIO on ROS2
## 개발환경
Board: OrangePI5  
OS: Ubuntu22.04  
Library: wiringOP

## ISSUE
### permission denied
At use [https://github.com/orangepi-xunlong/wiringOP](wiringOp) (custom package of `wiringPi` package),
`wiringPiSetup()` required `sudo` permission.  
In the Forum, Several people have the same problem. https://github.com/orangepi-xunlong/wiringOP/issues/28  
Not Only Ubuntu, also Armbian and other OS.

## wiringOp CODE Analysis
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
...
```

https://askubuntu.com/questions/1352726/how-do-i-use-pi4s-gpio-pins-with-ubuntu-20-04

https://waldorf.waveform.org.uk/2021/the-pins-they-are-a-changin.html

https://forum.armbian.com/topic/26302-wiringpi-gpio-for-orange-pi-5-armbian/

https://blog.mbedded.ninja/programming/operating-systems/linux/linux-serial-ports-using-c-cpp/#writing

http://wiringpi.com/reference/serial-library/


http://abyz.me.uk/lg/index.html
http://abyz.me.uk/lg/lgpio.html
https://github.com/joan2937/lg

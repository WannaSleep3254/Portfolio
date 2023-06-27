# How to use Autostart - Raspberry Pi OS (Desktop) 

https://forums.raspberrypi.com/viewtopic.php?t=294014

+ Note the settings and defaults described below are found in the Raspbian 'Buster' and later RPi OS releases.  
+ Raspbian 'Stretch' and before releases used different defaults as noted below.
## Autostart
The autostart feature can be used to automatically start just about any app, script or command at boot (User login). Requires boot to Desktop (GUI) enabled.  
Autostart(자동시작)기능은 부팅 후, 사용자 로그인 단계에서 거의 모든 앱, 스크립트 또는 명령을 자동으로 시작하는 데 사용할 수 있습니다.
데스크톱으로 부팅(GUI)이 활성화되어야 합니다.  

Autostart is particularly useful when you need to run Desktop apps or any scripts which require Desktop (GUI).  

Other methods such as rc.local or cron @reboot do not easily handle GUI programs as they attempt to start the app before the Desktop in ready resulting in failure to open.  

There are 4 autostart methods or options:
분류|설명
------|-------
• System (All Users) |**Default in 'Buster' and later OS releases**
• User (Specific Users) |**Default in 'Stretch' and earlier OS releases**
• Traditional (All Users) |with .desktop files
• Traditional (Specific User)| with .desktop files

## System Method:
The System method is easiest as the required autostart file is already present and commands can easily be added with a simple edit. By default, the System autostart is applied to all users and executes each time a user logs in.  
The System autostart file is located here:  **/etc/xdg/lxsession/LXDE-pi/**

To open System autostart using the nano editor:

```bash
sudo nano /etc/xdg/lxsession/LXDE-pi/autostart
```
See Using the Autostart File & Sample Autostart Files below.


## User Method:
The user method works much the same as the System method but allows for a unique or custom autostart file for each user. If there is only one user (pi) or your multiple users do not require different apps or scripts started at login / boot, then there is no advantage to doing the user method.  

The user autostart file and associated path does not exist by default.  

The pi user autostart needs to be located here: **/home/pi/.config/lxsession/LXDE-pi/** (If not user pi then substitute your username for pi **/home/{user}/.config/lxsession/LXDE-pi/**).  

You will first need to create the lxsession and LXDE-pi sub directories then copy the System autostart to the user(s) location(s).
```bash
mkdir /home/pi/.config/lxsession
mkdir /home/pi/.config/lxsession/LXDE-pi
cp /etc/xdg/lxsession/LXDE-pi/autostart /home/pi/.config/lxsession/LXDE-pi/
```
Note: If a user autostart file exists at **/home/pi/.config/lxsession/LXDE-pi**, then the System autostart file is totally ignored (for that user).  

To open the User autostart using the nano editor:
```bash
nano /home/pi/.config/lxsession/LXDE-pi/autostart
```
See Using the Autostart File & Sample Autostart Files below.  

Using the Autostart File (System or User):  

### The default System autostart file:
```bash
@lxpanel --profile LXDE-pi
@pcmanfm --desktop --profile LXDE-pi
@xscreensaver -no-splash
```
• The first 2 lines are important and must not be removed and must also be present if doing the User method. If you have made an autostart file without these lines, the desktop will boot to a blank (Openbox) screen. To recover, right click anywhere on the screen and select terminal from the menu. Then edit autostart and add those 2 lines.

• The xscreensaver command is only relevant if you have installed xscreensaver. If not then it can be removed or just ignored.

• The @ is optional. With @ present, if there is an error on first try, the system will attempt to run the command up to 4 more times before giving up.

• Note that the autostart file is not a bash script and is processed differently. The commands in the autostart file are processed independently at near the same time in a parallel fashion. Or the system does not wait for the previous command to complete before starting the next command. If you have multiple commands that are dependent on being run in a specific order then you would need to put your commands in a bash script, then call your bash script from autostart.

• All programs launched by autostart run in the background so no need to use & at the end of the command.

• It is important to use the complete paths to scripts in autostart. Also calls within a script need to have the complete path specified. Paths to installed apps are not required.

• Scripts must be made executable.

• If your script requires keyboard and/or console interaction, then use the lxterminal -e command. See examples and notes below.

• If your command or app requires network and does not seem to work from autostart, then try enabling the wait for network at boot option in sudo raspi-config

• In some rare cases you may need to add a delay before starting your script to give the Desktop more time to complete the booting process before your app starts. This can be done with a bash sleep command. Do not attempt to add the sleep command directly in autostart, it won’t work. Use the sleep command in a bash script instead.

### Sample Autostart Files:
1. Example to start Calculator:
	```bash
	@lxpanel --profile LXDE-pi
	@pcmanfm --desktop --profile LXDE-pi
	@xscreensaver -no-splash
	@galculator
	```
2. Example to start browser:
	```bash
	@lxpanel --profile LXDE-pi
	@pcmanfm --desktop --profile LXDE-pi
	@xscreensaver -no-splash
	@chromium-browser www.raspberrypi.org
	```
3. Example to start a Python 3 script:
	```bash
	@lxpanel --profile LXDE-pi
	@pcmanfm --desktop --profile LXDE-pi
	@xscreensaver -no-splash
	@python3 /path/my_script.py
	```
4. Example to start a Python 3 script with terminal:
	```bash
	@lxpanel --profile LXDE-pi
	@pcmanfm --desktop --profile LXDE-pi
	@xscreensaver -no-splash
	@lxterminal -e python3 /path/my_script.py
	```
5. Example to start a Bash script:
	```bash
	@lxpanel --profile LXDE-pi
	@pcmanfm --desktop --profile LXDE-pi
	@xscreensaver -no-splash
	@bash /path/my_script
	```
6. Example to start a Bash script with terminal:
	```bash
	@lxpanel --profile LXDE-pi
	@pcmanfm --desktop --profile LXDE-pi
	@xscreensaver -no-splash
	@lxterminal -e bash /path/my_script
	```
7. Example to open Lxterminal without running a command:
	```bash
	@lxpanel --profile LXDE-pi
	@pcmanfm --desktop --profile LXDE-pi
	@xscreensaver -no-splash
	@lxterminal
	```
Notes on using `lxterminal -e`:  
(Notes below apply when lxterminal -e is used directly in autostart file. The noted behavior may different than what happens when run from the terminal command line).  

No ‘ or “ in the path/filename to a script. This means that paths or filenames with spaces cannot be used. Use bash script instead.  

Only 1 command per line. (Cannot use ; to specify multiple commands). Put multiple commands in bash script instead.  

If the script or command exits to the command line for any reason then the terminal is immediately closed. This means if there is an error or the program terminates then you will not be able to see what happened as the terminal window will flash by too quickly.  


## Traditional System Method (All Users):
Beginning with the Dec 2020 release, Raspberry Pi OS now uses the `/etc/xdg/autostart` directory to start some background apps for printer etc. You can use this directory to start apps or scripts which will apply to all users.  
Note that autostart here is a directory and not a file.  
This method does not use an autostart file. It uses filename.desktop files instead. See example .desktop file below.  

## Traditional User Method (Specific User):  
The traditional method also has a user based option. It requires your filename.desktop file(s) to be located here for pi user /home/pi/.config/autostart/ or for other user **/home/{user}/.config/autostart/**  
You may need to create the auotstart directory if not present  

Note that the System autostart file OR User autostart file if present, is run and processed along with the .desktop file(s).  

In addition the `.desktop` files for system **/etc/xdg/autostart** and the .desktop files in the users home directory **/home/pi/.config/autostart/** will all be processed.

Example .desktop file to start File Manager:
```desktop
[Desktop Entry]
Name=File Manager
Exec=pcmanfm
Type=Application
```
Give the file a unique name such as pcm.desktop and place it in /etc/xdg/autostart for system wide all users or **/home/pi/.config/autostart** for specific user.  
You can have multiple `.desktop` files.

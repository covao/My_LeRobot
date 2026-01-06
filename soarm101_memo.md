# SOARM101 Setup Notes on WSL2

## Attach USB Device to WSL2
~~~Powershell
usbipd list | Select-String "CH343" | ForEach-Object {
    $busid = $_.Line.Split()[0]
    usbipd attach --wsl --busid $busid
}
~~~
## Check attached USB Devices in WSL2
- When a USB device is attached to WSL2, it becomes unavailable on the Windows side and can only be used on the Ubuntu side.
~~~Powershell
usbipd list
~~~
**Output Example:**

7-1    1a86:55d3  USB-Enhanced-SERIAL CH343 (COM4)                              Attached
7-2    1a86:55d3  USB-Enhanced-SERIAL CH343 (COM3)                              Attached


## Check USB Devices in Ubuntu 22.04
~~~bash
lsusb
dmesg | grep -i tty
~~~

**Output Example:**
~~~
(base) ubuntu@DESKTOP-E93UROG:~$ lsusb
dmesg | grep -i tty
Bus 002 Device 001: ID 1d6b:0003 Linux Foundation 3.0 root hub
Bus 001 Device 003: ID 1a86:55d3 QinHeng Electronics USB Single Serial
Bus 001 Device 002: ID 1a86:55d3 QinHeng Electronics USB Single Serial
Bus 001 Device 001: ID 1d6b:0002 Linux Foundation 2.0 root hub
[  107.048663] cdc_acm 1-1:1.0: ttyACM0: USB ACM device
[  108.633872] cdc_acm 1-2:1.0: ttyACM1: USB ACM device
(base) ubuntu@DESKTOP-E93UROG:~$
~~~
## Miniconda Activation
~~~bash
conda activate lerobot
~~~

## Setup SOARM101 Follower Motor (Only first time)
~~~bash
lerobot-setup-motors \
    --robot.type=so101_follower \
    --robot.port=/dev/ttyACM0  # <- paste here the port found at previous step
~~~

## Setup SOARM101 Leader Motor (Only first time)
~~~bash
lerobot-setup-motors \
    --teleop.type=so101_leader \
    --teleop.port=/dev/ttyACM1  # <- paste here the port found at previous step
~~~
## Servo range calibration notes
- Before starting calibration, set all joints to the center position.
- If the minimum value is 0 or the maximum value is reversed, the calibration may fail. In that case, adjust the servo mounting position.

## Calibrate SOARM101 Follower Arm
### Remove SOARM101 Follower Calibration Files

- To re-calibrate, please delete existing calibration files with the following command beforehand.
~~~bash
rm -rf ~/.cache/huggingface/lerobot/calibration/robots/so101_follower/*
~~~
### Follower Arm Calibration
~~~bash
lerobot-calibrate --robot.type=so101_follower --robot.port=/dev/ttyACM0 --robot.id=my_awesome_follower_arm
~~~


##  Calibrate SOARM101 Leader Arm 
### Remove SOARM101 Leader Calibration Files
~~~bash
rm -rf ~/.cache/huggingface/lerobot/calibration/teleoperators/so101_leader/*
~~~
### Leader Arm Calibration
~~~bash
lerobot-calibrate --teleop.type=so101_leader --teleop.port=/dev/ttyACM1 --teleop.id=my_awesome_leader_arm
~~~

## Teleoperate SOARM101 Follower Arm with Leader Arm
~~~bash
lerobot-teleoperate \
    --robot.type=so101_follower \
    --robot.port=/dev/ttyACM0 \
    --robot.id=my_awesome_follower_arm \
    --teleop.type=so101_leader \
    --teleop.port=/dev/ttyACM1 \
    --teleop.id=my_awesome_leader_arm
~~~

## References
- seeedstudio LEROBOT SOARM101
https://wiki.seeedstudio.com/lerobot_so100m
- GitHub Repository
https://github.com/TheRobotStudio/SO-ARM100/
- Feetech STS3215 Servo Motor Software
https://www.feetechrc.com/software.html
- Connecting USB Devices to WSL2    
https://learn.microsoft.com/windows/wsl/connect-usb






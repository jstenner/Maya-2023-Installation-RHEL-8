# Maya-2023-Installation-RHEL-8

## Autodesk Instructions Followed
- [Install Maya on Linux using the rpm Package](https://help.autodesk.com/view/MAYAUL/2023/ENU/?guid=GUID-E7E054E1-0E32-4B3C-88F9-BF820EB45BE5)
- [Additional required Linux packages for Maya installation](https://help.autodesk.com/view/MAYAUL/2023/ENU/?guid=GUID-D2B5433C-E0D2-421B-9BD8-24FED217FD7F)

## Steps Attempted for Installation
1. Start with fresh RHEL 8 Workstation installation
2. Run:
```bash
sudo dnf update
`
3. Download Autodesk Maya 2023 Installer
The downloaded file is named:
```
Autodesk_Maya_2023_ML_Linux_64bit.tar
```

4. Extract the Installer
```bash
tar xvf Autodesk_Maya_2023_ML_Linux_64bit.tar

#lines removed for brevity

[brenden@errmac Maya]$ ls
manifest  MayaConfig.pit  ODIS  Packages  Setup  SetupRes  setup.xml

cd Packages
```

5. Install ADLM
```
sudo yum install adlmapps25-25.0.3-0.x86_64.rpm
```

6. Install ADSK Licensing 
```
sudo yum install adsklicensing12.0.0.6537-0-0.x86_64.rpm
```

This installs and configures a `systemd` service as seen below:
![adsklicensing systemd service screenshot](https://github.com/bxbrenden/Maya-2020-Installation-CentOS-8/blob/master/adsklicensing.png)

7. Install ADLM Flexnet IPV6 Server
```bash
sudo yum install adskflexnetserverIPV6-11.18.0-0.x86_64.rpm
```

8. Install ADLM Flexnet Client
```bash
sudo yum install adskflexnetclient-11.16.0-0.x86_64.rpm
```

9. Install the Maya rpm
```bash
sudo yum install Maya2023_64-2023.0-1319.x86_64.rpm
```

10. Verify Maya installation
```bash
[brenden@errmac Packages]$ /opt/Autodesk/AdskLicensing/Current/helper/AdskLicensingInstHelper list
[
  {
    "feature_id": "MAYA",
    "def_prod_key": "<REDACTED>",
    "def_prod_ver": "2020.0.0.F",
    "sel_prod_key": "<REDACTED>",
    "sel_prod_ver": "2020.0.0.F",
    "supported_lic_methods": [
      2,
      1,
      4
    ],
    "lic_servers": [
      ""
    ],
    "serial_number_sa": "000-00000000",
    "serial_number_nw": "000-00000000",
    "def_prod_code": "MAYA",
    "sel_prod_code": "MAYA"
  }
]
```

11. Install Dependencies
```bash
sudo yum install xorg-x11-fonts-ISO8859-1-100dpi
sudo yum install xorg-x11-fonts-ISO8859-1-75dpi 
sudo dnf install epel-release.noarch   #yum is just a symlink to dnf
sudo dnf makecache
sudo yum install audiofile audiofile-devel
```

12. Attempt to start Maya

Run fails due to missing `libpng15.so.15`.
```bash
[brenden@errmac Packages]$ which maya
/usr/local/bin/maya
[brenden@errmac Packages]$ maya
/usr/autodesk/maya2023/bin/maya.bin: error while loading shared libraries: libpng15.so.15: cannot open shared object file: No such file or directory
```

13. Install libpng15.so.15 and try to start Maya

Run fails due to missing `libGLU.so.1`
```bash
[brenden@errmac Packages]$ sudo yum install libpng15-1.5.30-7.el8.x86_64

[brenden@errmac Packages]$ maya
/usr/autodesk/maya2023/bin/maya.bin: error while loading shared libraries: libGLU.so.1: cannot open shared object file: No such file or directory
```

14. Install libGLU and try to start Maya

[brenden@errmac Packages]$ sudo yum install mesa-libGLU

Maya installation wizard starts this time!

[brenden@errmac Packages]$ maya
```
![Maya starts after installing dependencies](https://github.com/bxbrenden/Maya-2020-Installation-CentOS-8/blob/master/maya-starts.png)

15. Click `Single-User` to register license (interface different in Maya 2023...but same idea!)

Maya crashes with an error:
```bash
[brenden@errmac Packages]$ maya
maya: Autodesk Maya 2020
A licensing error occurred that Autodesk systems were not able to handle for you. Please contact Autodesk Customer Support for help in resolving this error.
adlsdkAuthorize returned with error code: ADLSDK_STATUS_LICENSE_CHECKOUT_ERROR
The default location for log files to help diagnose the issue is: /usr/tmp
```

16. Check logs for failure details (log name differend in Maya 2023)

Contents of `/usr/tmp/MayaCLM-25-06-2020.log`:
```
2020-06-25 17:44:11: ====== BEGIN MAYA CLM LICENSE DIAGNOSTICS (level 2) ======
2020-06-25 17:44:11: INFO: MAYA VERSION: Autodesk Maya 2020
2020-06-25 17:44:11: INFO: Desktop Licensing Runtime Version: 2.6.3.31
2020-06-25 17:44:11: INFO: Desktop Licensing API version in Maya: 2.6.3.31
2020-06-25 17:44:11: INFO: initialize: GetInstance (Success)
2020-06-25 17:45:22: ====== BEGIN MAYA CLM LICENSE DIAGNOSTICS (level 2) ======
2020-06-25 17:45:22: INFO: MAYA VERSION: Autodesk Maya 2020
2020-06-25 17:45:22: INFO: Desktop Licensing Runtime Version: 2.6.3.31
2020-06-25 17:45:22: INFO: Desktop Licensing API version in Maya: 2.6.3.31
2020-06-25 17:45:22: INFO: initialize: GetInstance (Success)
2020-06-25 17:45:27: INFO: Received LicenseUpdateCallback for Maya 2020 (ADLSDK_UPDATE_REASON_LICENSE_UPDATE): NOT Authorized
2020-06-25 17:45:27: ERROR: Error Information: Maya 2020 : command execution error
2020-06-25 17:45:27: ERROR: adlsdkAuthorize returned with error code: ADLSDK_STATUS_LICENSE_CHECKOUT_ERROR
2020-06-25 17:45:27: ERROR: checkinLicense: unable to release authentication handle
2020-06-25 17:45:27: INFO: shutdown: normal exit
```

17. Click `enter a serial number` to try alternate route

Clicked `enter a serial number` and got an error:
```
Unable to initialize adlm.
Internal Error Message: <Service not installed.>
Error Code: <20>
```
![adlm init error](https://github.com/bxbrenden/Maya-2020-Installation-CentOS-8/blob/master/adlm-failed-init.png)

18. Read troubleshooting steps from Autodesk Forums
https://forums.autodesk.com/t5/installation-licensing/maya-2020-centos-7-7-unable-to-initialize-adlm/td-p/9223338

19. Try manual installation of Flexnet toolkit

Running the two scripts suggested in the [forum post](https://forums.autodesk.com/t5/installation-licensing/maya-2020-centos-7-7-unable-to-initialize-adlm/td-p/9223338) seems to indicate the Flexnet service is not running:
```bash
[brenden@errmac bin]$ sudo /opt/Autodesk/Adlm/FLEXnet/11.16.0.0/bin/toolkitinstall.sh

Checking system...
Configuring for Linux, Trusted Storage path /usr/local/share/macrovision/storage...
/usr/local/share/macrovision/storage already exists...
Setting permissions on /usr/local/share/macrovision/storage...
Permissions set...
Configuration completed successfully.

[brenden@errmac bin]$ sudo ./install_fnp.sh 
ERROR: Unable to locate anchor service to install, please specify correctly on command line
```

20. Try to run Flexnet service manually
Running the service returns the confusing error 'No such file or directory` even though it exists.

This [StackOverflow post](https://stackoverflow.com/questions/2716702/no-such-file-or-directory-error-when-executing-a-binary) indicates there may be missing libraries.
```bash
[brenden@errmac bin]$ pwd
/opt/Autodesk/Adlm/FLEXnet/11.16.0.0/bin
[brenden@errmac bin]$ ls
FNPLicensingService  install_fnp.sh  toolkitinstall.sh
[brenden@errmac bin]$ sudo ./FNPLicensingService 
sudo: unable to execute ./FNPLicensingService: No such file or directory
[brenden@errmac bin]$ file FNPLicensingService 
FNPLicensingService: ELF 64-bit LSB executable, x86-64, version 1 (SYSV), dynamically linked, interpreter /lib64/ld-lsb-x86-64.so.3, for GNU/Linux 2.6.18, stripped
```

21. Try to find missing libraries
Using the `readelf -a` command and grepping (ignore case) for `request` finds that the `ld-lsb-x86-64.so.3` file is missing.
```bash
[brenden@errmac bin]$ readelf -a FNPLicensingService  | grep -i request
      [Requesting program interpreter: /lib64/ld-lsb-x86-64.so.3]
[brenden@errmac bin]$ file /lib64/ld-lsb-x86-64.so.3
/lib64/ld-lsb-x86-64.so.3: cannot open `/lib64/ld-lsb-x86-64.so.3' (No such file or directory)

[brenden@errmac bin]$ sudo dnf whatprovides "*/ld-lsb-x86-64.so.3"
Last metadata expiration check: 0:37:12 ago on Thu 25 Jun 2020 05:32:38 PM PDT.
redhat-lsb-core-4.1-47.el8.x86_64 : LSB Core module support
Repo        : AppStream
Matched from:
Filename    : /lib64/ld-lsb-x86-64.so.3
```

22. Install `redhat-lsb-core` and try again
`FNPLicensingService` runs successfully!
```bash
[brenden@errmac bin]$ sudo dnf install redhat-lsb-core-4.1-47.el8.x86_64

[brenden@errmac bin]$ sudo ./FNPLicensingService 
FNPLicensingService -{rkhs}
   -r    Run the FNPLicensingService daemon
	-k    Kill the FNPLicensingService daemon
   -s    Print the status of the  FNPLicensingService daemon
   -h    Print help information

[brenden@errmac bin]$ sudo ./FNPLicensingService -r
Licensing Service daemon activated

[brenden@errmac bin]$ ps -ef | grep -i fnp
root       46841    6954  0 18:17 ?        00:00:00 ./FNPLicensingService -r
brenden    46864   16926  0 18:17 pts/0    00:00:00 grep --color=auto -i fnp
```

23. Try re-running Flexnet tools manually

Running `sudo ./install_fnp.sh` still fails with the `missing anchor service` error. 

So, I tried running `sudo ./install_fnp.sh --help` to see if there were help docs.

The help looked like this:
```bash
install_fnp.sh [--help] [--cert] [--nolsb] [--nodaemon] [ <path to FNLS> ]
```
 The `[ <path to FNLS> ] option looked promising, so I pointed it at the `FNPLicensingService` file that was now able to run:
```bash
[brenden@errmac bin]$ sudo ./install_fnp.sh `pwd`/FNPLicensingService

Checking for active FNP Licensing Service Daemon...
Need to restart FNP Licensing Service Daemon
Licensing Service daemon terminated

FNP Licensing Service Daemon checks complete

Checking for SELinux
*** WARNING: Running with SELINUX enabled can affect the operation of the FlexNet Licensing Service
***          Refer to the FlexNet Publisher Documentation for further details
SELinux checks complete

Checking LSB compatibility...
LSB compatibility checks complete

Installing licensing service from /opt/Autodesk/Adlm/FLEXnet/bin/FNPLicensingService to /usr/local/share/FNP/service64/11.16.0

Checking system for trusted storage area...
Configuring for Linux, Trusted Storage path /usr/local/share/macrovision/storage...
chattr: No such file or directory while trying to stat /usr/local/share/macrovision/storage/FLEXnet
chown: cannot access '/usr/local/share/macrovision/storage/FLEXnet': No such file or directory
/usr/local/share/macrovision/storage already exists...
Setting permissions on /usr/local/share/macrovision/storage...
Permissions set...


Checking system for Replicated Anchor area...
Configuring Replicated Anchor area...
Replicated Anchor area already exists...
Setting permissions on Replicated Anchor area...
Replicated Anchor area permissions set...
Configuring Temporary area...
Temporary area already exists...
Setting permissions on Temporary area...
Temporary area permissions set...
Setting FUSE configuration
ERROR: FlexNetFs mount-point inaccessible because of /dev/shm file permissions

Starting FNPLicensingService daemon owner by brenden
Licensing Service daemon activated

Checking FNPLicensingService is running
Configuration completed successfully.
```

24. Start Maya - mine crashed with error related to `dbus appears incorrectly setup`. Based on https://stackoverflow.com/questions/62711323/python3-gtk3-d-bus-library-appears-to-be-incorrectly-set-up-failed-to-read-ma one solution is to run:
```sudo dbus-uuidgen --ensure```

25. Start Maya again and try clicking `enter a serial number` (Maya 2023 interface is different)

Clicking `enter a serial number` on the installation wizard now works!
![enter a serial number](https://github.com/bxbrenden/Maya-2020-Installation-CentOS-8/blob/master/enter-license-works.png)

26. Enter a serial number at the activation page

The page prompts you to activate Maya
![Maya activation page](https://github.com/bxbrenden/Maya-2020-Installation-CentOS-8/blob/master/license-activation.png)

27. Activation is successful

The wizard shows a success message. Click `Finish`
![](https://github.com/bxbrenden/Maya-2020-Installation-CentOS-8/blob/master/license-activated.png)

28. Maya starts!
![Maya starts](https://github.com/bxbrenden/Maya-2020-Installation-CentOS-8/blob/master/maya-finally-starts.png)

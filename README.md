# root 권한을 user 에게 전달하기

## 사용자 생성하기

### 1. 사용자 생성

```bash
useradd vdiadmin
```

### 2. 비밀번호 설정

```bash
passwd vdiadmin
```

## SUDO 실행권한 주기

### 1. 설정파일 백업

설정 파일을 수정하기 전에 항상 백업파일을 만들어 두고, 진행합니다.

```bash
# sudoers 백업파일 생성
cp /etc/sudoers /etc/sudoers.back
```

### 2. 백업파일 생성 확인

cp 명령어로 복사한 백업파일이 생겼는지 확인합니다.

```bash
# sudoers 백업파일 생성 확인
cd /etc
ls -al | grep sudoers
```

### 3. 읽기전용 권한 해제

기존에는 root 권한으로도 수정할 수 없도록 읽기전용 설정이 되어있기 때문에, 읽기전용 속성을 먼저 해제합니다.

```bash
# 읽기전용 속성 해제
chattr -i /etc/sudoers
```

### 4. 쓰기권한 부여

설정파일을 수정하기 위해서 쓰기권한을 부여합니다.

```bash
# 쓰기권한 추가
chmod u+w /etc/sudoers
```

### 5. 설정파일 수정

/sbin/modprobe, /sbin/rmmod, /sbin/lsmod 명령어에 대해서 비밀번호 없이 sudo 명령어로 실행할 수 있도록 NOPASSWD 옵션을 추가합니다.

```bash
# 수정
vi /etc/sudoers

# 하단에 내용 추가
vdiadmin  ALL=(ALL:ALL) NOPASSWD:/sbin/modprobe,/sbin/rmmod,/sbin/lsmod
```

**&lt; 수정 후 설정파일 내용 &gt;**

```bash
## Sudoers allows particular users to run various commands as
## the root user, without needing the root password.
##
## Examples are provided at the bottom of the file for collections
## of related commands, which can then be delegated out to particular
## users or groups.
##
## This file must be edited with the 'visudo' command.

## Host Aliases
## Groups of machines. You may prefer to use hostnames (perhaps using
## wildcards for entire domains) or IP addresses instead.
# Host_Alias     FILESERVERS = fs1, fs2
# Host_Alias     MAILSERVERS = smtp, smtp2

## User Aliases
## These aren't often necessary, as you can use regular groups
## (ie, from files, LDAP, NIS, etc) in this file - just use %groupname
## rather than USERALIAS
# User_Alias ADMINS = jsmith, mikem


## Command Aliases
## These are groups of related commands...

## Networking
# Cmnd_Alias NETWORKING = /sbin/route, /sbin/ifconfig, /bin/ping, /sbin/dhclient, /usr/bin/net, /sbin/iptables, /usr/bin/rfcomm, /usr/bin/wvdial, /sbin/iwconfig, /sbin/mii-tool

## Installation and management of software
# Cmnd_Alias SOFTWARE = /bin/rpm, /usr/bin/up2date, /usr/bin/yum

## Services
# Cmnd_Alias SERVICES = /sbin/service, /sbin/chkconfig, /usr/bin/systemctl start, /usr/bin/systemctl stop, /usr/bin/systemctl reload, /usr/bin/systemctl restart, /usr/bin/systemctl status, /usr/bin/systemctl enable, /usr/bin/systemctl disable

## Updating the locate database
# Cmnd_Alias LOCATE = /usr/bin/updatedb

## Storage
# Cmnd_Alias STORAGE = /sbin/fdisk, /sbin/sfdisk, /sbin/parted, /sbin/partprobe, /bin/mount, /bin/umount

## Delegating permissions
# Cmnd_Alias DELEGATING = /usr/sbin/visudo, /bin/chown, /bin/chmod, /bin/chgrp

## Processes
# Cmnd_Alias PROCESSES = /bin/nice, /bin/kill, /usr/bin/kill, /usr/bin/killall

## Drivers
# Cmnd_Alias DRIVERS = /sbin/modprobe

# Defaults specification

#
# Refuse to run if unable to disable echo on the tty.
#
Defaults   !visiblepw

#
# Preserving HOME has security implications since many programs
# use it when searching for configuration files. Note that HOME
# is already set when the the env_reset option is enabled, so
# this option is only effective for configurations where either
# env_reset is disabled or HOME is present in the env_keep list.
#
Defaults    always_set_home
Defaults    match_group_by_gid

# Prior to version 1.8.15, groups listed in sudoers that were not
# found in the system group database were passed to the group
# plugin, if any. Starting with 1.8.15, only groups of the form
# %:group are resolved via the group plugin by default.
# We enable always_query_group_plugin to restore old behavior.
# Disable this option for new behavior.
Defaults    always_query_group_plugin

Defaults    env_reset
Defaults    env_keep =  "COLORS DISPLAY HOSTNAME HISTSIZE KDEDIR LS_COLORS"
Defaults    env_keep += "MAIL PS1 PS2 QTDIR USERNAME LANG LC_ADDRESS LC_CTYPE"
Defaults    env_keep += "LC_COLLATE LC_IDENTIFICATION LC_MEASUREMENT LC_MESSAGES"
Defaults    env_keep += "LC_MONETARY LC_NAME LC_NUMERIC LC_PAPER LC_TELEPHONE"
Defaults    env_keep += "LC_TIME LC_ALL LANGUAGE LINGUAS _XKB_CHARSET XAUTHORITY"

#
# Adding HOME to env_keep may enable a user to run unrestricted
# commands via sudo.
#
# Defaults   env_keep += "HOME"

Defaults    secure_path = /sbin:/bin:/usr/sbin:/usr/bin

## Next comes the main part: which users can run what software on
## which machines (the sudoers file can be shared between multiple
## systems).
## Syntax:
##
##      user    MACHINE=COMMANDS
##
## The COMMANDS section may have other options added to it.
##
## Allow root to run any commands anywhere
root    ALL=(ALL)       ALL

## Allows members of the 'sys' group to run networking, software,
## service management apps and more.
# %sys ALL = NETWORKING, SOFTWARE, SERVICES, STORAGE, DELEGATING, PROCESSES, LOCATE, DRIVERS

## Allows people in group wheel to run all commands
%wheel  ALL=(ALL)       ALL

## Same thing without a password
# %wheel        ALL=(ALL)       NOPASSWD: ALL

## Allows members of the users group to mount and unmount the
## cdrom as root
# %users  ALL=/sbin/mount /mnt/cdrom, /sbin/umount /mnt/cdrom

## Allows members of the users group to shutdown this system
# %users  localhost=/sbin/shutdown -h now

## Read drop-in files from /etc/sudoers.d (the # here does not mean a comment)
#includedir /etc/sudoers.d

# Allows VDIADMIN Command
vdiadmin  ALL=(ALL:ALL) NOPASSWD:/sbin/modprobe,/sbin/rmmod,/sbin/lsmod
```

### 5. 쓰기권한 해제

```bash
# 쓰기권한 해제
chmod u-w /etc/sudoers
```

### 6. 읽기전용 속성 추가

```bash
# 읽기전용 속성 추가
chattr +i /etc/sudoers
```

## 사용자 계정으로 명령어 실행하기

### 1. 사용자 계정으로 접속

```bash
su vdiadmin
```

### 2. 명령어 실행

```bash
# modprobe
sudo modprobe bramdisk autoload=0

# rmmod
sudo rmmod /proc/bramdisk

# lsmod
sudo lsmod | grep bramdisk
```




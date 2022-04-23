---
layout: post
title: "[Linux] 리눅스 디렉토리 구조"
subtitle: "[Linux] 리눅스 디렉토리 구조"
categories: devlang
tags: linux
comments: true
mathjax: true
---
#### Contents
- [리눅스 디렉토리 구조](#리눅스-디렉토리-구조)
- [리눅스 주요 디렉토리](#리눅스-주요-디렉토리)

해당 포스팅에서는 pandas의 리눅스 디렉토리 구조에 대해서 정리하였습니다.

<br>

---

## <span style="color:navy"> 리눅스 디렉토리 구조 <span>

리눅스는 파일 관리의 효율성을 위해서 Filesystem Hierarchy Standard(FHS)라는 디렉토리 구조를 사용한다. 
그리고, 사용자의 설정에 따라서 변경될 수 있지만 각 파일은 용도 혹은 목적에 따라서 적합한 디렉토리에서 관리된다.
즉, 리눅스의 각 디렉토리는 용도에 따라서 구분되며, 해당 포스팅에서는 이에 관해서 간단히 정리하고자 한다.

<br>

## <span style="color:navy"> 리눅스 주요 디렉토리 <span>

### **/**
- 루트(`root`) 디렉토리라고 부른다.
- 모든 디렉토리의 최상위에 위치한다.
- 대부분의 디렉토리의 시작 지점이다.

<br>

### **/bin**
- 리눅스에서 실행 가능한 **기본 명령어들을 저장**한다.
  - 예를 들어, `mv`, `cd`, `cp`, ... 등과 같은 명령어들이 있다.
- root 사용자와 일반 사용자가 함께 사용할 수 있다. 

<br>

### **/sbin**
- `/bin`디렉토리와 달리 **시스템 운영에 필요한 명령어를 보관**한다.
- 예를 들어, ifconfig, reboot, fdisk 등이 있다.

<br>

### **/etc**
- os혹은 application의 동작 제어하는 **system configuration 파일들을 보관**하고 있는 디렉토리이다.
- 예를 들어, etc내의 configuration 파일로 os가 text mode로 부팅할지, graphical mode로 부팅할지 설정할 수 있다.
- 대표적으로는 사용자 관리를 설정하는 `/etc/passwd`가 있다.
- 그외
  - `/etc/opt/` : `/opt`에 대한 설정 파일
  - `/etc/xml/` : `/xml`에 대한 설정 파일
  - `/etc/cron*`: 크론 설정 파일
  
<br>

### **/home**
- 사용자 홈 디렉토리가 보관되는 디렉토리이다.
- 리눅스 시스템은 종종 다수의 유저 계정을 가지고 있다.<br> 이떄, **home은 각 유저들의 데이터를 구분할 수 있는 디렉토리**이다.
- home에는 음악, 문서, 그리고 사진 등과 같은 개인적인 파일들도 보관하는데 사용할 수 있다.
- 예를 들어, jihyun이라는 유저의 home디렉토리는 `/home/jihyun`이다.
- 참고로 root 계정의 home디렉토리인 `/root`도 존재한다.

<br>

### **/opt**
- os에 기본적으로 포함되지 않은 **서드파티 소프트웨어가 설치**되는 디렉토리이다.
- 예를 들어, os에 기본적으로 포함되지 않는 구글 크롬은 opt디렉토리에 설치된다.
- 일반적인 소프트웨어의 경우 다음의 서브디렉토리를 가진다.
  - `/opt/{package}/bin`
  - `/opt/{package}/etc`
  - `/opt/{package}/lib`
  - `/opt/{package}/log`
- 또한, 구글과 같이 기업이 특정된 소프트웨어는 기업명이 상위 디렉토리로 위치할 수 있다.
  - `/opt/google/chrome`
  - `/opt/google/chrome/bin`
  - `/opt/google/chrome/etc`

<br>

### **/tmp**
- 시스템 사용 중에 발생한 **임시 데이터를 보관하는 디렉토리**이다.
- 일반적인 리눅스 배포판에서는 부**팅될 때마다 해당 디렉토리가 초기화**된다.
  - 따라서, 만약에 해당 디렉토리에 개인의 파일들을 저장하게 된다면, 부팅되면서 제거된다.
- 그러므로, 해당 디렉토리에는 오랜 기간 보관될 파일들을 저장하지 말아야 한다.

<br>

### **/usr**
- **일반 사용자들을 위한 프로그램 라이브러리, 헤더 파일 등이 보관**된다.
- usr에는 다음과 같은 서브디렉토리가 존재할 수 있다.
  - /usr/bin : 일반 사용자들이 사용가능한 명령어 파일들이 보관
  - /usr/sbin : 일반 사용자들이 사용가능한 시스템 명령어 파일들이 보관
  - /usr/lib : /usr/bin에 있는 명령어를 실행하기 위한 라이브러리 보관
  - /usr/local : 기본 os에 포함되지 않는 프로그램들이 설치되는 장소(≈ Window's Program Files)
    - `/usr/local/{package}/bin`
    - `/usr/local/{package}/etc`
    - `/usr/local/{package}/lib`
    - `/usr/local/{package}/log`

<br>

### **/var**
- 시스템 사용 중 로그(log)와 같이 **지속적으로 변경되는 동적 파일들을 보관**한다.
- 로그 파일의 경우 `/var/log`에 보관된다.

<br>

### **/boot**
- 리눅스 부트을 위한 커널 이미지, 부팅 지원 파일을 보관한다.

<br>

### **/dev**
- **장치 파일들을 보관**한다.
- 리눅스는 하드디스크, 키보드, 그리고 마우스 등과 같은 **외부 장치들을 "관리"하기 위한 장치파일**을 가지고 있다.
- 리눅스에서 외부 장치 관리를 위해 시스템 관리자는 해당 장치 파일에 접근해야만한다.
- 예를 들어, 하드디스크의 경우 `/dev/sda`에 장치 파일이 위치하며, CD-ROM의 경우 `/dev/cdrom`에 위치한다.

<br>

### **/media**
- DVD, CD-ROM, USB 등과 같이 **탈부착이 가능한 장치들의 마운트 포인트**로 사용되는 디렉토리

<br>

### **/mnt**
- `/media`**와 비슷한 용도**로 사용된다.
- 다른 장치들을 마운트(**m**ou**nt**)할 때 사용하는 디렉토리

<br>

### **/proc**
- 실행중인 프로세스에 대한 정보가 보관되는 디렉토리

<br>

### **/srv**
- FTP, RSYNC, WWW, CVS 와 같은 프로토콜 시스템에 의해서 제공되어지는 데이터를 보관하는 디렉토리
  - `/srv/www` : web server files
  - `/srv/ftp` : FTP files
- 해당 데이터는 외부 사용자와의 공유되며, 따라서 다른 디렉토리에 비해서 외부 접근이 용이하다.

<br>

---

## <span style="color:navy">References<span>

- [wikipedia, 파일시스템 계층구조 표준](https://ko.wikipedia.org/wiki/%ED%8C%8C%EC%9D%BC%EC%8B%9C%EC%8A%A4%ED%85%9C_%EA%B3%84%EC%B8%B5%EA%B5%AC%EC%A1%B0_%ED%91%9C%EC%A4%80)
- [stack exchange, What is the difference between /opt and /usr/local?](https://unix.stackexchange.com/questions/11544/what-is-the-difference-between-opt-and-usr-local)

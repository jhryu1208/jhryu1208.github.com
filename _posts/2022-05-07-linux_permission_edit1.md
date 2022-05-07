---
layout: post
title: "[Linux] 리눅스 퍼미션 변경1 : chmod"
subtitle: "[Linux] 리눅스 퍼미션 변경1 : chmod"
categories: devlang
tags: linux
comments: true
mathjax: true
---
#### Contents
- [퍼미션 변경 : chmod](#퍼미션-변경--chmod)
- [문자를 이용한 방법](#문자를-이용한-방법)
- [8진수을 이용한 방법](#8진수을-이용한-방법)

해당 포스팅에서는 리눅스 퍼미션 편집 명령어 `chmod`에 대해서 정리하였습니다.

<br>

---

## <span style="color:navy">퍼미션 변경 : chmod<span>

`chmod`("CH"ange "MOD"e)는 파일/디렉토리의 접근 권한을 편집할 수 있는 명령어 중 하나이다.
그리고 해당 명령어를 사용하는 방법에는 `문자열 모드`와 `8진수 모드`가 있다. 
개인적으로는 후자의 방법이 가장 많이 이용되는 것 같다. 
왜냐하면, 권한 관련 이슈로 구글링을 하게되면 `8진수 모드`를 사용한 해결책을 자주 찾아볼 수 있었기 때문이다.
하지만, 딱히 `8진수 모드`만 사용하라는 법은 없으며 자기 자신이 편한 모드를 사용하도록 하자.

<br>

chmod의 기본 형태는 다음과 같다.
```shell
$ chmod [option] [mode] [file_name]
```

<br>

## <span style="color:navy">문자를 이용한 방법<span>

문자를 이용한 방법은 `u/g/o/a`, `r/w/x`, 그리고 `+-=`와 같은 문자 심볼을 이용한 방법이다.
**`u/g/o/a`의 경우 퍼미션 편집 대상이며, 각 대상들에게 `r/w/x`를 부여(`+`)하거나, 지우(`-`)거나, 지정(`=`)할 수 있다**. 
예를 들자면, 아래의 `chmod`명령어는 파일의 소유 그룹에게 `rwx`(읽기/쓰기/실행) 권한을 부여한다는 의미이다.

```shell
$ chmod g+rwx
```

<br>

실제 예제를 통해 연습해보자. <br>
다음의 <i>hello_permission.py</i>파일에 대하여 <i>jhryu1208</i>유저와 <i>hello_group</i>그룹은  `rw`(읽기/쓰기) 권한을 부여 받았으며, 
그 외 유저들에게는 `r`(읽기)권한만 부여 받은 것을 확인할 수 있다.

![img.png](/assets/img/2022-05-07_linux_permission_edit1/img.png)

이 상태에서 <i>jhryu1208</i>유저(`u`)에게 실행(`x`) 권한을 추가하고 싶을 경우에는 다음의 명령어를 작성한다.
그 결과 해당 유저는 `x`권한까지 부여 받은 것을 확인할 수 있다.
```shell
$ chmod u+x hello_permission.py
```
![img_1.png](/assets/img/2022-05-07_linux_permission_edit1/img_1.png)

그리고, 이 상태에서 기타유저(`o`)들이 파일을 읽지(`r`) 못하도록 하고 싶으며, 
<i>hello_group</i>그룹(`g`)에 속한 유저들이 파일을 편집하지 못하고 읽고(`r`)/실행(`x`)할 수 있도록만 하고 싶을 경우에는
다음과 같이 명령어를 작성한다.
```shell
$ chmod o-r, g=rx hello_permission.py
```
![img_3.png](/assets/img/2022-05-07_linux_permission_edit1/img_3.png)

여기서, 전체유저(`a`)들에게 전체 권한을 부여하고 싶을 경우 다음과 같이 작성한다.
```shell
$ chmod a=rwx hello_permission.py
```
![img_4.png](/assets/img/2022-05-07_linux_permission_edit1/img_4.png)

<br>

## <span style="color:navy">8진수을 이용한 방법<span>

`문자열 모드`와 달리 `8진수 모드`는 네이밍에서 알 수 있듯이 0~7로 표현되는 수 체계로 퍼미션을 편집하는 방법이다. 
이때, 읽기(`r`), 쓰기(`w`), 실행(`x`) 권한은 각각 4, 2, 1로 매핑되고, 
다음과 같이 필요 권한에 따라 매핑된 숫자를 더하여 8진수 결과를 생성한다. 
예를 들어, 읽기(`r`)와 쓰기(`w`) 권한 각각에 매핑된 4와 2를 더한 결과인 6은 `rw-`를 의미한다.


| String |  Sum  | Octal |      Description       |
|:------:|:-----:|:-----:|:----------------------:|
|  ---   | 0+0+0 |   0   |     No Permissions     |
|  --x   | 0+0+1 |   1   |      Execute Only      |
|  -w-   | 0+2+0 |   2   |       Write Only       |
|  -wx   | 0+2+1 |   3   |    Wirte & Execute     |
|  r--   | 4+0+0 |   4   |       Read Only        |
|  r-x   | 4+0+1 |   5   |     Read & Execute     |
|  rw-   | 4+2+0 |   6   |      Read & Write      |
|  rwx   | 4+2+1 |   7   | Read & Write & Execute |

<br>

그리고, 퍼미션 문자열(ex. rwxr-xr--)에서
`소유자 권한`, `소유 그룹 권한`, `그 외 사용자 권한`을 의미하는 각 단락은 
다음과 같이 합산된 8진수 수 체계로 대치할 수 있다.

![img_5.png](/assets/img/2022-05-07_linux_permission_edit1/img_5.png)

<br>

위의 예시 이외에도 아래와 같이 8진수 숫자 조합으로 많은 권한 조합을 생성할 수 있다. 
이 중에서 `777`과 `666` 권한은 불필요한 사고를 유발할 수 있기 때문에 피하는 것을 권장한다. 


| User(u) | Group(g) | Other(o) | Octal |
|:-------:|:--------:|:--------:|:-----:|
|   rwx   |   rwx    |   rwx    |  777  |
|   rwx   |   r-x    |   r-x    |  755  |
|   rwx   |   ---    |   ---    |  700  |
|   rw-   |   rw-    |   rw-    |  666  |
|   rw-   |   rw-    |   r--    |  664  |
|   rw-   |   rw-    |   ---    |  660  |
|   rw-   |   ---    |   ---    |  600  |
|   r--   |   ---    |   ---    |  400  |

<br>

다음은 `8진수 모드`를 이용한 `chmod`명령어의 간단 예시이다. <br>
`rw-rw-r--(664)`에서 `rwx------(700)`으로 바뀐 것을 확인할 수 있다.

![img_6.png](/assets/img/2022-05-07_linux_permission_edit1/img_6.png)

<br>

다음 포스팅에서는 `chmod`와 다른 퍼미션 편집 명령어인 `umask`에 관해서 알아보고자 한다.

<br>

---

## <span style="color:navy">References<span>
- [wikipedia, chmod](https://ko.wikipedia.org/wiki/Chmod)

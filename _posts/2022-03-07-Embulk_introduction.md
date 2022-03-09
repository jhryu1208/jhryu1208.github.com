---
layout: post
title:  "[Embulk] embulk 소개 및 설치"
subtitle: "[Embulk] embulk 소개 및 설치"
categories: data
tags: embulk
comments: true
mathjax: true
---
#### Contents
- [embulk란?](#embulk란)
- [Install Embulk on Docker](#install-embulk-on-docker)
- [Embulk Example](#embulk-example)

본 포스팅은 embulk가 무엇인지 그리고 어떻게 사용하는지에 대해 정리한 글입니다.

<br>

---

오늘은 데이터 엔지니어링 솔루션 중에서 처음으로 마주한 embulk에 관해서 소개한다. embulk가 무엇이고 어떤 장점을 가졌는지 간단하게 소개하고, Docker에서의 설치 방법, 그리고 간단한 사용 예제에 대해서 설명하고자 한다. 

<img width="400" alt="context" src="https://user-images.githubusercontent.com/53929665/157066486-71aba843-0069-48dd-a841-71790ed38599.png">

<br>

---

## <span style="color:navy">Embulk란?<span>

![Untitled](https://user-images.githubusercontent.com/53929665/157066504-ce4adaaa-ba1f-4cfc-8928-5de1f0688078.png)


embulk는 **데이터 소스간의 대용량 데이터 전송을 지원하는 오픈소스 솔루션**이다. 그리고, 우리가 알법한 혹은 들어봤을 법한 **데이터 소스, 스토리지, 파일 포맷, 그리고 클라우드 서비스 대부분을 플러그인 형태로 지원**하며 **yml파일을 통해서 쉽게 설정**이 가능하다. 예를 들자면, MySQL, PostgresSQL, Redshift, mongo DB, 그리고 Bigquery가 있으며, 이러한 소스들의 플러그인을 ruby gem을 이용해서 간단히 설치할 수 있다. 

<br>

그리고, embulk는 **데이터의 병렬 로딩을 수행**할 수 있다. 예를 들어, embulk에서 데이터 전송 작업을 수행한다면 다음과 같이 MapReduce방식과 유사한 과정을 거치게 된다. 

1. embulk는 스크립트로 부터 쿼리를 읽어서 transaction 작업을 생성한다. 
2. 그리고,  MapReduce방식처럼 생성된 transaction을  지정한 thread 수 만큼 task를 나누어 독립적으로 처리한다. 
3. 마지막으로, 각 task들이 처리한 결과를 하나로 통합시켜 output source에 반환한다.   

물론, 이렇게 분산 병렬 처리를 수행하면,  처리 부하를 분담하여 처리 속도의 향상으로 이어지지만 비용적 측면의 문제로 리소스의 제한이 불가피한 케이스에서는 task를 얼마나 분할해야 효율적인지 직접 확인할 필요가 있다.  하지만,  task를 효율적으로 나누었다 해도 크기가 큰 데이터와 대비해서 cpu리소스의 기반이 받쳐주지 못한다면 데이터 resource와의 connection이 빈번하게 끊어지거나 실행 시간이 비이상적으로 길어질 수 있다. 따라서, embulk를 사용할 시에는 인프라 비용을 투자하여 원활한 환경을 구성하는 것도 중요하다.

<br>

마지막으로, embulk는 스키마 전달을 별도로 해주지 않아도 입력 데이터 소스의 테이블 혹은 파일을  통해서 **자동으로 스키마 구조를 예측**한다. 그리고, **간단한 데이터 변환**도 가능하다. 특히, MySQL, PostgressSQL 등이 input 소스로 지정될 경우 쿼리를 통해서 손쉽게 변환하여 output 소스에 전달해 줄 수 있다. 

<br>

---

## <span style="color:navy">Install Embulk on Docker<span>

이제 embulk를 설치해보도록 하자. 보통은 GCE와 같은 가상 인스턴스(VM)에 embulk를 설치하여 Airflow와 같은 Task Scheduler와 같이 사용한다.

하지만, 나는 Window 환경에서 이를 수행할 예정이며 embulk의 docker이미지([링크](https://hub.docker.com/r/ietty/embulk))를 감사히 공개하시는 분이 계셔서 이를 이용해 Docker에서 간편 설치하고자 한다.  linux 및 mac OS에서의 설치 방법은 embulk 메인 웹사이트([링크](https://www.embulk.org/))에서 확인할 수 있다.

![Untitled 1](https://user-images.githubusercontent.com/53929665/157066495-6b0d1cea-19da-4595-a3f4-869aff0a5471.png)

<br>

공개된 이미지를 이용하여 아래의 명령어로 간단하게 설치를 완료할 수 있다.<br> (docker가 역시.. 👍)

```bash
> docker pull ietty/embulk # 이미지 다운로드
> docker run -t -d --name embulk ietty/embulk # 컨테이너 생성 및 실행
> docker exec -it embulk bash
```

<br>

---

## <span style="color:navy">Embulk Example<span>

embulk에서 지원하는 예제를 이용해서 MySQL에 output시키는 작업을 진행해보자.

먼저, MySQL로 output시키기 위하여 `embulk-output-mysql`이라는 플러그인을 설치한다. `embulk gem install <plugin name>`명령어를 통해서 간편하게 설치할 수 있다. (다른 소스에 대한 플러그인은 embulk의 github([링크](https://github.com/embulk))에서 모두 확인할 수 있다.)

```bash
> embulk gem install embulk-output-mysql
```

<br>

다음으로, 예제 embulk에서 기본적으로 제공하는 예제 파일을 생성하기 위해 아래의 명령어를 수행한다.

```bash
> embulk example
```

<br>

embulk-example이라는 파일 안에는 `sample_01`이라는 `GZIP`으로 압축된 `csv`파일과 `seed.yml`이 존재하는 것을 확인할 수 있다. 

![Untitled 2](https://user-images.githubusercontent.com/53929665/157066497-f3ff7e67-1d40-4156-849d-43636948e66d.png)

<br>

`seed.yml`파일을 확인할 경우, 다음과 같이 input 소스와 output소스에 대한 정보를 담고 있는 것을 확인할 수 있다.

```bash
in:
  type: file
  path_prefix: '/home/zaidryu/embulk-example/csv/sample_'
out:
  type: stdout
```

<br>

다음으로, `embulk guess`명령어를 이용해서 위와 같이 간단하게 작성된 `yml`파일을 기반으로 input 설정을 추론할 수 있다. 하단의 반환된 결과를 확인해보면,  파일의 타입과 압축형식 부터 스키마까지 추론한 것을 확인할 수 있다.

```bash
in:
  type: file
  path_prefix: /home/zaidryu/embulk-example/csv/sample_
  decoders:
  - {type: gzip}
  parser:
    charset: UTF-8
    newline: LF
    type: csv
    delimiter: ','
    quote: '"'
    escape: '"'
    null_string: 'NULL'
    trim_if_not_quoted: false
    skip_header_lines: 1
    allow_extra_columns: false
    allow_optional_columns: false
    columns:
    - {name: id, type: long}
    - {name: account, type: long}
    - {name: time, type: timestamp, format: '%Y-%m-%d %H:%M:%S'}
    - {name: purchase, type: timestamp, format: '%Y%m%d'}
    - {name: comment, type: string}
out: {type: stdout}
```


<br>
그리고, 추론한 설정의 결과에 만족한다면 이제 이를 기반으로 설정파일을 생성하도록 하자. 이는 위의 명령어에 `-o [path]`를 추가하여 수행할 수 있다. 

```bash
embulk guess seed.yml -o sample.yml
```

![Untitled 3](https://user-images.githubusercontent.com/53929665/157066500-7990ef9c-837b-4b02-8d0a-15f8df19db86.png)

<br>

input 소스에 대한 정보 기술이 완료되었으니, 마지막으로 output소스에 대한 정보를 기술하도록 하자. 나는 MySQL이 목적지이므로, vim에디터를 이용하여 하단과 같이 편집하였다. (MySQL의 output플러그인에 대한 소개는 다음의 [링크](https://github.com/embulk/embulk-output-jdbc/tree/master/embulk-output-mysql)에서 확인할 수 있다.)

```bash
in:
  type: file
  path_prefix: /home/zaidryu/embulk-example/csv/sample_
  decoders:
  - {type: gzip}
  parser:
    charset: UTF-8
    newline: LF
    type: csv
    delimiter: ','
    quote: '"'
    escape: '"'
    null_string: 'NULL'
    trim_if_not_quoted: false
    skip_header_lines: 1
    allow_extra_columns: false
    allow_optional_columns: false
    columns:
    - {name: id, type: long}
    - {name: account, type: long}
    - {name: time, type: timestamp, format: '%Y-%m-%d %H:%M:%S'}
    - {name: purchase, type: timestamp, format: '%Y%m%d'}
    - {name: comment, type: string}
out:
   type: mysql
   host: [database host name]
   port: 3306
   user: [database login user name]
   password: [database login password]
   database: [destination database name]
   table: [destination table name]
   mode: replace
```

<br>

그리고,  embulk run명령어를 통해서 지정한 경로로 데이터 전송을 수행할 수 있다.

```bash
embulk run sample.yml
```

<br>

그 결과 지정한 MySQL DB에서 전송된 테이블을 확인할 수 있다.

![Untitled 4](https://user-images.githubusercontent.com/53929665/157066502-12b49d03-9961-4e73-843c-d932cfc2b447.png)

<br>

---

## <span style="color:navy">References<span>

- [embulk Github](https://github.com/orgs/embulk/repositories?type=all)
- [embulk main website](https://www.embulk.org/)
- [dockerhub : ietty/embulk](https://hub.docker.com/r/ietty/embulk)
- [jungwoon님 Blog : Embulk 설치 및 기본 사용법](https://jungwoon.github.io/bigdata/2017/08/31/Embulk_Setup.html)
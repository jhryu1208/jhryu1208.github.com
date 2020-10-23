---
layout: post
title:  "[MySQL] Query Timeout"
subtitle: "[MySQL] Query Timeout"
categories: devlang
tags: (dbms)mysql
comments: true
---
#### [ MySQL Workbench - ERROR ] 
#### Error Code: 2013. Lost connection to MySQL server during query
----

워크밴치에서 쿼리를 날리고 설정된 타임아웃에 의해
**Error Code: 2013. Lost connection to MySQL server during query** 에러를 수십번도 보고있는 중이다.
  
자연스러운건가 싶었는데 이것도 워크밴치의 설정을 조금만 건드려서 쉽게 조절할 수 있었다.
워크밴치의 다음 설정 루트를 통해 쉽게 해결할 수 있다.
```
Edit → Preferences → SQL Editor → DBMS connection read time out (in seconds): 600
```
덕분에 30초가 지나서 쿼리가 중단되는 일은 거의 없는 거 같다.
하지만, 쿼리를 최대한 깔끔하고 효율적으로 작성하는 방법에 대해서는 계속 공부해야겠다.

회사 데이터베이스에 누적된 정보들을 계속  처리하면서 쿼리의 출력시간이 점점 길어지는 것을 확인하고있기 때문에,<br>효율적인 쿼리를 짜는 방법의  중요성에 대해 다시금 생각하고 있다 :)


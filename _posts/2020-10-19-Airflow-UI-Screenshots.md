---
layout: post
title:  "[Airflow] Airflow UI/Screenshots"
subtitle: "[Airflow] Airflow UI/Screenshots"
categories: data
tags: Airflow
comments: true
---

- 본 포스팅은 Apache Airflow의 UI 환경에 관하여 다룹니다.

---
### [ DAGs View ]
- airflow 환경에 등록된 DAG의 목록을 보여준다.
- Task의 성공/실패/실행 여부등을 확인할 수 있다.
- Script 안에 있는 각 dag의 DAG함수에 tag를 다음과 같이 삽입하여 filter기능을 이용할 수 있다.   
( ex.  team이나 devlang별로 DAG를 분류하고 싶을 경우 사용된다. )
```python
dag = DAG( 'dag', tags = [ 'team1', 'sql' ]
```
<img width="945" alt="7" src="https://user-images.githubusercontent.com/53929665/96405941-584f3380-1219-11eb-9e32-2ddf3c2bf91b.PNG">

### [ Tree View ]
- Pipeline을 구성시, 빠르게 different steps와 blocking을 확인할 수 있다.

<img width="914" alt="tree" src="https://user-images.githubusercontent.com/53929665/96407827-3bb4fa80-121d-11eb-8fbb-79f105260c68.png">

- 또한, Tree View의 완료된 Task를 클릭하면 다음과 같은 context를 확인할 수 있다.
	- Task의 로그 확인(View Log), 실행(Run), 실행 상태 초기화(Clear)등을 할 수 있다.
	- Task 재실행시 Run 혹은 Clear를 누르면된다.

<img width="762" alt="context" src="https://user-images.githubusercontent.com/53929665/96407822-3a83cd80-121d-11eb-87e8-07d4d0385daa.png">


### [ Graph View ]
- Graph view는 종합적인 정보들을 보여준다.
- DAG구조에 속한 Task들의 의존도를 시각적으로 확인할 수 있다.
- 복잡한 워크플로우의 경우 그 구조를 파악하는데 유용하다.

<img width="1010" alt="graph" src="https://user-images.githubusercontent.com/53929665/96407826-3bb4fa80-121d-11eb-923f-923ae110592c.png">


### [ Gantt Chart ]
- 각 수행에서 Task들의 수행 순서에 따라서 소모된 시간과 함께 간트 차트로 보여준다.
- 수행 순서와 태스크당 시간을 한꺼번에 보여주기 때문에 <u>병목구간을 파악하기 쉽다</u>.

<img width="1010" alt="gantt" src="https://user-images.githubusercontent.com/53929665/96407987-8cc4ee80-121d-11eb-83df-0278a4bbc22e.png">

### [ Task Duration ]
- DAG에서 수행된 각 Task의 수행시간을 그래프의 형식으로 보여준다.
- X축은 수행 날짜, Y축은 수행하는데 걸린 시간을 의미한다. Task별로 서로 다른 색 선으로 구분된다.

<img width="1010" alt="duration" src="https://user-images.githubusercontent.com/53929665/96407982-8b93c180-121d-11eb-9aaa-54e940107267.png">

---
#### Index
- Airflow UI/Screenshots Guide : https://airflow.apache.org/docs/stable/ui.html
- 조대협님의 tistory 블로그 : https://bcho.tistory.com/1184
- zzsza님의 github 블로그 : wzzsza.github.io/data/2018/01/04/airflow-1/

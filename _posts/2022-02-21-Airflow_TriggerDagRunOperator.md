---
layout: post
title:  "[Airflow] TriggerDagRunOperator"
subtitle: "[Airflow] TriggerDagRunOperator"
categories: data
tags: Airflow
comments: true
mathjax: true
---

#### Contents
- [Introduction](#introduction)
- [TriggerDagRunOperator의 Format과 Parameter](#triggerdagrunoperator의-format과-parameter)
- [사용 예제](#사용-예제)

본 포스팅은 카테고리 Airflow의  TriggerDagRunOperator에 관하여 정리하였습니다.

<br>

---

## <span style="color:navy">Introudction<span>

[지난 포스팅](https://jhryu1208.github.io/data/2022/02/20/Airflow_ExternalTaskSensor/)에 이어서 `TriggerDagRunOperator`에 대해 알아보고자 한다.

---

## <span style="color:navy">TriggerDagRunOperator의 Format과 Parameter<span>

아래는 `TriggerDagRunOperator`의 기본 포맷과 파라미터에 관해서 보여준다.  나의 경우 Airflow 버전1을 사용하기 때문에 버전1을 초점에 맞추어 내용을 정리하였다. (편의상 Trigger 당하는 외부 Dag를 Slave Dag, 그리고 Trigger을 수행하는 Dag를 Master Dag라고 칭하겠다.)

 

```python
task = TriggerDagRunOperator('task_name_example',
                             trigger_dag_id = 'slave_dag_id',
                             python_callable = tirgger_something_func,
                             execution_date = '{{ ds }}')
```

- `trigger_dag_id` (*str*)
    - trigger할 Slave Dag의 `dag_id`를 받는다. 참고로, Slave Dag의 tirgger는 Master Dag가 대신하기 때문에 schedule_interval=None으로 지정해도 괜찮다.

- `python_callable` (*python callable*)
    - Airflow의 XCom과 비슷한 기능을 수행하지만,  XCom의 경우 동일 Dag에 있는 Task끼리만 가능하다. 하지만, 해당 파라미터를 이용할 경우 Master Dag에서 Slave Dag로의 단방향 객체 전달을 진행할 수 있다.
    - 해당 파리미터에 전달되는 객체는 `context object`와 `placeholder object`(`obj`)를 받는 함수이며, 이 함수는 `obj`를 반환해야한다. `obj`에는 커스텀할 수 있는 `run_id`와 `payload`속성이 존재한다.
    - `run_id`속성의 경우 Slave Dag Run을 식별할 수 있는 아래와 같은 값인데 직접적으로 트랙킹하기 어려워서 user define으로 값을 주기 위해서는 실제로는 커스텀 함수가 별도로 필요할 것 같다. 나의 경우에는 이용하지 않을 것 같다.
        
        ![Untitled](https://user-images.githubusercontent.com/53929665/154988161-cd5e9331-b611-4a1f-8d35-ac401be5eabe.png)
        
    - `payload`속성은 다른 Dag에 전달할 serialization가능한 `pickable object`를 해당 파라미터는 전달 받는다. 그리고 이를 전달 받은 Slave Dag의 Task는 `context object`의 `dag_run` key의 value의 `conf`속성으로 부터 호출할 수 있다. (즉, `context[’dag_run’].conf` )
    - 따라서 여기에 전달되는 함수의 기본 포맷은 다음과 같다.
        
        ```python
        def test_func(context, obj):
                obj.payload = {"key": "value"}
                return obj
        ```
        

- `execution_date` (*str/datetime.datetime*)
    - Slave Dag의 execution date를 받는다.
    - Master Dag가 완료되고 난 뒤에 바로 Slave Dag가 수행되기를 원한다면 고려할 필요는 없다.

<br>

---

## <span style="color:navy">사용 예제<span>

다음은 slave dag 코드와 slave dag를 tirgger시키는 master dag에 대한 코드이다.

master dag의 dag_id는 **Master_Dag_for_TDR**이라고 지정하였으며, slave dag의 dag_id는 **Slave_Dag_for_TDR**이라고 지정하였다. 더불어, tirgger당하는 slave dag의 경우 `schedule_interval`가 `None`으로 지정하였다.

##### </> trigger_master_dag.py

```python
from datetime import datetime
from airflow.models import DAG
from airflow.operators.dummy_operator import DummyOperator
from airflow.operators.python_operator import PythonOperator
from airflow.operators.dagrun_operator import TriggerDagRunOperator

default_args = {
    'owner': 'zaid.ryu'
}

dag = DAG('Master_Dag_for_TDR',
          description = 'Master Dag',
          schedule_interval = '0 17 * * *',
          default_args = default_args,
          start_date = datetime(2022, 2, 19),
          catchup = False)

def trigger_func(context, obj):
    obj.payload = {'name':'Zaid.Ryu',
                   'talk':'this is my first code for TriggerDagRunOperator'}
    return obj

def print_ds(ds):
    print(f'ds : {ds}')

m_task1 = DummyOperator(task_id = 'master_task',
                        dag = dag)

m_task2 = TriggerDagRunOperator(task_id = 'trigger_slave',
                                trigger_dag_id = 'Slave_Dag_for_TDR',
                                python_callable = trigger_func,
                                execution_date = "{{ ds }}",
                                dag = dag)

m_task3 = PythonOperator(task_id = 'printDS',
                         python_callable = print_ds,
                         op_args=["{{ ds }}"],
                         dag = dag)

m_task1 >> m_task2 >> m_task3
```

##### </> trigger_slave_dag.py

```python
from datetime import datetime
from airflow.models import DAG
from airflow.operators.dummy_operator import DummyOperator
from airflow.operators.python_operator import PythonOperator

default_args = {
    'owner': 'zaid.ryu'
}

dag = DAG('Slave_Dag_for_TDR',
          description = 'Slave Dag',
          schedule_interval = None,
          default_args=default_args,
          start_date = datetime(2022, 2, 19),
          catchup = False)

def pirnt_conf(**context):
    dag_run = context['dag_run'].conf
    print('{name} said \"{talk}\"'.format(**dag_run))

def print_ds(ds):
    print(f'ds : {ds}')

s_task1 = DummyOperator(task_id = 'slave_task',
                        dag = dag)

s_task2 = PythonOperator(task_id = 'printDagRun',
                         python_callable=pirnt_conf,
                         provide_context=True,
                         dag = dag)

s_task3 = PythonOperator(task_id = 'printDS',
                         python_callable = print_ds,
                         op_args=["{{ ds }}"],
                         dag = dag)

s_task1 >> s_task2 >> s_task3
```

<br>

그리고 이를 실행했을 때의 dag 관계를 도식화하면 다음과 같을 것이다. 

![Untitled 1](https://user-images.githubusercontent.com/53929665/154988151-994e654b-780e-4cbe-ba48-b329117e98db.png)

위의 그림의 직사각형은 Task를 의미하며 줄 선으로 묶어 Dag안에 포함되었음을 표현하였다. 해당 그림을 기준으로 master dag는 실행 중인 상태이며, master_task까지 수행이 완료되었음을 표시하였다. 그리고, 화살표의 경우 tirgger 관계를 표시한다.

해당 그림은 현재 master dag가 run인 상태이며, master_task까지 수행 완료하였음을 확인할 수 있다. 하지만, slave dag는 run 상태에 해당하지 않으며 스케줄업된 상태도 아닌 것을 확인할 수 있다.

이제 master dag의 trigger_slave가 scheduled상태가 된 다음을 확인해보자.

<br>


![Untitled 2](https://user-images.githubusercontent.com/53929665/154988153-4dc4cd3f-6ad3-4a72-bb94-edec409f7439.png)

master dag의 trigger_slave가 수행되면서 코드에서 지정한 slave dag가 수행 상태에 진입한 것을 확인하였다. 이때, tirgger_slave의 시작 시간, 종료시간, 그리고 slave dag의 시작 시간을 비교해보면 tirgger_slave가 수행이 끝난 뒤에 slave_dag가 tirgger되는 것이 아니라, trigger_slave가 시작되면서 slave_dag가 수행되는 것임을 확인할 수 있다. 

##### </> trigger_master_dag.py의 trigger_func 함수

```python
def trigger_func(context, obj):
    obj.payload = {'name':'Zaid.Ryu',
                   'talk':'this is my first code for TriggerDagRunOperator'}
    return obj
```

##### </> trigger_slave_dag.py의 print_conf함수

```python
def print_conf(**context):
    dag_run = context['dag_run'].conf
    print('{name} said \"{talk}\"'.format(**dag_run))
```


![Untitled 3](https://user-images.githubusercontent.com/53929665/154988157-aa9af60a-1740-4486-b1c6-0ce1654351ae.png)

그리고,  slave dag의 printDagRun Task의 log에서 trigger_func함수를 통해서 master dag가 slave dag에 보낸 object를 slave dag의 `context obj`에서 정상적으로 확인할 수 있었다. 

<br>

---

## References

- [Airflow version1 : dagrun_operator.TriggerDagRunOperator](https://airflow.apache.org/docs/apache-airflow/1.10.15/_api/airflow/operators/dagrun_operator/index.html?highlight=triggerdagrunoperator#airflow.operators.dagrun_operator.TriggerDagRunOperator)
- [Airflow version2 : trigger_dagrun.TriggerDagRunOperator](https://airflow.apache.org/docs/apache-airflow/stable/_api/airflow/operators/trigger_dagrun/index.html)
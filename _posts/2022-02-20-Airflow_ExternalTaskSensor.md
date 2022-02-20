---
layout: post
title:  "[Airflow] ExternalTaskSensor"
subtitle: "[Airflow] ExternalTaskSensor"
categories: data
tags: Airflow
comments: true
mathjax: true
---

#### Contents
- [Introduction](#introduction)
- [ExternalTaskSensor의 Format과 Parameter](#externaltasksensor의-format과-parameter)
- [execution_delta와 execution_date_fn의 중요성](#execution_delta와-execution_date_fn의-중요성)

본 포스팅은 카테고리 Airflow의  ExternalTaskSensor에 관하여 정리하였습니다.

<br>

---


## <span style="color:navy">Introduction<span>

최근 Firebase의 Bigquery 데이터 조회 비용을 감소시키기 위해서  s3로 적재시키고 AWS Glue를 통해서 주기적으로 관측하는 데이터 지표를 생성하고 있으며, 이를 Airflow를 통해서 관리하고 있다. 하지만, Firebase의 데이터로부터 빈번하게 조회하는 이벤트 데이터를 추출하여 Bigquery에서 Table로 관리하여 데이터 조회 비용을 더 감축시키고자 한다.  이 과정에서 위에서 언급한 AIrflow의 Dag내 특정 Task를 Trigger로써 혹은 해당 Task와 종속성을 가지는 Task를 구성하는 방법에 대해 살펴보다가 `ExternalTaskSensor`와  `TriggerDagRunOperator`를 확인하게 되었다.  이 중에서 먼저 `ExternalTaskSensor`에 대해서 먼저 알아보고자 한다.


<br>

---

## <span style="color:navy">ExternalTaskSensor의 Format과 Parameter<span>

아래는 `ExternalTaskSensor`의 기본 포맷과 각 파라미터에 관해서 보여준다.

(편의상 `ExternalTaskSensor`를 가지고 있는 Dag를 slave dag, 그리고 해당 sensor가 waiting하는 Dag를 master dag라고 별칭하겠다.) 

```python
from airflow.sensors.external_task import ExternalTaskSensor
from airflow.utils.state import State

task = ExternalTaskSensor('task_name_example',
                           external_dag_id = 'master_dag_id',
                           external_task_id = 'master_task_id',
                           allowed_states = [State.SUCCESS, State.SKIPPED],
                           failed_states = [State.FAILED],
                           execution_date_fn = lambda x : x,
                           check_existence = True)
```

- `external_dag_id`(*str*)
    - monitoring하는 Task를 가지고 있는 Dag의 id

- `external_task_id`(*str/default : None*)
    - waiting하는 Task의 구체적인 id
    - 만약 해당 parameter를 기입하지 않을 경우 (즉, external_task_id = None)
        
        sensor는 DAG를 타겟으로 잡는다.
        
    - waiting하는 task가 여러 개일 경우에는 `external_task_ids`(*Iterable*/*default : None*)를 이용한다.

- `allowed_states` (*Iterable)*
    - waiting중인 master dag의 task가 어떤 상태일 때 성공으로 판단하는지 지정한다.
    - default는 `[’success’]`이다.
    - [state참조 링크](https://airflow.apache.org/docs/apache-airflow/1.10.3/_modules/airflow/utils/state.html)

- `failed_states` (*Iterable)*
    - waiting중인 master dag의 task가 어떤 상태일 때 실패로 판단하는지 지정한다.
    - default는 `None`이다.
    - [state참조 링크](https://airflow.apache.org/docs/apache-airflow/1.10.3/_modules/airflow/utils/state.html)
    
- `execution_delta` or `execution_date_fn`
    - master dag와 `ExternalTaskSensor`Task를 가진 slave dag에 서로 다른 schedule interval을 설정하고 싶을 때 사용한다.
        
        
        예를 들어,  master dag의 scheduler interval이 “0 16 * * ***”**이고, 
        
        slave dag의 interval이 “5 16 * * *” 일 경우에는 
        
        `execution_delta`는 slave dag의 execution_date를 기준으로  
        
        `timedelta(minutes = -5)`로 설정하면 된다. 반면, `execution_date_fn`의 경우  `lambda x : x + timedelta(minutes = -5)` 로 지정할 수 있다.
        
    
    - 해당 두 파라미터를 동시에 사용할 수는 없다.
    - default는 waiting하는 dag의 execution_date이다.

- `check_existence`(*bool*)
    - True일 경우, waiting하고 있는 dag혹은 task가 존재하는지 확인한다.
    - 만약 존재하지 않을 경우에는 즉각적으로 master task혹은 dag의 waiting을 중단한다.
    - 기본값은 False이다.

<br>

---

## <span style="color:navy">execution_delta와 execution_date_fn의 중요성<span>

다음은 master dag code, slave dag1, 그리고 slave dag2에 대한 코드이다.

특정 slave dag내 task의 worker slot독점으로 인해 master dag의 task 수행이 멈추는 케이스를  방지하기 위해서 `mode`파라미터를 `reschedule`로 지정하였다.

###### </> master_dag
```python
from datetime import datetime

from airflow.models import DAG
from airflow.operators.dummy_operator import DummyOperator
from airflow.sensors.external_task_sensor import ExternalTaskMarker, ExternalTaskSensor

default_args = {
    'owner' : 'zaid.ryu',
}

dag = DAG('TEST_Sensor_Master_Dag',
        description = 'Test ExternalTaskSensor Master Dag',
        schedule_interval = '0 17 * * *',
        default_args = default_args,
        start_date = datetime(2022, 2, 18),
        catchup = False)

m_task1 = DummyOperator(task_id = 'master_task1',
                        dag = dag)

m_task2 = DummyOperator(task_id = 'master_task2',
                        dag= dag)

m_task3 = DummyOperator(task_id = 'master_task3',
                        dag= dag)

m_task4 = DummyOperator(task_id = 'master_task4',
                        dag= dag)

m_task5 = DummyOperator(task_id = 'master_task5',
                        dag= dag)

m_task1 >> m_task2
m_task1 >> m_task3
m_task3 >> m_task4
m_task3 >> m_task5
```

<br>

###### </> slave_dag1
```python
from datetime import datetime, timedelta

from airflow.models import DAG
from airflow.operators.python_operator import PythonOperator
from airflow.operators.dummy_operator import DummyOperator
from airflow.sensors.external_task_sensor import ExternalTaskSensor
from airflow.utils.state import State

default_args = {
    'owner' : 'zaid.ryu'
}

dag = DAG('TEST_Sensor_Slave_Dag1',
          description = 'Test ExternalTaskSensor Slave Dag1',
          schedule_interval = '5 17 * * *',
          default_args = default_args,
          start_date = datetime(2022, 2, 18),
          catchup = False)

s_task1 = ExternalTaskSensor(task_id = 'slave_task1_sensor',
                             external_dag_id = 'TEST_Sensor_Master_Dag',
                             external_task_id = 'master_task2',
                             allowed_states = [State.SUCCESS, State.SKIPPED],
                             failed_states = [State.FAILED],
                             check_existence = True,
                             execution_date_fn = lambda dt : dt + timedelta(minutes = -5),
                             mode='reschedule',
                             #timeout=3600,
                             dag = dag)

s_task2 = ExternalTaskSensor(task_id = 'slave_task2_sensor',
                             external_dag_id = 'TEST_Sensor_Master_Dag',
                             external_task_id = 'master_task2',
                             allowed_states = [State.SUCCESS, State.SKIPPED],
                             failed_states = [State.FAILED],
                             check_existence = True,
                             execution_date_fn = lambda dt : dt + timedelta(minutes = -2),
                             mode='reschedule',
                             #timeout=3600,
                             dag = dag)
```

<br>


###### </> slave_dag2
```python
from datetime import datetime, timedelta

from airflow.models import DAG
from airflow.operators.python_operator import PythonOperator
from airflow.operators.dummy_operator import DummyOperator
from airflow.sensors.external_task_sensor import ExternalTaskSensor
from airflow.utils.state import State

default_args = {
    'owner' : 'zaid.ryu'
}

dag = DAG('TEST_Sensor_Slave_Dag1',
          description = 'Test ExternalTaskSensor Slave Dag1',
          schedule_interval = '5 17 * * *',
          default_args = default_args,
          start_date = datetime(2022, 2, 18),
          catchup = False)

s_task1 = ExternalTaskSensor(task_id = 'slave_task1_sensor',
                             external_dag_id = 'TEST_Sensor_Master_Dag',
                             external_task_id = 'master_task2',
                             allowed_states = [State.SUCCESS, State.SKIPPED],
                             failed_states = [State.FAILED],
                             check_existence = True,
                             execution_date_fn = lambda dt : dt + timedelta(minutes = -5),
                             mode='reschedule',
                             #timeout=3600,
                             dag = dag)

s_task2 = ExternalTaskSensor(task_id = 'slave_task2_sensor',
                             external_dag_id = 'TEST_Sensor_Master_Dag',
                             external_task_id = 'master_task2',
                             allowed_states = [State.SUCCESS, State.SKIPPED],
                             failed_states = [State.FAILED],
                             check_existence = True,
                             execution_date_fn = lambda dt : dt + timedelta(minutes = -2),
                             mode='reschedule',
                             #timeout=3600,
                             dag = dag)
```

<br>

그리고 이를 도식화하면 다음과 같다. 

(편의상 글을 쓸 때는 TEST_Master_Dag를 master dag, TEST_Sensor_Slave_Dag1과 2를 slave dag1, slave dag2라고 지칭하겠다.)

![Untitled](https://user-images.githubusercontent.com/53929665/154834362-5813162c-aaed-44fb-9e58-62c735939da8.png)


master dag의 master task2가 완료(success상태)되면  slave dag1의 slave_task1_sensor와 slave_task2_sensor의 `reschedule`상태가 `running`상태로 변경되면서 slave dag1의 task들이 수행된다.

이때, master dag와 slave dag1의 `schedule_interval`의 차이가 5분인 것을 확인할 수 있다. 이때, 해당 slave dag의  경우 `execution_date_fn`혹은 `execution_delta`를 5분으로 지정할 필요가 있다. 

slave_task1_sensor의 경우에는 위와 같이 지정 되었지만, slave_task2_sensor의 경우에는 3분으로 지정하였다. (해당 task는 문제를 발생시킬 것이다.)

그리고, master dag의 master task1이 완료되면 slave dag2의 slave_task1_sensor가, master task5가 완료되면 slave_task2_sensor가  `reschedule`상태가 `running`상태로 변경될 것이다.

이때, master dag와 slave dag2의 `schedule_interval`이 동일한 것을 확인할 수 있다.  따라서, 해당 slave dag의 경우 `execution_date_fn`혹은 `execution_delta`를 0분으로 지정할 필요가 있다. 

그리고, slave dag2의 slave_task1_sensor와 slave_task2_sensor를 위와 같이 지정하였다.

<br>

이제 dag를 수행한 결과를 확인해보자.

![Untitled 1](https://user-images.githubusercontent.com/53929665/154834358-f2d53466-1b3c-46a9-8cc2-ce77016dc88b.png)


master dag의 모든 task들은 정상적으로 수행된 것을 확인할 수 있었다. 더불어, slave dag2의 slave_task2_sensor를 제외한 모든 `ExternalTaskSensor`가 정상적으로 수행한 것을 확인할 수 있다. 

해당 task만 실패한 이유는 위에서 언급했듯이  `execution_date_fn`혹은 `execution_delta`를 잘못 지정하였기 때문이다. 왜냐하면, 해당 task의 경우 timedelta(minutes = -2)로 지정하였기 때문에 slave dag2의 `schedule_interval = ‘5 17 * * *’`을 기준으로 **2022-02-18T17:03:00+00.00**에 실행되는 task를 1분 간격으로 확인하며 waiting하게 된다. 하지만, 도식에서도 볼 수 있듯이 그런 task는 존재하지 않는다.

<br>

![Untitled 2](https://user-images.githubusercontent.com/53929665/154834361-5f29ebd3-44be-4cb9-9d95-ca53b3c13c5d.png)

따라서, 해당 task는 위의 사진처럼 완료 상태의 master_task2를 무한정으로 waiting하게 된다. 이러한 무한정 waiting을 방지하기 위해서 `ExternalTaskSensor`내에 `timeout`파라미터를 지정할 수 있긴 하지만, 이런 상황을 발생하지 않도록 `execution_date_fn`혹은 `execution_delta`를 제대로 지정해주는 것이 중요한 것 같다.

<br>

---

## <span style="color:navy">References<span>

- [Cloud Walker’s Airflow - External Task Sensor posting](https://www.cloudwalker.io/2021/09/26/airflow-external-task-sensor/)
- [Airflow Operators Documents](https://airflow.apache.org/docs/apache-airflow/stable/_api/airflow/sensors/external_task/index.html)
- [Airflow Sensor Mode](https://guptakumartanuj.wordpress.com/category/airflow/)
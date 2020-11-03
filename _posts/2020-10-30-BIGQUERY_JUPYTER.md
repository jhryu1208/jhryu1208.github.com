---
layout: post
title:  "[BigQuery-Jupyter] 주피터 노트북에서 빅쿼리 데이터 가져오기!"
subtitle: "[BigQuery-Jupyter] 주피터 노트북에서 빅쿼리 데이터 가져오기!"
categories: gcp
tags: bigquery
comments: true
---
#### [ BIgQuery ] Jupyter Lab의 Jupyter Notebook과 연동
- 본 포스팅은 GCP의 데이터를 Jupyter Notebook에서 작업할 수 있는 방법에 관하여 작성하였습니다.
- <u>Windows 10 64bit</u> 환경에서 진행합니다.
- `pyarrow` 가 `pip`나 `conda`를 통해서 의존성 문제에 막혀 설치가 안될 경우에 관한 해결법에 관해서도  다룹니다.

---

### <u>pyarrow 라이브러리 설치</u>

BigQuery의 데이터를 이용하여 ML을 수행해야하는 일이 있었습니다.
그래서 GCP공식 문서에 따라, Jupyter Notebook을 연동시키는 작업을 진행하였지만 빅쿼리의 데이터를 데이터프레임으로 전환하는 과정에서 `pyarrow` 라이브러리가 import되지 않아서 진행할 수 없다는 둥, 없는` pyarrow`를 설치하려니깐 의존성확인에서 실패하는 둥 많은 고생을 겪었습니다. 

이를 수행하기전에 `google colab`(같은 구글 플렛폼이라서 그런가...?)으로 더 간단한 과정 및 오류없이 수행이 가능했으나, colab에서 자동 출력되는 차트의 크기및  gui 그리고 유틸이 개인적으로는 취향이 아니라서 jupyer nootebook으로 바꾸기 위해 발버둥 쳤습니다. 만약 colab으로 작업하는 것을 원하시는 분들은 다음 [링크](https://zzsza.github.io/data/2018/08/30/google-colab/)를 참조하시는 것을 권장드립니다.

저와 같은 고생을 겪은 사람들이 있으시다면 해당 포스팅이 도움이 되었으면 좋겠습니다.

먼저 pip나 conda를 통해 `pyarrow`를 설치하는 과정에서 발생하는 오류는 직접 파일을 다운받고 설치하는 식으로 해결하였습니다.

해당 [링크](https://pypi.org/project/pyarrow/2.0.0/#modal-close)에 들어가셔서 `pyarrow`에 관한 도큐먼트를 살펴봅니다.<br>그러면 왼쪽 Navigation에 Download files라는 탭이 있을텐데 들어가셔서 자기 파이썬버전과 윈도우환경에 맞는 버전을 선택하시면 됩니다.


<img width="397" alt="1" src="https://user-images.githubusercontent.com/53929665/97712071-487cfc80-1b01-11eb-9218-20f0bc728555.PNG">


저의 경우 Window 10 64bit 버전에 python은 3.9.X 버전이 설치되어있는 상태였습니다.<br>따라서 `win_amd64`버전을 선택하였습니다.<br>( 만약 인텔 64비트의 환경이라면 win32환경의 whl 파일로 설치를 권장합니다. 하지만 pyarrow의 경우 amd기반의 파일만 있기 때문에 amd64기반의 파일을 다운받겠습니다.)<br>
하지만, 현재 다운로드 파일로 공시된 최신 `pyarrow`는 python 3.8.x(cp38)버전기반이였습니다. <br>따라서 파이썬 버전을 낮추어 설치를 진행합니다.


> 만약 자신의 pc환경을 무시하고 설치를 진행하시면<br> `[파일명] is not a supported wheel on this platform`이라는 오류가 출력되는 것을 확인할 수 있습니다.


파이썬 버전 다운그레이드의 경우 다음 과정으로 쉽게 진행할 수 있습니다.<br>이미 주피터노트북으로 작업을 수행하고자하시는 분들은 대부분 anaconda prompt가 설치되어있으므로,<br>anaconda prompt를 실행하여 다음의 명령어들을 수행합니다.

- 사용 가능한 버전 python 버전 리스트 조회
```cmd
conda search python
```

- 특정 버전의 파이썬 설치 (예시에서는 요구되는 버전인 3.8.0을 기입하였습니다)
```cmd
conda install python=3.8.0
```

그리고 cmd에 python을 입력하면 제대로 다운그레이드 혹은 재설치가 진행되어 있음을 확인할 수 있습니다.

자신 pc환경에 맞는 파일을 다운로드한 후 C드라이브나 자신의 파이썬 라이브러리 패키지를 저장해두는 파일목록에 넣어두고 다음 과정을 진행하시면 됩니다.

- 먼저 pip업그레이드를 수행합니다.
```cmd
python -m pip install --upgrade pip
```

- 자신이 다운로드 받은 whl파일이 저장된 곳으로 이동하고 다음을 수행합니다
```cmd
pip install pyarrow-2.0.0-cp38-cp38-win_amd64.whl
```

해당 과정까지 진행하시면 pyarrow가 정상 설치된 것을 확인할 수 있습니다.

---

###  <u>GCP -> Jupyter Notebook으로 데이터 가져오기</u>

> jupyter lab에서 해당 과정을 수행하는 것을 권장드립니다.

<br>

- 먼저 필수 라이브러리들을 설치해줍니다.<br>(해당 라이브러리 외에 경고로 표시되는 라이브러리 또한 pip로 설치를 진행해줍시다.)
```cmd
pip install pydata-google-auth
pip install google-cloud-bigquery
pip install pandas-gbq
```


- 다음 코드를 노트북에 작성합니다.
	- 작성 후 실행하시면 자신의 구글 계정과 연동되는 것을 확인하실 수 있습니다.
	- 구글 계정은 물론 자신이 사용하는 빅쿼리 서버와 연동되는 계정을 사용하셔야 합니다.
	- 화면 지시에 따라 인증키를 발급 받고 이를 credentials에 저장하는 과정을 거칩니다.
```python
import pydata_google_auth
credentials = pydata_google_auth.get_user_credentials(
    ['https://www.googleapis.com/auth/bigquery'],
)
```

- 위의 작업을 수행하여 인증 credentials를 GCP 클라이언트에 전달하여 선택한 계정의 리소스에 액세스할 수 있게 되었습니다.
- 마지막으로 다음과정을 진행하여 빅쿼리의 결과 테이블을 주피터노트북에서 확인할 수 있습니다.
	- 자신이 수행하길 원하는 query를 기입하고
	- `to_dataframe()` 메소드를 이용하는 것으로, `Pandas`의 Dataframe으로 출력하는 것 까지 가능합니다.<br>(하지만 저는 이 과정에서 `pyarrow`임포트 오류와 마주하였고 위와 같이 수행하여 해결하였습니다.)

```python
from google.cloud import bigquery
client = bigquery.Client (project = '<project_id>' , credentials = credentials) 
query = 'SELECT * FROM <data set name> <table name> LIMIT 10' 

df_result = client.query (query) .to_dataframe ()
 ```



---
#### index
-  https://gri-blog.hatenablog.com/entry/2019/10/21/135645
- https://dololak.tistory.com/517
- https://pypi.org/project/pyarrow/2.0.0/#files
- https://zzsza.github.io/data/2018/08/30/google-colab/


---
layout: post
title:  "[ML with Python] 4.범주형 변수 & 원-핫-인코딩"
subtitle: "[ML with Python] 4.범주형 변수 & 원-핫-인코딩"
categories: data
tags: ml
comments: true
---
#### [ML with Python] 4. 범주형 변수 & 원-핫-인코딩
- 본 포스팅은 범주형 변수와 원-핫-인코딩에 관한 기본적인 내용에 관하여 다룹니다.


<h1>Table of Contents<span class="tocSkip"></span></h1>
<div class="toc"><ul class="toc-item"><li><span><a href="#1)--범주형-변수" data-toc-modified-id="1)--범주형-변수-0">1)  <u>범주형 변수</u></a></span><ul class="toc-item"><li><span><a href="#1-1)-원---핫---인코딩(가변수)" data-toc-modified-id="1-1)-원---핫---인코딩(가변수)-0.1">1-1) <u>원 - 핫 - 인코딩(가변수)</u></a></span></li><li><span><a href="#1-2)-숫자로-표현된-범주형-특성-" data-toc-modified-id="1-2)-숫자로-표현된-범주형-특성--0.2">1-2) <u>숫자로 표현된 범주형 특성 </u></a></span></li></ul></li><li><span><a href="#2)-OneHotEncoder-와-ColumnTransformer" data-toc-modified-id="2)-OneHotEncoder-와-ColumnTransformer-1">2) <u>OneHotEncoder 와 ColumnTransformer</u></a></span><ul class="toc-item"><li><span><a href="#2-1)-make_column_transformer로-간편하게-ColumnTransformer-만들기" data-toc-modified-id="2-1)-make_column_transformer로-간편하게-ColumnTransformer-만들기-1.1">2-1) <u>make_column_transformer로 간편하게 ColumnTransformer 만들기</u></a></span></li></ul></li><li><span><a href="#References" data-toc-modified-id="References-2">References</a></span></li></ul></div>

---

필요라이브러리 import


```python
import os
import pandas as pd
import mglearn
import warnings
from sklearn.preprocessing import OneHotEncoder
from sklearn.compose import ColumnTransformer
from sklearn.preprocessing import StandardScaler
from sklearn.linear_model import LogisticRegression
from sklearn.model_selection import train_test_split
warnings.filterwarnings(action='ignore')
```

<br>

---

### 1)  <u>범주형 변수</u>

앞에서는 계속 `연속형 특성(continuous feature)`이라 가정하며 공부를 해왔다. 하지만 대부분의 애플리케이션에서는 이렇게 친절하게 데이터가 수집되지 않는다. 일반적으로 보통은 `범주형/이산형 특성(categorical/discrete feature)`이다. 연속형과 범주형은 매우 큰 차이가 있다고는 말할 수 없지만 <u>(1)출력이 아닌 입력에 대한 것</u>이란 점이 다르다. 예를 들자면, 제품의 브랜드, 색상, 판매 분류 등이 범주형 특성에 속한다. 예시에서도 확인할 수도 있듯이 범주형 특성은 <u>(2)연속된 값으로 나타나지 않는다</u>. 또한 <u>(3)순서가 없다</u>.

<br>

하지만, 데이터가 어떤 형태의 특성으로 구성되어 있는가보다 머신러닝에서 데이터를 어떻게 표현하는가에 따라서 사용하는 모델의 성능이 매우 달라진다. 이처럼 특정 어플리케이션에 가장 적합한 데이터 표현을 찾는 것을 `특성 공학(feature engineering)`이라고 한다. 올바른 데이터 표현은 지도 학습 모델에서 적절한 매개변수를 선택하는 것보다 성능에 더 큰 영향을 끼친다.

<br>

그렇다면 만약 "학사", "석사", "박사"와 같은 범주형 변수들을 로지스틱 회귀와 같은 알고리즘에 학습시킬려면 어떻게해야 할까? 로지스틱 회귀에서 사용하는 변수들은 숫자여야 하므로  "학사", "석사", "박사"는 로지스틱 회귀를 위한 변수가 될 수 없다. 따라서, <u>이를 회귀에 사용하려면 데이터를 다른 방식으로 표현해야 한다</u>.

<br>

---

####  1-1) <u>원 - 핫 - 인코딩(가변수)</u>

- 범주형 변수를 표현하는 데 가장 널리 쓰이는 방법
- 범주형 변수를 0 또는 1 값을 가진 하나 이상의 새로운 특성으로 바꾼다.

<br>

`pandas`나 `scikit-learn`을 이용해 범주형 변수를 `원-핫 인코딩`으로 바꿀 수 있다. 여기서는 `pandas`로 실전처럼 진행해보자.


```python
import os
# 이 파일은 열 이름을 나타내는 헤더가 없으므로 header=None으로 지정하고
# "names" 매개변수로 열 이름을 제공합니다
data = pd.read_csv(
    os.path.join(mglearn.datasets.DATA_PATH, "adult.data"), header=None, index_col=False,
    names=['age', 'workclass', 'fnlwgt', 'education',  'education-num',
           'marital-status', 'occupation', 'relationship', 'race', 'gender',
           'capital-gain', 'capital-loss', 'hours-per-week', 'native-country',
           'income'])
# 예제를 위해 몇개의 열만 선택합니다
data = data[['age', 'workclass', 'education', 'gender', 'hours-per-week',
             'occupation', 'income']]
# IPython.display 함수는 주피터 노트북을 위해 포맷팅된 출력을 만듭니다
display(data.head())
```


<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>age</th>
      <th>workclass</th>
      <th>education</th>
      <th>gender</th>
      <th>hours-per-week</th>
      <th>occupation</th>
      <th>income</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>39</td>
      <td>State-gov</td>
      <td>Bachelors</td>
      <td>Male</td>
      <td>40</td>
      <td>Adm-clerical</td>
      <td>&lt;=50K</td>
    </tr>
    <tr>
      <th>1</th>
      <td>50</td>
      <td>Self-emp-not-inc</td>
      <td>Bachelors</td>
      <td>Male</td>
      <td>13</td>
      <td>Exec-managerial</td>
      <td>&lt;=50K</td>
    </tr>
    <tr>
      <th>2</th>
      <td>38</td>
      <td>Private</td>
      <td>HS-grad</td>
      <td>Male</td>
      <td>40</td>
      <td>Handlers-cleaners</td>
      <td>&lt;=50K</td>
    </tr>
    <tr>
      <th>3</th>
      <td>53</td>
      <td>Private</td>
      <td>11th</td>
      <td>Male</td>
      <td>40</td>
      <td>Handlers-cleaners</td>
      <td>&lt;=50K</td>
    </tr>
    <tr>
      <th>4</th>
      <td>28</td>
      <td>Private</td>
      <td>Bachelors</td>
      <td>Female</td>
      <td>40</td>
      <td>Prof-specialty</td>
      <td>&lt;=50K</td>
    </tr>
  </tbody>
</table>
</div>


위와 같이 데이터셋을 확인했을 때, <u>먼저 열에 어떤 의미있는 범주형 데이터가 있는지 확인해야한다</u>. 어플리케이션 사용자로부터 입력받은 데이터를 다룰 때는 여러가지 예상 밖의 상황이 있을 수 있다. 예를들어, 철자 혹은 대소문자가 틀리거나, 남성을 "male" 또는 "man"이라고도 표현할 수 있다. "male"과 "man"의 경우는 또 둘이 같은 입력값으로 취급해줘야한다. 따라서, 데이터셋을 받고 가장 먼저 할 일은 범주형 데이터를 확인하는 것이다.

<br>

열의 내용을 확인하는 가장 좋은 방법은 `Series`의 `value_counts`메서드를 사용하여 <u>범주형 데이터의 유일값이 각각 몇 번 나타나는지 출력하는 것</u>이다.


```python
print("gender :\n", data['gender'].value_counts())
print()
print("workclass :\n", data['workclass'].value_counts())
print()
print("occupation :\n", data['occupation'].value_counts())
```

    gender :
      Male      21790
     Female    10771
    Name: gender, dtype: int64
    
    workclass :
      Private             22696
     Self-emp-not-inc     2541
     Local-gov            2093
     ?                    1836
     State-gov            1298
     Self-emp-inc         1116
     Federal-gov           960
     Without-pay            14
     Never-worked            7
    Name: workclass, dtype: int64
    
    occupation :
      Prof-specialty       4140
     Craft-repair         4099
     Exec-managerial      4066
     Adm-clerical         3770
     Sales                3650
     Other-service        3295
     Machine-op-inspct    2002
     ?                    1843
     Transport-moving     1597
     Handlers-cleaners    1370
     Farming-fishing       994
     Tech-support          928
     Protective-serv       649
     Priv-house-serv       149
     Armed-Forces            9
    Name: occupation, dtype: int64
    

<br>

`pandas`에서는 `get_dummies`함수를 이용해 쉽게 범주형 데이터를 인코딩할 수 있다.

> `get_dummies` :
> 객체(object) 타입 혹은 범주형(categorical)가진 열을 자동으로 변환해준다.


```python
print("원본 특성 :\n", list(data.columns), "\n")
data_dummies = pd.get_dummies(data)
print("원본 특성 :\n", list(data_dummies.columns))
```

    원본 특성 :
     ['age', 'workclass', 'education', 'gender', 'hours-per-week', 'occupation', 'income'] 
    
    원본 특성 :
     ['age', 'hours-per-week', 'workclass_ ?', 'workclass_ Federal-gov', 'workclass_ Local-gov', 'workclass_ Never-worked', 'workclass_ Private', 'workclass_ Self-emp-inc', 'workclass_ Self-emp-not-inc', 'workclass_ State-gov', 'workclass_ Without-pay', 'education_ 10th', 'education_ 11th', 'education_ 12th', 'education_ 1st-4th', 'education_ 5th-6th', 'education_ 7th-8th', 'education_ 9th', 'education_ Assoc-acdm', 'education_ Assoc-voc', 'education_ Bachelors', 'education_ Doctorate', 'education_ HS-grad', 'education_ Masters', 'education_ Preschool', 'education_ Prof-school', 'education_ Some-college', 'gender_ Female', 'gender_ Male', 'occupation_ ?', 'occupation_ Adm-clerical', 'occupation_ Armed-Forces', 'occupation_ Craft-repair', 'occupation_ Exec-managerial', 'occupation_ Farming-fishing', 'occupation_ Handlers-cleaners', 'occupation_ Machine-op-inspct', 'occupation_ Other-service', 'occupation_ Priv-house-serv', 'occupation_ Prof-specialty', 'occupation_ Protective-serv', 'occupation_ Sales', 'occupation_ Tech-support', 'occupation_ Transport-moving', 'income_ <=50K', 'income_ >50K']
    

위에서 연속형 변수는 그대로지만, 범주형 변수인 "workclass", "education", "gender", "occupation"은 가진 변수값별로 새로운 특성으로 확장된 것을 확인할 수 있다. 그리고 변환된 데이터 프레임은 다음과 같다.



```python
data_dummies.head(5)
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>age</th>
      <th>hours-per-week</th>
      <th>workclass_ ?</th>
      <th>workclass_ Federal-gov</th>
      <th>workclass_ Local-gov</th>
      <th>workclass_ Never-worked</th>
      <th>workclass_ Private</th>
      <th>workclass_ Self-emp-inc</th>
      <th>workclass_ Self-emp-not-inc</th>
      <th>workclass_ State-gov</th>
      <th>...</th>
      <th>occupation_ Machine-op-inspct</th>
      <th>occupation_ Other-service</th>
      <th>occupation_ Priv-house-serv</th>
      <th>occupation_ Prof-specialty</th>
      <th>occupation_ Protective-serv</th>
      <th>occupation_ Sales</th>
      <th>occupation_ Tech-support</th>
      <th>occupation_ Transport-moving</th>
      <th>income_ &lt;=50K</th>
      <th>income_ &gt;50K</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>39</td>
      <td>40</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>...</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>50</td>
      <td>13</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
      <td>...</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>38</td>
      <td>40</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>...</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>53</td>
      <td>40</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>...</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>28</td>
      <td>40</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>...</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
    </tr>
  </tbody>
</table>
<p>5 rows × 46 columns</p>
</div>



<br>

이제 이를 이용해서 모델에 학습시켜보자. 학습시킬 때 타깃값은 "income"으로 지정한다.


```python
features = data_dummies.loc[:, 'age':'occupation_ Transport-moving']
X = features.values
y = data_dummies['income_ <=50K'].values
print("X.shape : {0}, y.shape : {1}".format(X.shape, y.shape))
```

    X.shape : (32561, 44), y.shape : (32561,)
    

<br>

이제 scikit_learn의 로지스틱 회귀에 사용가능한 모델이 되었으니 이전과 동일하게 모델에 적용한다.


```python
from sklearn.linear_model import LogisticRegression
from sklearn.model_selection import train_test_split
X_train, X_test, y_train, y_test = train_test_split(X, y, random_state = 0)
logreg = LogisticRegression()
logreg.fit(X_train, y_train)
print("테스트 점수 : {:.2f}".format(logreg.score(X_test,y_test)))
```

    테스트 점수 : 0.81
    

<br>

---

#### 1-2) <u>숫자로 표현된 범주형 특성 </u>

여기까지 공부하면서 ~~"범주형 특성 = 문자열"~~이라고 생각하는 것은 금물이다. 흔히 말하는 기출변형의 형태로 `범주형 변수`가 `숫자`로 인코딩된 경우도 많다. 이러한 범주형 변수들은 겉으로 보기에는 연속형 변수로 착각하기 쉬워서 주의해야한다.

<br>

앞서 사용한 pandas의 `get_dummies`함수의 경우 `숫자특성을 모두 연속형이라고 생각`<u>해서 가변수를 만들지 않기 때문이다</u>. 다음의 각각 문자열과 숫자로 표현된 두 개의 범주형 특성을 가진 데이터프레임을 예제로 살펴보자.


```python
demo_df = pd.DataFrame({'숫자 특성' : [0, 1, 2, 1], '범주형 특성' : ['양말', '여우', '양말', '상자']})
demo_df
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>숫자 특성</th>
      <th>범주형 특성</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>0</td>
      <td>양말</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1</td>
      <td>여우</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2</td>
      <td>양말</td>
    </tr>
    <tr>
      <th>3</th>
      <td>1</td>
      <td>상자</td>
    </tr>
  </tbody>
</table>
</div>




```python
pd.get_dummies(demo_df)
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>숫자 특성</th>
      <th>범주형 특성_상자</th>
      <th>범주형 특성_양말</th>
      <th>범주형 특성_여우</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>1</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
    </tr>
  </tbody>
</table>
</div>



<br>

하지만, `숫자 특성`도 가변수로 만들고 싶을 경우 `colums`매개변수에 인코딩하고 싶은 열을 다음과 같이 명시하면된다. 혹은 `숫자 특성`의 숫자들을 `astype`과 같은 함수를 통해 문자형으로 바꾸어주면 `columns`로 지정하지 않아도 가변수로 만들어준다.

- <b>정리 : 숫자 특성을 가변수로 만드는 방법</b>
    - 1. get_dummies의 매개변수  columns에 숫자 특성을 가진 열을 명시해준다.
    - 2. 숫자 특성을 가진 열을 문자열로 변환시켜준다.


```python
# columns로 제시할 경우
pd.get_dummies(demo_df,columns = ['숫자 특성', '범주형 특성'])
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>숫자 특성_0</th>
      <th>숫자 특성_1</th>
      <th>숫자 특성_2</th>
      <th>범주형 특성_상자</th>
      <th>범주형 특성_양말</th>
      <th>범주형 특성_여우</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>0</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
    </tr>
    <tr>
      <th>2</th>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>0</td>
      <td>1</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
    </tr>
  </tbody>
</table>
</div>




```python
# astype을 이용해 문자형으로 변경할 경우
demo_df['숫자 특성'] = demo_df['숫자 특성'].astype(str)
pd.get_dummies(demo_df)
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>숫자 특성_0</th>
      <th>숫자 특성_1</th>
      <th>숫자 특성_2</th>
      <th>범주형 특성_상자</th>
      <th>범주형 특성_양말</th>
      <th>범주형 특성_여우</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>0</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
    </tr>
    <tr>
      <th>2</th>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>0</td>
      <td>1</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
    </tr>
  </tbody>
</table>
</div>



<br>

---

### 2) <u>OneHotEncoder 와 ColumnTransformer</u>

앞에서와 같이 `pandas`의 `get_dummies`함수로 `원-핫-인코딩`을 수행한 것 처럼, `scikit-learn`의 `OneHotEncoder`클래스에서도 `원-핫-인코딩`을 수행할 수 있다. 하지만, 앞의 `get_dummies`<u>와 달리 </u>`OneHotEncoder`<u>는 모든 열에 인코딩을 수행한다</u>.


```python
from sklearn.preprocessing import OneHotEncoder

# sparse를 True로 할 경우 "(행, 열) 1"의 좌표리스트의 형식으로 결과를 보여준다.
# 하지만, False로 할 경우 넘파이 배열로 반환된다.
ohe = OneHotEncoder(sparse=False)
print(ohe.fit_transform(demo_df))

# 결과가 pandas의 get_dummies를 사용하여 모든 열을 가변수화 시켰을 때와 같음을 확인할 수 있다.
# scikit-learn에서의 출력은 DataFrame이 아니기 때문에 친절하게 열 이름을 주지 않는다.
pd.DataFrame(ohe.fit_transform(demo_df))
```

    [[1. 0. 0. 0. 1. 0.]
     [0. 1. 0. 0. 0. 1.]
     [0. 0. 1. 0. 1. 0.]
     [0. 1. 0. 1. 0. 0.]]
    




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>0</th>
      <th>1</th>
      <th>2</th>
      <th>3</th>
      <th>4</th>
      <th>5</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>0.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>0.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>0.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
  </tbody>
</table>
</div>



<br>

변환된 특성에 해당하는 원본 범주형 변수 이름을 얻으려면 `get_feature_names` 메서드를 사용해야한다. 아래의 예제에서 첫 번째 원본 특성(x0)는 "숫자 특성", 두 번째 원본 특성(x1)는 "범주형 특성"이다. 


```python
print(ohe.get_feature_names())
```

    ['x0_0' 'x0_1' 'x0_2' 'x1_상자' 'x1_양말' 'x1_여우']
    

<br>

여기까지 공부해봤을 때, 생각하는 것은 "굳이 `OneHotEncoder`를 쓸 필요가 있는건가?" 였다. 왜냐하면, `OneHotEncoder`는 모든 특성을 범주형이라고 가정할 뿐더러 `get_dummies`는 문자형 특성만을 기본값으로 가변수로 바꿀 수 있었으며, 필요에 따라 colums매개변수에 열을 추가할 수 있었다. 하지만, `ColumnTransformer`때문에 사용할 가치가 충분하다고 생각이 바뀌었다.

<br>

`ColumnTransformer`클래스를 사용하게되면, <u>입력 데이터에 있는 열마다 다른 변환을 적용할 수 있게 된다</u>(ㅎㄷㄷ...). 연속형 특성과 범주형 특성은 매우 다른 종류의 전처리 과정이 필요하기 때문에 이클래스는 매우 유용하다. 이를 확인하기위해 다음의 데이터프레임을 살펴보자.


```python
data.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>age</th>
      <th>workclass</th>
      <th>education</th>
      <th>gender</th>
      <th>hours-per-week</th>
      <th>occupation</th>
      <th>income</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>39</td>
      <td>State-gov</td>
      <td>Bachelors</td>
      <td>Male</td>
      <td>40</td>
      <td>Adm-clerical</td>
      <td>&lt;=50K</td>
    </tr>
    <tr>
      <th>1</th>
      <td>50</td>
      <td>Self-emp-not-inc</td>
      <td>Bachelors</td>
      <td>Male</td>
      <td>13</td>
      <td>Exec-managerial</td>
      <td>&lt;=50K</td>
    </tr>
    <tr>
      <th>2</th>
      <td>38</td>
      <td>Private</td>
      <td>HS-grad</td>
      <td>Male</td>
      <td>40</td>
      <td>Handlers-cleaners</td>
      <td>&lt;=50K</td>
    </tr>
    <tr>
      <th>3</th>
      <td>53</td>
      <td>Private</td>
      <td>11th</td>
      <td>Male</td>
      <td>40</td>
      <td>Handlers-cleaners</td>
      <td>&lt;=50K</td>
    </tr>
    <tr>
      <th>4</th>
      <td>28</td>
      <td>Private</td>
      <td>Bachelors</td>
      <td>Female</td>
      <td>40</td>
      <td>Prof-specialty</td>
      <td>&lt;=50K</td>
    </tr>
  </tbody>
</table>
</div>



이 데이터셋에 선형 회귀모델을 적용하여 소득을 예측하려면 
- 범주형 변수를 `원-핫-인코딩`로 변환
- 연속형 변수 age 열과 hours-per-week 열의 `스케일 조정`이 필요

이라는 두 가지의 변환이 필요하다. 이때 빛나는 것이 `ColumnTransformer`이다.<br>
`CounterTransformer`는 다음의 것들이 list로 지정해야 한다.
- 이름
- 변환기 객체 (ex. StandardScaler)
- 변환된 열의 이름 (만약, DataFrame이 아니라 배열에서 사용할 경우 정수 인덱스나 불린 마스크를 사용하면 된다.)

다음의 예를 살펴보자!


```python
from sklearn.compose import ColumnTransformer
from sklearn.preprocessing import StandardScaler

ct = ColumnTransformer(
[("scaling", StandardScaler(), ['age', 'hours-per-week']),
 ("onehot", OneHotEncoder(sparse = False), ['workclass', 'education', 'gender', 'occupation'])])
```

여기서 "scaling"과 "onehot"이 `이름`에 해당하며, StandardScaler와 OneHotEncdoer가 `변환기 객체`에 해당한다. 다른 scikit-learn의 변환기들과 동일하게 `ColumnTransformer`로 생성된 객체 또한 `fit`, `transform` 메서드를 사용할 수 있다.

<br>

이제 이를 이용하여 모델을 학습시켜보자!


```python
from sklearn.linear_model import LogisticRegression
from sklearn.model_selection import train_test_split

# income을 제외한 모든 열을 추출한다. (drop 기억해두자 은근 많이 써먹을 것 같다.)
data_features = data.drop("income", axis = 1)

# 데이터프레임과 incom을 분할한다.
X_train, X_test, y_train, y_test = train_test_split(data_features, data['income'], random_state = 0)

# ColumnTransformer로 생성된 객체 적용
ct.fit(X_train)
X_train_trans = ct.transform(X_train)

# 결과 확인
print(X_train_trans.shape)
pd.DataFrame(X_train_trans)
```

    (24420, 44)
    




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>0</th>
      <th>1</th>
      <th>2</th>
      <th>3</th>
      <th>4</th>
      <th>5</th>
      <th>6</th>
      <th>7</th>
      <th>8</th>
      <th>9</th>
      <th>...</th>
      <th>34</th>
      <th>35</th>
      <th>36</th>
      <th>37</th>
      <th>38</th>
      <th>39</th>
      <th>40</th>
      <th>41</th>
      <th>42</th>
      <th>43</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1.498941</td>
      <td>-0.445097</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>...</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2.380451</td>
      <td>-1.668489</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>...</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>0.250134</td>
      <td>-0.037300</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>...</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>-0.925213</td>
      <td>-0.037300</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>...</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>0.543971</td>
      <td>-0.037300</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>...</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>24415</th>
      <td>0.323594</td>
      <td>-0.037300</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>...</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>24416</th>
      <td>0.397053</td>
      <td>-0.445097</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>...</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>24417</th>
      <td>-1.145591</td>
      <td>-0.037300</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>...</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>24418</th>
      <td>0.470512</td>
      <td>0.370498</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>...</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>24419</th>
      <td>-0.998672</td>
      <td>0.615176</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>...</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
  </tbody>
</table>
<p>24420 rows × 44 columns</p>
</div>



이전에 해당 데이터프레임에 `get_dummies`를 사용했을 때 처럼 44개의 특성을 반환하였다. `get_dummies`와 달라진 점은 연속형 특성을 가진 열에 다른 스케일링 변환이 동시에 적용되었다는 점이다. 

<br>

이제 LogisticRegression모델에 사용할 수 있는 데이터로 변환되었으니 적용해보자!


```python
# 로지스틱회귀 모델 학습
logreg = LogisticRegression()
logreg.fit(X_train_trans, y_train)

X_test_trans = ct.transform(X_test)
print("테스트 점수 : {:.2f}".format(logreg.score(X_test_trans, y_test)))
```

    테스트 점수 : 0.81
    

(해당 데이터에서는 연속형 변수의 데이터 스케일링이 점수에 크게 영향을 미치지는 못했다ㅠ.ㅠ)

<br>

---

#### 2-1) <u>make_column_transformer로 간편하게 ColumnTransformer 만들기</u> 

코딩을 하거나 일을 하다보면 순간 멈칫하는 순간이 변수의 이름을 짓는 순간이다. 마찬가지로, `ColumnTransformer` 객체를 정의할 때 이름을 붙였었다. 하지만 클래스 이름을 기반으로 자동으로 각 단계에 이름을 붙여주는 편리한 `make_column_transformer`함수가 있다. 이 함수는 다음과 같이 사용할 수 있다.


```python
from sklearn.compose import make_column_transformer
ct = make_column_transformer(
    (StandardScaler(), ['age', 'hours-per-week']),
    (OneHotEncoder(sparse=False), ['workclass', 'education', 'gender', 'occupation']))

# ColumnTransformer로 생성된 객체 적용
ct.fit(X_train)
X_train_trans = ct.transform(X_train)

# 결과 확인
print(X_train_trans.shape)
pd.DataFrame(X_train_trans)
```

    (24420, 44)
    




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>0</th>
      <th>1</th>
      <th>2</th>
      <th>3</th>
      <th>4</th>
      <th>5</th>
      <th>6</th>
      <th>7</th>
      <th>8</th>
      <th>9</th>
      <th>...</th>
      <th>34</th>
      <th>35</th>
      <th>36</th>
      <th>37</th>
      <th>38</th>
      <th>39</th>
      <th>40</th>
      <th>41</th>
      <th>42</th>
      <th>43</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1.498941</td>
      <td>-0.445097</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>...</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2.380451</td>
      <td>-1.668489</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>...</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>0.250134</td>
      <td>-0.037300</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>...</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>-0.925213</td>
      <td>-0.037300</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>...</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>0.543971</td>
      <td>-0.037300</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>...</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>24415</th>
      <td>0.323594</td>
      <td>-0.037300</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>...</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>24416</th>
      <td>0.397053</td>
      <td>-0.445097</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>...</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>24417</th>
      <td>-1.145591</td>
      <td>-0.037300</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>...</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>24418</th>
      <td>0.470512</td>
      <td>0.370498</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>...</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>24419</th>
      <td>-0.998672</td>
      <td>0.615176</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>...</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
  </tbody>
</table>
<p>24420 rows × 44 columns</p>
</div>



<br>

---

### References

- 안드레아스 뮐러, 세라 가이도, 『파이썬 라이브러리를 활용한 머신러닝』, 박해선, 한빛미디어(2017)


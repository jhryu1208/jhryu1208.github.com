---
layout: post
title: "[Pandas] DataFrame 데이터 조회 (2) isin"
date: 2020-12-26 00:00:00 +0900
categories: [Data, Pandas]
tags: [Data, Pandas, isin]
comments: true
math: true
mermaid: true
---

## [Pandas] DataFrame 데이터 조회 (2) isin

지난번 `loc`에 이어 같이 알면 좋은 `isin`에 관하여 학습해보고자 한다.

<br>

`isin`은 지난번 `loc`에서처럼 `SQL`의 `WHERE`절과 유사하다.

<br>

메소드를 학습하기에 앞서 예제에서 사용할 데이터를 가져온다. 해당 데이터들은 [KOSIS국가통계포털](https://kosis.kr/index/index.do)에서 찾을 수 있었던 2020년 6월 행정구역별 인구수이다.


```python
import pandas as pd

df = pd.read_excel('./행정구역인구수.xlsx', sheet_name='데이터변경',  engine='openpyxl')
df.columns = ['region', 'tot_num', 'm_num', 'f_num']
df = df.set_index('region')
df[['tot_num', 'm_num', 'f_num']] = df[['tot_num', 'm_num', 'f_num']].astype('int64')

df.head()
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
      <th>tot_num</th>
      <th>m_num</th>
      <th>f_num</th>
    </tr>
    <tr>
      <th>region</th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>전국</th>
      <td>51839408</td>
      <td>25853937</td>
      <td>25985471</td>
    </tr>
    <tr>
      <th>서울특별시</th>
      <td>9720846</td>
      <td>4732275</td>
      <td>4988571</td>
    </tr>
    <tr>
      <th>종로구</th>
      <td>150383</td>
      <td>73288</td>
      <td>77095</td>
    </tr>
    <tr>
      <th>중구</th>
      <td>126092</td>
      <td>61697</td>
      <td>64395</td>
    </tr>
    <tr>
      <th>용산구</th>
      <td>229431</td>
      <td>110527</td>
      <td>118904</td>
    </tr>
  </tbody>
</table>
</div>


<br>

### with List

- `isin`에 입력되는 값이 `list`형태라면 `list`에 들어있는 값이 데이터프레임에 들어있는지 체크한다. 
- 위의 데이터프레임에서 서울특별시의 총 인구수, 남성 인구수, 여성 인구수만 리스트형태로 `isin`에 입력할 경우 데이터프레임에서 해당 value와 동일한 값을 가지는 값이 `True`로 반환되는 것을 확인할 수 있다.

```python
df.isin([9720846, 4732275, 4988571]).head(5)
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
      <th>tot_num</th>
      <th>m_num</th>
      <th>f_num</th>
    </tr>
    <tr>
      <th>region</th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>전국</th>
      <td>False</td>
      <td>False</td>
      <td>False</td>
    </tr>
    <tr>
      <th>서울특별시</th>
      <td>True</td>
      <td>True</td>
      <td>True</td>
    </tr>
    <tr>
      <th>종로구</th>
      <td>False</td>
      <td>False</td>
      <td>False</td>
    </tr>
    <tr>
      <th>중구</th>
      <td>False</td>
      <td>False</td>
      <td>False</td>
    </tr>
    <tr>
      <th>용산구</th>
      <td>False</td>
      <td>False</td>
      <td>False</td>
    </tr>
  </tbody>
</table>
</div>


### with Dict

- `isin`에 입력되는 값이 만약 `{'column_name' : [value1, value2]}`인 `dict`형태라면 `dict` 내부에 입력한 열만 부분적으로 해당 value들이 있나 없나 조회할 수 있다. 
- 쉽게확인하기 위해 region을 열로 전환시키고 region과 전국, 중구, 용산구를 `dict`형식으로 `isin`에 입력시키면 기존 region에서 전국, 중구, 용산구 값이 있던 자리만 True로 반환된 것을 확인할 수 있다.


```python
df = df.reset_index()
df.isin({'region' : ['전국', '중구', '용산구']}).head(5)
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
      <th>region</th>
      <th>tot_num</th>
      <th>m_num</th>
      <th>f_num</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>True</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
    </tr>
    <tr>
      <th>1</th>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
    </tr>
    <tr>
      <th>2</th>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
    </tr>
    <tr>
      <th>3</th>
      <td>True</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
    </tr>
    <tr>
      <th>4</th>
      <td>True</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
    </tr>
  </tbody>
</table>
</div>



<br>


### 응용

- 데이터프레임에서 `True` & `False`로 반환되는 것까지는 이해가 가지만, 이것을 어떻게 써먹을 수 있을까? 나같은 경우는 `isin`을 `SQL`의 `WHERE`과 동일하게 사용한다(이게 당연한 것일 수도 있지만, 또 다른 사람들은 다르게 사용할 것이라 생각한다.). 만약 `MySQL`이나 `Oracle`에서 원하는 지역데이터만을 뽑아오고 싶을 때는 `WHERE region IN('서울특별시', '용산구')`이런식으로 사용할 것이다. 이는 `pandas`에서도 `isin`을 통해 다음과 같이 사용할 수 있다.


```python
df[df['region'].isin(['서울특별시', '용산구'])]
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
      <th>region</th>
      <th>tot_num</th>
      <th>m_num</th>
      <th>f_num</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>1</th>
      <td>서울특별시</td>
      <td>9720846</td>
      <td>4732275</td>
      <td>4988571</td>
    </tr>
    <tr>
      <th>4</th>
      <td>용산구</td>
      <td>229431</td>
      <td>110527</td>
      <td>118904</td>
    </tr>
  </tbody>
</table>
</div>

---

### References

- [pandas](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.isin.html)
- [국가통계포털](https://kosis.kr/index/index.do)


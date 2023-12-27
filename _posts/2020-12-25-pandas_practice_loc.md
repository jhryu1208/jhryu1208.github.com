---
layout: post
title:   "[Pandas] DataFrame 데이터 조회 (1) loc"
subtitle: "[Pandas] DataFrame 데이터 조회 (1) loc"
categories: data
tags: pandas
comments: true
---
#### [Pandas] DataFrame 데이터 조회 (1) loc
본 포스팅은 데이터프레임에서 사용하기 유용한 loc에관한 기본적인 내용에 관하여 다룹니다.

- 특정 행 조회 
- 특정 열 조회
- 특정 행,열 조회
- 조건을 만족하는 열의 값을 가진 행 조회

---
---

평소에 pandas라이브러리로 데이터프레임을 이리저리 다루다 보면 항상 마주치는 에러중에 loc, iloc등을 사용을 권장하는 경고 메시지를 자주 보았었다. 하지만, 매번 공부한다는게 다른거 보기 바빠서 제대로 공부를 못했었고, 가끔 필요할 때 마다 구글링을 하여 사용법을 찾아보았기 때문에 시간 소모를 발생시켰었던 적이 많았다.이를 대체할 문법들은 pandas에 있지만, 해당 메소드들은 기존의 길었던 코드들을 간략하게 만들어주며, SQL에서처럼 python에서 데이터를 다룰 수 있게 해주는 유용성을 가진다. 그래서, 이를 블로그로 포스팅하며 배워보고자 한다.

<br>

메소드들을 사용하기에 앞서 예제에서 사용할 데이터를 가져온다. 해당 데이터들은 [KOSIS국가통계포털](https://kosis.kr/index/index.do)에서 찾을 수 있었던 2020년 6월 행정구역별 인구수이다.


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

---

#### <u>pandas.DataFrame.loc</u>


`loc`는 <u>행과 열의 데이터를 조회할 떄</u> 사용되어지는 메소드이다. 이는 `SQL`의 `WHERE절`과 유사해서 pandas에서 더욱더 유용한 메소드 중 하나이다.

<br>

<b>1) <u>Select Rows</b></u>
- 특정 행 조회
- 특정 행 다중 조회
- 특정 행 제외후 조회
- 특정 범위의 연속 행 조회

<br>

`loc`를 이용해 특정을 행을 조회하고 싶은 경우 해당 행이 존재하는 <b>`index name`</b>을 입력해야한다. (혹시 자신이 원하는 행을 넣기 위해 index를 넣었지만 오류가 떠서 고생하시는 분들이 계신다면 진짜로 입력하는 값이 index인지 확인하자 아닐 경우 `set_index()`를  적용하자)


```python
# 하나의 특정 행만을 조회할 경우
df.loc['전국']
```




    tot_num    51839408
    m_num      25853937
    f_num      25985471
    Name: 전국, dtype: int64



<br>

하지만, `Series`형태로 출력되기에 `DataFrame`형태를 주로 사용하는 나에게는 불편요소이다. 따라서 이를 <u>데이터프레임으로 바꾸어 조회하기 위해서는 </u>`array`형태로 index name을 입력해야한다.


```python
# series로 조회되는 loc반환값을 데이터 프레임으로 변경
df.loc[['전국']]
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
  </tbody>
</table>
</div>



<br>

<u>특정 행을 여러개 가져오고 싶을 경우</u> 가져오고 싶은 index name들을 `array` 형태로 입력해주면 된다.


```python
# 특정 행을 여러개 조회하고 싶을 경우
df.loc[['서울특별시','종로구']]
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
  </tbody>
</table>
</div>



<br>

<u>특정행을 제외하여 가져오고 싶은 경우</u> 다음과 같이 `!=`을 이용하여 간단하게 나타낼 수 있다. 하지만 아래의 코드에서는 array형태로 입력되지 않아서 의아해하는 분들이 계실 수도 있다. 이 경우 `df.index != [index_name]`의 타입과 결과를 조회하면 쉽게 넘어갈 수 있다.


```python
# 특정 행을 제외하여 조회하고 싶을 경우
df.loc[df.index != '전국'].head(5)
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
    <tr>
      <th>성동구</th>
      <td>297397</td>
      <td>145258</td>
      <td>152139</td>
    </tr>
  </tbody>
</table>
</div>




```python
print(type(df.index))
print(type(df.index != '전국'))
print('df.index != \'전국\' results :', list(df.index != '전국')[1:5])
```

    <class 'pandas.core.indexes.base.Index'>
    <class 'numpy.ndarray'>
    df.index != '전국' results : [True, True, True, True]
    

<br>

물론, list에서 처럼 <u>인덱싱을 이용해 연속으로 행을 조회</u>할 수 있다. <br>만약 특정 column이 index로 지정되지 않았을 경우에는 인덱스 넘버를 이용해서 똑같이 수행할 수 있다.


```python
# 인덱싱을 이용해서 연속 행 조회
df.loc['서울특별시': '용산구']
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

<b>2) <u>Select Columns</b></u>
- 특정 열 조회
- 특정 열 다중 조회
- 특정 열 제외 후 조회
- 특정 범위의 연속 열 조회

<br>

열을 조회한다고 해서 행을 조회하는 것이랑 크게 다르지 않다. 단순히 `:`와 `,`를 이용한다는 것인데, 이는 `array`에서 열을 조회하는 방법과 동일하다. 주의할 점이 있다면, 이전에 데이터프레임 형태의 결과값을 얻기 위해서 `index_name`을 `array`형태로 변환하여 입력하였었다. 마찬가지로 열을 조회할 때, 데이터프레임 형태의 결과값을 반환 받기 위해서는  `column_name`을 `array`형태로 변환하여 입력한다는 점을 주의하자.

<br>

<u>특정 열을 하나 조회하고 싶은 경우</u>는 다음과 같다.


```python
# 특정 열 하나를 조회
df.loc[:,['tot_num']].head(5)
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
    </tr>
    <tr>
      <th>region</th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>전국</th>
      <td>51839408</td>
    </tr>
    <tr>
      <th>서울특별시</th>
      <td>9720846</td>
    </tr>
    <tr>
      <th>종로구</th>
      <td>150383</td>
    </tr>
    <tr>
      <th>중구</th>
      <td>126092</td>
    </tr>
    <tr>
      <th>용산구</th>
      <td>229431</td>
    </tr>
  </tbody>
</table>
</div>



<br>

<u>특정 열을 다중으로 조회</u>하는 경우도 앞전과 크게 다르지 않다.


```python
# 특정 열 다중 조회
df.loc[:, ['tot_num', 'm_num']].head(5)
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
    </tr>
    <tr>
      <th>region</th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>전국</th>
      <td>51839408</td>
      <td>25853937</td>
    </tr>
    <tr>
      <th>서울특별시</th>
      <td>9720846</td>
      <td>4732275</td>
    </tr>
    <tr>
      <th>종로구</th>
      <td>150383</td>
      <td>73288</td>
    </tr>
    <tr>
      <th>중구</th>
      <td>126092</td>
      <td>61697</td>
    </tr>
    <tr>
      <th>용산구</th>
      <td>229431</td>
      <td>110527</td>
    </tr>
  </tbody>
</table>
</div>



<br>

<u>특정 열을 제외하고 가져오는 것</u>도 마찬가지이다. 단지 `.columns`메소드를 이용해야한다는 점을 주의하자!


```python
# 특정 열을 제외하여 조회하기
df.loc[:, df.columns != 'm_num'].head(5)
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
      <th>f_num</th>
    </tr>
    <tr>
      <th>region</th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>전국</th>
      <td>51839408</td>
      <td>25985471</td>
    </tr>
    <tr>
      <th>서울특별시</th>
      <td>9720846</td>
      <td>4988571</td>
    </tr>
    <tr>
      <th>종로구</th>
      <td>150383</td>
      <td>77095</td>
    </tr>
    <tr>
      <th>중구</th>
      <td>126092</td>
      <td>64395</td>
    </tr>
    <tr>
      <th>용산구</th>
      <td>229431</td>
      <td>118904</td>
    </tr>
  </tbody>
</table>
</div>



<br>

<u>연속 컬럼을 가져오는 것</u>도 다를바 없다!


```python
# 인덱싱을 이용해서 연속 열 조회
df.loc[:, 'tot_num' : 'f_num'].head(5)
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

<b>3) <u>Select Rows & Cols</u></b>

<br>

특정 행과 열을 동시에 조회하는 것은 위의 두 가지를 짬뽕시킬 뿐이다.


```python
# 인덱싱을 이용해 행과 열을 제한하여 조회
df.loc['서울특별시' : '용산구', 'm_num' : 'f_num']
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
      <th>m_num</th>
      <th>f_num</th>
    </tr>
    <tr>
      <th>region</th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>서울특별시</th>
      <td>4732275</td>
      <td>4988571</td>
    </tr>
    <tr>
      <th>종로구</th>
      <td>73288</td>
      <td>77095</td>
    </tr>
    <tr>
      <th>중구</th>
      <td>61697</td>
      <td>64395</td>
    </tr>
    <tr>
      <th>용산구</th>
      <td>110527</td>
      <td>118904</td>
    </tr>
  </tbody>
</table>
</div>



<br>

<b>4) <u>조건을 만족하는 행/열 조회</u></b>

<br>

<u>특정 index_name하나와 특정 column_name을 하나씩</u> loc에 입력해줄 경우 이에 속한 값을 출력하게 된다.


```python
# 하나의 값 출력
df.loc['서울특별시', 'tot_num']
```




    9720846



<br>

또한, 특정 조건을 만족하는 값을 가진 데이터프레임만을 부분 출력도 가능하다.<br>
아래의 첫번째 코드를 말로 풀어 설명하면, `m_num`컬럼의 값이 70000보다 작은 값을 가진 모든 행들을 출력한다는 의미이다.


```python
# 조건 1개
df.loc[df['m_num'] <= 70000].head(5)
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
      <th>중구</th>
      <td>126092</td>
      <td>61697</td>
      <td>64395</td>
    </tr>
    <tr>
      <th>중구</th>
      <td>41652</td>
      <td>20411</td>
      <td>21241</td>
    </tr>
    <tr>
      <th>서구</th>
      <td>107947</td>
      <td>52341</td>
      <td>55606</td>
    </tr>
    <tr>
      <th>동구</th>
      <td>89698</td>
      <td>44003</td>
      <td>45695</td>
    </tr>
    <tr>
      <th>영도구</th>
      <td>115120</td>
      <td>56700</td>
      <td>58420</td>
    </tr>
  </tbody>
</table>
</div>




```python
# 조건 2개
df.loc[(df['m_num'] <= 70000) & (df['f_num'] <= 70000)].head(5)
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
      <th>중구</th>
      <td>126092</td>
      <td>61697</td>
      <td>64395</td>
    </tr>
    <tr>
      <th>중구</th>
      <td>41652</td>
      <td>20411</td>
      <td>21241</td>
    </tr>
    <tr>
      <th>서구</th>
      <td>107947</td>
      <td>52341</td>
      <td>55606</td>
    </tr>
    <tr>
      <th>동구</th>
      <td>89698</td>
      <td>44003</td>
      <td>45695</td>
    </tr>
    <tr>
      <th>영도구</th>
      <td>115120</td>
      <td>56700</td>
      <td>58420</td>
    </tr>
  </tbody>
</table>
</div>




```python
# 조건 2개 + 특정 컬럼만 출력
df.loc[(df['m_num'] <= 70000) & (df['f_num'] <= 70000), ['tot_num']].head(5)
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
    </tr>
    <tr>
      <th>region</th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>중구</th>
      <td>126092</td>
    </tr>
    <tr>
      <th>중구</th>
      <td>41652</td>
    </tr>
    <tr>
      <th>서구</th>
      <td>107947</td>
    </tr>
    <tr>
      <th>동구</th>
      <td>89698</td>
    </tr>
    <tr>
      <th>영도구</th>
      <td>115120</td>
    </tr>
  </tbody>
</table>
</div>



<br>

또한 은근 유용할 것 같은 케이스도 있다. <br>다음과 같이 `= value`를 뒤에 이어줄 경우, 해당 조건을 만족하는 행의 값들을 value(숫자도 문자도 가능)로 모두 변경할 수도 있다.


```python
df.loc[df['m_num'] >= 500000] = 'hi'
df
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
      <td>hi</td>
      <td>hi</td>
      <td>hi</td>
    </tr>
    <tr>
      <th>서울특별시</th>
      <td>hi</td>
      <td>hi</td>
      <td>hi</td>
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
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>거창군</th>
      <td>61681</td>
      <td>30123</td>
      <td>31558</td>
    </tr>
    <tr>
      <th>합천군</th>
      <td>44515</td>
      <td>21402</td>
      <td>23113</td>
    </tr>
    <tr>
      <th>제주특별자치도</th>
      <td>671316</td>
      <td>337343</td>
      <td>333973</td>
    </tr>
    <tr>
      <th>제주시</th>
      <td>490254</td>
      <td>245805</td>
      <td>244449</td>
    </tr>
    <tr>
      <th>서귀포시</th>
      <td>181062</td>
      <td>91538</td>
      <td>89524</td>
    </tr>
  </tbody>
</table>
<p>292 rows × 3 columns</p>
</div>



<br>

이처럼 `loc`메소드는 데이터프레임에서 행열을 조회하는데 매우 도움이 된다. 또한 multi index인 데이터프레임에서도 `loc`메소드를 이용하여 원하는 데이터프레임을 일부 출력할 수도 있다! ([참고](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.loc.html)). 

<br>

`loc`에서는 각 인덱스 혹은 컬럼의 `name`을 사용했지만 , `iloc`를 이용하여 각 인덱스와 컬럼의 `number` 을 이용하여 `loc`와 동일하게 사용할 수 있다.  데이터 사이즈가 작다면 `iloc`가 유용할 수 있지만, 데이터사이즈가 많은 데이터에서는 `iloc`보다는 `loc`가 더 유용하게 사용되어진다. 

<br>

다음에는 `isin`에 관해서  간단히 다뤄보고자 한다.

<br>

---

### References

- [pandas](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.loc.html)
- [국가통계포털](https://kosis.kr/index/index.do)


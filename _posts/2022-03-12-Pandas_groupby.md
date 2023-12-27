---
layout: post
title: "[Pandas] 데이터 그룹 집계1 : groupby"
subtitle: "[Pandas] 데이터 그룹 집계1 : groupby"
categories: data
tags: pandas
comments: true
mathjax: true
---
#### Contents
- [groupby 메소드](#groupby-메소드)
  - [1) 1개 열을 기준으로 그룹화](#1-1개-열을-기준으로-그룹화)
  - [2) 여러 열을 기준으로 그룹화](#2-여러-열을-기준으로-그룹화)
  - [3) 그룹 연산 메소드](#4-그룹-연산-메소드)
  - [4) by에 function/dict(혹은 Series)가 전달되는 경우](#3-by에-functiondict혹은-series가-전달되는-경우)
  - [5) as_index](#5-as_index)
  - [6) level](#6-level)

해당 포스팅에서는 pandas의 `groupby`에 대해서 정리하였습니다.

<br>

---


이후에 소개될 예제들은 해당 데이터를 이용한다. 이 데이터는 스포츠 뉴스를 다루는 rotowire에서 기록한 2021 KBO의 타자별 데이터를 전처리한 데이터이다. 총 305명 선수의 소속 팀(team)과  수비할 때의 선수 포지션(pos), 그리고 시리즈 동안의 홈런 수(home_runs)를 담고 있으며, Null값은 존재하지 않는다. 

```python
project_path = os.getcwd()
full_file_path = project_path + '\data_example\kbo-player-stats-Batters.csv'
df = pd.read_csv(full_file_path, encoding = 'utf-8')
df = df.rename(columns = {'Player': 'name', 'Team': 'team', 
                        'Pos': 'pos', 'HR': 'home_runs'})
df = df[['name', 'team', 'pos', 'home_runs']]

def team_eng_to_kor(team_ab):
    ...
def pos_eng_to_kor(pos_ab):
    ...

df['team']=df['team'].apply(team_eng_to_kor)
df['pos']=df['pos'].apply(pos_eng_to_kor)

print(df.head(5))
print(df.info())
```

```bash
            name  team    pos  home_runs
0   Won Jun Choi   KIA  외야수          4
1    Sung Bum Na    NC  외야수         33
2  Hye Seong Kim   키움  유격수         3
3    Jose Pirela   삼성  외야수        29
4   Jun Woo Jeon   롯데  외야수         7

RangeIndex: 305 entries, 0 to 304
Data columns (total 4 columns):
 #   Column     Non-Null Count  Dtype 
---  ------     --------------  ----- 
 0   name       305 non-null    object
 1   team       305 non-null    object
 2   pos        305 non-null    object
 3   home_runs  305 non-null    int64 
dtypes: int64(1), object(3)
```

<br>

---

## <span style="color:navy">groupby 메소드<span>

`groupby`메소드의 기본 format및 해당 포스팅에서 확인할 파라미터는 다음과 같다.  

```python
DataFrame.groupby(by=None, as_index=True, level=None,	dropna=True)
```

- `by` ( ***mapping, function, label, or list of labels** )*
    - 해당 파라미터를 통해서 그룹 집계의 기준이되는 key를 결정한다.
    - 보통 list 형태로 기준으로 지정하는 key를 묶어서 전달한다.
    - dict혹은 Series가 전달될 경우 해당 객체들의 value가 그룹의 기준이 된다.
    - function혹은 mapping함수가 전달될 경우, Index를 함수의 input값으로 사용하였을 때 반환된 결과가 그룹의 기준이 된다.
    - 더불어, 해당 파라미터를 코드에서 굳이 명시하지 않아도 괜찮다.
- `level` **( *int, level name, or sequence of such, default None* )**
    - MultiIndex를 가진 DataFrame에 적용할 경우 특정 level에 의해서 그룹을 수행한다.
    - level=0일 경우 첫 번째 인덱스를 그룹의 key로 지정한다.
    - level=1일 경우 두 번째 인덱스를 그룹의 key로 지정한다.
- `as_index` ( ***bool, default True** )*
    - `True` : 그룹의 키로 지정된 열이 집계함수로 반환된 DataFrame의 Index로 지정된다.
    - `False` : 위와 같이 Index를 지정하지 않는다. (일반적인 정수형 Index로 반환)
- `dropna` ( ***bool, default True** )*
    - `True` : group의 key의 value가 NaN인 경우를 제외한다.
    - `False` : group의 key의 value가 NaN인 경우를 포함한다.
    - 대부분의 경우 default로 지정하여 사용한다.

<br>

### <span style="color:navy">1) 1개 열을 기준으로 그룹화<span>

다음의 예제는 예시 DataFrame에 오직 team열만을 기준으로 `groupby`메소드를 적용한 결과이다. `DataFrameGroupBy`라는 클래스 개체가 반환된 것을 확인할 수 있다.

그리고, 해당 객체는 그룹의 **key와 DataFrame을 튜플로 관리**하기 때문에  반복문을 이용해서 그룹 key와 그룹핑된 DataFrame 객체를 확인할 수 있다.

```python
group_df = df.groupby(['team'])

print(type(group_df))
for key, group in group_df:
    print(f'Group Key : {key}')
    print(f'DataFrame Row : {len(group)}')
    print(group.head(2))
```

```
<class 'pandas.core.groupby.generic.DataFrameGroupBy'>
Group Key : KIA
DataFrame Row : 31
    name        team  pos      home_runs
0   Won Jun Choi  KIA   외야수    4
13  Sun Bin Kim   KIA   2루수     5

Group Key : LG
DataFrame Row : 30
    name        team  pos      home_runs
9   Chang Ki Hong LG    외야수    4
12  Hyun Soo Kim  LG    외야수    17

... 생략 ...

Group Key : 키움
DataFrame Row : 31
    name          team  pos      home_runs
2   Hye Seong Kim  키움  유격수    3
23  Jung Hoo Lee   키움  외야수    7

Group Key : 한화
DataFrame Row : 36
    name          team  pos      home_runs
8   Ju Suk Ha     한화  유격수     10
14  Eun Won Jung   한화  2루수      6
```

<br>

그리고, 여기서 `get_group()` 메소드를 통해서 특정 그룹만을 선택하여 가져올 수 있다.

```python
grouped_kia = group_df.get_group('KIA')
print(grouped_kia.head(5))
```

```
              name team  pos  home_runs
0     Won Jun Choi  KIA  외야수        4
13     Sun Bin Kim  KIA  2루수         5
21  Preston Tucker  KIA  외야수        9
39    Chan Ho Park  KIA  유격수        1
51     Tae Jin Kim  KIA  3루수         1
```

<br>

### <span style="color:navy">2) 여러 열을 기준으로 그룹화<span>

다음은 팀과 포지션별로 예시 DataFrame을 그룹화시키는 예제이다. 그룹의 기준이 되는 두 key를 리스트로 묶어서 `groupby`메소드에 기입한다.  `groupby`메소드의 결과 `DataFrameGroupBy`클래스 객체가 반환되는 것을 확인할 수 있으며 두 열이 갖는 원소 값들로 만들 수 있는 모든 조합으로 키를 생성하는 것을 확인할 수 있다.

```python
multi_key = ['team', 'pos']
multi_group_df = df.groupby(multi_key)

print(type(multi_group_df))
for key, group in multi_group_df:
    print(f'Group Key : {key}')
```

```
<class 'pandas.core.groupby.generic.DataFrameGroupBy'>
Group Key : ('KIA', '1루수')
Group Key : ('KIA', '2루수')
Group Key : ('KIA', '3루수')
Group Key : ('KIA', '외야수')
Group Key : ('KIA', '유격수')
Group Key : ('KIA', '포수')

... 생략 ...

Group Key : ('한화', '1루수')
Group Key : ('한화', '2루수')
Group Key : ('한화', '3루수')
Group Key : ('한화', '외야수')
Group Key : ('한화', '유격수')
Group Key : ('한화', '포수')
```

<br>

단일 열 그룹 객체와 마찬가지로 `get_group()`메소드를 이용해서 특정 그룹의 DataFrame만 선택할 수 있다. 이때, 멀티 그룹 key는 **튜플**로 묶어서 `get_group`의 인자로 전달해야한다.

```python
grouped_lg_ss = multi_group_df.get_group(("LG", "유격수"))
print(grouped_lg_ss)
```

```
              name team  pos     home_runs
24      Ji Hwan Oh   LG  유격수          8
121   Young Bin Lee   LG  유격수          2
203    Bon Hyeok Gu   LG  유격수          0
```

<br>

### <span style="color:navy">3) 그룹 연산 메소드<span>

위에서 생성한 `DataFrameGroupBy`에는 각 그룹별로 다양한 집계치 연산을 수행할 수 있다. 기본적으로 pandas의 `DataFrameGroupBy`객체에는 다음과 같은 기본 메소드가 내장되어 있다.

| method  | description     | method     | description  |
|---------|-----------------|------------|--------------|
| max()   | 최대              | size()     | row 수        |
| min()   | 최소              | var()      | 분산           |
| sum()   | 합계              | std()      | 표준 편차        |
| count() | 열별 카운팅(NaN제외)   | mean()     | 평균           |
| first() | 첫 번째 행          | describe() | 통계 요약        |
| last()  | 마지막 행           | nunique()  | Distinct 카운팅 |

<br>

그 중, 대표적으로 `count()`와 `describe()`를 사용해보자.

`count()`를 사용했을 때는 다음과 같이 **각 열별 NaN이 아닌 값을 카운팅**한다. 해당 데이터 프레임의 경우 위에서 보았듯이, NaN이 존재하는 열이 없었기 때문에 각 team 그룹별 모든 열의 `count()` 반환 값이 동일한 것을 확인할 수 있다.

```python
x = df.groupby(['team'])
print(x.count())
```

```
        name  pos   home_runs
team                      
KIA       31   31         31
LG        30   30         30
NC        34   34         34
SSG       28   28         28
kt        27   27         27
두산      28   28         28
롯데      31   31         31
삼성      29   29         29
키움      31   31         31
한화      36   36         36
```

<br>

`describe()`의 경우 각 그룹에서의 **실수 혹은 정수형 값이 포함된 열을 기준으로 통계값**(row 수, 평균, 표준편차, 최소, 백분위, max)을 집계한다. 만약, 실수 혹은 정수형 타입의 값을 가진 열이 두 개이상 존재한다면, 각 열마다 통계값을 형성한다.

```python
x = df.groupby(['team'])
print(x.describe())
```

```
home_runs                                               
         count      mean     std  min  25%  50%   75%   max
team                                                         
KIA       31.0  2.129032  3.432608  0.0  0.0  1.0  3.00  13.0
LG        30.0  3.666667  4.887234  0.0  0.0  1.5  7.25  17.0
NC        34.0  5.000000  8.984836  0.0  0.0  1.0  6.75  33.0
SSG       28.0  6.607143  9.538768  0.0  0.0  3.0  7.25  35.0
kt        27.0  3.925926  4.937501  0.0  0.0  1.0  6.50  16.0
두산        28.0  3.928571  7.512600  0.0  0.0  0.0  5.00  28.0
롯데        31.0  3.451613  5.111025  0.0  0.0  1.0  4.50  19.0
삼성        29.0  4.586207  8.130706  0.0  0.0  1.0  4.00  29.0
키움        31.0  2.935484  5.341258  0.0  0.0  1.0  3.50  22.0
한화        36.0  2.222222  3.742506  0.0  0.0  0.5  3.25  18.0
```

<br>

### <span style="color:navy">4) by에 function/dict(혹은 Series)가 전달되는 경우<span>

 

다음은 홈런 수에 따라서 범주형 데이터로 나누는 함수를 `groupby`에 적용한 예제이다.  정의한 함수를 통해서 양적 데이터를 범주형 데이터로 변환하였으며, 해당 범주 열을 기준으로 고유 타자 수를 카운팅하였다. 결과적으로 각 범주형 데이터에 속하는 고유 타자 수를 집계할 수 있다.

```python
def create_category(col):
    if (0<col) & (col<=10):
        return '0<hit<=10'
    elif (10<col) & (col<=20):
        return '10<hit<=20'
    elif (20<col) & (col<=30):
        return '20<hit<=30'
    else:
        return '30<hit'

x = df.set_index('home_runs').groupby(create_category)[['name']].count()
print(x)
```

```
            name
home_runs       
0<hit<=10    141
10<hit<=20    18
20<hit<=30     8
30<hit       138
```

<br>

다음은 각 팀(key)에 따른 연고지(value)로 이루어진 dict와 Series를 `groupby`의 인자로 전달하여 연고지별 고유 타자 수를 카운팅하는 예제이다.

```python
region_dict = {'한화': '대전', 'NC': '창원', 'KIA': '광주',
             '롯데': '부산', '키움': '서울', 'LG': '서울',
             '삼성': '대구', 'SSG': '인천', '두산': '서울',
             'kt': '수원'}

region_sr = pd.Series(region_dict)

x = df.set_index('team').groupby(region_dict)[['name']].nunique()\
				.rename(columns={'name':'unique_cnt_by_dict'})
y = df.set_index('team').groupby(region_sr)[['name']].nunique()\
				.rename(columns={'name':'unique_cnt_by_sr'})
result = x.merge(y, left_index=True, right_index=True)
result.index.name = 'hometown'

print(result)
```

```
          unique_cnt_by_dict    unique_cnt_by_sr
hometown
광주                    31                31
대구                    29                29
대전                    36                36
부산                    31                31
서울                    88                88
수원                    27                27
인천                    28                28
창원                    34                34
```

<br>

### <span style="color:navy">5) as_index<span>

위에서 볼 수 있듯이, 그룹 연산 메소드를 수행했을 때 그룹이 되는 기준 열을 기준으로 Index가 설정되어 결과물이 출력되는 것을 확인할 수 있다.  

이와 같은 결과가 출력되는 이유는 `as_index`라는 파라미터가 기본적으로 `True`로 설정되어있기 때문이다. 따라서,  **Index를 열로 변환할 필요가 있을 경우**에는 `reset_index()`를 사용할 수 있지만, `groupby()`의 `as_index`파라미터를 `False`로 지정해서사전에 방지할 수 있다.

다음의 예제에서 `as_index=False`로 지정하여 반환된 결과와 `reset_index()`를 적용했을 때 반환된 집계 DataFrame의 열이 동일한 것을 확인할 수 있다.

```python
as_index_example = df.groupby(['team'], as_index=False).count()
reset_index_example = df.groupby(['team']).count().reset_index()

print(f"[ as_index를 False로 지정 했을 경우의 열 ]\n{as_index_example.columns}")
print(f"[ reset_index를 사용했을 경우의 열 ]\n{reset_index_example.columns}")
```

```
[ as_index를 False로 지정 했을 경우의 열 ]
Index(['team', 'name', 'pos', 'home_runs'], dtype='object')

[ reset_index를 사용했을 경우의 열 ] 
Index(['team', 'name', 'pos', 'home_runs'], dtype='object')
```

<br>

### <span style="color:navy">6) level<span>

`level`파라미터는 멀티 인덱스에서의 집계치 연산을 수행할 때 도움이 된다.  

예를 들어 다음과 같이 타자들의 팀과 수비 포지션(내야 수비 포지션만을 고려)을 기준으로 집계한 총 홈런 수 데이터가 있을 때,

```python
group_df = df[df['pos'].isin(['1루수', '2루수', '3루수'])]\
              .groupby(['team', 'pos']).sum()
print(group_df)
```

```
            home_runs
team   pos           
KIA   1루수         17
      2루수         5
      3루수         1
LG    1루수         27
      2루수         5
      3루수         17

... 생략 ...

키움  1루수          20
      2루수         4
      3루수         21
한화   1루수         15
      2루수         11
      3루수         18
```

<br>

`level=0`인 경우, **첫 번째 인덱스를 기준으로 집계함수를 적용**하며, <br>`level=1`인 경우, **두 번째 인덱스를 기준으로 집계함수를 적용**하는 것을 확인할 수 있다.

```python
level0_df = group_df.groupby(level=0).mean()
level1_df = group_df.groupby(level=1).mean()
print(level0_df)
print(level1_df)
```

```
        home_runs
team           
KIA     7.666667
LG      16.333333
NC      8.000000
SSG     27.666667
kt      14.333333
두산    20.666667
롯데    22.666667
삼성    12.666667
키움    15.000000
한화    14.666667

       home_runs
pos           
1루수       22.4
2루수       10.3
3루수       15.2
```

<br>

---

## <span style="color:navy">References<span>


- [rotowire KBO Player CSV](https://www.rotowire.com/)
- [pandas doc: pandas.DataFrame.groupby](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.groupby.html)
- 오승환, 『파이썬 머신러닝 판다스 데이터 분석』, 정보문화사 (2019)
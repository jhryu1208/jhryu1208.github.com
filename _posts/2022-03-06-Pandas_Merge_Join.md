---
layout: post
title: "[Pandas] 데이터 병합/연결2 : merge, join"
subtitle: "[Pandas] 데이터 병합/연결2 : merge, join"
categories: data
tags: pandas
comments: true
mathjax: true
---
#### Contents
- [merge 메소드](#merge-메소드)
- [join 메소드](#join-메소드)

지난 포스팅에 이어서 pandas의 `merge`와 `join`에 대해서 정리하였습니다.

<br>

---

## <span style="color:navy">merge 메소드<span>

다음은 `merge`메소드의 기본 format이다.

```python
DataFrame1.merge(DataFrame2, how='inner', 
                 on=None,
                 #| left_on=None, right_on=None, 
                 #| left_index=False, right_index=False, 
                 suffixes=('_x', '_y'))

"""
혹은 아래와 같이 사용할 수 있다.
pd.merge(DataFrame1, DataFrame2, how='innter',
				 on=None,
				 #| left_on=None, right_on=None, 
				 #| left_index=False, right_index=False, 
				 suffixes=('_x', '_y'))
"""
```

- `DataFrame1` (_**DataFrame or named Series**_)
  - merge의 대상에 해당하는 객체로, merge로 인해 출력된 결과의 좌측에 위치한다.
- `DataFrame2` (_**DataFrame or named Series**_)
  - merge의 대상에 해당하는 객체로, merge로 인해 출력된 결과의 우측에 위치한다.
- `how` (_**{‘left’, ‘right’, ‘outer’, ‘inner’, ‘cross’}, default ‘inner’**_)
    - 두 객체를 어떤 방식으로 조인시킬 것인지 결정한다.
    - `left`, `right`, `outer`, `inner`, `cross` 조인을 지정할 수 있으며, 
    `inner` 조인이 default에 해당한다.

<br>

- 조인의 기준이되는 Key를 지정하는 파라미터는 아래와 같다.
    - `on` (_**label or list**_)
        - Column을 조인 Key로 지정할 때 사용한다.
        - 주로 각 객체의 조인 Key가 동일한 이름일 경우에 사용한다.
    - `left_on`, `right_on` (_**label or list, or array-like**_)
        - Column을 조인 Key로 지정할 때 사용한다.
            
            주로 각 객체의 조인 Key가 동일한 이름이 아닐 경우에 사용한다. 
            
        - 이때 `left_on`에는 DataFrame1의 조인 Key를 지정하며,
            
            `right_on`에는 DataFrame2의 조인 Key 지정한다.
            
        - 물론  `on`대신 사용할 수 있다.
    - `left_index`, `right_index` (_**bool, default False**_)
        - Index를 조인 Key로 지정할 때 주로 사용한다.
        - 이때 `left_index`에는 DataFrame1의 조인 Key를 지정하며,
            
            `right_index`에는 DataFrame2의 조인 Key를 지정한다.
            
        - 물론 `set_index`와 함께 `on`대신 사용할 수 있다.

<br>
        
- `suffixes` (_**list-like, default is (“_x”, “_y”)**_)
    - `merge`되는 두 객체가 동일한 Column의 이름을 가지고 있을 경우, `merge`로 인해 출력된 결과에서 동일한 이름의 Column에 지정한 접미사를 붙여 구분할 때 사용한다.

<br>

조인 방식에 대한 차이점을 보다는 조인의 Key를 지정하는 방식에 초점을 맞추어 보도록하자. 

다음은 예제에 사용될 DataFrame이다. df1의 경우 직원의 이름과 직원의 부서 ID(≈ foreign key)를 담고있는 DataFrame이며, df2의 경우 부서 ID(≈ primary key)와 해당 부서 이름을 담고 있는 DataFrame이다.

```python
df1 = pd.DataFrame([['A', 1], ['B', 2], ['C', 3]],
                    index = ['row1', 'row2', 'row3'],
                    columns=['name', 'deptno'])

df2 = pd.DataFrame([[1, 'RESEARCH'], [2, 'MARKETER'], 
                    [3, 'DEVELOPER'], [4, 'CEO']],
                    index = ['row1', 'row2', 'row3', 'row4'],
                    columns=['id', 'dname'])

print('[ df1, 직원 테이블 ]\n', df1)
print()
print('[ df2, 부서 테이블 ]\n', df2)
```

```
[ df1, 직원 테이블 ]
      name  deptno
row1    A       1
row2    B       2
row3    C       3

[ df2, 부서 테이블 ]
      id      dname
row1   1   RESEARCH
row2   2   MARKETER
row3   3  DEVELOPER
row4   4        CEO
```

<br>

각 직원이 어떤 부서에 속한지 확인하기 위하여 df1과 df2에 `merge`를 적용해보자. 이때, 조인의 Key로는 각각 deptno와 id를 지정할 것이며, 두 Column의 이름이 다르기 때문에 `left/right_on`을 사용하였다. 

```python
result = df1.merge(df2, left_on = 'deptno', right_on = 'id', how = 'left')
print(result)
```

```
  name  deptno  id      dname
0    A       1   1   RESEARCH
1    B       2   2   MARKETER
2    C       3   3  DEVELOPER
```

<br>

이번에는 조인의 기준이 되는 Column의 이름을 동일하게 변경하고, 조인의 Key를 `on`과 `left/right_on`을 각각 이용한 예제이다. 서로 다른 이름의 Column을 조인 기준으로 이용했을 때와 달리 반환값에서는 동일 Column의 중복이 없는 것을 확인할 수 있다. 

```python
df1.rename(columns={'deptno':'Key'}, inplace = True)
df2.rename(columns={'id':'Key'}, inplace = True)

result1 = df1.merge(df2, on = 'Key', how = 'left')
result2 = df1.merge(df2, left_on = 'Key', right_on = 'Key', how = 'left')

print('[on]\n', result1)
print()
print('[left/right_on]\n', result2)
```

```
[on]
  name  Key      dname
0    A    1   RESEARCH
1    B    2   MARKETER
2    C    3  DEVELOPER

[left/right_on]
  name  Key      dname
0    A    1   RESEARCH
1    B    2   MARKETER
2    C    3  DEVELOPER
```

<br>

다음으로, `left/right_index`를 사용하는 예제를 확인해보자.

먼저, 위의 DataFrame의 Join 기준이 되는 Key를 Index로 변경하였다.  그리고, 변경된 Index를 기준으로 `left/right_index`를 `True`로 지정한 merge를 두 객체에 적용하였다. 

```python
sr1 = df1.set_index('Key')
sr2 = df2.set_index('Key')

result = pd.merge(sr1, sr2, left_index=True, right_index=True, how='inner')
print(result)
```

```
    name      dname
Key                
1      A   RESEARCH
2      B   MARKETER
3      C  DEVELOPER
```

<br>

마지막으로, `suffixes` 파라미터를 사용하는 예제를 확인해보자. 

다음 예제에서 조인의 기준이 되는 key값을 제외하고 두 DataFrame에 dup1과 dup2라는 동일한 Column이 존재하는 것을 확인할 수 있으며, `suffixes`파라미터를 적용한 merge의 반환값으로 두 중복된 Column이 `suffixes`파라미터에 의해 지정한 접미사로 구분되는 것을 확인할 수 있다. 

```python
df1 = pd.DataFrame([[1, 2, 3],[3, 4, 5],[5, 6, 7]], columns=['Key', 'dup1', 'dup2'])
df2 = pd.DataFrame([[1, 2, 3],[3, 4, 5],[5, 6, 7]], columns=['Key', 'dup1', 'dup2'])

result = pd.merge(df1, df2, on='Key', suffixes=('_x', '_y'))
print(result)
```

```
   Key  dup1_x  dup2_x  dup1_y  dup2_y
0    1       2       3       2       3
1    3       4       5       4       5
2    5       6       7       6       7
```

<br>

---

## <span style="color:navy">join 메소드<span>

`join`메소드의 기본 형태는 다음과 같다. 해당 메소드는 `merge`메소드를 기반으로 만들어졌기 때문에 기본적인 파라미터는 `merge`와 동일한 것을 확인할 수 있다. 

```python
DataFrame.join(other, on=None, how='left', lsuffix='', rsuffix='', sort=False)
```

그리고, `join`은 `left/right_index`를 지정한 `merge`와 동일한 결합 방식을 사용한다. 

즉, `join`은 행 인덱스를 기준으로 두 객체를 결합시킨다.

<br>

위의 직원 데이터와 부서 데이터를 `merge`대신  `join`을 이용해서 결합시키는 예제는 다음과 같다. 이때 반환된 DataFrame의 인덱스는 sr1을 따라가는 것을 확인할 수 있다.

```python
df1 = pd.DataFrame([['A', 1], ['B', 2], ['C', 3]],
                   index = ['row1', 'row2', 'row3'],
                   columns=['name', 'deptno'])

df2 = pd.DataFrame([[1, 'RESEARCH'], [2, 'MARKETER'], [3, 'DEVELOPER'], [4, 'CEO']],
                   index = ['row1', 'row2', 'row3', 'row4'],
                   columns=['id', 'dname'])

sr1 = df1.set_index('deptno')
sr2 = df2.set_index('id')

result = sr1.join(sr2)
print(result)
```

```
       name      dname
deptno                
1         A   RESEARCH
2         B   MARKETER
3         C  DEVELOPER
```

<br>

---

## <span style="color:navy">References<span>

- [pandas doc : pandas.DataFrame.merge](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.merge.html)
- [pandas.doc : pandas.DataFrame.join](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.join.html)
- 오승환, 『파이썬 머신러닝 판다스 데이터 분석』, 정보문화사 (2019)

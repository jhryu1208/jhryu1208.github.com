---
layout: post
title:  "[Pandas] 함수매핑2 : applymap, pipe"
subtitle: "[Pandas] 함수매핑2 : applymap, pipe"
categories: data
tags: pandas
comments: true
mathjax: true
---

#### Contents
- [applymap 메소드](#applymap-메소드)
- [pipe 메소드](#pipe-메소드)

이전 포스팅에 이어서 dataframe의 `applymap`과 `pipe`메소드에 대해서 정리하였습니다.

<br>

---

## <span style="color:navy">applymap 메소드<span>

`applymap`은 `apply`와 비슷한 기능을 수행하는 함수이다. 하지만, `applymap`은 `apply`와 달리 <u>dataframe의 요소 별로  매핑 함수를 적용할 수 있다는 장점</u>이 있다.  (Apply a function to a Dataframe elementwise)

<br>

`applymap`의 기본 format은 다음과 같다.

```python
[DataFrame 객체].applymap(mapping_func)
```

- `mapping_func`
    - <u>하나의 값으로 부터 하나의 결과 값을 리턴하는 함수</u>
    - 따라서,  `applymap`메소드는 `apply`메소드와 달리 <u>집계 함수를 사용할 수 없다</u>.
    (물론, 기입은 할 수 있고 에러도 발생하지 않지만, 집계값이 반환되지 않는다.)

<br>

다음은 DataFrame의 일부 요소들을 양수에서 음수로 변환하는 예제이다.

```python
df = pd.DataFrame([[1, 2, 3], [5, 3, 2], [3, 4, 1]],
                  index = ['row1', 'row2', 'row3'],
                  columns = ['col1', 'col2', 'col3'])

copy_df0 = df.copy()
copy_df0.loc['row2':'row3', 'col2':'col3'] = \
copy_df0.loc['row2':'row3', 'col2':'col3'].applymap(lambda x: -x)

print(copy_df0)
```

```
      col1  col2  col3
row1     1     2     3
row2     5    -3    -2
row3     3    -4    -1
```

<br>

그리고, 다음은 정수형 DataFrame 요소들을 실수로 변환하는 예제이다.

```python
df = pd.DataFrame([[1, 2, 3], [5, 3, 2], [3, 4, 1]],
                  index = ['row1', 'row2', 'row3'],
                  columns = ['col1', 'col2', 'col3'])

copy_df1 = df.copy()
copy_df1 = copy_df1.applymap(float)

print(copy_df1)
```

```
      col1  col2  col3
row1   1.0   2.0   3.0
row2   5.0   3.0   2.0
row3   3.0   4.0   1.0
```

<br>

`applymap`은 `apply`와 달리 DataFrame의 집계에 사용하기 적합하지 않으며  주로 위의 예제에서 확인할 수 있듯이 <u>DataFrame의 요소를 변환(Transformation)하는데 적합</u>하다.

그리고, 아래의 예제에서 볼 수 있듯이  <u>두 메소드 모두 하나의 값을 반환하는 함수를 입력받을 경우에는 동일한 결과를 출력하는 것을 확인</u>할 수 있다. 이때, 주로 performance가 더 좋은 것은 `applymap`이지만, 데이터의 상황에 따라 다르므로 두 메소드를 모두 적용해보고 더 나은 메소드를 고르는 것을 추천한다. 

```python
df = pd.DataFrame([[1, 2, 3], [5, 3, 2], [3, 4, 1]],
                  index = ['row1', 'row2', 'row3'],
                  columns = ['col1', 'col2', 'col3'])

func = lambda x : x+10

result_df0 = df.apply(func, axis=0)
result_df1 = df.applymap(func)

print(result_df0)
print()
print(result_df1)
```

```
      col1  col2  col3
row1    11    12    13
row2    15    13    12
row3    13    14    11

      col1  col2  col3
row1    11    12    13
row2    15    13    12
row3    13    14    11
```

<br>

---

## <span style="color:navy">pipe 메소드<span>

`pipe`메소드는 사용하는 <u>함수가 반환하는 리턴 값에 따라서 반환하는 객체의 종류가 결정</u>된다. DataFrame을 반환할 수 있고, Series를 반환할 수 있으며, 그리고 하나의 값을 반환할 수도 있다는 의미이다.

이렇게 범용적인 `pipe`메소드는 pandas의 `chainable function`이다. 즉, 해당 메소드를 이용하여 객체에 연속적으로 함수를 적용할 수 있다는 의미이다. 

<br>

다음은 `pipe`메소드의 기본 format이다.

```python
[ DataFrame 객체 | Series 객체 ].pipe(func, *args, **kwargs)
```

- `func` : Series혹은 DataFrame에 적용가능한 함수를 받는다.
- `*args` : func에 전달될 arguments를 받는다.
- `**kwargs` : func에 전달될 keyword arguments를 전달 받는다.

<br>

아래는 `pipe`메소드를 이용하여 아이템 판매 로그에서 특정 판매 개수를 만족하는 아이템의 총 판매 금액을 반환하는 코드이다. 아래와 같이 사용하는 경우는 없겠지만 `pipe`를 이해하기 위한 예제이기에 어떻게 `pipe`를 사용하는지만 보도록 하자.

```python
# 다음의 DataFrame은 판매 아이템에 대한 로그이며, 
# 각 column은 판매된 아이템의 이름, 개수, 그리고 아이템의 판매 총 가격을 의미한다.
df = \
pd.DataFrame([['item A', 1, 20900],
              ['item B', 1, 3200],
              ['item C', 5, 100],
              ['item D', 1, 4000],
              ['item E', 3, 5000]], columns = ['name', 'cnt', 'price'])

# 특정 판매 개수를 만족하는 아이템 판매 기록을 필터링한 DataFrame을 반환합니다.
def cnt_filter(df, a):
    cond = df.cnt == a
    filter_df = df.loc[cond]
    return filter_df

# 아이템 가격을 Series로 반환합니다.
def get_sr(df, col_name):
    return df[col_name]

# Series의 Value를 모두 더합니다.
def sum_val(sr):
    return sr.sum()

x = sum_val(get_sr(cnt_filter(df, 1), 'price')) # 일반
y = df.pipe(cnt_filter, 1).pipe(get_sr, 'price').pipe(sum_val) # pipe 이용

print(x, y)
```

```
28100 28100
```

위에서 볼 수 있듯이 함수안에 객체를 넣어서 처리하는 방식보다 `pipe` 메소드를 이용하여 순차적으로 처리하는 방식이 더 직관적이고 알기 쉬움을 알 수 있다.

<br>

---

## <span style="color:navy">References<span>

- [pandas doc : pandas.DataFrame.applymap](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.applymap.html?highlight=applymap)
- [pandas doc : pandas.DataFrame.pipe](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.pipe.html?highlight=pipe)
- [pandas doc : pandas.Series.pipe](https://pandas.pydata.org/docs/reference/api/pandas.Series.pipe.html?highlight=pipe#pandas.Series.pipe)
- 오승환, 『파이썬 머신러닝 판다스 데이터 분석』, 정보문화사 (2019)

---
layout: post
title:  "[Pandas] DataFrame : Delete"
subtitle: "[Pandas] DataFrame : Delete"
categories: data
tags: pandas
comments: true
mathjax: true
---
#### Contents
- [행/열 삭제: drop](#행열-삭제-drop)

본 포스팅은 Pandas의 DataFrame : Delete부분 중 데이터프레임의 행/열 삭제에 관하여 정리하였습니다.

<br>

---

## <span style="color:navy">행/열 삭제: drop<span>

데이터프레임에서 행 혹은 열을 삭제하기 위해 `drop`메소드가 사용되어진다. <br> `drop`메소드는 다음과 같은 특징 혹은 옵션을 가진다.

- 행을 삭제할지 열을 삭제할지는 `axis` 옵션을 이용하여 구분한다.
    - `axis = 0` or `Default` : 행을 삭제한다.
    - `axis = 1` : 열을 삭제한다. <br>(열을 삭제할 경우 필수 옵션이니 까먹지 말자!)
- 동시에 여러 행 혹은 열을 삭제할 때는 리스트 형태로 기입한다.
- `drop` 메소드의 경우 원본 객체를 변경하지 않고 새로운 값을 반환한다. 하지만,  `inplace` 옵션을 이용하여 원본 객체를 변경할 수 있다.

```python
# 행을 지울 경우
DataFrame.drop( 단일 행 인덱스 이름 or [행이름1, 행이름2, ...], 
		axis = 0, inplace = ... )
				
# 열을 지울 경우
DataFrame.drop( 단일 열 이름 or [열이름1, 열이름2, ...], 
		axis = 1, inplace = ... )
```

```python
print('[ 행 인덱스/열 이름 삭제 전 ]\n', df)

print('\n==========================================')

df_copy1 = df.copy()
df_copy1.drop('c0', inplace = True)
print('\n[ 특정 단일 행 삭제 ]\n', df_copy1)

df_copy2 = df.copy()
df_copy2.drop(['c0', 'c2'], axis = 0, inplace = True)
print('\n[ 특정 다중 행 삭제 ]\n', df_copy2)

print('\n==========================================')

df_copy3 = df.copy()
df_copy3.drop('a0', axis = 1, inplace = True)
print('\n[ 특정 다중 열 삭제 ]\n', df_copy3)

df_copy4 = df.copy()
df_copy4.drop(['a0', 'a2'], axis = 1, inplace = True)
print('\n[ 특정 다중 열 삭제 ]\n', df_copy4)
```

```
[ 행 인덱스/열 이름 삭제 전 ]
     a0  a1  a2
c0   1   4   7
c1   2   5   8
c2   3   6   9

==========================================

[ 특정 단일 행 삭제 ]
     a0  a1  a2
c1   2   5   8
c2   3   6   9

[ 특정 다중 행 삭제 ]
     a0  a1  a2
c1   2   5   8

==========================================

[ 특정  열 삭제 ]
     a1  a2
c0   4   7
c1   5   8
c2   6   9

[ 특정 다중 열 삭제 ]
     a1
c0   4
c1   5
c2   6
```

---
layout: post
title:  "[Python] Python 3에서 다른 경로에 저장된 파일 import하기"
subtitle: "[Python] Python 3에서 다른 경로에 저장된 파일 import하기"
categories: devlang
tags: python
comments: true
---
#### [Python] Python 3에서 다른 경로에 저장된 파일 import하기
-  본 포스팅은 `Python3`에서 다른 경로에 저장되어 있는 모듈을 import하는 방법을 소개한다.
- 동일/하위/상위 경로
- 절대 경로

---

pip등의 명령어로 install 할 수 없는 깃허브에서 개인적으로 다운 받은 모듈이나 직접 만든 모듈이 현재 작업하는 공간과 동 떨어져 있는 경우 no module에러와 마주치게된다. <br>이때  아래와 같은 방법들로 쉽게 `import`할 수 있다.

<br>

#### <u> 동일경로</u>

`import`하고자 하는 모듈이 작업하는 공간과 동일한 폴더에 있을 경우<br>현재 경로를 의미하는 `.`을 사용하여 `import`할 수 있다.

```python
from . import want_to_import
```

<br>

#### <u>하위경로</u>

`import`하고자 하는 모듈혹은 파일이 작업 중인 파일 공간과 같은 위치에 있는 폴더 안에 있을 경우 `from 하위폴더`를 지정하여 `import`할 수 있다.

```python
from [하위폴더] import want_to_import
```

<br>

#### <u>상위경로</u>

`import`하고자 하는 모듈혹은 파일이 저장된 폴더가<br> 작업 중인 파일의 상위 폴더에 위치해있을 때는 다음과 같이 작성한다.

```python
import sys
sys.path.append(os.path.dirname(os.path.abspath(os.path.dirname(__file__))))
from [상위폴더에_존재하는_폴더] import want_to_import
# 혹은 상위폴더에 모듈이 존재할 경우 from . import want_to_import
```

여기서`(os.path.dirname(os.path.abspath`는  한단계 상위를 의미한다. <br>따라서 두단계 상위폴더라면 `(os.path.dirname(os.path.abspath(os.path.dirname(os.path.abspath`<br>와 같이 두번 작성해주어야한다.

<br>
  
#### <u>절대경로</u>

절대경로를 이용해 `import`하려면 아래와 같이 `sys.path.append`를 이용한다.

```python
import sys
sys.path.append([파일이_저장된_구체적_경로])

from . import want_to_import
```

---

### References
  
-   [https://weejw.tistory.com/40](https://weejw.tistory.com/40)
-   [https://codechacha.com/ko/how-to-import-python-files/](https://codechacha.com/ko/how-to-import-python-files/)





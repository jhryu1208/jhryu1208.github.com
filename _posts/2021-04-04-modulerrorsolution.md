---
layout: post
title:  "[Python] jupyter notebook ModuleNotFoundError Solution"
subtitle: "[Python] jupyter notebook ModuleNotFoundError Solution"
categories: devlang
tags: python
comments: true
---
제가 최근까지 mac에서는 자주 경험하지 못했지만, window에서는 아래의 문제를  자주 경험하였습니다. 

```python
ModuleNotFoundError: No module named 'package name'
```

분명 cmd를 통해 설치되어있는 python 라이브러리를 확인하면 분명 설치되어있는데 jupyter notebook에서 import하려고하면 해당 라이브러리가 없다고 오류가 없어서 못 불러온다고 합니다 (생각만 해도 아찔한 부분).

제가 많이 보았던 해결책 중에 하나는 처음 파이썬 설치시 path환경변수에 관한 설정을 하지 못해서 직접 path 환경변수에 파이썬이 설치된 디렉토리를 추가해주는 방법이 있습니다. 저는 해당 방법으로 실패하였으며, 파이썬을 완전히 재설치하기에도 애매하였기에 다음과 같은 방법으로 문제를 확인하고 해결하였습니다.

<br>

---

### 문제 원인 파악 

python shell 그리고 jupyter  notebook 에서 각각 어디에 설치되어있는 python을 사용하는지 다음의 명령어를 이용해  확인할 필요가 있습니다. 

```python
import sys
print(sys.executable)
```

저의 경우는 문제를 확인하였을 때,  python shell에서 확인한 결과 anaconda3의 디렉토리에 위치한 파이썬을 이용하고 있었습니다. 더불어,  pip도 해당 디렉토리에 위치되어있습니다 (`where pip`명령어를 통해서 pip가 어디 설치되어있는지 확인할 수 있습니다. mac의 경우는 `where`이 아니라 `which`를 사용하시면 됩니다!). 

![3](https://user-images.githubusercontent.com/53929665/113511466-e2f2d780-959a-11eb-8a9e-6111a388f195.JPG)


<br>

jupyter notebook에서도 동일 명령어를 실행했을 때, python shell과 다른 위치에 설치되어 있는 python을 사용하는 것을 확인할 수 있습니다.

![1](https://user-images.githubusercontent.com/53929665/113511284-12551480-959a-11eb-9c12-91df3e109d5c.JPG)

<br>

즉,  다음과 같이 두 파이썬 경로가 달랐으며, 제대로 라이브러리를 설치했음에도 불구하고 설치한 라이브러리가 없는 폴더에서 import하려고 했기 때문에 문제가 발생했음을 알 수 있습니다. (python과 anaconda를 설치할 때 path를 신경써서 설치하였더라면 발생하지 않았을 문제였을텐데 ㅠ.ㅠ...)
 
![4](https://user-images.githubusercontent.com/53929665/113511797-acb65780-959c-11eb-8c07-58bfe7397ee2.JPG)

<br>

그렇다면 문제를 해결하려면 다음과 같이 jupyter notebook에서 사용하고 있는 python3의 위치를 python shell에서 확인되어진 디렉토리로 변경하거나, python shell에서  사용하고 있는 python3의 위치를 위와 반대로 변경하거나 해야합니다. 

![5](https://user-images.githubusercontent.com/53929665/113512028-c3a97980-959d-11eb-8f5b-673f1a2bd372.JPG)

<br>

---

### 문제 해결 과정

저는 문제를 해결하기 위해서 Jupyter notebook이 사용하는 python3의 디렉토리를 anaconda 가상환경으로 변경하고자합니다.

<br>

먼저 cmd에서 아래의 명령어를 통해 jupyter notebook에서 어떤 커널의 python3를 사용가능한지 확인해봅시다.

```cmd
jupyter kernelspec list
```

저는 기존에 로컬로 연결된 디렉토리 한개가 존재하네요. 따라서, python3가 설치되어있는 anaconda 가상환경 커널을 추가해줘야겠네요!
![25](https://user-images.githubusercontent.com/53929665/113512269-d4a6ba80-959e-11eb-9416-697a23c17a41.JPG)

<br>

다음의 명령어를 사용하여 현재 설치되어있는 가상환경의 리스트를 확인해봅시다.

```cmd
conda info -env
```

저는 가상환경을 직접 따로 설치한 적이 없어서 base라는 이름을 가진 가상환경만 확인할 수 있었습니다.
![6](https://user-images.githubusercontent.com/53929665/113512515-f3f21780-959f-11eb-8e2c-86398ba42923.JPG)

<br>

저는 해당 가상환경이 활성화(activate)된 상태이므로,  해당 (기존 python shell에서 python3가 설치되어있음을 확인한)가상환경을 jupyter notebook 커널에 연결시켜주기만하면 될 것 같습니다.  연결은 다음의 명령어를 통해 진행하시면 됩니다.   (저는 표시할 이름에 conda-python3라고 명시했습니다.)

```cmd
python -m ipykernel install --user \
--name 가상환경이름 \
--display-name "jupyter 커널에서 표시할 이름"
```

`jupyter kernelspec list`명령어로 다시 확인해보니 제대로 추가된 것을 확인할 수 있습니다! (짝짝짝)
![7](https://user-images.githubusercontent.com/53929665/113512667-a5914880-95a0-11eb-94f9-252f5d072db5.JPG)

확인한 뒤에 jupyter notebook의 커널을 확인하시면 제가 등록한 conda-python3라는 가상환경이  등록된 것을 확인할 수 있습니다.
![8](https://user-images.githubusercontent.com/53929665/113512737-f86b0000-95a0-11eb-8af1-e57d150f9b17.JPG)

<br>
위의 과정을 진행하고난 뒤에 새로 연결 시킨 커널로 ipynb파일을 생성하고 import에 실패하던 라이브러리를 다시 import해보니 성공적으로 수행되었습니다.


<br>

----

### References

-  https://chancoding.tistory.com/86
- https://d-tail.tistory.com/37


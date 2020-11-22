---
layout: post
title:  "[Python] 이미지 주소를 이용하여 이미지 출력"
subtitle: "[Python] 이미지 주소를 이용하여 이미지 출력"
categories: devlang
tags: python
comments: true
---
#### [Python] 이미지 주소를 이용하여 이미지 출력 및 저장
- 이미지 주소를 통해 이미지 <u>다운 후</u> 사용
    - `curl`
    - `urllib.request.urlretrieve`
- 이미지 주소를 통해 이미지 <u>다운 없이</u> 사용
    - `urllib.request.urlopen`
    - `request.get`

---


#### <u>curl</u>

python내부에서 직접 코드로 `curl`요청을 보내 이미지를 다운 받을 수 있다.


```python
import os
import time
from PIL import Image

# 다운받을 이미지 url
url = "https://search.pstatic.net/common/?src=http%3A%2F%2Fpost.phinf.naver.net%2FMjAxODExMTZfMjE0%2FMDAxNTQyMzU4MDY1OTE3.mke0JLFBO4jS-hJojejDruHQmJkV7b4gKs3oRfn7tdIg.1LxHXj9zP7M09hPrht0iW17TRKkmCAgV6kEjTgPtPDcg.JPEG%2FI1P4kSElZvKVehuLxO8qMBSTUkIU.jpg&type=sc960_832"

# time check
start = time.time()

# curl 요청
# curl "이미지 주소" > "저장 될 이미지 파일 이름" 
os.system("curl " + url + " > test.jpg")

# 이미지 다운로드 시간 체크
print(time.time() - start)


# 저장 된 이미지 확인
curl_img = Image.open("./test.jpg")
```

    0.0780174732208252
    

<br>

#### <u> urllib.request.urlretrieve </u>

`curl`과 마찬가지로 `urllib`라이브러리를 이용하여 이미지 주소를 통해 이미지를 외부에서 다운받을 수 있다.


```python
import urllib.request
from PIL import Image

url = "https://search.pstatic.net/common/?src=http%3A%2F%2Fpost.phinf.naver.net%2FMjAxODExMTZfMjE0%2FMDAxNTQyMzU4MDY1OTE3.mke0JLFBO4jS-hJojejDruHQmJkV7b4gKs3oRfn7tdIg.1LxHXj9zP7M09hPrht0iW17TRKkmCAgV6kEjTgPtPDcg.JPEG%2FI1P4kSElZvKVehuLxO8qMBSTUkIU.jpg&type=sc960_832"

# time check
start = time.time()


# 다양한 오류로 인해 실행이 안되어 긴급하게 아래의 세 줄 코드를 추가하여 수행하였다.
# 기존에는 내가 사용하던 방식도 공부할 때 참조했던 블로그에서도
# urllib.request.urlretrieve(url, PATH)을 사용하였는데
# 해당 방식이 HTTP Error 403: Forbidden에서 한 번, 정규표현식에서 한 번
# 에러가 발생하여 아래의 방법을 수행하게 되었다.
# 왜 에러가 발생했는지에 관하여 좀더 알아봐야겠다.
class AppURLopener(urllib.request.FancyURLopener):
    version = "Mozilla/5.0"
    
urllib._urlopener = AppURLopener()

urllib._urlopener.retrieve(url, "test_2.jpg")

# 이미지 다운로드 시간 체크
print(time.time() - start)

# 저장 된 이미지 확인
urlretrieve_img = Image.open("test_2.jpg")
```

    0.04400992393493652
    


      
    


```python
urlretrieve_img
```




![python_image_url_4_0](https://user-images.githubusercontent.com/53929665/99908420-ee4d1100-2d25-11eb-8225-5f95d8ca1aac.png)



<br>

#### <u>urllib.request.urlopen</u>

이미지 저장 없이 바로 사용하는 방법은 `urlopen`을 이용하면된다.


```python
from PIL import Image
import urllib.request
import time
from io import BytesIO

url = "https://search.pstatic.net/common/?src=http%3A%2F%2Fpost.phinf.naver.net%2FMjAxODExMTZfMjE0%2FMDAxNTQyMzU4MDY1OTE3.mke0JLFBO4jS-hJojejDruHQmJkV7b4gKs3oRfn7tdIg.1LxHXj9zP7M09hPrht0iW17TRKkmCAgV6kEjTgPtPDcg.JPEG%2FI1P4kSElZvKVehuLxO8qMBSTUkIU.jpg&type=sc960_832"

# time check
start = time.time()

# request.urlopen()
# HTTP Error 403: Forbidden 에러 때문에 하단의 소스 한 줄을 추가해주었다.
req = urllib.request.Request(url, headers = {"User-Agent" : "Mozilla/5.0"})
res = request.urlopen(req).read()

# 이미지 다운로드 시간 체크
print(time.time() - start)


# Image open
urlopen_img = Image.open(BytesIO(res))
```

    0.04500985145568848
    


```python
urlopen_img
```




![python_image_url_7_0](https://user-images.githubusercontent.com/53929665/99908422-eee5a780-2d25-11eb-9a84-5947ab875b53.png)



<br>

#### <u>request.get</u>

이미지 저장 없이 바로 사용하는 방법의 또 다른 방법으로 `request.get`이 있다.


```python
import requests
from io import BytesIO
from PIL import Image
import time

# 다운받을 이미지 url
url = "https://search.pstatic.net/common/?src=http%3A%2F%2Fpost.phinf.naver.net%2FMjAxODExMTZfMjE0%2FMDAxNTQyMzU4MDY1OTE3.mke0JLFBO4jS-hJojejDruHQmJkV7b4gKs3oRfn7tdIg.1LxHXj9zP7M09hPrht0iW17TRKkmCAgV6kEjTgPtPDcg.JPEG%2FI1P4kSElZvKVehuLxO8qMBSTUkIU.jpg&type=sc960_832"

# time check
start = time.time()

# request.get 요청
res = requests.get(url)

# 이미지 다운로드 시간 체크
print(time.time() - start)

#Img open
request_get_img = Image.open(BytesIO(res.content))
```

    0.0400090217590332
    


```python
request_get_img
```




![python_image_url_10_0](https://user-images.githubusercontent.com/53929665/99908423-ef7e3e00-2d25-11eb-8f96-6f5073e1f4da.png)




<br>

---

### References
- [https://soyoung-new-challenge.tistory.com/92](https://soyoung-new-challenge.tistory.com/92)
- [이미지 출처](https://m.post.naver.com/viewer/postView.nhn?volumeNo=17116205&memberNo=11567548&vType=VERTICAL)


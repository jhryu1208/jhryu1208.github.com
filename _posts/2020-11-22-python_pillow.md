---
layout: post
title:  "[Python] 파이썬 이미지 처리 : Pillow"
subtitle: "[Python] 파이썬 이미지 처리 : Pillow"
categories: devlang
tags: python
comments: true
---
#### [Python] 파이썬 이미지 처리 : Pillow
- 이미지 열기 : `Image.open([file_path], mode='r')`
- 이미지 크기 출력 :  `[이미지 객체].size`
- 이미지를 다른 포멧으로 저장 : `[이미지 객체].save()`
- Thumbnail 이미지 생성 : `[이미지 객체].thumbnail()`
- 이미지 부분 잘라내기 : `[이미지 객체].crop`
- 이미지 회전 및 사이즈 조정 : `[이미지 객체].rotate()`, `[이미지 객체].resize()`


---

### <u>파이썬 이미지 처리</u>

파이썬에서 이미지를 처리하고 다루기 위해서 `Pillow`, `urllib` 등의 외부 패키지를 설치해서 사용한다. 그리고 이미지 주소를 가지고 이미지 파일을 다운로드하기 위한 방법들도 파이썬 내부에 존재한다. 이들 중 `PILLOW`에 관하여 알아보고자 한다.

<br>

---

### <u>Pillow</u>

`Pillow` 패키지는 파이썬 이미지 라이브러리로서 다양한 포맷의 이미지들의 처리 기능을 제공하고 있다. 이 패키지를 사용하면, 이미지로부터 Thumbnail이미지를 만들다거나 다른 이미지 포맷으로 변환할 수 있고 이미지를 출력하는 일들을 할 수 있다. 또한, 이미지 크기를 변형하거나 회전 및 Transform, 그리고 필터링 등 다양한 이미지 프로세싱 작업들을 수행할 수 있다.


```python
from PIL import Image
```

#### 1. 이미지 열기 : `Image.open([file_path], mode='r')`

아래와 같이 img변수에 이미지에 관한 객체 정보가 전달되어 출력하면 이미지가 정상적으로 출력됨을 확인할 수 있다. 또한, `mode`매개변수는 `r`이외는 사용할 수 없다.


```python
PATH = './image_processing_figure/'

img = Image.open(PATH +'test.jpg', mode = 'r')
```


```python
img
```




![python_pillow_5_0](https://user-images.githubusercontent.com/53929665/99906577-3fa3d300-2d1b-11eb-9143-221201df7896.png)



<br>

#### 2. 이미지 크기 출력 :  `[이미지 객체].size`


```python
img.size
```




    (560, 313)



<br>

#### 3. 이미지를 다른 포멧으로 저장 : `[이미지 객체].save()`

수행 결과 원하는 path에 정상적으로 jpg에서 jpeg로 포맷이 변경되어 저장되어 있음을 확인할 수 있다.


```python
img.save('./image_processing_figure/test_jpg_to_jpeg.jpeg')
```

![1](https://user-images.githubusercontent.com/53929665/99905697-cbb2fc00-2d15-11eb-9684-808dcd010218.JPG)


<br>

#### 4. Thumbnail 이미지 생성 : `[이미지 객체].thumbnail()`



```python
# thumbnail 이미지 지정
size = (64, 64)
img.thumbnail(size)

# thumbnail 이미지 저장
img.save(PATH + 'test_thumbnail.jpg')
```

![2](https://user-images.githubusercontent.com/53929665/99905826-a672bd80-2d16-11eb-8b8a-9b70721d3422.JPG)

<br>

#### 5. 이미지 부분 잘라내기 : `[이미지 객체].crop`

이미지의 일부를 잘라내는 것을 Cropping이라한다. 이미지 객체에서 `crop()`메서드를 사용하여 이미지의 일부 영역을 잘라 낼 수 있다. `crop()` 메서드에 의해 반환된 이미지는 부분 이미지로서 잘라낸 이미지만 저장된다. `crop()`의 매개변수는 `(좌, 상, 우, 하)`의 위치를 저장하는 튜플로 지정한다.


```python
croped_img = img.crop((10, 10, 200, 200))
croped_img.save(PATH + 'croped_test.jpg')
```


```python
croped_img
```




![python_pillow_16_0](https://user-images.githubusercontent.com/53929665/99906578-3fa3d300-2d1b-11eb-99c0-a515e1603c16.png)



<br>

#### 6. 이미지 회전 및 사이즈 조정 : `[이미지 객체].rotate()`, `[이미지 객체].resize()`


```python
# 이미지 회전
rotate_angle = 90
rotated_img = img.rotate(rotate_angle)
rotated_img.save(PATH + 'rotated_img.jpg')

# 이미지 사이즈 조정
size = (60, 60)
resized_img = img.resize(size)
resized_img.save(PATH + 'resized_test.jpg')
```


```python
rotated_img
```




![python_pillow_19_0](https://user-images.githubusercontent.com/53929665/99906580-403c6980-2d1b-11eb-88c3-db5972f1189b.png)




```python
resized_img
```




![python_pillow_20_0](https://user-images.githubusercontent.com/53929665/99906572-3dda0f80-2d1b-11eb-96a9-c5a4c3e2200d.png)



<br>

#### 7. 이미지 필터링 : `[이미지 객체].filter()`
- 이미지 필터에 관한 매개변수들은 [다음](https://pillow.readthedocs.io/en/5.1.x/reference/ImageFilter.html) 사이트를 참조!!


```python
from PIL import Image, ImageFilter
# 이미지를 흐리게 필터링 BLUR
blur_test = img.filter(ImageFilter.BLUR)
# 이미지의 윤곽만 따오는 필터링 COUNTOUR
contour_test = img.filter(ImageFilter.CONTOUR)
```


```python
blur_test
```




![python_pillow_23_0](https://user-images.githubusercontent.com/53929665/99906574-3f0b3c80-2d1b-11eb-81c8-f40a2b00feca.png)




```python
contour_test
```




![python_pillow_24_0](https://user-images.githubusercontent.com/53929665/99906576-3f0b3c80-2d1b-11eb-93ab-5add4ee1f053.png)


<br>

---

### References
- [예제로 배우는 파이썬 프로그래밍](http://pythonstudy.xyz/python/article/406-%ED%8C%8C%EC%9D%B4%EC%8D%AC-%EC%9D%B4%EB%AF%B8%EC%A7%80-%EC%B2%98%EB%A6%AC-Pillow)
- [ultrakain.gitbooks.io](https://ultrakain.gitbooks.io/python/content/chapter8/image-process.html)
- [이미지 출처](https://m.post.naver.com/viewer/postView.nhn?volumeNo=17116205&memberNo=11567548&vType=VERTICAL)

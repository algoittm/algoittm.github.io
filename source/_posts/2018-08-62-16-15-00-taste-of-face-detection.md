---
title: 얼굴 검출의 맛
date: 2018-08-28 16:15:00
author: doomsheart
githublink: https://github.com/doomsheart
tags: [OpenCV, Python]  
---

OpenCV는 Open Source Computer Vision Library의 약자로 이미지/영상 처리 라이브러리입니다.

언어는 C++, Java, Python등을 지원하고 있으며, 컴퓨터 비전에선 굉장히 유명한 라이브러리입니다.

이를 이용하여 파이썬으로 영상에서 얼굴을 검출하는 코드를 짜보도록 해보겠습니다.

전체적인 흐름은 다음과 같습니다.

    1. 동영상 로드
    2. 로드된 동영상에서 프레임 추출
    3. 얼굴범위 추출
    4. 범위 표시

## 1. 동영상 로드

우선 첫번째로 OpenCV를 import하고 동영상을 불러오는 코드를 짜보겠습니다.
```Python
import cv2 as cv
# 자신의 동영상 파일의 경로를 적어주면 됩니다.
VIDEO_DIR = 'drive/FaceClustering/'
VIDEO_FILE_NAME = 'PowerUp.mp4'
video_capture = cv.VideoCapture(VIDEO_DIR + VIDEO_FILE_NAME)
print(video_capture.isOpened())
```
비디오가 잘 로드되었으면 True가 프린트 됩니다.

축하드립니다! 벌써 비디오를 로드 하셨습니다.


## 2. 로드된 동영상에서 프레임 추출

두번째로 프레임을 추출해보겠습니다.

우선 프레임에대해 생소하신 분들을 위해 잠깐 설명드리면, OpenCV에서의 프레임이란 비디오의 한 장면을 의미합니다. 

따라서 그의 형식은 이미지이며 2차원 행렬로 표현됩니다.

프레임을 추출하는 부분은 재사용이 많은 부분이므로 함수를 정의하겠습니다.
```Python
def get_frame(video_capture, frame):
    video_capture.set(cv.CAP_PROP_POS_FRAMES, frame)
    ret, img = video_capture.read()
    return img, ret
```
위는 cv.VideaoCapture객체와 frame 위치를 인자로 받는 함수입니다.

우선 cv.VideaoCapture.set() 함수를 호출해서 frame을 설정하고 cv.VideaoCapture.read()로 읽습니다. 

그러면 제대로 불러왔는지의 여부를 알려주는 ret와 이미지인 img를 받습니다.

잠시 cv.VideaoCapture.read() 함수를 살펴보면 이 함수는 cv.VideaoCapture 객체의 설정된 프레임을 읽고 다음 프레임으로 cv.VideaoCapture를 설정합니다.

따라서 cv.VideaoCapture.read()를 프레임 설정없이 반복적으로 부르면 프레임 재생이됩니다.


## 3. 얼굴범위 추출

다음으로는 얼굴범위를 찾아보겠습니다.

이 부분 또한 재사용이 많은 부분이므로 함수를 정의하겠습니다.

```Python
def get_area_of_frame_face_recognition(img, face_cascade):
    grayed_img = cv.cvtColor(img, cv.COLOR_BGR2GRAY)
    face_area = face_cascade.detectMultiScale(image=grayed_img,scaleFactor=1.3,minNeighbors=5)
    return face_area
```

이 함수는 이미지를 받는 img와 Cascade Classifier인 face_cascade를 인자로 받고있습니다.

Cascade Classifier는 자신이 검출하려는 xml을 쓰면 됩니다. 여기선 얼굴을 검출하고 싶으므로 lbpcascade_frontalface_improved.xml를 썼습니다.

우선 BGR이미지를 GRAY이미지, 즉 회색이미지로 바꾸어 주고 grayed_img에 넣습니다.

그다음 cv.CascadeClassifier.detectMultiScale() 함수에 이미지와 scaleFactor와 minNeighbors를 설정해줍니다. 

scaleFactor는 각 이미지 스케일마다 이미지가 얼마나 줄어들지, minNeighbors는 각 사각형 후보(얼굴 검출 범위)를 유지해야할 이웃 사각형의 개수를 의미합니다. 

사진에서 얼굴을 검출하는 방식중 하나는, 사진보다 작은 액자 하나를 사직위에 놓고 액자를 왼쪽위에서부터 옆으로 조금씩 움직이며 액자안에 얼굴이 나왔을때 얼굴을 검출하는 방식이 있습니다. 이를 이미지 피라미드(image pyramid) 방식이라 합니다.

근데 액자를 조금씩 옮기다보면 같은 얼굴인데도 액자 범위안에 여러번 나타날때가 있습니다.(왼쪽얼굴, 정면얼굴, 오른쪽 얼굴 등등...) 따라서 얼굴 주위에 얼굴이라 생각된 액자의 위치가 있을테고, 따라서 하나의 얼굴에 여러개의 액자 위치가 나올 수 있습니다. 이를 막기위해 범위끼리 겹치는 것을 minNeighbors를 통해 조절해 주는 겁니다. 

위 함수를 실행하면 그의 반환값은 얼굴 범위의 좌표입니다.

## 4. 범위 표시

각 프레임마다 보여줘야 함으로 재사용이 많으니 이 또한 함수로 정의하겠습니다.

```Python
def show_img(img, faces):
    if len(faces) != 0:
        for (x, y, w, h) in faces:
            cv.rectangle(img, (x, y), (x + w, y + h), (255, 0, 0), 1)
    cv.imshow('FaceDetection', img)
```
위의 `get_frame(video_capture, frame)`에서 반환한 img와 `get_area_of_frame_face_recognition(img, face_cascade)`에서 반환한 faces를 인자로 받고있는 함수입니다. 

우선 얼굴이 검출되었는지를 알기위해 리스트 faces가 비었는지를 확인합니다. 얼굴이 검출이 안되었다면 faces는 비어서 길이가 0이겠죠?(데헷)

얼굴이 검출되었다면 각 범위마다 cv.rectangle()을 이용하여 사진에 얼굴영역에 네모를 그려줍니다.

그다음 cv.imshow()를 통해 결과사진을 보여줍시다.

이렇게 해서 동영상을 프레임단위로 자르고 얼굴을 검출하는 방법을 알아보았습니다. 

------

via

[Osori FaceCluster](https://github.com/HyOsori/FaceClusterer/blob/master/doomsheart/OpenCV_detection/video_face_recongition.py)
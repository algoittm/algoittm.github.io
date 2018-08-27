---
title: 얼굴 검출의 맛
date: 2018-08-26 16:15:00
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

두번째로 프레임을 추출해보겠습니다.

우선 프레임에대해 생소하신 분들을 위해 잠깐 설명드리면, OpenCV에서의 프레임이란 비디오의 한 장면을 의미합니다. 

따라서 그의 형식은 이미지이며 2차원 행렬로 표현됩니다.
<!-- 프레임을 추출하는 부분은 재사용이 많은 부분이므로 함수를 정의하겠습니다. -->
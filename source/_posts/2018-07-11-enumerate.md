---
layout: post
title: enumerate()
date: 2018-07-11
tags: 
 - study
 - python
---

파이선을 쓰다보면 종종 만나게 되는 기본 함수 중에 하나가 `enumerate()` 입니다. 그럼에도 불구하고 다른 언어에서는 자주 보이지 않아서 낯선 함수이기도 하죠.

### 코드

```python
def enumerate(sequence, start=0)
	n = start
	for elem in sequence:
		yield n, elem
		n += 1
```



`enumerate()` 함수는 `sequence`  안의 element를 받아서 각 인자의 인덱스 값과 내용을 튜플 값으로 내보내도록 되어 있는 것을 알 수 있습니다.

### 용례

튜플 형태로 값이 반환되며 보통 `for` 문과 함께 사용됩니다.

```python
for i, item in enumerate(['apple','banana','tomato'])
	print(i,item)
# 0 apple
# 1 banana
# 2 tomato
```

개인적으로는 리스트 안에서 찾는 값의 위치를 알고 싶을 때 요긴하게 썼습니다.

```python
[i for i, x in enumerate([1,2,3,4,3,2]) if x == 2] # [1, 5]
```

### 유의사항

`enumerate()`는 어찌됐든 반복문이기 때문에 입력하는 시퀀스는 [iterable](https://algoittm.github.io/2018/07/11/이터러블과-이터레이터/)이어야 합니다.
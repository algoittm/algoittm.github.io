---
layout: post
title: iterable
date: 2018-07-11
tags: 
 - study
 - python
permalink: /이터러블과-이터레이터
---

`enumerate()`를 쓰다보면 어쩔 수 없이 `iterable`과 `iterator`라는 개념을 학습해야 합니다. `iterable`이 내용물을 차례대로 반환할 수 있는 객체라면 `iterator`는 `next()`로 데이터를 순차적으로 호출할 수 있는 객체를 가리킵니다. 생긴 건 비슷하지만 둘은 완전히 별개의 대상으로 생각하는 게 차라리 속 편합니다.

### iterable

> Iterable
>
> An object capable of returning its members one at a time. Examples of iterables include all sequence types (such as list, str, and tuple) and some non-sequence types like dict and file and objects of any classes you define with an __iter__() or __getitem__() method. Iterables can be used in a for loop and in many other places where a sequence is needed (zip(), map(), ...). When an iterable object is passed as an argument to the built-in function iter(), it returns an iterator for the object. This iterator is good for one pass over the set of values. When using iterables, it is usually not necessary to call iter() or deal with iterator objects yourself. The for statement does that automatically for you, creating a temporary unnamed variable to hold the iterator for the duration of the loop. See also iterator, sequence, and generator.
>
> via [python glossary](https://docs.python.org/3/glossary.html#term-iterable)

`list`나 `str` 같은 객체는 순서대로 그 내용물을 꺼내어 쓸 수 있습니다. `range()`의 경우도 마찬가지이고 `dict` 같은 경우도 순서는 없지만 하나하나 꺼내어 쓸 수 있다는 점에서 `iterable`이라고 불립니다.`zip()`이나 `map()` 같은 경우도 마찬가집니다.

### iterator

> Iterator
>
> An object representing a stream of data. Repeated calls to the iterator’s next() method return successive items in the stream. When no more data are available a StopIteration exception is raised instead. At this point, the iterator object is exhausted and any further calls to its next() method just raise StopIteration again. Iterators are required to have an __iter__() method that returns the iterator object itself so every iterator is also iterable and may be used in most places where other iterables are accepted. One notable exception is code which attempts multiple iteration passes. A container object (such as a list) produces a fresh new iterator each time you pass it to the iter() function or use it in a for loop. Attempting this with an iterator will just return the same exhausted iterator object used in the previous iteration pass, making it appear like an empty container.
>
> via [python glossary](https://docs.python.org/3/glossary.html#term-iterator)

`next()` 메소드로 다음 항목을 불러올 수 있는 객체를 `iterator`라고 합니다. 마지막 데이터에서 다음 항목을 불러올 수 없을 경우 StopIteration exception을 발생시키며 이후 시도되는 어떠한 `next()` 메소드에 대하여도 StopIteration exception을 발생시키게 됩니다. 모든 `iterator` 객체는 `__iter__()` 함수를 내장해야 하므로 모든 `iterator` 객체는 `iterable` 합니다.

다만 `list`와 같은 컨테이너 객체는 `for` 반복문이나 `iter()` 함수에 넘길 때마다 새로운 `iterator` 객체를 발생시키므로 매번 `iterator` 객체를 새로 생성할 필요가 없습니다. 이외의 경우에는 `iterator` 객체를 새로 생성하지 않는 한 빈 컨테이너에 대해서 반복문을 실행하는 것과 같은 효과를 내게 됩니다.
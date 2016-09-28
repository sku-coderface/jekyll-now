---
layout: post
title: Quick QuickSort
author: miles3898
---

## 빠르게 작성하는 QuickSort  

어제 낮에 오랜만에 알고리즘을 한번 더 공부해야겠다 싶어서 알고리즘 책을 펴 들었다.  
대부분의 알고리즘 책들이 그렇듯이, C언어 혹은 C언어를 기반으로 하는 의사코드로 작성이 되어있다.  
물론 내가 본 책 역시 예외가 아니었기 때문에, 나는 잠들어 버렸고, 달이 차올랐다.  

오늘 GCD를 좀 건드리다가 심심해서 Quick 정렬을 다시한번 작성해볼까 하는 마음에, Swift로 코딩을 했으나, 스위프트의 특성상 여간 귀찮은 점이 한 둘이 아니었다.  

첫 째, C-Style For Loop문이 없다.  
둘 째, 함수의 Argument로 전달 된 Parameter는 불변값이다.  
셋 째, 리스트는 Value 타입이다.  
즉, 두 번째 문제점이 있더라도, 리스트가 Reference 타입이면 실제 데이터를 변경하는 것은 문제가 되지 않지만 Value 타입은 영향을 많이 받게 된다.  

> **C-Style For Loop란?**  
> for(int idx = 0; idx < 99; i++){ ... } 형태의 for 문을 말한다.  
> 파이썬과 스위프트3에서는 이러한 C-Style For Loop를 지원하지 않는다.  

그러던 중, 현명한 사람들은 어떻게 작성했을까 하는 마음에 스위프트로 '잘' 작성된 QuickSort 알고리즘을 봤더니...

![QuickSort.swift](https://cloud.githubusercontent.com/assets/17308570/18813127/ad697b76-8327-11e6-8f62-5cc132d1b4c9.png)  

엄청 간단하게 작성된 것이었다.  
[마이클페더스의 글](http://bit.ly/1tm4xQI)에서 함수형 프로그래밍에 대해 설명되어 있다.

> 객체지향 프로그래밍은 움직이는 부분을 캡슐화하여 코드 이해를 돕고, 함수형 프로그래밍은 움직이는 부분을 최소화하여 이해를 돕는다.  

스위프트로 작성된 코드를 이해하기 어려울 수 있으니, 아래에 파이썬과 자바스크립트로 작성한 코드를 첨부한다.  


## python3

```python
#QuickSort.py

def quicksort(array):
  if not isinstance(array, list):
    return None
    
  if len(array) <= 1:
    return array
   
  mid = len(array)//2
  pivot = array[mid]
  less = list(filter(lambda x: x < pivot, array))
  equal = list(filter(lambda x: x == pivot, array))
  greater = list(filter(lambda x: x > pivot, array))
   
  return quicksort(less) + equal + quicksort(greater)
```

```python
#sortMe.py

from QuickSort import quicksort

arr = [9, 9, 8, 8, 7, 7, 6, 6, 5, 5, 4, 4, 3, 3, 2, 2, 1, 1, 0, 0]
sorted = quicksort(arr)
print(sorted)
#실행결과: 0, 0, 1, 1, 2, 2, 3, 3, 4, 4, 5, 5, 6, 6, 7, 7, 8, 8, 9, 9
```

## JavaScript (ES6, *)

```javascript

const quicksort = function(array){

	if (!(array instanceof Array)) {
		return
	}

	if (array.length <= 1){
		return array
	}

	const mid = parseInt(array.length/2)
	const pivot = array[mid]
	const less = array.filter(x => x < pivot)
	const equal = array.filter(x => x == pivot)
	const greater = array.filter(x => x > pivot)

	return quicksort(less).concat(equal).concat(quicksort(greater))
}

var arr = [0, 0, 1, 1, 2, 2, 3, 3, 4, 4, 5, 5, 6, 6, 7, 7, 8, 8, 9, 9]
var res = quicksort(arr)

console.log(res)

//실행결과: 0, 0, 1, 1, 2, 2, 3, 3, 4, 4, 5, 5, 6, 6, 7, 7, 8, 8, 9, 9
```

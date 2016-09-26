---
layout: post
title: Beautiful MergeSort
author: miles3898
---

###알흠다운 병합정렬

퀵 정렬에 이어서 병합정렬도 예쁘게 짤 수 있지 않을까 하는 생각에 시도해봤다.  
물론 지금 생각해보니 오히려 C-Style For Loop를 쓰지 않는 것이 더 **안정적**이고, 가독성 면에서도 뛰어나다.**  

병합정렬의 기본적인 원리를 모르는 사람은 없을 테니, 바로 코드를 보이는 것이 좋을 것 같다.  

###Python3

```python

def merge(lhs, rhs):

	merged = []
	while len(lhs) > 0 and len(rhs) > 0:
		merged.append(lhs.pop(0) if lhs[0] < rhs[0] else rhs.pop(0))
	for i in lhs:
		merged.append(i)
	for i in rhs:
		merged.append(i)

	return merged

def mergeSort(array):
	
	if not isinstance(array, list):
		return None
	
	if len(array) <= 1:
		return array
	
	mid = len(array) // 2
	print("lhs: ", 0, ":", mid, ", ", mid, ":", len(array))
	lhs = mergeSort(array[0:mid])
	rhs = mergeSort(array[mid:])

	return merge(lhs, rhs)

```

퀵 정렬은 사실 엄밀히 따지자면 정복 후 분할 방식에 가깝다.  
반면 병합정렬은 분할정복 원칙을 아주 정확히 지키고 있다.  
특히 병합정렬의 꽃은 merge 함수가 아니겠는가!  

우선 mergeSort함수에서는 매개변수가 리스트인지를 체크한다. 만약, 리스트가 아닌 경우라면, 정렬에 실패한다.  
(그야 당연히, 튜플은 불변값, 셋, 맵은 애초에 정렬 따위가 없기 때문이다.)  

그리고 리스트의 길이가 1이하인 경우, 리스트를 그대로 반환한다.  
이는 이 재귀 함수의 베이스케이스에 해당한다.  

그리고 나머지 부분은 리스트를 반으로 쪼개서 merge 함수에 넣는 역할을 한다.

그렇다면 merge 함수에서의 역할은?  
merge 함수에서는 두 개의 리스트 중 하나라도 빌 때까지 리스트의 첫 번째 값이 작은 것을 뽑아내 merged 리스트에 추가한다.  
(오토바이를 타고 서로를 달려오는 치킨 게임과 비슷하다. 둘 중에 하나가 죽으면(혹은 달아나면) 끝이 난다.)  

```python
  merged.append(lhs.pop(0) if lhs[0] < rhs[0] else rhs.pop(0))
```

이 함수 구문은 if 컴프리헨션이라고 한다. lhs[0]가 rhs[0]보다 작으면, lhs.pop(0)을, 반대의 경우 rhs.pop(0)을 실행한다.  

그리고 나머지 둘 중에 어떤 리스트가 비었는지 알 길이 없으니 둘 다 for loop를 돌려 merged 리스트에 다 때려 박는다.  

동일한 동작에 대한 스위프트 코드도 첨부한다.  
(자바스크립트는 나중에 추가하거나 할 예정...)  

```swift
private func merge<T>(lhs: [T], rhs: [T]) -> [T] where T: Comparable{
    
    var merged = [T]()
    var left = lhs
    var right = rhs
    
    while !left.isEmpty && !right.isEmpty{
        
        merged.append(left.first! < right.first! ? left.removeFirst() : right.removeFirst())
    }
    
    left.forEach{ merged.append($0) }
    right.forEach{ merged.append($0) }
    
    return merged
}

public func mergeSort<T>(array: [T]) -> [T] where T: Comparable {
    
    guard array.count > 1 else {
        return array
    }
    
    let mid = array.count / 2
    let lhs = mergeSort(array: [T](array[0..<mid]))
    let rhs = mergeSort(array: [T](array[mid..<array.count]))
    
    return merge(lhs: lhs, rhs: rhs)
}
```

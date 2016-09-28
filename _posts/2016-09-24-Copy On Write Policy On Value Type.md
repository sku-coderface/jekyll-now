---
layout: post
title: Copy On Write Policy On Value Type
author: miles3898
---

## Value 타입을 Value 타입이라 불러야 하나, Reference 타입이라 불러야 하나...

자바를 써온 나에게 C#을 쓸 때, 구조체를 써야할지, 클래스를 써야할지는 그다지 고민거리는 아니었다.  
왜냐? 자바에서는 모든 게 클래스기 때문에, 모든 걸 클래스로 만들었으니, C#에서도 예외는 없었다.  

하지만 스위프트에서는 큰 문제가 생겼는데, 자바도, C#도 웬만한 것들은 클래스로 만들어져 있다.  
(C#은 구조체를 그냥 만드는지, new 연산자로 만드는지에 따라 동작이 달라지지만 여기서 신경 쓸 부분은 아니다.)  
바로 스위프트의 기본 자료형들이 구조체로 만들어져 있다는 것이다.

Int, Double은 물론이고, 배열, 열거형까지 모든 것이 구조체다.  
자바: 모든 것이 클래스다!
스위프트: 모든 것이 객체다! 클래스와 구조체를 스까 놨다! 참고로 기본 자료형은 구조체다!  

C언어를 접해본 사람들이라면 구조체를 Value 타입으로 넘기는 것이 나중에 성능 저하를 불러올 수 있다는 것을 알고 있을 것이다.  
(물론, 클래스를 복사하는 건 더 느리겠지만...)

애플은 왜 이런 선택을 했을까?  
답을 찾은 후에는 애플이 이렇게 말하는 건 아닌가 싶었다.  
애플: "모르면서 나불거리지 말고 써보기나 해"  

그래서 오늘은 개발자를 위해 무엇을 준비했는지 보기로 한다.

```swift

var a = [1, 2, 3, 4, 5]
var b = a
var c = b

var addrA = UnsafeMutablePointer(mutating: a)
var addrB = UnsafeMutablePointer(mutating: b)
var addrC = UnsafeMutablePointer(mutating: c)

```

일단 위 코드는 그다지 어려운 코드는 아니다. a라는 배열(구조체로 구현 됨)을 만들고, b와 c에 각각 복사한다.

그리고 addrA, addrB, addrC 변수에 각각의 구조체를 포인터 형태로 할당한다.  
즉, addrA, addrB, addrC는 포인터 변수다.

그럼 이 주소들을 출력해보자.  

```swift

print(addrA)
print(addrB)
print(addrC)

```
> **[실행결과]**  
> 0x0000000100b03d80  
> 0x0000000100b03d80  
> 0x0000000100b03d80  

?????

모든 구조체의 주소가 같다?

사실 스위프트에서 Value 타입 변수는 전달될 때, 참조가 전달된다.  
그렇다면 "C++처럼 클래스와 구조체의 차이가 없는 것 아닌가?" 하는 의문이 들기 마련이다.(아님 말고)  

그래서 변수 a의 값을 변경해봤다.
a가 변경되면 b, c도 변경되겠지

```swift

a.append(4)

print(a)
print(b)
print(c)

```

> **[실행결과]**  
> [1, 2, 3, 4]  
> [1, 2, 3]  
> [1, 2, 3]  

~~미쳤어요~~

우리가 의도하던 게 아닌 것 같다.(아닌가?)  

그렇다면 이제 a, b, c의 주소는 어떻게 되는 걸까?

```swift

addrA = UnsafeMutablePointer(mutating: a)
addrB = UnsafeMutablePointer(mutating: b)
addrC = UnsafeMutablePointer(mutating: c)

print("A의 값이 바뀐 후")
print(addrA)
print(addrB)
print(addrC)

```

> **[실행결과]**  
> A의 값이 바뀐 후  
> 0x0000000100b04a70  
> 0x0000000100b03d80  
> 0x0000000100b03d80  

addrA의 주소가 변했다.  

애플의 공식 의견: "Value 타입은 당장 복사되지 않는다. 단, 그 값의 변경이 있을 때만 변경된다."  

그렇다면 이건 어떨까? 함수의 매개변수로 넘기거나 함수의 반환값으로 받는 경우는 복사되겠지?  

```swift

func passMe(arr: [Int])->[Int]{
    let addr = UnsafeMutablePointer(mutating: arr)
    print(addr)
    
    return arr
}

print("B의 주소")
print(addrB)
print("함수 내부에서 B의 주소")
let retAddrB = passMe(arr: b)
print("반환된 B의 주소")
print(UnsafeMutablePointer(mutating: retAddrB))

```
> **[실행결과]**  
> B의 주소  
> 0x0000000100b03d80  
> 함수 내부에서 B의 주소  
> 0x0000000100b03d80  
> 반환된 B의 주소  
> 0x0000000100b03d80  

결국 변하지 않았다...  

결론: 값 타입도 우선 참조된다.  
그리고 값 타입의 변경이 있을 경우에만 해당 변수가 참조하는 메모리 주소가 바뀐다.  

\* 참고로 배열은 immutable속성이다.

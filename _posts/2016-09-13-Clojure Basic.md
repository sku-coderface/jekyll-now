###기본 자료형

클로저의 기본 자료형은 아래 7가지로 나뉜다.  

1. 정수(java.lang.Long)
2. 실수(java.lang.Double)
3. 문자(java.lang.Charater)
4. 문자열(java.lang.String)
5. 진리값(java.lang.Boolean)
6. **키워드(clojure.lang.Keyword)**
7. **분수(clojure.lang.Ratio)**

키워드 및 분수를 제외한 나머지 자료형은 **자바 자료형**임에 주의한다.  
(왜냐면, 클로저는 JVM 위에서 동작하기 때문이다. 물론, CLR에서도 동작한다. 단, CLR에서의 자료형은 확인이 필요하다.)  

---

###연산

클로저는 리스프 언어 문법을 JVM으로 이식한 언어다. 즉, 리스프 문법을 이용하되, JVM의 자원을 그대로 이용할 수 있다.

그러나, 리스프의 문법을 그대로 옮겨왔다는 점에 주목할 필요가 있다.

리스프에서는 (함수 {매개변수}) 형식으로 함수를 호출한다.

연산자도 함수에 속하기 때문에 아래와 같이 호출한다.

```clojure
;; 사칙연산
(+ 1 5) ;; 6
(- 8 3) ;; 5
(* 4 7) ;; 28
(/ 6 4) ;; 3/2 clojure.lang.Ratio: 정수끼리의 연산은 분수로 나타낸다.
(/ 6.0 4) ;;  1.5 java.lang.Double: 실수와의 연산은 실수로 나타낸다.

;; modular 연산
(mod 7 3) ;; 1
```

문자는 C, C++, C#, Java에서 처럼 작은 따옴표로 나타내지 않는다.
문자는 백 슬래시(\) 기호를 통해 나타낸다.

---

###기타 자료형

```clojure
\a  ;; java.lang.Character: 'a'
```

진리값은 true, false로 나타낸다.

```clojure
(if true
  (println "It's true") ;;  "It's true"
  (println "It's false")
)
```

키워드는 콜론(:)을 prefix로하여 나타낸다.

```clojure
:as
:require
```

키워드의 사용 방법에 대해서는 뒤에서 계속 다루기로 한다.

---

###데이터 구조

클로저에서 제공하는 기본 데이터 구조에는 **리스트**, **벡터**, **맵**, **셋**이 있다.

**리스트**

리스트를 리터럴로 표현할 때는 작은 따옴표(**'**)와 괄호(**()**)를 이용한다.

```clojure
'(1, 3, 5, 7, 9)  ;; (1 3 5 7 9)
```

> 클로저에서 쉼표(**,**)는 공백처럼 취급된다. 따라서 가독성만 높일 뿐, 문법적인 의미는 없다.
```clojure
'(1 3 5 7 9) ;; '(1 3 5 7 9)
```

또는 리스트 함수로 생성한다.


```clojure
(list 1 2 3 4 5) ;; (1 2 3 4 5)
```

**벡터**

벡터를 리터럴로 표현할 때는 [ ] -브라켓(brackets)-을 이용한다.

```clojure
[1 3 5 7 9] ;; [1 3 5 7 9]
```

또는 벡터 함수로 생성한다.

```clojure
(vector 1 3 5 7 9)  ;; [1 3 5 7 9]
```

\> 리스트와 벡터에 관하여... <
리스트와 벡터는 서로 유사점이 많다. 일례로, 두 데이터 구조 모두 cons 함수를 호출할 수 있다.

```clojure
(cons 1 '()) ;; (1)
;; (cons 1 nil)
(cons 1 []) ;; [1]

(cons 1 (cons 2 '()))
(cons 1 (cons 2 []))
```

그리고 둘 다, 어떤 자료형이든 담을 수 있다.

```clojure
'(1 2.0 \a "Hello" true :key [1 2.0 \ㅁ "World!" false :value])
[1 2.0 \a "Hello" false :value], '(1 2.0 \a "Hello" true :key)]
```

그러나, 리스트는 연결 리스트(Linked List), 벡터는 배열(Array)와 유사하다. 따라서 둘의 동작 방식에 차이가 있다.

```clojure
(nth '(1 2 3 4 5) 2) ;; 3
;; 앞에서부터 0, 1, 2 순으로 찾아 두 번째 요소를 반환한다.
(nth [1 2 3 4 5] 2) ;; 3
;; 배열처럼 곧 바로 인덱스로 값을 참조한다.

(conj '(1 3 5 7) 9) ;; (9 1 3 5 7)
;;리스트에 conj 함수를 사용할 경우, 가장 앞에 추가된다.
(conj [1 3 5 7] 9) ;; [1 3 5 7 9]
;; 벡터에 conj 함수를 사용할 경우, 가장 뒤에 추가된다.
```

---

**맵**

맵 리터럴은 { } -브레이스(braces)- 를 이용해 나타낸다.

```clojure
{:a 1, :b 2, :c 3, :d 4, :e 5}
```

앞에서도 설명햇듯이, 쉼표는 공백처럼 취급된다. 따라서 위 리터럴은 아래처럼 평가된다.

```
{:a 1 :b 2 :c 3 :d 4 :e 5}
```

가독성을 위해서 쉼표를 넣는 것도 좋다.

맵에서 특정한 값을 얻기 위해서는 get 함수를 사용한다.

```clojure
(get {:a 1, :b 2, :c 3} :b} ;; 2
```

만약 맵의 키로 키워드(clojure.lang.Keyword)를 사용하면 맵을 조금 더 쉽게 다룰 수 있다.

```clojure
({:a 1, :b 2, :c 3} :c) ;; 3
(:c {:a 1, :b 2, :c 3}) ;; 3
```

맵의 값을 추출할 때 주의할 점으로, 맵에 존재하지 않는 키를 요철할 때 nil이 반환된다는 점이다.

```clojure
({:a 1, :b 2, :c 3} :d) ;; nil
```

맵에 키가 없을 경우 디폴트 값을 반환하도록 할수 있다.

```clojure
({:a 1, :b 2, :c 3} :d 0) ;; 0
```

맵에 키를 추가 혹은 갱신 하기 위해서는 assoc, 제거하기 위해서는 dissoc 함수를 사용한다.

```clojure
(assoc {:a 1, :b 2, :c 3} :d 4) ;; {:a 1, :b 2, :c 3, :d 4}
(dissoc {:a 1, :b 2, :c 3, :d 4} :a :b) ;; {:c 3, :d 4}
```

두 개 이상의 맵을 합칠 때는 merge 함수를 사용한다.

```clojure
(merge {:a 1, :b 2, :c 3} {:d 4, :e 5}) ;; {:a 1, :b 2, :c 3, :d 4, :e 5}
```

keys, vals 함수를 이용하여 맵의 키 혹은 값 리스트를 반환받을 수도 있다.

```clojure
(keys {:a 1, :b 2, :c 3}) ;; (:a :b :c)
(vals {:a 1, :b 2, :c 3}) ;; (1 2 3)
```

---

**셋**

셋 리터럴은 샾(**#**)과 브레이스(** { } **)로 나타낸다.

```clojure
#{1 2 3 \A \B \C :A :B :C} ;; #{1 2 3 \A \B \C :A :B :C}
```

셋 리터럴을 나타낼 때, 중복된 요소가 있으면 에러가 발생한다.

```clojure
#{:A :B :C :A} ;; IllegalArgumentException Duplicate key: :A  clojure.lang.PersistentHashSet.createWithCheck (PersistentHashSet.java:68)
```

셋은 기본적인 집합연산(union, intersection, difference)을 제공한다.

```clojure
(clojure.set/union #{:a :b :c} #{:c :d :e}) ;; #{:e :c :b :d :a}
(clojure.set/difference #{:a :b :c} #{:c : d :e}) ;; #{:b :a}
(clojure.set/intersection #{:a :b :c} #{:c : d :e}) ;; #{:c}
```

---

\> 맵과 셋에 대해여... <

맵과 셋은 서로 유사한 점이 많다.
셋은 중복되지 않는 요소를 담는다.
맵은 요소와 관련된 값을 값는 셋이다.

맵과 셋에 값을 추가하는 방법으로 리스트, 벡터와 같이 conj 함수를 제공한다.

```clojure
(conj {:a 1, :b 2, :c 3} [:d 4]) ;; {:a 1, :b 2, :c 3, :d 4}
(conj #{:a :b :c} :d) ;; #{:a :b :c :d}
```

맵에 새로운 키를 추가할 때는 벡터로 키-값 쌍을 감싸야한다는 점에 주의한다. 만약 두 개 이상의 쌍을 추가할 때는 각각의 벡터를 입력한다.

```clojure
(conj {:a 1, :b 2, :c 3} [:d 4] [:e 5]) ;; {:a 1, :b 2, :c 3, :d 4, :e 5}
```

벡터, 셋, 맵은 요소가 포함되어 있는지를 검사하기 위해 contains? 함수를 제공한다.

```clojure
(contains? {:a 1, :b 2, :c 3} :a) ;; true
(contains? #{:a :b :c} :d} ;; false
(contains? [1 2 3 4 5] 4) ;; true
```

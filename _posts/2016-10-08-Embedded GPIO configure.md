---
layout: post
title: Embedded GPIO configure 파일 사용하기
author: miles3898
---

이번 학기에는 라즈베리 파이를 활용한 IoT 강의를 듣고 있다.<br />
지난 학기에 들었던 자학과 임베디드 프로그래밍때는 모든 GPIO가 목적에 맞게 설계가 되어 있었지만,<br />
이번 학기에 듣는 임베디드 수업엔 GPIO를 일일이 설정을 해주어야 한다.<br />

현재 주어진 재료는 라즈베리 파이 2 B+모델과, 브레드 보드, LED, 버튼, 부저, 초음파 센서<br />

브레드 보드가 주어졌다는 것은 결국 회로 설계 또한 직접 해야 한다는 뜻이다.<br />
GPIO의 경우, 설정을 코드 내에 집어넣었다가는 회로 또한 `코드에 맞춰서` 설계되어야 한다는 뜻이다.<br />
그래서 나는 여타 개발 환경에서 설정 파일을 만들듯이, 나 또한 설정파일을 만들기로 했다.<br />

지금까지 만들어진 프로젝트 구조는 이렇다.<br />

```
*Project
|config
|init.h
|init.c
|main.c
*modules
- |modules.h
- |modules.c
- *fnd
- - |fnd.h
- - |fnd.c

```

\*init은 GPIO 설정을 담당한다.<br />
\*module은 하위 모듈의 초기화를 담당한다.<br />
\*fnd와 같은 하위 모듈들은 자기 자신이 할당받은 Pin 번호로 GPIO를 초기화한다.<br />

여기서 중점적으로 볼 부분은 \*init이다.<br />

init에서는 initGPIO() 함수가 있는데, 동작은 이렇다.<br />

```
config 파일에서 라인을 한 줄씩 읽는다.
읽어온 라인에서 핀 번호와 입출력 여부를 판단한다.
```

이 동작을 어떻게 구현할 수 있을까?

답은 간단하다. `파일을 열고, 읽으면 된다.`<br />

우선 config 파일의 형식을 보여주자면 이렇다.

```
0 output; D3
1 output; D2
2 output; D1
3 output; D0
4 output;
5 input;  button
6 output;
7 output;
8 output;
9 output;
10 output;
11 output;
12 output;
13 output;
14 output;
15 output;
16 output;
17 output;
18 output;
19 output;
20 output; A
21 output; B
22 output; C
23 output; D
24 output; E
25 output; F
26 output; G
27 output; dot
28 output;
29 output;
30 output;
31 output;
```
위와 같이 32개의 핀을 초기화 하는데, 5번 핀을 제외한 나머지 핀들을 output으로 지정한다.<br />
이때, `;`뒤의 내용은 주석으로 처리되어야 한다.<br />

위 내용을 읽어서 GPIO 설정을 변경하는 함수를 작성해보자.<br />

\*여기서 GPIO 입출력을 설정하는 함수는 `pinMode(int pin, int inout)`으로 주어진다.<br />
첫 번째 매개변수는 핀 번호를, 두 번째 매개변수는 `OUTPUT`, `INPUT` 상수 값 중 하나다.<br />

```C
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

void initGPIO(){

  int pinNumber;
  char inout[79];

  FILE* file = ("config", "r"); //config 파일을 읽기모드로 연다.

  if (file == NULL) {
    fprintf(stderr, "Can not find config file.");
    exit(-1);
  }

  while(fscanf(file, "%d %31[^;]%*[^\n]", &pinNumber, inout)) == 2 {
    if ( strcmp(inout, "input") == 0 ) {
      pinMode(pinNumber, INPUT);
    }else {
      pinMode(pinNumber, OUTPUT);
    }
  }
  fclose(file);
}

```
fscanf를 while 루프 안에 넣었는데, scanf 계열의 함수는 읽은 변수의 개수를 반환한다.<br />
이 예제에서 pinNumber와 inout에 읽어 넣기 때문에, 2를 반환할 것이다.<br />

그보다, 뭔가 이해가 잘 되지 않는 부분이 있을 것이다.<br />
fscanf의 두 번째 매개변수인 문자열 포맷에서 `"%d %31[^;]%*[^\n]"`은 도대체 무엇이란 말인가.<br />
이 부분은 정규표현식에 해당한다.<br />

%d와 %31[^;]사이에는 공백이 있다. 즉, config 파일에서 0 output; D3처럼 0과 output 사이의 공백이 항상 들어있다는 의미다.<br />
여튼, %d로 읽은 숫자는 inout 변수에 저장될 것이다.

%31[^;]에 대해 설명하기 전에 %4d는 무엇인지 다시 생각해보자. 4자리 수 정수라는 것이 기억이 나는가?<br />
%31[^;]도 비슷하다. 무언가 31자리를 읽는다는 뜻이다. 단, 정말 31자리를 다 읽는 것은 아니고, [^;]에 의해서 그 갯수가 한정된다. [^;]의 의미는 정규표현식에서 `;`를 제외한 글자라는 의미다.<br />
주의할 점이 있는데, `;`만 쏙 뺀 31자가 아니라, `;`가 나오기까지 31자를 읽는다. 따라서 %31[^;]는 output이라는 글자 6자만 읽어서 inout 배열에 저장할 것이다.

마지막으로 %*[^\n]는 무엇일까? [^\n]의 의미는 이제 명확하다. 줄바꿈 문자를 제외한다는 뜻이다.<br />
그리고 *은 `모든 문자(혹은 숫자)`를 다 읽는다는 의미다.<br />
즉, %*[^\n]의 의미는 줄바꿈 문자가 나올 때까지 모든 문자를 다 읽는다는 의미로 해석된다.<br />
단, *로 읽은 문자들은 변수에 저장될 수 없고, 버려지게 된다.(주석으로 처리해버린다.)<br />

---

GPIO와 같이 설정이 빈번하게 일어나는 경우, 설정 데이터를 코드에 첨부하기 보다는, 파일에 저장해서 동적으로 읽는 것이 프로그램을 조금 더 유연하게 만들 수 있다.<br />

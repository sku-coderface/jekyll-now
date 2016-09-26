---
layout: post
title: Clang block Syntax
author: miles3898
---

### 스크래치도 울고 갈 블럭 코딩

macOS를 써 본 사람이라면 살다가 한 번 이상 llvm이라는 단어를 들어보기 마련이다.  
llvm이 무엇인지를 이야기 하기 전에 컴파일러에 대한 짧은 지식이 필요한데, 간략히 설명하면 이렇다.  

컴파일러는 크게 전단부와 후단부로 나눌 수 있다. 이를 각각 프론트엔드(front-end)와 백엔드(back-end)라고 한다.  

프론트엔드에서는 어휘분석 - 구문분석 - 중간언어 생성 등 언어와 관련된 작업을,  
백엔드에서는 코드 구조 최적화, 기계어 변환 등의 기계 종속적인 작업을 한다.  

시판되고 있는 CPU 아키텍쳐의 종류가 n가지가 된다면 1개의 언어를 만들면, n개의 백엔드가 필요하며,  
m개의 언어를 만들면 m*n개의 백엔드가 필요한 셈이다.  

llvm은 백엔드를 대신하는 역할을한다.  
즉, JVM이나 .NET과 같은 VM의 역할을 하는데, 자바는 byte code로, .NET은 msil로 컴파일 하듯, llvm은 bit code로 컴파일 된다.  
byte code, msil, bit code와 같은 중간 산물을 중간코드라고 한다.  

애플은 llvm이나 clang과 같은 오픈소스 프로젝트에 가장 많이 기여한 기업으로 유명하다.  
덕분에 애플은 자신의 플랫폼인 Darwin에 llvm과 clang을 기본으로 탑재하고 있으며, C, ObjC, Swift에 수많은 변화를 불러 왔다.  

가장 유명한 것이 메시지 기반 호출인 동적 디스패치와 같은 것이다.

메시지 기반 호출에 대해 간략하게 소개하자면 이렇다.

```objc
#import <Foundation/Foundation.h>

int main(int argc, const char * argv[]) {
    @autoreleasepool {
        // insert code here...
        NSString* str = NULL;
        NSString* capitalized = [str capitalizedString];  //NULL에 capitializedString 메시지를 보낸다.
        NSLog(@"%@", capitalized);  // 출력 결과: null
        
    }
    return 0;
}
```

자바나 C++, C#에서 null 메시지 기반 호출에서는 NULL 포인터에 메서드를 호출하면 NullPointerException이 발상하기 마련이다.  
메시지 기반 호출에서는 객체에 메시지를 전달한다.  

그리고 어떤 부모 클래스를 상속하는 자식 클래스가 부모 클래스의 메서드를 오버라이드 하는 경우, 동적 바인딩은 llvm의 런타임에 의해 자식 클래스에게 메서드가 전달된다.  
이를 동적 디스패치라 한다.  

리눅스에서도 이러한 동적 디스패치가 적용되는 가장 기본적인 단위인 블럭 코딩이 가능하다.  

블럭 코딩은 파이썬의 람다 함수와 같이 함수를 리터럴로 작성하는 것을 말하는데, GCC나 VC++등에서 C언어를 접한 사람이라면 아마 놀랄 것이다.  

```C
#include<stdio.h>
#include<stdlib.h>

void repeat(void (^fn)(char*), char* arg, int count) {
  for(int i=0; i<count; i++) {
    fn(arg);
  }
}

int main(int argc, char* argv[]){
  void (^greeting)(char*) = ^(char* name) {
    printf("Hello, %s\n", name);
  };
  
  repeat(greeting, "miles3898", 5);
  
  //실행결과:
  //Hello, miles3898
  //Hello, miles3898
  //Hello, miles3898
  //Hello, miles3898
  //Hello, miles3898
}
```

이러한 코딩을 가능하게 해주는 것이 바로, 블럭 코딩이다.  

데비안 계열 리눅스에서 이 기능을 사용하기 위해서는 아래 세 가지를 설치해야 한다.

1. llvm  
2. clang
3. libBlocksRuntime

위 세가지를 설치하기 위해 다음 명령을 입력한다.  

> sudo apt-get install llvm  
> sudo apt-get install clang  
> sudo apt-get install libBlocksRuntime-dev  

설치가 완료되면, 위 코드를 작성하고 main.c 파일에 저장한다.  

다음으로 makefile을 작성할 차례다.  

```make
all: main.c  
  clang main.c -o main -fblocks -lBlockRuntime  
```

그리고 ./main을 입력하여 실행해보자!  

아마 아무런 이상 없이 잘 실행될 것이다.

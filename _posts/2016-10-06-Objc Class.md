---
layout: post
author: miles3898
title: Objc Class
---

스위프트가 Obj-C를 대신할까?<br />
내 생각엔 아니라고 본다.<br />

왜냐면, ObjC는 객체지향 프로그래밍 언어, 그리고 스위프트는 프로토콜 지향 언어다.<br />
사실 위 말이 완전히 맞는 건 아니고, ObjC와 Swift의 개념이 다르다.<br />

지난 번에 한번 서술한 듯이, ObjC는 `메시지 기반 호출` 방식을 사용한다.<br />
따라서, 메시지를 받는 수신자가 없으면, 그냥 무시된다.<br />

```ObjC

DummyClass* dummy = nil;
[dummy doSomething];

```

위 코드는 `아무 일도 일어나지 않는다.`<br />

그리고, ObjC에서 웬만한 모든 것들은 힙에서 관리된다.<br />
(사실 `모든 것은 객체다.` 라고 설명하려고 했지만, 스위프트에서도 모든 것이 객체(object)기 때문에 고쳤다.)<br />

여튼... 중요한 부분은 이것 보다도, ObjC의 클래스에 관한 것이다.<br />

ObjC의 클래스 작성은 뭔가가 쪼~~끔 이상하다.<br />

간단한 클래스 Person을 정의해보자.

`Person.h`

```ObjC

#import<Foundation/Foundation.h>

@interface Person: NSObject
{
  NSString* name;
  int age;
}

//Setter
-(void) setName:(NSString*)newName;
-(void) setAge:(int)nweAge;

//Getter
-(NSString*) name;
-(int) age;

//method
-(void) introduce;

@end
```

`Person.m`

```ObjC

#import "Person.h"

@implementation Person

-(void)setName:(NSString*) newName
{
  name = newName;
}

-(void)setAge:(int) newAge
{
  age = newAge;
}

-(NSString*) name
{
  return name;
}

-(int) age
{
  return age
}

-(void) introduce
{
  NSLog(@"My name is %@, I'm %d years old", name, age);
}

@end

```

뭔가 상당히 길다.<br />
이 코드를 줄여보자.<br />
`@property`와 `@synthesize`를 이용하면 코드를 확 줄일 수 있다.

`Person.h`

```ObjC

#import<Foundation/Foundation.h>

@interface Person: NSObject
{
  NSString* name;
  int age;
}

@property NSString* name;
@property int age;

-(void) introduce;

@end
```

`Person.m`

```ObjC

@implementation Person

@synthesize name;
@synthesize age;

-(void)introduce
{
  NSLog(@"My name is %@, I'm %d years old", name, age);
}

@end

```

많이 간략해졌다.<br />

여기서 주목할 점은 `@property`와 `@syntehsize` 키워드다.
`@property`는 액세서(getter, setter)를 정의한다.<br />
`@property` 키워드를 사용하지 않는 경우, `name`멤버에 접근하기 위해 `-(void)setName:(NSString*)name` 메서드와 `-(NSString*)name`를 정의해야 한다.<br />
그러나 `@property` 키워드를 사용하면 두 액세서를 한 번에 **선언**할 수 있다.<br />

그리고 `@synthesize` 키워드는 앞서 선언한 `@property` 키워드의 메서드를 **구현**한다.<br />

부족한 내용은 추가 예정...

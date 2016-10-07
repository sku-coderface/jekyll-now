---
layout: post
title: ObjC & Swift 스까쓰기
author: miles3898
---

애플이 Swift를 발표하면서 ObjC를 지속적으로 지원한다고 발표했다.<br />
그 사실을 입증하기라도 하는듯, Objective-C와 Swift 코드를 통합할 수 있는 방법을 제공한다.<br />

##Swift에서 ObjC 사용하기

간단히 예제 프로젝트로 이해하는 것이 좋을 것이라 생각한다.<br />

1. [macOS] - [Command Line Tool]을 선택한다.<br />
2. [Product Name]란에 `Swift-C`를 기입한다.<br />
3. [Language]는 Swift를 선택한다.<br />

프로젝트를 처음 생성하면 **네비게이터 영역**이 아래와 같이 구성된다.<br />
![navigator](https://cloud.githubusercontent.com/assets/17308570/19186160/df19d87c-8cbf-11e6-898f-0da7b8b321e0.png)

이 예제에서는 간단히 Person이라는 클래스를 만들어 볼 것이다.<br />
우선, 프로젝트에 ObjC 파일을 추가하기 위해 `⌘+N` 키를 눌러, 새로운 소스파일을 추가한다.<br />

**템플릿 선택 시트**가 나타나면, [Cocoa Class]를 선택한다.<br />
다음으로 [Class Name]란에 Person을 기입하고, [Subclass of]에서는 NSObject를 선택한다.<br />
\*주의: [Language]는 Objective-C를 선택한다.

파일을 생성하면 작은 시트가 나타난다.<br />
> Would you like to configure an Objective-C bridging header?

**반드시 Create Bridging Header를 선택해야한다.**

> **만약 실수로 Bridging header를 생성하지 않았다면?**<br />
> `⌘+N`을 눌러, 헤더파일(.h)을 하나 추가한다.<br />
> 헤더파일 명칭은 `Swift-C-Bridging-Header.h`로 지정한다.<br />
> 프로젝트 설정의 [Build Settings]에서 [Swift Compiler - General]을 찾는다.<br />
> Objective-C Bridging Header에 `Swift-C-Bridging-Header`를 기입한다.<br />

다음으로, Objective-C 소스파일에 Person 클래스를 선언할 차례다.<br />

`Person.h`파일에 아래와 같이 작성한다.

```ObjC
#import <Foundation/Foundation.h>

@interface Person: NSObject
{
  NSString* name;
  NSUInteger age;
}

@property NSString* name;
@property NSUInteger age;

- (void) intro;

@end
```

다음으로, `Person.m`파일을 작성한다.

```ObjC
#import "Person.h"

@implementation Person

@synthesize name;
@synthesize age;

- (void) intro
{
  NSLog(@"My name is %@, and I'm %lu years old.", name, age);
}

@end
```

이렇게, Person 클래스는 다 만들어졌으니, Person 클래스를 Swift에 추가할 차례다.<br />

`Swift-C-Bridging-Header.h` 파일을 열고 다음 한 줄을 기입하자.<br />

```ObjC
#import "Person.h"
```

이제, Swift에서 Person 클래스를 사용할 준비를 마쳤다.<br />
main.swift를 열고 아래 코드를 작성해보자.<br />

```Swift

Person miles = Person()
mlies.name = "Miles"
miles.age = "24"

miles.intro()

```

> **실행 결과**<br />
> My name is Miles, and I'm 24 years old.<br />

지금까지의 과정을 잘 따라 했다면, 아무 이상 없이 실행되었을 것이다.<br />

---

##ObjC에서 Swift 사용하기

ObjC에서 Swift를 사용하는 것은 약간 복잡해 보일 수 있다.<br />
이번에는 `ObjectiveSwift`라는 프로젝트를 만들어 볼 것이다.<br />

이전과 같이 [Command Line Tool]을 선택한다.<br />
Product Name은 `ObjectiveSwift`을 기입하고, Language는 Objective-C를 선택한다.<br />

**Objective-C에서 Swift 코드를 혼합할 때는 Bridging Header가 필요하지 않다.**<br />
단순히, 모듈을 추가하고, Swift 코드를 작성할 때 약간의 주의만 있으면 된다.

우선 Person.swift 클래스를 작성하자.<br />
`⌘+N`를 누르면 템플릿 선택 시트가 나타난다. 여기서 [Cocoa Class]를 선택한다.<br />
[Class]란에는 `Person`, [Subclass of]는 `NSObject`, [Language]는 Swift를 선택한다.<br />

여기서 Bridging Header를 생성할 것인지를 묻는 시트가 나타나는데, **굳이 생성하지 않아도 된다.**<br />
만약 추가하려는 Swift 코드에서 Objective-C 코드를 사용할 필요가 있다면, Bridging Header를 만들어야 할 것이다.<br />

`Person.swift`파일을 열고, 아래 코드를 작성한다.<br />

```Swift

import Foundation

@objc public class: NSObject {
  public var name: String?
  public var age: NSNumber?

  public intro() {
    Swift.print("Hello, My name is \(name!), I'm \(age) years old.")
  }
}

```

Person 클래스를 작성하는 과정이 아주 짧게 끝이났다.<br />
다음으로, ObjC 코드에서 Swift를 사용할 수 있도록 **특수 헤더**를 import해야한다.<br />

특수 헤더라고 해도 특별할 건 없고, 그냥 소스코드에 **모듈이름-Swift.h**를 import 하면 된다.<br />

`main.m` 파일을 아래와 같이 작성해보자.<br />

```ObjC
#import <Foundation/Foundation.h>
#import "ObjectiveSwift-Swift.h"

int main(int argc, char* argv[]) {

  @autoreleasepool {
    Person* miles = [[Person alloc] init];
    [miles setName: @"Miles"];
    [miles setAge: 24];

    [miles intro];

  }

  return 0;
}

```

> **실행결과**<br />
> My name is Miles, I'm 24 yers old.<br />

잘 따라 했다면 아무 이상 없이 실행될 것이다.<br />

---

글을 작성하면서 발견한 점이 있는데, Objective-C에서 Swift를 사용할 때, 프로젝트 이름에 `-Swift`라는 이름이 있으면 안되다.<br />
처음에 이름을 Objective-Swift로 지정했다가, 헤더를 찾을 수 없다는 에러 때문에, 15분 가량 소요했다.<br />

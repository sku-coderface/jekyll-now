---
layout: post
title: XCode에서 Java 개발환경 구축하기
author: miles3898
---

주변에서 갑자기 Java를 한다기에, "오랜만에 자바를 해볼까..." 하는 생각이 들었지만서도, <br />
사실 다른 언어~~스위프트~~를 주로 사용하기에 IDE를 설치하기엔 귀찮은 점이 이만저만이 아니었다.<br />

그래서 "차라리 XCode에서 자바를 하자"라고 생각했다. <br />

우선 XCode를 열고 [`Create a new Xcode project`]를 선택한다. <br />
그리고, [`Cross-platform`] 탭에서 [`External Build System`]을 선택! <br />
<br />

프로젝트 이름은 원하는 대로, 그러나 `Build Tool` 란에는 `javac의 경로`를 적는다. <br />
보통 `/usr/bin/javac`를 사용한다.(심볼릭 링크지만, 상관없다.) <br />
<br />

###프로젝트 생성이 완료된 후...

![](https://cloud.githubusercontent.com/assets/17308570/21093464/58c82a9e-c095-11e6-89a6-e34aceee8b8d.png) <br />
위 그림에서 보듯, Augument란에 `$(ACTION) $(TARGETNAME)`을 기입한다. <br />

그리고 새 파일을 추가한다.<br />
[File]-[New]-[File]-[Other]-[Empty]<br />

파일의 이름을 간단히 JavaMain.java라 하고, 다시 프로젝트 설정에서 타겟의 이름을 엔트리포인트 클래스 이름으로 변경한다.<br />
![](https://cloud.githubusercontent.com/assets/17308570/21093520/9b291786-c095-11e6-983e-b938fc5f51a1.png) <br />

이렇게 하면 `컴파일`은 무사히 된다.<br />
다음으로, 실행을 위해 몇 가지 설정을 변경할텐데, 우선 [Product]-[Scheme]-[Edit Scheme]을 선택한다. <br />

[`Run`] 항목에서 `Info` 탭 선택 후, [`Executable`]에서 `Other` 선택 <br />

![](https://cloud.githubusercontent.com/assets/17308570/21093695/8cf01ab0-c096-11e6-8c1a-817b50332296.png) <br />
Cmd + Shift + G를 누르면 입력 창이 나타나는데, /usr/bin/java를 적는다.<br />

그리고 돌아와서 [`Arguments`] 탭에서 아래 그림과 같이 설정한다.<br />
![](https://cloud.githubusercontent.com/assets/17308570/21093743/c7bdabe4-c096-11e6-8db0-910dc7f515bf.png) <br />

마지막으로 [`Options`] 탭에서 `Working Directory`의 값을 프로젝트 디렉터리로 설정하면 개발 환경 준비가 끝난다.<br />

---
layout: post
title: Visual Studio Code + .NET Core 로 프로젝트 만들어서 실행하는 법
author: sftblw
---

슬랙에 올렸던 걸 정리하였습니다.

# 전제

VS Code에 C# 확장기능은 설치되어있다고 가정함.

# 방법

좀 성가심.

1. 닷넷 코어 런타임 설치 https://www.microsoft.com/net/core

2. VS Code로 프로젝트 만들 폴더를 엶

3. `Ctrl + ｀` 로 명령창을 엶 (윈도는 cmd, 리눅스 계열은 shell이 뜸)

4. `dotnet new` 를 연 명령창에서 실행.

  VS Code만의 프로젝트 파일은 없고, 대신 닷넷 코어의 프로젝트 형식은 있음. 파이썬의 `setup.py`나 nodejs & npm 의 `package.json` 처럼 닷넷코어에는 `project.json` 이라는 프로젝트 설정파일이 있음.

5. `dotnet restore` 를 치면  `project.lock.json`이 생기는데 이게 없으면 실행 자체가 안 됨. 왜 필요한진 모르겠음.

  이 두 절차는 닷넷코어의 프로젝트 설정 절차이고, 닷넷코어 첫화면에 설명으로 나와있음.

6. 일단 `dotnet run` 명령을 명령창에 넣으면 실행은 됨.

  놀랍게도 알아서 빌드하는데, 출력 결과물은 dll 파일. 왜죠 (\*추가: 닷넷 런타임과 바이너리를 분리하기 위함이 아닐까 추정. 예를 들어, 닷넷코어로 컴파일한 바이너리를 모노로 실행한다던가.)

7. VS Code에서 바로 실행하려면, 일단 디버그 하려고 하면 뭐 이것저것 만들라고 하는데,

  최종적으로 `.vscode` 폴더 안에 `launch.json` 하고 `tasks.json` 이 만들어지면 OK. 각각 `.NET Core` 를 둘 다 고르면 됨.

8. 이 상태에서 F5로 실행하면 안 되는데, `launch.json` 의 "program" 속성 두 개의 값에 있는 꺾쇠란을 알맞은 걸로 바꿔줘야 됨.

    `dotnet run` 이나 Ctrl + Shift + B 로 빌드 후 dll 파일이 있는 경로를 적으면 됨.

    예시)
    `"program": "${workspaceRoot}/bin/Debug/<target-framework>/<project-name.dll>",`
        ↓
    `"program": "${workspaceRoot}/bin/Debug/netcoreapp1.0/vscode_test.dll",`
    (`dotnet new`를 실행하던 상위 폴더 이름이 vscode_test 였음)

# 기타

참고: 디버그 콘솔은 debug console을 검색하거나 Ctrl + Shift + Y 로 띄울 수 있음. 결과가 일반 콘솔에도 나오긴 하는데...

덧붙이자면, VS Code는 자체 프로젝트 형식이 없는 대신 여러가지 프로젝트 파일을 읽어올 수 있음.
심지어 Visual Studio 의 `*.sln` 파일도 읽어온다는 모양. 해보진 않음.

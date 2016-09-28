---
layout: post
title: 여러 계정 관리
author: miles3898
---

## Github 계정이 여러 개일 때, 어떻게 관리할까?

Github 계정을 하나 더 생성했다... 경력 관리 목적으로 만든 계정이라 건전한 글을 쓰려는 참이다.<br />
그런데, github page를 생성하고 Jekyll을 사용해서 블로그를 관리하려 해보니 뭔가 문제가 생겼다.

```
ERROR: Permission to <다른 계정의 저장소> denied to <기존 계정>.
```

바로, 해당 계정이 아니기 때문에 새로 생성한 계정의 저장소에 `push`를 할 수 없는 문제였다.

`그렇다면 이걸 도대체 어떻게 해결해야 할까?`하는 와중에 해결 방법을 찾았다.

우선, git을 처음 설치하고 가장 먼저하는 설정 단계로 돌아가보자.

```bash
git config --global user.name <userName>
git config --global user.email <userEmail@domail.com>
```

여기서 `--global`이 뭘까 하는 고민을 해본 사람이 있을까?<br />
(아마 없을 것 같다. ~~글 내려주세요~~)

git은 사용자 정보를 세 가지 방법으로 저장한다.<br />
1. `/etc/gitconfig`: 시스템의 모든 사용자와 저장소의 default 사용자 정보가 없을 경우 이 정보를 참조한다.
2. '~/.gitconfig', '~/.config/git/config': 특정 사용자에게만 지정되는 사용자 정보로, 저장소의 default 사용자가 없을 경우 이 정보를 참조한다.
3. '.git/config': 특정 저장소에 지정되는 사용자 정보다.

사용자 정보를 찾는 우선 순위는 역순이다.

나의 경우, global 영역에 계정정보를 저장했기 때문에 시스템에 다른 사용자를 추가하지 않는 이상 다른 계정을 추가하는 방법은 저장소에 계정을 지정하는 방법 뿐이다.<br />

---

실질적으로 다른 계정에 대한 접근을 지정해보자.

우선 전제가 되는 상황을 먼저 기술해보자면 이렇다.<br />
1. 기존 계정: JavaScript<br />
2. 기존 이메일: JavaScript@github.com<br />
3. 새로운 계정: Python<br />
4. 새로운 이메일: Python@github.com<br />

기존 계정은 이렇게 설정되어 있을 것이다.<br />

```bash
git config --global user.name JavaScript
git config --global user.email JavaScript@github.com
```
그리고 기존 계정의 ssh-rsa는 ~/.ssh/id_rsa에 저장되어 있을 것이다.<br />

이제 새로운 계정을 추가할 차례다.

그 전에, ssh-rsa 키를 발급하자. 이미 있다고? 상관없다. 새로운 계정에서는 무용지물이니까.<br />
새로운 계정 이름이 Python이니, 공개키, 비밀키 파일 이름을 바꿔서 저장할 것이다.<br />

```bash
$ `ssh-keygen -t rsa -C "Python@github.com"`
Enter file in which to save the key (/Users/JavaScript/.ssh/id_rsa): `/Users/JavaScript/.ssh/id_rsa_Python`
Enter passphrase (empty for no passphrase): 
Enter same passphrase again: 
```
passphrase는 공란으로 뒀다. 매번 비밀번호 적는 게 귀찮으니...<br />

id_rsa는 이미 있을 테니, id_rsa와 id_rsa_Python 중에서 어떤 키를 사용해야할지 시스템이 알 수 있을까?<br />
config 파일을 작성해서 이 문제를 해결할 수 있다.

```bash
~/.ssh $ `touch config`
~/.ssh $ vi config
```

config 파일에 이렇게 작성한다.

```config
Host github.com
  HostName github.com
  User git
  IdentityFile ~/.ssh/id_rsa
  
Host github-Python
  HostName github.com
  User git
  IdentityFile ~/.ssh.id_rsa_Python
```
Host에 주어진 `github.com`과 `github-Python`은 remote upstream의 URL에서 식별된다.<br />
그리고 IdentityFile로써 어떤 키를 사용할지를 알려준다.

다음으로 새로운 계정의 원격 저장소의 이름을 PyStorage라고 하자.

```bash
git init
echo "Hello, Github!" > README.md
git -am "initial commit"
git remote add origin git@github-Python:Python/PyStorage
git push
```

위와 같은 과정으로써 새로운 계정을 이용해 다른 저장소에 접근할 수 있게 되었다.<br />

###읽어도 이해가 잘 안되는 분들을 위한 설명

리눅스 시스템을 부팅하면 로그인을 하는데, 이를 시스템 사용자라고 부르기로 한다.<br />
하나의 시스템 사용자가 별도의 계정을 만들어서 이중 생활(?)을 하고싶다.<br />
그러나 JavaScript라는 계정으로 이미 github를 사용하고 있다.<br />
당연히 ssh-rsa 키를 발급 받은 상태다.<br />

이중생활용 계정 Python을 생성했으나, 푸시를 하면 JavaScript 계정으로 접근 시도가 있엇다며, 접근을 막아버린다.<br />
Python 계정을 위해 ssh-rsa 키를 발급받는다. 이때 키는 ~/.ssh 디렉터리에 저장되며 비밀키, 공개키는 각각 `id_rsa_python`, `id_rsa_python.pub`이다.
일단 github에서 id_rsa_python.pub 파일의 내용을 붙여넣는다.

~/.ssh/ 디렉털에 config 파일을 만든다.
JavaScript 계정으로 저장소를 등록할 때는 git remote add origin https://github.com/JavaScript/JavaScript.github.io 와 같이 작성하며<br />
Python 계정으로 저장소를 등록할 때는 git remote add origin git@github-python:Python/Python.github.io 처럼 등록할 것이다.

따라서 config 파일에 기존 계정은 github.com으로 식별되며, Python 게정은 github-Python으로 식별된다. <br />
식별된 문자는 HostName 필드의 `github.com`으로 대체되며, 식별할 공개키 파일은 id_rsa_Python이다.<br />

이제 커밋 후, 푸시를 하면 정상적으로 푸시가 된다.<br />

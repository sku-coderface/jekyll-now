---
layout: post
title: Embedded Quiz paper
author: cineraria219
---

## 이광엽 교수님 Quiz 대비
끝나고 삭제 예정

* alias

```python
alias ll='ls -l'
alias cpa='cp -a'
alias rmd='rm -rf'
unalias rmd='rm -rf'
```

* awk

```python
ls -al | awk '{print $1, $9}' // list 중 1,9번 필드만 보이기
```

* bzip2

```python
bzip2 test.c hello.c  // 압축
bzip2 -d test.c.bz2   // 압축 해제
bunzip2 hello.c.bz2   // 압축 해제
```

* cat

```python
cat > text.txt    // 파일 만들고(있다면 덮음) 내용을 넣는다.
안녕하세요~
김민철입니다.
cat -n hello.c    // 라인넘버와 함께 출력
1 안녕하세요~
2 김민철입니다.
cat file.c | more // 맨 위에서 부터 스페이스바 눌러야 다음페이지 넘어감(엔터 한 줄)
```

* cd

```python
pwd        // 현재 디렉터리 확인
cd ..      // 상위 디렉터리
cd ~user2  // user2의 홈 디렉터리
cd /       // 루트 디렉터리
```

* chmod

```python
chmod ugo+rwx,go-wx test.c  // test.c 파일에 user group others에 권한 read write execute 줌
                            // test.c 파일에 group others에 권한 write execute을 뺏음
chmod 731 test.c            // 소유자에게 rwx, 그룹에게 wx, 다른 사용자에게 x 권한을 줌
```

* chown

```python
chown user2:student2 test.c // 소유자와 그룹의 변경
```

* cp

```python
alias | grep cp
alias cp='cp -i'
cp .test.c ./exam/  //
cp -R ./exam/* .
```

---
layout: post
title: Embedded Quiz paper
author: cineraria219
---

## 교수님 Quiz 대비
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
cp .test.c ./exam/  // test.c 파일을 exam 디렉터리로 복사
cp -R ./exam/* .    // exam 디렉터리 안의 모든 파일을 현재 위치로 복사
```

* diff

```python
cat > text1.txt
cat > text2.txt
diff -urN ./text1.txt ./text2.txt > result.txt    // -u 문맥상 합쳐진 줄을 지정한 n수만큼 보여준다.
                                                     -r 비교 대상 디렉터리의 하위 디렉터리나 파일까지 비교한다.
                                                     -N 존재하지 않는 파일을 빈 파일로 인식한다.
```

* file

```python
file text.txt     // ascii나 text나 date 같은 파일명의 종류를 알려준다.
file -b text.txt  // 지정한 파일명은 보여주지 않고, 파일의 유형만을 보여준다.
```

* find

```python
find /root -name test.c       // /root에서 test.c 찾기
find . -name "*.[ch]" -print  // 현재 디렉터리에서부터 .c .h 확장자를 가지는 모든 파일을 찾아 표준 출력으로 출력
```

* grep

```python
alias | grep cp         // cp 명령어에 대한 alias 설정 확인
rpm -qa | grep bootp    // rpm 패키지 검색 결과중 bootp가 들어가는 문자열을 찾는다.
```

* gzip / gunzip

```python
gzip -r dir       // dir 디렉터리 하위에 있는 파일 모두 압축
gzip -dr dir      // dir 디렉터리 하위에 있는 파일 모두 압축 해제
gunzip -r dir     // 위와 같음
```

* insmod / rmmod / lsmod

```python
insmod ./step.ko   // 현재 디렉터리에 있는 모듈 파일(step.ko)을 적재
lsmod              // 적재된 모듈의 확인
rmmod step         // 등록된 모듈 제거
```

* kill

```python
ps                  // 프로세스 확인
kill -9 9130        // PID가 9130인 프로세스를 강제종료한다.
```

* ln

```python
ln -s /home/use/html /Documents/test    // 소프트링크
ln test test_hardlink
```

* netstat

```python
netstat -au // 네트워크 상태 중 udp 소켓만 보여준다.
```

* ping

```python
pint -c 3 192.168.11.202  // 3회 만큼 packet을 전송한다.
```

* pwd

```python
pwd        // 현재 디렉터리의 절대 경로
```

* rm

```python
rm -i test.c    // 삭제하기전 확인
rm -f test.c    // 강제로 삭제
rm -rf          // 강제로 디렉터리 삭제
```

* tar

```python
tar cvfz archive.tar.gz .     // 현재 디렉터리에 압축
tar xvfz archive.tar.gz       // 압축 해제
```

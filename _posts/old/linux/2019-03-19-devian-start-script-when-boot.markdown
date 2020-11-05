---
title:      "Start script and service when boot"
categories: [Old]
last_modified_at:       2019-03-19 01:00
tags:
  - linux
  - devian
published: false
---

## 들어가며

리눅스는 시작시 /etc/init.d디렉토리 내부의 스크립트를 실행하도록 되어 있다. 따라서 이 디렉토리 내부에 실행하기 위한 코드를 삽입해 주면 부팅시 코드가 실행된다.

## 구현방안

##### 부팅시 디렉토리를 만드는 예제

```sh
$ cd /etc/init.d
$ vi ex_script 
```
ex_script라는 파일에 mkdir 명령어를 통해 test라는 디렉토리를 생성하도록 명령어를 넣는다.

```sh
#!/bin/bash
mkidr /home/test 
```

이제 이 명령어는 부팅시 /home디렉토리에 test라는 디렉토리를 생성 할 것이다. 부팅시 실행이 가능하도록 755로 권한을 변경한다.

```sh
$ chmod 755 ex_script 
```

이제 부팅시 만들어진 스크립트가 실행되도록 등록한다.

```sh
$ update-rc.d ex_script defaults 
```

경고 메세지가 출력될 수 있는데, 실행에는 문제가 없으므로 경고 메세지는 무시해도 된다.

이제 리눅스를 부팅하면 test라는 디렉토리가 생성될 것이다.

만약 등록한 스크립트를 해제한다면 아래처럼 defaults 대신 remove를 사용하면 된다.

```sh
$ update-rc.d ex_script remove 
```

##### 서비스 만들기

부팅시 디렉토리 만드는 예제처럼 똑같이 하면 된다. 여기서는 ex_script 파일 대신 test라는 파일을 만든다. 그리고 아래의 내용을 /etc/init.d 디렉토리 내에 저장한다.

```sh
#!/bin/bash

case "$1" in
        start)
                mkdir -p /home/server/test
        ;;
        stop)
                rm -rf /home/server/test
        ;;
        *)
                echo "Usage {start|stop}"
        exit 1
        ;;
esac
exit 0
```

부팅시 실행이 가능하도록 755로 권한을 변경한다.

```sh
$ chmod 755 test
```

스크립트가 실행되도록 등록한다.

```sh
$ update-rc.d test defaults
```

이제 서비스가 test라는 이름으로 등록되었다. 아래처럼 service 명령어를 통해 test를 start 시키면

```sh
$ service test start 
```

/home/server/test라는 디렉토리가 생성된 것을 확인 할 수 있다.

그럼 중단시키면 rm -rf 명령어때문에 test라는 디렉토리가 삭제된 것을 알 수 있을 것이다.

```sh
$ service test stop
```


출처: https://sacstory.tistory.com/entry/Debian-부팅시-script-및-서비스-시작하도록-등록하기 [나긋한 개발자]
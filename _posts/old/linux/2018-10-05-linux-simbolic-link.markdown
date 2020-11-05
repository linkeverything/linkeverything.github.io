---
title:      "Linux Symbolic link"
categories: [Old]
last_modified_at:       2018-10-05
tags:
  - linux
  - Ubuntu
  - CentOS
published: false
---

## 들어가며

심볼링 링크.. 단어가 크게 와닿지 않지만, 그저 링크라고 생각하면 됩니다. 윈도우에서 바로가기 같은 개념인데, Console 을 주로 이용하는 Linux 의 경우, 명령어를 조금만 잘못 입력하면 문제가 생기는 경우가 있어, 포스팅하면서 한번 더 복습합니다. 

실무에서도 예전에는 링크를 많이 만들었었는데, 사람들 손이 빨라져서인지, 아니면 가시적인 효과가 덜해서인지는 잘 모르겠으나 심볼릭 링크는 잘 사용하지 않고 있습니다. 물론 그렇다고 없어지는 추세다.. 라는 것은 아니나 너무 빈번하게 사용하면 잘 눈에 보이지 않아, 꼭 필요한 경우에 사용하시길 권장 드립니다. 


## 학습

##### 심볼릭 링크 생성

생성 명령어는 ln 입니다. link 의 약어처럼 보이는데 맞는지는 잘 모르겠습니다. 파일의 링크 생성시에는 -s 옵션을 붙여서 원본과 링크파일을 명시해 주면 됩니다. 


> ln -s {원본파일} {링크파일}


연습용 파일을 아래 명령어로 생성하고 확인합니다. 

```sh
ubuntu@ip-172-26-15-247:~$ echo hello > test.txt
ubuntu@ip-172-26-15-247:~$ cat test.txt
hello
ubuntu@ip-172-26-15-247:~$ ll test.txt
-rw-rw-r-- 1 ubuntu ubuntu 6 Oct  5 00:01 test.txt
ubuntu@ip-172-26-15-247:~$
```

아래와 같이 심폴릭 링크를 생성하고 확인합니다. 

```sh
ubuntu@ip-172-26-15-247:~$ ln -s ~/test.txt ~/tmp/s_link.txt
ubuntu@ip-172-26-15-247:~$ ll ~/tmp/s_link.txt
lrwxrwxrwx 1 ubuntu ubuntu 21 Oct  5 00:13 /home/ubuntu/tmp/s_link.txt -> /home/ubuntu/test.txt
ubuntu@ip-172-26-15-247:~$ cat ~/tmp/s_link.txt
hello
ubuntu@ip-172-26-15-247:~$
```

##### 하드링크와 심볼릭 링크

앞서 설명한 -s 옵션은 심볼릭 링크의 옵션입니다. 그리고 아무런 옵션을 주지 않으면 하드 링크라고 부르고 이는 다음과 같은 차이가 있습니다. 실제로 만들어 놓고 그 차이를 확인해 봅니다. 

```sh
ubuntu@ip-172-26-15-247:~$ ln ~/test.txt ~/tmp/h_link.txt
ubuntu@ip-172-26-15-247:~$ ls -i ~/test.txt
256131 /home/ubuntu/test.txt
ubuntu@ip-172-26-15-247:~$ ls -i ~/tmp/h_link.txt
256131 /home/ubuntu/tmp/h_link.txt
ubuntu@ip-172-26-15-247:~$ ls -i ~/tmp/s_link.txt
524159 /home/ubuntu/tmp/s_link.txt
ubuntu@ip-172-26-15-247:~$
```

위에서 확인한 것 처럼, 원본 파일과 하드링크 파일은 디스크에서 완전히 동일한 파일입니다. 하지만 심볼릭 링크 파일은 다른 파일이고 윈도우에서의 바로가기처럼 링크만 가지고 있는 것이라고 확인할 수 있습니다. 

##### 심볼릭 링크의 원본 삭제

원본 파일을 삭제하면, 심볼릭 링크에 해당하는 파일은 존재하지만 실제 링크를 따라가지는 못합니다. (단 하드링크는 그야말로 복사본을 가지고 있게 되기 때문에 원본이 삭제되어도 하드링크 파일은 그대로 살아 있습니다. 

```sh
ubuntu@ip-172-26-15-247:~$ rm -f ~/test.txt
ubuntu@ip-172-26-15-247:~$ ll ~/tmp/s_link.txt
lrwxrwxrwx 1 ubuntu ubuntu 21 Oct  5 00:13 /home/ubuntu/tmp/s_link.txt -> /home/ubuntu/test.txt
ubuntu@ip-172-26-15-247:~$ cat ~/tmp/s_link.txt
cat: /home/ubuntu/tmp/s_link.txt: No such file or directory
ubuntu@ip-172-26-15-247:~$ cat ~/tmp/h_link.txt
hello
```

##### 심볼릭 링크 삭제 - 파일

파일에 대한 심볼릭 링크는 rm -f 로 삭제하면 됩니다. 이렇게 링크를 삭제하면 원본 파일은 그대로 남아 있습니다. 

```sh
ubuntu@ip-172-26-15-247:~$ touch ~/tmp/myfile.txt
ubuntu@ip-172-26-15-247:~$ ln -s ~/tmp/myfile.txt myfilelink
ubuntu@ip-172-26-15-247:~$ ll | grep myfilelink
lrwxrwxrwx  1 ubuntu ubuntu    27 Oct  5 00:28 myfilelink -> /home/ubuntu/tmp/myfile.txt
ubuntu@ip-172-26-15-247:~$
ubuntu@ip-172-26-15-247:~$
ubuntu@ip-172-26-15-247:~$ rm -f myfilelink
ubuntu@ip-172-26-15-247:~$ ll | grep myfilelink
ubuntu@ip-172-26-15-247:~$ ll ~/tmp/myfile.txt
-rw-rw-r-- 1 ubuntu ubuntu 0 Oct  5 00:27 /home/ubuntu/tmp/myfile.txt
ubuntu@ip-172-26-15-247:~$
```

##### 심볼릭 링크 삭제 - 폴더

폴더에 대한 삭제는 똑같이 rm -f 로 삭제하면 된다. 마지막에 / 를 붙이면 삭제가 되지 않는다. 

```sh
ubuntu@ip-172-26-15-247:~$ mkdir -p ~/tmp/myfolder/hello
ubuntu@ip-172-26-15-247:~$ ln -s ~/tmp/myfolder/ myfolderlink
ubuntu@ip-172-26-15-247:~$ ll | grep myfolderlink
lrwxrwxrwx  1 ubuntu ubuntu    26 Oct  5 00:32 myfolderlink -> /home/ubuntu/tmp/myfolder//
```
```shell_session
ubuntu@ip-172-26-15-247:~$ rm -f myfolderlink/
rm: cannot remove 'myfolderlink/': Is a directory
```

이 경우, rm -rf 명령어를 사용하면 삭제가 가능하지만, 원본이 삭제되므로 주의한다.

```sh
ubuntu@ip-172-26-15-247:~$ rm -f myfolderlink
ubuntu@ip-172-26-15-247:~$ ll | grep myfolderlink
ubuntu@ip-172-26-15-247:~$ ll ~/tmp/myfolder/
total 12
drwxrwxr-x 3 ubuntu ubuntu 4096 Oct  5 00:31 ./
drwxrwxr-x 3 ubuntu ubuntu 4096 Oct  5 00:31 ../
drwxrwxr-x 2 ubuntu ubuntu 4096 Oct  5 00:31 hello/
ubuntu@ip-172-26-15-247:~$
```


출처: https://4urdev.tistory.com/49 [Simplify]
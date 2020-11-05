---
title:      "How to terminate process with a specific port"
categories: [old]
last_modified_at:       2018-02-23
tags:
  - Windows
published: false
---

## 들어가며

특정 포트를 사용하고 있는 프로세스를 검색하여, 해당 프로세스를 종료시켜야 하는 경우가 있습니다. 창이 떠 있는 프로세스의 경우에는 크게 문제될 것이 없어보이지만(창을 닫으면 프로세스가 종료됨), 화면이 없는 프로세스의 경우에는 찾아서 종료시키는 것이 까다롭습니다. 

예를 들어, 개발툴로 아직도 Eclipse 를 많이 사용하고 있는데, Eclipse 에 embedded tomcat, 혹은 외부 tomcat 에 올려서 웹 프로젝트를 진행하다가 eclipse 가 비정상적으로 종료되는 경우, tomcat 프로세스가 아직 살아있으면서 다시 실행이 안되는 문제점이 발생합니다. 

이런 프로세스는 작럽관리자를 실행시키면 보이긴 하지만, 어떤 포트를 사용하고 있는지는 잘 확인하기가 힘듭니다. 따라서 아래 명령어를 통해 프로세스를 검색하고 이를 종료시키면 됩니다. 


## 해결방안

##### 프로세스 검색

```dos
netstat -ano | findstr :yourPortNumber
```

여기서 &#124; 는 추가적인 동작을 하기 위한 것으로 linux 시스템과 동일한 구문이고, findstr 은 linux 에서 grep 명령어에 해당하는 찾기 명령어입니다. 결국 netstat -ano 옵션으로 리스트를 하는데, :포트번호 를 검색해서 그런 라인만 출력하도록 하는 것입니다. 

하나의 포트를 다중 프로세스가 점유하는 것이 불가능하기 때문에 하나의 라인만 출력되는 것이 정상입니다.



##### 프로세스 종료

```dos
taskkill /PID typeyourPIDhere /F
```

taskkill 명령어는 kill 명령어와 동일하고 /pid 옵션으로 특정 pid 의 task 를 죽이겠다는 것으로 /f 는 강제 종료를 의미합니다.





출처 : https://stackoverflow.com/questions/39632667/how-to-kill-a-currently-using-port-on-localhost-in-windows



출처: https://4urdev.tistory.com/36 [Simplify]
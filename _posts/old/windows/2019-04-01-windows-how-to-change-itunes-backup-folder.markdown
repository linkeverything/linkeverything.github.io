---
title:      "How to change itunes backup folder in Windows 10 (Store ver.)"
categories: [Old]
last_modified_at:       2019-04-01 00:00
tags:
  - Windows
  - iPhone
  - iTunes
published : true
published: false
---

## 들어가며

아이튠즈는 기본적으로 백업 폴더를 사용자가 지정할 수 없도록 강제하고 있습니다. 제 경우, 실제 아이폰에서 설정>일반>저장용량 쪽에서 확인한 용량보다, 실제 백업 용량이 훨씬 더 컸던 것으로 기억합니다. 약 40G정도를 사용하는데, 실제 백업은 70G정도 되던 것으로 기억합니다. 이건 실제 캐시 데이터(즉 임시파일 등등)에 대해서 용량으로 계산되지 못하게 하고 있고, 이를 빈 공간으로 보여주게 설계된 탓에(애플 제품 모두..) 이렇게 보일 수 있다고 합니다. 

예전에 Apple 홈페이지에서 iTunes 를 다운로드 받아 설치하면 특정 경로에 백업이 설정되도록 했었는데, Windwos 10, Microsoft Store 에서 다운로드 받은 iTunes 는 그 설정이 조금 바뀌었기 때문에 여기서 다시 한 번 정리합니다. 구 버전에 대해서도 아래 쪽에서 정리하도록 합니다.


## 설정하기

아마도 apple 홈페이지에서 다운로드 받은 버전과, 스토어 버전이 동시 실행이 가능한 상태이기 때문에, 파일을 같은 곳에 쌓아두면 서로 충돌이 발생할 수 있어, 파일을 분리하게 된 것 같습니다. 아래와 같이 찾아서 변경할 수 있습니다. 


##### 1. Use the *Windows key + X* keyboard shortcut to open the Power User menu and select Programs and Features.

##### 2. Click the *Turn Windows features on or off* link.

![](/assets/images/posts/old/img/post/2019-03-19-windows-how-to-enable-ftp-server-on-windows-10/windows-how-to-enable-ftp-server-on-windows-10-00001.webp)

##### 3. Expand Internet Information Services and check the *FTP Server* option.

##### 4. Expand FTP Server and check the *FTP Extensibility* option.

##### 5. Check *Web Management Tools* with the default selections.

##### 6. Click *OK* to begin the installation.

![](/assets/images/posts/old/img/post/2019-03-19-windows-how-to-enable-ftp-server-on-windows-10/windows-how-to-enable-ftp-server-on-windows-10-00002.webp)

##### 7. Click *Close*.


## How to configure an FTP site on Windows 10

After installing the necessary components to run an FTP server on your PC, you need to create an FTP site using the following instructions:

##### 1. Use the *Windows key + X* keyboard shortcut to open the Power User menu and select Control Panel.

##### 2. Open *Administrative Tools*.

##### 3. Double-click *Internet Information Services (IIS) Manager*.

![](/assets/images/posts/old/img/post/2019-03-19-windows-how-to-enable-ftp-server-on-windows-10/windows-how-to-enable-ftp-server-on-windows-10-00003.webp)

##### 4. Expand and right-click *Sites* on the Connections pane.

##### 5. Select *Add FTP Site*.

![](/assets/images/posts/old/img/post/2019-03-19-windows-how-to-enable-ftp-server-on-windows-10/windows-how-to-enable-ftp-server-on-windows-10-00004.webp)

##### 6. Name your new FTP site and enter the path to the FTP folder you want to use to send and receive files.

> Note: You can also use the Make New Folder button to create a specific folder to store your FTP files.

##### 7. Click *Next*.

![](/assets/images/posts/old/img/post/2019-03-19-windows-how-to-enable-ftp-server-on-windows-10/windows-how-to-enable-ftp-server-on-windows-10-00005.webp)

##### 8. On Binding and SSL Settings leave all the default settings, but change the SSL option to *No SSL*.

> Note: It's worth pointing out that in a business environment or on an FTP server that will host sensitive data, it's best practice to configure the site to require SSL.

##### 9. Click *Next*.

![](/assets/images/posts/old/img/post/2019-03-19-windows-how-to-enable-ftp-server-on-windows-10/windows-how-to-enable-ftp-server-on-windows-10-00006.webp)

##### 10. On Authentication, check the *Basic* option.

##### 11. On Authorization, select *Specified users* from the drop-down menu.

##### 12. Type the email address of your Windows 10 account or local account name to allow yourself access to the FTP server.

##### 13. Check the options *Read* and *Write*.

##### 14. Click *Finish*.

![](/assets/images/posts/old/img/post/2019-03-19-windows-how-to-enable-ftp-server-on-windows-10/windows-how-to-enable-ftp-server-on-windows-10-00007.webp)


## How to allow an FTP server through Windows Firewall

If you have Windows Firewall running on your computer, then the security feature will block any connections trying to access the FTP server. Use the steps below to allow the FTP server through the firewall.

##### 1. Open the Start menu, do a search for *Windows Firewall*, and press *Enter*.

##### 2. Click the *Allow an app or feature through Windows Firewall* link.

![](/assets/images/posts/old/img/post/2019-03-19-windows-how-to-enable-ftp-server-on-windows-10/windows-how-to-enable-ftp-server-on-windows-10-00008.webp)

##### 3. Click the *Changes* settings button.

##### 4. Select *FTP Server* and make sure to allow it on a *Private* and *Public* network.

##### 5. Click *OK*.

![](/assets/images/posts/old/img/post/2019-03-19-windows-how-to-enable-ftp-server-on-windows-10/windows-how-to-enable-ftp-server-on-windows-10-00009.webp)

At this point, you should be able to use your favorite FTP client to connect your newly created FTP server from your local network.

> Note: Make sure to check your software vendor support website for specific instructions to allow an FTP server, if you're using another security software other than the Windows Firewall.

출처 : https://www.windowscentral.com/how-set-and-manage-ftp-server-windows
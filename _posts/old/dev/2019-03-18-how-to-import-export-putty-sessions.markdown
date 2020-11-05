---
title:      "How to Backup and restore putty sessions"
categories: [old]
last_modified_at:       2019-03-18 01:00 tags: [putty, windows]
published: false
---

## 들어가며

putty에 세션을 백업하고 복원하는 것이 가끔이나마 필요하여 다음과 같이 정리합니다. 출처는 글의 맨 아래에 명시 해 두었습니다.

## 백업/복원방법

### Export

#### cmd.exe, require elevated prompt:

##### Only sessions:

```cmd
regedit /e "%USERPROFILE%\Desktop\putty-sessions.reg" HKEY_CURRENT_USER\Software\SimonTatham\PuTTY\Sessions
```

##### All settings:

```cmd
regedit /e "%USERPROFILE%\Desktop\putty.reg" HKEY_CURRENT_USER\Software\SimonTatham
```

#### Powershell:

##### Only sessions:

```cmd
reg export HKCU\Software\SimonTatham\PuTTY\Sessions ([Environment]::GetFolderPath("Desktop") + "\putty-sessions.reg")
```

##### All settings:

```cmd
reg export HKCU\Software\SimonTatham ([Environment]::GetFolderPath("Desktop") + "\putty.reg")
```

### Import

Double-click on the &#42;.reg file and accept the import.

#### Alternative ways:

##### cmd.exe, require elevated command prompt:

```cmd
regedit /i putty-sessions.reg
regedit /i putty.reg
```

##### PowerShell:

```cmd
reg import putty-sessions.reg
reg import putty.reg
```

- Note: **do not replace** ```SimonTatham``` with your username.

- Note: It will create a ```reg``` file on the Desktop of the current user.

- Note: It will **not** export related SSH keys.

출처 : https://stackoverflow.com/questions/13023920/how-to-export-import-putty-sessions-list
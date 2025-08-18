[[linux_idx]]


---


```bash

-> 失敗
# https://nssm.cc/download
nssm.exe install "demoService" "C:\demo\demo.exe"
nssm.exe start DemoService
nssm.exe stop DemoService
nssm.exe remove "DemoService"



# ssh 設定 default shell 為 pwsh 7
New-ItemProperty -Path "HKLM:\SOFTWARE\OpenSSH" -Name DefaultShell -Value "C:\Program Files\PowerShell\7\pwsh.exe" -PropertyType String -Force





啟動資料夾
%AppData%\Microsoft\Windows\Start Menu\Programs\Startup


start /min glances -w -> 跑出 python 的


```
## 例外
- 若开启了bit locker硬盘加密无法绕密

## 1️⃣
开机后按住`shift`键点击重启，开启windows的高级启动选项，选择疑难解答，依次点击高级选项-命令提示符。
## 2️⃣
输入以下代码：
```
copy C:\Windows\System32\utilman.exe C:\Windows\System32\utilman.exebk #复制辅助功能的备份
copy C:\Windows\System32\cmd.exe C:\Windows\System32\utilman.exe          #将辅助功能选项替换为cmd命令行工具
```
回车后会提示是否覆盖，输入yes覆盖即可

## 3️⃣
关闭命令行工具，点击继续重启，重启后点击辅助功能的小人打开cmd命令行
```
net localgroup administrators #列出账户名，选择你自己的账户名
net user 你的账户名 *               #会提示输入新密码，若没有则
control userpasswords2
```
登陆，选择🔑输入密码。

# PE盘
- 打开Dism++，点击上方出现的系统版本，打开会话
- 左侧工具箱-账户管理，选择相对应的用户名看能否清空密码



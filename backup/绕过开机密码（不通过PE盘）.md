利用粘滞键:连按5次shift键,弹出窗口,即可利用 利用windows错误修复(通过开机时断电)

![image](https://github.com/user-attachments/assets/f83c5e8b-7e4b-4d68-baa7-5a3a18ff491e)

选择取消,让win7尝试修复

![image](https://github.com/user-attachments/assets/a24406b9-4dd7-4388-ae27-288f338dfcb7)

肯定修复不明白

![image](https://github.com/user-attachments/assets/0279a081-97bc-494e-b8ac-71db9476bd07)

打开第二个文档 是一个记事本 

![image](https://github.com/user-attachments/assets/c3807171-862a-4934-bbec-5e9d28802d17)

在文件中可以找到”打开选项”,即可进入系统目录 

![image](https://github.com/user-attachments/assets/22ca71f3-0039-4aec-92b7-f089b8697959)

![image](https://github.com/user-attachments/assets/b02602d7-77cd-44b5-8f25-8939cba13446)

那么就可以有一个思路: 找到cmd 复制一个新的并将其重命名为粘滞键的程序 
1. 进入`windows\system32\`找到`sethc`也就是粘滞键的程序

![image](https://github.com/user-attachments/assets/b6987ea4-aafb-4b1a-b080-a2681ad5d844)

2. 将sethc改掉任意名,cmd复制并重命名为sethc

![image](https://github.com/user-attachments/assets/0a3d3784-0cfc-4545-acd3-3e070a52a635)

3. 然后重启 再次连按5次shift键即可打开cmd窗口 
4. 就可以有两种思路:

- 1.更改密码
 `net user` 查看用户

​ `net user 用户名 ""密码" `更改密码

​- 2.添加用户`net user root 123 /add`

​ 移动到管理员组`net localgroup administrators root /add`

- cmd常见的小命令:

​ `rd/s/q C:\Users\Administrator\myfirstwebpack`执行后会永久删除文件。

​  自动关机：`shutdown -s -t 0 0`秒后自动关机

​ 添加计划任务：`at 09:09 ./1.bat `补充：在win10中at命令已经弃用,改`schtasks`

​ 复制文件：`copy -r `

​ 启动net use服务;：`net start networkstation`

cmd编写文件：

`echo >1.txt` 覆盖原来文件

`echo >>1.txt `从最后一行开始写 

重命名：`rename` 

win10的登录界面程序从`sethc变为utilman`可以重复上述步骤

具体步骤就不再演示
## 一、开启虚拟化
打开任务管理器-CPU，查看下方是否显示为已启用：（BIOS中开启）
<div align=center>
<img src="https://github.com/user-attachments/assets/dad8dbb4-52f0-42cc-b9e1-0ef4a4d9b0a7" width="20%" height="20%"/>
</div>

## 二、启用相关Windows功能
<div align=center>
<img src="https://img-bca.pages.dev/api/file/f171be8ceb88001d29f7a.png" width="40%" height="40%"/>
</div>

## 三、安装WSL
使用cmd命令行输入以下代码：
```bash
wsl --install
```
若是在国内网络不畅可以尝试如下：
```bash
wsl --install --web-download
```
> [!TIP]
> 理想情况下这样应该已安装完成（默认安装的Ubuntu），不过大概率是会报错！

> [!NOTE]
> Go to https://aka.ms/wsl2kernel. Then download and install the Linux kernel update package:https://wslstorestorage.blob.core.windows.net/wslblob/wsl_update_x64.msi

之后在命令行中输入`wsl --update`

- 当然还可以安装其他的Linux发行版，通过`wsl --list --online`命令即可查看
<div align=center>
<img src="https://img-bca.pages.dev/file/9b3fdff2120a679b8cacf.png" />
</div>

## 四、安装kali图形化界面
1. 更新系统源
`apt-get update`
2. 安装图形化界面
`apt-get install kali-win-kex`
3. 运行kex
`kex --esm --ip -sound`
之后系统会调用远程桌面连接，连接到kali
## 五、安装kali的所有工具
```
sudo apt-get install kali-linux-default  # 安装默认工具集
或
sudo apt-get install kali-linux-large    # 安装更全面的工具集
或
sudo apt-get install kali-linux-everything # 安装所有的工具
```
# 依赖
- 电脑 Win/Mac/Linux，需要支持网线连接，如果是 Mac，需要一个网线转接器
- 小米路由器 4A 千兆版
- Python3 环境
- 网线
# 开启路由器SSH
需要先开启路由器的 SSH，小米的固件只有开发版支持 SSH，但是现在所有的版本基本都不会放出开发版；所以只能通过破解的方式开启(开启后不支持保修)；

因为小米路由器系统存在 [CVE-2019-18370](https://nvd.nist.gov/vuln/detail/CVE-2019-18370) 漏洞，所以可以通过该漏洞开启 SSH；需要使用项目 [acecilia/OpenWRTInvasion](https://github.com/acecilia/OpenWRTInvasion) 提供的脚本

- 下载 [acecilia/OpenWRTInvasion](https://github.com/acecilia/OpenWRTInvasion) ，并安装依赖
```
git clone https://github.com/acecilia/OpenWRTInvasion.git
cd OpenWRTInvasion
pip3 install -r requirements.txt
```
- 执行脚本

通过 WiFi 或有线的方式连接到路由器，然后进入命令行，执行以下命令，运行 `remote_command_execution_vulnerability.py` 脚本；执行脚本时需要输入路由器的地址和后台访问密码；地址如果没有改动默认就是 `192.168.31.1`，或者访问 `miwifi.com`也可以直接访问到；如果选择了离线模式，会在本地启动一个 ftp server
```
python3 remote_command_execution_vulnerability.py
```
在执行结束后，会输出提示信息，用于通过 SSH 访问路由器
```
here two options to provide the files needed for invasion:
1. Use a local TCP file server runing on random port to provide files in local directory `script_tools`.
2. Download needed files from remote github repository. (choose this option only if github is accessable inside router device.)
Which option do you prefer? (default: 1)1
****************
router_ip_address: miwifi.com
stok: f19c778f7bc6fbe92cc70a6c6bd2bd4f
file provider: local file server
****************
start uploading config file...
start exec command...
local file server is runing on 0.0.0.0:57165. root='script_tools'
local file server is getting 'busybox-mipsel' for 192.168.31.1.
local file server is getting 'dropbearStaticMipsel.tar.bz2' for 192.168.31.1.
done! Now you can connect to the router using several options: (user: root, password: root)
* telnet miwifi.com
* ssh -oKexAlgorithms=+diffie-hellman-group1-sha1 -c 3des-cbc -o UserKnownHostsFile=/dev/null root@miwifi.com
* ftp: using a program like cyberduck
```
- SSH访问路由器

通过指定参数的方式，使用 SSH 访问路由器，用户名密码都是 `root`
```
ssh -oKexAlgorithms=+diffie-hellman-group1-sha1 -c 3des-cbc -o UserKnownHostsFile=/dev/null root@miwifi.com
```
# 刷入 Breed
启用 SSH 之后，就可以刷入 Breed 了；Breed 是由 [HackPascal](https://github.com/hackpascal) 开发的 Bootloader（引导装载程序），最大的特点是支持通过访问网页刷机，刷机失败后可以直接重置而不会导致机器变砖
- 下载

小米路由器4A试用的CPU是mt7621，可以在 [https://breed.hackpascal.net/](https://breed.hackpascal.net/breed-mt7621-pbr-m1.bin) 下载，名称为 `breed-mt7621-pbr-m1.bin`
- 上传

下载 Breed 后，需要通过 FTP 的方式上传到路由器，可以使用 [Cyberduck](https://cyberduck.io/download/)
连接地址为路由器的地址，默认是 `192.168.31.1`，选择上传到 `/tmp` 目录下（其他目录可能会因为权限导致上传失败）
![image](https://github.com/DIO0v0/DIO0v0.github.io/assets/152008182/b53898ba-d48e-4774-9e11-56879096f550)
- 刷入

通过 SSH 登录路由器，执行以下命令，刷入 Breed 固件，需要注意的是，输入 Breed 后路由器不会开启 WiFi，只能通过网线的方式访问；另外刷入 Breed 后，路由器的指示灯会变得很暗或完全不亮，可以通过网线连接电脑，查看连接属性，如果分配了 IP 则说明路由器正常进入 Breed
```
cd /tmp
mtd write breed-mt7621-pbr-m1.bin Bootloader
```
刷入完成后，路由器会重启；通过网线连接后访问 http://192.168.1.1/ 即可进入 Breed 界面
<img width="1255" alt="homelab-miwifi-breed-info" src="https://github.com/DIO0v0/DIO0v0.github.io/assets/152008182/b913654c-dcbb-4e15-9162-be0350c216c3">
# 刷入OpenWrt
注意，下面的镜像都有 initramfs-kernel 和 squashfs-sysupgrade 两种；需要使用的是 squashfs-sysupgrade；如果刷入 initramfs-kernel，在重启后配置会丢失，这是因为 initramfs-kernel 将配置保存在内存中，而不是磁盘
## 下载官方镜像
官方镜像可以从以下两个镜像下载；测试发现官方的 initramfs-kernel 可以正常刷入，但是 squashfs-sysupgrade 刷入后会导致路由器不断的重启，无法工作
- [Xiaomi Mi Router 4A Gigabit Edition 说明文档](https://openwrt.org/inbox/toh/xiaomi/xiaomi_mi_router_4a_gigabit_edition)
<img width="1332" alt="homelab-miwifi-openwrt-firmware-official2" src="https://github.com/DIO0v0/DIO0v0.github.io/assets/152008182/c553226f-a077-483f-b7a9-dafec705d83c">

- [https://firmware-selector.openwrt.org/](https://firmware-selector.openwrt.org/?version=21.02.3&target=ramips/mt7621&id=xiaomi_mi-router-4a-gigabit)

![image](https://github.com/DIO0v0/DIO0v0.github.io/assets/152008182/71526f16-5cd9-4e67-8b00-34b3098ef23a)


## 刷入OpenWrt
进入 Breed，选择固件更新，选择固件，在文件夹中选择 `Kernel` 版本的镜像，上传并更新；
<img width="1258" alt="homelab-miwifi-breed-firmware-upload" src="https://github.com/DIO0v0/DIO0v0.github.io/assets/152008182/0fc377d6-20ea-40de-8ae6-e185030aff8a">
![image](https://github.com/DIO0v0/DIO0v0.github.io/assets/152008182/0f10a0e9-a004-4cae-9c88-e4914993e3cf)

等待刷入完成后，访问 http://192.168.31.1/ 即可进入 OpenWrt， 默认的用户名密码是 root 和 password
<img width="1671" alt="homelab-miwifi-openwrt-homepage" src="https://github.com/DIO0v0/DIO0v0.github.io/assets/152008182/fefe29d5-5b50-426c-984f-b4ee4ef266fc">

如果刷入失败，或者想重新刷入其他固件，可以将路由器断电，按住 Reset 后通电，直到路由器指示灯闪烁后松开 Reset，即可进入 Breed 重新进行刷机










以navicat15为例，菜单栏上点击`文件`，选择`导出连接...`，一定要选中`导出密码`！！！导出格式为`*.ncx`（该ncx本质上是xml文件，文件中包含连接的全部信息），密码可以在此文件中看到
<div align=center>
<img src="https://github.com/user-attachments/assets/a9bf53d0-bdba-4153-a3a4-10f90cf509b8" width="60%" height="60%"/>
</div>
## 一
在网上找的代码，本地运行或者找php在线运行都可。

```php
<?php
class NavicatPassword
{
    protected $version = 0;
    protected $aesKey = 'libcckeylibcckey';
    protected $aesIv = 'libcciv libcciv ';
    protected $blowString = '3DC5CA39';
    protected $blowKey = null;
    protected $blowIv = null;
     
    public function __construct($version = 12)
    {
        $this->version = $version;
        $this->blowKey = sha1('3DC5CA39', true);
        $this->blowIv = hex2bin('d9c7c3c8870d64bd');
    }
     
    public function encrypt($string)
    {
        $result = FALSE;
        switch ($this->version) {
            case 11:
                $result = $this->encryptEleven($string);
                break;
            case 12:
                $result = $this->encryptTwelve($string);
                break;
            default:
                break;
        }
         
        return $result;
    }
     
    protected function encryptEleven($string)
    {
        $round = intval(floor(strlen($string) / 8));
        $leftLength = strlen($string) % 8;
        $result = '';
        $currentVector = $this->blowIv;
         
        for ($i = 0; $i < $round; $i++) {
            $temp = $this->encryptBlock($this->xorBytes(substr($string, 8 * $i, 8), $currentVector));
            $currentVector = $this->xorBytes($currentVector, $temp);
            $result .= $temp;
        }
         
        if ($leftLength) {
            $currentVector = $this->encryptBlock($currentVector);
            $result .= $this->xorBytes(substr($string, 8 * $i, $leftLength), $currentVector);
        }
         
        return strtoupper(bin2hex($result));
    }
     
    protected function encryptBlock($block)
    {
        return openssl_encrypt($block, 'BF-ECB', $this->blowKey, OPENSSL_RAW_DATA|OPENSSL_NO_PADDING);
    }
     
    protected function decryptBlock($block)
    {
        return openssl_decrypt($block, 'BF-ECB', $this->blowKey, OPENSSL_RAW_DATA|OPENSSL_NO_PADDING);
    }
     
    protected function xorBytes($str1, $str2)
    {
        $result = '';
        for ($i = 0; $i < strlen($str1); $i++) {
            $result .= chr(ord($str1[$i]) ^ ord($str2[$i]));
        }
         
        return $result;
    }
     
    protected function encryptTwelve($string)
    {
        $result = openssl_encrypt($string, 'AES-128-CBC', $this->aesKey, OPENSSL_RAW_DATA, $this->aesIv);
        return strtoupper(bin2hex($result));
    }
     
    public function decrypt($string)
    {
        $result = FALSE;
        switch ($this->version) {
            case 11:
                $result = $this->decryptEleven($string);
                break;
            case 12:
                $result = $this->decryptTwelve($string);
                break;
            default:
                break;
        }
         
        return $result;
    }
     
    protected function decryptEleven($upperString)
    {
        $string = hex2bin(strtolower($upperString));
         
        $round = intval(floor(strlen($string) / 8));
        $leftLength = strlen($string) % 8;
        $result = '';
        $currentVector = $this->blowIv;
         
        for ($i = 0; $i < $round; $i++) {
            $encryptedBlock = substr($string, 8 * $i, 8);
            $temp = $this->xorBytes($this->decryptBlock($encryptedBlock), $currentVector);
            $currentVector = $this->xorBytes($currentVector, $encryptedBlock);
            $result .= $temp;
        }
         
        if ($leftLength) {
            $currentVector = $this->encryptBlock($currentVector);
            $result .= $this->xorBytes(substr($string, 8 * $i, $leftLength), $currentVector);
        }
         
        return $result;
    }
     
    protected function decryptTwelve($upperString)
    {
        $string = hex2bin(strtolower($upperString));
        return openssl_decrypt($string, 'AES-128-CBC', $this->aesKey, OPENSSL_RAW_DATA, $this->aesIv);
    }
};
 
 
//navicat版本，11，或者12.如果用的15，这里可以填12试试

$navicatPassword = new NavicatPassword(11);
 
//解密
$decode = $navicatPassword->decrypt('xxxxxxxxxxxxxxx'); // navicat密钥
echo $decode."\n";
?>
```
navicat版本11或12都可以试试

![](https://i0.hdslb.com/bfs/article/0b5bd08f247c53d54a88a9784933477e487596537.png)


## 二
当然还有更简单的方法，[navicat_password_decrypt](https://github.com/Zhuoyuan1/navicat_password_decrypt)，忘记navicat密码时,此工具可以帮您查看密码。
![](https://i0.hdslb.com/bfs/article/535a01b9ac3f2632c89b9e3e7f7917c6487596537.png)

## 三
如果有自动化取证工具也是可以的例如：取证大师中的小程序——navicat会话解密。
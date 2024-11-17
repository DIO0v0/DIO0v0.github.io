## 获取密文
一般finalshell的连接记录在`connect_config.json`文件中，例如：
![image](https://github.com/user-attachments/assets/05c9cda5-11b6-456b-a2a2-b95ad838b03f)

## 解密
可以通过代码或编译好的在线网站来解密
1. https://antontwelve.github.io/finalshellPasswordDecrypter
![image](https://github.com/user-attachments/assets/4e65b56e-25a6-4201-ade3-24a10876d54e)
2. 使用下面 Java 程序可以解密。在线 Java 运行工具：https://c.runoob.com/compile/10/
```java
import java.io.ByteArrayOutputStream;
import java.io.DataOutputStream;
import java.io.IOException;
import java.math.BigInteger;
import java.security.MessageDigest;
import java.security.NoSuchAlgorithmException;
import java.security.SecureRandom;
import java.util.Base64;
import java.util.Random;
 
import javax.crypto.Cipher;
import javax.crypto.SecretKey;
import javax.crypto.SecretKeyFactory;
import javax.crypto.spec.DESKeySpec;
 
public class FinalShellDecodePass {
    public static void main(String[] args)throws Exception {
        System.out.println(decodePass("LnxBUXEMESGQ6fcB1PwFtTEkqFavMB6qzeWZhCe4pj0="));
    }
    public static byte[] desDecode(byte[] data, byte[] head) throws Exception {
        SecureRandom sr = new SecureRandom();
        DESKeySpec dks = new DESKeySpec(head);
        SecretKeyFactory keyFactory = SecretKeyFactory.getInstance("DES");
        SecretKey securekey = keyFactory.generateSecret(dks);
        Cipher cipher = Cipher.getInstance("DES");
        cipher.init(2, securekey, sr);
        return cipher.doFinal(data);
    }
    public static String decodePass(String data) throws Exception {
        if (data == null) {
            return null;
        } else {
            String rs = "";
            byte[] buf = Base64.getDecoder().decode(data);
            byte[] head = new byte[8];
            System.arraycopy(buf, 0, head, 0, head.length);
            byte[] d = new byte[buf.length - head.length];
            System.arraycopy(buf, head.length, d, 0, d.length);
            byte[] bt = desDecode(d, ranDomKey(head));
            rs = new String(bt);
 
            return rs;
        }
    }
    static byte[] ranDomKey(byte[] head) {
        long ks = 3680984568597093857L / (long)(new Random((long)head[5])).nextInt(127);
        Random random = new Random(ks);
        int t = head[0];
 
        for(int i = 0; i < t; ++i) {
            random.nextLong();
        }
 
        long n = random.nextLong();
        Random r2 = new Random(n);
        long[] ld = new long[]{(long)head[4], r2.nextLong(), (long)head[7], (long)head[3], r2.nextLong(), (long)head[1], random.nextLong(), (long)head[2]};
        ByteArrayOutputStream bos = new ByteArrayOutputStream();
        DataOutputStream dos = new DataOutputStream(bos);
        long[] var15 = ld;
        int var14 = ld.length;
 
        for(int var13 = 0; var13 < var14; ++var13) {
            long l = var15[var13];
 
            try {
                dos.writeLong(l);
            } catch (IOException var18) {
                var18.printStackTrace();
            }
        }
 
        try {
            dos.close();
        } catch (IOException var17) {
            var17.printStackTrace();
        }
 
        byte[] keyData = bos.toByteArray();
        keyData = md5(keyData);
        return keyData;
    }
    public static byte[] md5(byte[] data) {
        String ret = null;
        byte[] res=null;
 
        try {
            MessageDigest m;
            m = MessageDigest.getInstance("MD5");
            m.update(data, 0, data.length);
            res=m.digest();
            ret = new BigInteger(1, res).toString(16);
        } catch (NoSuchAlgorithmException e) {
            e.printStackTrace();
        }
        return res;
    }
}
```
![image](https://github.com/user-attachments/assets/f2516c99-1a1c-4dc4-9486-a06e295a172b)


---
layout: post
title:  java和php对接通用加解密方法整理
category: PHP 
tags: [PHP]
---

整理记录下，java和php对接，对于数据加解密的方法。

```
import javax.crypto.Cipher;
import javax.crypto.spec.SecretKeySpec;
import java.util.Base64;

/**
 * @author baihe
 */ public class AES {

    /**
 * 加密算法
  *
 * @param sSrc
  * @param sKey
  * @return
  * @throws Exception
 */  public static String Encrypt(String sSrc, String sKey) throws Exception {
        if (sKey == null) {
            System.out.print("Key为空null");
            return null;
        }
        // 判断Key是否为16位
  if (sKey.length() != 16) {
            System.out.print("Key长度不是16位");
            return null;
        }
        byte[] raw = sKey.getBytes("utf-8");
        SecretKeySpec skeySpec = new SecretKeySpec(raw, "AES");
        Cipher cipher = Cipher.getInstance("AES/ECB/PKCS5Padding");//"算法/模式/补码方式"
  cipher.init(Cipher.ENCRYPT_MODE, skeySpec);
        byte[] encrypted = cipher.doFinal(sSrc.getBytes("utf-8"));

        String result = Base64.getEncoder().encodeToString(encrypted);
        return java.net.URLEncoder.encode(result, "UTF-8"); //此处使用BASE64做转码功能，同时能起到2次加密的作用。
  }

    // 解密算法
  public static String Decrypt(String sSrc, String sKey) throws Exception {
        try {
            // 判断Key是否正确
  if (sKey == null) {
                System.out.print("Key为空null");
                return null;
            }
            // 判断Key是否为16位
  if (sKey.length() != 16) {
                System.out.print("Key长度不是16位");
                return null;
            }
            byte[] raw = sKey.getBytes("utf-8");
            SecretKeySpec skeySpec = new SecretKeySpec(raw, "AES");
            Cipher cipher = Cipher.getInstance("AES/ECB/PKCS5Padding");
            cipher.init(Cipher.DECRYPT_MODE, skeySpec);
            try {
                sSrc = java.net.URLDecoder.decode(sSrc, "UTF-8");
                byte[] encrypted1 = Base64.getDecoder().decode(sSrc);//先用base64解密
  byte[] original = cipher.doFinal(encrypted1);
                String originalString = new String(original, "utf-8");
                return originalString;
            } catch (Exception e) {
                System.out.println(e.toString());
                return null;
            }
        } catch (Exception ex) {
            System.out.println(ex.toString());
            return null;
        }
    }

    public static void main(String[] args) throws Exception {
        /*
 * 此处使用AES-128-ECB加密模式，key需要为16位。  */  String cKey = "WeDoct_XiangYang";
        // 需要加密的字串
  String cSrc = "{\"doctorIdcard\":\"420682198102024516\",\"doctorBelongTeamid\":\"84\",\"idcard\":\"110101200003070236\",\"mobile\":\"13910043421\",\"name\":\"\\u5927\\u767d\",\"type\":\"1\",\"relationship\":\"10\",\"relationDesc\":\"\\u5b59\\u5973\",\"liveProvinceid\":\"420000000000\",\"liveCityid\":\"420600000000\",\"liveCountyid\":\"420682000000\",\"liveTownshipid\":\"420682104000\",\"liveVillageid\":\"420682104001\",\"liveDetailAddress\":\"\\u8944\\u9633\\u5e02\",\"crowdType\":\"1\",\"isTuberculosis\":\"0\",\"isHypertension\":\"0\",\"isDiabetes\":\"0\",\"isPsychologica\":\"0\",\"isDestitute\":\"0\",\"isDisabilit\":\"0\",\"isPoverty\":\"0\",\"isFlowPeople\":\"1\",\"isFiveInsured\":\"0\",\"isLowInsured\":\"0\",\"app_id\":\"xysignbaokang\",\"app_secret\":\"3RDfblpUvziDOmSk\"}";

        System.out.println(cSrc);
        // 加密
  String enString = AES.Encrypt(cSrc, cKey);
        System.out.println("加密后的字串是：" + enString);

        // 解密
  String DeString = AES.Decrypt(enString, cKey);
        System.out.println("解密后的字串是：" + DeString);
    }
}
```


PHP加解密方法：


```
public static function WeDecrypt($input, $securityKey) {
  $input = urldecode($input); 
  $input = openssl_decrypt($input, 'AES-128-ECB', $securityKey);    
  if (!$input) {
    return false;
   }  
   return $input; 
 }

public static function WeDoctorEncrypt($input, $securityKey) {
  
  $inputArr = json_decode($input, true);//转为数组
  if (!is_array($inputArr) || empty($inputArr)) {
    return false;
   }    
   $input = json_encode($inputArr, JSON_UNESCAPED_UNICODE);//转为json字符串   
   //进行Aes加密  
   $data = openssl_encrypt($input, 'AES-128-ECB', $securityKey);
    return urlencode($data); 
 }
```

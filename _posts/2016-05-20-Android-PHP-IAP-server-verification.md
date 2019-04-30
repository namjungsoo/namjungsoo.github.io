---
layout: post
title: Android PHP 인앱 구매 영수증 서버 검증 예제
---
이전 [[iOS PHP 인앱 구매 영수증 서버 검증 예제]](/iOS-PHP-IAP-server-verification.html)에서 iOS와 PHP로도 인앱 구매 영수증 서버 검증을 진행했는데 이번에는 Android에도 적용해 보자.  

일단 플레이스토어에 가면 다음과 같이 [서비스 및 API] > [라이센스 및 인앱 결제] 항목에 Base64 인코딩된 RSA 공개키가 있다.  
![스크린샷](/assets/img/play.png)

이것을 .pem형식으로 다음과 같이 저장한다. (./play.pem)  
물론 PHP의 chunk_split함수를 이용해도 된다.  
단, 설명문에 RSA 공개키라고 하더라도 반드시 그냥 PUBLIC KEY라고 입력해야 한다.  

-----BEGIN PUBLIC KEY-----  
한 줄에 64바이트씩 잘라서 입력   
-----END PUBLIC KEY-----  

다음은 안드로이드에서 구매후 $signature와 $data를 보내줘야 한다.  
주의할 사항은 $data는 original json을 그대로 보내면 된다.  
```java
    params.put("signature", info.getSignature());
    params.put("data", info.getOriginalJson());
```
검증 PHP는 다음과 같이 구성한다.
openssl_verify가 1을 리턴하면 검증이 성공한 것이다.

```php
function verify_android($signature) {
    // key 파일을 읽음 
    $public_key_path="./play.pem";
    $public_key = file_get_contents($public_key_path);
    $public_key_id = openssl_get_publickey ($public_key);
    $decoded_signature = base64_decode($signature);
    $data = "";
    if(isset($_POST['data'])) {
        $data = $_POST['data'];        
    }
    $result = (int)openssl_verify($data, $decoded_signature, $public_key_id);
    if($result == 1) {
        return true;
    }
    else {
        return false;
    }    
}
```
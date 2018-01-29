---
layout: post
title: iOS PHP 인앱 구매 영수증 서버 검증 예제
---
iOS side
```objc
//iOS 
-(void)serverVerification:(SKPaymentTransaction*)transaction andRestore:(BOOL)isRestore
{
    NSUserDefaults *userDefault = [NSUserDefaults standardUserDefaults];
    NSString *user_id = [userDefault objectForKey:@"uid"];
    NSString *item_id = transaction.payment.productIdentifier;
    
    // 추가된 order_id(구글과 맞추기 위해서 용어를 변경하였다.)
    NSString *order_id;
    int restore = 0;
    if(isRestore == YES) {
        order_id = transaction.originalTransaction.transactionIdentifier;
        restore = 1;
    }
    else {
        order_id = transaction.transactionIdentifier;
    }

    // Load the receipt from the app bundle.
    NSURL *receiptURL = [[NSBundle mainBundle] appStoreReceiptURL];
    NSData *receipt = [NSData dataWithContentsOfURL:receiptURL];
    
    // Create the JSON object that describes the request
    NSError *error;
    NSDictionary *requestContents = @{
                                      @"receipt-data": [receipt base64EncodedStringWithOptions:0],
                                      @"platform": @"ios",
                                      @"item_id": item_id,
                                      @"order_id": order_id,
                                      @"user_id": user_id,
                                      @"restore": [NSNumber numberWithInt:restore],
                                      @"sandbox": [NSNumber numberWithInt:0]
                                      };
    NSData *requestData = [NSJSONSerialization dataWithJSONObject:requestContents
                                                          options:0
                                                            error:&error];
    
    // Create a POST request with the receipt data.
//    NSURL *storeURL = [NSURL URLWithString:@"https://sandbox.itunes.apple.com/verifyReceipt"];

    //검증하려고 하는 자체 서버 
    NSURL *storeURL = [NSURL URLWithString:[URLManager getPaymentLog]];
    NSMutableURLRequest *storeRequest = [NSMutableURLRequest requestWithURL:storeURL];
    [storeRequest setHTTPMethod:@"POST"];
    [storeRequest setHTTPBody:requestData];
    
    // Make a connection to the iTunes Store on a background queue.
    NSOperationQueue *queue = [[NSOperationQueue alloc] init];
    [NSURLConnection sendAsynchronousRequest:storeRequest queue:queue
        completionHandler:^(NSURLResponse *response, NSData *data, NSError *connectionError) {
            if (connectionError) {
                /* ... Handle error ... */
            } else {
                NSError *error;
                NSString *jsonData = [[NSString alloc]initWithData:data encoding:NSUTF8StringEncoding];

                NSDictionary *jsonResponse = [NSJSONSerialization JSONObjectWithData:[jsonData dataUsingEncoding:NSUTF8StringEncoding] options:NSJSONReadingAllowFragments error:&error];
                if (!jsonResponse) { /* ... Handle error ...*/
                }
                /* ... Send a response back to the device ... */
                NSNumber *result = [jsonResponse objectForKey:@"result"];
                if(result == nil) {
                    NSLog(@"구매 인증 실패");
                    handleSendBuyEvent(-1, 0);// M_E_ERROR = -1
                }
                else {
                    if([result longValue] == 0) {
                        NSLog(@"구매 인증 성공");
                        [self provideContent: transaction.payment.productIdentifier];
                    }
                    else {
                        NSLog(@"구매 인증 실패 %d", [result longValue]);
                        handleSendBuyEvent(-1, 0);// M_E_ERROR = -1
                    }
                }
            }
        }];
    
}
```

PHP side
```php
//PHP
//JSON데이터를 $_POST에 넣어준다 
$postdata = file_get_contents("php://input");
$_POST = json_decode($postdata, true);

function verify($sandbox, $receipt) {
    // Environment sandbox인지 체크
    // 영수증을 애플 서버에 보내자
    $endpoint = "";
    
    // 샌드박스일 경우 
    if($sandbox == 1) {
        $endpoint = 'https://sandbox.itunes.apple.com/verifyReceipt';      
    }
    else {// 실결제일 경우 
        $endpoint = 'https://buy.itunes.apple.com/verifyReceipt'; 
    }
    error_log("endpoint=$endpoint");

    $postData = json_encode(array('receipt-data' => $receipt));  
    
    // curl로 요청하자 
    $ch = curl_init($endpoint);  
    curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);  
    curl_setopt($ch, CURLOPT_FOLLOWLOCATION , true);  
    curl_setopt($ch, CURLOPT_POST, true);  
    curl_setopt($ch, CURLOPT_POSTFIELDS, $postData);
    
    $response = curl_exec($ch);  
    $errno    = curl_errno($ch);  
    $errmsg   = curl_error($ch);  
    curl_close($ch);   

    error_log("response=$response");
    if($errno)
        error_log("errno=$errno");
    if($errmsg)
        error_log("errmsg=$errmsg");
    error_log("response ok");
    
    $data = json_decode($response);
    error_log("data=".print_r($data, true));
    
    if(!is_object($data)) {
        return false;
    }
    
    if(!isset($data->status) || $data->status != 0) {
        return false;
    }
    return true;
}

// 2. 애플 영수증 검증
// 실결제를 먼저하고, 샌드박스 결제를 검증해야 한다.
// 왜냐하면 애플 심사과정에서는 샌드박스로 테스트를 하기 때문이다.
if(strlen($receipt) > 0) {
    if(!verify(0, $receipt)) {// 실결제 테스트
        if(!verify(1, $receipt)) {// 샌드박스 테스트
            // 실결제, 샌드박스 둘다 실패하면 에러다
            $ret = array("result" => 5, "error" => "receipt error");
            echo json_encode($ret);
            exit;
        }
    }
}
// 문제없이 exit가 안되었다면 검증이 성공한 것이다. 
```
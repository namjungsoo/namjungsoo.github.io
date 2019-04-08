---
layout: post
title: BitcoinSV create new transaction
---

이전 글 [BitcoinSV generate new address](/BitcoinSV-generate-new-address.html)에서 BitcoinSV의 새로운 address를 생성하는 방법을 배웠다.
이제는 코인을 전송하는 방법을 배워 보자.

## 신규 address 생성 
일단 이전 글의 소스 코드로 test에서 새로운 address를 2개 생성한다.
![newaddress](./assets/img/newaddress.png)

### Address 0 (from)
private key: cVnpKJPYfTAAtbqahjPsrz6sjuVyp25itkwJ5H4fg7CLS4DGDivY  
address: n4pKQauaywwsooZpqQsBA7moTUMb4qNBqj

### Address 1 (to)
private key: cT1LK7KRoz2TYtNqQpjQFWtrFNTXu6YhAMVTDwKmkkn1y5Kt8Gd5  
address: mqYoezdaPQVihdRSpquCnBG82m8MwF6i3T

## 테스트에 사용할 무료 코인 받기 
그리고 생성된 address 0에 [BitcoinSV Testnet Faucet](https://bitcoincloud.net/faucet) 에서 무료 코인을 받자.  
![faucet1](./assets/img/faucet1.png)
![faucet2](./assets/img/faucet2.png)

faucet은 testnet에서만 제공하는 것인데, address를 입력하면 해당 address로 테스트 할 수 있는 코인을 무료로 송금해 준다.

## 받은 무료 코인의 UXTO 확인 
이제는 코인을 받았으면 받은 코인이 들어있는 UTXO(Unspent Transaction Output)를 찾아보자. BitcoinSV는 코인이 잔고 개념으로 존재하는게 아니라 transaction의 unspent로 존재한다. 

### address 검색
검색은 BitcoinSV의 [Testnet explorer](https://testnet.bitcoincloud.net)에서 나의 address로 검색하면 된다.  
https://testnet.bitcoincloud.net/address/n4pKQauaywwsooZpqQsBA7moTUMb4qNBqj  
![findaddress](./assets/img/findaddress.png)

### tx ID 검색
이때 transaction ID가 표시되는데 transaction ID로 검색해도 똑같이 transaction을 찾을수 있다.
https://testnet.bitcoincloud.net/tx/0c35d13465758ca223eabb15fa6a236a678a2a6f68d88ac667824fd4e64e0024  
![findtx](./assets/img/findtx.png)

transaction ID는 `0c35d13465758ca223eabb15fa6a236a678a2a6f68d88ac667824fd4e64e0024`이다. 

이제 UTXO에 기초하여 transaction을 코드로 구성해 보자.
```js
const bsv = require('bsv')
const Transaction = bsv.Transaction
const Script = bsv.Script

// utxo의 txid
const txId = '0c35d13465758ca223eabb15fa6a236a678a2a6f68d88ac667824fd4e64e0024'

// output은 2개가 있는데 내가 받은 0.43 BSV가 첫번째 이므로 0번이다. 0부터 시작하는 인덱스이다.
const outputIndex = 0
const satoshis = 43000000 // 0.43 BSV

// from address + private key
const fromAddress = 'n4pKQauaywwsooZpqQsBA7moTUMb4qNBqj'
const fromPrivateKey = 'cVnpKJPYfTAAtbqahjPsrz6sjuVyp25itkwJ5H4fg7CLS4DGDivY'

// to address info
// 송금액은 0.3 BSV
// fee는 400 sats
const toAddress = 'mqYoezdaPQVihdRSpquCnBG82m8MwF6i3T'
const amount = 30000000
const fee = 400

const utxos = {
    address: fromAddress,
    txId: txId,
    outputIndex: outputIndex,
    script: Script.buildPublicKeyHashOut(fromAddress).toString(),
    satoshis: satoshis
}

const transaction = new Transaction()
    // 순서를 잘 지켜야 한다.
    // change 이전에 fee를 셋팅해야함 
    .from(utxos) // 사용할 UXTO를 입력한다.
    .to(toAddress, amount) // 받는사람 주소와 사토시 단위의 금액을 입력한다.

    .fee(fee)

    .change(fromAddress) // 잔돈을 받을 주소. 보내는 사람의 주소로 설정한다. 
    .sign(fromPrivateKey) // 보내는 사람의 private key로 sign한다. 
    // 아래와 같이 여러개의 sign을 할수가 있다.
    //.sign
    //.sign... 

const str = transaction.serialize(true)

// 여기서 출력되는 str이 transaction이다. 
console.log(str)
```

콘솔에 출력되는 raw transaction은 다음과 같다.
```
010000000124004ee6d44f8267c68ad8686f2a8a676a236afa15bbea23a28c756534d1350c010000006b483045022100aedcfcfa2a63de2e387bbca842cc0a88f928a0fe8b0c44a965c0a7bed81c360c02205ce661b08a96f9eab145971b3405fcde757e6f005d083c1c8598d48d9b249c72412102a9ea1692b889ab95d425dc71e91eddd9aa8ca618459e9fdb731d8c2ca83152daffffffff0280c3c901000000001976a9146e0ae0d9ba1f9016e1014ad561f7c5aef128114d88acb05bc600000000001976a914ff93969a9e3c1c6f347b106658412919f6829aba88ac00000000
```

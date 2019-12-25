---
layout: post
title: "BitcoinSV #3 Broadcast raw transaction"
---
이전 글 [BitcoinSV &#35;2 Create a raw transaction](/BitcoinSV-create-new-transaction.html)에서 생성한 transaction을 broadcast해보자. Transaction은 bitcoin network에 broadcast를 한 후 miner가 채굴을 해야 실제로 거래가 이루어 진다. 방법은 3가지가 있으며 testnet은 1번 방법만 가능하다.

## 1. bitcoind를 통해서 broadcast하는 방법
궁극적인 방법인데 실제로 bitcoin node를 운영하면 손쉽게 내가 운영하는 node를 통해서 transaction을 broadcast할수 있다.
설치하는 방법은 [BitcoinSV build on macOS](/BitcoinSV-build-on-macOS.html) 이 글을 참고 하자.

bitcoin 데몬을 띄운다.
> bitcoind 

테스트넷에서 작업할 경우는 다음과 같이 한다.
> bitcoind -testnet

이제 bitcoin-cli를 통해서 아까 실행했던 bitcoind로 JSON-RPC 명령을 통해서 sendrawtransaction API를 실행 시키자.
> bitcoin-cli sendrawtransaction (transaction)  
> bitcoin-cli -testnet sendrawtransaction (transaction)

bitcoin-cli를 사용하지 않더라도 직접 내가 프로그래밍 함으로서 내가 운영중인 bitcoind에 JSON-RPC를 통해서도 API 실행이 가능하다.

broadcast 하기 전에 bitcoin-cli는 decoderawtransaction이라는 API를 제공해 주므로, 맞게 생성하였는지를 확인할 수가 있다.
```
bitcoin-cli -testnet decoderawtransaction  010000000124004ee6d44f8267c68ad8686f2a8a676a236afa15bbea23a28c756534d1350c000000006b483045022100c489a8b6c82f01d97ec93a00259fc1597c18c6d0606900f66e4f02b837a90b9d0220326cac301b8603573fe6d79a2f023f8d02bcdb4d939e18050c18ec0cce254331412102a9ea1692b889ab95d425dc71e91eddd9aa8ca618459e9fdb731d8c2ca83152daffffffff0280c3c901000000001976a9146e0ae0d9ba1f9016e1014ad561f7c5aef128114d88acb05bc600000000001976a914ff93969a9e3c1c6f347b106658412919f6829aba88ac00000000
```

JSON으로 결과가 출력된다.
```json
{
  "txid": "4fea825a796be177e99a8d19327441906448e9edaff2259abcd56e3782ecbea0",
  "hash": "4fea825a796be177e99a8d19327441906448e9edaff2259abcd56e3782ecbea0",
  "size": 226,
  "version": 1,
  "locktime": 0,
  "vin": [
    {
      "txid": "0c35d13465758ca223eabb15fa6a236a678a2a6f68d88ac667824fd4e64e0024",
      "vout": 0,
      "scriptSig": {
        "asm": "3045022100c489a8b6c82f01d97ec93a00259fc1597c18c6d0606900f66e4f02b837a90b9d0220326cac301b8603573fe6d79a2f023f8d02bcdb4d939e18050c18ec0cce254331[ALL|FORKID] 02a9ea1692b889ab95d425dc71e91eddd9aa8ca618459e9fdb731d8c2ca83152da",
        "hex": "483045022100c489a8b6c82f01d97ec93a00259fc1597c18c6d0606900f66e4f02b837a90b9d0220326cac301b8603573fe6d79a2f023f8d02bcdb4d939e18050c18ec0cce254331412102a9ea1692b889ab95d425dc71e91eddd9aa8ca618459e9fdb731d8c2ca83152da"
      },
      "sequence": 4294967295
    }
  ],
  "vout": [
    {
      "value": 0.30000000,
      "n": 0,
      "scriptPubKey": {
        "asm": "OP_DUP OP_HASH160 6e0ae0d9ba1f9016e1014ad561f7c5aef128114d OP_EQUALVERIFY OP_CHECKSIG",
        "hex": "76a9146e0ae0d9ba1f9016e1014ad561f7c5aef128114d88ac",
        "reqSigs": 1,
        "type": "pubkeyhash",
        "addresses": [
          "bchtest:qphq4cxehg0eq9hpq99d2c0hckh0z2q3f5kl4v5uzp"
        ]
      }
    },
    {
      "value": 0.12999600,
      "n": 1,
      "scriptPubKey": {
        "asm": "OP_DUP OP_HASH160 ff93969a9e3c1c6f347b106658412919f6829aba OP_EQUALVERIFY OP_CHECKSIG",
        "hex": "76a914ff93969a9e3c1c6f347b106658412919f6829aba88ac",
        "reqSigs": 1,
        "type": "pubkeyhash",
        "addresses": [
          "bchtest:qrle8956nc7pcme50vgxvkzp9yvldq56hggp4t0yq7"
        ]
      }
    }
  ]
}
```

확인이 끝났으면 이제 sendrawtransaction으로 transaction을 broadcast 시키자.
```sh
bitcoin-cli -testnet sendrawtransaction 010000000124004ee6d44f8267c68ad8686f2a8a676a236afa15bbea23a28c756534d1350c000000006b483045022100c489a8b6c82f01d97ec93a00259fc1597c18c6d0606900f66e4f02b837a90b9d0220326cac301b8603573fe6d79a2f023f8d02bcdb4d939e18050c18ec0cce254331412102a9ea1692b889ab95d425dc71e91eddd9aa8ca618459e9fdb731d8c2ca83152daffffffff0280c3c901000000001976a9146e0ae0d9ba1f9016e1014ad561f7c5aef128114d88acb05bc600000000001976a914ff93969a9e3c1c6f347b106658412919f6829aba88ac00000000
```

콘솔에 결과값으로 나오는 것이 transaction hash이다. 
```
4fea825a796be177e99a8d19327441906448e9edaff2259abcd56e3782ecbea0
```
이제 [https://testnet.bitcoincloud.net](https://testnet.bitcoincloud.net/tx/4fea825a796be177e99a8d19327441906448e9edaff2259abcd56e3782ecbea0)에 가서 4fea825a796be177e99a8d19327441906448e9edaff2259abcd56e3782ecbea0으로 검색을 하자.

현재는 unconfirmed 상태이다.
![unconfirmed](/assets/img/unconfirmed.png)

10분정도 기다리면 confirmed가 되며 target 주소에 BSV가 입금이 되어 있을 것이다.
![confirmed](/assets/img/confirmed.png)

## 2. blockchain explorer에서 제공하는 public API를 이용하는 방법
[https://www.bitindex.network](https://www.bitindex.network)를 통해 다음과 같은 화면 같이 raw transaction을 broadcast 시킬수 있다.
![bitindex](/assets/img/bitindex.png)

## 3. blockchain explorer의 웹 UI를 통해서 transaction을 broadcast하는 방법
[https://bchsvexplorer.com/tx/send](https://bchsvexplorer.com/tx/send)를 통해 다음과 같은 화면 같이 raw transaction을 broadcast 시킬수 있다.
![bchsvexplorer](/assets/img/bchsvexplorer.png)

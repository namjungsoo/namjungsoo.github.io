---
layout: post
title: BitcoinSV generate new address
---

BitcoinSV는 기존의 Bitcoin Core의 라이브러리인 [bitcoinjs-lib](https://github.com/bitcoinjs/bitcoinjs-lib)를 사용할수도 있지만, MoneyButton에서 제공한 [bsv](https://github.com/moneybutton/bsv)가 현재로서는 최선의 선택이다.  

다음과 같이 bsv라이브러리를 설치하자.
> npm i -S bsv

그리고 다음과 같이 network에 맞는 코드를 사용해서 address를 생성하자.  
### testnet address 생성 
```js
const bsv = require('bsv')
const privateKey = bsv.PrivateKey.fromRandom('testnet')

// WIF는 저장해야할 private key의 string이다. 
// 콘솔에 출력되는 값을 복사하여 보관하면 된다.
console.log(privateKey.toWIF())

// 마찬가지로 address 문자열을 복사하여 보관하자.
const address = bsv.Address.fromPrivateKey(privateKey, 'testnet')
console.log(address.toString())
```

### mainnet address 생성 
```js
const bsv = require('bsv')
const privateKey = bsv.PrivateKey.fromRandom()

// WIF는 저장해야할 private key의 string이다. 
// 콘솔에 출력되는 값을 복사하여 보관하면 된다.
console.log(privateKey.toWIF())

// 마찬가지로 address 문자열을 복사하여 보관하자.
const address = bsv.Address.fromPrivateKey(privateKey)
console.log(address.toString())
```

testnet과 mainnet의 차이는 단순히 fromRandom, fromPrivateKey를 호출할 때 'testnet'이라는 문자열을 추가적으로 전달함으로서 가능하다.  

mainnet은 private key가 K, L로 시작되고 testnet은 c로 시작되는 점이 차이점이다.  

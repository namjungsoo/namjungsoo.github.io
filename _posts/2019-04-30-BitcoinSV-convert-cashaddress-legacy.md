---
layout: post
title: BitcoinSV convert cash address to legacy format
---
BitDB나 Planaria를 사용하다 보면 address format이 다음과 같이 cash address format으로 되어 있는 경우가 있다.  
![bitquery](/assets/img/bitquery.png)
![blockchair-cashaddr](/assets/img/blockchair-cashaddr.png)

그런데 BSV는 cash address format을 포기하기로 하였고, 더이상 bsv 라이브러리에서도 지원하지 않는다.  
따라서 address 처리를 위해서라면 cash address를 legacy Bitcoin address format으로 변환해야 한다.
```js
const cashaddr = require('cashaddrjs')
const bsv = require('bsv')

...

// 앞에 prefix('bitcoincash:')를 붙여 주어야 한다.
const cash = cashaddr.decode('bitcoincash:' + addr)

// mainnet일 경우 00을 붙여주어야 한다.
const hex = '00' + Buffer.from(cash.hash).toString('hex')
const address = bsv.Address.fromHex(hex)

// lecacy address string이다. 
const addressStr = address.toString()
```

Address prefix의 경우 아래를 참고 하면된다.
https://en.bitcoin.it/wiki/List_of_address_prefixes
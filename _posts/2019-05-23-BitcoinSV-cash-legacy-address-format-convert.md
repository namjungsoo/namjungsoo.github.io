---
layout: post
title: BitcoinSV convert lecacy to cash format &#35;2
---

이전 글 [BitcoinSV convert cash address to legacy format](/BitcoinSV-convert-cashaddress-legacy.html)에서는 cash format을 legacy format으로 변경하는 방법을 배웠다.  
그러나 그 역변환(legacy to cash)도 필요한 순간이 있다.

그럴때는 다음과 같은 라이브러리 소스를 참고하여 필요할때 사용하면 된다.
```js
const cashaddr = require('cashaddrjs')
const bsv = require('bsv')

function legacy2cash(str) {
    let buf = new Uint8Array(str.length * 2)
    let buf2 = new Uint16Array(buf)

    for (let i = 0; i < str.length; i++) {
        buf2[i] = str.charCodeAt(i)
    }

    let addr = bsv.Address.fromString(str)
    let uint8 = new Uint8Array(addr.toBuffer())
    uint8 = uint8.slice(1) // 제일 왼쪽의 00을 제거한다.

    const cash = cashaddr.encode("bitcoincash", "P2PKH", uint8)
    return cash
}

function cash2legacy(addr) {
    const {
        prefix,
        type,
        hash
    } = cashaddr.decode(addr)

    const hex = '00' + Buffer.from(hash).toString('hex')
    const address = bsv.Address.fromHex(hex)
    return address.toString()
}

if (require.main === module) {
    let cash = legacy2cash('1LEDCd4LoQkkJK8TrnhSsTMc79VV3BniVu')
    console.log(cash)
    console.log(cash2legacy(cash))
}

module.exports = {
    legacy2cash,
    cash2legacy
}
```
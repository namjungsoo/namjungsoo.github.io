---
layout: post
title: BitcoinSV bitsocket example
---
아래는 Bitcoin SV의 실시간 event를 받을수 있는 bitsocket의 example이다. 
Node.js로 작성했으며, 다음과 같은 패키지를 npm으로 설치해야 한다.
>npm i -S btoa  
>npm i -S eventsource

```js
var btoa = require('btoa')
var EventSource = require('eventsource')

// Write a bitquery
var query = {
  "v": 3, "q": { "find": {} }
}

// Encode it in base64 format
var b64 = btoa(JSON.stringify(query))

// Subscribe
var bitsocket = new EventSource('https://genesis.bitdb.network/s/1FnauZ9aUH2Bex6JzdcV4eNX7oLSSEbxtN/'+b64)

// Event handler
bitsocket.onmessage = function(e) {
  console.log(e.data)
}
```

호출결과는 다음과 같으며 실시간으로 계속 메세지가 추가된다.
```json
{"type":"open","data":[]}
{
    "type": "u",
    "data": [{
        "tx": {
            "h": "0c07b01307b14e3935bcdbaf50770c0e4a4e23aeb9c09c49550e09a5ad570652"
        },
        "in": [{
            "i": 0,
            "b0": "MEUCIQD6VwsndQUrrpmFCJPlxuAF1fUmZcPAx5UgC803Som2cQIgZuwlNDM3Puu/3wqqzTd5cFbUO8fEX4Q5Y9jCBdKMoDtB",
            "b1": "A8ydsZIv8Hew5jLQLoKpxqunqCx2xT18H1aKd3XIlURe",
            "str": "3045022100fa570b2775052bae99850893e5c6e005d5f52665c3c0c795200bcd374a89b671022066ec253433373eebbfdf0aaacd37797056d43bc7c45f843963d8c205d28ca03b41 03cc9db1922ff077b0e632d02e82a9c6aba7a82c76c53d7c1f568a7775c895445e",
            "e": {
                "h": "07225863412d4d037cb5514ccf94a29583691367218f28eced772c0ef1e9a866",
                "i": 1,
                "a": "1EomR6yvfRUCUeR9RdiLE8AUkveW6wA2uz"
            },
            "h0": "3045022100fa570b2775052bae99850893e5c6e005d5f52665c3c0c795200bcd374a89b671022066ec253433373eebbfdf0aaacd37797056d43bc7c45f843963d8c205d28ca03b41",
            "h1": "03cc9db1922ff077b0e632d02e82a9c6aba7a82c76c53d7c1f568a7775c895445e"
        }],
        "out": [{
            "i": 0,
            "b0": {
                "op": 118
            },
            "b1": {
                "op": 169
            },
            "b2": "4QBR3rSIWSQI33JyQGzqWd/mEcY=",
            "s2": "�\u0000Q޴�Y$\b�rr@l�Y��\u0011�",
            "b3": {
                "op": 136
            },
            "b4": {
                "op": 172
            },
            "e": {
                "v": 1000,
                "i": 0,
                "a": "1MWhRPAtAdXFSTgZrkrRuDy5xsofz63nHD"
            },
            "h2": "e10051deb488592408df7272406cea59dfe611c6"
        }, {
            "i": 1,
            "b0": {
                "op": 118
            },
            "b1": {
                "op": 169
            },
            "b2": "l3HYfq/xcSUVBLXAClhqHDwqGj4=",
            "s2": "�q�~��q%\u0015\u0004��\nXj\u001c<*\u001a>",
            "b3": {
                "op": 136
            },
            "b4": {
                "op": 172
            },
            "e": {
                "v": 6695136,
                "i": 1,
                "a": "1EomR6yvfRUCUeR9RdiLE8AUkveW6wA2uz"
            },
            "h2": "9771d87eaff171251504b5c00a586a1c3c2a1a3e"
        }],
        "_id": "5cbe972233dedb166dc469c8"
    }]
}
```
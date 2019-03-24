---
layout: post
title: Ethereum web3.js에서 localhost 접속하기
---
### 개요
이더리움에서 web3.js를 통해서 network에 접속하는 API를 제공받게 되는데 현재 버그가 존재한다.

```javascript
web3.setProvider('http://localhost:8545')
```
### 현상
다음과 같은 에러메세지를 볼수 있다.  
not connected to provider

다음과 같은 곳에서 자료를 찾을수 있다.
<https://github.com/ethereum/web3.js/issues/1051>

### 해결 방법
다음과 같이 package.json을 만들어서 local module로 관리한다.
npm install web3@0.20.0 --save  

```javascript
{
    "dependencies": {
        "request": "^2.83.0",
        "web3": "0.19.0"
    }
}
```

### 문제의 원인
npm으로 web3를 버전 없이 설치하였을 경우 web3@1.0.0-beta.x가 설치되는데 이 베타버전에서 localhost로 setProvider를 수행하는 과정에서 버그가 있었기 때문이다.  


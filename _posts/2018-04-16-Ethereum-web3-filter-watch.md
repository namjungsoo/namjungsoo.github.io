---
layout: post
title: Ethereum web3 filter로 transaction watch하기 
---
이더리움에서 일반 이더를 송금할 때는 이벤트를 받을수가 없다.  

따라서 다음과 같이 filter를 걸어서 block안의 transactions에 우리가 원하는 transactionHash가 포함되었는지를 확인함으로서 송금이 완료되었는지를 확인할수 있다.  

아래의 소스코드는 node.js로 작성하였다.

```javascript
// 이더리움 web3에 접속 
var EthConn = require('../contracts/ethconn.js');
var host = 'http://localhost:8545'
var ethconn = new EthConn(host);
console.log(ethconn.web3.eth.accounts);

var web3 = ethconn.web3;
var eth = web3.eth;
var personal = web3.personal;
var accounts = eth.accounts;

// 0번을 unlock
personal.unlockAccount(accounts[0], '1');
var txhash = eth.sendTransaction({
    from: accounts[0],
    to: accounts[1],
    value: web3.toWei(1, 'ether')
});
console.log(`txhash=${txhash}`);

var filterString = 'latest';
var filter = eth.filter('latest');

filter.watch((err, res) => {
    if (!err) {
        console.log(`res ${res}`);
        var block = eth.getBlock(res);
        console.log(`block ${JSON.stringify(block)}`);
        if (block.transactions.includes(txhash)) {
            filter.stopWatching();
            console.log(`${txhash} completed`);
            var receipt = eth.getTransactionReceipt(txhash);
            console.log(receipt);
        }
    } else {
        console.log(`err ${err}`);
    }
});
console.log('filter watch');
```

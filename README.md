potcoind-rpc.js
===============

[![NPM Package](https://img.shields.io/npm/v/potcoind-rpc.svg?style=flat-square)](https://www.npmjs.org/package/potcoind-rpc)
[![Build Status](https://img.shields.io/travis/potcoin-dev/potcoind-rpc.svg?branch=master&style=flat-square)](https://travis-ci.org/potcoin-dev/potcoind-rpc)
[![Coverage Status](https://img.shields.io/coveralls/potcoin-dev/potcoind-rpc.svg?style=flat-square)](https://coveralls.io/r/potcoin-dev/potcoind-rpc?branch=master)

A client library to connect to Potcoin Core RPC in JavaScript.

## Get Started

potcoind-rpc.js runs on [node](http://nodejs.org/), and can be installed via [npm](https://npmjs.org/):

```bash
npm install potcoind-rpc
```

## Examples

```javascript
var run = function() {
  var potcore = require('potcore');
  var RpcClient = require('potcoind-rpc');

  var config = {
    protocol: 'http',
    user: 'user',
    pass: 'pass',
    host: '127.0.0.1',
    port: '18332',
  };

  var rpc = new RpcClient(config);

  var txids = [];

  function showNewTransactions() {
    rpc.getRawMemPool(function (err, ret) {
      if (err) {
        console.error(err);
        return setTimeout(showNewTransactions, 10000);
      }

      function batchCall() {
        ret.result.forEach(function (txid) {
          if (txids.indexOf(txid) === -1) {
            rpc.getRawTransaction(txid);
          }
        });
      }

      rpc.batch(batchCall, function(err, rawtxs) {
        if (err) {
          console.error(err);
          return setTimeout(showNewTransactions, 10000);
        }

        rawtxs.map(function (rawtx) {
          var tx = new potcore.Transaction(rawtx.result);
          console.log('\n\n\n' + tx.id + ':', tx.toObject());
        });

        txids = ret.result;
        setTimeout(showNewTransactions, 2500);
      });
    });
  }

  showNewTransactions();
};
```

## License

**Code released under [the MIT license](https://github.com/potcoin-dev/potcore/blob/master/LICENSE).**

Copyright 2013-2014 PotPay, Inc.

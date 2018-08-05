# stim-node-api

stim-node-api is an Express middleware plugin that easily exposes a URL structure for interfacing with a stimd Stim wallet.

NB: The middleware is experimental at present. Certain JSON-RPC methods are not supported yet and/or experimental. These are methods with more complex parameters that do not fit easily into a query string:

- addmultisigaddress
- createmultisig
- createrawtransaction
- getaddednodeinfo
- lockunspent
- sendmany
- signrawtransaction
- submitblock

These methods may be added in the future. If there any other problems with the other methods, please report the bugs.

## Install

```javascript
npm install stim-node-api
```

## How to use

### Node.js

```javascript
var stimapi = require('stim-node-api');
var express = require('express');
var app = express();

//Username and password relate to those set in the stim.conf file

var wallet = {
  host: 'localhost',
  port: 8091,
  user: 'rpcuser',
  pass: 'rpcpassword'
};

stimapi.setWalletDetails(wallet);
stimapi.setAccess('default-safe'); //Access control
app.use('/stim/api', stimapi.app); //Bind the middleware to any chosen url

app.listen(3000);
```

### Client/Browser

Just add the method name after the binded url.

* http://localhost:3000/URL/METHOD

For example:

* http://localhost:3000/stim/api/getinfo

This returns data exactly as would be expected from the JSON-RPC api.

```javascript
{
  "version": 1000003,
  "protocolversion": 70208,
  "walletversion": 61000,
  "balance": 148.97728640,
  "privatesend_balance": 147.99147990,
  "blocks": 41429,
  "timeoffset": 0,
  "connections": 24,
  "proxy": "",
  "difficulty": 34.784777202308973,
  "testnet": false,
  "keypoololdest": 1530241288,
  "keypoolsize": 1001,
  "paytxfee": 0.00000000,
  "relayfee": 0.00010000,
  "errors": ""
}
```

Parameters are sent via a query string:

* http://localhost:3000/stim/api/gettransaction?txid=d6c7e35ff9c9623208c22ee37a118ad523ae6c2d137d10053739cb03dbac62e0

```javascript
{
  "amount": 0.002,
  "confirmations": 1321,
  "blockhash": "000000000000009d0a2bb76c81dd185d1f6c28256037baef7b3345b7a7e958c7",
  "blockindex": 150,
  "blocktime": 1372728756,
  "txid": "d6c7e35ff9c9623208c22ee37a118ad523ae6c2d137d10053739cb03dbac62e0",
  "time": 1372728436,
  "timereceived": 1372728436,
  "details": [
    {
      "account": "localtest",
      "address": "nHtev475XRVYfenku7ZWXGPSun15ESynCq",
      "category": "receive",
      "amount": 0.002
    }
  ]
}
```

## Access Control

### .setWalletPassphrase(passphrase);

If you have encrypted your wallet.dat you need to set the passphrase before attaching the middleware.
```javascript
stimapi.setWalletDetails(wallet);
stimapi.setWalletPassphrase(passphrase);
app.use('/stim/api', stimapi.app);
```

### .setAccces(type, accesslist);

The .setAccess method controls the access to the urls. By default all commands are accessible. The method takes two parameters: type (string) and accesslist (array). To restrict access there are two ways to do this:

#### 'only'

The 'only' type only exposes the methods given by an array of methods as the accesslist parameter.

```javascript
//Only allow the getinfo method
stimapi.setAccess('only', ['getinfo']);
```

#### 'restrict'

The 'restrict' type prevents methods from being accessed.

```javascript
stimapi.setAccess('restrict', ['dumpprivkey', 'sendmany']);
```

### Access Profiles

stim-node-api has predefined access profiles to make it easy to set up.

#### 'default-safe'

It prevents 'dumpprivkey' and 'walletpassphrasechange' being accessed. This prevents potential theft. Also removes the 'stop' command to prevent someone from stopping the server.

```javascript
stimapi.setAccess('default-safe');
```

#### 'read-only'

This profile only exposes methods that show information. No methods that can send/alter the wallet are exposed.

```javascript
stimapi.setAccess('read-only');
```


# Licence

Copyright (C) 2018 The Stim Community

Permission is hereby granted, free of charge, to any person obtaining a copy of this software and associated documentation files (the "Software"), to deal in the Software without restriction, including without limitation the rights to use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of the Software, and to permit persons to whom the Software is furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.

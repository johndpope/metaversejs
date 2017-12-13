<p align="center">
  <a href="https://mvs.org/">
    <img src="https://mvs.org/images/metaverselogo.png" alt="">
  </a>
  <br>
  <a href="https://travis-ci.org/canguruhh/metaversejs">
     <img src="https://travis-ci.org/canguruhh/metaversejs.png?branch=master" alt="Build status">
  </a>
  <br>
  A javascript library for the Metaverse blockchain
</p>

## Installation
Install using npm:
``` bash
npm install metaversejs
```

## Setup
### NodeJS
``` javascript
let Metaverse = require('metaversejs');
```
<a href="https://nodei.co/npm/metaversejs/"><img src="https://nodei.co/npm/metaversejs.png?downloads=true&downloadRank=true&stars=true"></a>
### Browser
For use in webapps the npm package contains a dist/metaverse.min.js. You can generate this file from source using grunt.

## Usage

Please also check the examples folder.

### Wallet generation
``` javascript
let Metaverse = require('metaversejs')

let number_of_addresses = 10

Metaverse.wallet.generateMnemonic()
  .then( (mnemonic) => Metaverse.wallet.fromMnemonic(mnemonic) )
  .then( (wallet)   => {
      let addresses=[]
      for (let i=0; i<number_of_addresses; i++) {
          addresses.push(wallet.getAddress(i))
      } 
      return { "mnemonic": mnemonic, "addresses": addresses, "wallet": wallet }
  })
  .then(console.log)
```
This will generate a new random wallet and return the mnemonic words as well as the first 10 addresses of the wallet.

### Transaction creation
As Metaverse is UTXO based you need to specify the inputs and outputs of the transaction. The following example creates a basic P2PKH (pay to public key hash) transaction.
``` javascript
//Transaction object
var tx = new Metaverse.transaction()
//Add inputs
tx.addInput("MKXYH2MhpvA3GU7kMk8y3SoywGnyHEj5SB","5554b27dbf657d008511df56e747ffb2173749fd933b03317cee3c1fde271aea",1)
//Add outputs
tx.addOutput("MVpxH8aAa3BAXvbdqUUJwEP6s2ajGKKtyd","ETP",1)
tx.addOutput("MKXYH2MhpvA3GU7kMk8y3SoywGnyHEj5SB","ETP",4939995)
console.log('transaction details: ' + tx)
//Sign the inputs
wallet.sign(tx)
    .then( (stx) => {
        console.log('signed transaction: ' + stx)
        //Encode (serialize) the transaction
        return stx.encode()
    })
    .then( (raw_tx) => {
        console.log('Encoded transaction: ' + raw_tx)
    })
```
You can also add other assets to the transaction. The resulting raw transaction can be broadcasted to any blockchain nodes. It is not possible to change the transaction without invalidating the signatures.

### Deposit
In order to create a deposit and get interest you just have to add a lock output to a transaction. Possible values for the locktime are: 25200, 108000, 331200, 655200 and 1314000 blocks.
``` javascript
//Transaction object
var tx = new Metaverse.transaction()
//Add inputs
tx.addInput("MKXYH2MhpvA3GU7kMk8y3SoywGnyHEj5SB","5554b27dbf657d008511df56e747ffb2173749fd933b03317cee3c1fde271aea",1)
//Add outputs
tx.addLockOutput("MVpxH8aAa3BAXvbdqUUJwEP6s2ajGKKtyd",1, 25200) //Lock for 25200 blocks
tx.addOutput("MKXYH2MhpvA3GU7kMk8y3SoywGnyHEj5SB","ETP",4939995)
console.log('transaction details: ' + tx)
//Sign the inputs
wallet.sign(tx)
    .then( (stx) => {
        console.log('signed transaction: ' + stx)
        //Encode (serialize) the transaction
        return stx.encode()
    })
    .then( (raw_tx) => {
        console.log('Encoded transaction: ' + raw_tx)
    })
```
The reward transaction will be generated by the miner of the block that will contain the transaction.

### Issue asset
In order to issue a new asset add an asset issue output to a transaction. Please make sure that the asset symbol is unique. Precision must be in range 0..19 and the maximum supply is must be provided as a quantity of the smallest unit. An asset with 1000 tokens that should have a precision of 2 decimals must be provided as 1000000 and precision 2. The transaction fee must be 10 ETP.
``` javascript
//Transaction object
var tx = new Metaverse.transaction()
//Add inputs
tx.addInput("tFKkLFZ5B29CkMLkYcAd85JxZ8v2joXFDJ","01a37d6434dbc7b8bc61665ad5498096a57150a14118a786cccf1ac165a5c6ab", 0);
//Add outputs
tx.addAssetIssueOutput("SUN.SHINE", 1000000000,4,"coinmaster", "tBcdLMaqR1D3mhyBL7CEWwLPR8yRnPkHBd", "Let it shine");
//Set the change
tx.addOutput("tFKkLFZ5B29CkMLkYcAd85JxZ8v2joXFDJ", "ETP", 22699990000);
```
The asset will be issued to the address defined in the asset issue output.

### Attach message
To attach a message to any transaction there is a function called addMessage.
``` javascript
//Transaction object
var tx = new Metaverse.transaction();
//Add inputs
tx.addInput("tK8UKnBKhk4NQYhSeSb2zeWgMSZaHsn1TY", "15a3a80a867315ee1d3f1ff67e7d5cd0709b1a1d4a48a938f33b01ec8f47425f", 0);
//Add message
tx.addMessage("tK8UKnBKhk4NQYhSeSb2zeWgMSZaHsn1TY", "hi. this is so easy!");
//Don't forget the change
tx.addOutput("tK8UKnBKhk4NQYhSeSb2zeWgMSZaHsn1TY", "ETP", 299990000);
```

### Decode transaction
You can also easily decode a hex encoded transaction.
``` javascript
Metaverse.transaction.decode(rawtx)
```
Not all information about the previous outputs of the inputs are enoded in a raw transaction. If you want to encode the transaction again you have to add the missing values to the inputs.

## Testing
To run the unit tests just execute:
``` bash
npm test
```




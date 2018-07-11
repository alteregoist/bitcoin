## Bitcoin

#### tx

*tx* describes a bitcoin transaction, in reply to *getdata*

| Field Size | Description  | Data type           | Comments                                                     |
| ---------- | ------------ | ------------------- | ------------------------------------------------------------ |
| 4          | version      | int32_t             | Transaction data format version (note, this is signed)       |
| 0 or 2     | flag         | optional uint8_t[2] | If present, always 0001, and indicates the presence of witness data |
| 1+         | tx_in count  | var_int             | Number of Transaction inputs (never zero)                    |
| 41+        | tx_in        | tx_in[]             | A list of 1 or more transaction inputs or sources for coins  |
| 1+         | tx_out count | var_int             | Number of Transaction outputs                                |
| 9+         | tx_out       | tx_out[]            | A list of 1 or more transaction outputs or destinations for coins |
| 0+         | tx_witnesses | tx_witness[]        | A list of witnesses, one for each input; omitted if *flag* is omitted above |
| 4          | lock_time    | uint32_t            | The block number or timestamp at which this transaction is unlocked:ValueDescription0Not locked< 500000000Block number at which this transaction is unlocked>= 500000000UNIX timestamp at which this transaction is unlockedIf all TxIn inputs have final (0xffffffff) sequence numbers then lock_time is irrelevant. Otherwise, the transaction may not be added to a block until after lock_time (see [NLockTime](https://en.bitcoin.it/wiki/NLockTime)). |

TxIn consists of the following fields:

| Field Size | Description      | Data type | Comments                                                     |
| ---------- | ---------------- | --------- | ------------------------------------------------------------ |
| 36         | previous_output  | outpoint  | The previous output transaction reference, as an OutPoint structure |
| 1+         | script length    | var_int   | The length of the signature script                           |
| ?          | signature script | uchar[]   | Computational Script for confirming transaction authorization |
| 4          | sequence         | uint32_t  | Transaction version as defined by the sender. Intended for "replacement" of transactions when information is updated before inclusion into a block. |

The OutPoint structure consists of the following fields:

| Field Size | Description | Data type | Comments                                                     |
| ---------- | ----------- | --------- | ------------------------------------------------------------ |
| 32         | hash        | char[32]  | The hash of the referenced transaction.                      |
| 4          | index       | uint32_t  | The index of the specific output in the transaction. The first output is 0, etc. |

The Script structure consists of a series of pieces of information and operations related to the value of the transaction.

(Structure to be expanded in the future… see script.h and script.cpp and [Script](https://en.bitcoin.it/wiki/Script) for more information)

The TxOut structure consists of the following fields:

| Field Size | Description      | Data type | Comments                                                     |
| ---------- | ---------------- | --------- | ------------------------------------------------------------ |
| 8          | value            | int64_t   | Transaction Value                                            |
| 1+         | pk_script length | var_int   | Length of the pk_script                                      |
| ?          | pk_script        | uchar[]   | Usually contains the public key as a Bitcoin script setting up conditions to claim this output. |

The TxWitness structure consists of a var_int count of witness data components, followed by (for each witness data component) a var_int length of the component and the raw component data itself.

Example *tx* message:

```
000000	F9 BE B4 D9 74 78 00 00  00 00 00 00 00 00 00 00   ....tx..........
000010	02 01 00 00 E2 93 CD BE  01 00 00 00 01 6D BD DB   .............m..
000020	08 5B 1D 8A F7 51 84 F0  BC 01 FA D5 8D 12 66 E9   .[...Q........f.
000030	B6 3B 50 88 19 90 E4 B4  0D 6A EE 36 29 00 00 00   .;P......j.6)...
000040	00 8B 48 30 45 02 21 00  F3 58 1E 19 72 AE 8A C7   ..H0E.!..X..r...
000050	C7 36 7A 7A 25 3B C1 13  52 23 AD B9 A4 68 BB 3A   .6zz%;..R#...h.:
000060	59 23 3F 45 BC 57 83 80  02 20 59 AF 01 CA 17 D0   Y#?E.W... Y.....
000070	0E 41 83 7A 1D 58 E9 7A  A3 1B AE 58 4E DE C2 8D   .A.z.X.z...XN...
000080	35 BD 96 92 36 90 91 3B  AE 9A 01 41 04 9C 02 BF   5...6..;...A....
000090	C9 7E F2 36 CE 6D 8F E5  D9 40 13 C7 21 E9 15 98   .~.6.m...@..!...
0000A0	2A CD 2B 12 B6 5D 9B 7D  59 E2 0A 84 20 05 F8 FC   *.+..].}Y... ...
0000B0	4E 02 53 2E 87 3D 37 B9  6F 09 D6 D4 51 1A DA 8F   N.S..=7.o...Q...
0000C0	14 04 2F 46 61 4A 4C 70  C0 F1 4B EF F5 FF FF FF   ../FaJLp..K.....
0000D0	FF 02 40 4B 4C 00 00 00  00 00 19 76 A9 14 1A A0   ..@KL......v....
0000E0	CD 1C BE A6 E7 45 8A 7A  BA D5 12 A9 D9 EA 1A FB   .....E.z........
0000F0	22 5E 88 AC 80 FA E9 C7  00 00 00 00 19 76 A9 14   "^...........v..
000100	0E AB 5B EA 43 6A 04 84  CF AB 12 48 5E FD A0 B7   ..[.Cj.....H^...
000110	8B 4E CC 52 88 AC 00 00  00 00                     .N.R......


Message header:
 F9 BE B4 D9                                       - main network magic bytes
 74 78 00 00 00 00 00 00 00 00 00 00               - "tx" command
 02 01 00 00                                       - payload is 258 bytes long
 E2 93 CD BE                                       - checksum of payload

Transaction:
 01 00 00 00                                       - version

Inputs:
 01                                                - number of transaction inputs

Input 1:
 6D BD DB 08 5B 1D 8A F7  51 84 F0 BC 01 FA D5 8D  - previous output (outpoint)
 12 66 E9 B6 3B 50 88 19  90 E4 B4 0D 6A EE 36 29
 00 00 00 00

 8B                                                - script is 139 bytes long

 48 30 45 02 21 00 F3 58  1E 19 72 AE 8A C7 C7 36  - signature script (scriptSig)
 7A 7A 25 3B C1 13 52 23  AD B9 A4 68 BB 3A 59 23
 3F 45 BC 57 83 80 02 20  59 AF 01 CA 17 D0 0E 41
 83 7A 1D 58 E9 7A A3 1B  AE 58 4E DE C2 8D 35 BD
 96 92 36 90 91 3B AE 9A  01 41 04 9C 02 BF C9 7E
 F2 36 CE 6D 8F E5 D9 40  13 C7 21 E9 15 98 2A CD
 2B 12 B6 5D 9B 7D 59 E2  0A 84 20 05 F8 FC 4E 02
 53 2E 87 3D 37 B9 6F 09  D6 D4 51 1A DA 8F 14 04
 2F 46 61 4A 4C 70 C0 F1  4B EF F5

 FF FF FF FF                                       - sequence

Outputs:
 02                                                - 2 Output Transactions

Output 1:
 40 4B 4C 00 00 00 00 00                           - 0.05 BTC (5000000)
 19                                                - pk_script is 25 bytes long

 76 A9 14 1A A0 CD 1C BE  A6 E7 45 8A 7A BA D5 12  - pk_script
 A9 D9 EA 1A FB 22 5E 88  AC

Output 2:
 80 FA E9 C7 00 00 00 00                           - 33.54 BTC (3354000000)
 19                                                - pk_script is 25 bytes long

 76 A9 14 0E AB 5B EA 43  6A 04 84 CF AB 12 48 5E  - pk_script
 FD A0 B7 8B 4E CC 52 88  AC

Locktime:
 00 00 00 00                                       - lock time
```

![img](https://letstalkpayments.com/wp-content/uploads/2016/06/1-22.png)



![Blockchain â Bitcoin as a Mainstream Case Study](https://s3-us-west-2.amazonaws.com/go-medici/uploads/2016/06/2-2.png)



## ethereum

![img](https://steemitimages.com/0x0/https://steemitimages.com/DQmXQMW2iUTs87EzRRRKcKyozFhkM5kBKTGKWvj9xyaYRGc/%EC%9D%B4%EB%8D%94%20%ED%8A%B8%EB%9E%9C%EC%9E%AD%EC%85%98.jpg)

[https://medium.com/@codetractio/inside-an-ethereum-transaction-fa94ffca912f](https://medium.com/@codetractio/inside-an-ethereum-transaction-fa94ffca912f)

![img](https://cdn-images-1.medium.com/max/1600/1*1MvHES_ElVJfTTK07o3hsg.png)

Next we take a look at a transaction, there are 6 input fields

`nonce` the count of the number of outgoing transactions, starting with 0 
`gasPrice` the price to determine the amount of ether the transaction will cost
`gasLimit` the maximum gas that is allowed to be spent to process the transaction
`to` the account the transaction is sent to, if empty, the transaction will create a contract
`value` the amount of ether to send
`data` could be an arbitrary message or function call to a contract or code to create a contract



```javascript
var rawTx = {
    nonce: web3.toHex(0),
    gasPrice: web3.toHex(20000000000),
    gasLimit: web3.toHex(100000),
    to: '0x687422eEA2cB73B5d3e242bA5456b782919AFc85',
    value: web3.toHex(1000),
    data: '0xc0de'
};
```

![img](https://cdn-images-1.medium.com/max/1600/1*4Ta0bUfCEmgH4kOrNl8mKg.png)



![img](https://cdn-images-1.medium.com/max/1600/1*6x3AKjKsjCaOFncemcWvUg.png)



https://www.coindesk.com/information/how-ethereum-works/

#### The ethereum blockchain

The structure of the ethereum blockchain is very similar to bitcoin's, in that it is a shared record of the entire transaction history. Every node on the network stores a copy of this history.

The big difference with ethereum is that its nodes store the most recent state of each smart contract, in addition to all of the ether transactions. (This is much more complicated than described, but the text below should help you get your feet wet.)

For each ethereum application, the network needs to keep track of the 'state', or the current information of all of these applications, including each user's balance, all the smart contract code and where it's all stored.

Bitcoin uses unspent transaction outputs to track who has how much bitcoin.

While it sounds more complex, the idea is fairly simple. Every time a bitcoin transaction is made, the network 'breaks' the total amount as if it was paper money, issuing back bitcoins in a way that makes the data behave similarly to physical coins or change.

To make future transactions, the bitcoin network must add up all your pieces of change, which are classed as either 'spent' or 'unspent'.

Ethereum, on the other hand, uses accounts.

Like bank account funds, ether tokens appear in a wallet, and can be ported (so to speak) to another account. Funds are always somewhere, yet don't have what you might call a continued relationship.

![img](https://media.coindesk.com/uploads/2017/03/state-3-15-01.png)



## OmiseGo

[https://steemkr.com/kr-dev/@nida-io/2oduk2-erc20-1](https://steemkr.com/kr-dev/@nida-io/2oduk2-erc20-1)

ERC20

## Bitcoin Cash

bitcoinwise

## XRP

https://ripple.com/kr/build/transactions/

https://developers.ripple.com/rippleapi-reference.html#transaction-overview

http://blog.hellworld.me/17?category=654058

https://github.com/ripple/ripple-keypairs/blob/master/src/index.js

```javascript
const secp256k1 = {
  ...
  sign: function(message, privateKey) {
    return bytesToHex(Secp256k1.sign(hash(message),
      hexToBytes(privateKey), {canonical: true}).toDER())
  },
  ...
}

const ed25519 = {
  ...
  sign: function(message, privateKey) {
    // caution: Ed25519.sign interprets all strings as hex, stripping
    // any non-hex characters without warning
    assert(Array.isArray(message), 'message must be array of octets')
    return bytesToHex(Ed25519.sign(
      message, hexToBytes(privateKey).slice(1)).toBytes())
  },
  ...
}
```



### 8. 다른 지갑에 돈을 보내보자

`#6`에서 적었던 내용을 이제 진짜로 해보도록 합시다.

#### 8.1. payment 생성하기(prepare)

`preparePayment(address: string, payment: Object, instructions: Object): Promise<Object>`
payment 트랜잭션을 생성해보도록 합시다. preparePayment API를 사용하면 됩니다. 예시와 함께 보도록 합시다.

```
let transcation = await api.preparePayment('rfct6fDdS9mjERh7n3byyyo9ysLDxjaAUm', {
  "source": {
    "address": "rfct6fDdS9mjERh7n3byyyo9ysLDxjaAUm",  //출발지 주소
    //"tag" : unsigned 32bit integer,
    "maxAmount": {
      "value": "1",  //몇 개 보낼거임?
      "currency": "XRP" //돈의 단위, XRP인 경우에는 counterparty 필드를 생략해도 됩니다.
    }
  },
  "destination": {
    "address": "rsG1sNifXJxGS2nDQ9zHyoe1S5APrtwpjV",  //도착지 주소(다시 빗썸으로....)
    "tag" : 1000763131, //도착지의 destination tag 입니다. 제 거입니다. 기부해주시면 잘 쓰겠습니다 ㅎㅎ
    "amount": {
      "value": "1",
      "currency": "XRP"
    }
  }
});
```

결과는 다음과 같이 나옵니다

```
{ txJSON: '{"TransactionType":"Payment","Account":"rfct6fDdS9mjERh7n3byyyo9ysLDxjaAUm","Destination":"rsG1sNifXJxGS2nDQ9zHyoe1S5APrtwpjV","Amount":"1000000","Flags":2147483648,"DestinationTag":1000763131,"LastLedgerSequence":35965715,"Fee":"12","Sequence":2}',
  instructions: { fee: '0.000012', sequence: 2, maxLedgerVersion: 35965715 } }
```

```
{ txJSON: '{"TransactionType":"Payment","Account":"rfct6fDdS9mjERh7n3byyyo9ysLDxjaAUm","Destination":"rsG1sNifXJxGS2nDQ9zHyoe1S5APrtwpjV","Amount":"1000000","Flags":2147483648,"DestinationTag":1000763131,"LastLedgerSequence":35965723,"Fee":"12","Sequence":2}',
  instructions: { fee: '0.000012', sequence: 2, maxLedgerVersion: 35965723 } }
```

txJSON은 해당 트랜잭션의 JSON 텍스트입니다. insturctions는 수수료, 시퀀스, 레저 버전이 나옵니다. sequence, maxLedgerVersion은 사인, 제출에 참조할 가능성이 높습니다. 함수의 3번째 파라미터는 instructions인데 오프라인에서 함수를 사용할 경우 직접 명시해줘야 한다고 합니다.

그리고 제가 일부러 2번 넣었습니다. txJSON의 `LastLedgerSequence`하고 instructions의 `maxLedgerVersion`이 바뀐 걸 볼 수 있습니다. 수수료 정책이 바뀌면 txJSON의 `Fee`와 instructions의 `fee`도 바뀌겠죠. sequence의 변화는 `#8`을 몇 번 더 해서 확인해보도록 하겠습니다.

아, 그리고 sequence가 2인 이유는 제가 송금을 실제로 몇 번 해봤기 때문입니다. 테스트하면서 주소 입력을 잘못하는 바람에 제 1리플 + 수수료 0.000012가 날아가는 대참사가 있었습니다. ㅜㅜ

#### 8.2. 거래 승인하기(sign)

`sign(txJSON: string, secret: string, options: Object): {signedTransaction: string, id: string}`
아직 승인받지 않은 거래에 사인하는 과정입니다. txJSON을 secret을 이용하여 강력한 해시로 만든다고 보면 되겠습니다. secret은 아까 지갑 주소 생성할 때 같이 나왔던 값입니다. 이 값이 제일 중요하므로 숨기라고 무려 **빨간색** 으로 경고하고 있습니다. 역시나 예제로 보겠습니다.

```
let signedTransaction = api.sign(transaction.txJSON, accountInfo.secret);
```

세번째 인자로 options가 있는데 다중 사인이 필요할 경우 사인자 정보를 넣을 수 있습니다. 결과는 다음과 같습니다.

```
{ signedTransaction: '120000228000000024000000022E3BA66EFB201B0224CB386140000000000F424068400000000000000C732103514F098A2140B4BC0E8A1926519A3596DF8728C97F0B8A33F2E50E99CA82CCD1744730450221008782B13A0F95DDD4548C155E2E74D1388CDD91001781BB833ECF25FBB4DC5D8302202CA80AC5A79C9DBF52E2D33E56F05155FBEEBD040204FC21C1A5B01C6B131D538114489A76982ACC1AB4F0AB13C2B540D6679A0C7B26831418F042D8514C49E75CF1E92F20970D6CAD3C04A6',
  id: '8D36F1AD354D5C031B24FB72E4A3C6351A9CD715AE5E710CCCA096190ED9AA67' }
```

id는 트랜잭션의 ID입니다. 트랜잭션 생성 이후 조회할 때 필요합니다. signedTransaction은 암호화된 트랜잭션 값입니다.

#### 8.3. 트랜잭션 요청하기(submit)

`submit(signedTransaction: string): Promise<Object>`
이제 리플 서버에 해당 트랜잭션을 보내보도록 하겠습니다. payment는 낙장불입입니다!!! 신중을 기해 발송을 해보도록 합니다. 이 블로그를 쓰면서도 주소 입력을 잘못하는 바람에 1리플을 날려먹었습니다 ㅠㅠ. 코드는 다음과 같습니다.

```
let submitResult = await api.submit(signedTransaction.signedTransaction);
```

저도 겁나므로 여러 번 시행 안하고 한 번에 보도록 하겠습니다.

```
{ resultCode: 'tesSUCCESS',
  resultMessage: 'The transaction was applied. Only final in a validated ledger.' }
```

성공했습니다. ID는 결과에 나오지 않으니 아까 sign할 때 찾으셔야 합니다. 제가 방금 만들었던 트랜잭션ID는 `3B69CA0C16B8ECC7E9FE131B00D9209B08F176922E74CFDA5902ED2C687C1667` 입니다. [해당 URL](https://xrpcharts.ripple.com/#/transactions/3B69CA0C16B8ECC7E9FE131B00D9209B08F176922E74CFDA5902ED2C687C1667) 을 통해 확인해보도록 합시다.

실패해도 sequence가 늘어나고 **수수료가 날아가니** 요청은 신중하게 합시다!!!

## EOS

[https://steemit.com/eos/@dan/eos-developer-s-log-stardate-201707-9](https://steemit.com/eos/@dan/eos-developer-s-log-stardate-201707-9)

https://github.com/EOSIO/eos/wiki/Smart-Contract

https://github.com/EOSIO/eosjs

```javascript
Signature.signHash = function(dataSha256, privateKey, encoding = 'hex') {
    if(typeof dataSha256 === 'string') {
        dataSha256 = Buffer.from(dataSha256, encoding)
    }
    if( dataSha256.length !== 32 || ! Buffer.isBuffer(dataSha256) )
        throw new Error("dataSha256: 32 byte buffer requred")

    privateKey = PrivateKey(privateKey)
    assert(privateKey, 'privateKey required')

    var der, e, ecsignature, i, lenR, lenS, nonce;
    i = null;
    nonce = 0;
    e = BigInteger.fromBuffer(dataSha256);
    while (true) {
      ecsignature = ecdsa.sign(curve, dataSha256, privateKey.d, nonce++);
      der = ecsignature.toDER();
      lenR = der[3];
      lenS = der[5 + lenR];
      if (lenR === 32 && lenS === 32) {
        i = ecdsa.calcPubKeyRecoveryParam(curve, e, ecsignature, privateKey.toPublic().Q);
        i += 4;  // compressed
        i += 27; // compact  //  24 or 27 :( forcing odd-y 2nd key candidate)
        break;
      }
      if (nonce % 10 === 0) {
        console.log("WARN: " + nonce + " attempts to find canonical signature");
      }
    }
    return Signature(ecsignature.r, ecsignature.s, i);
};
```

https://github.com/Chainers/Cryptography.ECDSA

- This library implements transaction signing algorithm secp256k1 which is used in several blockchains like Bitcoin, EOS and Graphene-based Steem, Golos, BitShares. The library is based on <https://github.com/warner/python-ecdsa> and <https://github.com/bitcoin-core/secp256k1>) No other curves are included. C#, MIT license.

https://github.com/EOSIO/eos/blob/master/programs/eosio-applesedemo/README.md

- EOSIO supports two different elliptic curves for ECDSA -- secp256k1 and secp256r1 (also called prime256v1). The benefit of the r1 curve is hardware support in many mobile devices such as iPhones or Android devices. This application demonstrates usage of the r1 curve in a hardware device via the Secure Enclave on recent MacBook Pros with the Touch Bar. An r1 private key is stored on the Secure Enclave and is not visible to the host. This private key can then be used to sign SHA256 digests (which could be the hash of an EOSIO transaction) via the Secure Enclave. For verification of proper operation, the application then allows verification of public key recovery in the EOSIO code base.

#### Transaction

The transaction function accepts the standard blockchain transaction.

Required transaction header fields will be added unless your signing without a network connection (httpEndpoint == null). In that case provide you own headers:

```
// only needed in cold-storage or for offline transactions
const headers = {
  expiration: '2018-06-14T18:16:10'
  ref_block_num: 1,
  ref_block_prefix: 452435776
}
```

Create and send (broadcast) a transaction:

```javascript
/** @return {Promise} */
eos.transaction(
  {
    // ...headers,
    actions: [
      {
        account: 'eosio.token',
        name: 'transfer',
        authorization: [{
          actor: 'inita',
          permission: 'active'
        }],
        data: {
          from: 'inita',
          to: 'initb',
          quantity: '7.0000 SYS',
          memo: ''
        }
      }
    ]
  }
  // options -- example: {broadcast: false}
)
```

#### Named action functions

More concise functions are provided for applications that may use actions more frequently. This avoids having lots of JSON in the code.

```javascript
// Run with no arguments to print usage.
eos.transfer()

// Callback is last, when omitted a promise is returned
eos.transfer('inita', 'initb', '1.0000 SYS', '', (error, result) => {})
eos.transfer('inita', 'initb', '1.1000 SYS', '') // @returns {Promise}

// positional parameters
eos.transfer('inita', 'initb', '1.2000 SYS', '')

// named parameters
eos.transfer({from: 'inita', to: 'initb', quantity: '1.3000 SYS', memo: ''})

// options appear after parameters
options = {broadcast: true, sign: true}

// `false` is a shortcut for {broadcast: false}
eos.transfer('inita', 'initb', '1.4000 SYS', '', false)
```

Read-write API methods and documentation are generated from the eosio token and system.

Assets amounts require zero padding. For a better user-experience, if you know the correct precision you may use DecimalPad to add the padding.

```javascript
DecimalPad = Eos.modules.format.DecimalPad
userInput = '10.2'
precision = 4
assert.equal('10.2000', DecimalPad(userInput, precision))
```

For more advanced signing, see `keyProvider` and `signProvider` in index.test.js.




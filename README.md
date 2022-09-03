# cogno

```
cognomen 

Noun

A word or set of words by which a person or thing is known, addressed, or referred to

A familiar, invented given name for a person or thing 

used instead of the actual name of the person or thing
```

A smart contract for UTxO based wallet cognomens. Each UTxO is a wallet identifier similar to ada handles but instead of NFTs representing the nickname it will be a UTxO inside a smart contract.

## Use

A user is always in control of their cognomen. If the public key hash is present upon removal then the user may do as they wish with their Cogno deposit. A user can update their Cogno data at any time. The ownership of the UTxO must remain the same but the utxo owner may change any other data in the datum.

The primary use case of this contract is for reference. This contract will contain information with known wallet information.

## The basic Cogno

A wallet will create a Cogno by supplying a payment public key hash, a staking credential, and a cognomen.


```hs
data CognoData = CognoData
  { cdPkh   :: PlutusV2.PubKeyHash
  -- ^ The public key hash of the wallet.
  , cdSc    :: PlutusV2.PubKeyHash
  -- ^ The stake hash of the wallet.
  , cdCogno :: PlutusV2.BuiltinByteString
  -- ^ The cognomen of the wallet.
  ...
  }
```

This is all the information required to identify a wallet address with a cognomen.

### Example

Assume the wallet address is

```bash
addr1qxvlcxj3fg3jk2dp3kmkxhnx2zuv7edktk5rfy2n9juj3h2m0cw9csycjc4v59ywy7fk8nqfu6qjdzjejhvayfhf8dwsttnjt6
```

then the hash representation is

```bash
0199fc1a514a232b29a18db7635e6650b8cf65b65da83491532cb928dd5b7e1c5c4098962aca148e279363cc09e681268a5995d9d226e93b5d
```

For this example address, the datum will become

```json
{
  "constructor": 0,
  "fields": [
    {
      "constructor": 0,
      "fields": [
        {
          "bytes": "99fc1a514a232b29a18db7635e6650b8cf65b65da83491532cb928dd"
        },
        {
          "bytes": "5b7e1c5c4098962aca148e279363cc09e681268a5995d9d226e93b5d"
        },
        {
          "int": 0
        },
        {
          "bytes": "546865416e6369656e744b72616b656e"
        },
        {
          "list": []
        },
        {
          "list": []
        },
        {
          "bytes": ""
        }
      ]
    }
  ]
}
```
The datum above is the default cogno profile for a new user. The other entries in the datum are designated for information used to create a custom profile for the cogno.

## Use Case

When a wallet address is queried, the wallet address can be cross reference with datum data from this contract to relay information about that specific wallet. This behaves very similarly to already existing NFT based identification but the key difference is the updatable data and that it can be referenced on-chain. Smart contracts will now have the ability to reference a wallets Cogno and use that data in on-chain validation functions.

# tag

The tag data structure is designed for displaying and connecting messages on the blockchain. Similarly to the cogno data, a wallet owns the utxo that holds their message. The tag data holds a tag, a short title or label for the post, the details of the post, and if applicable a quote, the txId information of a previous post. 

```hs
data TagData = TagData
  { tPkh    :: PlutusV2.PubKeyHash
  -- ^ The public key hash of the wallet.
  , tSc     :: PlutusV2.PubKeyHash
  -- ^ The stake hash of the wallet.
  , tTag    :: PlutusV2.BuiltinByteString
  -- ^ The tag of the message.
  , tDetail :: [PlutusV2.BuiltinByteString]
  -- ^ The details of the message.
  , tQuoteTxId :: PlutusV2.BuiltinByteString
  -- ^ The TxId of the quote tag.
  , tQuoteIndex :: Integer
  -- ^ The Index of the quote tag.
  }
```
The user may decide to remove the tag after tagging or they may just update an already existing tag with a new message. The message is permanent and available to all on the blockchain.

Another user may see a tag and quote it in their own tag. This type of tag referencing is very similar to commenting to someone elses message on social media. The type of quoting system allows for a direct pointer to the utxo of a previous tag, allowing for dynamic connections to made while all being referencable on-chain.

Any wallet may make a tag but if a tagger happens to have a cogno then their data will be connected and the profile will be shown with their post. This allows public profiles to exist while permitting pseudo-anonymous taggers. 

## Use Case

Tagging the chain with messages can work as a social media dApp or as a data aggregation portal for oracles. The key aspect about the tagging system is the cogno connections. On chain data structures can be linked to off chain profiles that are held on chain.

![Cogno connecting with a Tag](./images/cogno-tag-connection.png)

There are many cogno and many tags but the connections between them reveal a wallet profile that can be shown off chain. The connection is displayed above by the yellow arrows indicating a network betwen a cogno, a tag, and a quote. This information may be used to relay the cogno information for a frontend website to use.

# Other Data

Any new data structure may be added to the ecosystem along side the cogno and tag structures.
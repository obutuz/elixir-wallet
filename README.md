# ElixirWallet

# Creation of mnemonic phrase
The mnemonic phrase is created following the [BIP-0039](https://github.com/bitcoin/bips/blob/master/bip-0039.mediawiki)

```elixir
indexes = GenerateIndexes.generate_indexes()
Mnemonic.generate_phrase(indexes)
```


## Encrypting the mnemonic

The mnemonic phrase is encrypted using the AES algorithm with the CBC cipher mode. In the following diagram you can see how the Cypher Block Chaining (CBC) mode is working. More info [here](https://en.wikipedia.org/wiki/Block_cipher_mode_of_operation#Cipher_Block_Chaining_.28CBC.29)

In code goes like follows

```elixir
Cypher.encrypt("text to encrypt", "password") 
```

```elixir
Cypher.decrypt(encrypted, "pass")
```



# Creation of master public and private key

## From Mnemonic to seed


To create a seed from an already generated mnemonic phrase use the following function. 
```elixir
seed = KeyPair.generate_seed(mnemonic)
```
If a passphrase is not present, an empty string "" is used instead.

But an user may decide to protect their mnemonic with a passphrase, to do so add the passphrase as the next parameter
```elixir
seed = KeyPair.generate_seed(mnemonic, pass_phrase)
```


## Creating HD Wallet from the Seed
Following the [BIP-0032](https://github.com/bitcoin/bips/blob/master/bip-0032.mediawiki)


HD wallets are created from a single root seed, which is a 128bit random number. Most commonly, this seed is generated from a mnemonic as detailed in the previous section.

Every key in the HD wallet is deterministically derived from this root seed, which makes it possible to re-create the entire HD wallet from that seed in any compatible HD wallet. This makes it easy to back up, restore, export, and import HD wallets containing thousands or even millions of keys by simply transferring only the mnemonic that the root seed is derived from.


### Creating extended 'master' Private key from a root seed

After we have generated our seed we can use to generate the Master Private key use the following function:
```elixir
master_private_key = KeyPair.generate_master_key(seed, :seed)
```
This function will generate a Bitcoin private key. If we want to make an Aeternity key we add the `:ae` atom at the end:
```elixir
master_priv_key = KeyPair.generate_master_key(seed, :seed, :ae)
```


### Creating extended Public key

After we have generated the extended private key we can convert it to public key using the following function:
```elixir
extended_pub_key = KeyPair.generate_pub_key(extended_private_key)
```

If the private key has an Aeternity prefix, an Aeternity public key shall be created, otherwise a Bitcoin public key. 


### Creating the Address


Having already the Public key generated, we can derive the address as follows
```elixir
address = KeyPair.generate_wallet_address(pub_key)
```

### Deriving a child key

Once we have the master extended keys we can use them to derive children using the following functions:

If we want to derive child private key we will use lowercase `m` in the path
```elixir
KeyPair.derive("m/0'", extended_priv_key)
```

If we want to derive child public key we will use uppercase `M` in the path
```elixir
KeyPair.derive("M/0'", extended_priv_key)
```

To derive hardned key use an apostrophe `'` sign after the number and a slash `/` to go deeper in the hierarchy


### Aeternity key and address formatting

For deriving a Private key the following prefix values should be used:
`mainnet = 0x9E850AC9`
`testnet = 0x9E850AC9`

For deriving a Public key the following prefix values should be used:
`mainnet = 0x9E86B78E`
`testnet = 0xF350DAF8`

For the address use the following Network bytes:
`mainnet = 0x18`
`testnet = 0x42`




## Installation


If [available in Hex](https://hex.pm/docs/publish), the package can be installed
by adding `elixir_wallet` to your list of dependencies in `mix.exs`:

```elixir
def deps do
  [
    {:elixir_wallet, "~> 0.1.0"}
  ]
end
```

Documentation can be generated with [ExDoc](https://github.com/elixir-lang/ex_doc)
and published on [HexDocs](https://hexdocs.pm). Once published, the docs can
be found at [https://hexdocs.pm/elixir_wallet](https://hexdocs.pm/elixir_wallet).


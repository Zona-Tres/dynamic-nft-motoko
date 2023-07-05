# Dynamic NFT using DIP721

This implementation was created based on this [example](https://github.com/dfinity/examples/tree/master/motoko/dip721-nft-container) from the [samples repository](https://github.com/dfinity/examples). Please refer to them for any information about the standard and the base project.

This particular example only augments a counter found inside the key_val_data vector. It won't work with other data types as it is, since it would be necessary to modify the code to work with each case. The purpose of this repository is to exemplify an approach for an NFT with dynamic data without having to modify anything about the current standard.

## Using the example

 ### Step 1: Clone the repo:

```
git clone https://github.com/zinhunter/dynamic-nft-motoko.git
```

 ### Step 2: Run a local instance of the Internet Computer:

```
dfx start --background 
```

**If this is not a new installation, you may need to run `start` with the `--clean` flag.**

```
dfx start --clean --background
```

 ### Step 3: Deploy the DIP721 NFT canister to your local IC.
This command deploys the DIP721 NFT canister with the following initialization arguments:

```
dfx deploy --argument "(
  principal\"$(dfx identity get-principal)\", 
  record {
    logo = record {
      logo_type = \"image/png\";
      data = \"\";
    };
    name = \"My DIP721\";
    symbol = \"DFXB\";
    maxLimit = 10;
  }
)"
```

 ### Step 4: Mint an NFT.

Use the following command to mint an NFT with a counter. Notice we are initializing the counter to 0:

```
dfx canister call dynamic-nft-motoko mintDip721 \
"(
  principal\"$(dfx identity get-principal)\", 
  vec { 
    record {
      purpose = variant{Rendered};
      data = blob\"hello\";
      key_val_data = vec {
        record { key = \"counter\"; val = variant{Nat8Content=0:nat8} };
      }
    }
  }
)"
```

If this succeeds, you should see the following message:

```
(variant { Ok = record { id = 1 : nat; token_id = 0 : nat64 } })
```

 ### Step 5: Increase the counter.

 The number (0) represents the ID of the token to update.

```
dfx canister call dynamic-nft-motoko increaseCounter '(0)'
```

If successful, you should get something like this:

```
(variant {Ok=2})
```

### Step 6: Check the token's metadata.

Check back the token's metadata to verify the previous method worked correctly.

```
dfx canister call dynamic-nft-motoko getMetadataDip721 '(0)'
```

Again, if this succeeds, the output should look something like this:

```
(
  variant {
    Ok = vec {
      record {
        data = blob "hello";
        key_val_data = vec {
          record { key = "counter"; val = variant { Nat8Content = 1 : nat8 } };
        };
        purpose = variant { Rendered };
      };
    }
  },
)
```

## Final thoughts

Again, this is just a proof of concept, you should handle the modifications for each specific type yourself. I just wanted to find a way to achieve this without having to declare anything as `var` inside the NFT structure and without having to change the standard too much.
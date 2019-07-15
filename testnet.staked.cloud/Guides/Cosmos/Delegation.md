# Using Staked's APIs to Delegate to the Cosmos Pool

## Step 0: Pre-requisites

- You have a Cosmos account with some Atoms you want to delegate to Staked.
- That account is accessible via the `gaiacli keys list` on a PC that is or is not internet connected -*OR*- the account is available on a ledger.
- Using one of the two methods above, you can sign a transaction with those keys and return the signature via API.

## Step 1: API key

You will need an API key to access Staked's APIs. If you don't already have an API key, contact Staked to request one.

## Step 2: Delegate Assets

- Use the `/delegations` ([Documentation](https://developer.staked.cloud/docs/testnet.staked.cloud/1/routes/delegations/%7Bchain%7D/get)) endpoint to request a delegation transaction to sign and register the address for subsequent reporting.
- Shell example:

  ```bash
    $ curl -X POST -H "content-type:application/json" -d '{"amount": "100"}' "http://testnet.staked.cloud/api/delegations/COSMOS/delegator/<YOURCOSMOSADDR>?api_key=<YOURAPIKEY>"
  ```

  - Response will be a Delegation object with attributes, including a txn to sign, like:
  
  ```json
  {
    "id": "<id>",
    "address": "<YOURCOSMOSADDR>",
    "chain": "COSMOS",
    "attributes": {
      "value": {
        "fee": {
          "gas": "99429",
          "amount": []
        },
        "msg": [
          {
            "type": "cosmos-sdk/MsgDelegate",
            "value": {
              "amount": {
                "denom": "atom",
                "amount": "100"
              },
              "delegator_address": "<YOURCOSMOSADDR>",
              "validator_address": "cosmosvaloper18vspjrcxgq66spd5c4s42eg8v7u20wqu9y2u3a"
            }
          }
        ],
        "memo": "Delegation txn created by Staked: <timestamp>",
        "signatures": null
      },
      "type": "auth/StdTx",
      "chain_id": "<testnet_chain_id>"
    },
    "amount": "100",
    "created": "<timestamp>",
    "status": "CREATED"
  }
  ```

## Step 3: Sign the Transaction with Your Private Key

- Assumes `gaiacli` and that you have `--recover` a key with the alias `MyKey`
- Build `txn_to_sign.json`, manually or with `jq`:
  - With the json from above, copy the content of `attributes` into a file `txn_to_sign.json`.

    -*OR*-

  - With `jq`:  

    ```bash
    $ curl ... | jq ' .attributes  ' > txn_to_sign.json
    ```

- `txn_to_sign.json` should look like:

  ```json
  {
    "value": {
      ...
    },
    "type": "...",
    "chain_id": "<testnet_chain_id>"
  }
  ```

- With `txn_to_sign.json`, run the following, with `chain-id` from the POST response:  

  ```bash
  $ gaiacli tx sign delegate_to_sign.json --from=MyKey --chain-id=<testnet_chain_id>
  ```

- The output will be a nearly identical json as the input, but including signatures:

```json
{
  ...
  "signatures": [
    {
      "pub_key": {
        "type": "tendermint/PubKeySecp256k1",
        "value": "<addr>"
      },
      "signature": "<signature>"
    }
  ],
  ...
}
```

## Step 4: PUT Signed Transaction to the Delegations Endpoint

- Given a JSON file `txn_signed.json` containing the signed transcation from [Step 3](#step-3-sign-the-transaction-with-your-private-key), rewrap `value` in `attributes`. `tx_signed.json` should look like:
  ```json
  {
    "attributes": {
      "value": {
        ...
        "signatures": [
          ...
        ]
      }
    }
  }
  ```

- `PUT` `txn_signed` back to the API. This can be done in 2 steps or 1.

  - 2-step process:

    ```bash
    $ curl -X PUT -H "content-type:application/json" -d @txn_signed.json "http://testnet.staked.cloud/api/delegations/COSMOS/delegator/<YOURCOSMOSADDR>?api_key=<YOURAPIKEY>"

    # optionally, GET delegation to confirm values
    curl -X GET "http://testnet.staked.cloud/api/delegations/COSMOS/delegator/<YOURCOSMOSADDR>?api_key=<YOURAPIKEY>"

    $ curl -X PUT "http://testnet.staked.cloud/api/delegations/COSMOS/delegator/<YOURCOSMOSADDR>?api_key=<YOURAPIKEY>"
    ```

  - 1-step process:

    ```bash
    $ curl -X PUT -H "content-type:application/json" -d @txn_signed.json "http://testnet.staked.cloud/api/delegations/COSMOS/delegator/<YOURCOSMOSADDR>/broadcast?api_key=<YOURAPIKEY>"
    ```
  
## Step 6: Access Reporting

- Use the `/reports` ([Documentation](https://developer.staked.cloud/docs/testnet.staked.cloud/1/routes/reports/%7Bchain%7D/balance/get)) endpoint to access reporting. You can request reporting on the Cosmos address with date filters for rewards:

## ~~Step 7: Unbonding~~ **COMING SOON**

<!--
Comment this out until we setup unbonding
~~The delegation can be Unbonded with a DELETE call on the address~~

~~- API call to shut down the masternode~~
  ```bash
  $ curl -X DELETE -H "content-type:application/json" "http://testnet.staked.cloud/api/delegations/COSMOS/delegator/<YOURCOSMOSADDR>?api_key=<YOURAPIKEY>"
  ```
-->

## References

[Cosmos SDK Docs](https://cosmos.network/rpc/)

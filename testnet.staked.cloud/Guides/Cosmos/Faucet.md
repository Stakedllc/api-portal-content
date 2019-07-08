# Using Staked's APIs to Distribute Cosmos Tokens to a testnet address

## Step 0: Pre-requisites

- You have a Cosmos testnet address
- Your Cosmos testnet address is findable via the [block explorer](https://gaia-13k3.bigdipper.live/)

## Step 1: API key

You will need an API key to access Staked's APIs. If you don't already have an API key, contact Staked to request one.

## Step 2: Distribute Tokens

- Use the `partners/distribute` ([Documentation](https://developer.staked.cloud/docs/testnet.staked.cloud/1/routes/partners/distribute/put)) endpoint to send 200,000muon (uatom) at a time to the testnet address mentioned in [Step 0](#step-0-pre-requisites).

- Shell example:

  ```bash
    $ curl -X POST -H "content-type:application/json" -d '{"address": "yourtestnetaddress", "chain": "cosmos"}' "http://testnet.staked.cloud/api/partners/distribute?api_key=<YOURAPIKEY>"
  ```

- The response will be a json object with your new distributions, distributions to date, and remaining distrubtions, in muon. 

## Step 3: Usage

To receive Cosmos delegation rewards you will need 1,000,000muon (5 distributions from the faucet). We provide up to an additional 400,000muon (1,400,000muon total) to accommodate for accidents or excessive fees during testing. If, for whatever reason, you need additional tokens please get in contact.

To view your current distributions (and other data) use the `partners` ([Documentation](https://developer.staked.cloud/docs/testnet.staked.cloud/1/routes/partners/get)) endpoint.  

- Shell example:

  ```bash
    $ curl -H "content-type:application/json" "http://testnet.staked.cloud/api/partners?api_key=<YOURAPIKEY>"
  ```

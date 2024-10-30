# FASTNEAR APIs

FASTNEAR provides several APIs to interact with the NEAR Protocol. This document outlines the various APIs and their capabilities, with links to their respective GitHub repositories for further exploration.

## FASTNEAR API  ([`fastnear-api-server-rs`](https://github.com/fastnear/fastnear-api-server-rs))

*   **Endpoints:**
    *   **Mainnet:** `https://api.fastnear.com`
    *   **Testnet:** `https://test.api.fastnear.com`

*   **Available Request Paths:**
    *   `/status`
    *   `/health`
    *   `/v1/ft/{token_id}/top`
    *   `/v1/account/{account_id}/staking`
    *   `/v1/account/{account_id}/ft`
    *   `/v1/account/{account_id}/nft`
    *   `/v1/account/{account_id}/full`
    *   `/v0/public_key/{public_key}`
    *   `/v0/public_key/{public_key}/all`
    *   `/v0/account/{account_id}/staking`
    *   `/v0/account/{account_id}/ft`
    *   `/v0/account/{account_id}/nft`

*   **Core Functionalities:**  This API offers a streamlined method to access consolidated data regarding NEAR accounts, enabling tasks like:
    *   **Public Key to Account ID Mapping:**  Map public keys to their corresponding account IDs, encompassing implicit account IDs (even for non-existent accounts). This mapping supports both full-access keys and limited access keys.
    *   **Staking Pool Delegation:** Identify the staking pools an account has delegated to and the block height of the last delegation change.
    *   **Fungible Token (FT) Details:** Retrieve information about fungible tokens (FTs) held by an account, including contract IDs, last update block heights, and balances. The returned `balance` represents the raw balance and is not adjusted for decimals defined in the FT metadata. A `balance` value of `""` (empty string) might indicate issues with the FT contract.
    *   **Non-Fungible Token (NFT) Information:** Retrieve data about non-fungible tokens (NFTs) an account has interacted with, including contract IDs and last update block heights.
    *   **Comprehensive Account Information:** Fetch detailed account information, encompassing its state (balance, locked balance, storage usage), staking pools, fungible tokens, and non-fungible tokens.

*   **Token Holder Information:**  You can obtain a list of the top 100 account IDs holding a specific fungible token, ordered by their descending balance.
*   **API Version:**  API V1 is the current version and provides more detailed information.
*   **Server Status:**  Use the `/status` and `/health` endpoints to check the API server's health and synchronization status.

## FASTNEAR Explorer API  ([`explorer-api`](https://github.com/fastnear/explorer-api))

*   **Backend:** This server employs a Clickhouse database for efficient query handling and data retrieval for blockchain exploration purposes. 
*   **Endpoint:** `https://explorer.main.fastnear.com/v0/`
*   **Available Request Paths:**
    *   `/v0/transactions`
    *   `/v0/account`
    *   `/v0/block`
    *   `/v0/blocks/last`
    *   `/v0/receipt`

*   **Core Functionalities:**  The Explorer API utilizes POST requests for accessing blockchain data. Key endpoints include:
    *   `/v0/transactions`: Fetch transactions by providing their hashes.
    *   `/v0/account`: Retrieve transaction metadata associated with a given account ID. The results encompass transactions signed by the account and those related to the account (e.g., token transfers).  Use the `max_block_height` parameter for pagination to limit results to transactions below a specific block height.
    *   `/v0/block`: Obtain transactions associated with a specific block, referenced either by its height (integer) or hash (string). The response includes transactions originating from the block or having a receipt in it. 
    *   `/v0/blocks/last`: Get the number of transactions for each of the last 10 blocks. 
    *   `/v0/receipt`: Find the transaction associated with a specific receipt ID. 

## NEAR Data Server ([`neardata-server`](https://github.com/fastnear/neardata-server/))

*   **Purpose:**  This server provides indexed blockchain data for the NEAR Protocol, offering a simpler and free alternative to the NEAR Lake Framework.
*   **Endpoints:**
    *   **Mainnet:** `https://mainnet.neardata.xyz`
    *   **Testnet:** `https://testnet.neardata.xyz`

*   **Available Request Paths:**
    *   `/v0/first_block`
    *   `/v0/block/:block_height`
    *   `/v0/block_opt/:block_height`
    *   `/v0/last_block/final`
    *   `/v0/last_block/optimistic`

*   **Core Functionalities:**  The server uses GET requests to provide block data:
    *   `/v0/first_block`: Redirects to the first block following the genesis block, guaranteeing its existence and providing an immediate response.
    *   `/v0/block/:block_height`: Retrieves the finalized block at the specified height in JSON format. The server waits for the block to be produced if it doesn't exist but is nearing finalization. Unlike NEAR Lake, this endpoint delivers the block as a single JSON object. Additionally, it includes the `tx_hash` for every receipt in the `receipt_execution_outcomes`, indicating the transaction hash that generated the receipt.
    *   `/v0/block_opt/:block_height`: Retrieves the optimistic block at the given height, redirecting to the finalized block if the block is relatively old.
    *   `/v0/last_block/final`: Redirects to the latest finalized block, ensuring its availability and delivering an immediate response.
    *   `/v0/last_block/optimistic`: Redirects to the latest optimistic block and provides an immediate response.

*   **Key Features:**
    *   **Free Access:** No authentication is needed to use the NEAR Data Server.
    *   **Bandwidth Limit:** The server has a 1 Gbps bandwidth limit. High concurrent request volume might cause throttling.
    *   **Caching:**  Caching is implemented to improve response times, particularly for recent blocks.

*   **Data Indexing:** 
    *   **Historical Data:** Index historical data by sequentially reading blocks, starting from a specific block or the genesis block.
    *   **Real-Time Data:** To stay updated with the latest data, start from the latest finalized block and poll for new blocks by incrementing the block height with each request.

*   **Local Setup:** Developers can run the NEAR Data Server locally using Rust and the Actix Web framework. Setup instructions, including environment variable configurations, are provided in the repository.

This page describe some of the errors found running the secret-store.

-   ```
    b'"\\"Internal error: ACL checker call error: please ensure the contract and method you\'re 
    calling exist! failed to decode empty bytes. if you\'re using jsonrpc this is likely due to jsonrpc 
    returning 0x in case contract or method don\'t exist\\""
    ``` 
     SS and parity client are not in the same network or the ACL contract is not deployed properly.


-   ```
    Feb 04 15:26:34 ip-172-31-0-123.ec2.internal docker[10597]: 2019-02-04 15:26:34 
    UTC tokio-runtime-worker-3 WARN secretstore_net  0xb98e…fec0: decryption session error 
    'Consensus unreachable' when processing message Decryption.DecryptionConsensusMessage.ConfirmConsensusInitialization(false) 
    from node 0xfc10…b0ab
    ```
     Error in the contracts that is solve it in KEEPER-CONTRACTS>= 0.6.12

-   ```
    ValueError: {'code': -32041, 'message': 'Request has been rejected because of queue limit.'}
    ```
     Pending to be solve, only in Nile.


-   ```
    b'"\\"Internal error: Calling ACL contract without trusted blockchain client\\""'
    ```
    Solve it running parity with the --no-warp option.

-   ```
    Exception: Error decrypting the urls: Failed to retrieve decryption keys: Forbidden
    ``` 
    The access has not been granted. It could be because you are granting access with the wrong account.
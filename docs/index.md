# Opus Token: Technical Documentation

## Introduction
The Opus token is a solution to the tokenization of physical assets based on the ERC-1155 token standard. Compatibility and modularity both have high priority in the design of the Opus token. Therefore, the Opus token includes all mandatory functions of the ERC-1155 standard to achieve full compatibility with the ERC-1155 ecosystem and security through maturity. 

## Base Contract

The base contract includes all mandatory functions of the ERC-1155 standard. In this prototype the ERC-1155 standard is implemented by inheritance of an ERC-1155 contract optimized for minting new tokens in an already deployed smart contract (“ERC1155Mintable”). Transactions and metadata are handled according to the procedures specified in the [ERC-1155 whitepaper](https://eips.ethereum.org/EIPS/eip-1155). 
```solidity
    function mint(uint256 _id, address[] calldata _to, uint256[] calldata _quantities) external creatorOnly(_id) {

```

## Transactions
Transaction are handled according to the procedures in the [ERC-1155 whitepaper](https://eips.ethereum.org/EIPS/eip-1155) to guarantee compatibility. 

## Token Types and Identifiers
Token types are referred to by a unique identifier (ID) as specified in the ERC-1155 standard. In contrast to standards such as ERC-20, the identifier of integer type is the only way to refer to a token; symbols and other strings may not be used. Provided it is of integer type, the choice of identifier can be arbitrary. In the **Opus** contract, a variable of integer type called *nonce* (inherited from the *ERC1155Mintable* contract) keeps track of the token IDs and ensures that the ID of each token type is unique. *nonce* is initalized to 0 in the deployment of the contract and subsequently incremented each time the *create* function is called.

## Constructor and Deployment

The constructor only performs two actions, it initializes the nonce to zero and it specifies a governance address, which technically can be either an account or a smart contract. 
```solidity
   constructor(address governance_) public ERC1155Mintable() {
        // governance address 
        governance = governance_; 
        // nonce is initialized to zero, ensures unambiguous ID for each token type
        nonce = 0; 
    }

```

## Creating and Minting Tokens
Two functions are involved in the minting of tokens: The *create* function creates a new token type. The *mint* function mints additional tokens of an existing token type.
```solidity
    // Creates a new token type and assigns _initialSupply to minter
    function create(uint256 _initialSupply, string calldata _uri) external returns(uint256 _id) {

        _id = ++nonce;
        creators[_id] = msg.sender;
        balances[_id][msg.sender] = _initialSupply;

        // Transfer event with mint semantic
        emit TransferSingle(msg.sender, address(0x0), msg.sender, _id, _initialSupply);

        if (bytes(_uri).length > 0)
            emit URI(_uri, _id);
    }
´´´

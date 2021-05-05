# Opus Tokenization Framework: Technical Documentation

## Introduction
The Opus tokenization framework is a solution to the tokenization of physical assets based on the ERC-1155 token standard. The tokenization protocol consists of a base contract of the ERC-1155 standard with minting function inherited from the *ERC1155Mintable.sol* contract and ERC-1155 compatible metadata extensions. Additional compliance functionalities (address whitelisting) can be included as needed. Compatibility and modularity both have high priority in the design of the Opus token. Therefore, the Opus token includes all mandatory functions of the ERC-1155 standard to achieve full compatibility with the ERC-1155 ecosystem and security through maturity. 

## Dependencies and Inheritances
The Opus contract
| Contract / Standard |  Role |
| :-- | :-- |
| [ERC1155](https://eips.ethereum.org/EIPS/eip-1155) | transactions |
| ERC1155Mintable | minting / creation of tokens |
| ERC1155Metadata | metadata specification / handling |
| ERC165 | interface compatibility |
| ERC2477 | metadata integrity |
| ERC1405 | whitelisting |

## Base Contract

The base contract includes all mandatory functions of the ERC-1155 standard. In this prototype the ERC-1155 standard is implemented by inheritance of an ERC-1155 contract optimized for minting new tokens in an already deployed smart contract (“ERC1155Mintable”). Transactions and metadata are handled according to the procedures specified in the [ERC-1155 whitepaper](https://eips.ethereum.org/EIPS/eip-1155). 


## Transactions
Transaction are handled according to the procedures in the [ERC-1155 whitepaper](https://eips.ethereum.org/EIPS/eip-1155) to guarantee compatibility. 

## Token Types and Identifiers
Token types are referred to by a unique identifier (ID) as specified in the ERC-1155 standard. In contrast to standards such as ERC-20, the identifier of integer type is the only way to refer to a token; symbols and other strings may not be used. Provided it is of integer type, the choice of identifier can be arbitrary. In the **Opus** contract, a variable of integer type called *nonce* (inherited from the *ERC1155Mintable* contract) keeps track of the token IDs and ensures that the ID of each token type is unique. *nonce* is initalized to 0 in the deployment of the contract and subsequently incremented each time the *create* function is called.

## Constructor and Deployment

The constructor only performs two actions, it initializes the nonce to zero and it specifies a governance address, which technically can be either an account or a smart contract. 
```solidity
    constructor()
        ERC1155("https://github.com/DominiqueO/Opus/tree/main/metadata/{id}.json")
    {
        _setupRole(DEFAULT_ADMIN_ROLE, msg.sender);
        _setupRole(URI_SETTER_ROLE, msg.sender);
        _setupRole(MINTER_ROLE, msg.sender);
        _setupRole(WHITELISTED_ROLE, msg.sender);
    }
```

## Creating and Minting Tokens
Two functions are involved in the minting of tokens: The *create* function creates a new token type. The *mint* function mints additional tokens of an existing token type. Both of these functions are inherited from *ERC1155Mintable.sol*. 

### The ***create*** function
This function creates a specified number (*_initialSupply*) of a new type of token. The new tokens are assigned to the minter / caller of the ***create*** functions. *_initialSupply* can be set to 0, if a new type of token should be created initially without creating any tokens. Governance over a specified token type is automatically yielded to creator of said token type (caller of the ***create*** function instance used to create the token type). 

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
```

### The *mint* and *mintBatch* Functions
The ***mint*** function is the primary procedure to mint new tokens under the Opus tokenization protocol. The ***mint*** function allows for newly minted tokens to be directly transferred to an arbitrary number of accounts. The order of the addresses (*_to*) has to match the order of the token amounts (*_quantities*) and the two arrays have to be of the same size. The ***mint*** function can only be called by the creator of the token type, i.e. the caller of the *create* function for this token type. 

```solidity
    function mint(address account, uint256 id, uint256 amount, bytes memory data)
        public
    {
        require(hasRole(MINTER_ROLE, msg.sender));
        _mint(account, id, amount, data);
    }

    function mintBatch(address to, uint256[] memory ids, uint256[] memory amounts, bytes memory data)
        public
    {
        require(hasRole(MINTER_ROLE, msg.sender));
        _mintBatch(to, ids, amounts, data);
    }
```

## Access and Governance
The Opus tokenization framework is equipped with the flexible access control features of the OpenZeppelin [*AccessControl*](https://docs.openzeppelin.com/contracts/4.x/api/access) module. Access roles can be defined separately in the constructor of the smart contract. 

## Metadata and Information Management





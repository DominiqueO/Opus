# Opus Tokenization Framework: Technical Documentation

## Introduction
The Opus tokenization framework is a solution to the tokenization of physical assets based on the ERC-1155 token standard. The tokenization protocol consists of a base contract of the ERC-1155 standard with minting function inherited from the *ERC1155Mintable.sol* contract and ERC-1155 compatible metadata extensions. Additional compliance functionalities (address whitelisting) can be included as needed. Compatibility and modularity both have high priority in the design of the Opus token. Therefore, the Opus token includes all mandatory functions of the ERC-1155 standard to achieve full compatibility with the ERC-1155 ecosystem and security through maturity. 

## Dependencies and Inheritances
The Opus framework is based on the OpenZeppelin implementations of the following standards:
| Contract / Standard |  Role |
| :-- | :-- |
| [ERC1155](https://eips.ethereum.org/EIPS/eip-1155) | transactions |
| ERC1155Metadata | metadata specification / handling |
| ERC165 | interface compatibility check|
| ERC2477 | metadata integrity |

## Base Contract

The base contract includes all mandatory functions of the ERC-1155 standard. In this prototype the ERC-1155 standard is implemented by inheritance of an ERC-1155 contract optimized for minting new tokens in an already deployed smart contract (“ERC1155Mintable”). Transactions and metadata are handled according to the procedures specified in the [ERC-1155 whitepaper](https://eips.ethereum.org/EIPS/eip-1155). 


## Transactions
Transaction are handled according to the procedures in the [ERC-1155 whitepaper](https://eips.ethereum.org/EIPS/eip-1155) to guarantee compatibility. 

## Token Types and Identifiers
Token types are referred to by a unique identifier (ID) as specified in the ERC-1155 standard. In contrast to standards such as ERC-20, the identifier of integer type is the only way to refer to a token; symbols and other strings may not be used. Provided it is of integer type, the choice of identifier can be arbitrary. 

## Constructor and Deployment
The constructor 

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
Two functions are involved in the minting of tokens: 

### The *mint* and *mintBatch* Functions
The ***mint*** function and the ***mintBatch*** function have the same basic functionality. The ***mintBatch*** function can mint tokens of different types and directly transfer them to a specified account.

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
The Opus tokenization framework is equipped with the flexible access control features of the OpenZeppelin [*AccessControl*](https://docs.openzeppelin.com/contracts/4.x/api/access) module. Access roles can be defined in the constructor of the smart contract. Governance such as KYC and AML measures is also enforced through access control by the definition of the *WHITELISTED_ROLE* variable.

## Metadata and Information Management
Metadata integrity can be provided by hashing functions in accordance with the proposal for the ERC-2477 standard:

```solidity
    function tokenURIIntegrity(uint256 tokenId) external view returns(bytes memory digest, string memory hashAlgorithm);
    
    function tokenURISchemaIntegrity(uint256 tokenId) external view returns(bytes memory digest, string memory hashAlgorithm);
}
```



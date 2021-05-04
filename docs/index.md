# Opus Token: Technical Documentation

## Introduction
The Opus token is a solution to the tokenization of physical assets based on the ERC-1155 token standard. Compatibility and modularity both have high priority in the design of the Opus token. Therefore, the Opus token includes all mandatory functions of the ERC-1155 standard to achieve full compatibility with the ERC-1155 ecosystem and security through maturity. 

## Base Contract

The base contract includes all mandatory functions of the ERC-1155 standard. In this prototype the ERC-1155 standard is implemented by inheritance of an ERC-1155 contract optimized for minting new tokens in an already deployed smart contract (“ERC1155Mintable”). Transactions and metadata are handled according to the procedures specified in the [ERC-1155 whitepaper](https://eips.ethereum.org/EIPS/eip-1155). 
```solidity
    function mint(uint256 _id, address[] calldata _to, uint256[] calldata _quantities) external creatorOnly(_id) {

```

For more details see [GitHub Flavored Markdown](https://guides.github.com/features/mastering-markdown/).

## Transactions
Transaction are handled according to the procedures in the [ERC-1155 whitepaper](https://eips.ethereum.org/EIPS/eip-1155) to guarantee compatibility. 

Your Pages site will use the layout and styles from the Jekyll theme you have selected in your [repository settings](https://github.com/DominiqueO/Opus/settings/pages). The name of this theme is saved in the Jekyll `_config.yml` configuration file.

## Constructor and Deployment

The constructor only performs two actions, it initializes the nonce to zero and it specifies a governance address, which technically can be either an account or a smart contract. 
```solidity
   constructor(address governance_) public ERC1155Mintable() {
        governance = governance_;
        nonce = 0; // ID is initialized to zero
    }

```

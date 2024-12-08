# TWIL

TWIL (This Week I Learned) will serve as a source for what I have learned in the
course of a week about Ethereum and ZKsync. The goal is to help others who are
looking to move from beginner to intermediate. 

Sources:

- Updraft Notes: <https://github.com/bxpana/Updraft/blob/main/NOTES.md>
- zkLearn: <https://github.com/bxpana/zkLearn/blob/main/NOTES.md>

## Week of November 29, 2024

**Understanding `block.timestamp` in Solidity**

This week, I delved into how Solidity handles random numbers, specifically using `block.timestamp`. Here’s what I learned:

**What is `block.timestamp`?**

`block.timestamp` is a globally available unit in Solidity that returns the current approximate time according to the blockchain.

**Using `block.timestamp` to Measure Time Intervals**

One common use of `block.timestamp` is to check if a certain amount of time has passed within a smart contract. For example:

```solidity
if (`block.timestamp` - s_lastTimeStamp > i_interval) {
    // Execute code after the interval has passed
}
```

In this snippet:

- `s_lastTimeStamp` is a stored timestamp from a previous event.
- `i_interval` is the time interval you want to wait before executing the next action.

**Setting `block.timestamp` in the Constructor**

You can initialize a timestamp when the contract is created by setting it in the constructor:

```solidity
constructor() {
    s_lastTimeStamp = `block.timestamp`;
}
```

This sets s_lastTimeStamp to the timestamp at the moment of contract deployment.

## Week of December 2, 2024

### **Signature Standards**

**EIP-191 and EIP-712: Verifying Signatures in Contracts**

---

**Why Do We Need Them?**

- **Human-readable transactions:** EIP-712 provides better context for users by displaying structured, human-readable transaction messages.
- **Security:** EIP-712 helps prevent replay attacks by including domain-specific metadata.
- **Sponsored transactions:** EIP-191 enables sending transactions on behalf of a user using their signature.

---

**EIP-191: Basic Structure**

**Overview**
- Format: `0x19<1 byte version><version specific data><data to sign>`
  - `0x19`: Prefix signaling a signature.
  - `<1 byte version>`: Indicates the version of the signed data.
    - `0x00`: Data with an intended validator.
    - `0x01`: Structured data (EIP-712-compatible).
    - `0x45`: Personal sign messages.
  - `<version specific data>`: Metadata specific to the version.
  - `<data to sign>`: The actual data being signed.

**Example Solidity Function**

```solidity
function getSigner191(uint256 message, uint8 _v, bytes32 _r, bytes32 _s) public view returns (address) {
    bytes1 prefix = bytes1(0x19);
    bytes1 eip191Version = bytes1(0);
    address intendedValidatorAddress = address(this);
    bytes32 applicationSpecificData = bytes32(message);

    bytes32 hashedMessage = keccak256(abi.encodePacked(
        prefix, 
        eip191Version, 
        intendedValidatorAddress, 
        applicationSpecificData
    ));

    address signer = ecrecover(hashedMessage, _v, _r, _s);
    return signer;
}
```

**EIP-712: Typed, Structured Data Signing**

Advantages

1. Readability: Clearly structured messages for users.
2. Security: Reduces risks from signing unintended data by explicitly defining the format.

Message Format

0x19 0x01 <domainSeparator> <hashStruct(message)>
- 0x19 0x01: Fixed prefix from EIP-191.
- <domainSeparator>: Represents the signing domain context (e.g., app name, version, chain ID, etc.).
- <hashStruct(message)>: Hash of the structured message to be signed.

Key Components of EIP-712

Domain Separator

- Ensures the signature is valid within its intended domain and prevents cross-domain replay attacks.
- Example:

```solidity
struct EIP712Domain {
    string name;
    string version;
    uint256 chainId;
    address verifyingContract;
}

bytes32 domainSeparator = keccak256(abi.encode(
    keccak256("EIP712Domain(string name,string version,uint256 chainId,address verifyingContract)"),
    keccak256(bytes(name)),
    keccak256(bytes(version)),
    chainId,
    verifyingContract
));
```

**Hashing the Message**

- The structured message is hashed to ensure integrity and prevent tampering.

Example:

```solidity
struct Message {
    string content;
    uint256 amount;
}

bytes32 hashStruct = keccak256(abi.encode(
    keccak256("Message(string content,uint256 amount)"),
    keccak256(bytes(content)),
    amount
));

bytes32 finalHash = keccak256(abi.encodePacked(
    0x19,
    0x01,
    domainSeparator,
    hashStruct
));
```

### Diamond Proxy Architecture

Overview

- A modular and upgradeable smart contract architecture based on EIP-2535.
- Key features:
- Modularity: Contracts can be broken into smaller, manageable parts (facets).
- Upgradeability: Specific parts of the contract can be upgraded without altering the main address.
- Shared Resources: Facets share a single storage structure.

Analogy: The Hotel

- Diamond Proxy: The hotel’s front desk, routing requests.
- Facets: Various hotel services (e.g., room service, cleaning).
- Function Selector Mapping: How requests are routed to the correct service.
- Shared Storage: Shared hotel resources (e.g., a centralized guest database).
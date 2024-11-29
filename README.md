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

Stay tuned for next week’s insights!
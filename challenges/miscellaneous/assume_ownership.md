# Assume ownership
<sup>MISCELLANEOUS: 300 POINTS</sup>

To complete this challenge, become the owner.

```solidity
pragma solidity ^0.4.21;

contract AssumeOwnershipChallenge {
    address owner;
    bool public isComplete;

    function AssumeOwmershipChallenge() public {
        owner = msg.sender;
    }

    function authenticate() public {
        require(msg.sender == owner);

        isComplete = true;
    }
}
```

Enjoy this inspirational music while you work: [Owner Of A Lonely Heart](https://youtu.be/ytc4_JJWqMQ).


Solution: Typo in the constructor name, call the function and gg.
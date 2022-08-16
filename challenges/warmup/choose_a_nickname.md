# Choose a nickname
<sup>WARMUP: 200 POINTS</sup>

It’s time to set your Capture the Ether nickname! This nickname is how you’ll show up on the [leaderboard](https://capturetheether.com/leaderboard/).

The CaptureTheEther smart contract keeps track of a nickname for every player. To complete this challenge, set your nickname to a non-empty string. The smart contract is running on the Ropsten test network at the address 0x71c46Ed333C35e4E6c62D32dc7C8F00D125b4fee.

Here’s the code for this challenge:

```solidity
pragma solidity ^0.4.21;

// Relevant part of the CaptureTheEther contract.
contract CaptureTheEther {
    mapping (address => bytes32) public nicknameOf;

    function setNickname(bytes32 nickname) public {
        nicknameOf[msg.sender] = nickname;
    }
}

// Challenge contract. You don't need to do anything with this; it just verifies
// that you set a nickname for yourself.
contract NicknameChallenge {
    CaptureTheEther cte = CaptureTheEther(msg.sender);
    address player;

    // Your address gets passed in as a constructor parameter.
    function NicknameChallenge(address _player) public {
        player = _player;
    }

    // Check that the first character is not null.
    function isComplete() public view returns (bool) {
        return cte.nicknameOf(player)[0] != 0;
    }
}
```

Enjoy this inspirational music while you work: [Say My Name](https://youtu.be/9YZXPs8uAB0).
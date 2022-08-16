# Mapping
<sup>MATH: 750 POINTS</sup>

Who needs `mapping`s? I’ve created a contract that can store key/value pairs using just an array.

```solidity
pragma solidity ^0.4.21;

contract MappingChallenge {
    bool public isComplete;
    uint256[] map;

    function set(uint256 key, uint256 value) public {
        // Expand dynamic array as needed
        if (map.length <= key) {
            map.length = key + 1;
        }

        map[key] = value;
    }

    function get(uint256 key) public view returns (uint256) {
        return map[key];
    }
}
```

Enjoy this inspirational music while you work: [Map To My Heart](https://www.youtube.com/watch?v=A259WnFMRXI).


Solution:

Overflow the length with MAX_INT - 1 

Then we need to find isCompleted address and set it to 1

To find inital map address we do keccak256(bytes32(1))

Than to find the isComplete address we subtract to the last address 0xffff... the map[0] address

Finally set that value to 1 (== true)

```solidity
contract Test {
    uint256 public MAX_INT = 2 ** 256 - 1;
    bytes32 public init = bytes32(1);
    bytes32 public map0Addr = keccak256(init); 
    // 0xb10e2d527612073b26eecdfd717e6a320cf44b4afac2b0732d9fcbe2b7fa0cf6
    bytes32 public isCompleteAddr = 0xffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffff - 0xb10e2d527612073b26eecdfd717e6a320cf44b4afac2b0732d9fcbe2b7fa0cf6;
    // 0x4ef1d2ad89edf8c4d91132028e8195cdf30bb4b5053d4f8cd260341d4805f309
}
```


In the case of a dynamic array, the reserved slot p contains the length of the array as a uint256, and the array data itself is located sequentially at the address keccak256(p).

Ref 1, Ref 2
Concretely, the contract’s storage looks like this:
```
slot 0: isComplete
slot 1: map.length
...
slot keccak(1): map[0]
slot keccak(1) + 1: map[1]
slot keccak(1) + 2: map[2]
slot keccak(1) + 3: map[3]
slot keccak(1) + 4: map[4]
...
```

Note that the array items wrap around after they reached the max storage slot of 2^256 - 1. Using a bit of math we can find the map index that writes to the isComplete variable at storage slot 0:

need to find array index that maps to 0 mod 2^256
i.e., keccak(1) + index mod 2^256 = 0
<=> index = -keccak(1) mod 2^256
 => index = 2^256 - keccak(1) as keccak(1) is in range
 

Ref 1: https://github.com/Arachnid/uscc/tree/master/submissions-2017/doughoyte#solidity-storage-layout
Ref 2: https://docs.soliditylang.org/en/v0.6.8/internals/layout_in_storage.html#mappings-and-dynamic-arrays

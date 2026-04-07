# Simple-Options-Contract
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

import "https://github.com/OpenZeppelin/openzeppelin-contracts/blob/v5.0.0/contracts/access/Ownable.sol";

contract SimpleCallOption is Ownable {
    address public buyer;
    uint256 public strikePrice;
    uint256 public expiry;
    uint256 public premium;

    event OptionExercised(address buyer, uint256 payout);

    constructor(address _buyer, uint256 _strike, uint256 _expiryDays) payable Ownable(msg.sender) {
        buyer = _buyer;
        strikePrice = _strike;
        expiry = block.timestamp + (_expiryDays * 1 days);
        premium = msg.value;
    }

    function exercise() external {
        require(msg.sender == buyer, "Not buyer");
        require(block.timestamp < expiry, "Expired");
        require(address(this).balance >= strikePrice, "Insufficient funds");

        payable(buyer).transfer(address(this).balance);
        emit OptionExercised(buyer, address(this).balance);
    }
}

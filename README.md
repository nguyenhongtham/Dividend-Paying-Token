# Dividend-Paying-Token
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.26;

import "@openzeppelin/contracts/token/ERC20/ERC20.sol";
import "@openzeppelin/contracts/access/Ownable.sol";

contract DividendToken is ERC20, Ownable {
    uint256 public totalDividends;
    mapping(address => uint256) public lastClaimed;

    constructor() ERC20("DividendBase", "DBT") {
        _mint(msg.sender, 100_000_000 * 10 ** decimals());
    }

    function distributeDividends() external payable onlyOwner {
        totalDividends += msg.value;
    }

    function claim() external {
        uint256 pending = pendingDividends(msg.sender);
        require(pending > 0, "No dividends");
        lastClaimed[msg.sender] = totalDividends;
        payable(msg.sender).transfer(pending);
    }

    function pendingDividends(address account) public view returns (uint256) {
        uint256 shares = balanceOf(account);
        if (shares == 0) return 0;
        return (totalDividends - lastClaimed[account]) * shares / totalSupply();
    }
}

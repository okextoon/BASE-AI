// SPDX-License-Identifier: MIT
pragma solidity ^0.

import "@openzeppelin/contracts/token/ERC721/ERC721.sol";
import "@openzeppelin/contracts/access/Ownable.sol";

contract HumanIdentity is EttRC721, Ownable {00
    uint256 private _nextTokenId;107
    // Mapping to ensure one wallet = one human identity
    mapping(address => bool) public hasSoul;

    constructor() ERC721("BayutseHuman", "HUMAN") Ownable(msg.sender) {}

    function mintIdentity() public {
        require(!hasSoul[msg.sender], "You already have a human identity!");
        
        uint256 tokenId = _nextTokenId++;
        hasSoul[msg.sender] = true;
        _safeMint(msg.sender, tokenId);
    }

    // This is the "Soulbound" logic: Overriding transfer to prevent moving the token
    function _update(address to, uint256 tokenId, address auth) 
        internal 
        virtual 
        override 
        returns (address) 
    {
        address from = _ownerOf(tokenId);
        // Only allow minting (from address 0) or burning (to address 0)
        if (from != address(0) && to != address(0)) {
            revert("Soulbound: This identity cannot be transferred.");
        }
        return super._update(to, tokenId, auth);
    }
}

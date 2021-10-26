Project Aithisi

Before directly diving into the Smart Contract, this time I want to briefly talk about the underlying idea. The Smart Contracts will be just the tool to make the idea happen. But first we need a strategy how we actually deliver tokens.

For our project, we are using olive oil as the product that gets shipped. We are experimenting with a small batch where we collaborate with Artists around the world, who create digital art, which then gets printed as labels for the bottles:

We have a few components that play together:

A digital Art piece
A physical olive oil (that doesn't exist yet!)
A physical label
A customized video message of the harvest
The Process to get the art + oil to the end-customer is supposedly a simple 4 step process:

Some artist creates a digital art piece
We mint an ERC721 NFT and auction it
We harvest the olives and press the olive oil
And then we stick the label on the bottle and ship the olive oil to the NFT holder

What can possibly go wrong? ...

Physical<->Digital Twin Challenges¶
Each one of these steps have their unique challenges. To give you a brief rundown:

Pay to Play - Gas Costs: Creating the initial ERC721 contract requires about 4 million gas, which is $250 to $500 in Ether just to deploy the contract the first time at 40 Gwei. Lazy Minting is not an option in this case, more on that later.
10 Artists: That means 10 deliverables that need to be on time, and it also means 10 different opinions on how branding/packaging/bottles should look like.
Authenticity of the Owner: Who to ship to? That needs to be a trustable address.
The video isn't known upfront before the auction
It is a variable time when the harvest occurs. It's nature after all, we can't predict in May at which day exactly in October the olives will be half-ripe.
Before we write the code, let's dive into the mechanics of Authenticity first. Then write the code and see if we can bring down the gas costs.

Authenticity and Shipping Address¶
The difference between ERC20 and ERC721 tokens is that every token in the ERC721 contract is unique. It's like deploying many ER20 contracts, just under one address. ERC20 Tokens can be sent from one address to another. This is a very fundamental property why ERC20 tokens are so popular.

NFTs are usually ERC-721 contracts and should adhere to the ERC-721 interface. This means, they should also contain a function called function transferFrom(address from, address to, uint256 tokenId) external;. With this function you can transfer a token from one address to another address, just like with ERC20 tokens. With ERC721 you need to add an additional tokenId, because every token is unique. But the functionality is essentially the same.

That causes a problem when we want to ship the physical product that is represented by a token. There is a very real way to game the system.

How to Game the System¶
Let's say Mr.X has the token and wants the bottle to ship to New York. We're shipping the bottle to New York. While the bottle is on the way, he sells the token to Mr.Y in Russia. Mr.X gets the bottle, Mr.Y gets no physical product. That can happen intentional, but also unintentional.


This is why we will introduce a novel locking mechanism that requires the physical product to unlock the token transfer again.

Time-Based Locking Mechanism¶
The token will lock itself automatically on Oct 1st, the month in which the harvest takes place. The token owner can then send us the shipping address in a secure way (more on that later). Then we harvest the olives, produce the oil and ship the bottle. On the bottle there will be a QR code to unlock the token.

This can be done only by the token owner, by nobody else.


The QR code will either be directly in the label or an additional flyer added to the packaging. We will provide a simple website to scan the QR code later and generate a simple transaction to unlock the token, which should work from every wallet - MetaMask, Fortmatic, Portis, etc... without problems.

With this in mind, let's start to create a simple version of our ERC721 Token.
## Opensea Ethmoji Contract Review

Opensea is a marketplace for the decentralized web where creators can list their Compositions (music, paintings, digital artworks) for sale as NFTs.
Contract `Ethmoji` inherits the characteristics of ERC721Token, Ownable, PullPayment and Pausable contract.

- `ERC721` -lays the base specifications for an NFT contract .
- `Ownable` - focuses on basic authorization functions. This helps us with user permissions.
- `Pullpayment` - instead of using send or transfer to send money, through this contract, we get to the asyncTransfer function which is called by the depositor to store an amount in a particular address. PullPayment makes use of the Escrow contract which is an Ownable contract.
The Escrow contract holds money deposited into an address till the payee withdraws it.
- `Pausable` - allows users to implement an emergency stop mechanisms which calls the pause function when the contract is not paused or unpause functions.

# Function mintTo
![f1](https://user-images.githubusercontent.com/53812432/162449831-356e7b2c-246a-4b38-ab10-c2ceac60f158.png)

This function creates a base token to an address with a given composition price.
It takes in the following parameters - address _to, _compositionPrice, _changeRate, _changeableCompPrice, _imageHash

_to address of the future owner of the token
_compositionPrice uint256 composition price for the new token
_changeRate uint256 the rate at which comp price increases after every use
_changeableCompPrice bool whether or not the comp price can be changed
_imageHash uint256 hash of the resulting image


uint256 newTokenIndex = _getNextTokenId();

The index is called by the getNextTokenId() which is a view function that returns
the totalSupply function and adds 1 to it.
The totalSupply function  is a function in the ERC721Token.sol which returns an all token Array which is an Array with all token ids used for enumeration.


_mint is an ERC721Token that creates tokens to an address who would own the monted token

tokenIdToLayers[newTokenIndex] = [newTokenIndex];
TokenIdLayer is a mapping that maps a token id to the layers representing it

 isUnique is an ERC721 functions which checks whether a composition is unique.
takes in 2 parameters 

line 75 - makes sure that token ids are true.
// Hash of all layers to track uniqueness of ethmojis
    mapping (bytes32 => bool) public compositions;

images hashes - tracks the uniqueness of the ethmoji images
.
/it is a Reverse mapping of token ID to image hash.
    mapping (uint256 => uint256) public tokenIdToImageHash;


tokenIdToImageHash[newTokenIndex] = _imageHash; 
using the newtokenIndex as the key, we are able to access the imageHash which is a parameter for the mint function to take place.

before the mint function is successfully executed, the paramaters must be satisfied.

emit BaseTokenCreated(newTokenIndex) - is alert button to inform the frontend that an action has taken place.

We had to get the newTokenIndex, we set values to the composition price, change rate and the changeablePrice..
# FUNCTION 2
![f2](https://user-images.githubusercontent.com/53812432/162450021-9d756540-774e-4224-8fc8-fec0102b6e1e.png)

This function mints a composition emoji
the array of layers that will make up the composition(NFT)

The compose function takes in a uint array of tokenIds and the uint imageHash. It is a public function that can be called by anyone and it is payable i.e it can receive ether. 
WhenNotPaused is a function modifier that is triggered when the contract is not paused.

uint256 price = getTotalCompositionPrice(_tokenIds);
PRICE = calls the getTotalCompositionPrice which is a function that gets the total price for minting a composition. 


it takes in a uint array of the desired layers which is our tokenId. 
it checks that the totalcompositionPrice = o;

 require(msg.sender != address(0) && msg.value >= price);
 It requires that the caller of the contact is not address 0( a burn address) and the amount(msg.value) is greater than price.
 It has a require statement that says the length of the token id should be less than Max Layer which is 100.

Line 97&98 sets the size to 0

if the actual size is less than the length of the tokens id, increment it.

isCompositionOnlyWithBaseLayers is a bool that lets us know if this contract
accepts making compositions with other compositions
# FUNCTION 3
[f3](https://user-images.githubusercontent.com/53812432/162450249-f1cf478d-3b49-4941-9551-699a530fcc5e.png)

requires the tokenId to return the TokenIdToLayers mappings
allows us to know the equivalent of a TokenId to Layers representing it.
Rline 102 - requires that tokenLayersExist and is accessible with the CompositionLayerID

# FUNCTION 4
![f4](https://user-images.githubusercontent.com/53812432/162450321-7a28a882-060d-4456-bb94-ffe0d8d989ef.png)

this Function  tells us if a composition(NFT) is valid and unique
takes in the _tokenIds and the _imageHash which we have gotten in Function 1

 isCompositionOnlyWithBaseLayers IS a boolean which lets us know Whether or not this contract accepts making compositions with other compositions. It returns 
 the function isValidBasedLayersOnly which does the following

# FUNCTION 5
![f5](https://user-images.githubusercontent.com/53812432/162450527-e2a2ec87-265e-4a3b-a172-76eb4561bb75.png)

Get the composition price of an idea and returns it.
Get total price for minting a composition given the array of desired layers

# FUNCTION 6
![f6](https://user-images.githubusercontent.com/53812432/162450573-7d223e0d-eb60-4acc-b01b-ad30de3e343f.png)
Sets the composition price for a token ID. 
    * Cannot be lower than the current composition fee

`totalCompositionPrice = SafeMath.add(totalCompositionPrice, tokenIdToCompositionPrice[_tokenIds[i]]);`

Add 
# FUNCTION 7
![f7](https://user-images.githubusercontent.com/53812432/162450648-1f0f9ed9-ed20-4fbc-8933-62ca7073a9e5.png)


Sets the composition price for a tokenID.
`require(tokenIdToCompPricePermission[_tokenId] == true);`

Cannot be lower than the current composition fee
 # FUNCTION 8
![f8](https://user-images.githubusercontent.com/53812432/162450680-8f63191a-cc26-4ecd-9a86-441157c1a168.png)

given an array of ids, returns whether or not this composition is valid and unique
for when only base layers are allowed
does not assume the layers array is flattened 


# FUNCTION 9
![f9](https://user-images.githubusercontent.com/53812432/162450744-e90c7242-203f-445f-a834-acdbcf75fd30.png)

# FUNCTION 10 
![f10](https://user-images.githubusercontent.com/53812432/162450796-ea4acb6c-1fd1-4074-824c-abfa1ef42dda.png)

 Trims the given array to a given size
it takes in an array of numebrs and a size number
It is a private function with pure functionality 

# FUNCTION 11
![f11](https://user-images.githubusercontent.com/53812432/162450843-46b0bbe2-373b-498b-b96e-c2182bf717c8.png)

checks if a token is an existing token by checking if it has non-zero layers
It takes in a token id number with its visibilty to set to private. it only reads the state . It has a check which returns a bool if
the length of the TokenIdtolayers mapping which is accessible by the toeknId is not equal to 0; 

# FUNCTION 12
![f12](https://user-images.githubusercontent.com/53812432/162450885-1334ef8c-822f-47e6-aa76-c13471f96dce.png)

set composition price for a token
It is a private function that takes in tokenId and price as its parameter. set
we set the price by accessing the `tokenIdToCompositionPrice mapping` with the tokenId keyy

Emits the event CompositionPriceChanged 

# FUNCTION 13
![f13](https://user-images.githubusercontent.com/53812432/162450941-a2d29cd7-80c8-4e15-983b-6ca25837d591.png)
FUNCTION 13 - set composition price increase rate a token

# FUNCTION 14
![f14](https://user-images.githubusercontent.com/53812432/162451005-c89e060a-8b14-4a4c-8501-41c32afe6b7c.png)
FUNCTION 14 - Set permission to change comp price in the future

# FUNCTION 15

![f15](https://user-images.githubusercontent.com/53812432/162451083-30365d5b-8e4b-4424-90fa-8925b19c7d2d.png)
FUNCTION 15  - calculates the next token ID based on totalSupply.
# FUNCTION 16
![f16](https://user-images.githubusercontent.com/53812432/162451125-59bb0cab-2746-4262-b0b1-662d31afd313.png)

FUNCTION 16 - given an array of ids, returns ig a composition is unique.

# FUNCTION 17
OWNER FUNCTIONS - these are the functions that can be called by the Contract Owner only.
![f17](https://user-images.githubusercontent.com/53812432/162451198-98f44f9c-792b-4b25-8ed7-e8d596b71522.png)


This function is for the contract owner to payout the profits to a given address. it can only be called by the contract owner.
I dont understand the totalPayment
The  contract owner sends the balance of the contract to an address.


# FUNCTION 18
![f18](https://user-images.githubusercontent.com/53812432/162451292-26c2af46-2008-4768-9fe0-91fcb18349a5.png)


This sets the default composition fee for all new tokens. Only The caller of the contract can call this function. If any other person calls it, it would throw an error.

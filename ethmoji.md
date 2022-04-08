## Opensea Ethmoji Contract Review

Opensea is a marketplace for the decentralized web where creators can list their Compositions (music, paintings, digital artworks) for sale as NFTs.
Contract `Ethmoji` inherits the characteristics of ERC721Token, Ownable, PullPayment and Pausable contract.

- `ERC721` -lays the base specifications for an NFT contract .
- `Ownable` - focuses on basic authorization functions. This helps us with user permissions.
- `Pullpayment` - instead of using send or transfer to send money, through this contract, we get to the asyncTransfer function which is called by the depositor to store an amount in a particular address. PullPayment makes use of the Escrow contract which is an Ownable contract.
The Escrow contract holds money deposited into an address till the payee withdraws it.
- `Pausable` - allows users to implement an emergency stop mechanisms which calls the pause function when the contract is not paused or unpause functions.

# Function mintTo
![1](https://user-images.githubusercontent.com/53812432/162506627-c07e3bfd-e967-48c2-9247-d10e71c6d20e.png)

It takes in the following parameters - address _to, _compositionPrice, _changeRate, _changeableCompPrice, _imageHash

- _to  = address of the future owner of the token
- _compositionPrice  = uint256 composition price for the new token
- _changeRate uint256 = the rate at which comp price increases after every use
- _changeableCompPrice bool = whether or not the comp price can be changed
- _imageHash uint256 = hash of the resulting image


This function creates a base token to an address with a given composition price.
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

# Function compose
![2](https://user-images.githubusercontent.com/53812432/162508460-f6915f46-8322-458c-9937-11cef7204805.png)


This function mints a composition emoji and it takes in the parameters.
- _tokenIds uint256[] the array of layers that will make up the composition
- _imageHash uint256 hash of the resulting image

 It is a public function that can be called by anyone and it is payable i.e it can receive ether. 
WhenNotPaused is a function modifier that is triggered when the contract is not paused.

uint256 price = getTotalCompositionPrice(_tokenIds);
PRICE = calls the getTotalCompositionPrice which is a function that gets the total price for minting a composition. 
it takes in a uint array of the desired layers which is our tokenId. 
it checks that the totalcompositionPrice = o;

 require(msg.sender != address(0) && msg.value >= price);
 It requires that the caller of the contact is not address 0( a burn address) and the amount(msg.value) is greater than price.
 It has a require statement that says the length of the token id should be less than Max Layer which is 100.
if the actual size is less than the length of the tokens id, increment it.

isCompositionOnlyWithBaseLayers is a bool that lets us know if this contract
accepts making compositions with other compositions.

# Function getTokenLayers
![3](https://user-images.githubusercontent.com/53812432/162509888-5588ad2e-a50c-42fc-a202-9c6fca9a0de5.png)


This function allows an address to withdraw its balance in the contract
It takes in _tokenId uint256 the token ID and returns uint256[] list of layers for a token.

requires the tokenId to return the TokenIdToLayers mappings
allows us to know the equivalent of a TokenId to Layers representing it.
requires that tokenLayersExist and is accessible with the CompositionLayerID

# Function isValidComposition
![4](https://user-images.githubusercontent.com/53812432/162510393-097ddf19-832f-4bc7-8f84-f6a5fb2fe153.png)

This Function  tells us if a composition is valid and unique.
takes in the paramters _tokenIds and the _imageHash which we have gotten in Function 1.
- _tokenIds uint256[] the array of layers that will make up the composition
- _imageHash uint256 hash of the resulting image


 `isCompositionOnlyWithBaseLayers` IS a boolean which lets us know Whether or not this contract accepts making compositions with other compositions. It returns 
 the function isValidBasedLayersOnly which does the following

# Function getCommpositionPrice
![5](https://user-images.githubusercontent.com/53812432/162510950-eb17ef3c-b5e0-4d37-b144-12d526256c85.png)

It takes in the paramter -  _tokenIds uint256[] the array of layers that will make up the composition
 `return tokenIdToCompositionPrice[_tokenId];`
Returns the composition price of a given token ID.


# Function getTotalCompositionPrice
![6](https://user-images.githubusercontent.com/53812432/162511492-1fcbc678-fb14-4451-9a30-be7c843802c5.png)

This function gets the total price for minting a composition given the array of desired layers
It takes in the paramter -  _tokenIds uint256[] the array of layers that will make up the composition.


Sets the composition price for a token ID. 
Cannot be lower than the current composition fee
`totalCompositionPrice = SafeMath.add(totalCompositionPrice, tokenIdToCompositionPrice[_tokenIds[i]]);`


# Function setCompositionPrice
![7](https://user-images.githubusercontent.com/53812432/162513089-4ec29cdd-29bb-4b8d-9568-51bd76817040.png)

This function Sets the composition price for a tokenID with two parameters 
- tokenId uint256 the token ID
-  _price uint256 the new composition price


`require(tokenIdToCompPricePermission[_tokenId] == true);`
 Cannot be lower than the current composition fee


 # Function _isValidBaseLayersOnly

![8](https://user-images.githubusercontent.com/53812432/162517671-ed2a8ccb-7c24-473e-97fe-14d2c1cbdcaf.png)
it takes in 2 parameters
- tokenIds uint256[] an array of token IDs
- _imageHash uint256 hash of the resulting image

This function tells us that given an array of ids, it returns whether or not this composition is valid and unique for when only base layers are allowed. It does not assume the layers array is flattened 


# Function _isValidWithCompositions
![9](https://user-images.githubusercontent.com/53812432/162518171-a0fe3694-5ae7-42d4-a446-5df01f4be532.png)

This function returns whether or not this composition is valid and unique when compositions are allowed.
It returns a bool that checks whether or not the composition is unique

# Function Trim
![10](https://user-images.githubusercontent.com/53812432/162518398-1025ce2d-c41c-4d38-a444-30686a2fbe86.png)

This private function Trims the given array to a given size.
it takes in an array of numebrs and a size number (_layers, _size)


# Function _tokenLayersExist
![11](https://user-images.githubusercontent.com/53812432/162518701-abbb37c3-78df-4871-9ae5-570ad6fd0b4d.png)

This function checks if a token is an existing token by checking if it has non-zero layers
It takes in a token id number with its visibilty to set to private. it only reads the state. It has a check which returns a bool ifthe length of the TokenIdtolayers mapping which is accessible by the toeknId is not equal to 0; 


# Function _setCompositionPrice
![12](https://user-images.githubusercontent.com/53812432/162518845-d8414ec7-d047-409a-a6cd-6929098c6215.png)

This function set composition price for a token
It is a private function that takes in tokenId and price as its parameter. set
we set the price by accessing the `tokenIdToCompositionPrice mapping` with the tokenId key.
Then we Emits the event `CompositionPriceChanged`


# Function _setCompositionPriceChangeRate
![13](https://user-images.githubusercontent.com/53812432/162519131-4de888d9-f4e8-42d8-bf09-268d4df54e62.png)

This function takes in 2 parameters (_tokenId uint256 which is the token ID and the  _changeRate uint256 composition price change rate) . This sets composition price increase rate a token.

# Function _setCompositionPriceChangePermission
![14](https://user-images.githubusercontent.com/53812432/162521034-d6fe0d82-f7f1-4f76-92d7-8b7fa7c0e780.png)

It takes in 2 parameters which does the following:
- _tokenId uint256 token ID
- _canChange bool whether or not the composition price can be manually changed

This sets the permission to change comp price in the future.

# Function _getNextTokenId
![15](https://user-images.githubusercontent.com/53812432/162522171-d7ce0f7b-6201-42b2-9c0c-203276775289.png)

It calculates the next token ID based on totalSupply.

# Function _isUnique
![16](https://user-images.githubusercontent.com/53812432/162522783-4f67d813-dfef-4b40-b98b-c3be76776d46.png)

this function shows that given an array of ids, returns whether a composition is unique. It assumes the layers are all base layers (flattened) and returns a bool to see  whether or not the composition is unique.


# Function payout
![17](https://user-images.githubusercontent.com/53812432/162524054-4f8e5196-16cc-434a-aaf5-196f0d561b85.png)

This function is for the contract owner to payout the profits to the inputted address. it can only be called by the contract owner. The  contract owner sends the balance of the contract to an address.


# Function setMinCompositionFee
![18](https://user-images.githubusercontent.com/53812432/162524582-ac660652-6280-405e-b924-e17e31fd5c53.png)

This sets the default composition fee for all new tokens. Only The caller of the contract can call this function. If any other person calls it, it would throw an error.

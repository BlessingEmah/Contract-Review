Opensea Ethmoji Contract Review

Opensea is a marketplace for the decentralized web where creators can list their Compositions (music, paintings, digital artworks) for sale as NFTs.

Contract `Ethmoji` inherits the characteristics of ERC721Token, Ownable, PullPayment and Pausable contract.


ERC721 -lays the base specifications for an NFT contract .

Ownable contract focuses on basic authorization functions. This helps us with user permissions.

Pullpayment contract - instead of using send or transfer to send money, through this contract, we get to the asyncTransfer function which is called by the depositor to store an amount in a particular address.
PullPayment makes use of the Escrow contract which is an Ownable contract.
The Escrow contract holds money deposited into an address till the payee withdraws it.


Pausable Contract allows users to implement an emergency stop mechanisms which calls the pause function when the contract is not paused or unpause functions




Function 1
This function creates a base token to an address with a given composition price.

uint256 newTokenIndex = _getNextTokenId();

The index is called by the getNextTokenId() which is a view function that returns
the totalSupply function and adds 1 to it.
The totalSupply function  is a function in the ERC721Token.sol which returns an all token Array which is an Array with all token ids used for enumeration.


_mint is an ERC721Token that creates tokens to an address who would own the monted token

tokenIdToLayers[newTokenIndex] = [newTokenIndex];
TokenIdLayer is a mapping that maps a token id to the layers representing it

 require(_isUnique(tokenIdToLayers[newTokenIndex], _imageHash));
 isUnique is an ERC721 functions which checks whether a composition is unique.
takes in 2 parameters 
 function _isUnique(uint256[] _layers, uint256 _imageHash) private view returns (bool) { 
        return compositions[keccak256(_layers)] == false && imageHashes[_imageHash] == 0;

line 75 - makes sure that token ids are true.
// Hash of all layers to track uniqueness of ethmojis
    mapping (bytes32 => bool) public compositions;

images hashes - tracks the uniqueness of the ethmoji images
.
 tokenIdToImageHash[newTokenIndex] = _imageHash; 
/it is a Reverse mapping of token ID to image hash.
    mapping (uint256 => uint256) public tokenIdToImageHash;


tokenIdToImageHash[newTokenIndex] = _imageHash; 
using the newtokenIndex as the key, we are able to access the imageHash which is a parameter for the mint function to take place.

before the mint function is successfully executed, the paramaters must be satisfied.

emit BaseTokenCreated(newTokenIndex) - is alert button to inform the frontend that an action has taken place.

_setCompositionPrice(newTokenIndex, _compositionPrice);
_setCompositionPriceChangeRate(newTokenIndex, _changeRate);
_setCompositionPriceChangePermission(newTokenIndex, _changeableCompPrice);
    
We had to get the newTokenIndex, we set values to the composition price, change rate and the changeablePrice..


2 IS A LOTTT
FUNCTION 2 - mintd a composition emoji
the array of layers that will make up the composition(NFT)

The compose function takes in a uint array of tokenIds and the uint imageHash. It is a public function that can be called by anyone and it is payable i.e it can receive ether. 
WhenNotPaused is a function modifier that is triggered when the contract is not paused.

uint256 price = getTotalCompositionPrice(_tokenIds);
PRICE = calls the getTotalCompositionPrice which is a function that gets the total price for minting a composition. 

BREAK DOWN
function getTotalCompositionPrice(uint256[] _tokenIds) public view returns(uint256) {
        uint256 totalCompositionPrice = 0;
        for (uint i = 0; i < _tokenIds.length; i++) {
            require(_tokenLayersExist(_tokenIds[i]));
            totalCompositionPrice = SafeMath.add(totalCompositionPrice, tokenIdToCompositionPrice[_tokenIds[i]]);
        }

        totalCompositionPrice = SafeMath.div(SafeMath.mul(totalCompositionPrice, 105), 100);

        return totalCompositionPrice;
    }

it takes in a uint array of the desired layers which is our tokenId. 
it checks that the totalcompositionPrice = o;

 require(msg.sender != address(0) && msg.value >= price);
 It requires that the caller of the contact is not address 0( a burn address) and the amount(msg.value) is greater than price.
 It has a require statement that says the length of the token id should be less than Max Layer which is 100.

Line 97&98 sets the size to 0

if the actual size is less than the length of the tokens id, increment it.

isCompositionOnlyWithBaseLayers is a bool that lets us know if this contract
accepts making compositions with other compositions

FUNCTION 3 
requires the tokenId to return the TokenIdToLayers mappings
allows us to know the equivalent of a TokenId to Layers representing it.
Rline 102 - requires that tokenLayersExist and is accessible with the CompositionLayerID



FUNCTION 4 tells us if a composition(NFT) is valid and unique
takes in the _tokenIds and the _imageHash which we have gotten in Function 1

 isCompositionOnlyWithBaseLayers IS a boolean which lets us know Whether or not this contract accepts making compositions with other compositions. It returns 
 the function isValidBasedLayersOnly which does the following


FUNCTION 5
Get the composition price of an idea and returns it.
Get total price for minting a composition given the array of desired layers

FUNCTION 6
Sets the composition price for a token ID. 
    * Cannot be lower than the current composition fee

`totalCompositionPrice = SafeMath.add(totalCompositionPrice, tokenIdToCompositionPrice[_tokenIds[i]]);`

Add 


FUNCTION 7

    function setCompositionPrice(uint256 _tokenId, uint256 _price) public onlyOwnerOf(_tokenId) {
        require(tokenIdToCompPricePermission[_tokenId] == true);
        _setCompositionPrice(_tokenId, _price);

Sets the composition price for a tokenID.
`require(tokenIdToCompPricePermission[_tokenId] == true);`

Cannot be lower than the current composition fee

FUNCTION 8

given an array of ids, returns whether or not this composition is valid and unique
for when only base layers are allowed
does not assume the layers array is flattened 



  function _isValidBaseLayersOnly(uint256[] _tokenIds, uint256 _imageHash) private view returns (bool) { 
        require(_tokenIds.length <= MAX_LAYERS);
        uint256[] memory layers = new uint256[](_tokenIds.length);

        for (uint i = 0; i < _tokenIds.length; i++) { 
            if (!_tokenLayersExist(_tokenIds[i])) {
                return false;
            }

            if (tokenIdToLayers[_tokenIds[i]].length != 1) {
                return false;
            }

            for (uint k = 0; k < layers.length; k++) { 
                if (layers[k] == tokenIdToLayers[_tokenIds[i]][0]) {
                    return false;
                }
                if (layers[k] == 0) { 
                    layers[k] = tokenIdToLayers[_tokenIds[i]][0];
                    break;
                }
            }
        }
    
        return _isUnique(layers, _imageHash);
    }
FUNCTION 9

FUNCTION 10 - Trims the given array to a given size
it takes in an array of numebrs and a size number
It is a private function with pure functionality 

FUNCTION 11
checks if a token is an existing token by checking if it has non-zero layers
It takes in a token id number with its visibilty to set to private. it only reads the state . It has a check which returns a bool if
the length of the TokenIdtolayers mapping which is accessible by the toeknId is not equal to 0; 


FUNCTION 12 -  set composition price for a token
It is a private function that takes in tokenId and price as its parameter. set
we set the price by accessing the `tokenIdToCompositionPrice mapping` with the tokenId keyy

Emits the event CompositionPriceChanged 

FUNCTION 13 - set composition price increase rate a token

FUNCTION 14 - Set permission to change comp price in the future

FUNCTION 15  - calculates the next token ID based on totalSupply.


FUNCTION 16 - given an array of ids, returns ig a composition is unique.

OWNER FUNCTIONS - these are the functions that can be called by the Contract Owner only.

Function 17 (unclear)
 function payout (address _to) public onlyOwner { 
        totalPayments = 0;
        address(_to).transfer(address(this).balance);
    }
This function is for the contract owner to payout the profits to a given address. it can only be called by the contract owner.
I dont understand the totalPayment
The  contract owner sends the balance of the contract to an address.


Function 18
Function setMinCompositionFee(uint256 _price) public onlyOwner {
minCompositionFee = _price;
	
}

This sets the default composition fee for all new tokens. Only The caller of the contract can call this function. If any other person calls it, it would throw an error.
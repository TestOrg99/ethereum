#  Solidity Hello World with Ethereum Wallet
---
The syntax for a contract in Solidity is really similar to a class in oriented object programming languages. A contract have functions we can call and variables we can store and read.
## steps:
- Counter contract will store the number of times it has been called and make the value available for everyone to read on the blockchain.
~~~sh
pragma solidity ^0.4.11;

contract Counter {

    /* define variable count of the type uint */
   uint count = 0;

    /* this runs when the contract is executed */
function increment() public {
       count = count + 1;
    }

    /* used to read the value of count */
function getCount() constant returns (uint) {
       return count;
    }

}
~~~

- To publish our contract on the blockchain, open Ethereum Wallet and go to My contracts.
- Click Deploy a new contract.
- In the code text area paste our Counter contract code.
- On the right select the contract you want to publish: our Counter contract.
- Enter your password and press Send transaction. The gas price is the amount of Ethereum that will be required to publish your contract to the block chain.
- Now if you execute our increment function counter value will be equal to 1. That may take some time as the execution of the code must be written in the blockchain when the next block is mined.
- If you execute the increment function another time you’ll see the counter value changing.


# Ethereum smart contracts lifecycle

### Birth of a contract

Same as classes in an object oriented programing language, smart contracts have also a constructor . The constructor is a function with the same name as the contract.
```sh

pragma solidity ^0.4.11;
contract power {
    uint value;
    /* this function is executed at initialization of the contract */
    function power(uint number, uint p) { 
      value = number ** p;
    }
    function getPower() constant returns (uint) {
       return value;
    }
}
```
The constructor  is really helpful to customize your contracts at the time you deploy it. A traditional class has most of the the time a destructor which is called when the object is destructed. It’s also possible with contracts.

### Who’s who?
 In the Ethereum blockchain, actors (smart contracts or wallets) are identified by their address . If you want to know the address  that called a function you can access it by using msg.sender . Storing addresses can let you implement logic depending on the transaction creator.
Let’s say we want to improve our Counter contract from the Hello World tutorial and only allow the creator of the contract to be able to update the counter. We’ll need to add two steps, a constructor that will store the address of the creator and a condition in the increment function to make sure it is the creator that call the function.
```sh
pragma solidity ^0.4.11;
contract Counter {
    uint count = 0;
    address owner; //Let's keep track of the owner
    function Counter() {
       owner = msg.sender; // We keep the address of the creator
    } 
    function increment() public {
       if (owner == msg.sender) { // We check who calls the function
          count = count + 1;
       }
    }
    /* used to read the value of count */
    function getCount() constant returns (uint) {
       return count;
}
}
```

### Death of a contract

Even the best things have an end and smart contracts can die too! When a contract is killed using the kill function it will not be possible to interact with it anymore. To kill a contract you need to to call selfdestruct(address) . Providing an address as the parameter let you transfer the remaining founds stored in the contract to the address.
When you implement a kill function to destroy a contract, checking the identity of the caller let you protect the contract from getting destroyed by anyone.
```sh
pragma solidity ^0.4.11;
contract Counter {
    uint count = 0;
    address owner;
    function Counter() {
       owner = msg.sender;
    }
    function increment() public {
       if (owner == msg.sender) {
          count = count + 1;
       }
    }
    function getCount() constant returns (uint) {
       return count;
    }
    function kill() {
       if (owner == msg.sender) { // We check who is calling
          selfdestruct(owner); //Destruct the contract
       }
    }
}
```
we can also use suicide()  instead of selfdestruct() . The suicide()  function has been renamed in order to have a more healthy language.
After few seconds if you try to access your contract, you’ll see that you can no longer interact with it anymore.








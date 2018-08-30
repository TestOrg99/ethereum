# Inheritance in Solidity

In Solidity, inheritance is pretty similar to classic oriented object programming languages. You’ll first write your base contract and tell that your new contract will inherit from the base one.

You also have to know that Solidity supports multiple inheritance by copying code including polymorphism. All function calls are virtual, which means that the most derived function is called, except when the contract name is explicitly given. When a contract inherits from multiple contracts, only a single contract is created on the blockchain, and the code from all the base contracts is copied into the created contract.

Let’s write our base contract: it will let us add easily ownership to our contracts. We’ll name it Ownable . The people at at OpenZeppelin wrote a lot of reusable code you can use in your smart contracts. The snippets are available through their tool or their Github repository.

Here is the code:
~~~sh
pragma solidity ^0.4.11;

/**
 * @title Ownable
 * @dev The Ownable contract has an owner address, and provides basic authorization control
 * functions, this simplifies the implementation of "user permissions".
 */
contract Ownable {

  address public owner;

  /**
   * @dev The Ownable constructor sets the original `owner` of the contract to the sender
   * account.
   */
  function Ownable() {
    owner = msg.sender;
  }


  /**
   * @dev Throws if called by any account other than the owner.
   */
  modifier onlyOwner() {
    require(msg.sender == owner);
    _;
  }


  /**
   * @dev Allows the current owner to transfer control of the contract to a newOwner.
   * @param newOwner The address to transfer ownership to.
   */
  function transferOwnership(address newOwner) onlyOwner {
    require(newOwner != address(0));      
    owner = newOwner;
  }

}
~~~
Another pattern we often write is the ability to destroy our contract and transfer the funds that were stored in the contract to the owner or to another address. What is important is that we don’t want to anyone to be able to destroy our contract, so our Destructible should inherit from Ownable . The inheritance is done with the is  keyword after the name of your smart contract.

You have to note that is Solidity, by default functions or are accessible from derived class. As in other programing language you can specify what should be accessible from the outside or from the derived contract. Functions can be specified as being external , public , internal  or private , where the default is public .

- external : External functions are part of the contract interface, which means they can be called from other contracts and via transactions. An external  function f cannot be called internally (i.e. f() does not work, but this.f() works). External functions are sometimes more efficient when they receive large arrays of data.
- public : Public functions are part of the contract interface and can be either called internally or via messages. For public state variables, an automatic getter function (see below) is generated.
- internal : Those functions and state variables can only be accessed internally (i.e. from within the current contract or contracts deriving from it), without using this.
- private : Private functions and state variables are only visible for the contract they are defined in and not in derived contracts.

Here is our second contract.
~~~sh
pragma solidity ^0.4.11;

/**
 * @title Destructible
 * @dev Base contract that can be destroyed by owner. All funds in contract will be sent to the owner.
 */
contract Destructible is Ownable {

  function Destructible() payable { } 

  /**
   * @dev Transfers the current balance to the owner and terminates the contract. 
   */
  function destroy() onlyOwner {
    selfdestruct(owner);
  }

  function destroyAndSend(address _recipient) onlyOwner {
    selfdestruct(_recipient);
  }
}
~~~
Now using those two base contracts, we’ll write a simple BankAccount contract where people can send money and the owner can withdraw it.
~~~sh
pragma solidity ^0.4.11;

contract BankAccount is Ownable, Destructible {

  function store() public payable {
      
  } 

  function withdraw(uint amount) public onlyOwner {
      if (this.balance >= amount) {
        msg.sender.transfer(amount);
      }
  } 

}
~~~
Note that we need to inherit from both contracts. The order of inheritance is important.  A simple rule to know the order is to specify the base classes in the order from “most base-like” to “most derived”.

 Here is the whole code we’ll deploy:
 ~~~sh
 pragma solidity ^0.4.11;

/**
 * @title Ownable
 * @dev The Ownable contract has an owner address, and provides basic authorization control
 * functions, this simplifies the implementation of "user permissions".
 */
contract Ownable {

  address public owner;

  /**
   * @dev The Ownable constructor sets the original `owner` of the contract to the sender
   * account.
   */
  function Ownable() {
    owner = msg.sender;
  }


  /**
   * @dev Throws if called by any account other than the owner.
   */
  modifier onlyOwner() {
    require(msg.sender == owner);
    _;
  }


  /**
   * @dev Allows the current owner to transfer control of the contract to a newOwner.
   * @param newOwner The address to transfer ownership to.
   */
  function transferOwnership(address newOwner) onlyOwner {
    require(newOwner != address(0));      
    owner = newOwner;
  }

}

/**
 * @title Destructible
 * @dev Base contract that can be destroyed by owner. All funds in contract will be sent to the owner.
 */
contract Destructible is Ownable {

  function Destructible() payable { } 

  /**
   * @dev Transfers the current balance to the owner and terminates the contract. 
   */
  function destroy() onlyOwner {
    selfdestruct(owner);
  }

  function destroyAndSend(address _recipient) onlyOwner {
    selfdestruct(_recipient);
  }
}

contract BankAccount is Ownable, Destructible {

  function store() public payable {
      
  } 

  function withdraw(uint amount) public onlyOwner {
      if (this.balance >= amount) {
        msg.sender.transfer(amount);
      }
  } 

}
 ~~~
 We can now deploy our bank account contract.After being deployed we can see that we see our bank account functions but also the inherited one.
 
 
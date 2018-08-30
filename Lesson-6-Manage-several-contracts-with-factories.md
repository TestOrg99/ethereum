# Manage several contracts with factories
Let’s create our contract CounterFactory  that will manage all the other Counters . It will contain a mapping that will associate an owner to the address of his counter contract.
~~~sh
mapping(address => address) counters;
~~~
When a user want to use our counter system to have his own cool counter he will need request the creation of his counter.
~~~sh
function createCounter() public {
    if (counters[msg.sender] == 0) {
       counters[msg.sender] = new Counter(msg.sender);
    }
}
~~~
Note that we pass to the constructor the address of it’s owner so we’ll transfer the ownership of the caller. In the constructor of the new contract, the msg.sender  will refer to the address of our contract factory. This is a really important point to understand as using a contract to interact with other contracts is a common practice. You should therefore take care of who is the sender in complex cases.

Now the increment function, we’ll first check if the user already registered a contract and call the increment function from the contract.  As the mapping store the address of the contract, we need to cast the address to the Counter contract type. Storing the address of the contract and not a direct reference to a contract permit us to check if the contract was initialized or not by checking it with a null address: 0 or 0x0..
~~~sh
function increment() public {
   require (counters[msg.sender] != 0);
   Counter(counters[msg.sender]).increment(msg.sender);
}
~~~
Finally to read the value of the counter we’ll take the address of the user as parameter to fetch the value of the counter.

~~~sh
function getCount(address account) public constant returns (uint) {
   if (counters[account] != 0) {
       return (Counter(counters[account]).getCount());
    }
}
~~~
In this example, we keep it simple but you could imagine few scenarios, for example require to send Ether to the createCounter()  function so the initial creator of the contract could get some earning for his hard work. We could also had the ability to the original creator to delete a counter, or to associate a contract to a string or a number.

The Counter contract was slightly edited to fit the new address passed as parameter.

Here is the complete code:

~~~sh
pragma solidity ^0.4.11;
 
contract Counter {
 
    address owner;
    address factory;
    uint count = 0;
 
    function Counter(address _owner) {
        owner = _owner;
        factory = msg.sender
    }
 
    modifier isOwner(address _caller) {
        require(msg.sender == factory);
        require(_caller == owner);
        _;
    }
    
    function increment(address caller) public isOwner(caller) {
       count = count + 1;
    }
 
    function getCount() constant returns (uint) {
       return count;
    }
 
}
 
contract CounterFactory {
 
    mapping(address => address) counters;
 
    function createCounter() public {
        if (counters[msg.sender] == 0) {
            counters[msg.sender] = new Counter(msg.sender);
        }
    }
    
    function increment() public {
        require (counters[msg.sender] != 0);
        Counter(counters[msg.sender]).increment(msg.sender);
    }
    
    function getCount(address account) public constant returns (uint) {
        if (counters[account] != 0) {
            return (Counter(counters[account]).getCount());
        }
    }
 
 
}
~~~ 

Note that if called to many times, our counter could possibly victim of an overflow. You should use the SafeMath library as much as possible to protect from this possible case.

To deploy our contract, you will need to provide both the code of the CounterFactory  and the Counter . When deploying you’ll need to select CounterFactory .

After executing the createCounter()  function from one of your accounts and calling the increment()  function in the reading part of the contract interface you’ll need to put the address of your account to read the value of your counter. You can now have one counter per account.

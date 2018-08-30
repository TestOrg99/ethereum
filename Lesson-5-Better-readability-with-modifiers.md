# Better readability with modifiers

Modifiers are functions you can apply to other functions. They are really useful to make sure some prerequisite are met before calling a function. Let’s take a simple example. In the contracts we already wrote we often have to check if the caller of a function if the owner of the contract:

~~~sh
    modifier isAdmin() {
        require(msg.sender == owner);
        _;
    }

    
    modifier isAdmin() {
        require(msg.sender == owner);
        _;
    }
 
    function increment() public isAdmin {
        count = count + 1;
    }
~~~
Our modifier looks like a function. The require()  function evaluate a condition and will throw an exception if the condition is not met which will stop the execution of our smart contract. The _  keyword tells the compiler to replace _  by the body of the function.

As same as a function, a modifier can accept parameters. Let’s say we could have multiple users and want to check the ownership.


~~~sh
    modifier onlyBy(address _account) {
        require(msg.sender == _account);
        _;
    }
 
    function increment() public onlyBy(owner) {
        count = count + 1;
    }
~~~
As you can see our modifier is more generic and let us do more.

You could also write the most generic modifier that only runs if any condition is true:
~~~sh
    modifier onlyIf(bool _condition) {
        require(_condition);
        _;
    }
 
    function increment() public onlyIf(msg.sender == owner) {
        count = count + 1;
    }
~~~
Multiple modifiers are applied to a function by specifying them in a whitespace-separated list and are evaluated in the order presented.
~~~sh
    modifier onlyIf(bool _condition) {
        require(_condition);
        _;
    }
 
    function increment() public onlyIf(msg.sender == owner) onlyIf(count < 200) {
        count = count + 1;
    }
~~~
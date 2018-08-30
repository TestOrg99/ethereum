# Handling users with mapping: a simple lottery example


The purpose of our lottery will be that multiple users (participants) will be able to send money to participate in a lottery. The more money the user will send, the higher chance he will have to win all the funds. When the owner of the lottery will decide to close the lottery, a winner will be chose and the total bets will be transferred to the winner.

To store each players bet, we’ll see a new data types which are mapping . A mapping  bind a key to a value. To declare you have to both specify the type of the key and the value. For example here we will store the money that belongs to an address:


~~~sh
mapping(address => uint) usersBet;
usersBet[msg.sender] = 10;
// usersBet[msg.sender] == 10
~~~
Unfortunately there is no way to iterate the values of a mapping  if the indexes are not linear and we know the number of records. So for iterating along our bets we will need to store separately the number of people who placed bets and the list of the address of the players in another mapping.

So we’ll store 3 variables plus the address of the owner:

~~~sh
mapping(address => uint) usersBet;
mapping(uint => address) users;
uint nbUsers = 0;
uint totalBets = 0;
 
address owner;
~~~
We’ll then make the Bet function. Same as normal accounts, smart contracts can contain Ethereum. Our function Bet needs to have the the payable modifier. We’ll talk more about modifiers in the next tutorial but it simply make it possible to send Ethereum to the contract when the function is called. The amount of sent Ether will be stored in msg.value.

So when the function is called, we’ll first check if some Ethereum was sent: msg.value > 0 . Then we will store the amount sent in the usersBet mapping. If the bet fro this user was equal to 0, we increment our nbUsers and store the address of the player so we can iterate through all the players when ending the lottery.


~~~sh
function Bet() public payable  {
    if (msg.value > 0) {
       if (usersBet[msg.sender] == 0) { // Is it a new player
          users[nbUsers] = msg.sender;
          nbUsers += 1;
       }
     usersBet[msg.sender] += msg.value;
     totalBets += msg.value;
    }
}
~~~
The last part of our basic lottery system will be to pick a winner. Our function EndLottery()  must be only accessible by the owner of the lottery. To keep it simple we’ll pick a random number between 0 and the total amount of Ethereum played. We’ll then iterate through the players and check who won. When a player is found, we’ll simply destroy the contract passing as an argument the winner’s address. He will receive all the Ethereum  that are in the contract.


~~~sh
function EndLottery() public {
    if (msg.sender == owner) {
        uint sum = 0; 
        uint winningNumber = uint(block.blockhash(block.number-1)) % totalBets;
        for (uint i=0; i < nbUsers; i++) {
           sum += usersBet[users[i]]; 
           if (sum >= winningNumber) {
               selfdestruct(users[i]);
               return;
           }
        }
    }
}
~~~
A special note to say that we used a really simple way to get or winningNumber, in real life applications – especially when handling money you need to use a better way for getting real random number.

Here is all the contract code put together:

~~~sh
pragma solidity ^0.4.11;

contract Lottery {

    mapping(address => uint) usersBet;
    mapping(uint => address) users;
    uint nbUsers = 0;
    uint totalBets = 0;

    address owner;

    function Lottery() {
        owner = msg.sender;
    }
    
    function Bet() public payable  {
        if (msg.value > 0) {
            if (usersBet[msg.sender] == 0) {
                users[nbUsers] = msg.sender;
                nbUsers += 1;
            }
            usersBet[msg.sender] += msg.value;
            totalBets += msg.value;
        }
    }
    
    function EndLottery() public {
        if (msg.sender == owner) {
            uint sum = 0;
            uint winningNumber = uint(block.blockhash(block.number-1)) % totalBets + 1;
            for (uint i=0; i < nbUsers; i++) {
                sum += usersBet[users[i]];
                if (sum >= winningNumber) {
                    selfdestruct(users[i]);
                    return;
                }
            }
        }
    }
    
}


 ~~~

So let’s deploy our contract and play with it. We’ll send Ethereum using the bet function with our two accounts. As the Bet function has the payable  modifier you’ll see we can add Ether when calling the function.
- After sending your Ethers, you’ll see that the contract now holds it.
- If you call the EndLottery() function from the account owner, the lottery gains will be transferred to the winner and the contract will be close.





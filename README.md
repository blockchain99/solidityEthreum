# solidityEthreum
* [solidity cheatsheet](https://reference.auditless.com/cheatsheet/) 
* [solidity cheatsheet & best practice](https://github.com/manojpramesh/solidity-cheatsheet)
* Solidity Visual Developer : A feature-rich plugin that makes smart contract audits go a lot smoother. Use this in combination with the vscode-solidity and MythX plugins for the ultimate audit workbench.
- [Facebook announces Libra cryptocurrency: All you need to know](https://techcrunch.com/2019/06/18/facebook-libra/)
- remix : https://remix.ethereum.org/
- ganache : HTTP://127.0.0.1:7545
- Truffle
As you already did, you migrated a contract. So Truffle can be used for contract compilation and migration. It aims for easy and fast migration.

- Geth
Is an Ethereum-client, which means that you can run your own private blockchain with it. You can adjust your needs by defining for example the amount of threads you offer for mining. Geth itself is a command line tool, which can run a full Ethereum node implemented in Go. It provides the command lines, a Json-rpc server and an interactive console, where you can run your own scripts written in javascript.(below cause errors!)
    - ``sudo geth --rpc --rpccorsdomain https://remix.ethereum.org``
    - To run Remix & a local Geth test node, use this command: ``sudo geth --rpc --rpccorsdomain="https://remix.ethereum.org" --rpcapi web3,eth,debug,personal,net --vmdebug --datadir ~/ethreum_bootcamp --dev console`` 

- Ganache : Developer Networks - unittesting, Fast, Non-persistent


If you want a GUI, where you can track all deployments and transactions on your blockchain, you can choose Ganache. It allows you to create your own private blockchain mainly for testing purposes. It is used for deployment-testing for example, because there are no real miners on a "ganache-blockchain", so you can test if your contracts work.

I would suggest you to use Geth and Truffle if you want to set up your own blockchain on your local node and then deploy some contracts on it. There are plenty of manuals online on how to set up your own local node. For example:

[Create your own local node with geth](https://hackernoon.com/hands-on-creating-your-own-local-private-geth-node-beginner-friendly-3d45902cc612)


[solidity extention for vscode](https://marketplace.visualstudio.com/items?itemName=JuanBlanco.solidity)

Ethreum network :

- Main Network: persistent , Real-blockchain, costs Money
- Test Networks: Persistent but can be deleted, real-blockchain, beta release

http://remix.ethereum.org/ with ganache excution command : ./ganache-2.4.0-linux-x86_64.Appimage 
- In remix, Web3 Provider Endpoint : http://127.0.0.1:7545 

- solidity cheatsheet
1. Data Types
Useful data types used to store state variables or local variables

```javascript
pragma solidity >=0.5.0;

contract DataTypes {
    // Boolean
    bool trueOrFalse;
    // Unsigned integer. default size is 256
    uint firstInteger;
    // The same as above, with definition
    uint256 secondInteger;
    // Signed integer, 128
    int128 thirdinteger;
    // Address, add keywork 'payable' to be able to send ether to it
    address myAddress;
    // Two byte array
    bytes2 twoBytesArray;
    // Sixteen byte array
    bytes16 sixteenBytesArray;
    // String
    string myString;
    // Array of strings (can be an array of any other data type)
    string[] myStringArray;
    // Enums
    enum direction { left, right, up, down }
}
```
* Mappings : 
Mappings are key, value pairs or associative arrays.
```javascript
pragma solidity >=0.5.0;

contract Mappings {

    // State variable
    mapping(address => uint) public myMapping;

    // Store a new value in the mapping
    function putThing(address _key, uint _value) public {
        myMapping[_key] = _value;
    }

    // Retrieve a value from the mapping using key to search
    function getThing(address _search) public view returns (uint){
        return myMapping[_search];
    }

}
```

* Structs : Structured data
```javascript
pragma solidity >=0.5.0;

contract Structs {
    
	// Define the Person struct
	struct Person {
		string name;
		uint8 age;
	}

	// Create a new person struct,
	// Access the age of the struct
	function personAge() external pure returns(uint8) {
		Person memory person = Person("Alex", 27);
		// Will return 27
		return person.age;
	}
}
```

* Require Statements : 
Require statements are essential conditions that must be true in order to continue executing the function. Use these to ensure that no ambiguities exist in the data before performing changes to the state.
```javascript
pragma solidity >=0.5.0;

contract RequireStatements {

    function someFunction(address _anAddress) external {
        // Require that the address given as a parameter is not equal to an empty addess.
        // If the address is empty, the whole transaction will not be completed and will be reverted
        require(_anAddress != address(0), "Not a valid address");
        ...
    }
}
```
* Within an Ethereum transaction, the zero-account is just a special case used to indicate that a new contract is being deployed. It is literally '0x0' set to the to field in the raw transaction.

    * Every Ethereum transaction, whether it's a transfer between two external accounts, a request to execute contract code, or a request to deploy a new contract, are encoded in the same way. A raw transaction object will look something like this:
```javascript
transaction = {
  nonce: '0x0', 
  gasLimit: '0x6acfc0', // 7000000
  gasPrice: '0x4a817c800', // 20000000000
  to: '0x0', //new contract will be created
  value: '0x0',
  data: '0xfffff'
};
```
* If to is set to something other than '0x0', this request will result in transferring ether to the address (if value is non-zero), and execute the function encoded in the data field. Remember, the address can either be a contract or an external account.

    * When the to address is the zero-address, a new contract will be created by executing the code in data (this is what is meant by "code that returns the code"). 

* Access Modifiers :
Access modifiers define who or what can access state variables and functions. They are public, external, private and internal.
```javascript
pragma solidity >=0.5.0;

contract AccessModifiers {
    // Public modifer exposes a getter for state variable
    string public myString;
    // Private variable only accessible within contract
    string private myPrivateString;

    // Internal functions can only be used within this contract
    function innerFunction() internal {}
    // External functions are part of the contract interface and can be called from outside
    function outerFunction() external {}
}
```
* Custom Modifiers :
As well as built-in Access Modifiers, you can also create your own. Here’s an example of an onlyOwner modifier, a commonly used concept.
```javascript
pragma solidity >=0.5.0;

contract CustomModifiers {

    // State variable
    address private owner;

	// Custom modifier requiring that the sender of the transaction is the owner
	// otherwise revert the transaction
    modifier onlyOwner {
        require(msg.sender == owner, "Only owner can call this function.");
        _;
    }

	// Constructor takes an address and stores it in the owner variable
    constructor(address _owner) public {
        owner = _owner;
    }

	// someFunction() uses the onlyOwner modifier to make use of our
	// custom definition in above onlyOwner modifier
    function someFunction() external onlyOwner { 
        ...
    }
}

```

* Inheritance : 
```javascript
pragma solidity >=0.5.0;

contract Vehicle {

}

contract Car is Vehicle {

}

contract Van is Vehicle {
    
}

* [solidity bitdgree](https://www.bitdegree.org/learn/solidity-functions)
```
*  access classifiers:

- public - all can access
- external - Cannot be accessed internally, only externally
- internal - only this contract and contracts deriving from it can access
- private - can be accessed only from this contract
*As you can notice private is a subset of internal and external is a subset of public.
* Event is an inheritable member of a contract. An event is emitted, it stores the arguments passed in transaction logs. 
*  These logs are stored on blockchain and are accessible using address of the contract till the contract is present on the blockchain.

II . geth
* 
-  IPC endpoint opened                      url=/home/ys/.ethereum/geth.ipc
1. terminal1: geth   
2. terminal2: geth attach (client module)- prompt :  > admin 
* Geth has three syncmodes.
1. first geth server running by $ geth 
  * in above command windows find geth.ipc path (IPC endpoint opened :url=/home/ys/.ethereum/geth.ipc)
2. then run geth client module (geth attach ipc:/home/ys/.ethereum/geth.ipc) -> if success : It can attach to geth. 
$ geth --syncmode "full"
$ geth --syncmode "fast"
$ geth --syncmode "light"
- We’ll run a light testnet node to familiarize ourselves with how to manage and interact with a node. To do so, simply run
$ geth --testnet --syncmode "light"  (default testnet-respen,see below)

* save geth file on specific HDD dir : $ geth --datadir $PATH
* [geth --testnet --syncmode "light"](https://ethereum.org/en/developers/tutorials/run-light-node-geth/) : default testnet- rospen

-----------------------------------------------
* geth --rinkeby --syncmode "light"  -> It works!!! , after run this geth server, need to run client by below command. 
   * then client moudle run by $geth attach /home/ys/.ethereum/rinkeby/geth.ipc  (It works!!!)
   * > admin
   * > personal.newAccount(); -> javascript command //Management API
      passphrase: lagestGunInKangWon_postechEntYr_MYInitialCapital_byul
   -> ethereum create new account : my address. "0x1988f138d5bd466ee38d24a00cfb91deed7c5932"
    - It saved in my hdd, ./ethreum/keystore -> if use mainnetwork, in this case, ~/.ethereum/rinkeby/keystore
    as "UTC--2020-11-13T18-22-37.442122432Z--1988f138d5bd466ee38d24a00cfb91deed7c5932 "
- Working with ethreum wallet, working with website that connects to blockchain ,
  * It's going via the browser to another blockchain node,
where It has javascript rpc interface then it will send command there, 
  * ex) crate new accounts for our website
- github, go-ethreum site:
  * Interface Document / Management API(personal.newAccount())
  * JSON RPC API (eth_syncing ,,, )

- Genesis.json
{
 "config": {
 "chainId": 15,
 "homesteadBlock": 0,
 "eip150Block": 0,
 "eip155Block": 0,
 "eip158Block": 0,
 "byzantiumBlock": 0,
 "constantinopleBlock": 0,
 "petersburgBlock": 0,
 "ethash": {}
 },
 "difficulty": "0x20000",
 "gasLimit": "0x8000000",
 "alloc": {}
}
-----------------------------------------------------------------------------------
- ys:/media/ys/hdd2/tmp/ex_private_network$ geth init ./genesis.json --datadir mychaindata

- ys:/media/ys/hdd2/tmp/ex_private_network$ geth --datadir ./mychaindata/ --nodiscover  (geth server with default 30303)
=======================================================================================================
-  ys:/media/ys/hdd2/tmp/ex_private_network$ geth --datadir ./mychaindata/ --nodiscover --port "30304"
  * shows : IPC endpoint opened   url=/media/ys/hdd2/tmp/ex_private_network/mychaindata/geth.ipc

  * netstat -an | grep '30303'  -> show results
     * $ fuser -k 30303/tcp
  * since 30303 already bind Error : --port "30304" (above is geth server, since I already run geth server(30303) )

- client : ys:/media/ys/hdd2/tmp/ex_private_network$ geth attach ipc:/media/ys/hdd2/tmp/ex_private_network/mychaindata/geth.ipc
Welcome to the Geth JavaScript console!
> eth.accounts
[]
> personal

> personal.newAccount()

> eth.accounts
["addr_of_acct"] , which can be found in /media/ys/hdd2/tmp/ex_private_network/mychaindata/keystore
---------------------
ys:/media/ys/hdd2/tmp/ex_private_network$ geth --datadir ./mychaindata/ --nodiscover --unlock 0 --mine 1
* unlock 0 : unlock first account in array, 

* Using geth light mode you can't mine coins. Light mode will broadcast transactions to the Eth network.
 --mine 1 error --------
You can mine in two ways:

1. Via RPC/IPC: Attach Geth with RPC and run miner.start() - in client 
$> geth attach [IPC/RPC] -> ys:/media/ys/hdd2/tmp/ex_private_network$ geth attach ipc:/media/ys/hdd2/tmp/ex_private_network/mychaindata/geth.ipc

$> miner.start()

2. geth server : but light mode -> not working !!
Geth command: --mine option along with geth other options. I'll recommend 1st option is best way. Because you can enable and disable any time. Still you can control mine via 1st step.
-----------------
https://docs.openzeppelin.com/contracts/2.x/crowdsales

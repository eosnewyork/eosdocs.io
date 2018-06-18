---
title: "Data Storage"
date: 2018-04-30T15:53:46-04:00
draft: false
weight: 8
---

### Storage
Performing CRUD operations against EOS smart contracts is achieved with the eosio::multi_index interface, which brings functionality which can be compared to a traditional database 


### To Do Smart Contract

In this article we will be creating a simple To Do list smart contract which enables the user to add a todo element e.g. "feed the cat" and mark it as complete. 

In the contracts directory, create a new folder "todo_contract" and a new file CPP file of the same name.
```
mkdir todo_contract
cd todo_contract
touch todo_contract.cpp
vim todo_contract.cpp
```

Alternatively you can use eosiocpp to generate a boiler plate for the project including the project folder. From your top-level project folder, execute the following:
```
eosiocpp -n todo_contract
```
This will give you the following folder structure:
```
.
├── todo_contract.cpp
└── todo_contract.hpp

0 directories, 2 files
```

We will be demonstrating Create, Update & Delete functionality with our ABI actions 
- create(author, id, description) 
- destroy(author, id)
- complete(author, id)

Our todo elements have 3 simple properties. 
```
{
	id,
	description,
	completed
}

```

which as a struct looks like

```
// @abi table todos i64
struct todo {
	uint64_t id;
	std::string description;
	uint64_t completed;

	uint64_t primary_key() const { return id; }
	EOSLIB_SERIALIZE(todo, (id)(description)(completed))
};
```

### Create function
The create function creates a new todo element using the [emplace method](https://github.com/EOSIO/eos/wiki/Persistence-API#emplace). The first parameter is the payer, a valid account that authorized the current action (and thus allowed to be billed for storage usage)

```
// @abi action
void create(account_name author, const uint32_t id, const std::string& description) {
	todos.emplace(author, [&](auto& new_todo) {
		new_todo.id  = id;
		new_todo.description = description;
		new_todo.completed = 0;
	});
```



### Destroy function

The [erase](https://github.com/EOSIO/eos/wiki/Persistence-API#erase) method will remove the object from the table and refund the existing payer for storage of it. 
```
// @abi action
void destroy(account_name author, const uint32_t id) {
	auto todo_lookup = todos.find(id);
	todos.erase(todo_lookup);

	eosio::print("todo#", id, " destroyed");
}
```

### Complete function

We complete our todo by simply changing the completed property from 0 to 1 with the [modify](https://github.com/EOSIO/eos/wiki/Persistence-API#modify-1) method. 

The 2nd parameter used in the modify method is the *payer* (account_name)

Passing 0 indicates the payer of the newly modified row is the same as the original. 

If the new payer is the same as the old, he only pays the difference between the old and new object. 

If the new payer is not the old, the old is refunded for the storage usage of the existing object and the new payer is liable.  

```
    // @abi action
    void complete(account_name author, const uint32_t id) {
      auto todo_lookup = todos.find(id);
      eosio_assert(todo_lookup != todos.end(), "Todo does not exist");

      todos.modify(todo_lookup, author, [&](auto& modifiable_todo) {
        modifiable_todo.completed = 1;
      });

      eosio::print("todo#", id, " marked as complete");
    }
```

## Table Set up

We will now set up the container, think of it as the table. 
```
typedef eosio::multi_index<N(todos), todo> todo_table;
todo_table todos;
```

## Deployment
Full code available from EOS Asia [here](https://github.com/eosasia/eos-todo/blob/master/contract/todo.cpp)

```
eosiocpp -o todo_contract.wast todo_contract.cpp
eosiocpp -g todo_contract.abi todo_contract.cpp
cleos --wallet-url http://wallet:5555 -u http://server:7777 set contract mynewaccount /eos/contracts/todo_contract -p mynewaccount
```

## Interaction
### Get 
```
cleos --wallet-url http://wallet:5555 -u http://server:7777 get table mynewaccount todo todos
```

### Create
```
cleos --wallet-url http://wallet:5555 -u http://server:7777 push action mynewaccount create '["mynewaccount", 1, "feed cat"]' -p mynewaccount
```

### Complete
```
cleos --wallet-url http://wallet:5555 -u http://server:7777 push action mynewaccount complete '["mynewaccount", 1]' -p mynewaccount
```

### Destroy
```
cleos --wallet-url http://wallet:5555 -u http://server:7777 push action mynewaccount destroy '["mynewaccount", 1]' -p mynewaccount
```

____________

More storage resources

* This video https://youtu.be/E3Tx2DseLGE by Object Computing
	* [Writing to tables (storage)](https://youtu.be/E3Tx2DseLGE?t=50m27s)  (50 minutes - 1 hr)
	* [Deleting from a table (storage)](https://youtu.be/E3Tx2DseLGE?t=1h00m00s)  (1 hr - 1h 3 minutes)
	* [Using an alternate table index (storage)](https://youtu.be/E3Tx2DseLGE?t=1h03m00s)  (1h 3 minutes - 1h 4 minutes)
	* [Defining who pays for he storage](https://youtu.be/E3Tx2DseLGE?t=56m36s)  (56 minutes - 58 minutes)

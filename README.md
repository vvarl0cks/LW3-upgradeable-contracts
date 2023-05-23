# 🆙 Upgradeable Smart Contracts and Proxy Patterns

We know that smart contracts on Ethereum are immutable, as the code is immutable and cannot be changed once it is deployed. But writing perfect code the first time around is hard, and as humans we are all prone to making mistakes. Sometimes even contracts which have been audited turn out to have bugs that cost them millions.

```shell
gitpod /workspace/LW3-upgradeable-contracts (main) $ npx hardhat test
Compiled 1 Solidity file successfully


  Lock
    Deployment
      ✔ Should set the right unlockTime (1038ms)
      ✔ Should set the right owner
      ✔ Should receive and store the funds to lock
      ✔ Should fail if the unlockTime is not in the future
    Withdrawals
      Validations
        ✔ Should revert with the right error if called too soon
        ✔ Should revert with the right error if called from another account
        ✔ Shouldn't fail if the unlockTime has arrived and the owner calls it
      Events
        ✔ Should emit an event on withdrawals
      Transfers
        ✔ Should transfer the funds to the owner

  ERC721 Upgradeable
    ✔ Should deploy an upgradeable ERC721 Contract (256ms)


  10 passing (1s)
```

# 🚀 We will now study Proxy patterns in detail

## 👀 Transparent Proxy Pattern
The Transparent Proxy Pattern is a simple way to separate responsibilities between Proxy and Implementation contracts. In this case, the upgradeTo function is part of the Proxy contract, and the Implementation can be upgraded by calling upgradeTo on the proxy thereby changing where future function calls are delegated to.

There are some caveats though. There might be a case where the Proxy Contract and Implementation Contract have a function with the same name and arguments. Imagine if Proxy Contract has a owner() function and so does Implementation Contract. In Transparent Proxy contracts, this problem is dealt by the Proxy contract which decides whether a call from the user will execute within the Proxy contract itself or the Implementation Contract based on the msg.sender global variable

So if the msg.sender is the admin of the proxy then the proxy will not delegate the call and will try to execute the call if it understands it. If it's not the admin address, the proxy will delegate the call to the Implementation Contract even if the matches one of the proxy's functions.

Issues with Transparent Proxy Pattern
As we know that the address of the owner will have to be stored in the storage and using storage is one of the most inefficient and costly steps in interacting with a smart contract every time the user calls the proxy, the proxy checks whether the user is the admin or not which adds unnecessary gas costs to majority of the transactions taking place.

## 👀 UUPS Proxy Pattern
The UUPS Proxy Pattern is another way to separate responsibilities between Proxy and Implementation contracts. In this case, the upgradeTo function is also part of the Implementation contract, and is called using a delegatecall through the Proxy by the owner.

In UUPS whether its the admin or the user, all the calls are sent to the Implementation Contract The advantage of this is that every time a call is made we will not have to access the storage to check if the user who started the call is an admin or not which improved efficiency and costs. Also because its the Implementation Contract you can customize the function according to your need by adding things like Timelock, Access Control etc with every new Implementation that comes up which couldn't have been done in the Transparent Proxy Pattern

## 👀 Issues with UUPS Proxy Pattern
The issue with this is now because the upgradeTo function exists on the side of the Implementation contract developer has to worry about the implementation of this function which may sometimes be complicated and because more code has been added, it increases the possibility of attacks. This function also needs to be in all the versions of Implementation Contract which are upgraded which introduces a risk if maybe the developer forgets to add this function and then the contract can no longer be upgraded.

```shell
888    888                      888 888               888
888    888                      888 888               888
888    888                      888 888               888
8888888888  8888b.  888d888 .d88888 88888b.   8888b.  888888
888    888     "88b 888P"  d88" 888 888 "88b     "88b 888
888    888 .d888888 888    888  888 888  888 .d888888 888
888    888 888  888 888    Y88b 888 888  888 888  888 Y88b.
888    888 "Y888888 888     "Y88888 888  888 "Y888888  "Y888

Welcome to Hardhat v2.14.0

✔ What do you want to do? · Create a JavaScript project
✔ Hardhat project root: · /workspace/LW3-upgradeable-contracts
✔ Do you want to add a .gitignore? (Y/n) · y
✔ Help us improve Hardhat with anonymous crash reports & basic usage data? (Y/n) · y

Project created

See the README.md file for some example tasks you can run

Give Hardhat a star on Github if you're enjoying it!

     https://github.com/NomicFoundation/hardhat

```

## <p align="center">[🤖 Warlocks!](http://warlocks.netlify.app)

# Solidity - Smart Contracts with Solidity

# "Looks like we've made our first contract!"


![solidity img 2](https://user-images.githubusercontent.com/83671629/135772300-353863c1-bfaf-4948-af19-ce3174535935.jpg)

## Background

My new startup has created its own Ethereum-compatible blockchain to help connect financial institutions, and the team wants to build smart contracts to automate some company finances to make everyone's lives easier, increase transparency, and to make accounting and auditing practically automatic.

Fortunately, I've been learning how to program smart contracts with Solidity! I will create 3 `ProfitSplitter` contracts. These contracts will do several things:

* Pay associate-level employees quickly and easily.

* Distribute profits to different tiers of employees.

* Distribute company shares for employees in a "deferred equity incentive plan" automatically.

## Files

* [`AssociateProfitSplitter.sol`](AssociateProfitSplitter.sol) — Level 1 code.

* [`TieredProfitSplitter.sol`](TieredProfitSplitter.sol) — Level 2 code.

* [`DeferredEquityPlan.sol`](DeferredEquityPlan.sol) — Level 3 code.

## Instructions

* **Level One** is an `AssociateProfitSplitter` contract. This will accept ether into the contract, and divide it evenly among associate-level employees. This will allow the human resources department to pay employees quickly and efficiently.

* **Level Two** is a `TieredProfitSplitter` that will distribute different percentages of incoming ether to employees at different tiers/levels. For example, the CEO gets paid 60%, CTO 25%, and Bob gets 15%.

* **Level Three** is a `DeferredEquityPlan` that models traditional company stock plans. This contract will automatically manage 1000 shares, with an annual distribution of 250 shares over four years for a single employee.



![Solidity-programming-blockchain-featured-image-osfy-Jan-21](https://user-images.githubusercontent.com/83671629/135947368-db9382a6-4527-422f-a6d6-471dd8d84f1b.jpg)

### Starting the project using Solidity

Navigating to the [Remix IDE](https://remix.ethereum.org) I created a new contract called `AssociateProfitSplitter.sol`.

While developing and testing this contract, I used the [Ganache](https://www.trufflesuite.com/ganache) development chain, and point MetaMask to `localhost:8545`, or replace the port with what you have set in your workspace.

### Level One: The `AssociateProfitSplitter` Contract

At the top of the contract, I defined the following `public` variables:

* `employee_one` — The `address` of the first employee. Maked sure to set this to `payable`.

* `employee_two` — Another `address payable` that represents the second employee.

* `employee_three` — The third `address payable` that represents the third employee.

Created a constructor function that accepts:

* `address payable _one`

* `address payable _two`

* `address payable _three`

Within the constructor, I set the employee addresses equal to the parameter values. This will allow to avoid hardcoding the employee addresses.

Next, created the following functions:

* `balance` — This function should be set to `public view returns(uint)`, and must return the contract's current balance. Since we should always be sending ether to the beneficiaries, this function should always return `0`. If it does not, the `deposit` function is not handling the remainders properly and should be fixed. This will serve as a test function of sorts.

* `deposit` — This function should be set to `public payable` check, ensuring that only the owner can call the function.

  * In this function, the following steps were performed:

    * Set a `uint amount` to equal `msg.value / 3;` in order to calculate the split value of the ether.

    * Transfer the `amount` to `employee_one`.

    * Repeat the steps for `employee_two` and `employee_three`.

    * Since `uint` only contains positive whole numbers, and Solidity does not fully support float/decimals, we must deal with a potential remainder at the end of this function, since `amount` will discard the remainder during division.

    * We may either have `1` or `2` wei left over, so transfer the `msg.value - amount * 3` back to `msg.sender`. This will re-multiply the `amount` by 3, then subtract it from the `msg.value` to account for any leftover wei, and send it back to human resources.

* Created a fallback function using `function() external payable`, and call the `deposit` function from within it. This will ensure that the logic in `deposit` executes if ether is sent directly to the contract. This is important to prevent ether from being locked in the contract, since we don't have a `withdraw` function in this use case.

#### Testing the contract

In the `Deploy` tab in Remix, deploy the contract to the local Ganache chain by connecting to `Injected Web3` and ensuring MetaMask is pointed to `localhost:8545`.

Filled in the constructor parameters with designated `employee` addresses.

Tested the `deposit` function by sending various values. watched the `employee` balances as I sent different amounts of ether to the contract and ensured the logic is executing properly.

![Screenshot trans](https://user-images.githubusercontent.com/83671629/135775912-3a11619d-a885-403b-8d28-eeb193dfd109.png)

![trans](https://user-images.githubusercontent.com/83671629/135777653-ec3fdc3b-0583-4cf4-8a39-6f22f7798700.png)

![2nd try 2](https://user-images.githubusercontent.com/83671629/135775951-a64986dc-1a65-4243-916e-a45abc1fd861.png)

![2nd try ganache](https://user-images.githubusercontent.com/83671629/135775955-d6bd0b99-4bad-4b52-be68-9b9e8986048c.png)

### Level Two: The `TieredProfitSplitter` Contract

In this contract, rather than splitting the profits between associate-level employees, I calculated rudimentary percentages for different tiers of employees (CEO, CTO, and Bob).

Using the code, within the `deposit` function, performed the following:

* Calculated the number of points/units by dividing `msg.value` by `100`.

  * This will allow us to multiply the points with a number representing a percentage. For example, `points * 60` will output a number that is ~60% of the `msg.value`.

* The `uint amount` variable will be used to store the amount to send each employee temporarily. For each employee, set the `amount` to equal the number of `points` multiplied by the percentage (say, 60 for 60%).

* After calculating the `amount` for the first employee, add the `amount` to the `total` to keep a running total of how much of the `msg.value` we are distributing so far.

* Then, transfer the `amount` to `employee_one`. Repeat the steps for each employee, setting the `amount` to equal the `points` multiplied by their given percentage.

* For example, each transfer should look something like the following for each employee, until after transferring to the third employee:

  * Step 1: `amount = points * 60;`

    * For `employee_one`, distribute `points * 60`.

    * For `employee_two`, distribute `points * 25`.

    * For `employee_three`, distribute `points * 15`.

  * Step 2: `total += amount;`

  * Step 3: `employee_one.transfer(amount);`

* Sent the remainder to the employee with the highest percentage by subtracting `total` from `msg.value`, and sending that to an employee.

* Deployed and tested the contract functionality by depositing various ether values (20 ether).

  * The provided `balance` function can be used as a test to see if the logic in the `deposit` function is valid. Since all of the ether should be transferred to employees, this function should always return `0`, since the contract should never store ether itself.


![level 2](https://user-images.githubusercontent.com/83671629/135946710-bf30f5e3-2868-48b1-b76a-0f7626244c91.png)

![Level 2_trans](https://user-images.githubusercontent.com/83671629/135946722-6112080b-ffb3-4696-96ca-f51549c78131.png)

![level 2 Ganache](https://user-images.githubusercontent.com/83671629/135946728-b4472921-dc49-473b-ae52-750013d88d89.png)

  * Note: 
  * I used ether to transfer to see the changes in the account clearly.
  * The 100 wei threshold is due to the way we calculate the points. If we send less than 100 wei, for example, 80 wei, `points` would equal `0` because `80 / 100` equals `0` because the remainder is discarded. We will learn more advanced arbitrary precision division later in the course. In this case, we can disregard the threshold as 100 wei is a significantly smaller value than the ether or Gwei units that are far more commonly used in the real world (most people aren't sending less than a penny's worth of ether).

### Level Three: The `DeferredEquityPlan` Contract

In this contract, we will be managing an employee's "deferred equity incentive plan," in which 1000 shares will be distributed over four years to the employee. We won't need to work with ether in this contract, but we will be storing and setting amounts that represent the number of distributed shares the employee owns, and enforcing the vetting periods automatically.

* **A two-minute primer on deferred equity incentive plans:** In this set-up, employees receive shares for joining and staying with the firm. They may receive, for example, an award of 1000 shares when joining, but with a four-year vesting period for these shares. This means that these shares would stay with the company, with only 250 shares (1000/4) actually distributed to and owned by the employee each year. If the employee leaves within the first four years, he or she would forfeit ownership of any remaining (“unvested”) shares.

  * If, for example, the employee only sticks around for the first two years before moving on, the employee’s account will end up with 500 shares (250 shares * 2 years), with the remaining 500 shares staying with the company. In this example, only half of the shares (and any distributions of company profit associated with them) actually “vested,” or became fully owned by the employee. The remaining half, which were still “deferred” or “unvested,” ended up fully owned by the company since the employee left midway through the incentive/vesting period.

  * Specific vesting periods, the dollar/crypto value of shares awarded, and the percentage equity stake (the percentage ownership of the company) all tend to vary according to the company, the specialized skills, or seniority of the employee, and the negotiating positions of the employee/company. If you receive an offer from a company offering equity (which is great!), just make sure you can clarify the current dollar value of those shares being offered (based on, perhaps, valuation implied by the most recent outside funding round). In other words, don’t be content with just receiving “X” number of shares without having a credible sense of what amount of dollars that “X” number represents. Be sure to understand your vesting schedule as well, particularly if you think you may not stick around for an extended period of time.

Using the code, the following were performed:

* Human resources will be set in the constructor as the `msg.sender`, since HR will be deploying the contract.

* Below the `employee` initialization variables at the top (after `bool active = true;`), set the total shares and annual distribution:

  * Created a `uint` called `total_shares` and set this to `1000`.

  * Created another `uint` called `annual_distribution` and set this to `250`. This equates to a four-year vesting period for the `total_shares`, as `250` will be distributed per year. Since it is expensive to calculate this in Solidity, we can simply set these values manually. We can tweak them as you see fit, as long as we can divide `total_shares` by `annual_distribution` evenly.

* The `uint start_time = now;` line permanently stores the contract's start date. We'll use this to calculate the vested shares later. Below this variable, set the `unlock_time` to equal `now` plus `365 days`. We will increment each distribution period.

* The `uint public distributed_shares` will track how many vested shares the employee has claimed and were distributed. By default, this is `0`.

* In the `distribute` function:

  * Add the following `require` statements:

    * Require that `unlock_time` is less than, or equal to, `now`.

    * Require that `distributed_shares` is less than the `total_shares` the employee was set for.

    * Ensure to provide error messages in your `require` statements.

  * After the `require` statements, add `365 days` to the `unlock_time`. This will calculate next year's unlock time before distributing this year's shares. We want to perform all of our calculations like this before distributing the shares.

  * Next, I set the new value for `distributed_shares` by calculating how many years have passed since `start_time` multiplied by `annual_distributions`. For example:

    * The `distributed_shares` is equal to `(now - start_time)` divided by `365 days`, multiplied by the annual distribution. If `now - start_time` is less than `365 days`, the output will be `0` since the remainder will be discarded. If it is something like `400` days, the output will equal `1`, meaning `distributed_shares` would equal `250`.

    * Made sure to include the parenthesis around `now - start_time` in your calculation to ensure that the order of operations is followed properly.

  * The final `if` statement provided checks in the case the employee does not cash out until 5+ years after the contract start, the contract does not reward more than the `total_shares` agreed upon in the contract.

* Deployed and tested the contract locally.


![level 3](https://user-images.githubusercontent.com/83671629/136112555-ca6501b9-4560-41d7-a46b-f3347df349ac.png)


![Level 3_depo](https://user-images.githubusercontent.com/83671629/136112564-69e00137-a246-4447-a6af-9b0f44791b04.png)


![Level3 Trans](https://user-images.githubusercontent.com/83671629/136112568-fde5d6f0-a923-4edb-a637-52e314a66ef6.png)

  * For this contract, test the timelock functionality by adding a new variable called `uint fakenow = now;` as the first line of the contract, then replace every other instance of `now` with `fakenow`. Utilize the following `fastforward` function to manipulate `fakenow` during testing.

  * Add this function to "fast forward" time by 100 days when the contract is deployed (requires setting up `fakenow`):

    ```solidity
    function fastforward() public {
        fakenow += 100 days;
    }
    ```

  * Once satisfied with the contract's logic, reverted the `fakenow` testing logic.

* Congratulate!! for building such complex smart contracts with Solidity!!

### Deploying the contracts to a live Testnet

Once you feel comfortable with your contracts, point MetaMask to the Kovan or Ropsten network. Make sure that you have test ether on this network!

After switching MetaMask to Kovan, deploy the contracts as before, and copy/keep a note of their deployed addresses. The transactions will also be in your MetaMask history, and on the blockchain permanently to explore later.
![Level3_meta](https://user-images.githubusercontent.com/83671629/136112604-6226f657-5fcf-4d51-8d63-f07fcd319dc1.png)


## Resources

Building the next financial revolution isn't easy, but we need your help. Don't be intimidated by the semicolons!

There are lots of great resources to learn Solidity. Remember, you are helping push the very edge of this space forward,
so don't feel discouraged if you get stuck! In fact, be proud that you are taking on such a challenge!

For some succinct and straightforward code snips, check out [Solidity By Example](https://github.com/raineorshine/solidity-by-example)

For a more extensive list of awesome Solidity resources, check out [Awesome Solidity](https://github.com/bkrem/awesome-solidity)

Another tutorial is available at [EthereumDev.io](https://ethereumdev.io/)

If you enjoy building games, here's an excellent tutorial called [CryptoZombies](https://cryptozombies.io/)


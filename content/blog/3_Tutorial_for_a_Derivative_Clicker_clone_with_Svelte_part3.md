---
title: 'Tutorial for an Idle game with Svelte (Part3)'
url: '/blog/tutorial-for-idle-game-svelte-part3'
date: Sat, 19 Oct 2019 20:00:00 +0000
draft: true
tags: [svelte,SPA,game,tutorial,idle]
featured: false
toc: true
summary: "Adding upgrades to the game."

---

# Part3: Adding upgrades to the game.

##  Introduction

This article is the third part of a tutorial aiming at discovering Svelte while building a game. I also wrote a short post on Svelte and the idea behind this tutorial. Below you can find the links to check those posts or other parts of the tutorial.

- [introduction to Svelte]({{< relref "3_Introduction_to_Svelte.md" >}})
- [part1: Start of the tutorial: making a Derivative Idle clone with Svelte]({{< relref "3_Tutorial_for_a_Derivative_Clicker_clone_with_Svelte.md" >}})
- [part2: Going further with Svelte using components and props]({{< relref "3_Tutorial_for_a_Derivative_Clicker_clone_with_Svelte_part2.md" >}})
- [part3: Adding upgrades to the game]({{< relref "3_Tutorial_for_a_Derivative_Clicker_clone_with_Svelte_part3.md" >}})

In this part we will focus mostly on how to implement an upgrade system.

## Upgrades

Now we will implement upgrades for our game. Upgrades are buyable bonuses that grants bonuses to production in many way. The most straightforward is giving a multiplier bonus to the production of a building. That's what we will be working on right now. Note that Clicker Idle upgrades gives a bonus to buildings depending on how many specific building has been bought by the player (not the total number of building the player has, because we have buildings producing other buildings). We will have to track that number bought to implement the upgrades.

 Also Derivative Clicker gives the same bonus to all the buildings on the same row (all the buildings on the top row are tier1 buildings, producing a currency, then tier2 buildings on the second row produces tier1 buildings and so on and so forth for tier3, tier4...).

An example: if the bonus is 1% bonus to tier1 building for each tier1 buildings bought, if you buy 5 "1th Derivative" and 5 "Combinatorics" buildings, you have 10% bonus to all of those buildings.

### Differentiating building bought and building total.

To do that first thing is to create a variable in `store.js` to handle the new information.

```js
/* store.js */
...
// first level buildings
export const firstDerivativeNum = writable(0);
export const firstDerivativeBoughtNum = writable(0);
export const combinatoricsNum = writable(0);
export const combinatoricsBoughtNum = writable(0);

// second level buildings
export const secondDerivativeNum = writable(0);
export const secondDerivativeBoughtNum = writable(0);
```

Now that we have that, we can define props for the buildings and adjust the template:

```html
<!-- Building.svelte -->
<script>
  // imports declarations
  ...
  
  // variables declarations
  ...
  
  // props declaration
  ...
  export let numberOfBuildingBought;
  
  // reactive declarations
  ...
  
  // functions declarations
  ...
  
  function updateNumbers(){
    ...
    numberOfBuildingBought.update(n => n + 1);
  }
  
  ...
</script>

...

<button ...>
  ...
  <p>Owned:{$numberOfBuilding} ({$numberOfBuildingBought})</p>
</button>
```

And now we can import the new stores and pass them to our Building components:

```html
<!-- App.svelte -->
<script>
  // import declarations
  ...
  import { money, proofs, firstDerivativeNum, firstDerivativeBoughtNum, secondDerivativeNum, secondDerivativeBoughtNum, combinatoricsNum, combinatoricsBoughtNum } from './store.js';
  ...
</script>

...


<Building name="1th Derivative" numberOfBuilding={firstDerivativeNum} numberOfBuildingBought={firstDerivativeBoughtNum}></Building>
<Building name="Combinatorics" currencyProduced={proofs} numberOfBuilding={combinatoricsNum} numberOfBuildingBought={combinatoricsBoughtNum}></Building>
<Building name="2nde Derivative" currencyProduced={firstDerivativeNum} numberOfBuilding={secondDerivativeNum} numberOfBuildingBought={secondDerivativeBoughtNum}></Building>
```

### Fixing an accidental  ~~bug~~ feature 

In the last part we introduced a bug. A building's cost is dependent on how many of that building there are. But now that other buildings can generate buildings themselves, that'd be better if the cost would be associated with how many buildings were bought, not how many there are in total. Because we already solved the issue of differentiating bought versus total, the fix is as simple as:

```html
<!-- Building.svelte -->
<script>
  ...
  // reactive declarations
  ...
  $: cost = ($numberOfBuildingBought + 1) * 5;
  ...
</script>
```

### A new component for upgrades

Let's define a new component. To do that, let's create a new file called `Upgrade.svelte`. 

Their states will be: 

- how many we bought (you can buy the same upgrade many times)
- they will have a cost that scale of of how many we bought
- they'll have a name

Upgrade component has a lot in common with the Building component, it is even a subset of it. We can copy the Building component and remove what we don't need. (Mostly anything that has to do with producing things).

The result is this:



```html
<!-- Upgrade.svelte -->
<script>
  // import declarations
  import { money } from './store.js';
  import { currencyToString } from './utils.js'
	
  // props declarations
  export let name;
  export let numberOfUpgradeBought;
	
  // reactive declarations
  $: cantBuy = cost > $money;
  $: cost = ($numberOfUpgradeBought + 1) * 5;
  
  // function declarations	
	
  // update the value of `currencyProduced` to the store, adding `n` to it.
  function updateMoney(n){
    money.update(m => m + n);
  }
	
  // update the values of `currencyProduced` and `numberBuildings`
  function updateNumbers(){
    updateMoney(-cost);
    numberOfUpgradeBought.update(n => n + 1);
  }
</script>

<style>
  button {
    outline: 1px solid black;
    background: aquamarine;
    cursor: pointer;
  }

  button.cantbuy {
    background: #555;
    color: #DDD;
    cursor: default;
  }
</style>

<button on:click={updateNumbers} class:cantbuy={cantBuy} disabled={cantBuy}>
  <h3>{name}</h3>
  <p>costs {cost}$.</p>
  <p>Owned:{$numberOfUpgradeBought}</p>
</button>
```

Then as usual we need to define new variables in the store to hold the number of upgrade bought:

```js
/* store.js */
...
// upgrades
export const firstRowBoughtNum = writable(0);
```

And finally let's use the new component in `App.svelte`:

```html
<!-- App.svelte -->
<script>
  // import declarations
  ...
  import Upgrade from './Upgrade.svelte';
  import { money, proofs, firstDerivativeNum, firstDerivativeBoughtNum, secondDerivativeNum, secondDerivativeBoughtNum, combinatoricsNum, combinatoricsBoughtNum, firstRowBoughtNum } from './store.js';
  ...
	
  // variable declarations

  // reactive declarations
  
  // function declarations
	
  // lifecycle functions
</script>

<style>
</style>

...

<div>
	<Upgrade name="First row" numberOfUpgradeBought={firstRowBoughtNum}></Upgrade>
</div>
```

I made another big ugly thing here, using the div around the `<Upgrade>` tag to push it down the page. We will refactor all those ugly bits at the end of the tutorial because let's face it it's not the most interesting things to spend time on right now. But keep in mind that this is not the nicest way to do it.

### Updating the production to take into account upgrades

Now we have to modify how `Building.svelte` calculate how it produces its currency.

We need to have a props to know the bonus given by the upgrade. 

Also we need to move things a bit, before we had a variable `buildingProduction = 1` which is gonna be replaced by a reactive declaration taking account the bonus given by the upgrade given as a prop, and this will impact `productionPerTick`. 

```html
<!-- Building.svelte -->
<script>
  // import declarations
  ...
  
  // variable declarations
  ...
  
  // props declarations
  ...
  export let upgradeBonus;

  // reactive declarations
  ...
  $: buildingProduction = 1 + Math.floor(1 * upgradeBonus);
  
  ...

</script>
```

We used `Math.floor()` to restrict the upgrade bonus to only whole number, so if a building produce initially 10 unit of currency each tick, with a bonus of 25%, it actually produces 12 unit per tick and not 12.5. For now I will let it that way, even so the upgrade is weaker than what is advertised, it is made to deal only with Integer (Num) and not Float or Double. Another way to do this is to calculate correctly the upgrade without flooring or rounding and to only apply the rounding on the number when we display it.  

Note also that is the formula we have twice the magic number `1` which refers to the initial number of unit produced by the building. I will let it that way for now, but in the future we will change it to allow for more flexibility when creating buildings.

Next we need to change `App.svelte` to calculate the upgrade bonus and pass it to the buildings. For now for each building of the first tier we will get 25% bonus. These numbers are for development purpose and will be changed when we will balance the game progression.

```html
<!-- App.svelte -->
<script>
  // import declaration
  ...
  
  // variables declaration
  let firstTierUpgradeBonusPerBuilding = 0.25;
  
  // reactives declarations
  $: firstTierBuildingBought = $firstDerivativeBoughtNum + $combinatoricsBoughtNum;
  $: firstTierBuildingUpgradeBonus = (firstTierBuildingBought * firstTierUpgradeBonusPerBuilding) * $firstRowUpgradeBoughtNum;
  
</script>

...

<Building name="1th Derivative" numberOfBuilding={firstDerivativeNum} numberOfBuildingBought={firstDerivativeBoughtNum} upgradeBonus={firstTierBuildingUpgradeBonus}></Building>
<Building name="Combinatorics" currencyProduced={proofs} numberOfBuilding={combinatoricsNum} numberOfBuildingBought={combinatoricsBoughtNum} upgradeBonus={firstTierBuildingUpgradeBonus}></Building>
<Building name="2nde Derivative" currencyProduced={firstDerivativeNum} numberOfBuilding={secondDerivativeNum} numberOfBuildingBought={secondDerivativeBoughtNum} upgradeBonus={0}></Building>

<div>
	<Upgrade name="First row" numberOfUpgradeBought={firstRowUpgradeBoughtNum}></Upgrade>
```

Great! Now we can buy upgrades for our first tier buildings :)

One thing that is far from perfect is `$: firstTierBuildingBought = $firstDerivativeBoughtNum + $combinatoricsBoughtNum;`. Every time we are gonna add a new tier1 building, we are gonna need to update this. For now we will keep it that way but maybe later on we will find a way to simplify this.

### Extending the upgrades for the second tier (and adding an initial cost to upgrades)

If you looked carefully at the last code snippet we used a placeholder for the upgrade bonus of "2nd Derivative" `<Building name="2nde Derivative" currencyProduced={firstDerivativeNum} numberOfBuilding={secondDerivativeNum} numberOfBuildingBought={secondDerivativeBoughtNum} upgradeBonus={0}></Building>`.

That would be better to implement the upgrade for that tier of building too and it is pretty straightforward.

First we need to add a new value in the store:

```js
/* store.js */
...
export const secondRowUpgradeBoughtNum = writable(0);
```

We will also modify `Upgrade.svelte` to make the initial cost of each upgrade different and we will also change the formula for the cost:

```html
<!-- Upgrade.svelte -->
<script>
  // import declarations
  ...
  
  // props declaration
  ...
  export let initialCost;
  
  // reactive declaration
  ...
  $: cost = initialCost * Math.pow(100, $numberOfUpgradeBought);
  
  ...
</script>

...
```

Then we can modify `App.svelte` to define the bonus for second tier building (100% bonus for each building of tier 2 bought).

```html
<!-- App.svelte -->
<script>
  // import declarations
  ...
  import { ..., secondRowUpgradeBoughtNum } from './store.js';
  ...
  
  // variable declarations
  ...
  let secondTierUpgradeBonusPerBuilding = 1;
  
  // reactive declarations
  ...
  $: secondTierBuildingBought = $secondDerivativeBoughtNum;
  $: secondTierBuildingUpgradeBonus = (secondTierBuildingBought * secondTierUpgradeBonusPerBuild) * $secondRowUpgradeBoughtNum;
 
  
  ...  
</script>

...

<div>
  <Building name="1th Derivative" numberOfBuilding={firstDerivativeNum} numberOfBuildingBought={firstDerivativeBoughtNum} upgradeBonus={firstTierBuildingUpgradeBonus}></Building>
  <Building name="Combinatorics" currencyProduced={proofs} numberOfBuilding={combinatoricsNum} numberOfBuildingBought={combinatoricsBoughtNum} upgradeBonus={firstTierBuildingUpgradeBonus}></Building>
</div>

<div>
  <Building name="2nde Derivative" currencyProduced={firstDerivativeNum} numberOfBuilding={secondDerivativeNum} numberOfBuildingBought={secondDerivativeBoughtNum} upgradeBonus={secondTierBuildingUpgradeBonus}></Building>
</div>

<div>
  <Upgrade name="First row" numberOfUpgradeBought={firstRowUpgradeBoughtNum} initialCost={100}></Upgrade>
  <Upgrade name="Second row" numberOfUpgradeBought={secondRowUpgradeBoughtNum} initialCost={10000}></Upgrade>
</div>
```

This time we pass an initial cost to the upgrade so that the first upgrade cost 100 time less than the second.

Also I put the Buildings into div to make the second tier building on a row below the first tier building. As usual, it is to not use CSS now, but at the end we'll do a cleanup phase to use proper semantic for HTML and use CSS when needed.

### Add a second tier building

A last thing before wrapping up this part, we'll add a second tier2 building that will produce `Combinatorics` to make things even (two tier1, two tier2 and two upgrades). To do that we need to have the proper values in `store.js` then adding the component into `App.svelte`.

```js
/* store.js */
...
//second level buildings
...
export const probabilityNum = writable(0);
export const probabilityBoughtNum = writable(0);

...
```

```html
<!-- App.svelte -->
<script>
  // import declarations
  ...
  import { money, ..., , probabilityNum, probabilityBoughtNum, ...} from './store.js';
  ...
  
  // variable declarations
  ...
          
  // reactive declarations
  ...
  $: secondTierBuildingBought = $secondDerivativeBoughtNum + $probabilityBoughtNum;
  ...
</script>

...



```

This works fine and to finalize it, we should modify the function that display the currency the building is producing:

```js
/* utils.js */
import { ..., combinatoricsNum } from './store.js' 

// Returns a string representing `currency`. If `c` is not recognized, returns '?'
export function currencyToString(c){
  ...
    
  ...
  else if (c === combinatoricsNum)
    result = " Combinatorics"

  ...
}
```

That will display nicely on the new building that he is producing "Combinatorics".

## Recap

It's the end of this part, this time we implemented a new tier2 building and a basic upgrade system. The full code follows:

```html
<!-- App.svelte -->
<script>
  // import declarations
  import Building from './Building.svelte';
  import Upgrade from './Upgrade.svelte';
  import { money, proofs, firstDerivativeNum, firstDerivativeBoughtNum, secondDerivativeNum, secondDerivativeBoughtNum, combinatoricsNum, combinatoricsBoughtNum, probabilityNum, probabilityBoughtNum, firstRowUpgradeBoughtNum, secondRowUpgradeBoughtNum } from './store.js';
  import { currencyToString } from './utils.js';
	
  // variable declarations
  let firstTierUpgradeBonusPerBuilding = 0.25;
  let secondTierUpgradeBonusPerBuilding = 1;

  // reactive declarations
  $: firstTierBuildingBought = $firstDerivativeBoughtNum + $combinatoricsBoughtNum;
  $: secondTierBuildingBought = $secondDerivativeBoughtNum + $probabilityBoughtNum;
  $: firstTierBuildingUpgradeBonus = (firstTierBuildingBought * firstTierUpgradeBonusPerBuilding) * $firstRowUpgradeBoughtNum;
  $: secondTierBuildingUpgradeBonus = (secondTierBuildingBought * secondTierUpgradeBonusPerBuilding) * $secondRowUpgradeBoughtNum;
	
  // function declarations
	
  // lifecycle functions
</script>

<style>
</style>

<h1>You have {$money}{currencyToString(money)} and {$proofs}{currencyToString(proofs)}!</h1>

<div>
  <Building name="1th Derivative" numberOfBuilding={firstDerivativeNum} numberOfBuildingBought={firstDerivativeBoughtNum} upgradeBonus={firstTierBuildingUpgradeBonus}></Building>
  <Building name="Combinatorics" currencyProduced={proofs} numberOfBuilding={combinatoricsNum} numberOfBuildingBought={combinatoricsBoughtNum} upgradeBonus={firstTierBuildingUpgradeBonus}></Building>
</div>

<div>
  <Building name="2nde Derivative" currencyProduced={firstDerivativeNum} numberOfBuilding={secondDerivativeNum} numberOfBuildingBought={secondDerivativeBoughtNum} upgradeBonus={secondTierBuildingUpgradeBonus}></Building>
  <Building name="Probability" currencyProduced={combinatoricsNum} numberOfBuilding={probabilityNum} numberOfBuildingBought={probabilityBoughtNum} upgradeBonus={secondTierBuildingUpgradeBonus}></Building>
</div>

<div>
  <Upgrade name="First row" numberOfUpgradeBought={firstRowUpgradeBoughtNum} initialCost={100}></Upgrade>
  <Upgrade name="Second row" numberOfUpgradeBought={secondRowUpgradeBoughtNum} initialCost={10000}></Upgrade>
</div>
```

Building:

```html
<!-- Building.svelte -->
<script>
  // import declarations
  import { onMount } from 'svelte';
  import { money } from './store.js';
  import { currencyToString } from './utils.js'
	
  // variable declarations
  let tickSpeed = 1000;
	
  // props declarations
  export let name;
  export let currencyProduced = money;
  export let numberOfBuilding;
	export let numberOfBuildingBought;
	export let upgradeBonus;
	
  // reactive declarations
  $: cantBuy = cost > $money;
  $: cost = ($numberOfBuildingBought + 1) * 5;
  $: buildingProduction = 1 + Math.floor(1 * upgradeBonus);  // currency produced per building per tick
  $: productionPerTick = $numberOfBuilding * buildingProduction;
  
  // function declarations

  // update the value of `currencyProduced` to the store, adding `n` to it.
  function updateMoney(n){
    money.update(m => m + n);
  }
	
  // update the value of `currencyProduced` to the store, adding `n` to it.
  function updateCurrencyProduced(n){
    currencyProduced.update(m => m + n);
  }
	
  // update the values of `currencyProduced` and `numberBuildings`
  function updateNumbers(){
    updateMoney(-cost);
    numberOfBuilding.update(n => n + 1);
    numberOfBuildingBought.update(n => n + 1);
  }
	
  // update `currencyProduced` with `productionPerTick` and set a timeout to call itself after `tickSpeed` ms
  function launchTimeout(){
    updateCurrencyProduced(productionPerTick);
    setTimeout(launchTimeout, tickSpeed);
  }
  
  // lifecycle functions
  onMount(() => {
    launchTimeout();
  }); 
</script>

<style>
  button {
    outline: 1px solid black;
    background: aliceblue;
    cursor: pointer;
  }

  button.cantbuy {
    background: #555;
    color: #DDD;
    cursor: default;
  }
</style>

<button on:click={updateNumbers} class:cantbuy={cantBuy} disabled={cantBuy}>
  <h3>{name}</h3>
  <p>{buildingProduction}{currencyToString(currencyProduced)}/tick: costs {cost}$.</p>
  <p>Owned:{$numberOfBuilding} ({$numberOfBuildingBought})</p>
	
</button>
```

Upgrade:

```html
<!-- Upgrade.svelte -->
<script>
  // import declarations
  import { money } from './store.js';
  import { currencyToString } from './utils.js'
	
  // props declarations
  export let name;
  export let numberOfUpgradeBought;
  export let initialCost;
	
  // reactive declarations
  $: cantBuy = cost > $money;
  $: cost =  initialCost * Math.pow(100, $numberOfUpgradeBought);
  
  // function declarations	
	
  // update the value of `currencyProduced` to the store, adding `n` to it.
  function updateMoney(n){
    money.update(m => m + n);
  }
	
  // update the values of `currencyProduced` and `numberBuildings`
  function updateNumbers(){
    updateMoney(-cost);
    numberOfUpgradeBought.update(n => n + 1);
  }
</script>

<style>
  button {
    outline: 1px solid black;
    background: aquamarine;
    cursor: pointer;
  }

  button.cantbuy {
    background: #555;
    color: #DDD;
    cursor: default;
  }
</style>

<button on:click={updateNumbers} class:cantbuy={cantBuy} disabled={cantBuy}>
  <h3>{name}</h3>
  <p>costs {cost}$.</p>
  <p>Owned:{$numberOfUpgradeBought}</p>
</button>
```

store:

```js
/* store */
import { writable } from 'svelte/store';

export const money = writable(20);
export const proofs	= writable(0);

// first level buildings
export const firstDerivativeNum = writable(0);
export const firstDerivativeBoughtNum = writable(0);
export const combinatoricsNum = writable(0);
export const combinatoricsBoughtNum = writable(0);

// second level buildings
export const secondDerivativeNum = writable(0);
export const secondDerivativeBoughtNum = writable(0);
export const probabilityNum = writable(0);
export const probabilityBoughtNum = writable(0);

// upgrades
export const firstRowUpgradeBoughtNum = writable(0);
export const secondRowUpgradeBoughtNum = writable(0);

```

And finally utils:

```js
/* utils.js */
import { money, proofs, firstDerivativeNum, combinatoricsNum } from './store.js' 

// Returns a string representing `currency`. If `currency` is not recognized, returns '?'
export function currencyToString(c){
	let result = "?";

	if (c === money)
		result = "$";
	else if (c === proofs)
		result = " proofs"
	else if (c === firstDerivativeNum)
		result = " 1st Derivative"
	else if (c === combinatoricsNum)
		result = " Combinatorics"

	return result;
}
```

The result in picture:

{{< figure src="/img/blog_3_part3_final_recap.png" alt="Result of the third part tutorial" >}}

You can also play with the game and the code in the [Svelte REPL](https://svelte.dev/repl/ae7ab07c14804b9f92b631dca91bd129?version=3.4.2) or just enjoy [the game made so far](http://tuto-derivative-clicker-part3.surge.sh/) (hosted by Surge).

## Going further

If you want to go further and see if you understood how the game works, here is some elements you can try to add to the game:

-  another tier1 building that cost `proof`  and generate money(called "Computer" if you want to follow closely Derivative Clicker).
- another tier2 building that costs `proof` and generates `Computer` (see the point above)  called "Assembly line".

## What's next?

Next time we will try our hands at implementing a prestige layer :) If you did not remember from the introduction or did not read it, you can go read about what is a prestige layer in the [introduction post]({{< relref "3_Introduction_to_Svelte.md" >}}).

The next part is here: COMING SOON


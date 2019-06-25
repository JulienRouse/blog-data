---
title: 'Tutorial for a Idle game with Svelte (Part3)'
url: '/blog/tutorial-for-idle-game-svelte-part3'
date: Sat, 29 Jun 2019 20:00:00 +0000
draft: true
tags: [svelte,SPA,game,tutorial,idle]
featured: false
toc: true
summary: "Adding upgrades and a prestige layer to the game."

---

# Part3: Adding upgrades and a prestige layer to the game.

##  Introduction

## Upgrades

Now we will implement upgrades for our game. Upgrades are buyable bonuses that grants bonuses to production in many way. The most straightforward is giving a multiplier bonus to a building production. That's what we will be working on right now. Note that Clicker Idle upgrades gives a bonus to building depending on how many specific building has been bought by the player (not the total number of building the player has, because we have buildings producing other buildings). We will have to track that number bought to implement the upgrades.

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

### Fixing an accidental ~bug~ feature 

In the last part we introduced a bug. A building's cost is dependent on how many building there are. But now that other buildings can generate buildings themselves, that'd be better if the cost would be associated with how many buildings were bought, not how many there are in total. Because we already solved the issue of differentiating bought versus total, the fix is as simple as:

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

 

## Prestige layer


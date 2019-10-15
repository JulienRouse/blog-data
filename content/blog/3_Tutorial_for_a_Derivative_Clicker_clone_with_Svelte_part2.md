---
title: 'Tutorial for an Idle game with Svelte (Part2)'
url: '/blog/tutorial-for-idle-game-svelte-part2'
date: Sat, 12 Oct 2019 20:00:00 +0000
draft: false
tags: [svelte,SPA,game,tutorial,idle]
featured: false
toc: true
summary: "Diving deeper into Svelte, continuing our idle game."
---

# Part2: Going further with Svelte using components and props

## Introduction

This article is the second part of a tutorial aiming at discovering Svelte[1] while building a game. I also wrote a very short post on Svelte and the idea behind this tutorial. Below you can find the links to check those posts or other parts of the tutorial.

- [introduction to Svelte]({{< relref "3_Introduction_to_Svelte.md" >}})
- [part1: Start of the tutorial: making a Derivative Idle clone with Svelte]({{< relref "3_Tutorial_for_a_Derivative_Clicker_clone_with_Svelte.md" >}})
- [part2: Going further with Svelte using components and props]({{< relref "3_Tutorial_for_a_Derivative_Clicker_clone_with_Svelte_part2.md" >}})

## Objectives for this part

In this part we will learn how to use *components*, *props* and a *state store* to grow our game further.  

### Make more buildings using components

Right now we have one building producing money. That's a start but it is not enough. What we want now is to make many more buildings.

One way to do that would be to copy paste the following code to add a new building:

```html
<-- App.svelte -->
<button on:click={updateNumbers} class:cantbuy={cantBuy} disabled={cantBuy}>
  <p>{numberOfBuilding} buildings bought, next one cost {cost}$.</p>
  <p>You gain {productionPerTick}$ / tick.</p>
</button>

<button on:click={updateNumbers} class:cantbuy={cantBuy} disabled={cantBuy}>
  <p>{numberOfBuilding} buildings bought, next one cost {cost}$.</p>
  <p>You gain {productionPerTick}$ / tick.</p>
</button>
```

Try it yourself, it makes a second building. The problem is that the two buildings are identical, and they share their internal state. The number of buildings bought, the cost of it, how much money it generates each tick...

We need a way to encapsulate that information locally to have each building handle this for itself.

And that is what components are for! (It's the same concept in React[2] and Angular[3]) When you need to repeat the same independent elements (with slight variations) in different places, components are the way to go. They are also a nice way to have *building block* (think Lego) for your application. Usually you can have big components for the header, footer and main page. Then you can have smaller components like forms and menus that you can reuse in bigger components. And you can also compose your smaller components from even smaller components. It creates a tree architecture, with the root being often named `App`, the main entry point to your app, and then the leaf and nodes are other components used to build your app. 

To create one,  you need to create a `.svelte` file. (You can see that `App.svelte` is our root component here).Then inside the component you can declare some logic inside the `<script>` tag, some style inside the `<style>` tag and then an html template. The script and style part are local to the component by default. You can declare a new component `Test.svelte` that look like that:

```html
<!-- Test.svelte -->
<style>
  button {
    background: red;
  }
</style>

<button>Test button</button>
```

Then import it in our `App.svelte` to use it:

```html
<!-- App.svelte -->
<script>
  // import declarations
  ...
  import Test from './Test.svelte';
</script>

<style>
  ...
</style>

<button on:click={updateNumbers} class:cantbuy={cantBuy} disabled={cantBuy}>
  <p>{numberOfBuilding} buildings bought, next one cost {cost}$.</p>
  <p>You gain {productionPerTick}$ / tick.</p>
</button>

<button on:click={updateNumbers} class:cantbuy={cantBuy} disabled={cantBuy}>
  <p>{numberOfBuilding} buildings bought, next one cost {cost}$.</p>
  <p>You gain {productionPerTick}$ / tick.</p>
</button>

<Test></Test>
```

And the result:

{{< figure src="/img/blog_3_part2_component_local_style.png" alt="Showing components have scoped css" >}}

You see that even so we declared a red background for `<button>`, because we declared it inside the component, the CSS is scoped and don't interfere with the other elements on the page.

Let's go back to making a component for our buildings. You can delete `Test.svelte`.  You can also delete in `App.svelte` the import to `Test.svelte` and the `<Test></Test>` code in the template.

Now let's create a component for our building. Create a file named `Building.svelte` and we are gonna remove every part relating to our building from `App.svelte` to put it inside `Building.svelte`.

 We are gonna remove everything from `App.svelte` except we are going to import the Building component, keep the declaration for money and in the template keep the title and insert two Building components.

```html
<!-- App.svelte -->
<script>
  // import declarations
  import Building from './Building.svelte';
	
  // variable declarations
  let money = 20;
	
  // reactive declarations
  
  // function declarations
   
  // lifecycle functions
</script>

<style>
</style>

<h1>You have {money} $!</h1>

<Building></Building>
<Building></Building>
```

And inside the `Building.svelte` we have now:

```html
<!-- Building.svelte -->
<script>
  // import declarations
  import { onMount } from 'svelte';
	
  // variable declarations
  let money = 20;
  let numberOfBuilding = 0;
  let buildingProduction = 1; // $ produced per building per tick
  let tickSpeed = 1000;
	
  // reactive declarations
  $: cantBuy = cost > money;
  $: cost = (numberOfBuilding + 1) * 5;
  $: productionPerTick = numberOfBuilding * buildingProduction;
  
  // function declarations
  
  // update the values of `money` and `numberBuildings`
  function updateNumbers(){
    money -= cost;
    numberOfBuilding += 1
  }
	
  // update `money` with `productionPerTick` and set a timeout to call itself after `tickSpeed` ms
  function updateMoney(){
    money += productionPerTick;
    setTimeout(updateMoney, tickSpeed);
  }
  
  // lifecycle functions
  onMount(() => {
    updateMoney();
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
  <p>{numberOfBuilding} buildings bought, next one cost {cost}$.</p>
  <p>You gain {productionPerTick}$ / tick.</p>
</button>
```

You can see we have kept only one `<button>` inside `Building.svelte` but we have inserted `<Building></Building>` twice in `App.svelte` to have two independent buildings.

That's great! But if you test if, you'll see a new problem. To highlight, let's alter slightly the building to show for each building their `money` variable:

```html
<!-- Building.svelte -->

<button on:click={updateNumbers} class:cantbuy={cantBuy} disabled={cantBuy}>
  <p>Money: {money}$</p>
  <p>{numberOfBuilding} buildings bought, next one cost {cost}$.</p>
  <p>You gain {productionPerTick}$ / tick.</p>
</button>
```

When you click on the buildings, you see that they are independent, but a bit too much! They don't share a common `money` value anymore, each one use its own, and when they produce, it only goes to their own `money` stash as well. That is not what we want. We would like for them to have a shared `money` value, and then handle the rest of their state independently. 

### Using the store to handle global state

One way to have some global state that can be shared among components is to have a *store* with values that components can subscribe to. They can also update the values if you allow them to, and any change to the values are propagated back to other components subscribing to those values as well. It is the same idea that React/Redux uses (or Reagent/re-frame[4] in the ClojureScript ecosystem) .

Let's create our store, we need a JS file that we will call `store.js` and put some code in it: (I use `/* store.js */` instead of `<!-- store.js -->` to show you that the code belongs to `store.js` because it's no longer a template file, but a proper JS file. It allows you to copy paste my snippet without having an error into your editor.)

```js
/* store.js */
import { writable } from 'svelte/store';

export const money = writable(20);
```

And now we can use it inside our `App.svelte` and `Building.svelte`.

In `App.svelte`, we need to import it, remove the local variable declaration and change slightly the template where we used it. 

```html
<!-- App.svelte -->
<script>
  // import declarations
  ...
  import { money } from './store.js';
  
  // variable declarations
  // we removed let money = 20;
  ...
</script>

<h1>You have {$money} $!</h1>

<Building></Building>
<Building></Building>
```

#### Read from the store

To reference a value from the store, you prepend the dollar character `$` before its name. Here to access the value of `money` in the template we use `$money`. What it does in reality is auto-subscribe to the store value, and unsubscribe to it when the component is destroyed using the `onDestroy` lifecycle functions. `onDestroy` is kind of the opposite of `onMount`, it is called just after the component is unmounted from the DOM.

You can also opt out of the automatic subscribe and unsubscribe by doing it yourself, which allow more control over it. Let see what it would have looked like:

```html
<!-- ManualSubscribeToMoney.svelte -->
<script>
  import { money } from './store.js'
  import { onDestroy } from 'svelte'
    
  let money_value;

  // here we subscribe to 'money' and assign it's value to 'money_value'
  // each time 'money' change, it send it's new value to 'money_value' to keep it up to date.
  const unsubscribe = money.subscribe(value => {
    money_value = value;
  });
    
  // here we unsubscribe to 'money' when the component is unmounted.
  onDestroy(unsubscribe);
</script>

<!-- and here we can use 'money_value' instead of $money. -->
<h1>You have {money_value} $!</h1>

```

*Because it's much shorter to use the shorthand, I'll use for the rest of the tutorial. But keep in mind of what it does behind the scene.*

#### Write to the store (if the value is a `writable`)

You can't update the value of the store like this: `$money += 1`. You need to use the methods `update` or `set`. For example, if you want to update the value of money to add 1:

```js
// n refer to the old value of money, and n + 1 will be the new value 
money.update(n => n + 1)
```

And if you want to give a totally new value to it, you can use `set`:

```js
// set money to the greatest number ever
money.set(42)
```

For more information on the store: [Svelte store tutorial](https://svelte.dev/tutorial/writable-stores) first and later [Svelte doc](https://svelte.dev/docs#svelte_store) to have more information on Writable, Readable, derived store value and `get()`.

#### Going back to our project

Now that we know how to read and write to the store, we can update `Building.svelte`. We need to import the store, change every instance of `money` with `$money` and also change the way we change the value of  `money` using `update`. 

We are gonna declare a new function that will *hide* the fact that money is a store value.

```js
// update the value of `money` to the store, adding `n` to it.
function updateMoney(n){
  money.update(m => m + n);
}
```

But doing so, we need to rename the old `updateMoney`  that was launching the `setTimeout` to `launchSetTimeout`. The name was not good before, and it is still not good, if you have any suggestion, I'll gladly take it! And we can replace every instance where we add `money += something;` by `updateMoney(something);`.  After all the transformation, the code looks like that (keeping only the parts that changed): 

```html
<!-- Building.svelte-->
<script>
  // import declarations
  ...
  import { money } from './store.js';
	
  // variable declarations
  ...
  
  // reactive declarations
  $: cantBuy = cost > $money;
  ...
  
  // function declarations
	
  // update the value of `money` to the store, adding `n` to it.
  function updateMoney(n){
    money.update(m => m + n);
  }
	
  // update the values of `money` and `numberBuildings`
  function updateNumbers(){
    updateMoney(-cost);
    numberOfBuilding += 1
  }
	
  // update `money` with `productionPerTick` and set a timeout to call itself after `tickSpeed` ms
  function launchTimeout(){
    updateMoney(productionPerTick);
    setTimeout(launchTimeout, tickSpeed);
  }
  
  // lifecycle functions
  onMount(() => {
    launchTimeout();
  }); 
</script>

<style>
  ...
</style>

<button on:click={updateNumbers} class:cantbuy={cantBuy} disabled={cantBuy}>
  <p>Money: {$money}$</p>
  ...
</button>
```

Now you can see that both the value of `money` in `App.svelte` and both `Building.svelte` is shared, but each building keep their numbers local otherwise. 

{{< figure src="/img/blog_3_part2_store_implemented.png" alt="Money is synchronized between all components.">}}

### Naming our building with props

A very short step, now that we have many buildings, it could be nice to give them a name.

But remember that if we declare a variable inside a component, every instance of that component will have the same value for that variable. Not very helpful to have every building have the same name.

What we need is called **props**. To draw an analogy, props are to a component what parameters are to a function. Props are used to initialize the component with some values. They are an essential building block because they allow to re-use component by giving them the flexibility they need.

To declare a prop, it's like a variable declaration but with the keyword `export` added at the front of the declaration. And also we can replace on the template the reference to `money` with a reference to `name`.

```html
<!-- Building.svelte -->
<script>
  // import declarations
  ...
  // variable declarations
  ...
  // prop declarations
  export let name; // you can also give default value to your prop: export let name = "Producer";
  ...
</script>

<button on:click={updateNumbers} class:cantbuy={cantBuy} disabled={cantBuy}>
  <h3>{name}</h3>
  <p>{numberOfBuilding} buildings bought, next one cost {cost}$.</p>
  <p>You gain {productionPerTick}$ / tick.</p>
</button>
```

And to define the prop value, you define it like an HTML attribute on the `Building` tag.

```html
<!-- App.svelte -->

<Building name="1th Derivative"></Building>
<Building name="Combinatorics"></Building>
```

And here is the result:

{{< figure src="/img/blog_3_part2_name_with_prop.png" alt="Building with their names" >}}

### Digging a bit about what props enable

Using props will allow us to customize a little more each building.

We can now change many aspects of them like:

- their initial cost
- their initial number (we can imagine starting off with a few buildings already built after buying an upgrade)
- what they produce. Right now it's money but they could produce another currency or even other buildings!
- what they cost. Their cost could be another currency, another building or a combination of those. (Or even cost tick, accelerating or slowing down the game as you buy this building)
- they could also have a maximum number of units. (Example no more than 30 "Combinatorics" buildings and no limit for the "1th Derivative" building).
- And a lot more!

As a starting point, let's change what they produce.

### Building producing proofs

In the original *Derivative Clicker* game, the primary currency is money but there is a secondary currency named **proofs** that can be produced and is used to buy powerful buildings. (It is also one of the two elements determining the amount of prestige currency you earn when you use the reset/prestige mechanic.).

Let's implement proof :)

For the same reason *money* was put into the store, we are gonna put *proofs* there too.

```js
/* store.js */
import { writable } from 'svelte/store';

export const money = writable(20);
export const proofs	= writable(0);
```

We can then change the building component to replace explicit references to `money` to a generic `currencyProduced` that will can be either `money` or `proofs`. 

```html
<!-- Building.svelte -->
<script>
  // import declarations
  ...
    
  // variable declarations
  ...
  
  // props declaration
  ...
  export let currencyProduced = money // By default, building will produce money
  
  // reactive declarations
  ...
  
  // function declarations
  ...
  
  function updateCurrencyProduced(n){
    currencyProduced.update(m => m + n);
  }

  ...
	
  function launchTimeout(){
    updateCurrencyProduced(productionPerTick); // change updateMoney to updateCurrencyProduced
    setTimeout(launchTimeout, tickSpeed);
  }
</script>
```

Then to tie it up, in `App.svelte` we can import `proofs` from the store, and pass it as a prop to the building we want. (and update the display of the value as well).

```html
<!-- App.svelte -->
<script>
  // import declarations  
  ...
  import { money, proofs } from './store.js';
    
  ...
</script>

<h1>You have {$money}$ and {$proofs} proofs!</h1>

<Building name="1th Derivative"></Building>
<Building name="Combinatorics" currencyProduced={proofs}></Building>
```

Because gave a default value to `currencyProduced` for buildings, we don't need to use the prop for every building. But if we want to make it explicit, we can:

```html
<Building name="1th Derivative" currencyProduced={money}></Building>
<Building name="Combinatorics" currencyProduced={proofs}></Building>
```

Note also that we did not need to import `proofs` into `Building.svelte` (and we only need to import `money` because we use it as the default value for `currencyProduced`). We just import it in `App.svelte` and pass either `money` or `proofs` to the buildings. Then we rely on the fact that every store object can be read with `$` and updated with the `update` method. The only constraint is that what we give as a props to the buildings must be a writable store reference on a number (because we update it with `+` inside the building component). To relax this constraint, we could fire an event in the building component that could be handled in his parent component, updating the currency however it wants to. But this feel a bit heavy, and we won't go this way (for now).

### Fixing the UI

Now our "Combinatorics" building produces proofs, but we still display that it produces money.

We could do a function with lots of `if then else` to display either "$" or "proof" or "proofs" or something else depending on what currency the building produce. Or we could add another prop to handle that. It means that we could have two building producing the same currency but displaying it differently. I'll go with the first way of doing it because it will be quicker, but the second one is definitely more flexible (but I don't think that will be needed).

And because we are fixing the UI, we will also make it closer to *Derivative Clicker*. (at least for buildings)

So we want a function that will display the currency depending on what we produce. We will add it to a new file: `utils.js` because that function can be re-used in more than one component and is not tied to only one of them.

```js
/* utils.js */
import { money, proofs } from './store.js';

function currencyToString(currency){
  result = "?";
  if (currency === money)
      result = "$";
  else if (currency === proofs)
      result = "proofs"
  return result;
};
```

I use a `if else` here, but a `switch case` will probably scale better in readability if we get a lot more cases to handle. We will see how that pan out.

Let's use that function in `Building.svelte`:

```html
<!-- Building.svelte -->
<script>
  // import declarations
  ...
  import { currencyToString } from './utils.js'; 
  
  ...

</script>

...

<button on:click={updateNumbers} class:cantbuy={cantBuy} disabled={cantBuy}>
  <h3>{name}</h3>
  <p>{buildingProduction}{currencyToString(currencyProduced)}/tick: costs {cost}$.</p>
  <p>Owned:{numberOfBuilding} ({numberOfBuilding})</p>
</button>
```

To get closer to the original game I changed `buildingProductionPerTick` to `buildingProduction`. Both information are useful, we could display both, but I'll stick with the original design and display only `buildingProduction`.

Also I used `<p>Owned:{numberOfBuilding} ({numberOfBuilding})</p>`. It seems weird right now, but we will edit it later when we implement building producing buildings. Then it will be `<p>Owned:{numberOfBuildingTotal}  ({numberOfBuildingBought})</p>`.

In `App.svelte`, we can use the function as well:

```html
<!-- App.svelte -->
<script>
  // import declarations
  ...
  import { currencyToString } from './utils.js'; 
    
  ...
</script>

<h1>You have {$money}{currencyToString(money)} and {$proofs}{currencyToString(proofs)}!</h1>

...
```

If you follow closely, you may have noticed that the currency for the cost of the building is still hardcoded as `$` and not `currencyToString(money)`. It's because for now we have not looked into buying building with other currencies than money. We will change it when times come to implement that feature.

Note also that `currencyToString()` might possibly have been a component. (And that might have been a better implementation choice, I'm not sure). Try it out if you want!   

### Building producing buildings

Now that we have building producing proofs, what about buildings producing buildings?

To do that, we need to change a few things: 

- in `store.js` we are gonna had new variables for each building we have so we can synchronize their number.
- in `Building.svelte` we need to use this new variable, and update it when needed. Be careful when updating as the new variable will be a `writable`, not a plain number. We will need to use `$` to dereference the value and `update()`to update it. We also need to define new props to use those variables.
- in `App.svelte` we have to update the props and the template html to add the second level of buildings.

```js
/* store.js */

...

// first level buildings
export const firstDerivativeNum = writable(0);
export const combinatoricsNum = writable(0);

// second level buildings
export const secondDerivativeNum = writable(0);
```

```html
<!-- Building.js -->
<script>
  // import declarations
  ...
	
  // variable declarations
  let buildingProduction = 1; // $ produced per building per tick
  let tickSpeed = 1000;
	
  // props declarations
  export let name;
  export let currencyProduced = money;
  export let numberOfBuilding;
	
  // reactive declarations
  ...
  $: cost = ($numberOfBuilding + 1) * 5;
  $: productionPerTick = $numberOfBuilding * buildingProduction;
  
  // function declaration
  ...
  
  // update the values of `currencyProduced` and `numberBuildings`
  function updateNumbers(){
    updateMoney(-cost);
    numberOfBuilding.update(n => n + 1);
  }
  
  ...
</script>

...

<button on:click={updateNumbers} class:cantbuy={cantBuy} disabled={cantBuy}>
  <h3>{name}</h3>
  <p>{buildingProduction}{currencyToString(currencyProduced)}/tick: costs {cost}$.</p>
  <p>Owned:{$numberOfBuilding} ({$numberOfBuilding})</p>
</button>
```

```php+HTML
<!-- App.js -->
<script>
  // import declarations
  import Building from './Building.svelte';
  import { money, proofs, firstDerivativeNum, secondDerivativeNum, combinatoricsNum } from './store.js';
	import { currencyToString } from './utils.js';
	
  // variable declarations

  // reactive declarations
  
  // function declarations
	
  // lifecycle functions
</script>

<style>
</style>

<h1>You have {$money}{currencyToString(money)} and {$proofs}{currencyToString(proofs)}!</h1>

<Building name="1th Derivative" numberOfBuilding={firstDerivativeNum}></Building>
<Building name="Combinatorics" currencyProduced={proofs} numberOfBuilding={combinatoricsNum}></Building>
<Building name="2nde Derivative" currencyProduced={firstDerivativeNum} numberOfBuilding={secondDerivativeNum}></Building>
```



Now that we have that, we can generate other buildings :)

A little tweak to fix the UI again, the "2nd Derivative" building display a `?` instead of the name of the building it produces. To fix that we need to update `utils.js`. 

```js
/* utils.js */
import { money, proofs, firstDerivativeNum } from './store.js' 

// Returns a string representing `currency`. If `currency` is not recognized, returns '?'
export function currencyToString(c){
   ...
   
	else if (c === firstDerivativeNum)
		result = " 1st Derivative"

	return result;
}
```

To be fair, the UI needs a lot more fixing but it will do for now ;)

By doing this we introduced a ~bug~ feature with the costs of the buildings, if you start by buying a building that produces another building, this building's costs will go up even so we did not buy any manually. We will ~fix this bug~ reverse this feature in the next tutorial when we will implement upgrades.

## Recap

That's it for this second part of the tutorial. Below is the full code we have so far.

```html
<!-- App.svelte -->
<script>
  // import declarations
  import Building from './Building.svelte';
  import { money, proofs, firstDerivativeNum, secondDerivativeNum, combinatoricsNum } from './store.js';
  import { currencyToString } from './utils.js';
	
  // variable declarations

  // reactive declarations
  
  // function declarations
	
  // lifecycle functions
</script>

<style>
</style>

<h1>You have {$money}{currencyToString(money)} and {$proofs}{currencyToString(proofs)}!</h1>

<Building name="1th Derivative" numberOfBuilding={firstDerivativeNum}></Building>
<Building name="Combinatorics" currencyProduced={proofs} numberOfBuilding={combinatoricsNum}></Building>
<Building name="2nde Derivative" currencyProduced={firstDerivativeNum} numberOfBuilding={secondDerivativeNum}></Building>
```

```html
<!-- Building.svelte -->
<script>
  // import declarations
  import { onMount } from 'svelte';
  import { money } from './store.js';
  import { currencyToString } from './utils.js'
	
  // variable declarations
  let buildingProduction = 1; // $ produced per building per tick
  let tickSpeed = 1000;
	
  // props declarations
  export let name;
  export let currencyProduced = money;
  export let numberOfBuilding;
	
  // reactive declarations
  $: cantBuy = cost > $money;
  $: cost = ($numberOfBuilding + 1) * 5;
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
  <p>Owned:{$numberOfBuilding} ({$numberOfBuilding})</p>
</button>
```

```js
/* store.js */
import { writable } from 'svelte/store';

export const money = writable(20);
export const proofs	= writable(0);

// first level buildings
export const firstDerivativeNum = writable(0);
export const combinatoricsNum = writable(0);

// second level buildings
export const secondDerivativeNum = writable(0);
```

```js
/* utils.js */
import { money, proofs, firstDerivativeNum } from './store.js' 

// Returns a string representing `currency`. If `currency` is not recognized, returns '?'
export function currencyToString(c){
	let result = "?";

	if (c === money)
		result = "$";
	else if (c === proofs)
		result = " proofs"
	else if (c === firstDerivativeNum)
		result = " 1st Derivative"

	return result;
}
```

And below you can see the result: 

{{< figure src="/img/blog_3_part2_final_recap.png" alt="Result" >}}

## Objectives for the next parts

There are quite a few things to implement still:

- Update the stats recap at the top
- Make buildings have varying costs
- Upgrades for our buildings
- Prestige layer
- Options (Save, Export Save, Load Save, Stats, number formatting
- Balancing the game

We will continue in the next part: Coming soon.

You can also [play with the project in the Svelte REPL](https://svelte.dev/repl/230b7ab7fe774b31b1936a2aa2ebf050?version=3.4.2) or [play the game made so far](tuto-derivative-clicker-part2.surge.sh) (hosted by Surge) .

[^4]: https://github.com/Day8/re-frame
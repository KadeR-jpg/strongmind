<script lang="ts">
	let pizzaChef: boolean = true;
	let name: string = '';
	let pizzaName: string = '';
	let toppingList: string[] = [];
	/**
	 * using typrscript to define unique types made it easier to keep track of everything.
	 */
	type pizza = {
		id: number;
		name: string;
		toppings: string[];
	};
	type topping = {
		id: number;
		name: string;
		onPizza: boolean;
	};
	let Recipes: pizza[] = [
		{
			id: 1,
			name: 'cheese pizza',
			toppings: ['cheese', 'sauce']
		},
		{
			id: 2,
			name: 'pepperoni pizza',
			toppings: ['pepperoni', 'cheese', 'sauce']
		},
		{
			id: 3,
			name: 'sauce pizza',
			toppings: ['sauce']
		},
		{
			id: 4,
			name: 'pineapple pizza',
			toppings: ['pepperoni', 'pineapple', 'cheese', 'sauce']
		},
		{
			id: 5,
			name: 'Real Pineapple Pizza',
			toppings: ['pineapple', 'sauce']
		}
	];
	let toppings: topping[] = [
		{
			id: 1,
			name: 'pepperoni',
			onPizza: true
		},
		{
			id: 2,
			name: 'sauce',
			onPizza: false
		},
		{
			id: 3,
			name: 'cheese',
			onPizza: true
		},
		{
			id: 4,
			name: 'pineapple',
			onPizza: true
		}
	];
	/**
	 * Svelte reactivity is triggered by assignments so just updating the array was not enough,
	 * this way i can update and get the reactivity for the svelte each template to render the new object.
	 * I used this technique several times to get reactivity to work correctly.
	 */
	function addTopping() {
		if (!toppings.find((element) => element.name.toLowerCase() == name.toLowerCase())) {
			toppings = [
				...toppings,
				{
					id: Object.keys(toppings).length + 1,
					name,
					onPizza: false
				}
			];
			name = '';
		} else {
			alert(`${name.toLowerCase()} is already in the toppings list`);
			name = '';
		}
	}
	/**
	 * The use of filter also let me leverage the reactivity of svelte to get it to update the changes for the user.
	 * Since filter is just creating a copy of the list w/o the item that i want in there it worked well.
	 *
	 */
	function removeTopping(remove: any) {
		toppings = toppings.filter((topping) => topping !== remove);
	}
	function removePizza(remove: any) {
		Recipes = Recipes.filter((pizza) => pizza.name !== remove);
	}
	function savePizza() {
		toppings.forEach((topping) => {
			if (topping.onPizza) {
				toppingList.push(topping.name);
			}
		});
		if (!Recipes.find((pizza) => pizza.name.toLowerCase() == pizzaName.toLowerCase())) {
			Recipes = [
				...Recipes,
				{
					id: Object.keys(Recipes).length + 1,
					name: pizzaName,
					toppings: toppingList
				}
			];
			toppingList = [];
		} else {
			alert(`${pizzaName.toLowerCase()} is already the name of an existing pizza recipe`);
			pizzaName = '';
		}
		console.log(Recipes);
	}
	function removeRecipeTopping(fromPizza: pizza, removeTopping: string) {
		fromPizza.toppings = fromPizza.toppings.filter((topping) => topping !== removeTopping);
		if (fromPizza.toppings.length === 0) {
			console.log(fromPizza.toppings.length);
			Recipes = Recipes.filter((data) => data.name !== fromPizza.name);
		}
		Recipes = Recipes;
	}
</script>

<div class="grid grid-cols-3 justify-center h-full">
	<div class="flex flex-col items-center p-4 gap-12 mb-auto">
		<h1 class="flex text-5xl">
			{#if pizzaChef}
				Pizza Chef Mode
			{:else if !pizzaChef}
				Store Owner Mode
			{/if}
		</h1>
		<div class="flex justify-center">
			<label class="flex flex-row gap-4 align-middle items-center ">
				<input
					class="w-6 h-6 text-blue-600 bg-gray-100 rounded border-gray-300 p-4"
					type="checkbox"
					bind:checked={pizzaChef}
				/>
				Enable Pizza Chef
			</label>
		</div>
		<div class="flex flex-col h-screen">
			{#if pizzaChef}
				<form class="flex flex-col">
					<label for="pizzaName" class="p-4"
						>Name pizza
						<input
							type="text"
							id="pizzaName"
							class="border-2 rounded-md p-2"
							bind:value={pizzaName}
						/>
					</label>
				</form>
				<button
					on:click={(event) => savePizza()}
					class="m-2 bg-green-500 text-white p-2 rounded-md hover:bg-green-300 hover:text-black transition-colors ease-in-out duration-150"
					>Save Pizza</button
				>
				<div class="p-4 h-96 overflow-y-scroll">
					<ul>
						{#each Recipes as pizza}
							<li class="flex flex-col gap-4 justify-between">
								<h2 class="text-lg border-b m-auto text-center pt-4">{pizza.name.toUpperCase()}</h2>
								<div class="flex flex-col">
									<h2 class="text-lg border-b text-right">Topppings list</h2>
									{#each pizza.toppings as topping}
										<div class="flex flex-row justify-between items-center p-2">
											<p class="flex text-right items-center text-lg">
												{topping}
											</p>
											<svg
												on:click={(event) => removeRecipeTopping(pizza, topping)}
												xmlns="http://www.w3.org/2000/svg"
												fill="none"
												viewBox="0 0 24 24"
												stroke-width="1.5"
												stroke="currentColor"
												class="w-5 h-5 rotate-45 hover:stroke-red-500 hover:rotate-90 transition-all ease-in-out duration-150 "
											>
												<path
													stroke-linecap="round"
													stroke-linejoin="round"
													d="M6 18L18 6M6 6l12 12"
												/>
											</svg>
										</div>
									{/each}
								</div>
								<button
									class="text-white hover:bg-red-500 bg-red-700 m-auto p-2 rounded transition-colors ease-in duration-150"
									on:click={(event) => removePizza(pizza.name)}
								>
									Remove
								</button>
							</li>
						{/each}
					</ul>
				</div>
			{/if}
		</div>
	</div>
	<div class="flex flex-col items-center p-4 gap-12 mb-auto">
		<h1 class="text-5xl">Recipe</h1>
		<div class="flex flex-row align-middle justify-center gap-2 flex-wrap">
			{#each toppings as topping}
				{#if topping.onPizza === true}
					<p>{topping.name.toUpperCase()}</p>
				{/if}
			{/each}
			<p>PIZZA</p>
			<p>{toppingList}</p>
		</div>
	</div>
	<div class="flex flex-col items-center p-4 gap-12 mb-auto">
		<h1 class="text-5xl">Topping selection</h1>
		<form on:submit|preventDefault={addTopping} class="flex flex-col">
			<label for="topping"
				>Add a topping
				<input type="text" id="topping" class="border-2 rounded-md p-2" bind:value={name} />
			</label>
		</form>
		<ul class="overflow-y-scroll p-4 h-48">
			{#each toppings as topping}
				<li class="flex flex-row gap-4 justify-between">
					<input type="checkbox" name={topping.name} bind:checked={topping.onPizza} />
					<p>{topping.name}</p>
					<button
						class="hover:text-red-500 transition-colors ease-in duration-150"
						on:click={(event) => removeTopping(topping)}
					>
						Remove
					</button>
				</li>
			{/each}
		</ul>
	</div>
</div>

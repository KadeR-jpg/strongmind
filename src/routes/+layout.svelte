<script lang='ts'>
    import '../tailwind.postcss'
    import pizzaFav from '$lib/pizza.png'
</script>
<svelte:head>
        <link rel="icon" type="image/svg" href={pizzaFav}/>
        <title>PizzaShop</title>
</svelte:head>
<body>
        <slot />
</body>
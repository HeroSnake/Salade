<script>	
	import Icon from 'svelte-awesome';
	import { faBreadSlice } from '@fortawesome/free-solid-svg-icons';
	import { formatter } from "./helpers";
	
	export let order, salads, sizes, defaultOrders;
	
	const changeOrder = () => {
		let current = defaultOrders.find(item => item.name === order.name );
		if(typeof current !== 'undefined'){
			order.salad = typeof current.salad !== 'undefined' ? salads[current.salad].name : '';
			order.size = typeof current.size !== 'undefined' ? sizes[current.size] : '';
			order.bread = typeof current.pain !== 'undefined' ? current.pain : 0;
		}
		sizeOrder();
	}
	const toggleBread = () => {
		order.bread = !order.bread;
	}
	const sizeOrder = () => {
		order.price = order.size ? order.size.price : order.price;
	}
	const saladOrder = () => {
		order.price = order.salad == salads[10].name ? 0 : order.size.price;
	}
	$: order.total = Number(order.price) + (order.bread ? 0.6 : 0);
	$: order.meal = order.salad != salads[10].name ? null : order.meal;
</script>

<div>
	<input type="text" bind:value={order.name} on:input={changeOrder} style="width:150px" />
	<select bind:value={order.salad} on:change="{saladOrder}">
		{#each salads as salad}
			<option value={salad.name}>{salad.name}</option>
		{/each}
	</select>
	{#if order.salad !== salads[10].name}
		<select id="size" bind:value={order.size} on:change="{sizeOrder}">
			{#each sizes as size}
				<option value={size}>{size.label}</option>
			{/each}
		</select>
		<span on:click="{toggleBread}" style="top: 5px;position: relative;">
			<Icon data={faBreadSlice} scale="1.5" style="cursor:pointer; color:{order.bread ? '#805828' : '#8058285e'};"/>
	</span>
	{:else}
		<input type="text" bind:value={order.meal} style="width:150px" />
		<span on:click="{toggleBread}" style="top: 5px;position: relative;">
			<Icon data={faBreadSlice} scale="1.5" style="cursor:pointer; color:{order.bread ? '#805828' : '#8058285e'};"/>
		</span>
		<input type="text" bind:value={order.price} style="width:50px" />
	{/if}
	<span>{formatter.format(order.total)}</span>
</div>
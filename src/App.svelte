
<script>	
	import Icon from 'svelte-awesome';
  import { faTrash, faPaperPlane, faPlus, faMap } from '@fortawesome/free-solid-svg-icons';
	import Line from "./Line.svelte";
	import { formatter } from "./helpers";
	
	const getWeek = (today) => {
    const firstDayOfYear = new Date(today.getFullYear(), 0, 1);
    const pastDaysOfYear = (today - firstDayOfYear) / 86400000;
    return Math.ceil((pastDaysOfYear + firstDayOfYear.getDay() + 1) / 7);
	}

	const today = new Date();
	let month = (today.getMonth() + 1).toLocaleString('en-US', {
    minimumIntegerDigits: 2,
    useGrouping: false
  });
	let year = today.getFullYear();
	let week = getWeek(today) - 1;
	let url = 'https://www.atelierduchef.fr/wp-content/uploads/'+year+'/'+month+'/menus-semaine-'+week+'.pdf';

	const salads = [
		{ id: 1, name: "César" },
		{ id: 2, name: "New Yorkaise" },
		{ id: 3, name: "Niçoise" },
		{ id: 4, name: "Lyonnaise" },
		{ id: 5, name: "Bressane" },
		{ id: 6, name: "Savoyarde" },
		{ id: 7, name: "Fermière" },
		{ id: 8, name: "Chèvre" },
		{ id: 9, name: "Nordique" },
		{ id: 10, name: "Péruvienne" },
		{ id: 11, name: "Autre..." }
	];
	
	const defaultOrders = [
		{ name: "Florent", salad: 6},
		{ name: "Laurent", size: 1 },
		{ name: "Geoffrey", size: 1 },
		{ name: "Thomas", salad: 4, size: 1, pain: 1 }
	]
	
	const sizes = [
		{ label:"Pte", price: 5.6 },
		{ label: "Gde", price: 7.6 }
	];
	
	const orderInitialValues = {
		name: "",
		meal: null,
		salad: salads[0].name,
		size: sizes[0],
		bread: false,
		price: 5.6,
		total: 0
	};
	
	const addOrder = () => orders = [...orders, {
		id: orders.length ?? 0,
		...orderInitialValues
	}];
	
	const deleteOrder = (id) => {
		orders = orders.filter((order) => order.id != id);
	}
	
	const openCart = () => {
		window.open(url, '_blank').focus();
	}

	const exportOrders = () => {
		let email = "mailto:contact@atelierduchef.fr?subject=Commande Vinatis&body=";
		email += "Bonjour, voici la commande de Vinatis : %0D%0A %0D%0A";
		console.log(orders);
		orders.map((order) => {
			let totalLine = formatter.format(order.total);
			let meal = order.meal ? order.meal : order.size.label + " " + order.salad;
			email += (`${order.name} %0D%0A 1 ${meal} ${order.bread ? '%0D%0A1 pain' : ''} %0D%0A ${totalLine} %0D%0A`).toUpperCase();
		})
		let totalOrder = formatter.format(totals);
		email += `%0D%0A Total : ${totalOrder}`;
		email += `%0D%0A %0D%0A Merci %0D%0A %0D%0A Vinatis %0D%0A 6 Avenue du Pré de Challes, %0D%0A 74940 Annecy-le-Vieux`;
		window.location.href = email;
	}
	
	let orders = [orderInitialValues];

	$: totals = orders.reduce((acc, curr) => {
		acc += curr.total;
		return acc;
	}, 0)
</script>

{#each orders as order (order.id)}
<div style="display:flex; justify-content:space-between;">
	<Line bind:order={order} {salads} {sizes} {defaultOrders}/>
	<button on:click={() => deleteOrder(order.id)}>
		<Icon data={faTrash} style="color:#d94343;"/>
	</button>
</div>
{/each}
<div>Total : {formatter.format(totals)}</div>
<button on:click={addOrder}><Icon data={faPlus} scale="0.8"/> Commande</button>
<button on:click={exportOrders}><Icon data={faPaperPlane} style="color:#2361b0;"/></button>
<button on:click={openCart}><Icon data={faMap} style="color:#814600;"/></button>


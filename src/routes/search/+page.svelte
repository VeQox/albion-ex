<script lang="ts">

	type priceRequestType = { 
        item_id: string; 
        city: {
            "Martlock": string;
            "Bridgewatch": string;
            "Lymhurst": string;
            "Fort Sterling": string;
            "Thetford": string;
            "Caerleon": string;
        };
        quality: {
            1 : number;
            2 : number;
            3 : number;
            4 : number; 
            5 : number;
        };
        sell_price_min: number;
        sell_price_min_date: number;
        sell_price_max: number;
        sell_price_max_date: number;
        buy_price_min: number;
        buy_price_min_date: number;
        buy_price_max: number;
        buy_price_max_date: number;
    };

    const api = "https://www.albion-online-data.com/api/v2/stats/prices/";

	const cities = 'Martlock,Bridgewatch,Lymhurst,Fort Sterling,Thetford,Caerleon';
	const enchantments = ['.0', '.1', '.2', '.3'];
	const qualities = ['Normal', 'Good', 'Outstanding', 'Excellent', 'Masterpiece'];

	const items = ['T4_BAG', 'T5_BAG'];

	const fetchData = async (url: string) : Promise<priceRequestType> => {
		let response = await fetch(url);
        let data = await response.json();
        return data;
	};

    let inputField : any;
    let qualitieInputField : any;
    let enchantmentsInputField : any;
    const keydownEvent = async (e : KeyboardEvent) => {
        if(e.code === "Enter"){
            const data = await fetchData(`${api}${getItemName(inputField.value)}?locations=${cities}&qualities=${qualitieInputField.value}`)
        
            console.log(data);
        }
    }

    const getItemName = (itemID : string) => {
        const enchantment : number = enchantmentsInputField.value;
        if(enchantment != 0){
            return `${itemID}@${enchantment}`;
        }
        return itemID;
    }
</script>

<div class="flex justify-center space-x-6 border">
	<input bind:this={inputField} list="items" class="text-center" placeholder="Name" on:keydown={keydownEvent}/>

	<datalist id="items">
		{#each items as item}
			<option value={item} />{/each}
	</datalist>

	<select bind:this={enchantmentsInputField}>
		{#each enchantments as enchantment, i}
			<option value={i}>{enchantment}</option>
		{/each}
	</select>

	<select bind:this={qualitieInputField}>
		{#each qualities as qualitie, i}
			<option value={i + 1}>{qualitie}</option>
		{/each}
	</select>
</div>
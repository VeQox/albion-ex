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
        sell_price_min_date: string;
        sell_price_max: number;
        sell_price_max_date: string;
        buy_price_min: number;
        buy_price_min_date: string;
        buy_price_max: number;
        buy_price_max_date: string;
    };

    const API = "https://www.albion-online-data.com/api/v2/stats/prices/";
    const imgAPI : string = "https://render.albiononline.com/v1/item/";

	const cities = 'Martlock,Bridgewatch,Lymhurst,Fort Sterling,Thetford,Caerleon,Black Market';
	const enchantments = ['.0', '.1', '.2', '.3'];
	const qualities = ['Normal', 'Good', 'Outstanding', 'Excellent', 'Masterpiece'];

	let items : string[] = ["T1_BAG", "T2_BAG", "T3_BAG", "T4_BAG", "T5_BAG", "T6_BAG", "T7_BAG", "T8_BAG"];

	const fetchData = async (url: string) : Promise<priceRequestType[]> => {
		let response = await fetch(url);
        let data = await response.json();
        return data;
	};

    const getItemName = () => {
        let input = document.getElementById("item") as HTMLInputElement;
        if(input !== null){
            return input.value;
        }
        return "";
    }
    const getQuality = () => {
        let input = document.getElementById("qualities") as HTMLSelectElement;
        if(input !== null){
            return  Number(input.value);
        }
        return 0;
    }
    const getEnchantment = () => {
        let input = document.getElementById("enchantments") as HTMLSelectElement;
        if(input !== null){
            return input.value;
        }
        return 0;
    }
    const getItemID = () => {
        const enchantment = getEnchantment();
        const item = getItemName();
        
        if(enchantment !== 0){
            return `${item}@${enchantment}`
        }
        return item;
    }

    let hasResponse = false;
    let response : priceRequestType[] = [];
    const keydownEvent = async (e : KeyboardEvent) => {
        if(e.code === "Enter"){
            await createItemView();
        }
    }
    const createItemView = async () => {
        const itemID = getItemID();
        const quality = getQuality();
        appendItemImage(itemID, quality);
        response = await fetchData(`${API}${itemID}?locations=${cities}&qualities=${quality}`);
        hasResponse = true;
        console.table(response);
    }
    const appendItemImage = (itemID : string, quality : number) => {
        let responseDiv = document.getElementById("response") as HTMLDivElement;
        responseDiv.innerHTML = `<img src="${`${imgAPI}${itemID}.png?quality=${quality}&size=150`}" alt="item">`
    }
</script>

<div class="flex justify-center space-x-6 border">
	<input id="item" list="items" class="text-center" placeholder="Name" on:keydown={keydownEvent}/>

	<datalist id="items">
		{#each items as item}
			<option value={item} />
        {/each}
	</datalist>

	<select id="enchantments">
		{#each enchantments as enchantment, i}
			<option value={i}>{enchantment}</option>
		{/each}
	</select>

	<select id="qualities">
		{#each qualities as qualitie, i}
			<option value={i + 1}>{qualitie}</option>
		{/each}
	</select>
</div>

<div>
    <div id="response" class="flex justify-center items-center">

    </div>
    {#if hasResponse }
        <div class="grid grid-cols-4 border-b-2">
            <p class="col-span-2 text-center border-x-2">City</p>
            <p class="text-center">buy price</p>
            <p class="text-center border-x-2">sell price</p>
        </div>
    {/if}

    {#each response as itemPrice, i}
        <div class="grid grid-cols-4 border-b-2">
            <div class="inline col-span-2 border-x-2">
                <!--<img class="inline" src={`https://render.albiononline.com/v1/item/Elder's%20${itemPrice.city}%20Crest.png?locale=en&size=50`} alt="cityLogo">-->
                <p class="inline">{itemPrice.city}</p>
            </div>
            <div class="flex items-center justify-center">
                <p>{new Intl.NumberFormat('de-IN').format(Math.round((itemPrice.buy_price_max + itemPrice.buy_price_min) / 2))}</p>
            </div>
            <div class="flex items-center justify-center border-x-2">
                <p>{new Intl.NumberFormat('de-IN').format(Math.round((itemPrice.sell_price_max + itemPrice.sell_price_min) / 2))}</p>
            </div>
        </div>
    {/each}
</div>


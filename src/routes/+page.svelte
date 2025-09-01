<script>
	import { json } from "@sveltejs/kit";
    import Field from "$lib/field.svelte";



    let active_pools = {
        "200-1000": true
    }

    let generated = {
        question: "",
        answer: "",
    }

    const num_dict = {
        "0": "cero",
        "1": "uno",
        "2": "dos",
        "3": "tres",
        "4": "cuarto",
        "5": "cinco",
        "6": "seis",
        "7": "siete",
        "8": "ocho",
        "9": "nueve",
        "10": "diez",
        "11": "once",
        "12": "doce",
        "13": "trece",
        "14": "catorce",
        "15": "quince",
        "16": "dieciseis",
        "17": "diecisiete",
        "18": "dieciocho",
        "19": "diecinueve",
        "20": "veinte",
        "21": "veintiuno", // veintiun
        "22": "veintidos",
        "23": "veintitres",
        "24": "veinticuarto",
        "25": "veinticinco",
        "26": "veintiseis",
        "27": "veintisiete",
        "28": "veintiocho",
        "29": "veintinueve",
        "30": "treinta",
        "40": "cuarenta",
        "50": "cincuenta",
        "60": "sesenta",
        "70": "setenta",
        "80": "ochenta",
        "90": "noventa",

        "100": "cien",
        "1xx": "ciento",
        "200": "doscientos",
        "300": "trescientos",
        "400": "cuatrocientos",
        "500": "quinientos",
        "600": "seiscientos",
        "700": "setecientos",
        "800": "ochocientos",
        "900": "novecientos",
        "1000": "mil"
    }

    const num_to_str = (x, no_cero = false) => {
        if (x == 0 && no_cero) return ""
        if (x < 30) return num_dict[x]
        if (x < 100) return num_dict[x - x % 10] + " " + num_to_str(x%10, true)
        if (x == 100) return num_dict[x]
        if (x < 200) return num_dict["1xx"] + " " + num_to_str(x % 100, true)
        if (x < 1000) return num_dict[x - x % 100] + " " + num_to_str(x % 100, true)
        return "wtf"
    }
    


    const pools = {}
    pools["0-9"]= {
        left: 0,
        right: 9,
        step: 1
    }

    pools["10-19"] =  {
        left: 10,
        right: 19,
        step: 1
    }
    pools["20-29"] = {
        left: 20,
        right: 29,
        step: 1
    }
    pools["20,30-100"] = {
        left: 20,
        right: 100,
        step: 10
    }

    pools["30-99"] = {
        "left": 30,
        "right": 99,
        step: 1
    }


    pools["100-199"] = {
        left: 100,
        right: 199,
        step:1
    }

    pools["100,200-1000"] = {
        left: 100,
        right: 1000,
        step: 100
    }

    pools["200-1000"] = {
        left: 200,
        right: 1000,
        step: 1
    }



    let genpool = []
    let idx = -1

    $: console.log("update")
    
    const generate = () => {
        let keys = Object.keys(active_pools    ).filter((x) => active_pools[x])
        console.log(keys)


        if (idx === -1 || idx === genpool.length) {
            genpool = []
            for (let i = 0; i < keys.length; i++) {
                const pool = pools[keys[i]]
                if (!active_pools[keys[i]])  {
                    continue
                }
                for (let i = pool.left; i <= pool.right; i += pool.step) {
                    genpool[genpool.length] = {
                        num: i,
                        pool: pool
                    }
                }
            }
            for (let i = 0; i < genpool.length; i++) {
                const rn = Math.floor(Math.random() *genpool.length)
                const t = genpool[i]
                genpool[i] = genpool[rn]
                genpool[rn] = t
            }
            idx = 0
        }
        

        console.log(genpool)

        const item = genpool[idx]
        generated.question = item.num
        generated.answer = num_to_str(item.num)
        idx++

    }
    const regenerate = () => {
        idx = -1
        generate()
    }
    generate()

    let answerList = []

    const addAnswer = () => {
        answerList = answerList.reverse()
        answerList[answerList.length] = {
            question: generated.question,
            answer: generated.answer
        }
        answerList = answerList.reverse()
        answerList = answerList.slice(0, 5)

        generate()
    }

    const nums = [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]
    const ng = [
        (x) => num_to_str(x),
        (x) => num_to_str(10+x),
        (x) => num_to_str(20+x),
        (x) => num_to_str(x * 10, true),
        (x) => num_to_str(x * 100, true),
    ]

</script>

<h1 class="text-3xl">{generated.question}</h1>
<button on:click={addAnswer}>Show answer</button>

<div>
{#each answerList as item} 
<p class="text-2xl">{item.question} : {item.answer}</p>
{/each}
</div>


<div>
{#each Object.keys(pools) as pool, i}
<input type="checkbox"  bind:checked={active_pools[pool]} on:change={regenerate}/> {pool} <br/>
{/each}
</div>

<table>
    <thead>
    <tr><td></td><td>x</td><td>1x</td><td>2x</td><td>x0</td><td>x00</td></tr>
    </thead>
    {#each nums as num}
    <tr>
        <td>{num}</td>
        {#each ng as n}
        <td>{n(num)}</td>
        {/each}
    </tr>
    {/each}
</table>

<Field label="Hello" value={generated.question}></Field>    


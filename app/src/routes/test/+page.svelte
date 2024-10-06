<script>
    import { onMount } from "svelte";
    // 0 - " "
    // 1 - "a"
    // 27 - "?"
    let data;
    function letter_to_num(letter) {
        if (letter === " ") {
            return 0;
        }
        if (letter === "?") {
            return 27;
        }
        return letter.charCodeAt(0) - 96;
    }
    function num_to_letter(num) {
        if (num === 0) {
            return " ";
        }
        if (num === 27) {
            return "?";
        }
        return String.fromCharCode(num + 96);
    }
    let trigram = null;
    $: cond = (trigram && trigram.length===3) ? cond_from_trigram(trigram) : [];
    $: cond_pairs = Array.from(cond).map((val, idx) => [num_to_letter(idx), val]);
    onMount(async () => {
        const response = await fetch("/dummy_matrix");
        const buffer = await response.arrayBuffer();
        data = new Float64Array(buffer);
        trigram = "the";
    });
    function cond_from_trigram(trigram) {
        let c1 = letter_to_num(trigram[0]);
        let c2 = letter_to_num(trigram[1]);
        let c3 = letter_to_num(trigram[2]);
        let offset = c1 * 28 * 28 * 28 + c2 * 28 * 28 + c3 * 28;
        return data.slice(offset, offset + 28);
    }

</script>

<input type="text" class="border border-black" bind:value={trigram}>
<br>
<br>
<div class="flex flex-row flex-wrap justify-left items-center gap-center">
{#each cond_pairs as [letter, val]}
    <div class="bg-white border border-black h-16 w-64 flex flex-row items-center justify-end pr-16 relative">
        <div>
            {letter}
        </div>
        <div class="h-16 bg-blue-500 flex flex-row justify-center items-center absolute left-0"
            style:width={val*100 + "%"}
        >
        </div>
    </div>
    <br>
{/each}
</div>


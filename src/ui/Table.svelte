<script lang="ts">
    import { hideTable } from "../utils/modifiedTable";

    export let rowNum: number = 8;
    export let colNum: number = 8;
    export let selectedTableEnd: number[];
    export let hoverTableEnd: number[];

    let grid = [rowNum, colNum];

    $: col = `repeat(${grid[1]}, 1fr)`;
    $: row = `repeat(${grid[0]}, 1fr)`;
    $: is_active = Array(grid[0]).fill(0).map(_ => Array(grid[1]).fill(false));

    let start: number[] = [];
    let end: number[] = [];

    function hover(i: number, j: number) {
        start = [0, 0];
        end = [i, j];
        hoverTableEnd = [i + 1, j + 1];
        checkActive(end);
    }

    function unHover() {
        start = end = [-1, -1];
        setTimeout(() => {
            hoverTableEnd = [0, 0];
            checkActive(end);
        }, 1000);
    }

    function click(i: number, j: number) {
        if(j === 0) return;
        selectedTableEnd = [i + 1, j + 1];
        hideTable();
    }

    function isInRange([i, j]: number[], [i2, j2]: number[]) {
        return ((i - start[0]) * (i - i2) <= 0) &&
            ((j - start[1]) * (j - j2)<= 0)
    }

    function checkActive (end: number[]) {
        is_active = is_active.map(
            (a, i) => a.map((_, j) => isInRange([i, j], end)));
    }
</script>

<div class="container" style="grid-template-rows: {row}; grid-template-columns: {col};" on:mouseleave={() => unHover()} on:blur={() => unHover()}>
    {#each {length: grid[0]} as _, i (i)}
        {#each {length: grid[1]} as _, j (j)}
            <div
                 class="table-generator-cell"
                 class:active={is_active[i][j]}
                 on:mouseenter={() => hover(i, j)}
                 on:click={() => click(i, j)}
            ></div>
        {/each}
    {/each}
</div>

<style>

    .container {
        display: grid;
        border: 1px solid #999;
        border-radius: 2px;
        width: 150px;
        height: 150px;
        grid-gap: 1px;
        background: #999;
    }

    .container div {
        background: #fff;
    }

    div.active {
        background-color: #DEF;
    }

</style>

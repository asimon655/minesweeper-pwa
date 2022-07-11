<script lang="ts">
    import type {Box} from '../../type'
    import { color_map } from '../../const'

    export let box: Box
    export let x: number
    export let y: number
    export let parent_click: Function
    export let game_on: boolean
    
    function click() {
        parent_click(x,y)
    }
    function onRightClick() {
        box.marked = !box.marked
    }
</script>

{#if box.covered}
    <button on:click={click} on:contextmenu|preventDefault="{onRightClick}" class:clicked={!box.covered} disabled={!game_on}>
        {#if box.marked}
            <span>📍</span>
        {/if}
    </button>
{:else}
    <div class="content">
        <span style="color: {color_map.get(box.value)};">
            {box.value == -1 ? '💥' : box.value == 0 ? '' : box.value}
        </span>
    </div>
{/if}

<style>
    button {
        width: 2rem;
        height: 2rem;
        font-size: 1.5rem;
        background-color: rgb(192, 192, 192);
        text-align: center;
        padding: 0;
        font-weight: bold;
        border-width: 0.2rem;
        border-color: lightgray;
    }
    .clicked {
        border: 0ch;
    }
    div.content {
        width: 2rem;
        height: 2rem;
        font-size: 1.5rem;
        background-color: rgb(192, 192, 192);
        border-width: 0.5rem;
        border-color: gray;
        text-align: center;
        padding: 0;
        font-weight: bold;
    }
    span {
        vertical-align: middle;
        font-family: monospace;
    }
</style>
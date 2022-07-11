<script lang="ts">
    import LandBox from "./game/_landBox.svelte";
    import type { Box } from "../type";

    export let width = 10;
    export let height = 10;
    export let mind_count = 15;

    let intmap: Box[][] = new Array(height)
        .fill(0)
        .map(() =>
            new Array<Box>(width).fill({
                value: 0,
                covered: true,
                marked: false,
            })
        );
    let game_on = true;
    start_new_game();

    function reset_minds() {
        for (let y = 0; y < height; y++) {
            for (let x = 0; x < width; x++) {
                if (intmap[y][x].value == -1) {
                    intmap[y][x] = { value: 0, covered: true, marked: false};
                }
            }
        }
    }

    function init_minds() {
        for (let i = 0; i < mind_count; i++) {
            const x = Math.floor(Math.random() * width);
            const y = Math.floor(Math.random() * height);

            if (intmap[y][x] && intmap[y][x].value == -1) {
                i--;
                continue;
            } else {
                intmap[y][x] = { value: -1, covered: true, marked: false};
            }
        }
    }

    function init_others() {
        for (let y = 0; y < height; y++) {
            for (let x = 0; x < width; x++) {
                if (intmap[y][x].value != -1) {
                    intmap[y][x] = { value: calc_around(x, y), covered: true, marked: false};
                }
            }
        }
    }

    function calc_around(x: number, y: number): number {
        let count = 0;
        if (x - 1 >= 0 && y - 1 >= 0 && intmap[y - 1][x - 1].value == -1)
            count++;
        if (x - 1 >= 0 && intmap[y][x - 1].value == -1) count++;
        if (x - 1 >= 0 && y + 1 < height && intmap[y + 1][x - 1].value == -1)
            count++;
        if (y - 1 >= 0 && intmap[y - 1][x].value == -1) count++;
        if (y + 1 < height && intmap[y + 1][x].value == -1) count++;
        if (x + 1 < width && y - 1 >= 0 && intmap[y - 1][x + 1].value == -1)
            count++;
        if (x + 1 < width && intmap[y][x + 1].value == -1) count++;
        if (x + 1 < width && y + 1 < height && intmap[y + 1][x + 1].value == -1)
            count++;
        return count;
    }

    function recursive_reveale(
        x: number,
        y: number,
        visited: Array<{ x: number; y: number }>
    ) {
        if (visited.some((i) => i.x == x && i.y === y)) return;
        visited.push({ x, y });
        if (intmap[y][x].value == -1) return;
        intmap[y][x].covered = false;
        if (intmap[y][x].value != 0) return;
        if (x - 1 >= 0 && y - 1 >= 0) recursive_reveale(x - 1, y - 1, visited);
        if (x - 1 >= 0) recursive_reveale(x - 1, y, visited);
        if (x - 1 >= 0 && y + 1 < height) recursive_reveale(x - 1, y + 1, visited);
        if (y - 1 >= 0) recursive_reveale(x, y - 1, visited);
        if (y + 1 < height) recursive_reveale(x, y + 1, visited);
        if (x + 1 < width && y - 1 >= 0) recursive_reveale(x + 1, y - 1, visited);
        if (x + 1 < width) recursive_reveale(x + 1, y, visited);
        if (x + 1 < width && y + 1 < height) recursive_reveale(x + 1, y + 1, visited);
    }

    function click(x: number, y: number) {
        if (intmap[y][x].marked) return
        intmap[y][x].covered = false;
        if (intmap[y][x].value == -1) {
            game_over();
        } else {
            recursive_reveale(x, y, []);
            if (check_for_win()) game_win();
        }
    }
    function check_for_win() {
        for (let y = 0; y < height; y++) {
            for (let x = 0; x < width; x++) {
                if (intmap[y][x].value != -1 && intmap[y][x].covered)
                    return false;
            }
        }
        return true;
    }
    function game_over() {
        alert("game over");
        game_on = false;
    }
    function game_win() {
        alert("win!!!");
        game_on = false;
    }
    function start_new_game() {
        reset_minds();
        init_minds();
        init_others();
        game_on = true;
    }
</script>

<table>
    <tbody>
        {#each intmap as line, y}
            <tr>
                {#each line as box, x}
                    <td>
                        <LandBox parent_click={click} {x} {y} {box} {game_on} />
                    </td>
                {/each}
            </tr>
        {/each}
    </tbody>
</table>
<br />
<div class="flex flex-row">
    <button on:click={start_new_game}>new game</button>
</div>

<style>
    table {
        margin: auto;
        /* border-collapse: collapse; */
        background-color: darkgray;
    }
    td {
        padding: 0;
        border-spacing: 0;
    }
    tr {
        padding: 0;
    }
    div {
        text-align: center;
    }
</style>

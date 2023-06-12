<script>
    import { onDestroy } from "svelte";

    const size = 100;

    let last_time = window.performance.now();
    let frame;

    let field = [];

    (function update() {
        frame = requestAnimationFrame(update);

        const time = window.performance.now();
        const elapsed = time - last_time;

        if (elapsed < 1000) return;
        last_time = time;

        field = field.map((row, i) =>
            row.map((cell, j) => {
                const neighbors = [
                    field[i - 1]?.[j - 1],
                    field[i - 1]?.[j],
                    field[i - 1]?.[j + 1],
                    field[i][j - 1],
                    field[i][j + 1],
                    field[i + 1]?.[j - 1],
                    field[i + 1]?.[j],
                    field[i + 1]?.[j + 1],
                ].filter(Boolean).length;
                return neighbors === 3 || (cell && neighbors === 2);
            })
        );
    })();

    onDestroy(() => {
        cancelAnimationFrame(frame);
    });

    function randomize() {
        field = Array.from({ length: size }, () =>
            Array.from({ length: size }, () => Math.random() < 0.3)
        );
    }

    randomize();
</script>

<!-- svelte-ignore a11y-click-events-have-key-events -->
<div on:click={randomize}>
    <table>
        {#each field as row, i}
            <tr>
                {#each row as cell, j}
                    <td style="background: {cell ? 'green' : 'white'}" />
                {/each}
            </tr>
        {/each}
    </table>
    <div>Click on the field to randomize</div>
</div>

<style>
    :global(html) {
        width: 100%;
        height: 100%;
    }
    div {
        display: grid;
        place-items: center;
    }
    td {
        padding: 0;
        margin: 0;
        width: 4px;
        height: 4px;
    }
    @media (max-width: 800px) {
        td {
            width: 2px;
            height: 2px;
        }
    }
</style>

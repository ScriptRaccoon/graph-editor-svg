<script>
    import { fly } from "svelte/transition";
    export let showHelp = false;
    const speed = 300;
</script>

{#if showHelp}
    <section transition:fly={{ duration: speed, y: -40 }}>
        <h1>Graph editor</h1>
        <p>
            This editor doesn't have any menus. It is controlled with
            mouse and keyboard &ndash; so mobile is not supported.
        </p>
        <p>
            Create nodes by clicking on the canvas. Select a node by
            clicking on it, then it follows your mouse. Clicking again
            deselects it.
        </p>
        <p>
            Create lines by double-clicking nodes. The first
            double-clicked node is the start, the second one is the
            end of the line. Select or delect a line by clicking on
            it.
        </p>
        <p>
            When a node or line is selected, it can be edited with the
            keyboard: <strong>+</strong> and <strong>-</strong>
            control the size. <strong>r</strong> removes the element.
            When nothing is selected, <strong>r</strong> removes everything.
        </p>
        <p>
            For a selected node, <strong>c</strong> cycles through
            colors. For a selected line, <strong>t</strong> cycles through
            the four arrow types (one-sided, reversed, two-sided, none).
        </p>
        <p>
            If it's unclear, check out <a
                href="/demo.mp4"
                target="_blank">this demo video</a
            >.
        </p>
        <p class="centered">
            <button on:click={() => (showHelp = false)}>Ok</button>
        </p>
    </section>
{:else}
    <button
        transition:fly={{ duration: speed, x: 100 }}
        id="helpToggler"
        on:click={() => (showHelp = true)}
    >
        Show Help
    </button>
{/if}

<style>
    section {
        position: absolute;
        z-index: 100;
        top: 50%;
        left: 50%;
        transform: translate(-50%, -50%);
        width: min(90%, 600px);
        padding: 30px;
        background-color: #eee;
        box-shadow: 0px 0px 10px #000a;
        border-radius: 10px;
        font-size: 20px;
        text-align: justify;
    }
    h1 {
        text-align: center;
        padding: 10px 0px;
        font-size: 34px;
    }

    p {
        margin: 10px 0px;
    }

    p:last-child {
        margin-top: 20px;
    }

    #helpToggler {
        position: fixed;
        top: 10px;
        right: 15px;
    }
</style>

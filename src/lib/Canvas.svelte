<script>
    import Line from "./Line.svelte";
    import Node from "./Node.svelte";
    import { onMount, onDestroy, setContext } from "svelte";
    import { fade } from "svelte/transition";

    // global variables
    let nodeList = [];
    let lineList = [];
    let mouse = { x: 0, y: 0 };
    let selectedNode = null;
    let selectedLine = null;
    let startNode = null;

    const animationSpeed = 120;
    setContext("animationSpeed", animationSpeed);

    // make nodes and lines reactive on the selected node
    $: if (selectedNode) {
        nodeList = nodeList;
        lineList = lineList;
    }

    // makes lines reactive on the selected line
    $: if (selectedLine) {
        lineList = lineList;
    }

    // canvas click generates a new node
    function handleCanvasClick() {
        if (selectedLine || selectedNode || startNode) return;
        const newNode = {
            ...mouse,
            id: crypto.randomUUID(),
            radius: 10,
            hue: 0,
        };
        nodeList = [...nodeList, newNode];
    }

    // add mousemove and keydown handlers
    onMount(() => {
        window.addEventListener("mousemove", followMouse);
        window.addEventListener("keydown", handleKeyDown);
    });

    // remove handlers
    onDestroy(() => {
        window.removeEventListener("mousemove", followMouse);
        window.removeEventListener("keydown", handleKeyDown);
    });

    // selected node follows mouse
    function followMouse(e) {
        mouse.x = e.clientX;
        mouse.y = e.clientY;
        if (selectedNode) {
            selectedNode.x = mouse.x;
            selectedNode.y = mouse.y;
        }
    }

    // handle keydown
    function handleKeyDown(e) {
        switch (e.key) {
            // remove nodes and lines
            case "r":
                if (startNode) {
                    startNode = null;
                } else if (selectedLine) {
                    lineList = lineList.filter(
                        (line) => line != selectedLine
                    );
                    selectedLine = null;
                } else if (selectedNode) {
                    nodeList = nodeList.filter(
                        (node) => node != selectedNode
                    );
                    lineList = lineList.filter(
                        (line) =>
                            line.startNode != selectedNode &&
                            line.endNode != selectedNode
                    );
                    selectedNode = null;
                } else {
                    selectedNode = null;
                    selectedLine = null;
                    lineList = [];
                    nodeList = [];
                }
                break;
            // increase size
            case "+":
                if (selectedNode) {
                    selectedNode.radius += 1;
                } else if (selectedLine) {
                    selectedLine.width += 1;
                }
                break;
            // decrease size
            case "-":
                if (selectedNode) {
                    selectedNode.radius = Math.max(
                        1,
                        selectedNode.radius - 1
                    );
                } else if (selectedLine) {
                    selectedLine.width = Math.max(
                        1,
                        selectedLine.width - 1
                    );
                }
                break;
            // change color
            case "c":
                if (selectedNode) {
                    selectedNode.hue = (selectedNode.hue + 20) % 360;
                }
                break;
            // change arrow type
            case "t":
                if (selectedLine) {
                    selectedLine.type = (selectedLine.type + 1) % 4;
                }
                break;
        }
    }

    // select/deselect a node via click
    function handleNodeClick(node) {
        if (startNode) return;
        selectedNode = node === selectedNode ? null : node;
        selectedLine = null;
    }

    // start/finish a line by double-click on a node
    function handleNodeDoubleClick(node) {
        if (node == selectedNode) return;
        if (!startNode) {
            startNode = node;
        } else if (startNode !== node) {
            const newLine = {
                startNode: startNode,
                endNode: node,
                id: crypto.randomUUID(),
                width: 2,
                type: 1,
            };
            lineList = [...lineList, newLine];
            startNode = null;
        }
    }

    // select/deselect a line via click
    function handleLineClick(line) {
        selectedLine = line === selectedLine ? null : line;
        selectedNode = null;
    }
</script>

<!-- markup starts here -->

<svg xmlns="http://www.w3.org/2000/svg" on:click={handleCanvasClick}>
    <!-- lines -->
    {#each lineList as line (line.id)}
        <g
            out:fade={{ duration: animationSpeed }}
            on:click|stopPropagation={() => handleLineClick(line)}
        >
            <Line {line} isSelected={line === selectedLine} />
        </g>
    {/each}
    <!-- created line -->
    {#if startNode}
        <g>
            <Line
                line={{
                    startNode: startNode,
                    endNode: mouse,
                    width: 2,
                    type: 1,
                    id: "",
                }}
            />
        </g>
    {/if}
    <!-- nodes -->
    {#each nodeList as node (node.id)}
        <g
            out:fade={{ duration: animationSpeed }}
            on:click|stopPropagation={() => handleNodeClick(node)}
            on:dblclick|stopPropagation={() =>
                handleNodeDoubleClick(node)}
        >
            <Node
                {node}
                isSelected={node === selectedNode ||
                    node == startNode}
            />
        </g>
    {/each}
</svg>

<!-- make canvas fullscreen -->
<style>
    svg {
        width: 100vw;
        height: 100vh;
        display: block;
    }
    g {
        cursor: pointer;
    }
</style>

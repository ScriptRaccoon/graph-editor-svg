<script>
    import Line from "./Line.svelte";
    import Node from "./Node.svelte";
    import { onMount, onDestroy } from "svelte";

    let nodeList = [];
    let lineList = [];
    let mouse = { x: 0, y: 0 };
    let selectedNodeId = null;
    let selectedLineId = null;
    let startNode = null;

    $: selectedNodeIndex = nodeList.findIndex(
        (node) => node.id == selectedNodeId
    );

    $: selectedLineIndex = lineList.findIndex(
        (line) => line.id == selectedLineId
    );

    function handleCanvasClick() {
        if (selectedLineId || selectedNodeId || startNode) return;
        const newNode = {
            ...mouse,
            id: crypto.randomUUID(),
            radius: 10,
            hue: 0,
        };
        nodeList = [...nodeList, newNode];
    }

    onMount(() => {
        window.addEventListener("mousemove", followMouse);
        window.addEventListener("keydown", handleKeyDown);
    });

    onDestroy(() => {
        window.removeEventListener("mousemove", followMouse);
        window.removeEventListener("keydown", handleKeyDown);
    });

    function followMouse(e) {
        mouse.x = e.clientX;
        mouse.y = e.clientY;
        if (selectedNodeId) {
            nodeList[selectedNodeIndex].x = mouse.x;
            nodeList[selectedNodeIndex].y = mouse.y;
        }
    }

    function handleKeyDown(e) {
        switch (e.key) {
            case "r":
                if (startNode) {
                    startNode = null;
                } else if (selectedLineId) {
                    lineList = lineList.filter(
                        (line) => line.id != selectedLineId
                    );
                    selectedLineId = null;
                } else if (selectedNodeId) {
                    nodeList = nodeList.filter(
                        (node) => node.id != selectedNodeId
                    );
                    lineList = lineList.filter(
                        (line) =>
                            line.startNode.id != selectedNodeId &&
                            line.endNode.id != selectedNodeId
                    );
                    selectedNodeId = null;
                } else {
                    selectedNodeId = null;
                    selectedLineId = null;
                    lineList = [];
                    nodeList = [];
                }
                break;
            case "+":
                if (selectedNodeId) {
                    nodeList[selectedNodeIndex].radius += 1;
                } else if (selectedLineId) {
                    lineList[selectedLineIndex].width += 1;
                }
                break;
            case "-":
                if (selectedNodeId) {
                    nodeList[selectedNodeIndex].radius = Math.max(
                        1,
                        nodeList[selectedNodeIndex].radius - 1
                    );
                } else if (selectedLineId) {
                    lineList[selectedLineIndex].width = Math.max(
                        1,
                        lineList[selectedLineIndex].width - 1
                    );
                }
                break;
            case "c":
                if (selectedNodeId) {
                    nodeList[selectedNodeIndex].hue =
                        (nodeList[selectedNodeIndex].hue + 20) % 360;
                }
                break;
            case "t":
                if (selectedLineId) {
                    lineList[selectedLineIndex].type =
                        (lineList[selectedLineIndex].type + 1) % 4;
                }
                break;
        }
    }

    function handleNodeClick(node) {
        if (startNode) return;
        selectedNodeId = node.id === selectedNodeId ? null : node.id;
        selectedLineId = null;
    }

    function handleNodeDoubleClick(node) {
        if (node.id == selectedNodeId) return;
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

    function handleLineClick(line) {
        selectedLineId = line.id === selectedLineId ? null : line.id;
        selectedNodeId = null;
    }
</script>

<svg xmlns="http://www.w3.org/2000/svg" on:click={handleCanvasClick}>
    {#key nodeList}
        {#each lineList as line (line.id)}
            <g on:click|stopPropagation={() => handleLineClick(line)}>
                <Line
                    {line}
                    isSelected={line.id === selectedLineId}
                />
            </g>
        {/each}
    {/key}
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

    {#each nodeList as node (node.id)}
        <g
            on:click|stopPropagation={() => handleNodeClick(node)}
            on:dblclick|stopPropagation={() =>
                handleNodeDoubleClick(node)}
        >
            <Node
                {node}
                isSelected={node.id === selectedNodeId ||
                    node == startNode}
            />
        </g>
    {/each}
</svg>

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

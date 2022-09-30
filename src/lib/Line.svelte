<script>
    export let line;
    export let isSelected = false;
    $: color = isSelected ? "#888" : "#222";
    $: x1 = line.startNode.x;
    $: x2 = line.endNode.x;
    $: y1 = line.startNode.y;
    $: y2 = line.endNode.y;
    $: angle = Math.atan2(y2 - y1, x2 - x1) * (180 / Math.PI);
</script>

<line {x1} {y1} {x2} {y2} stroke={"#fff0"} stroke-width={10} />

<line {x1} {y1} {x2} {y2} stroke={color} stroke-width={line.width} />

{#if line.type == 1 || line.type == 3}
    <polyline
        style="transform-origin: {x2}px {y2}px;
               transform: translateX(-{line.endNode.radius}px);
               rotate: {angle}deg"
        points="{x2},{y2}
        {x2 - 10 * line.width},{y2 + 5 * line.width}
        {x2 - 10 * line.width},{y2 - 5 * line.width}"
        fill={color}
    />
{/if}
{#if line.type == 2 || line.type == 3}
    <polyline
        style="transform-origin: {x1}px {y1}px;
               transform: translateX({line.startNode.radius}px);
               rotate: {angle}deg"
        points="{x1},{y1}
        {x1 + 10 * line.width},{y1 + 5 * line.width}
        {x1 + 10 * line.width},{y1 - 5 * line.width}"
        fill={color}
    />
{/if}

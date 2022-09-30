<script>
    export let node;
    export let isSelected = false;
</script>

<!-- filled circle for the node -->
<circle
    cx={node.x}
    cy={node.y}
    r={node.radius}
    fill="hsl({node.hue},100%,50%)"
/>

<!-- outer circle indicating selection -->
{#if isSelected}
    <circle
        cx={node.x}
        cy={node.y}
        r={node.radius + 5}
        stroke-width="3"
        fill="none"
        stroke="#0007"
    />
{/if}

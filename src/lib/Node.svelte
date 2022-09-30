<script>
    export let node;
    export let isSelected = false;
    import { scale } from "svelte/transition";
    import { quadOut } from "svelte/easing";
    import { getContext } from "svelte";
    const animationSpeed = getContext("animationSpeed");
</script>

<!-- filled circle for the node -->
<circle
    transition:scale={{ duration: animationSpeed, easing: quadOut }}
    cx={node.x}
    cy={node.y}
    r={node.radius}
    fill="hsl({node.hue},100%,50%)"
    style="transform-origin: {node.x}px {node.y}px"
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

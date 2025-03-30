<script lang="ts">
  import { useEdges, useNodes, useSvelteFlow } from "@xyflow/svelte";

  const { screenToFlowPosition } = useSvelteFlow();

  export let onClick: () => void;
  export let id: string;
  export let x: number;
  export let y: number;

  const MENU_HEIGHT = 500;
  const MENU_WIDTH = 200;

  $: top = y < window.innerHeight - MENU_HEIGHT ? y : undefined;
  $: left = x < window.innerWidth - MENU_WIDTH ? x : undefined;
  $: right = x >= window.innerWidth - MENU_WIDTH ? window.innerWidth - x : undefined;
  $: bottom = y >= window.innerHeight - MENU_HEIGHT ? window.innerHeight - y : undefined;

  const nodes = useNodes();
  const edges = useEdges();


  function duplicateNode() {
    const node = $nodes.find((node) => node.id === id);
    if (node) {
      $nodes.push({
        ...node,
        // You should use a better id than this in production
        id: `${id}-copy${Math.random()}`,
        position: {
          x: node.position.x,
          y: node.position.y + 50,
        },
      });
    }
    $nodes = $nodes;
  }

  function deleteNode() {
    $nodes = $nodes.filter((node) => node.id !== id);
    $edges = $edges.filter((edge) => edge.source !== id && edge.target !== id);
  }
</script>

<div style="top: {top}px; left: {left}px; right:{right}px; bottom: {bottom}px" class="context-menu" on:click={onClick}>
  <p style="margin: 0.5em;">
    <small>node: {id}</small>
  </p>
  <button on:click={duplicateNode}>duplicate</button>
  <button on:click={deleteNode}>delete</button>
</div>

<style>
  .context-menu {
    background: white;
    border-style: solid;
    box-shadow: 10px 19px 20px rgba(0, 0, 0, 10%);
    position: absolute;
    z-index: 10;
  }

  .context-menu button {
    border: none;
    display: block;
    padding: 0.5em;
    text-align: left;
    width: 100%;
  }

  .context-menu button:hover {
    background: white;
  }
</style>


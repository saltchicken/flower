<script lang="ts">
  import { writable } from "svelte/store";
  import {
    SvelteFlow,
    Controls,
    Background,
    BackgroundVariant,
    MiniMap,
  } from "@xyflow/svelte";

  import Sidebar from "./Sidebar.svelte";
  import ContextMenu from "./ContextMenu.svelte";

  import "@xyflow/svelte/dist/style.css";

  const nodes = writable([
    {
      id: "1",
      type: "input",
      data: { label: "Input Node" },
      position: { x: 150, y: 5 },
    },
    {
      id: "2",
      type: "default",
      data: { label: "Node" },
      position: { x: 0, y: 150 },
    },
    {
      id: "3",
      type: "output",
      data: { label: "Output Node" },
      position: { x: 300, y: 150 },
    },
  ]);

  const edges = writable([
    {
      id: "1-2",
      type: "default",
      source: "1",
      target: "2",
      label: "Edge Text",
    },
    {
      id: "1-3",
      type: "smoothstep",
      source: "1",
      target: "3",
    },
  ]);

  let menu: {
    id: string;
    x?: number;
    y?: number;
  } | null;

  function handleContextMenu({ detail: { event, node } }) {
    event.preventDefault();
    console.log(menu);
    if (!menu) {
      // Calculate position of the context menu. We want to make sure it
      // doesn't get positioned off-screen.
      menu = {
        id: node.id,
        x: event.clientX,
        y: event.clientY,
      };
    } else {
      console.log("Why");
      menu = null;
    }
    return;
  }

  // Close the context menu if it's open whenever the window is clicked.
  function handlePaneClick() {
    menu = null;
  }
</script>

<main>
  <SvelteFlow
    {nodes}
    {edges}
    fitView
    colorMode="dark"
    on:nodecontextmenu={handleContextMenu}
    on:paneclick={handlePaneClick}
  >
    <Controls />
    <Background variant={BackgroundVariant.Dots} />
    {#if menu}
      <ContextMenu
        onClick={handlePaneClick}
        id={menu.id}
        x={menu.x}
        y={menu.y}
      />
    {/if}
    <MiniMap />
  </SvelteFlow>
  <Sidebar />
</main>

<style>
  main {
    height: 100vh;
    display: flex;
  }
</style>

<script lang="ts">
  import { writable } from "svelte/store";
  import {
    SvelteFlow,
    Controls,
    Background,
    MiniMap,
    type Node,
    type Edge,
  } from "@xyflow/svelte";

  import "@xyflow/svelte/dist/style.css";

  const nodes = writable<Node[]>([
    {
      id: "1",
      data: { label: "Hello" },
      position: { x: 0, y: 0 },
    },
    {
      id: "2",
      data: { label: "World" },
      position: { x: 150, y: 150 },
    },
  ]);

  const edges = writable<Edge[]>([
    {
      id: "1-2",
      source: "1",
      target: "2",
    },
  ]);

  let contextMenu = {
    visible: false,
    x: 0,
    y: 0,
    nodeId: null as string | null,
    type: null as "node" | "background" | null,
  };

  function handleContextMenu(event: MouseEvent) {
    console.log(event);
    event.preventDefault();
    const target = event.target as HTMLElement;
    const nodeElement = target.closest(".svelte-flow__node");

    if (nodeElement) {
      const nodeId = nodeElement.getAttribute("data-id");
      contextMenu = {
        visible: true,
        x: event.clientX,
        y: event.clientY,
        nodeId,
        type: "node",
      };
    } else if (target.closest(".svelte-flow__pane")) {
      contextMenu = {
        visible: true,
        x: event.clientX,
        y: event.clientY,
        nodeId: null,
        type: "background",
      };
    } else {
      console.log("Not a node or background");
      contextMenu.visible = false;
    }
  }

  function handleBackgroundClick(event: MouseEvent) {
    // Check if the click is on the context menu itself
    console.log(event);
    const target = event.target as HTMLElement;
    if (!target.closest(".context-menu")) {
      contextMenu.visible = false;
    }
  }

  function deleteNode() {
    if (contextMenu.nodeId) {
      nodes.update((nodes) =>
        nodes.filter((node) => node.id !== contextMenu.nodeId),
      );
      edges.update((edges) =>
        edges.filter(
          (edge) =>
            edge.source !== contextMenu.nodeId &&
            edge.target !== contextMenu.nodeId,
        ),
      );
      contextMenu.visible = false;
    }
  }

  function addNode() {
    const newId = `node-${Math.random().toString(36).substr(2, 9)}`;
    nodes.update((nodes) => [
      ...nodes,
      {
        id: newId,
        data: { label: "New Node" },
        position: { x: contextMenu.x, y: contextMenu.y },
      },
    ]);
    contextMenu.visible = false;
  }
</script>

<div
  style:height="100vh"
  on:contextmenu={handleContextMenu}
  on:click={handleBackgroundClick}
  role="application"
  aria-label="Flow diagram canvas"
>
  <SvelteFlow {nodes} {edges} fitView colorMode="dark">
    <Controls />
    <Background />
    <MiniMap />
  </SvelteFlow>
  {#if contextMenu.visible}
    <div
      class="context-menu"
      style="left: {contextMenu.x}px; top: {contextMenu.y}px;"
      role="menu"
      aria-label={contextMenu.type === "node"
        ? "Node actions"
        : "Canvas actions"}
    >
      {#if contextMenu.type === "node"}
        <button on:click={deleteNode} role="menuitem">Delete Node</button>
      {:else if contextMenu.type === "background"}
        <button on:click={addNode} role="menuitem">Add Node</button>
      {/if}
    </div>
  {/if}
</div>

<style>
  .context-menu {
    position: fixed;
    background: #1e1e1e;
    border: 1px solid #333;
    border-radius: 4px;
    padding: 8px;
    box-shadow: 0 2px 8px rgba(0, 0, 0, 0.4);
    z-index: 1000;
    color: #fff;
  }

  .context-menu button {
    display: block;
    width: 100%;
    padding: 8px 12px;
    border: none;
    background: none;
    cursor: pointer;
    text-align: left;
    color: #fff;
    border-radius: 2px;
  }

  .context-menu button:hover {
    background: #2d2d2d;
  }
</style>

<script lang="ts">
  import { SvelteFlowProvider } from "@xyflow/svelte";

  import Workflow from "./Workflow/Workflow.svelte";
</script>

<SvelteFlowProvider>
  <Workflow />
</SvelteFlowProvider>

<script lang="ts">
  import { ImageStatus } from "../types.d";
  import CloudinaryLogo from "./assets/CloudinaryLogo.svelte";
    import StepEdit from "./lib/StepEdit.svelte";
  import StepUpload from "./lib/StepUpload.svelte";
  import { imageStatus } from "./store";
</script>

<div class="max-w-xl m-auto grid grid-cols-1 place-content-between w-full h-screen p-4">
  <header class="flex justify-center">
    <h1 class="text-3xl font-bold text-gray-600 tracking-tighter">
      Remove<span class="text-gray-300">.pro</span>
    </h1>
  </header>

  <main class="block w-full">
    {#if $imageStatus === ImageStatus.READY || $imageStatus === ImageStatus.UPLOADING}
      <StepUpload />
    {:else if $imageStatus === ImageStatus.DONE}
      <StepEdit />
    {/if}
  </main>

  <footer class="flex justify-center items-center gap-x-2 font-semibold pt-10">
    Hecho con <a href="https://cloudinary.com" target="_blank" rel="noreferrer"
      ><CloudinaryLogo /></a
    >
  </footer>
</div>

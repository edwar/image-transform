<script>
  import { SkeletonImage } from 'skeleton-elements/svelte'
  import 'skeleton-elements/css/skeleton-elements.css'
  import 'two-up-element'
  import { originalImage, modifiedImage, errorImage } from "../store";
  import Error from '../assets/Error.svelte';

  let processingImage = true
  let tries = 0
  let intervalId
  let w = 0
  let h = 0

  $: {
    if(processingImage) {
      clearInterval(intervalId)
      intervalId = setInterval(() => {
        tries++
        if(tries > 10) {
          processingImage = false
        }

        const img = new Image()
        img.src = $modifiedImage
        img.onload = () => {
          processingImage = false
          clearInterval(intervalId)
        }
        img.onerror = () => {
          errorImage.set(true)
        }
      }, 300)
    }
  }
</script>

<two-up>
  <img src={$originalImage} alt="Imagen original subida por el usuario"/>
  {#if processingImage}
    <div class="flex justify-center items-center w-full h-full" bind:clientHeight={h} bind:clientWidth={w}>
      {#if w > 0 && h > 0}
        <SkeletonImage borderRadius='0%' width={w} height={h} showIcon effect="wave" color='#999' iconColor='#fff' />
      {/if}
    </div>
  {:else}
    {#if !errorImage}
      <img
        src={$modifiedImage}
        alt="Imagen original subida por el usuario"
      />
    {:else}
      <div class="flex justify-center items-center w-full h-full">
        <Error />
      </div>
    {/if}
  {/if}
</two-up>

<a download target="_blank" rel="noreferrer" href={$modifiedImage} class={`${errorImage ? 'pointer-events-none bg-blue-200 hover:bg-blue-300' : 'bg-blue-600 hover:bg-blue-700'} block w-full text-xl text-center font-bold text-white rounded-full px-4 py-4 mt-10`}>
  Descargar imagen sin fondo
</a>
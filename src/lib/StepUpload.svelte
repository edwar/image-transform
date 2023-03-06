<script lang="ts">
  import { Cloudinary } from '@cloudinary/url-gen'
  import { backgroundRemoval } from '@cloudinary/url-gen/actions/effect';

  import { ImageStatus } from "../../types.d"
  import { imageStatus, modifiedImage, originalImage } from '../store'
  import Dropzone from "dropzone";
  import "dropzone/dist/dropzone.css";

  import { onMount } from "svelte";

  const cloudinary = new Cloudinary({
    cloud: {
      cloudName: 'edwar'
    },
    url: {
      secure: true
    }
  })

  onMount(() => {
    const dropzone = new Dropzone("#dropzone", {
      uploadMultiple: false,
      acceptedFiles: ".jpg,.png,.webp,.jpeg",
      maxFiles: 1,
    });

    dropzone.on("sending", (file, xhr, formData) => {
      imageStatus.set(ImageStatus.UPLOADING)
      formData.append("upload_preset", "sdxpyaic");
      formData.append("timestamp", Date.now() / 1000);
      formData.append("api_key", 486149773318536);
    });

    dropzone.on("success", (file, response) => {
      const {
        secure_url: url,
        public_id: publicId
      } = response

      const imageWithoutBackground = cloudinary
        .image(publicId)
        .effect(backgroundRemoval())
      imageStatus.set(ImageStatus.DONE)
      if(typeof imageWithoutBackground.toURL() === 'string') {
        modifiedImage.set(imageWithoutBackground.toURL())
      }
      originalImage.set(url)
      console.log(response);
    });

    dropzone.on("error", (file, response) => {
      console.log("Ha ido mal");
      console.log(response);
    });
  });
</script>

<form
  id="dropzone"
  class="shadow-2xl rounded-lg aspect-video w-full flex justify-center items-center flex-col"
  action="https://api.cloudinary.com/v1_1/edwar/image/upload"
>
  {#if $imageStatus === ImageStatus.READY}
  <button
    class="pointer-events-none bg-blue-600 rounded-full font-bold text-white text-xl px-6 py-4"
    >Subir imagen</button
  >
  <strong class="text-lg mt-4 text-gray-400">O arrastra una imagen</strong>
  {/if}
</form>

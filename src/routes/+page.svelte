<script>
  import { onMount } from "svelte";
  import ImageCopy from "../components/ImageCopy.svelte";

  let userAgent;

  onMount(() => {
    userAgent = navigator.userAgent;
  });
</script>

<ImageCopy {userAgent} />
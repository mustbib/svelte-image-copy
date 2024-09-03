<script>
    import { onMount } from "svelte";
    import html2canvas from "html2canvas";

    export let userAgent;
    let imageDataURL = null;

    async function captureImage() {
        const elementToCapture = document.getElementById("captureElement");

        try {
            const canvas = await html2canvas(elementToCapture);
            imageDataURL = canvas.toDataURL();
            console.log('success capture');
        } catch (error) {
            console.error("Error capturing image:", error);
        }
    }

    function copyTextToClipboard() {
        let text_to_copy = document.getElementById("captureElement").innerHTML;

        text_to_copy = text_to_copy.replace(/<[^>]+>/g, "\n"); // Replace HTML tags with newline characters
        navigator.clipboard.writeText(text_to_copy).then(() => {
            // console.log("Text copied to clipboard.");
            console.log(text_to_copy);
        }).catch((error) => {
            console.error("Error copying Text:", error);
        });
    }

    function downloadImage() {
        if (imageDataURL) {
            let utc = new Date().toLocaleDateString().slice(0,10).replace(/-/g,'/');
            const a = document.createElement("a");
            a.href = imageDataURL;
            a.download = "image-"+utc+".png";
            a.style.display = "none";
            document.body.appendChild(a);
            a.click();
            document.body.removeChild(a);

            console.log("Fetched image downloaded.");
        }
    }

    function generateImage() {
        if (imageDataURL) {
            const img = document.createElement("img");
            img.src = imageDataURL;
            document.body.appendChild(img);
        }
    }

    function copyImageToClipboard() {
        const img = new Image();
        img.src = imageDataURL;
        img.style.cssText = "position:absolute; top:-1000px; left:-1000px"; // Position it off-screen

        img.onload = function () {
            const canvas = document.createElement("canvas");
            canvas.width = img.width;
            canvas.height = img.height;
            const ctx = canvas.getContext("2d");
            ctx.drawImage(img, 0, 0);

            canvas.toBlob(function (blob) {
                const item = new ClipboardItem({ "image/png": blob });
                navigator.clipboard.write([item]).then(function () {
                    console.log("Image copied to clipboard.");
                    alert("Image copied to clipboard.");
                });
            }, "image/png");
        };
    }

    async function writeClipImg() {
        try {
            const data = await fetch(imageDataURL);
            const blob = await data.blob();
            await navigator.clipboard.write([
                new ClipboardItem({
                    [blob.type]: blob,
                }),
            ]);
            alert("Fetched image copied.");
            console.log("Fetched image copied.");
        } catch (err) {
            console.error(err.name, err.message);
        }
    }

    onMount(() => {
        captureImage();
    });
</script>

<div id="captureElement">
  <h1>Hello, Svelte Image Generator!</h1>
  <p>This element would be capture.</p>
</div>

<button on:click={copyImageToClipboard}>Copy Image 1</button>
<button on:click={writeClipImg}>Copy Image 2</button>
<button on:click={copyTextToClipboard}>Copy Text</button>
<button on:click={downloadImage}>Download Image</button>
<button on:click={generateImage}>Generate Image</button>
<button on:click={() => (userAgent.toLowerCase().includes("firefox") ? downloadImage() : writeClipImg())}>Copy or Download</button>

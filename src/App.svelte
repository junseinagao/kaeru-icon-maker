<script lang="ts">
  import KaeruImage from "./assets/kaeru.png";
  import { Canvas, Layer } from "svelte-canvas";
  let canvasComponent;
  let anchorRef: HTMLAnchorElement;

  let left = 0;
  let top = 0;
  let scale = 1;

  const bgImage = new Image();
  const inputImage = new Image();

  const onFileSelected = (e) => {
    const targetImage = e.target.files[0];
    const reader = new FileReader();
    reader.readAsDataURL(targetImage);
    reader.onload = (e) => {
      inputImage.src = e.target.result as string;
    };
  };

  const downloadImage = () => {
    const canvasDom = canvasComponent.getCanvas();
    anchorRef.href = canvasDom.toDataURL("image/png");
    anchorRef.download = "kaeru-icon.png";
    anchorRef.click();
  };

  $: kaeruRender = ({ context, width, height }) => {
    bgImage.src = KaeruImage;
    context.drawImage(bgImage, 0, 0, width, height);
  };
  $: inputRender = ({ context, width, height }) => {
    context.drawImage(
      inputImage,
      0 + left,
      0 + top,
      width * scale,
      height * scale
    );
  };
</script>

<h1>Kaeru Icon Genrator</h1>

<div class="center">
  <Canvas width={640} height={640} bind:this={canvasComponent}>
    <Layer render={inputRender} />
    <Layer render={kaeruRender} />
  </Canvas>
</div>

<hr />

<input
  type="file"
  accept=".jpg, .jpeg, .png"
  on:change={(e) => onFileSelected(e)}
/>

<div class="controllers">
  <div>
    <div>タテ</div>
    <input type="range" min="-512" max="512" bind:value={top} />
  </div>
  <div>
    <div>ヨコ</div>
    <input type="range" min="-512" max="512" bind:value={left} />
  </div>
  <div>
    <div>スケール</div>
    <input type="range" min="0" max="2" step="0.1" bind:value={scale} />
  </div>
</div>

<button class="button" on:click={downloadImage}>ダウンロード</button>

<!-- svelte-ignore a11y-missing-attribute -->
<!-- svelte-ignore a11y-missing-content -->
<a bind:this={anchorRef} />

<style>
  .controllers {
    display: flex;
    justify-content: center;
    column-gap: 1rem;
  }

  .center {
    display: flex;
    justify-content: center;
  }
</style>

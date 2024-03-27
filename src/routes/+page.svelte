<script>
  const manifests = [
    "https://customer-2p3jflss4r4hmpnz.cloudflarestream.com/675985423ee4454da86def6ecbfcb01e/manifest/video.m3u8",
    "https://customer-2p3jflss4r4hmpnz.cloudflarestream.com/d6ff25ad402743dcae94677d82197c5d/manifest/video.m3u8",
  ];

  let url = "";
  let hls;

  let videoEl;

  let error = false;

  function loadVideo(url) {
    if (!url) return;
    if (hls && hls.destroy) {
      hls.destroy();
    }

    if (videoEl.canPlayType("application/vnd.apple.mpegurl")) {
      videoEl.src = url + "#t=0.1";
    } else if (Hls.isSupported()) {
      hls = new Hls({ maxBufferLength: 5, debug: true });
      hls?.loadSource(url);
      hls?.attachMedia(videoEl);
      hls?.on(Hls.Events.ERROR, function (event, data) {
        error = true;
        console.error("VideoError", { event, data });
      });
    } else {
      // Fallback to mp4
      videoEl.src = `${getMp4Url(uid)}${ios ? "#t=0.1" : ""}`;
      console.error("Hls not supported");
    }
  }

  $: loadVideo(url);
</script>

<h1>Using hls.js v1.5.7</h1>

<div>
  <button on:click={() => (url = manifests[0])}>load manifest w/ error</button>
  <button on:click={() => (url = manifests[1])}>load manifest w/o error</button>
</div>

<div>
  Loaded manifest URL: <pre>{url ||
      "No URL Loaded. Press buttons to load a manifest 👆"}</pre>
</div>

{#if error}
  <h1>ERROR Loading video!</h1>
  <div>Open Dev console to see the logs</div>
{/if}

<!-- svelte-ignore a11y-media-has-caption -->
<video bind:this={videoEl} autoplay muted />

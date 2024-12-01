<svelte:head>
  <link rel="preconnect" href="https://fonts.googleapis.com">
  <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
  <link href="https://fonts.googleapis.com/css2?family=Dosis:wght@700&display=swap" rel="stylesheet">
  <title>Logo Generator</title>
  <link rel="shortcut icon" href="/favicon.ico">
</svelte:head>

<script>
  import { onMount } from 'svelte';
  let text1 = 'VR';
  let text2 = 'CHAT';
  let canvas;
  let ctx;
  let fontLoaded = false;

  onMount(async () => {
    // フォントの読み込みを待つ
    await document.fonts.ready;
    // Dosisフォントが特に読み込まれるのを確認
    await document.fonts.load('700 10px "Dosis"');
    
    fontLoaded = true;
    ctx = canvas.getContext('2d', { alpha: true });
    generateLogo(false);
  });

  function generateLogo(forExport = false) {
    if (!ctx || !fontLoaded) return;

    ctx.font = '700 80px "Dosis"';
    const text1Metrics = ctx.measureText(text1);
    const text2Metrics = ctx.measureText(text2);
    const spacing = 15;
    
    const leftPadding = 20;    
    const rightPadding = 40;   
    const bottomPadding = -30;  
    const topPadding = 50;     
    const totalTextWidth = text1Metrics.width + text2Metrics.width + spacing;
    const totalWidth = totalTextWidth + leftPadding + rightPadding;
    
    canvas.width = totalWidth + 80;
    canvas.height = 200;

    ctx.clearRect(0, 0, canvas.width, canvas.height);

    if (!forExport) {
        ctx.fillStyle = '#FFFFFF';
        ctx.fillRect(0, 0, canvas.width, canvas.height);
    }

    const cornerRadius = 15;
    const textHeight = 90 + topPadding + bottomPadding;
    const x = (canvas.width - totalWidth) / 2;
    const y = (canvas.height - textHeight) / 2;
    const bubbleCenterY = y + textHeight / 2;

    ctx.font = '700 80px "Dosis"';

    ctx.beginPath();
    ctx.moveTo(x + cornerRadius, y);
    ctx.lineTo(x + totalWidth - cornerRadius, y);
    ctx.quadraticCurveTo(x + totalWidth, y, x + totalWidth, y + cornerRadius);
    ctx.lineTo(x + totalWidth, y + textHeight - cornerRadius);
    ctx.quadraticCurveTo(x + totalWidth, y + textHeight, x + totalWidth - cornerRadius, y + textHeight);
    
    ctx.lineTo(x + totalWidth - cornerRadius - 5, y + textHeight);
    ctx.lineTo(x + totalWidth - cornerRadius - 5, y + textHeight + 30);
    ctx.lineTo(x + totalWidth - cornerRadius - 40, y + textHeight);
    
    ctx.lineTo(x + cornerRadius, y + textHeight);
    ctx.quadraticCurveTo(x, y + textHeight, x, y + textHeight - cornerRadius);
    ctx.lineTo(x, y + cornerRadius);
    ctx.quadraticCurveTo(x, y, x + cornerRadius, y);
    ctx.closePath();

    ctx.fillStyle = '#FFFFFF';
    ctx.fill();
    ctx.lineWidth = 8;
    ctx.strokeStyle = '#000000';
    ctx.stroke();

    ctx.fillStyle = '#000000';
    ctx.textAlign = 'left';
    ctx.textBaseline = 'middle';
    ctx.fillText(text1, x + leftPadding, bubbleCenterY);

    const chatX = x + leftPadding + text1Metrics.width + spacing;
    const chatBoxHeight = textHeight/1.4;
    const chatY = bubbleCenterY - chatBoxHeight/2 - 5;
    const chatBoxWidth = text2Metrics.width + 20;
    const chatCornerRadius = 10;

    ctx.beginPath();
    ctx.moveTo(chatX + chatCornerRadius, chatY);
    ctx.lineTo(chatX + chatBoxWidth - chatCornerRadius, chatY);
    ctx.quadraticCurveTo(chatX + chatBoxWidth, chatY, chatX + chatBoxWidth, chatY + chatCornerRadius);
    ctx.lineTo(chatX + chatBoxWidth, chatY + chatBoxHeight - chatCornerRadius);
    ctx.quadraticCurveTo(chatX + chatBoxWidth, chatY + chatBoxHeight, chatX + chatBoxWidth - chatCornerRadius, chatY + chatBoxHeight);
    ctx.lineTo(chatX + chatCornerRadius, chatY + chatBoxHeight);
    ctx.quadraticCurveTo(chatX, chatY + chatBoxHeight, chatX, chatY + chatBoxHeight - chatCornerRadius);
    ctx.lineTo(chatX, chatY + chatCornerRadius);
    ctx.quadraticCurveTo(chatX, chatY, chatX + chatCornerRadius, chatY);
    ctx.closePath();
    
    ctx.fillStyle = '#000000';
    ctx.fill();

    ctx.fillStyle = '#FFFFFF';
    ctx.fillText(text2, chatX + 10, bubbleCenterY);
  }

  function downloadImage() {
    generateLogo(true);
    const link = document.createElement('a');
    link.download = 'vrchat-logo.png';
    link.href = canvas.toDataURL('image/png', 1.0);
    link.click();
    generateLogo(false);
  }

  $: if (ctx && fontLoaded && (text1 || text2)) generateLogo(false);
</script>

<main>
  <h1 class="dosis-logo">VRSNS風ロゴジェネレーター</h1>
  
  <div class="input-group">
    <input 
      type="text" 
      bind:value={text1} 
      placeholder="VR"
      class="dosis-logo"
    >
    <input 
      type="text" 
      bind:value={text2} 
      placeholder="CHAT"
      class="dosis-logo"
    >
  </div>
  
  {#if !fontLoaded}
    <div class="loading">フォントを読み込み中...</div>
  {/if}
  
  <canvas 
    bind:this={canvas} 
    on:click={downloadImage}
    title="クリックしてダウンロード"
    class:hidden={!fontLoaded}
  ></canvas>

  <p class="dosis-logo">↑ キャンバスをクリックするとPNG画像をダウンロードできます</p>
</main>

<style>
  :global(body) {
    background-color: #1a1b26;
    color: #a9b1d6;
    margin: 0;
    padding: 0;
  }

  :global(.dosis-logo) {
    font-family: "Dosis", sans-serif;
    font-optical-sizing: auto;
    font-weight: 700;
    font-style: normal;
    color: #a9b1d6;
  }

  main {
    text-align: center;
    padding: 20px;
  }

  .input-group {
    display: flex;
    gap: 10px;
    justify-content: center;
    margin: 20px 0;
  }

  input {
    padding: 10px;
    font-size: 16px;
    width: 140px;
    border: 2px solid #a9b1d6;
    border-radius: 5px;
    background-color: #1a1b26;
    color: #a9b1d6;
  }

  input::placeholder {
    color: #565f89;
  }

  canvas {
    max-width: 100%;
    height: auto;
    margin: 20px auto;
    display: block;
    border: 1px solid #a9b1d6;
    cursor: pointer;
  }

  canvas:hover {
    border-color: #fff;
  }

  p {
    color: #a9b1d6;
    font-size: 14px;
  }

  .loading {
    color: #a9b1d6;
    margin: 20px 0;
    font-size: 16px;
  }

  .hidden {
    display: none;
  }
</style>

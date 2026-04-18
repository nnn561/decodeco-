<!DOCTYPE html>
<html lang="zh-CN">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
  <title>鑽石貼紙編輯器 (手機版)</title>
  <style>
    body { font-family: sans-serif; text-align: center; background: #f4f4f4; margin: 0; padding: 10px; }
    .card { background: white; padding: 15px; border-radius: 12px; box-shadow: 0 2px 10px rgba(0,0,0,0.1); margin-bottom: 15px; }
    
    /* 畫布適應手機螢幕 */
    #myCanvas { 
      border: 1px solid #ccc; 
      background: #fff; 
      max-width: 100%; 
      height: auto; 
      touch-action: none; /* 防止手機點擊時頁面滾動 */
    }

    .sticker-list { display: flex; justify-content: center; gap: 10px; margin: 15px 0; }
    .sticker-option {
      width: 60px; height: 60px;
      border: 3px solid transparent;
      cursor: pointer;
      border-radius: 8px;
      object-fit: contain;
      background: #eee;
    }
    .sticker-option.selected { border-color: #0078d7; background: #e6f7ff; }
    
    button { padding: 10px 20px; font-size: 16px; border-radius: 8px; border: none; background: #ff4d4f; color: white; cursor: pointer; }
  </style>
</head>
<body>

  <div class="card">
    <h3 style="margin-top:0">1. 上傳底圖</h3>
    <input type="file" id="imgInput" accept="image/*">
  </div>

  <div class="card">
    <h3>2. 選擇鑽石點擊上方貼上</h3>
    <div class="sticker-list" id="stickerList">
      <img src="image/sticker1.jpg" class="sticker-option selected" onclick="selectSticker(0)">
      <img src="image/sticker2.jpg" class="sticker-option" onclick="selectSticker(1)">
      <img src="image/sticker3.jpg" class="sticker-option" onclick="selectSticker(2)">
    </div>
    <button onclick="clearCanvas()">清空貼紙</button>
  </div>

  <canvas id="myCanvas"></canvas>

  <script>
    const canvas = document.getElementById('myCanvas');
    const ctx = canvas.getContext('2d');
    let mainImgObj = new Image();
    let stickerRecords = [];
    let currentStickerIndex = 0;

    // 預加載貼紙 (對應你 GitHub 裡的 image 檔案夾和 .jpg 格式)
    const stickerPaths = ['image/sticker1.jpg', 'image/sticker2.jpg', 'image/sticker3.jpg'];
    const stickers = stickerPaths.map(src => {
      const img = new Image();
      img.src = src;
      return img;
    });

    // 處理圖片上傳
    document.getElementById('imgInput').onchange = function(e) {
      const file = e.target.files[0];
      if (!file) return;
      const url = URL.createObjectURL(file);
      mainImgObj.src = url;
      mainImgObj.onload = function() {
        canvas.width = mainImgObj.width;
        canvas.height = mainImgObj.height;
        draw();
      };
    };

    function selectSticker(idx) {
      currentStickerIndex = idx;
      const imgs = document.querySelectorAll('.sticker-option');
      imgs.forEach((img, i) => img.classList.

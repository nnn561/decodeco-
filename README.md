<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>手機鑽石貼紙</title>
    <style>
        body { margin: 0; padding: 10px; font-family: sans-serif; text-align: center; background: #eee; }
        #canvas { background: white; width: 100%; max-width: 500px; height: auto; border: 1px solid #ccc; touch-action: none; }
        .toolbar { display: flex; justify-content: center; gap: 10px; margin-top: 10px; background: white; padding: 10px; border-radius: 10px; }
        .stk-btn { width: 50px; height: 50px; border: 2px solid transparent; }
        .stk-btn.active { border-color: blue; }
    </style>
</head>
<body>
    <input type="file" id="upload" accept="image/*" style="margin-bottom:10px;">
    <br>
    <canvas id="canvas"></canvas>
    
    <div class="toolbar">
        <img src="sticker1.png" class="stk-btn active" onclick="setStk(0, this)">
        <img src="sticker2.png" class="stk-btn" onclick="setStk(1, this)">
        <img src="sticker3.png" class="stk-btn" onclick="setStk(2, this)">
    </div>

    <script>
        const canvas = document.getElementById('canvas');
        const ctx = canvas.getContext('2d');
        let bg = new Image(), stks = [], records = [], curIdx = 0;

        // 預加載貼紙
        ['sticker1.png','sticker2.png','sticker3.png'].forEach(src => {
            let img = new Image(); img.src = src; stks.push(img);
        });

        document.getElementById('upload').onchange = (e) => {
            const reader = new FileReader();
            reader.onload = (f) => {
                bg.src = f.target.result;
                bg.onload = () => {
                    canvas.width = bg.width;
                    canvas.height = bg.height;
                    draw();
                };
            };
            reader.readAsDataURL(e.target.files[0]);
        };

        function setStk(i, el) {
            curIdx = i;
            document.querySelectorAll('.stk-btn').forEach(b => b.classList.remove('active'));
            el.classList.add('active');
        }

        // 同時支持電腦點擊和手機觸摸
        canvas.addEventListener('pointerdown', (e) => {
            if(!bg.src) return;
            const rect = canvas.getBoundingClientRect();
            const scaleX = canvas.width / rect.width;
            const scaleY = canvas.height / rect.height;
            const x = (e.clientX - rect.left) * scaleX;
            const y = (e.clientY - rect.top) * scaleY;
            records.push({x, y, i: curIdx});
            draw();
        });

        function draw() {
            ctx.clearRect(0,0,canvas.width,canvas.height);
            ctx.drawImage(bg, 0, 0);
            records.forEach(r => {
                let size = canvas.width * 0.15; // 貼紙大小
                ctx.drawImage(stks[r.i], r.x - size/2, r.y - size/2, size, size);
            });
        }
    </script>
</body>
</html>
# decodeco-

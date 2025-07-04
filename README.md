<!DOCTYPE html>
<html>
<head>
  <meta charset="UTF-8">
  <title>Seberus Prototype</title>
  <style>
    canvas { background: #081a11; display: block; margin: 0 auto; }
    .btn { position: absolute; padding: 10px; background: rgba(255,255,255,0.3); border: none; font-size: 16px; }
    #left{ left: 10px; bottom: 80px; }
    #right{ left: 80px; bottom: 80px; }
    #up{ left: 45px; bottom: 140px; }
    #atk{ right: 10px; bottom: 80px; }
    #shoot{ right: 80px; bottom: 80px; }
    #parry{ right: 10px; bottom: 150px; }
  </style>
</head>
<body>
  <canvas id="game" width="480" height="360"></canvas>
  <button class="btn" id="left">?</button>
  <button class="btn" id="right">?</button>
  <button class="btn" id="up">?</button>
  <button class="btn" id="atk">??</button>
  <button class="btn" id="shoot">??</button>
  <button class="btn" id="parry">???</button>
  <script>
    const canvas = document.getElementById('game');
    const ctx = canvas.getContext('2d');
    let px = 240, py = 300, vx = 0, state = 'idle', seberusHp = 300;
    let phase = 1, heads = 3, regenTimer = 0, regenDelay = 0;
    let eyeRed = false;

    function update(dt) {
      // Player movement
      px = Math.max(20, Math.min(460, px + vx * dt));
      // regen
      regenTimer += dt;
      if (phase === 3 && heads ===1 && regenTimer > (regenDelay>0? regenDelay:15)*1000) {
        seberusHp = Math.min(300, seberusHp + 30);
        regenTimer = 0;
        regenDelay = 15000;
        eyeRed = false;
      }
      // phase logic
      if (seberusHp < 200 && phase === 1) { phase=2; }
      if (seberusHp < 100 && phase === 2) { phase=3; }

      // blinking eye when about to roar
      if (phase ===3) {
        eyeRed = (regenTimer > 14000);
      }
    }

    function draw() {
      ctx.clearRect(0,0,480,360);
      ctx.fillStyle = '#0f0'; ctx.fillRect(px-10, py-20, 20, 40);
      // Seberus
      ctx.fillStyle = '#f00';
      ctx.fillRect(200, 100, 80, 200);
      // HP
      ctx.fillStyle = '#fff';
      ctx.fillText(`HP: ${seberusHp}`, 10,20);
      ctx.fillText(`Phase:${phase} Heads:${heads}`,10,40);
      if (eyeRed) {
        ctx.strokeStyle = 'red';
        ctx.beginPath();
        ctx.arc(240,140,10,0,Math.PI*2);
        ctx.stroke();
      }
    }

    let last = performance.now();
    function loop(now) {
      const dt = now - last;
      update(dt); draw();
      last = now;
      requestAnimationFrame(loop);
    }
    loop(last);

    document.getElementById('left').addEventListener('touchstart', ()=>vx=-0.1);
    document.getElementById('left').addEventListener('touchend', ()=>vx=0);
    document.getElementById('right').addEventListener('touchstart', ()=>vx=0.1);
    document.getElementById('right').addEventListener('touchend', ()=>vx=0);
    document.getElementById('up').addEventListener('touchstart', ()=>vy=-0.1);
    document.getElementById('up').addEventListener('touchend', ()=>vy=0);
    document.body.onkeydown = e => {
      if (e.key=='ArrowLeft') vx=-0.1;
      if (e.key=='ArrowRight') vx=0.1;
      if (e.key=='j') { if (phase===3&&eyeRed) { regenDelay=15000; eyeRed=false; } }
    }
    document.body.onkeyup = e => {
      if (e.key=='ArrowLeft'||e.key=='ArrowRight') vx=0;
    }
  </script>
</body>
</html>

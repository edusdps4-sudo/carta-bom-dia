<!DOCTYPE html>
<html lang="pt-BR">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Adivinha quem amanheceu pensando em vc 💌</title>
<style>
  * { margin: 0; padding: 0; box-sizing: border-box; }

  body {
    min-height: 100vh;
    display: flex;
    align-items: center;
    justify-content: center;
    padding: 24px;
    font-family: Georgia, 'Times New Roman', serif;
    background: linear-gradient(135deg, #fff5f5 0%, #ffe3ec 100%);
    overflow-x: hidden;
  }

  .scene { perspective: 1400px; }

  .envelope {
    position: relative;
    width: min(92vw, 400px);
    height: 260px;
    cursor: pointer;
    transform-style: preserve-3d;
    transition: opacity .5s ease, transform .5s ease;
  }
  .scene.reading .envelope {
    opacity: 0;
    transform: scale(.85);
    pointer-events: none;
  }

  .env-back {
    position: absolute; inset: 0;
    background: #f2b8c6;
    border-radius: 10px;
    box-shadow: 0 24px 48px rgba(214, 69, 107, .25);
  }

  .env-letter-peek {
    position: absolute;
    left: 16px; right: 16px; top: 14px; bottom: 14px;
    background: #fffdf8;
    border-radius: 6px;
    box-shadow: 0 4px 12px rgba(0,0,0,.12);
    display: flex; align-items: center; justify-content: center;
    text-align: center;
    padding: 20px;
    font-size: 18px;
    color: #b03060;
    transition: transform .8s cubic-bezier(.2,.8,.25,1) .35s;
    z-index: 3;
  }
  .scene.open .env-letter-peek { transform: translateY(-120px); }

  .env-front {
    position: absolute; inset: 0;
    background: #f7cdd9;
    border-radius: 10px;
    clip-path: polygon(0 0, 50% 52%, 100% 0, 100% 100%, 0 100%);
    z-index: 4;
  }

  .env-msg {
    position: absolute;
    left: 0; right: 0; top: 56%; bottom: 0;
    display: flex;
    align-items: center;
    justify-content: center;
    text-align: center;
    padding: 0 24px;
    font-size: 19px;
    font-style: italic;
    line-height: 1.5;
    color: #8e2549;
    z-index: 5;
    transition: opacity .4s ease;
  }
  .scene.open .env-msg { opacity: 0; }

  .env-flap {
    position: absolute;
    left: 0; right: 0; top: 0;
    height: 52%;
    background: #ee9db4;
    border-radius: 10px 10px 0 0;
    clip-path: polygon(0 0, 100% 0, 50% 100%);
    transform-origin: top center;
    transition: transform .7s cubic-bezier(.4,0,.2,1);
    z-index: 5;
    backface-visibility: hidden;
  }
  .scene.open .env-flap { transform: rotateX(180deg); z-index: 1; }

  .seal {
    position: absolute;
    left: 50%; top: 52%;
    transform: translate(-50%, -50%);
    width: 64px; height: 64px;
    border-radius: 50%;
    background: radial-gradient(circle at 35% 30%, #e4577a, #b03060);
    display: flex; align-items: center; justify-content: center;
    font-size: 28px; color: #fff;
    box-shadow: 0 6px 14px rgba(176,48,96,.4);
    z-index: 6;
    transition: transform .4s ease, opacity .4s ease;
    animation: pulse 1.8s ease-in-out infinite;
  }
  .scene.open .seal { transform: translate(-50%,-50%) scale(0); opacity: 0; }
  @keyframes pulse {
    0%, 100% { box-shadow: 0 6px 14px rgba(176,48,96,.4), 0 0 0 0 rgba(176,48,96,.35); }
    50%      { box-shadow: 0 6px 14px rgba(176,48,96,.4), 0 0 0 14px rgba(176,48,96,0); }
  }

  .hint {
    position: absolute;
    left: 50%; bottom: -44px;
    transform: translateX(-50%);
    font-size: 14px;
    color: #b03060;
    white-space: nowrap;
    animation: floaty 2.4s ease-in-out infinite;
  }
  @keyframes floaty { 0%,100%{ transform:translate(-50%,0);} 50%{ transform:translate(-50%,-6px);} }
  .scene.open .hint, .scene.reading .hint { display: none; }

  .letter-overlay {
    position: fixed;
    inset: 0;
    background: rgba(255, 240, 245, .92);
    backdrop-filter: blur(4px);
    display: flex;
    align-items: flex-start;
    justify-content: center;
    padding: 32px 16px;
    overflow-y: auto;
    opacity: 0;
    pointer-events: none;
    transition: opacity .6s ease .2s;
    z-index: 10;
  }
  .letter-overlay.reading { opacity: 1; pointer-events: auto; }

  .letter {
    width: min(100%, 600px);
    background: #fffdf8;
    border-radius: 12px;
    box-shadow: 0 24px 60px rgba(176, 48, 96, .18);
    padding: 40px 32px;
    position: relative;
    transform: translateY(30px);
    transition: transform .6s ease .3s;
  }
  .letter-overlay.reading .letter { transform: translateY(0); }

  .stamp {
    position: absolute;
    top: 16px; right: 16px;
    width: 56px; height: 68px;
    background: #fff;
    border: 2px dashed #e4577a;
    border-radius: 4px;
    display: flex; align-items: center; justify-content: center;
    font-size: 24px;
    transform: rotate(6deg);
  }

  .letter h1 {
    font-size: 24px;
    color: #b03060;
    text-align: center;
    margin-bottom: 20px;
    font-weight: normal;
  }
  .letter p {
    font-size: 17px;
    line-height: 1.8;
    color: #4a3b40;
    margin-bottom: 16px;
    opacity: 0;
    transform: translateY(12px);
    transition: opacity .5s ease, transform .5s ease;
  }
  .letter-overlay.reading .letter p { opacity: 1; transform: translateY(0); }
  .letter .center { text-align: center; color: #b03060; }

  .close-btn {
    display: block;
    margin: 24px auto 0;
    padding: 12px 28px;
    font-family: inherit;
    font-size: 15px;
    color: #fff;
    background: #b03060;
    border: none;
    border-radius: 999px;
    cursor: pointer;
    transition: background .3s ease, transform .2s ease;
  }
  .close-btn:hover { background: #8e2549; transform: translateY(-2px); }

  .heart {
    position: fixed;
    bottom: -30px;
    color: rgba(214, 69, 107, .5);
    animation: rise linear forwards;
    pointer-events: none;
    z-index: 15;
  }
  @keyframes rise {
    to { transform: translateY(-110vh) rotate(25deg); opacity: 0; }
  }
</style>
</head>
<body>

<div class="scene" id="scene">
  <div class="envelope" id="envelope" role="button" aria-label="Abrir carta" tabindex="0">
    <div class="env-back"></div>
    <div class="env-letter-peek">Bom dia! ☀️</div>
    <div class="env-front"></div>
    <div class="env-msg">Adivinha quem amanheceu pensando em vc! ❤️</div>
    <div class="env-flap"></div>
    <div class="seal">❤</div>
  </div>
  <div class="hint">✨ clique no lacre para abrir ✨</div>
</div>

<div class="letter-overlay" id="overlay">
  <div class="letter">
    <div class="stamp">💌</div>
    <h1>Adivinha quem amanheceu pensando em você? ❤️</h1>
    <p>Se você chegou até aqui, é porque resolveu abrir a carta... então agora aguenta, porque eu preparei isso especialmente para você. 😄</p>
    <p>Bom dia!</p>
    <p>Que hoje seja um daqueles dias em que as coisas, mesmo as pequenas, consigam arrancar um sorriso seu.</p>
    <p>Que você tenha força para enfrentar aquilo que precisar, tranquilidade para não se preocupar com o que não depende de você e sabedoria para aproveitar cada coisa boa que aparecer pelo caminho.</p>
    <p>Que não falte saúde, paz, coragem, motivos para sorrir e, principalmente, esperança de que coisas boas ainda estão por vir.</p>
    <p>Se alguma coisa não sair como você esperava, respira... um dia ruim não define a sua vida. Amanhã existe justamente porque sempre podemos tentar de novo.</p>
    <p>Então levanta, coloca esse sorriso no rosto e vai viver o seu dia. Você merece que ele seja bom. E se hoje ninguém ainda te disse isso...</p>
    <p>Eu estou te desejando um dia maravilhoso.</p>
    <p>Que seu café seja gostoso, seu caminho seja leve, seu coração fique tranquilo e que, no meio da correria, você encontre pelo menos um motivo para sorrir.</p>
    <p>E lembre-se:</p>
    <p>Tem alguém aqui que acordou, lembrou de você e decidiu transformar esse pensamento em um bom dia. ❤️</p>
    <p class="center">Agora pode seguir seu dia...<br>Mas não esquece de sorrir. 😉</p>
    <button class="close-btn" id="closeBtn">Guardar a carta 💌</button>
  </div>
</div>

<script>
  const scene = document.getElementById('scene');
  const envelope = document.getElementById('envelope');
  const overlay = document.getElementById('overlay');
  const closeBtn = document.getElementById('closeBtn');
  const letterParas = document.querySelectorAll('.letter p');

  envelope.addEventListener('click', () => {
    if (!scene.classList.contains('open')) {
      scene.classList.add('open');
      setTimeout(() => {
        scene.classList.add('reading');
        overlay.classList.add('reading');
      }, 1100);
    }
  });
  envelope.addEventListener('keydown', e => {
    if (e.key === 'Enter' || e.key === ' ') { e.preventDefault(); envelope.click(); }
  });

  closeBtn.addEventListener('click', () => {
    overlay.classList.remove('reading');
    scene.classList.remove('reading');
    setTimeout(() => scene.classList.remove('open'), 500);
  });

  function stagger() {
    letterParas.forEach((p, i) => {
      p.style.transitionDelay = (0.45 + i * 0.14) + 's';
    });
  }
  stagger();

  function spawnHeart() {
    if (!overlay.classList.contains('reading')) return;
    const h = document.createElement('div');
    h.className = 'heart';
    h.textContent = ['❤','🧡','💛','💗'][Math.floor(Math.random() * 4)];
    h.style.left = Math.random() * 100 + 'vw';
    h.style.fontSize = (14 + Math.random() * 20) + 'px';
    h.style.animationDuration = (4 + Math.random() * 4) + 's';
    document.body.appendChild(h);
    setTimeout(() => h.remove(), 9000);
  }
  setInterval(spawnHeart, 650);
</script>

</body>
</html>

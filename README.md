<!--
============================================================
INVITACIÓN FLIPBOOK (UN SOLO index.html)
✅ Flip book bidireccional (prev/next) — EXCEPTO portada: solo abre con el anillo
✅ Página girando (3D) + destellos al pasar de sección
✅ Partículas flotantes en portada (azul, rose gold, blanco, plateado)
✅ Cursor mágico (visible en TODAS las páginas) + rastro de corazones/partículas
✅ Partículas que siguen el cursor incluso dentro del flipbook
✅ Reglas de audio/video:
  - Página 1 (PORTADA): fondo rose gold + anillo mágico
    * Al cargar: intenta iniciar marcha_nupcial.mp3 (si el navegador bloquea autoplay, se inicia en el primer click)
    * Al click en el anillo: inicia/reanuda marcha_nupcial.mp3 y abre Página 2 (apertura.mp4)
  - Página 2: apertura.mp4
  - Página 3: promesa.mp4 (al entrar a página 3, reproduce automáticamente)
  - Página 4: estheryerick.mp4
  - Página 5: esposa.mp4 (al entrar a página 5, reproduce automáticamente esposa.mp4
            + suena automáticamente unmundodiferente.mp3)
  - Página 6: padres.mp4
  - Página 7: boda.mp4
  - Página 8: ceremonia.mp4

📁 Archivos esperados en /assets:
  marcha_nupcial.mp3
  unmundodiferente.mp3
  apertura.mp4
  promesa.mp4
  estheryerick.mp4
  esposa.mp4
  padres.mp4
  boda.mp4
  ceremonia.mp4
============================================================
-->
<!doctype html>
<html lang="es">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width,initial-scale=1" />
  <title>Invitación</title>

  <style>
    :root{
      --bg1:#05060a;
      --bg2:#0a0d18;

      --text:#f4f7ff;
      --muted:#b7c0d6;
      --line: rgba(255,255,255,.12);
      --card: rgba(255,255,255,.06);

      /* Rose gold background page 1 */
      --roseA:#2a141f;
      --roseB:#4a2332;
      --roseGlow: rgba(231,168,184,.25);

      /* plata */
      --silver1:#f7fbff;
      --silver2:#cfd6e2;
      --silver3:#7e8796;
      --silver4:#eef2f7;

      /* gema azul */
      --blue1:#bfe6ff;
      --blue2:#4bb7ff;
      --blue3:#1677ff;
      --blue4:#0a2a8a;

      /* partículas */
      --pBlue:  rgba(75,183,255,.80);
      --pRose:  rgba(231,168,184,.78);
      --pWhite: rgba(255,255,255,.85);
      --pSilver:rgba(215,219,229,.85);
    }

    *{ box-sizing:border-box; }
    html,body{ height:100%; }
    body{
      margin:0;
      font-family: ui-sans-serif, system-ui, -apple-system, Segoe UI, Roboto, Arial;
      color:var(--text);
      background:
        radial-gradient(900px 520px at 20% 10%, rgba(75,183,255,.18), transparent 60%),
        radial-gradient(850px 520px at 80% 25%, rgba(231,168,184,.12), transparent 60%),
        linear-gradient(180deg, var(--bg1), var(--bg2));
      overflow:hidden;
      cursor:none; /* cursor mágico */
    }

    /* Partículas portada (fondo) */
    #bgParticles{
      position:fixed;
      inset:0;
      z-index:0;
      pointer-events:none;
      opacity: 1;
      transition: opacity 450ms ease;
    }

    /* Partículas del cursor (siempre) */
    #fxCanvas{
      position:fixed;
      inset:0;
      z-index: 90;
      pointer-events:none;
    }

    /* Destello transición */
    #flash{
      position: fixed;
      inset: 0;
      z-index: 80;
      pointer-events:none;
      background:
        radial-gradient(320px 220px at var(--x,50%) var(--y,50%), rgba(255,255,255,.35), transparent 60%),
        radial-gradient(520px 320px at var(--x,50%) var(--y,50%), rgba(75,183,255,.16), transparent 65%),
        radial-gradient(560px 380px at var(--x,50%) var(--y,50%), rgba(231,168,184,.12), transparent 70%);
      opacity: 0;
      transition: opacity 220ms ease;
    }
    #flash.on{ opacity: 1; }

    /* Cursor mágico */
    .cursor{
      position: fixed;
      z-index: 100;
      pointer-events:none;
      transform: translate(-50%, -50%);
      will-change: transform;
    }
    .cursor .dot{
      width: 10px; height: 10px;
      border-radius: 999px;
      background: rgba(255,255,255,.88);
      box-shadow:
        0 0 18px rgba(75,183,255,.45),
        0 0 28px rgba(231,168,184,.22);
    }
    .cursor .ring{
      position:absolute;
      left:50%; top:50%;
      transform: translate(-50%,-50%);
      width: 28px; height: 28px;
      border-radius: 999px;
      border: 1px solid rgba(255,255,255,.25);
      box-shadow: 0 0 22px rgba(75,183,255,.20);
      opacity:.75;
    }

    /* Stage */
    .stage{
      position:relative;
      z-index: 2;
      height: 100%;
      display:flex;
      align-items:center;
      justify-content:center;
      padding: 22px;
    }

    /* Flipbook */
    .bookWrap{
      width: min(1020px, 96vw);
      height: min(680px, 82vh);
      perspective: 1900px;
      position: relative;
      filter: drop-shadow(0 28px 70px rgba(0,0,0,.55));
    }

    .book{
      width:100%;
      height:100%;
      position:relative;
      transform-style: preserve-3d;
    }

    .page{
      position:absolute;
      inset:0;
      transform-style: preserve-3d;
      transform-origin: left center;
      transition: transform 900ms cubic-bezier(.18,.9,.17,1);
      border-radius: 24px;
      overflow:hidden;
      border: 1px solid rgba(255,255,255,.10);
      background: linear-gradient(180deg, rgba(255,255,255,.07), rgba(255,255,255,.03));
      box-shadow: 0 22px 60px rgba(0,0,0,.45);
    }

    /* Lomo */
    .page::before{
      content:"";
      position:absolute;
      top:0; bottom:0;
      left:0;
      width: 34px;
      background: linear-gradient(90deg, rgba(0,0,0,.22), rgba(0,0,0,0));
      opacity: .55;
      pointer-events:none;
      transform: translateZ(1px);
    }

    .page.flipped{
      transform: rotateY(-180deg);
    }

    .face{
      position:absolute;
      inset:0;
      backface-visibility:hidden;
      display:flex;
      flex-direction:column;
      padding: 26px 24px;
      gap: 12px;
    }

    .front{ transform: rotateY(0deg) translateZ(1px); }
    .back{ transform: rotateY(180deg) translateZ(1px); }

    /* Página 1 (portada) fondo rose gold */
    .coverFace{
      background:
        radial-gradient(700px 420px at 50% 25%, var(--roseGlow), transparent 60%),
        linear-gradient(180deg, var(--roseA), var(--roseB));
    }

    .title{
      margin:0;
      font-size: 36px;
      letter-spacing:.2px;
      text-align:center;
    }
    .subtitle{
      margin:0;
      text-align:center;
      color: var(--muted);
      font-size: 14px;
      letter-spacing:.03em;
    }

    .yes{
      margin: 6px 0 0;
      font-size: 46px;
      text-align:center;
      letter-spacing:.2px;
    }
    .instruction{
      margin: 0;
      text-align:center;
      color: rgba(255,255,255,.80);
      font-size: 14px;
      letter-spacing:.05em;
      text-transform: lowercase;
    }

    .panel{
      flex:1;
      display:flex;
      align-items:center;
      justify-content:center;
      position:relative;
    }

    .contentCard{
      width: min(860px, 100%);
      background: rgba(0,0,0,.14);
      border: 1px solid rgba(255,255,255,.10);
      border-radius: 20px;
      padding: 18px;
    }

    .nav{
      display:flex;
      justify-content:space-between;
      gap:10px;
      margin-top: 12px;
    }
    .btn{
      appearance:none;
      border:1px solid rgba(255,255,255,.14);
      background: rgba(255,255,255,.06);
      color: var(--text);
      padding: 12px 14px;
      border-radius: 14px;
      cursor:pointer;
      font-size: 13px;
      letter-spacing:.02em;
      display:inline-flex;
      align-items:center;
      justify-content:center;
      gap:8px;
      user-select:none;
    }
    .btn.primary{
      border-color: rgba(75,183,255,.35);
      background: rgba(75,183,255,.16);
    }
    .btn:active{ transform: translateY(1px); }
    .btn[disabled]{ opacity:.45; cursor:not-allowed; }

    /* EXCEPCIÓN: NO botones en portada */
    .noNav{ display:none; }

    /* Videos */
    .video{
      width: 100%;
      border-radius: 18px;
      border:1px solid rgba(255,255,255,.12);
      background:#000;
    }

    /* Anillo mágico botón */
    .ringBtn{
      appearance:none;
      border:0;
      background: transparent;
      cursor:pointer;
      padding: 10px 14px 8px;
      border-radius: 22px;
      display:inline-flex;
      flex-direction:column;
      align-items:center;
      gap: 8px;
      user-select:none;
      -webkit-tap-highlight-color: transparent;
    }
    .ringBtn:focus-visible{
      outline: 3px solid rgba(75,183,255,.55);
      outline-offset: 8px;
    }
    .ringSvg{
      width: 270px;
      height: 270px;
      filter: drop-shadow(0 18px 34px rgba(0,0,0,.45));
      transform: translateZ(0);
      transition: transform .25s ease, filter .25s ease;
    }
    .ringBtn:hover .ringSvg{
      transform: translateY(-2px) scale(1.02);
      filter: drop-shadow(0 24px 44px rgba(0,0,0,.60));
    }
    .ringBtn:active .ringSvg{ transform: translateY(1px) scale(.99); }

    /* Animaciones princesa */
    .sparkle { animation: twinkle 1.8s ease-in-out infinite; transform-origin: center; }
    .sparkle.s2{ animation-delay: .25s; opacity:.75; }
    .sparkle.s3{ animation-delay: .55s; opacity:.6; }
    @keyframes twinkle{
      0%,100%{ transform: scale(.92); opacity:.35; }
      50%{ transform: scale(1.06); opacity:1; }
    }
    .gemGlow { animation: gemPulse 2.2s ease-in-out infinite; }
    @keyframes gemPulse{
      0%,100%{ filter: drop-shadow(0 0 0 rgba(75,183,255,0)); }
      50%{ filter: drop-shadow(0 0 18px rgba(75,183,255,.55)); }
    }
    .shimmer { animation: shimmerSweep 1.9s ease-in-out infinite; }
    @keyframes shimmerSweep{
      0%{ opacity:0; transform: translateX(-34px) rotate(-12deg); }
      22%{ opacity:.9; }
      55%{ opacity:.15; }
      100%{ opacity:0; transform: translateX(74px) rotate(-12deg); }
    }
    .float { animation: floaty 3.4s ease-in-out infinite; }
    @keyframes floaty{
      0%,100%{ transform: translateY(0px); }
      50%{ transform: translateY(-6px); }
    }

    /* Hover sparkles sobre botones */
    .btn:hover{
      box-shadow: 0 0 0 1px rgba(255,255,255,.06), 0 0 18px rgba(231,168,184,.12);
    }

    @media (max-width: 520px){
      .yes{ font-size: 36px; }
      .ringSvg{ width: 240px; height: 240px; }
      .title{ font-size: 28px; }
    }

    @media (prefers-reduced-motion: reduce){
      .page{ transition:none; }
      .sparkle, .gemGlow, .shimmer, .float{ animation:none !important; }
      body{ cursor:auto; }
      .cursor{ display:none; }
      #fxCanvas{ display:none; }
    }
  </style>
</head>

<body>
  <!-- Fondo partículas (solo portada) -->
  <canvas id="bgParticles"></canvas>

  <!-- Destello -->
  <div id="flash" aria-hidden="true"></div>

  <!-- Canvas partículas del cursor (SIEMPRE) -->
  <canvas id="fxCanvas"></canvas>

  <!-- Cursor mágico -->
  <div class="cursor" id="cursor" aria-hidden="true">
    <div class="ring"></div>
    <div class="dot"></div>
  </div>

  <!-- Audios -->
  <audio id="marcha_nupcial" src="assets/marcha_nupcial.mp3" preload="auto"></audio>
  <audio id="unmundodiferente" src="assets/unmundodiferente.mp3" preload="auto"></audio>

  <div class="stage">
    <div class="bookWrap">
      <div class="book" id="book">

        <!-- =======================
             PÁGINA 1 (PORTADA)
             - Fondo rose gold
             - Anillo mágico botón
             - Autoplay intento marcha_nupcial
             - Abre solo con anillo
        ======================== -->
        <div class="page" data-index="0" style="z-index: 8;">
          <div class="face front coverFace">
            <h1 class="yes">¡Sí, acepto!</h1>
            <p class="instruction">da clic en el anillo para abrir</p>

            <div class="panel">
              <button id="btnRing" class="ringBtn" type="button" aria-label="Abrir invitación">
                <svg class="ringSvg float" viewBox="0 0 260 260" role="img" aria-hidden="true">
                  <defs>
                    <!-- Plata -->
                    <linearGradient id="silverGrad" x1="0" y1="0" x2="1" y2="1">
                      <stop offset="0%"  stop-color="var(--silver1)"/>
                      <stop offset="22%" stop-color="var(--silver2)"/>
                      <stop offset="48%" stop-color="var(--silver3)"/>
                      <stop offset="72%" stop-color="var(--silver4)"/>
                      <stop offset="100%" stop-color="var(--silver2)"/>
                    </linearGradient>

                    <!-- Brillo plata -->
                    <linearGradient id="silverSpec" x1="0" y1="0" x2="1" y2="0">
                      <stop offset="0%" stop-color="rgba(255,255,255,0)"/>
                      <stop offset="45%" stop-color="rgba(255,255,255,.95)"/>
                      <stop offset="55%" stop-color="rgba(255,255,255,.15)"/>
                      <stop offset="100%" stop-color="rgba(255,255,255,0)"/>
                    </linearGradient>

                    <!-- Gema azul -->
                    <radialGradient id="blueGem" cx="35%" cy="30%" r="75%">
                      <stop offset="0%"  stop-color="var(--blue1)"/>
                      <stop offset="35%" stop-color="var(--blue2)"/>
                      <stop offset="70%" stop-color="var(--blue3)"/>
                      <stop offset="100%" stop-color="var(--blue4)"/>
                    </radialGradient>

                    <filter id="softShadow" x="-40%" y="-40%" width="180%" height="180%">
                      <feDropShadow dx="0" dy="10" stdDeviation="10" flood-color="rgba(0,0,0,.45)"/>
                    </filter>

                    <filter id="sparkleGlow" x="-80%" y="-80%" width="260%" height="260%">
                      <feGaussianBlur stdDeviation="1.8" result="bb"/>
                      <feMerge>
                        <feMergeNode in="bb"/>
                        <feMergeNode in="SourceGraphic"/>
                      </feMerge>
                    </filter>
                  </defs>

                  <ellipse cx="130" cy="210" rx="74" ry="18" fill="rgba(0,0,0,.28)"/>

                  <g class="gemGlow" filter="url(#softShadow)">
                    <path d="M130 42 L168 78 L130 114 L92 78 Z" fill="rgba(245,250,255,.92)"/>
                    <path d="M130 50 L160 80 L130 110 L100 80 Z" fill="url(#blueGem)"/>
                    <path d="M130 50 L140 80 L130 110 L120 80 Z" fill="rgba(255,255,255,.22)"/>
                    <path d="M100 80 L120 80 L130 110 L112 110 Z" fill="rgba(255,255,255,.12)"/>
                    <path d="M160 80 L140 80 L130 110 L148 110 Z" fill="rgba(0,0,0,.10)"/>
                    <path d="M118 66 C124 60,132 60,138 66 C132 70,124 70,118 66 Z"
                          fill="rgba(255,255,255,.75)"/>
                  </g>

                  <g>
                    <path
                      d="M130 100
                         C 78 100, 52 140, 52 174
                         C 52 214, 85 236, 130 236
                         C 175 236, 208 214, 208 174
                         C 208 140, 182 100, 130 100
                         Z"
                      fill="none"
                      stroke="url(#silverGrad)"
                      stroke-width="24"
                      stroke-linecap="round"
                      stroke-linejoin="round"
                    />
                    <path
                      d="M130 112
                         C 90 112, 70 144, 70 172
                         C 70 206, 96 222, 130 222
                         C 164 222, 190 206, 190 172
                         C 190 144, 170 112, 130 112
                         Z"
                      fill="none"
                      stroke="rgba(0,0,0,.12)"
                      stroke-width="8"
                      stroke-linecap="round"
                    />
                    <g class="shimmer">
                      <path d="M80 168 C 80 138, 104 122, 126 118"
                            fill="none"
                            stroke="url(#silverSpec)"
                            stroke-width="12"
                            stroke-linecap="round"
                            opacity=".9"/>
                    </g>
                    <path d="M148 232 C 186 224, 199 200, 201 176"
                          fill="none"
                          stroke="rgba(255,255,255,.22)"
                          stroke-width="9"
                          stroke-linecap="round"/>
                  </g>

                  <g filter="url(#sparkleGlow)">
                    <g class="sparkle" transform="translate(32,54)">
                      <path d="M18 0 L22 14 L36 18 L22 22 L18 36 L14 22 L0 18 L14 14 Z"
                            fill="rgba(255,255,255,.92)"/>
                    </g>
                    <g class="sparkle s2" transform="translate(190,70) scale(.75)">
                      <path d="M18 0 L22 14 L36 18 L22 22 L18 36 L14 22 L0 18 L14 14 Z"
                            fill="rgba(200,240,255,.95)"/>
                    </g>
                    <g class="sparkle s3" transform="translate(182,150) scale(.62)">
                      <path d="M18 0 L22 14 L36 18 L22 22 L18 36 L14 22 L0 18 L14 14 Z"
                            fill="rgba(255,255,255,.85)"/>
                    </g>
                  </g>
                </svg>
              </button>
            </div>

            <p class="subtitle">✨ toca el anillo para comenzar ✨</p>
          </div>
          <div class="face back coverFace"></div>
        </div>

        <!-- =======================
             PÁGINA 2 - apertura.mp4
        ======================== -->
        <div class="page" data-index="1" style="z-index: 7;">
          <div class="face front">
            <h2 class="title">Apertura</h2>
            <p class="subtitle">Video</p>

            <div class="panel">
              <div class="contentCard">
                <video id="v_apertura" class="video" controls playsinline preload="metadata">
                  <source src="assets/apertura.mp4" type="video/mp4" />
                </video>
                <div class="nav">
                  <button class="btn" data-nav="prev">⟵ Anterior</button>
                  <button class="btn primary" data-nav="next">Siguiente ⟶</button>
                </div>
              </div>
            </div>
          </div>
          <div class="face back"></div>
        </div>

        <!-- =======================
             PÁGINA 3 - promesa.mp4 (autoplay al entrar)
        ======================== -->
        <div class="page" data-index="2" style="z-index: 6;">
          <div class="face front">
            <h2 class="title">Promesa</h2>
            <p class="subtitle">Video</p>

            <div class="panel">
              <div class="contentCard">
                <video id="v_promesa" class="video" controls playsinline preload="metadata">
                  <source src="assets/promesa.mp4" type="video/mp4" />
                </video>
                <div class="nav">
                  <button class="btn" data-nav="prev">⟵ Anterior</button>
                  <button class="btn primary" data-nav="next">Siguiente ⟶</button>
                </div>
              </div>
            </div>
          </div>
          <div class="face back"></div>
        </div>

        <!-- =======================
             PÁGINA 4 - estheryerick.mp4
        ======================== -->
        <div class="page" data-index="3" style="z-index: 5;">
          <div class="face front">
            <h2 class="title">Esther &amp; Erick</h2>
            <p class="subtitle">Video</p>

            <div class="panel">
              <div class="contentCard">
                <video id="v_estheryerick" class="video" controls playsinline preload="metadata">
                  <source src="assets/estheryerick.mp4" type="video/mp4" />
                </video>
                <div class="nav">
                  <button class="btn" data-nav="prev">⟵ Anterior</button>
                  <button class="btn primary" data-nav="next">Siguiente ⟶</button>
                </div>
              </div>
            </div>
          </div>
          <div class="face back"></div>
        </div>

        <!-- =======================
             PÁGINA 5 - esposa.mp4 + al entrar: unmundodiferente.mp3 y autoplay esposa.mp4
        ======================== -->
        <div class="page" data-index="4" style="z-index: 4;">
          <div class="face front">
            <h2 class="title">Esposa</h2>
            <p class="subtitle">Video + canción</p>

            <div class="panel">
              <div class="contentCard">
                <video id="v_esposa" class="video" controls playsinline preload="metadata">
                  <source src="assets/esposa.mp4" type="video/mp4" />
                </video>
                <div class="nav">
                  <button class="btn" data-nav="prev">⟵ Anterior</button>
                  <button class="btn primary" data-nav="next">Siguiente ⟶</button>
                </div>
              </div>
            </div>
          </div>
          <div class="face back"></div>
        </div>

        <!-- =======================
             PÁGINA 6 - padres.mp4
        ======================== -->
        <div class="page" data-index="5" style="z-index: 3;">
          <div class="face front">
            <h2 class="title">Padres</h2>
            <p class="subtitle">Video</p>

            <div class="panel">
              <div class="contentCard">
                <video id="v_padres" class="video" controls playsinline preload="metadata">
                  <source src="assets/padres.mp4" type="video/mp4" />
                </video>
                <div class="nav">
                  <button class="btn" data-nav="prev">⟵ Anterior</button>
                  <button class="btn primary" data-nav="next">Siguiente ⟶</button>
                </div>
              </div>
            </div>
          </div>
          <div class="face back"></div>
        </div>

        <!-- =======================
             PÁGINA 7 - boda.mp4
        ======================== -->
        <div class="page" data-index="6" style="z-index: 2;">
          <div class="face front">
            <h2 class="title">Boda</h2>
            <p class="subtitle">Video</p>

            <div class="panel">
              <div class="contentCard">
                <video id="v_boda" class="video" controls playsinline preload="metadata">
                  <source src="assets/boda.mp4" type="video/mp4" />
                </video>
                <div class="nav">
                  <button class="btn" data-nav="prev">⟵ Anterior</button>
                  <button class="btn primary" data-nav="next">Siguiente ⟶</button>
                </div>
              </div>
            </div>
          </div>
          <div class="face back"></div>
        </div>

        <!-- =======================
             PÁGINA 8 - ceremonia.mp4
        ======================== -->
        <div class="page" data-index="7" style="z-index: 1;">
          <div class="face front">
            <h2 class="title">Ceremonia</h2>
            <p class="subtitle">Video</p>

            <div class="panel">
              <div class="contentCard">
                <video id="v_ceremonia" class="video" controls playsinline preload="metadata">
                  <source src="assets/ceremonia.mp4" type="video/mp4" />
                </video>
                <div class="nav">
                  <button class="btn" data-nav="prev">⟵ Anterior</button>
                  <button class="btn primary" data-nav="next" disabled>Fin</button>
                </div>
              </div>
            </div>
          </div>
          <div class="face back"></div>
        </div>

      </div>
    </div>
  </div>

  <script>
    // ============================
    // Flipbook state
    // ============================
    const pages = Array.from(document.querySelectorAll(".page"));
    const maxIndex = pages.length - 1;
    let currentIndex = 0; // 0 = portada

    const flash = document.getElementById("flash");
    const bgParticlesCanvas = document.getElementById("bgParticles");

    function flashAt(x,y){
      flash.style.setProperty("--x", x + "px");
      flash.style.setProperty("--y", y + "px");
      flash.classList.add("on");
      setTimeout(() => flash.classList.remove("on"), 240);
    }

    function renderBook(){
      pages.forEach(p => {
        const idx = Number(p.dataset.index);
        if (idx < currentIndex) p.classList.add("flipped");
        else p.classList.remove("flipped");
      });

      // Partículas de fondo SOLO en portada
      bgParticlesCanvas.style.opacity = (currentIndex === 0) ? "1" : "0";
    }

    function goTo(idx, e){
      const next = Math.max(0, Math.min(maxIndex, idx));
      if (next === currentIndex) return;

      // destello
      if (e?.clientX != null) flashAt(e.clientX, e.clientY);
      else flashAt(window.innerWidth*0.5, window.innerHeight*0.35);

      // detener videos no visibles (para evitar audio múltiple)
      stopAllVideos();

      currentIndex = next;
      renderBook();

      // Disparadores por página (autoplay)
      onEnterPage(currentIndex);
    }

    // Navegación (excepto portada -> solo anillo para avanzar)
    document.addEventListener("click", (e) => {
      const navBtn = e.target.closest("[data-nav]");
      if (!navBtn) return;

      const dir = navBtn.getAttribute("data-nav");
      if (dir === "prev") goTo(currentIndex - 1, e);
      if (dir === "next") {
        // Bloquear avanzar desde portada por botón (no existe) pero por seguridad:
        if (currentIndex === 0) return;
        goTo(currentIndex + 1, e);
      }
    });

    // ============================
    // Audio / Video rules
    // ============================
    const aMarcha = document.getElementById("marcha_nupcial");
    const aMundo  = document.getElementById("unmundodiferente");

    const videos = {
      1: document.getElementById("v_apertura"),
      2: document.getElementById("v_promesa"),
      3: document.getElementById("v_estheryerick"),
      4: document.getElementById("v_esposa"),
      5: document.getElementById("v_padres"),
      6: document.getElementById("v_boda"),
      7: document.getElementById("v_ceremonia")
    };

    function stopAllVideos(){
      Object.values(videos).forEach(v => {
        if (!v) return;
        try{
          v.pause();
          v.currentTime = 0;
        }catch(_){}
      });
    }

    function stopAllAudio(){
      [aMarcha, aMundo].forEach(a => {
        try{
          a.pause();
          a.currentTime = 0;
        }catch(_){}
      });
    }

    async function tryPlay(media){
      if (!media) return;
      try{ await media.play(); }catch(err){ /* autoplay puede bloquearse */ }
    }

    function onEnterPage(pageIndex){
      // pageIndex: 0..7  (0 portada, 1..7 páginas de video)
      // Entrar a página 3 (index 2) => autoplay promesa.mp4
      if (pageIndex === 2){
        tryPlay(videos[2]); // promesa
      }

      // Entrar a página 5 (index 4) => sonar unmundodiferente + autoplay esposa.mp4
      if (pageIndex === 4){
        // Cambia canción
        try{
          aMarcha.pause();
        }catch(_){}
        // reproducir unmundodiferente
        tryPlay(aMundo);
        // reproducir esposa.mp4
        tryPlay(videos[4]);
      }
    }

    // Portada: intento de autoplay marcha_nupcial (puede bloquearse)
    async function tryAutoplayMarcha(){
      // En móviles iOS/Chrome suele bloquear hasta interacción.
      // Aun así lo intentamos: si falla, se disparará cuando toquen el anillo.
      try{
        await aMarcha.play();
      }catch(_){}
    }

    // Click en anillo: iniciar/reanudar marcha + abrir página 2 (index 1)
    document.getElementById("btnRing").addEventListener("click", async (e) => {
      // Asegura que unmundodiferente no esté sonando al inicio
      try{ aMundo.pause(); aMundo.currentTime = 0; }catch(_){}

      // Inicia marcha nupcial por gesto del usuario (esto sí debe funcionar)
      await tryPlay(aMarcha);

      // Abrir página 2 (apertura)
      goTo(1, e);
    });

    // Primera carga
    renderBook();
    tryAutoplayMarcha();

    // ============================
    // Fondo partículas portada (bgParticles)
    // ============================
    const bg = bgParticlesCanvas.getContext("2d");
    function resizeBg(){
      bgParticlesCanvas.width = Math.floor(innerWidth * devicePixelRatio);
      bgParticlesCanvas.height = Math.floor(innerHeight * devicePixelRatio);
      bgParticlesCanvas.style.width = innerWidth + "px";
      bgParticlesCanvas.style.height = innerHeight + "px";
      bg.setTransform(devicePixelRatio,0,0,devicePixelRatio,0,0);
    }
    addEventListener("resize", resizeBg);
    resizeBg();

    const bgColors = [
      getComputedStyle(document.documentElement).getPropertyValue("--pBlue").trim(),
      getComputedStyle(document.documentElement).getPropertyValue("--pRose").trim(),
      getComputedStyle(document.documentElement).getPropertyValue("--pWhite").trim(),
      getComputedStyle(document.documentElement).getPropertyValue("--pSilver").trim()
    ];
    const rand = (a,b)=>Math.random()*(b-a)+a;

    const bgParticles = Array.from({length: 85}, () => ({
      x: rand(0, innerWidth),
      y: rand(0, innerHeight),
      r: rand(1.2, 3.8),
      vx: rand(-0.18, 0.18),
      vy: rand(-0.38, -0.08),
      a: rand(0.18, 0.8),
      c: bgColors[(Math.random()*bgColors.length)|0],
      t: rand(0, Math.PI*2),
      sp: rand(0.006, 0.02)
    }));

    function drawBgParticles(){
      // Solo dibujar si estamos en portada
      if (currentIndex !== 0){
        requestAnimationFrame(drawBgParticles);
        return;
      }

      bg.clearRect(0,0,innerWidth,innerHeight);

      for (const p of bgParticles){
        p.t += p.sp;
        const glow = (Math.sin(p.t)+1)*0.5;
        const alpha = Math.max(0.05, Math.min(0.95, p.a * (0.55 + glow*0.75)));

        bg.beginPath();
        bg.arc(p.x, p.y, p.r*(0.8 + glow*0.45), 0, Math.PI*2);
        bg.fillStyle = p.c.replace(/[\d.]+\)$/g, alpha + ")");
        bg.fill();

        if (glow > 0.84){
          bg.strokeStyle = "rgba(255,255,255," + (alpha*0.55) + ")";
          bg.lineWidth = 1;
          bg.beginPath();
          bg.moveTo(p.x - 6, p.y);
          bg.lineTo(p.x + 6, p.y);
          bg.moveTo(p.x, p.y - 6);
          bg.lineTo(p.x, p.y + 6);
          bg.stroke();
        }

        p.x += p.vx;
        p.y += p.vy;

        if (p.y < -12) { p.y = innerHeight + 12; p.x = rand(0, innerWidth); }
        if (p.x < -12) p.x = innerWidth + 12;
        if (p.x > innerWidth + 12) p.x = -12;
      }
      requestAnimationFrame(drawBgParticles);
    }
    drawBgParticles();

    // ============================
    // Cursor mágico + partículas que siguen el cursor (fxCanvas)
    // ============================
    const fxCanvas = document.getElementById("fxCanvas");
    const fx = fxCanvas.getContext("2d");

    function resizeFx(){
      fxCanvas.width = Math.floor(innerWidth * devicePixelRatio);
      fxCanvas.height = Math.floor(innerHeight * devicePixelRatio);
      fxCanvas.style.width = innerWidth + "px";
      fxCanvas.style.height = innerHeight + "px";
      fx.setTransform(devicePixelRatio,0,0,devicePixelRatio,0,0);
    }
    addEventListener("resize", resizeFx);
    resizeFx();

    const cursorEl = document.getElementById("cursor");
    let mx = innerWidth/2, my = innerHeight/2;
    let lastSpawn = 0;

    const fxPalette = [
      "rgba(255,255,255,.85)",
      "rgba(75,183,255,.85)",
      "rgba(231,168,184,.80)",
      "rgba(215,219,229,.85)"
    ];

    const fxParticles = [];
    function spawnCursorParticles(x,y,boost=false){
      const now = performance.now();
      if (!boost && now - lastSpawn < 18) return;
      lastSpawn = now;

      const count = boost ? 12 : 5;
      for (let i=0; i<count; i++){
        fxParticles.push({
          x, y,
          vx: rand(-0.8, 0.8),
          vy: rand(-1.4, -0.2),
          life: rand(28, 48),
          size: rand(1.2, 2.8),
          c: fxPalette[(Math.random()*fxPalette.length)|0],
          type: Math.random() < 0.28 ? "heart" : "spark"
        });
      }
    }

    function drawHeart(x,y,s,color){
      fx.save();
      fx.translate(x,y);
      fx.scale(s, s);
      fx.globalAlpha = 1;
      fx.fillStyle = color;
      fx.beginPath();
      // corazón simple con curvas
      fx.moveTo(0, 2);
      fx.bezierCurveTo(-3, -2, -8, -1, -8, 4);
      fx.bezierCurveTo(-8, 9, -2, 12, 0, 15);
      fx.bezierCurveTo(2, 12, 8, 9, 8, 4);
      fx.bezierCurveTo(8, -1, 3, -2, 0, 2);
      fx.fill();
      fx.restore();
    }

    function tickFx(){
      fx.clearRect(0,0,innerWidth,innerHeight);

      for (let i=fxParticles.length-1; i>=0; i--){
        const p = fxParticles[i];
        p.x += p.vx;
        p.y += p.vy;
        p.vy += 0.03;
        p.life -= 1;

        const alpha = Math.max(0, Math.min(1, p.life / 40));
        fx.globalAlpha = alpha;

        if (p.type === "heart"){
          drawHeart(p.x, p.y, p.size*0.35, p.c);
        }else{
          fx.fillStyle = p.c;
          fx.beginPath();
          fx.arc(p.x, p.y, p.size, 0, Math.PI*2);
          fx.fill();

          // mini destello cruz
          if (alpha > 0.6 && Math.random() < 0.06){
            fx.strokeStyle = "rgba(255,255,255," + (alpha*0.65) + ")";
            fx.lineWidth = 1;
            fx.beginPath();
            fx.moveTo(p.x - 4, p.y);
            fx.lineTo(p.x + 4, p.y);
            fx.moveTo(p.x, p.y - 4);
            fx.lineTo(p.x, p.y + 4);
            fx.stroke();
          }
        }

        if (p.life <= 0) fxParticles.splice(i,1);
      }

      requestAnimationFrame(tickFx);
    }
    tickFx();

    // Cursor move
    addEventListener("pointermove", (e) => {
      mx = e.clientX;
      my = e.clientY;
      cursorEl.style.left = mx + "px";
      cursorEl.style.top = my + "px";

      spawnCursorParticles(mx, my, false);
    }, {passive:true});

    // Interacciones: destellos/partículas extra al click y hover
    addEventListener("pointerdown", (e) => {
      flashAt(e.clientX, e.clientY);
      spawnCursorParticles(e.clientX, e.clientY, true);
    });

    // Hover extra sobre elementos interactivos
    document.addEventListener("pointerover", (e) => {
      const el = e.target.closest("button, a, video");
      if (!el) return;
      const r = el.getBoundingClientRect();
      flashAt(r.left + r.width*0.5, r.top + r.height*0.35);
      spawnCursorParticles(r.left + r.width*0.5, r.top + r.height*0.5, true);
    });

    // Primer gesto del usuario: si marcha no logró autoplay, aquí se habilita
    document.addEventListener("pointerdown", async () => {
      if (currentIndex === 0 && aMarcha.paused){
        try{ await aMarcha.play(); }catch(_){}
      }
    }, {once:true});
  </script>
</body>
</html> 

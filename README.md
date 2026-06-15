# Gebu_Nadine
Spiel für Nadines Geburtstag
<!DOCTYPE html>
<html lang="de">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0">
<title>Brauhaus Tour</title>
<script src="https://cdn.tailwindcss.com"></script>
<style>
  @font-face {
    font-family: 'Press Start 2P';
    src: url('https://fonts.gstatic.com/s/pressstart2p/v15/e3t4euO8T-267oIAQAu6jDQyK3nVivM.woff2') format('woff2');
  }

  body {
    margin: 0;
    overflow-x: hidden;
  }

  .pixel-font {
    font-family: 'Press Start 2P', monospace;
  }

  /* --- Scene 1: Welcome --- */
  #scene-welcome {
    background: #000;
    height: 100vh;
  }

  .blink-text {
    animation: blink 1.1s steps(1) infinite;
  }

  @keyframes blink {
    0%, 50% { opacity: 1; }
    50.01%, 100% { opacity: 0; }
  }

  .arcade-input {
    background: transparent;
    border: 2px solid #fff;
    color: #fff;
    text-align: center;
    caret-color: #fff;
  }

  .arcade-input:focus {
    outline: none;
    box-shadow: 0 0 0 3px rgba(255,255,255,0.25);
  }

  .arcade-btn {
    background: #fff;
    color: #000;
    transition: transform 0.08s ease, box-shadow 0.08s ease;
    box-shadow: 4px 4px 0px #555;
  }

  .arcade-btn:active {
    transform: translate(4px, 4px);
    box-shadow: 0px 0px 0px #555;
  }

  /* --- Scene transitions --- */
  .scene {
    position: fixed;
    inset: 0;
    transition: opacity 0.6s ease;
  }

  .scene-hidden {
    opacity: 0;
    pointer-events: none;
  }

  /* --- Scene 2: Map --- */
  #scene-map {
    background: linear-gradient(180deg, #FFF4D6 0%, #F7E2B8 100%);
    overflow-y: auto;
  }

  .station-dot {
    transition: transform 0.2s ease;
  }

  .station-dot:active {
    transform: scale(0.92);
  }

  .path-line {
    stroke-dasharray: 10 10;
    stroke: #B45309;
    stroke-width: 4;
    fill: none;
  }

  .station-label {
    font-family: 'Press Start 2P', monospace;
  }
</style>
</head>
<body class="pixel-font">

  <!-- SCENE 1: WELCOME -->
  <section id="scene-welcome" class="scene flex items-center justify-center px-6">
    <div class="flex flex-col items-center gap-8 text-center">
      <p class="blink-text text-white text-base sm:text-2xl tracking-widest">
        Bitte Namen eingeben
      </p>
      <div class="flex flex-col items-center gap-4 w-full max-w-xs">
        <input
          id="name-input"
          type="text"
          maxlength="20"
          class="arcade-input pixel-font w-full px-4 py-3 text-sm sm:text-base"
          placeholder="..."
        >
        <button
          id="confirm-btn"
          class="arcade-btn pixel-font px-6 py-3 text-xs sm:text-sm w-full"
        >
          Bestätigen
        </button>
      </div>
    </div>
  </section>

  <!-- SCENE 2: MAP -->
  <section id="scene-map" class="scene scene-hidden flex flex-col items-center px-4 py-8 sm:py-12">

    <div class="text-center mb-8 max-w-md">
      <h1 class="text-amber-900 text-lg sm:text-2xl mb-3 leading-relaxed">
        Hallo <span id="player-name" class="text-amber-700">Spieler</span>!
      </h1>
      <p class="font-sans text-amber-800 text-sm sm:text-base">
        Bereit für deine Brauhaus Tour durch Köln?
      </p>
    </div>

    <!-- Map container -->
    <div class="relative w-full max-w-sm">
      <svg viewBox="0 0 300 600" class="w-full h-auto" preserveAspectRatio="xMidYMid meet">
        <!-- Dotted path connecting stations -->
        <path class="path-line" d="M 150 60 L 220 180 L 90 300 L 220 420 L 150 540"></path>

        <!-- Station 1 -->
        <g class="station-dot cursor-pointer" data-station="1">
          <circle cx="150" cy="60" r="28" fill="#D97706" stroke="#7C2D12" stroke-width="3"></circle>
          <text x="150" y="66" text-anchor="middle" font-size="16" fill="#fff" font-weight="bold" font-family="sans-serif">1</text>
        </g>

        <!-- Station 2 -->
        <g class="station-dot cursor-pointer" data-station="2">
          <circle cx="220" cy="180" r="28" fill="#D97706" stroke="#7C2D12" stroke-width="3"></circle>
          <text x="220" y="186" text-anchor="middle" font-size="16" fill="#fff" font-weight="bold" font-family="sans-serif">2</text>
        </g>

        <!-- Station 3 -->
        <g class="station-dot cursor-pointer" data-station="3">
          <circle cx="90" cy="300" r="28" fill="#D97706" stroke="#7C2D12" stroke-width="3"></circle>
          <text x="90" y="306" text-anchor="middle" font-size="16" fill="#fff" font-weight="bold" font-family="sans-serif">3</text>
        </g>

        <!-- Station 4 -->
        <g class="station-dot cursor-pointer" data-station="4">
          <circle cx="220" cy="420" r="28" fill="#D97706" stroke="#7C2D12" stroke-width="3"></circle>
          <text x="220" y="426" text-anchor="middle" font-size="16" fill="#fff" font-weight="bold" font-family="sans-serif">4</text>
        </g>

        <!-- Station 5 (Finish) -->
        <g class="station-dot cursor-pointer" data-station="5">
          <circle cx="150" cy="540" r="32" fill="#92400E" stroke="#451A03" stroke-width="3"></circle>
          <text x="150" y="546" text-anchor="middle" font-size="14" fill="#fff" font-weight="bold" font-family="sans-serif">Ziel</text>
        </g>
      </svg>

      <!-- Station labels -->
      <div class="absolute inset-0 pointer-events-none">
        <p class="station-label absolute text-[8px] sm:text-[10px] text-amber-900" style="top: 4%; left: 60%;">Station 1</p>
        <p class="station-label absolute text-[8px] sm:text-[10px] text-amber-900" style="top: 27%; left: 78%;">Station 2</p>
        <p class="station-label absolute text-[8px] sm:text-[10px] text-amber-900" style="top: 47%; left: 4%;">Station 3</p>
        <p class="station-label absolute text-[8px] sm:text-[10px] text-amber-900" style="top: 67%; left: 78%;">Station 4</p>
      </div>
    </div>

    <p class="font-sans text-amber-700 text-xs sm:text-sm mt-6 text-center max-w-xs">
      Tippe auf Station 1, um deine Tour zu starten.
    </p>

  </section>

  <script>
    const sceneWelcome = document.getElementById('scene-welcome');
    const sceneMap = document.getElementById('scene-map');
    const nameInput = document.getElementById('name-input');
    const confirmBtn = document.getElementById('confirm-btn');
    const playerNameLabel = document.getElementById('player-name');

    function startTour() {
      const name = nameInput.value.trim();
      playerNameLabel.textContent = name || 'Spieler';

      sceneWelcome.classList.add('scene-hidden');
      sceneMap.classList.remove('scene-hidden');
    }

    confirmBtn.addEventListener('click', startTour);

    nameInput.addEventListener('keydown', (e) => {
      if (e.key === 'Enter') startTour();
    });

    // Placeholder: station click handlers (logic added later)
    document.querySelectorAll('.station-dot').forEach((station) => {
      station.addEventListener('click', () => {
        const stationNumber = station.getAttribute('data-station');
        console.log(`Station ${stationNumber} clicked`);
        // Mini-game logic for each station will be added here
      });
    });
  </script>

</body>
</html>

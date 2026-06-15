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

  .error-shake {
    animation: shake 0.4s ease;
  }

  @keyframes shake {
    0%, 100% { transform: translateX(0); }
    20%, 60% { transform: translateX(-8px); }
    40%, 80% { transform: translateX(8px); }
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

  /* --- Scene 2: Intro / Living Room --- */
  #scene-intro {
    background: linear-gradient(180deg, #F2D6A2 0%, #D9A86C 60%, #B9824F 100%);
    overflow: hidden;
  }

  .room-deco {
    opacity: 0.35;
  }

  .character-sprite {
    width: 26vw;
    max-width: 130px;
    transition: transform 0.3s ease;
  }

  .character-card {
    background: #fff8ec;
    border: 3px solid #7C2D12;
    border-radius: 16px;
  }

  .character-img-box {
    height: 38vh;
    max-height: 220px;
  }

  .nadine-enter {
    animation: slideIn 0.7s ease-out forwards;
  }

  @keyframes slideIn {
    from { transform: translateX(120%) scale(0.8); opacity: 0; }
    to { transform: translateX(0) scale(1); opacity: 1; }
  }

  .dialogue-box {
    background: #fffaf0;
    border: 4px solid #7C2D12;
    border-radius: 18px;
    box-shadow: 0 6px 0 rgba(0,0,0,0.15);
  }

  .dialogue-text {
    font-family: 'Press Start 2P', monospace;
    line-height: 1.8;
    min-height: 3.6em;
  }

  .continue-prompt {
    animation: bob 1s ease-in-out infinite;
  }

  @keyframes bob {
    0%, 100% { transform: translateY(0); }
    50% { transform: translateY(-4px); }
  }

  /* --- Scene 3: Map --- */
  #scene-map {
    background: linear-gradient(180deg, #FFF4D6 0%, #F7E2B8 100%);
    overflow-y: auto;
  }

  .station-dot {
    transition: transform 0.15s ease, filter 0.15s ease;
    cursor: pointer;
  }

  .station-dot:active {
    transform: scale(0.92);
  }

  .station-dot.selected circle {
    filter: drop-shadow(0 0 6px #FBBF24);
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

  .info-panel {
    background: #fffaf0;
    border: 3px solid #7C2D12;
    border-radius: 16px;
  }
</style>
</head>
<body class="pixel-font">

  <!-- SCENE 1: WELCOME -->
  <section id="scene-welcome" class="scene flex items-center justify-center px-6">
    <div class="flex flex-col items-center gap-8 text-center">
      <p id="welcome-text" class="blink-text text-white text-base sm:text-2xl tracking-widest">
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
        <p id="error-msg" class="text-red-500 text-[10px] sm:text-xs hidden">
          Falscher Name! Versuch's nochmal!
        </p>
      </div>
    </div>
  </section>

  <!-- SCENE 2: INTRO DIALOGUE -->
  <section id="scene-intro" class="scene scene-hidden flex flex-col items-center justify-end relative px-4 pb-6">

    <!-- Decorative room elements (simple shapes for "vintage living room" feel) -->
    <div class="room-deco absolute top-6 left-6 w-16 h-20 bg-amber-900 rounded-t-full"></div>
    <div class="room-deco absolute top-10 right-8 w-24 h-16 bg-amber-800 rounded-lg"></div>
    <div class="room-deco absolute top-4 left-1/2 -translate-x-1/2 w-32 h-10 bg-amber-700 rounded-full"></div>

    <!-- Characters -->
    <div class="flex-1 w-full flex items-end justify-center gap-4 sm:gap-8 pb-4 relative">

      <!-- Bennet -->
      <div class="character-card flex flex-col items-center p-2 character-sprite">
        <div class="character-img-box w-full flex items-end justify-center overflow-hidden">
          <img src="assets/bennet.png" alt="Bennet" class="w-full h-full object-contain object-bottom" onerror="this.style.display='none'; this.nextElementSibling.style.display='flex';">
          <span style="display:none;" class="text-4xl">🐯</span>
        </div>
        <p class="font-sans text-[10px] sm:text-xs mt-1 text-amber-900">Bennet</p>
      </div>

      <!-- Maria -->
      <div class="character-card flex flex-col items-center p-2 character-sprite">
        <div class="character-img-box w-full flex items-end justify-center overflow-hidden">
          <img src="assets/maria.png" alt="Maria" class="w-full h-full object-contain object-bottom" onerror="this.style.display='none'; this.nextElementSibling.style.display='flex';">
          <span style="display:none;" class="text-4xl">🧥</span>
        </div>
        <p class="font-sans text-[10px] sm:text-xs mt-1 text-amber-900">Maria</p>
      </div>

      <!-- Nadine (slides in) -->
      <div id="nadine-card" class="character-card flex flex-col items-center p-2 character-sprite opacity-0">
        <div class="character-img-box w-full flex items-end justify-center overflow-hidden">
          <img src="assets/nadine.png" alt="Nadine" class="w-full h-full object-contain object-bottom" onerror="this.style.display='none'; this.nextElementSibling.style.display='flex';">
          <span style="display:none;" class="text-4xl">🎉</span>
        </div>
        <p class="font-sans text-[10px] sm:text-xs mt-1 text-amber-900">Nadine</p>
      </div>
    </div>

    <!-- Dialogue box -->
    <div class="dialogue-box w-full max-w-md p-4 sm:p-5 relative z-10">
      <p id="dialogue-text" class="dialogue-text text-amber-900 text-xs sm:text-sm"></p>
      <div class="flex justify-end mt-2">
        <p id="continue-prompt" class="continue-prompt font-sans text-amber-700 text-xs">▼ klicken</p>
        <button id="to-map-btn" class="arcade-btn px-4 py-2 text-[10px] sm:text-xs hidden">
          Zur Karte
        </button>
      </div>
    </div>

  </section>

  <!-- SCENE 3: MAP -->
  <section id="scene-map" class="scene scene-hidden flex flex-col items-center px-4 py-8 sm:py-12">

    <div class="text-center mb-6 max-w-md">
      <h1 class="text-amber-900 text-base sm:text-xl mb-2 leading-relaxed">
        Deine Brauhaus Tour
      </h1>
      <p class="font-sans text-amber-800 text-xs sm:text-sm">
        Köln-Ehrenfeld &amp; Umgebung
      </p>
    </div>

    <div class="w-full max-w-4xl flex flex-col lg:flex-row gap-6 items-center lg:items-start justify-center">

      <!-- Map -->
      <div class="relative w-full max-w-sm">
        <svg viewBox="0 0 300 600" class="w-full h-auto" preserveAspectRatio="xMidYMid meet">
          <!-- Dotted path connecting stations -->
          <path class="path-line" d="M 150 60 L 220 200 L 90 340 L 200 500"></path>

          <!-- Station 1: Haus Scholzen -->
          <g class="station-dot selected" data-station="1">
            <circle cx="150" cy="60" r="30" fill="#D97706" stroke="#7C2D12" stroke-width="3"></circle>
            <text x="150" y="67" text-anchor="middle" font-size="22" font-family="sans-serif">🏠</text>
          </g>

          <!-- Station 2: Braustelle -->
          <g class="station-dot" data-station="2">
            <circle cx="220" cy="200" r="30" fill="#D97706" stroke="#7C2D12" stroke-width="3"></circle>
            <text x="220" y="207" text-anchor="middle" font-size="22" font-family="sans-serif">🍺</text>
          </g>

          <!-- Station 3: Päffgen -->
          <g class="station-dot" data-station="3">
            <circle cx="90" cy="340" r="30" fill="#D97706" stroke="#7C2D12" stroke-width="3"></circle>
            <text x="90" y="347" text-anchor="middle" font-size="22" font-family="sans-serif">🍻</text>
          </g>

          <!-- Station 4: Lommerzheim -->
          <g class="station-dot" data-station="4">
            <circle cx="200" cy="500" r="32" fill="#92400E" stroke="#451A03" stroke-width="3"></circle>
            <text x="200" y="508" text-anchor="middle" font-size="22" font-family="sans-serif">🏛️</text>
          </g>
        </svg>

        <!-- Station name labels -->
        <div class="absolute inset-0 pointer-events-none">
          <p class="station-label absolute text-[7px] sm:text-[9px] text-amber-900 text-center w-24" style="top: 1%; left: 55%;">Haus Scholzen</p>
          <p class="station-label absolute text-[7px] sm:text-[9px] text-amber-900 text-center w-24" style="top: 30%; left: 73%;">Braustelle</p>
          <p class="station-label absolute text-[7px] sm:text-[9px] text-amber-900 text-center w-24" style="top: 53%; left: 0%;">Päffgen</p>
          <p class="station-label absolute text-[7px] sm:text-[9px] text-amber-900 text-center w-24" style="top: 80%; left: 58%;">Lommerzheim</p>
        </div>
      </div>

      <!-- Info panel -->
      <div class="info-panel w-full max-w-sm p-4 sm:p-5">
        <p class="font-sans text-amber-700 text-[10px] sm:text-xs mb-2" id="panel-eyebrow">
          Deine nächste Station:
        </p>
        <h2 id="panel-title" class="text-amber-900 text-sm sm:text-base mb-2 leading-relaxed">
          Haus Scholzen
        </h2>
        <p id="panel-subtitle" class="font-sans text-amber-700 text-xs sm:text-sm mb-3 italic">
          Ehrenfeld
        </p>
        <p id="panel-desc" class="font-sans text-amber-800 text-xs sm:text-sm leading-relaxed">
          Ein urig-gemütliches Brauhaus mit langer Geschichte. Hier startet eure Tour. (Mini-Spiel folgt bald!)
        </p>
      </div>

    </div>

  </section>

  <script>
    // ============ SCENE 1: WELCOME ============
    const sceneWelcome = document.getElementById('scene-welcome');
    const sceneIntro = document.getElementById('scene-intro');
    const sceneMap = document.getElementById('scene-map');
    const nameInput = document.getElementById('name-input');
    const confirmBtn = document.getElementById('confirm-btn');
    const errorMsg = document.getElementById('error-msg');

    function checkName() {
      const name = nameInput.value.trim().toLowerCase();
      if (name === 'nadine') {
        errorMsg.classList.add('hidden');
        sceneWelcome.classList.add('scene-hidden');
        sceneIntro.classList.remove('scene-hidden');
        startDialogue();
      } else {
        errorMsg.classList.remove('hidden');
        nameInput.classList.add('error-shake');
        setTimeout(() => nameInput.classList.remove('error-shake'), 400);
      }
    }

    confirmBtn.addEventListener('click', checkName);
    nameInput.addEventListener('keydown', (e) => {
      if (e.key === 'Enter') checkName();
    });

    // ============ SCENE 2: INTRO DIALOGUE ============
    const dialogueLines = [
      "Hallo Nadine! Bereit für deinen Geburtstag?",
      "Wir haben uns etwas ganz Besonderes für dich überlegt!",
      "Wir nehmen dich mit auf eine Brauhaus-Tour durch Köln!",
      "Und damit du dich nicht verläufst, haben wir eine Karte für dich.",
      "Klick auf 'Weiter', um die Karte zu sehen!"
    ];

    const dialogueTextEl = document.getElementById('dialogue-text');
    const continuePrompt = document.getElementById('continue-prompt');
    const toMapBtn = document.getElementById('to-map-btn');
    const nadineCard = document.getElementById('nadine-card');

    let currentLine = 0;
    let isTyping = false;
    let typeInterval = null;

    // Simple chirpy "Animal Crossing" style beep using Web Audio API
    let audioCtx = null;
    function playBeep() {
      try {
        if (!audioCtx) {
          audioCtx = new (window.AudioContext || window.webkitAudioContext)();
        }
        const osc = audioCtx.createOscillator();
        const gain = audioCtx.createGain();
        osc.connect(gain);
        gain.connect(audioCtx.destination);

        osc.type = 'square';
        const basePitch = 700;
        osc.frequency.value = basePitch + Math.random() * 250;

        gain.gain.setValueAtTime(0.06, audioCtx.currentTime);
        gain.gain.exponentialRampToValueAtTime(0.001, audioCtx.currentTime + 0.06);

        osc.start();
        osc.stop(audioCtx.currentTime + 0.06);
      } catch (e) {
        // Audio not available; fail silently
      }
    }

    function typeLine(text, onComplete) {
      isTyping = true;
      dialogueTextEl.textContent = '';
      continuePrompt.classList.add('hidden');
      let i = 0;

      typeInterval = setInterval(() => {
        const char = text[i];
        dialogueTextEl.textContent += char;

        if (char && char.trim() !== '') {
          playBeep();
        }

        i++;
        if (i >= text.length) {
          clearInterval(typeInterval);
          isTyping = false;
          continuePrompt.classList.remove('hidden');
          if (onComplete) onComplete();
        }
      }, 45);
    }

    function showNextLine() {
      if (isTyping) {
        clearInterval(typeInterval);
        dialogueTextEl.textContent = dialogueLines[currentLine];
        isTyping = false;
        continuePrompt.classList.remove('hidden');
        return;
      }

      currentLine++;

      if (currentLine >= dialogueLines.length) {
        continuePrompt.classList.add('hidden');
        toMapBtn.classList.remove('hidden');
        return;
      }

      typeLine(dialogueLines[currentLine]);
    }

    function startDialogue() {
      setTimeout(() => {
        nadineCard.classList.remove('opacity-0');
        nadineCard.classList.add('nadine-enter');
      }, 300);

      currentLine = 0;
      typeLine(dialogueLines[0]);
    }

    document.querySelector('.dialogue-box').addEventListener('click', (e) => {
      if (e.target === toMapBtn) return;
      if (!toMapBtn.classList.contains('hidden')) return;
      showNextLine();
    });

    toMapBtn.addEventListener('click', () => {
      sceneIntro.classList.add('scene-hidden');
      sceneMap.classList.remove('scene-hidden');
    });

    // ============ SCENE 3: MAP ============
    const breweryData = {
      "1": {
        eyebrow: "Deine nächste Station:",
        title: "Haus Scholzen",
        subtitle: "Ehrenfeld",
        desc: "Ein urig-gemütliches Brauhaus mit langer Geschichte. Hier startet eure Tour. (Mini-Spiel folgt bald!)"
      },
      "2": {
        eyebrow: "Station 2:",
        title: "Braustelle",
        subtitle: "Kölns kleinste Brauerei",
        desc: "Winzig, aber legendär – hier wird das Bier direkt vor Ort gebraut. (Mini-Spiel folgt bald!)"
      },
      "3": {
        eyebrow: "Station 3:",
        title: "Päffgen Brauhaus",
        subtitle: "Traditionell und urig",
        desc: "Klassisches Kölsch in historischem Ambiente, Friesenstraße. (Mini-Spiel folgt bald!)"
      },
      "4": {
        eyebrow: "Station 4:",
        title: "Lommerzheim",
        subtitle: "Deutz",
        desc: "Ikonische Fassade, kölsche Institution – euer großes Finale! (Mini-Spiel folgt bald!)"
      }
    };

    const panelEyebrow = document.getElementById('panel-eyebrow');
    const panelTitle = document.getElementById('panel-title');
    const panelSubtitle = document.getElementById('panel-subtitle');
    const panelDesc = document.getElementById('panel-desc');

    document.querySelectorAll('.station-dot').forEach((station) => {
      station.addEventListener('click', () => {
        const stationNumber = station.getAttribute('data-station');
        const data = breweryData[stationNumber];

        document.querySelectorAll('.station-dot').forEach(s => s.classList.remove('selected'));
        station.classList.add('selected');

        panelEyebrow.textContent = data.eyebrow;
        panelTitle.textContent = data.title;
        panelSubtitle.textContent = data.subtitle;
        panelDesc.textContent = data.desc;

        // Placeholder: mini-game logic for each station will be added here
        console.log(`Station ${stationNumber} clicked: ${data.title}`);
      });
    });
  </script>

</body>
</html>

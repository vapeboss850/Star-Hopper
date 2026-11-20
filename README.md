[starhopper_orbital_arcade.html](https://github.com/user-attachments/files/35178655/starhopper_orbital_arcade.html)
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no" />
  <title>STARHOPPER — Orbital Arcade</title>
  <script src="https://cdn.tailwindcss.com/3.4.17"></script>
  <style>
    @import url('https://fonts.googleapis.com/css2?family=Inter:wght@500;600;700;800&family=Orbitron:wght@700;800;900&display=swap');

    * {
      box-sizing: border-box;
      -webkit-tap-highlight-color: transparent;
      user-select: none;
    }

    html, body {
      margin: 0;
      padding: 0;
      width: 100%;
      height: 100%;
      overflow: hidden;
      font-family: 'Inter', system-ui, -apple-system, sans-serif;
      background: #030712;
      touch-action: manipulation;
    }

    .font-display {
      font-family: 'Orbitron', ui-monospace, SFMono-Regular, Menlo, monospace;
    }

    #game-canvas {
      display: block;
      width: 100%;
      height: 100%;
      cursor: pointer;
    }
  </style>
</head>
<body class="flex items-center justify-center bg-gray-950 text-white">
  <div id="app-shell" class="relative w-full h-full max-w-[480px] max-h-[860px] sm:h-[92vh] sm:rounded-3xl overflow-hidden shadow-2xl border border-indigo-500/30 bg-slate-950">
    <!-- Canvas -->
    <canvas id="game-canvas" aria-label="Starhopper arcade game canvas"></canvas>

    <!-- Top HUD Controls -->
    <div class="pointer-events-none absolute top-0 left-0 right-0 z-20 flex items-center justify-between p-3 sm:p-4">
      <div class="pointer-events-auto flex items-center gap-1.5">
        <button id="btn-theme" type="button" title="Toggle Space Palette" aria-label="Toggle space color theme"
          class="flex h-10 w-10 items-center justify-center rounded-xl bg-slate-900/65 text-white backdrop-blur-md border border-white/15 hover:bg-slate-800/80 active:scale-95 transition">
          <span id="icon-theme" class="text-base leading-none">🌌</span>
        </button>
        <button id="btn-mute" type="button" title="Mute / Unmute (M)" aria-label="Toggle sound"
          class="flex h-10 w-10 items-center justify-center rounded-xl bg-slate-900/65 text-white backdrop-blur-md border border-white/15 hover:bg-slate-800/80 active:scale-95 transition">
          <span id="icon-mute" class="text-base leading-none">🔊</span>
        </button>
      </div>

      <!-- Center HUD: Best + Shards -->
      <div class="flex items-center gap-1.5">
        <div class="rounded-full bg-slate-900/65 px-3 py-1.5 text-xs font-bold tracking-wide text-cyan-300 backdrop-blur-md border border-white/15">
          BEST <span id="hud-best-value">0</span>
        </div>
        <div class="rounded-full bg-slate-900/65 px-3 py-1.5 text-xs font-bold tracking-wide text-amber-300 backdrop-blur-md border border-white/15">
          ✦ <span id="hud-shards-value">0</span>
        </div>
      </div>

      <div class="pointer-events-auto">
        <button id="btn-pause" type="button" title="Pause / Resume (P)" aria-label="Pause or resume game"
          class="flex h-10 w-10 items-center justify-center rounded-xl bg-slate-900/65 text-white backdrop-blur-md border border-white/15 hover:bg-slate-800/80 active:scale-95 transition disabled:opacity-40 disabled:pointer-events-none">
          <span id="icon-pause" class="text-xs font-bold leading-none">❚❚</span>
        </button>
      </div>
    </div>

    <!-- Live Score & Active Power-Ups -->
    <div id="live-hud" class="pointer-events-none absolute top-16 left-0 right-0 z-10 flex flex-col items-center hidden">
      <div id="sector-pill" class="mb-1 rounded-full bg-indigo-950/70 px-2.5 py-0.5 text-[10px] font-bold uppercase tracking-widest text-indigo-200 border border-indigo-400/25">
        Sector 1 · Orbital Dawn
      </div>
      <span id="score-text" class="font-display text-4xl sm:text-5xl font-black text-white"
        style="text-shadow: 0 3px 12px rgba(6, 182, 212, 0.55), 0 2px 0 #0f172a;">0</span>
      <div id="active-buffs" class="mt-2 flex items-center gap-2"></div>
    </div>

    <!-- Start / Ready Overlay -->
    <div id="overlay-ready" class="pointer-events-none absolute inset-0 z-10 flex flex-col items-center justify-between pt-20 pb-24 px-5">
      <div class="text-center mt-1">
        <span class="inline-block rounded-full bg-cyan-500/20 border border-cyan-400/40 px-3 py-0.5 text-[10px] font-bold uppercase tracking-widest text-cyan-300">
          Original Orbital Arcade
        </span>
        <h1 class="mt-2 font-display text-3xl sm:text-4xl font-black tracking-wider text-white"
          style="text-shadow: 0 4px 18px rgba(56, 189, 248, 0.55), 0 2px 0 #020617;">STARHOPPER</h1>
        <p class="mt-2.5 inline-block rounded-full bg-slate-900/65 px-3.5 py-1 text-xs font-medium text-slate-200 backdrop-blur-sm border border-white/15">
          Tap, Click, or Press Space to Boost
        </p>
      </div>

      <!-- Bottom Controls: Difficulty + Hangar -->
      <div class="pointer-events-auto w-full max-w-xs flex flex-col gap-2.5">
        <div class="flex flex-col items-center gap-2 bg-slate-900/75 backdrop-blur-md rounded-2xl p-3 border border-white/15 shadow-lg">
          <span class="text-[10px] font-bold uppercase tracking-widest text-slate-300">Flight Mode</span>
          <div class="flex w-full items-center gap-1.5" role="group" aria-label="Difficulty selector">
            <button type="button" data-diff="cadet"
              class="diff-btn flex-1 rounded-lg py-1.5 text-xs font-semibold transition bg-white/10 text-slate-200 hover:bg-white/20">
              Cadet
            </button>
            <button type="button" data-diff="pilot"
              class="diff-btn flex-1 rounded-lg py-1.5 text-xs font-semibold transition bg-cyan-400 text-slate-950 shadow">
              Pilot
            </button>
            <button type="button" data-diff="ace"
              class="diff-btn flex-1 rounded-lg py-1.5 text-xs font-semibold transition bg-white/10 text-slate-200 hover:bg-white/20">
              Ace
            </button>
          </div>
        </div>

        <button id="btn-open-hangar" type="button"
          class="w-full rounded-2xl bg-indigo-600/90 hover:bg-indigo-500 active:scale-[0.98] border border-indigo-400/40 py-2.5 text-xs font-bold uppercase tracking-wider text-white shadow-lg backdrop-blur-md transition">
          🚀 Ship Hangar &amp; Skins
        </button>
      </div>
    </div>

    <!-- Hangar / Skin Shop Modal -->
    <div id="overlay-hangar" class="hidden absolute inset-0 z-30 flex items-center justify-center bg-slate-950/80 backdrop-blur-md p-4">
      <div class="w-full max-w-sm rounded-3xl bg-slate-900 border border-indigo-500/35 p-5 shadow-2xl">
        <div class="flex items-center justify-between">
          <div>
            <h2 class="font-display text-lg font-bold text-white">SHIP HANGAR</h2>
            <p class="text-xs text-slate-400">Unlock craft with Star Shards (✦)</p>
          </div>
          <div class="rounded-full bg-amber-500/15 border border-amber-400/30 px-3 py-1 text-xs font-bold text-amber-300">
            ✦ <span id="hangar-shards">0</span>
          </div>
        </div>

        <div id="hangar-list" class="mt-4 flex flex-col gap-2.5 max-h-[340px] overflow-y-auto pr-1"></div>

        <button id="btn-close-hangar" type="button"
          class="mt-4 w-full rounded-xl bg-white/10 hover:bg-white/20 active:scale-[0.98] py-2.5 text-sm font-semibold text-white transition">
          Back to Launchpad
        </button>
      </div>
    </div>

    <!-- Pause Overlay -->
    <div id="overlay-pause" class="hidden absolute inset-0 z-30 flex items-center justify-center bg-slate-950/70 backdrop-blur-sm p-6">
      <div class="w-full max-w-xs rounded-3xl bg-slate-900/95 border border-white/15 p-6 text-center shadow-2xl">
        <h2 class="font-display text-lg font-bold text-cyan-300">FLIGHT PAUSED</h2>
        <p class="mt-2 text-xs text-slate-300">Systems on standby. Press P or Resume to continue.</p>
        <div class="mt-5 flex flex-col gap-2.5">
          <button id="btn-resume" type="button"
            class="w-full rounded-xl bg-cyan-400 hover:bg-cyan-300 active:scale-[0.98] py-2.5 text-sm font-bold text-slate-950 transition shadow">
            Resume Flight
          </button>
          <button id="btn-pause-restart" type="button"
            class="w-full rounded-xl bg-white/10 hover:bg-white/20 active:scale-[0.98] py-2.5 text-sm font-semibold text-white transition">
            Return to Launchpad
          </button>
        </div>
      </div>
    </div>

    <!-- Game Over Scorecard Overlay -->
    <div id="overlay-gameover" class="hidden absolute inset-0 z-30 flex items-center justify-center bg-slate-950/65 backdrop-blur-sm p-5">
      <div class="w-full max-w-[330px] rounded-3xl bg-slate-900/95 border border-indigo-400/35 p-5 text-white shadow-2xl">
        <div class="text-center">
          <span id="gameover-sector" class="text-[10px] font-bold uppercase tracking-widest text-indigo-300">Sector 1</span>
          <h2 class="mt-1 font-display text-xl font-black text-rose-400">HULL BREACH</h2>
        </div>

        <div class="mt-4 flex items-center justify-between gap-4 rounded-2xl bg-slate-800/85 border border-white/10 p-4">
          <!-- Rank Badge -->
          <div class="flex flex-col items-center justify-center w-20">
            <span class="text-[10px] font-bold uppercase tracking-wider text-slate-400">Rank</span>
            <div id="medal-badge" class="mt-1.5 flex h-14 w-14 items-center justify-center rounded-full border-2 border-slate-600 bg-slate-700/60 text-2xl shadow-inner">
              —
            </div>
            <span id="medal-label" class="mt-1 text-[10px] font-bold text-slate-300">Cadet</span>
          </div>

          <!-- Scores -->
          <div class="flex flex-1 flex-col items-end gap-2">
            <div class="text-right">
              <div class="text-[10px] font-bold uppercase tracking-wider text-slate-400">Score</div>
              <div id="final-score" class="font-display text-2xl font-black text-white">0</div>
            </div>
            <div class="text-right">
              <div class="flex items-center justify-end gap-1.5">
                <span id="new-best-badge" class="hidden rounded bg-cyan-400 px-1.5 py-0.5 text-[9px] font-extrabold uppercase text-slate-950">NEW</span>
                <span class="text-[10px] font-bold uppercase tracking-wider text-slate-400">Best</span>
              </div>
              <div id="final-best" class="font-display text-xl font-bold text-cyan-300">0</div>
            </div>
          </div>
        </div>

        <div class="mt-3 flex items-center justify-between rounded-xl bg-amber-500/10 border border-amber-400/20 px-3.5 py-2 text-xs">
          <span class="text-amber-200">Shards Collected</span>
          <span id="run-shards" class="font-bold text-amber-300">+0 ✦</span>
        </div>

        <div class="mt-4 flex gap-2">
          <button id="btn-gameover-hangar" type="button"
            class="rounded-xl bg-indigo-600/80 hover:bg-indigo-500 active:scale-[0.98] px-3.5 py-3 text-xs font-bold text-white transition">
            🚀 Hangar
          </button>
          <button id="btn-play-again" type="button"
            class="flex-1 rounded-xl bg-cyan-400 hover:bg-cyan-300 active:scale-[0.98] py-3 font-display text-xs font-black text-slate-950 shadow transition">
            LAUNCH AGAIN
          </button>
        </div>
        <p class="mt-2.5 text-center text-[11px] font-medium text-slate-400">
          Press Space or R to relaunch
        </p>
      </div>
    </div>
  </div>

  <script>
    (function () {
      'use strict';

      // --- Safe Storage ---
      const STORAGE_KEYS = {
        best: 'starhopper_best_v1',
        shards: 'starhopper_shards_v1',
        unlocked: 'starhopper_unlocked_v1',
        skin: 'starhopper_skin_v1',
        mute: 'starhopper_mute_v1',
        theme: 'starhopper_theme_v1'
      };

      function readStorage(key, fallback) {
        try {
          const val = localStorage.getItem(key);
          return val !== null ? val : fallback;
        } catch (e) {
          return fallback;
        }
      }

      function writeStorage(key, val) {
        try {
          localStorage.setItem(key, String(val));
        } catch (e) {
          // Ignore storage restrictions
        }
      }

      // --- Ship Skins Catalog ---
      const SHIPS = [
        { id: 'vanguard', name: 'Vanguard Pod', desc: 'Standard orbital explorer', cost: 0, body: '#38bdf8', accent: '#e0f2fe', flame: '#fb923c' },
        { id: 'crimson', name: 'Crimson Comet', desc: 'High-contrast interceptor hull', cost: 15, body: '#f43f5e', accent: '#ffe4e6', flame: '#fde047' },
        { id: 'emerald', name: 'Emerald Viper', desc: 'Bio-luminescent stealth craft', cost: 35, body: '#10b981', accent: '#d1fae5', flame: '#38bdf8' },
        { id: 'solaris', name: 'Solaris Gold', desc: 'Gilded deep-space flagship', cost: 75, body: '#f59e0b', accent: '#fef3c7', flame: '#f43f5e' }
      ];

      // --- Sectors (Biome Progression) ---
      const SECTORS = [
        { name: 'Orbital Dawn', minScore: 0, top: '#0f172a', mid: '#1e3a5f', bot: '#0284c7', gate: '#22d3ee', gateDark: '#0e7490' },
        { name: 'Nebula Drift', minScore: 10, top: '#1e1b4b', mid: '#4c1d95', bot: '#9d174d', gate: '#e879f9', gateDark: '#a21caf' },
        { name: 'Deep Void', minScore: 25, top: '#020617', mid: '#0f172a', bot: '#1e293b', gate: '#34d399', gateDark: '#059669' },
        { name: 'Solar Flare', minScore: 45, top: '#2a0a0a', mid: '#7c2d12', bot: '#c2410c', gate: '#fbbf24', gateDark: '#b45309' }
      ];

      // --- DOM Elements ---
      const shell = document.getElementById('app-shell');
      const canvas = document.getElementById('game-canvas');
      const ctx = canvas.getContext('2d');

      const btnTheme = document.getElementById('btn-theme');
      const iconTheme = document.getElementById('icon-theme');
      const btnMute = document.getElementById('btn-mute');
      const iconMute = document.getElementById('icon-mute');
      const btnPause = document.getElementById('btn-pause');
      const iconPause = document.getElementById('icon-pause');

      const hudBestValue = document.getElementById('hud-best-value');
      const hudShardsValue = document.getElementById('hud-shards-value');
      const liveHudEl = document.getElementById('live-hud');
      const sectorPillEl = document.getElementById('sector-pill');
      const scoreTextEl = document.getElementById('score-text');
      const activeBuffsEl = document.getElementById('active-buffs');

      const overlayReady = document.getElementById('overlay-ready');
      const overlayHangar = document.getElementById('overlay-hangar');
      const overlayPause = document.getElementById('overlay-pause');
      const overlayGameover = document.getElementById('overlay-gameover');

      const btnOpenHangar = document.getElementById('btn-open-hangar');
      const btnCloseHangar = document.getElementById('btn-close-hangar');
      const btnGameoverHangar = document.getElementById('btn-gameover-hangar');
      const hangarShardsEl = document.getElementById('hangar-shards');
      const hangarListEl = document.getElementById('hangar-list');

      const btnResume = document.getElementById('btn-resume');
      const btnPauseRestart = document.getElementById('btn-pause-restart');
      const btnPlayAgain = document.getElementById('btn-play-again');

      const gameoverSectorEl = document.getElementById('gameover-sector');
      const finalScoreEl = document.getElementById('final-score');
      const finalBestEl = document.getElementById('final-best');
      const newBestBadge = document.getElementById('new-best-badge');
      const runShardsEl = document.getElementById('run-shards');
      const medalBadge = document.getElementById('medal-badge');
      const medalLabel = document.getElementById('medal-label');
      const diffButtons = document.querySelectorAll('.diff-btn');

      // --- Synthesized Web Audio ---
      let audioCtx = null;
      let isMuted = readStorage(STORAGE_KEYS.mute, 'false') === 'true';

      function ensureAudio() {
        if (isMuted) return null;
        if (!audioCtx) {
          const AudioContextClass = window.AudioContext || window.webkitAudioContext;
          if (AudioContextClass) {
            audioCtx = new AudioContextClass();
          }
        }
        if (audioCtx && audioCtx.state === 'suspended') {
          audioCtx.resume().catch(function () {});
        }
        return audioCtx;
      }

      function playTone(freqStart, freqEnd, duration, type, gainPeak, delay) {
        const ac = ensureAudio();
        if (!ac || isMuted) return;
        const now = ac.currentTime + (delay || 0);
        const osc = ac.createOscillator();
        const gain = ac.createGain();
        osc.type = type || 'sine';
        osc.frequency.setValueAtTime(freqStart, now);
        if (freqEnd !== freqStart) {
          osc.frequency.exponentialRampToValueAtTime(Math.max(20, freqEnd), now + duration);
        }
        gain.gain.setValueAtTime(0.0001, now);
        gain.gain.linearRampToValueAtTime(gainPeak || 0.12, now + 0.01);
        gain.gain.exponentialRampToValueAtTime(0.0001, now + duration);
        osc.connect(gain);
        gain.connect(ac.destination);
        osc.start(now);
        osc.stop(now + duration + 0.02);
      }

      function sfxBoost() {
        playTone(220, 440, 0.09, 'triangle', 0.12, 0);
      }

      function sfxGate() {
        playTone(660, 880, 0.08, 'sine', 0.08, 0);
      }

      function sfxShard() {
        playTone(987.77, 1318.51, 0.11, 'sine', 0.1, 0);
      }

      function sfxPowerup() {
        playTone(523.25, 1046.5, 0.18, 'triangle', 0.12, 0);
      }

      function sfxShieldBreak() {
        playTone(420, 120, 0.22, 'sawtooth', 0.15, 0);
      }

      function sfxHit() {
        playTone(150, 42, 0.24, 'sawtooth', 0.18, 0);
      }

      function sfxNewBest() {
        playTone(523.25, 523.25, 0.09, 'triangle', 0.12, 0.08);
        playTone(659.25, 659.25, 0.09, 'triangle', 0.12, 0.17);
        playTone(783.99, 783.99, 0.18, 'triangle', 0.14, 0.26);
      }

      // --- Game Configuration ---
      const DIFFICULTIES = {
        cadet: { gravity: 1320, boostVy: -410, speed: 165, gap: 170, interval: 1.62 },
        pilot: { gravity: 1620, boostVy: -460, speed: 195, gap: 146, interval: 1.38 },
        ace:   { gravity: 1920, boostVy: -505, speed: 235, gap: 132, interval: 1.15 }
      };

      let currentDifficulty = 'pilot';
      let isHighContrast = readStorage(STORAGE_KEYS.theme, 'nebula') === 'deep';
      let bestScore = parseInt(readStorage(STORAGE_KEYS.best, '0'), 10) || 0;
      let totalShards = parseInt(readStorage(STORAGE_KEYS.shards, '0'), 10) || 0;
      let unlockedSkins = ['vanguard'];
      try {
        const parsed = JSON.parse(readStorage(STORAGE_KEYS.unlocked, '["vanguard"]'));
        if (Array.isArray(parsed) && parsed.length > 0) unlockedSkins = parsed;
      } catch (e) {}
      let activeSkinId = readStorage(STORAGE_KEYS.skin, 'vanguard');
      if (unlockedSkins.indexOf(activeSkinId) === -1) activeSkinId = 'vanguard';

      // --- Canvas Sizing ---
      let viewW = 400;
      let viewH = 700;
      let floorHeight = 76;

      function resizeCanvas() {
        const rect = shell.getBoundingClientRect();
        const dpr = Math.min(window.devicePixelRatio || 1, 2);
        viewW = Math.max(280, Math.floor(rect.width));
        viewH = Math.max(420, Math.floor(rect.height));
        floorHeight = Math.round(Math.max(64, Math.min(94, viewH * 0.11)));

        canvas.width = Math.floor(viewW * dpr);
        canvas.height = Math.floor(viewH * dpr);
        ctx.setTransform(dpr, 0, 0, dpr, 0, 0);

        if (state === 'READY') {
          ship.x = Math.round(viewW * 0.26);
          ship.y = Math.round((viewH - floorHeight) * 0.46);
        }
      }

      // --- Game State ---
      // READY | HANGAR | PLAYING | PAUSED | DYING | GAME_OVER
      let state = 'READY';
      let previousMenuState = 'READY';
      let score = 0;
      let runShards = 0;
      let currentSectorIdx = 0;
      let spawnTimer = 0;
      let floorOffset = 0;
      let flashAlpha = 0;
      let shakeTimer = 0;
      let gameOverTimestamp = 0;
      let rafId = null;
      let lastTime = 0;

      const ship = {
        x: 110,
        y: 280,
        vy: 0,
        radius: 15,
        rotation: 0,
        hasShield: false,
        magnetTimer: 0,
        invulnTimer: 0
      };

      let gates = [];
      let pickups = [];
      let particles = [];
      let starfield = [];

      function initStarfield() {
        starfield = [];
        for (let i = 0; i < 65; i++) {
          starfield.push({
            x: Math.random(),
            y: Math.random() * 0.88,
            r: 0.7 + Math.random() * 1.8,
            speed: 12 + Math.random() * 45,
            twinkle: Math.random() * Math.PI * 2
          });
        }
      }

      function getActiveSkin() {
        return SHIPS.find(function (s) { return s.id === activeSkinId; }) || SHIPS[0];
      }

      function getCurrentSector() {
        let idx = 0;
        for (let i = 0; i < SECTORS.length; i++) {
          if (score >= SECTORS[i].minScore) idx = i;
        }
        return { idx: idx, data: SECTORS[idx] };
      }

      // --- UI Sync ---
      function updateMuteUI() {
        iconMute.textContent = isMuted ? '🔇' : '🔊';
        btnMute.setAttribute('aria-pressed', String(isMuted));
      }

      function updateThemeUI() {
        iconTheme.textContent = isHighContrast ? '🌑' : '🌌';
      }

      function updateHUDValues() {
        hudBestValue.textContent = String(bestScore);
        hudShardsValue.textContent = String(totalShards);
        hangarShardsEl.textContent = String(totalShards);
      }

      function updateDifficultyUI() {
        diffButtons.forEach(function (btn) {
          const active = btn.dataset.diff === currentDifficulty;
          btn.className = active
            ? 'diff-btn flex-1 rounded-lg py-1.5 text-xs font-semibold transition bg-cyan-400 text-slate-950 shadow'
            : 'diff-btn flex-1 rounded-lg py-1.5 text-xs font-semibold transition bg-white/10 text-slate-200 hover:bg-white/20';
        });
      }

      function updateBuffsHUD() {
        const badges = [];
        if (ship.hasShield) {
          badges.push('<span class="rounded-full bg-cyan-500/25 border border-cyan-400/50 px-2.5 py-0.5 text-[10px] font-bold text-cyan-200">🛡️ SHIELD</span>');
        }
        if (ship.magnetTimer > 0) {
          badges.push('<span class="rounded-full bg-amber-500/25 border border-amber-400/50 px-2.5 py-0.5 text-[10px] font-bold text-amber-200">🧲 ' + ship.magnetTimer.toFixed(1) + 's</span>');
        }
        activeBuffsEl.innerHTML = badges.join('');
      }

      function setOverlayState() {
        overlayReady.classList.toggle('hidden', state !== 'READY');
        overlayHangar.classList.toggle('hidden', state !== 'HANGAR');
        overlayPause.classList.toggle('hidden', state !== 'PAUSED');
        overlayGameover.classList.toggle('hidden', state !== 'GAME_OVER');
        liveHudEl.classList.toggle('hidden', state !== 'PLAYING' && state !== 'PAUSED' && state !== 'DYING');
        btnPause.disabled = !(state === 'PLAYING' || state === 'PAUSED');
        iconPause.textContent = state === 'PAUSED' ? '▶' : '❚❚';
      }

      function getRank(sc) {
        if (sc >= 45) return { icon: '💠', name: 'Quantum', bg: 'bg-cyan-500/20 border-cyan-400' };
        if (sc >= 30) return { icon: '🥇', name: 'Commander', bg: 'bg-amber-500/20 border-amber-400' };
        if (sc >= 15) return { icon: '🥈', name: 'Navigator', bg: 'bg-slate-400/20 border-slate-300' };
        if (sc >= 5)  return { icon: '🥉', name: 'Ensign', bg: 'bg-orange-500/20 border-orange-400' };
        return { icon: '—', name: 'Cadet', bg: 'bg-slate-700/60 border-slate-600' };
      }

      // --- Hangar Rendering ---
      function renderHangar() {
        updateHUDValues();
        hangarListEl.innerHTML = '';
        SHIPS.forEach(function (item) {
          const isUnlocked = unlockedSkins.indexOf(item.id) !== -1;
          const isEquipped = activeSkinId === item.id;
          const canAfford = totalShards >= item.cost;

          const row = document.createElement('div');
          row.className = 'flex items-center justify-between gap-3 rounded-2xl bg-slate-800/80 border p-3 ' +
            (isEquipped ? 'border-cyan-400/70' : 'border-white/10');

          const left = document.createElement('div');
          left.className = 'flex items-center gap-3';

          const swatch = document.createElement('div');
          swatch.className = 'flex h-10 w-10 shrink-0 items-center justify-center rounded-xl border border-white/20';
          swatch.style.background = 'linear-gradient(135deg, ' + item.body + ', ' + item.accent + ')';
          swatch.innerHTML = '<span class="text-sm">🚀</span>';

          const info = document.createElement('div');
          info.innerHTML = '<div class="text-xs font-bold text-white">' + item.name + '</div>' +
            '<div class="text-[11px] text-slate-400">' + item.desc + '</div>';

          left.appendChild(swatch);
          left.appendChild(info);

          const btn = document.createElement('button');
          btn.type = 'button';
          if (isEquipped) {
            btn.textContent = 'Equipped';
            btn.disabled = true;
            btn.className = 'rounded-xl bg-cyan-400/20 border border-cyan-400/40 px-3 py-1.5 text-xs font-bold text-cyan-300';
          } else if (isUnlocked) {
            btn.textContent = 'Equip';
            btn.className = 'rounded-xl bg-white/15 hover:bg-white/25 px-3 py-1.5 text-xs font-bold text-white transition';
            btn.addEventListener('click', function () {
              activeSkinId = item.id;
              writeStorage(STORAGE_KEYS.skin, activeSkinId);
              renderHangar();
            });
          } else {
            btn.textContent = '✦ ' + item.cost;
            btn.disabled = !canAfford;
            btn.className = canAfford
              ? 'rounded-xl bg-amber-400 hover:bg-amber-300 px-3 py-1.5 text-xs font-bold text-slate-950 transition'
              : 'rounded-xl bg-slate-700/60 px-3 py-1.5 text-xs font-bold text-slate-400 cursor-not-allowed';
            if (canAfford) {
              btn.addEventListener('click', function () {
                totalShards -= item.cost;
                unlockedSkins.push(item.id);
                activeSkinId = item.id;
                writeStorage(STORAGE_KEYS.shards, totalShards);
                writeStorage(STORAGE_KEYS.unlocked, JSON.stringify(unlockedSkins));
                writeStorage(STORAGE_KEYS.skin, activeSkinId);
                sfxPowerup();
                renderHangar();
              });
            }
          }

          row.appendChild(left);
          row.appendChild(btn);
          hangarListEl.appendChild(row);
        });
      }

      // --- State Transitions ---
      function resetGame() {
        state = 'READY';
        score = 0;
        runShards = 0;
        currentSectorIdx = 0;
        gates = [];
        pickups = [];
        particles = [];
        spawnTimer = 0;
        flashAlpha = 0;
        shakeTimer = 0;

        ship.x = Math.round(viewW * 0.26);
        ship.y = Math.round((viewH - floorHeight) * 0.46);
        ship.vy = 0;
        ship.rotation = 0;
        ship.hasShield = false;
        ship.magnetTimer = 0;
        ship.invulnTimer = 0;

        scoreTextEl.textContent = '0';
        sectorPillEl.textContent = 'Sector 1 · Orbital Dawn';
        updateBuffsHUD();
        setOverlayState();
      }

      function spawnThrusterParticles(count) {
        const skin = getActiveSkin();
        for (let i = 0; i < count; i++) {
          particles.push({
            x: ship.x - 14 + (Math.random() - 0.5) * 6,
            y: ship.y + 3 + (Math.random() - 0.5) * 8,
            vx: -65 - Math.random() * 75,
            vy: (Math.random() - 0.5) * 65,
            r: 2 + Math.random() * 3,
            life: 0.28 + Math.random() * 0.2,
            maxLife: 0.48,
            color: i % 2 === 0 ? skin.flame : '#fef08a'
          });
        }
      }

      function boost() {
        if (state === 'PAUSED' || state === 'DYING' || state === 'GAME_OVER' || state === 'HANGAR') return;

        const cfg = DIFFICULTIES[currentDifficulty];
        if (state === 'READY') {
          state = 'PLAYING';
          spawnTimer = cfg.interval * 0.55;
          setOverlayState();
        }
        if (state === 'PLAYING') {
          ship.vy = cfg.boostVy;
          ship.rotation = -0.4;
          spawnThrusterParticles(6);
          sfxBoost();
        }
      }

      function handleObstacleImpact(hitFloorDirectly) {
        if (ship.invulnTimer > 0) return;

        if (!hitFloorDirectly && ship.hasShield) {
          ship.hasShield = false;
          ship.invulnTimer = 1.1;
          flashAlpha = 0.45;
          shakeTimer = 0.16;
          sfxShieldBreak();
          updateBuffsHUD();
          for (let i = 0; i < 14; i++) {
            const ang = (Math.PI * 2 * i) / 14;
            particles.push({
              x: ship.x,
              y: ship.y,
              vx: Math.cos(ang) * 140,
              vy: Math.sin(ang) * 140,
              r: 3,
              life: 0.4,
              maxLife: 0.4,
              color: '#22d3ee'
            });
          }
          return;
        }

        if (state === 'DYING' || state === 'GAME_OVER') return;
        flashAlpha = 0.75;
        shakeTimer = 0.25;
        sfxHit();

        if (hitFloorDirectly) {
          finishGameOver();
        } else {
          state = 'DYING';
          ship.vy = Math.max(ship.vy, 70);
          setOverlayState();
        }
      }

      function finishGameOver() {
        state = 'GAME_OVER';
        gameOverTimestamp = performance.now();

        let isNew = false;
        if (score > bestScore) {
          bestScore = score;
          writeStorage(STORAGE_KEYS.best, bestScore);
          isNew = true;
          sfxNewBest();
        }
        if (runShards > 0) {
          totalShards += runShards;
          writeStorage(STORAGE_KEYS.shards, totalShards);
        }
        updateHUDValues();

        const sec = getCurrentSector();
        gameoverSectorEl.textContent = 'Sector ' + (sec.idx + 1) + ' · ' + sec.data.name;
        finalScoreEl.textContent = String(score);
        finalBestEl.textContent = String(bestScore);
        newBestBadge.classList.toggle('hidden', !isNew);
        runShardsEl.textContent = '+' + runShards + ' ✦';

        const rank = getRank(score);
        medalBadge.textContent = rank.icon;
        medalBadge.className = 'mt-1.5 flex h-14 w-14 items-center justify-center rounded-full border-2 text-2xl shadow-inner ' + rank.bg;
        medalLabel.textContent = rank.name;

        setOverlayState();
      }

      function togglePause() {
        if (state === 'PLAYING') {
          state = 'PAUSED';
          setOverlayState();
        } else if (state === 'PAUSED') {
          state = 'PLAYING';
          lastTime = performance.now();
          setOverlayState();
        }
      }

      function spawnGateAndPickups() {
        const cfg = DIFFICULTIES[currentDifficulty];
        const playH = viewH - floorHeight;
        const speedBonus = Math.min(45, score * 1.1);
        const currentGap = Math.max(114, cfg.gap - Math.min(18, score * 0.35));

        const minTop = 64;
        const maxTop = Math.max(minTop + 24, playH - currentGap - 64);
        const topHeight = minTop + Math.random() * (maxTop - minTop);
        const gateW = Math.round(Math.max(46, Math.min(58, viewW * 0.13)));
        const spawnX = viewW + 24;

        gates.push({
          x: spawnX,
          width: gateW,
          topHeight: topHeight,
          gap: currentGap,
          speedOffset: speedBonus,
          passed: false
        });

        // Spawn a Star Shard or Power-Up inside or near the gate
        const centerY = topHeight + currentGap / 2;
        const roll = Math.random();
        if (roll < 0.72) {
          pickups.push({
            x: spawnX + gateW / 2,
            y: centerY + (Math.random() - 0.5) * (currentGap * 0.36),
            type: 'shard',
            r: 10,
            bob: Math.random() * Math.PI * 2
          });
        } else if (roll < 0.85 && !ship.hasShield) {
          pickups.push({
            x: spawnX + gateW / 2,
            y: centerY,
            type: 'shield',
            r: 12,
            bob: Math.random() * Math.PI * 2
          });
        } else if (roll < 0.94 && ship.magnetTimer <= 0) {
          pickups.push({
            x: spawnX + gateW / 2,
            y: centerY,
            type: 'magnet',
            r: 12,
            bob: Math.random() * Math.PI * 2
          });
        }
      }

      // --- Update Loop ---
      function update(dt, nowSec) {
        const cfg = DIFFICULTIES[currentDifficulty];
        const playH = viewH - floorHeight;

        if (flashAlpha > 0) flashAlpha = Math.max(0, flashAlpha - dt * 2.8);
        if (shakeTimer > 0) shakeTimer = Math.max(0, shakeTimer - dt);

        // Starfield parallax
        const starMult = state === 'PLAYING' ? 1.5 : (state === 'READY' ? 0.55 : 0);
        if (starMult > 0) {
          for (let i = 0; i < starfield.length; i++) {
            const s = starfield[i];
            s.x -= (s.speed * starMult * dt) / viewW;
            if (s.x < 0) s.x += 1;
          }
        }

        // Particles
        for (let i = particles.length - 1; i >= 0; i--) {
          const p = particles[i];
          p.x += p.vx * dt;
          p.y += p.vy * dt;
          p.life -= dt;
          if (p.life <= 0) particles.splice(i, 1);
        }

        if (state === 'READY' || state === 'HANGAR') {
          floorOffset = (floorOffset + cfg.speed * 0.5 * dt) % 32;
          ship.y = Math.round(playH * 0.46) + Math.sin(nowSec * 3.8) * 8;
          ship.rotation = Math.sin(nowSec * 3.8) * 0.06;
          return;
        }

        if (state === 'PAUSED' || state === 'GAME_OVER') return;

        if (state === 'DYING') {
          ship.vy += cfg.gravity * dt;
          ship.y += ship.vy * dt;
          ship.rotation = Math.min(Math.PI / 2, ship.rotation + dt * 6);
          if (ship.y + ship.radius >= playH) {
            ship.y = playH - ship.radius;
            finishGameOver();
          }
          return;
        }

        // PLAYING
        if (ship.invulnTimer > 0) ship.invulnTimer = Math.max(0, ship.invulnTimer - dt);
        if (ship.magnetTimer > 0) {
          ship.magnetTimer = Math.max(0, ship.magnetTimer - dt);
          updateBuffsHUD();
        }

        const speedBoost = Math.min(45, score * 1.1);
        const moveSpeed = cfg.speed + speedBoost;
        floorOffset = (floorOffset + moveSpeed * dt) % 32;

        ship.vy += cfg.gravity * dt;
        ship.y += ship.vy * dt;

        if (ship.vy < 120) {
          ship.rotation = Math.max(-0.42, ship.rotation - dt * 3.4);
        } else {
          const targetRot = Math.min(Math.PI / 2, ((ship.vy - 120) / 480) * (Math.PI / 2));
          ship.rotation += (targetRot - ship.rotation) * Math.min(1, dt * 8);
        }

        if (ship.y - ship.radius < 0) {
          ship.y = ship.radius;
          ship.vy = 0;
        }

        if (ship.y + ship.radius >= playH) {
          ship.y = playH - ship.radius;
          handleObstacleImpact(true);
          return;
        }

        spawnTimer += dt;
        if (spawnTimer >= cfg.interval) {
          spawnTimer = 0;
          spawnGateAndPickups();
        }

        const hitR = ship.radius - 2.5;
        for (let i = gates.length - 1; i >= 0; i--) {
          const g = gates[i];
          g.x -= moveSpeed * dt;

          if (!g.passed && g.x + g.width / 2 < ship.x) {
            g.passed = true;
            score += 1;
            scoreTextEl.textContent = String(score);
            sfxGate();

            const sec = getCurrentSector();
            if (sec.idx !== currentSectorIdx) {
              currentSectorIdx = sec.idx;
              sectorPillEl.textContent = 'Sector ' + (sec.idx + 1) + ' · ' + sec.data.name;
              sfxPowerup();
            }
          }

          const bottomY = g.topHeight + g.gap;
          if (circleRectCollide(ship.x, ship.y, hitR, g.x, 0, g.width, g.topHeight) ||
              circleRectCollide(ship.x, ship.y, hitR, g.x, bottomY, g.width, playH - bottomY)) {
            handleObstacleImpact(false);
            if (state !== 'PLAYING') return;
          }

          if (g.x + g.width < -30) gates.splice(i, 1);
        }

        // Pickups
        for (let i = pickups.length - 1; i >= 0; i--) {
          const pk = pickups[i];
          pk.x -= moveSpeed * dt;
          pk.bob += dt * 4;

          if (pk.type === 'shard' && ship.magnetTimer > 0) {
            const dx = ship.x - pk.x;
            const dy = ship.y - pk.y;
            const dist = Math.hypot(dx, dy);
            if (dist < 190 && dist > 1) {
              pk.x += (dx / dist) * 340 * dt;
              pk.y += (dy / dist) * 340 * dt;
            }
          }

          const d = Math.hypot(ship.x - pk.x, ship.y - pk.y);
          if (d < ship.radius + pk.r + 4) {
            if (pk.type === 'shard') {
              runShards += 1;
              sfxShard();
            } else if (pk.type === 'shield') {
              ship.hasShield = true;
              sfxPowerup();
              updateBuffsHUD();
            } else if (pk.type === 'magnet') {
              ship.magnetTimer = 7.0;
              sfxPowerup();
              updateBuffsHUD();
            }
            pickups.splice(i, 1);
            continue;
          }

          if (pk.x < -30) pickups.splice(i, 1);
        }
      }

      function circleRectCollide(cx, cy, cr, rx, ry, rw, rh) {
        const closestX = Math.max(rx, Math.min(cx, rx + rw));
        const closestY = Math.max(ry, Math.min(cy, ry + rh));
        const dx = cx - closestX;
        const dy = cy - closestY;
        return dx * dx + dy * dy < cr * cr;
      }

      // --- Rendering ---
      function render(nowSec) {
        const playH = viewH - floorHeight;
        const sec = getCurrentSector().data;

        ctx.save();
        if (shakeTimer > 0) {
          const mag = (shakeTimer / 0.25) * 6;
          ctx.translate((Math.random() - 0.5) * mag * 2, (Math.random() - 0.5) * mag * 2);
        }

        // Space Sky Gradient
        const skyGrad = ctx.createLinearGradient(0, 0, 0, playH);
        if (isHighContrast) {
          skyGrad.addColorStop(0, '#020617');
          skyGrad.addColorStop(1, '#0f172a');
        } else {
          skyGrad.addColorStop(0, sec.top);
          skyGrad.addColorStop(0.55, sec.mid);
          skyGrad.addColorStop(1, sec.bot);
        }
        ctx.fillStyle = skyGrad;
        ctx.fillRect(0, 0, viewW, playH);

        // Starfield
        for (let i = 0; i < starfield.length; i++) {
          const s = starfield[i];
          const alpha = 0.35 + 0.55 * Math.sin(nowSec * 2.2 + s.twinkle);
          ctx.fillStyle = 'rgba(255, 255, 255, ' + alpha.toFixed(2) + ')';
          ctx.beginPath();
          ctx.arc(s.x * viewW, s.y * playH, s.r, 0, Math.PI * 2);
          ctx.fill();
        }

        // Distant Planet
        ctx.fillStyle = isHighContrast ? 'rgba(56, 189, 248, 0.14)' : 'rgba(255, 255, 255, 0.12)';
        ctx.beginPath();
        ctx.arc(viewW * 0.78, playH * 0.22, 34, 0, Math.PI * 2);
        ctx.fill();
        ctx.strokeStyle = 'rgba(255, 255, 255, 0.16)';
        ctx.lineWidth = 2;
        ctx.beginPath();
        ctx.ellipse(viewW * 0.78, playH * 0.22, 54, 11, -0.25, 0, Math.PI * 2);
        ctx.stroke();

        // Laser Gates
        for (let i = 0; i < gates.length; i++) {
          drawGate(gates[i], playH, sec);
        }

        // Pickups
        for (let i = 0; i < pickups.length; i++) {
          drawPickup(pickups[i]);
        }

        // Particles
        for (let i = 0; i < particles.length; i++) {
          const p = particles[i];
          ctx.globalAlpha = Math.max(0, p.life / p.maxLife);
          ctx.fillStyle = p.color;
          ctx.beginPath();
          ctx.arc(p.x, p.y, p.r, 0, Math.PI * 2);
          ctx.fill();
        }
        ctx.globalAlpha = 1;

        // Deck Floor
        drawDeckFloor(playH, sec);

        // Player Craft
        drawShip(nowSec);

        // Impact Flash
        if (flashAlpha > 0) {
          ctx.fillStyle = 'rgba(255, 255, 255, ' + flashAlpha.toFixed(2) + ')';
          ctx.fillRect(0, 0, viewW, viewH);
        }

        ctx.restore();
      }

      function drawGate(g, playH, sec) {
        const capH = 16;
        // Top pylon
        if (g.topHeight > 0) {
          drawPylon(g.x, 0, g.width, g.topHeight, true, sec);
        }
        // Bottom pylon
        const bottomY = g.topHeight + g.gap;
        const bottomH = playH - bottomY;
        if (bottomH > 0) {
          drawPylon(g.x, bottomY, g.width, bottomH, false, sec);
        }
        // Subtle energy beam hint at cap
        ctx.fillStyle = sec.gate;
        ctx.globalAlpha = 0.2;
        ctx.fillRect(g.x + g.width * 0.42, g.topHeight - capH, g.width * 0.16, capH);
        ctx.fillRect(g.x + g.width * 0.42, bottomY, g.width * 0.16, capH);
        ctx.globalAlpha = 1;
      }

      function drawPylon(x, y, w, h, isTop, sec) {
        // Metallic body
        ctx.fillStyle = '#1e293b';
        ctx.fillRect(x, y, w, h);

        // Glowing core strip
        ctx.fillStyle = sec.gateDark;
        ctx.fillRect(x + 8, y, w - 16, h);

        ctx.fillStyle = sec.gate;
        ctx.fillRect(x + w * 0.38, y, w * 0.24, h);

        // Emitter cap
        const capY = isTop ? y + h - 14 : y;
        ctx.fillStyle = '#334155';
        ctx.fillRect(x - 4, capY, w + 8, 14);
        ctx.strokeStyle = sec.gate;
        ctx.lineWidth = 2;
        ctx.strokeRect(x - 4, capY, w + 8, 14);
      }

      function drawPickup(pk) {
        const py = pk.y + Math.sin(pk.bob) * 4;
        ctx.save();
        ctx.translate(pk.x, py);

        if (pk.type === 'shard') {
          ctx.fillStyle = '#fbbf24';
          ctx.strokeStyle = '#fef3c7';
          ctx.lineWidth = 2;
          ctx.beginPath();
          ctx.moveTo(0, -10);
          ctx.lineTo(8, 0);
          ctx.lineTo(0, 10);
          ctx.lineTo(-8, 0);
          ctx.closePath();
          ctx.fill();
          ctx.stroke();
        } else if (pk.type === 'shield') {
          ctx.fillStyle = 'rgba(34, 211, 238, 0.25)';
          ctx.strokeStyle = '#22d3ee';
          ctx.lineWidth = 2.5;
          ctx.beginPath();
          ctx.arc(0, 0, 13, 0, Math.PI * 2);
          ctx.fill();
          ctx.stroke();
          ctx.fillStyle = '#e0f2fe';
          ctx.font = 'bold 12px Inter, sans-serif';
          ctx.textAlign = 'center';
          ctx.textBaseline = 'middle';
          ctx.fillText('S', 0, 1);
        } else if (pk.type === 'magnet') {
          ctx.fillStyle = 'rgba(245, 158, 11, 0.25)';
          ctx.strokeStyle = '#fbbf24';
          ctx.lineWidth = 2.5;
          ctx.beginPath();
          ctx.arc(0, 0, 13, 0, Math.PI * 2);
          ctx.fill();
          ctx.stroke();
          ctx.fillStyle = '#fef3c7';
          ctx.font = 'bold 12px Inter, sans-serif';
          ctx.textAlign = 'center';
          ctx.textBaseline = 'middle';
          ctx.fillText('M', 0, 1);
        }
        ctx.restore();
      }

      function drawDeckFloor(playH, sec) {
        ctx.fillStyle = '#0f172a';
        ctx.fillRect(0, playH, viewW, floorHeight);

        // Glowing top rail
        ctx.fillStyle = sec.gate;
        ctx.fillRect(0, playH, viewW, 4);

        // Scrolling tech chevrons
        ctx.fillStyle = '#1e293b';
        for (let x = -floorOffset - 32; x < viewW + 32; x += 32) {
          ctx.beginPath();
          ctx.moveTo(x, playH + 14);
          ctx.lineTo(x + 16, playH + 14);
          ctx.lineTo(x + 24, playH + 28);
          ctx.lineTo(x + 8, playH + 28);
          ctx.closePath();
          ctx.fill();
        }
      }

      function drawShip(nowSec) {
        const skin = getActiveSkin();
        ctx.save();
        ctx.translate(ship.x, ship.y);
        ctx.rotate(ship.rotation);

        if (ship.invulnTimer > 0 && Math.floor(nowSec * 16) % 2 === 0) {
          ctx.globalAlpha = 0.5;
        }

        // Shield bubble
        if (ship.hasShield) {
          ctx.strokeStyle = '#22d3ee';
          ctx.fillStyle = 'rgba(34, 211, 238, 0.16)';
          ctx.lineWidth = 2.5;
          ctx.beginPath();
          ctx.arc(0, 0, 23, 0, Math.PI * 2);
          ctx.fill();
          ctx.stroke();
        }

        // Rear thruster glow
        ctx.fillStyle = skin.flame;
        ctx.beginPath();
        ctx.moveTo(-14, -4);
        ctx.lineTo(-24 - Math.sin(nowSec * 30) * 3, 0);
        ctx.lineTo(-14, 4);
        ctx.closePath();
        ctx.fill();

        // Main spacecraft hull
        ctx.fillStyle = skin.body;
        ctx.strokeStyle = '#0f172a';
        ctx.lineWidth = 2.5;
        ctx.beginPath();
        ctx.moveTo(19, 0);
        ctx.quadraticCurveTo(8, -14, -13, -11);
        ctx.lineTo(-16, 0);
        ctx.lineTo(-13, 11);
        ctx.quadraticCurveTo(8, 14, 19, 0);
        ctx.closePath();
        ctx.fill();
        ctx.stroke();

        // Cockpit visor
        ctx.fillStyle = skin.accent;
        ctx.beginPath();
        ctx.ellipse(5, -2, 7, 4.5, 0.1, 0, Math.PI * 2);
        ctx.fill();
        ctx.stroke();

        // Wing fin
        ctx.fillStyle = skin.accent;
        ctx.beginPath();
        ctx.moveTo(-4, 4);
        ctx.lineTo(-14, 13);
        ctx.lineTo(-10, 2);
        ctx.closePath();
        ctx.fill();
        ctx.stroke();

        ctx.restore();
      }

      // --- Main Loop ---
      function loop(timestamp) {
        if (!lastTime) lastTime = timestamp;
        const dt = Math.min(0.05, Math.max(0, (timestamp - lastTime) / 1000));
        lastTime = timestamp;

        update(dt, timestamp / 1000);
        render(timestamp / 1000);

        rafId = window.requestAnimationFrame(loop);
      }

      // --- Event Listeners ---
      canvas.addEventListener('pointerdown', function (e) {
        e.preventDefault();
        boost();
      });

      window.addEventListener('keydown', function (e) {
        if (e.code === 'Space' || e.code === 'ArrowUp' || e.code === 'KeyW') {
          e.preventDefault();
          if (state === 'GAME_OVER') {
            if (performance.now() - gameOverTimestamp > 450) resetGame();
          } else {
            boost();
          }
        } else if (e.code === 'KeyP' || e.code === 'Escape') {
          if (state === 'PLAYING' || state === 'PAUSED') togglePause();
        } else if (e.code === 'KeyM') {
          isMuted = !isMuted;
          writeStorage(STORAGE_KEYS.mute, isMuted);
          updateMuteUI();
        } else if (e.code === 'KeyR') {
          if (state === 'GAME_OVER' || state === 'PAUSED') resetGame();
        }
      });

      btnTheme.addEventListener('click', function () {
        isHighContrast = !isHighContrast;
        writeStorage(STORAGE_KEYS.theme, isHighContrast ? 'deep' : 'nebula');
        updateThemeUI();
      });

      btnMute.addEventListener('click', function () {
        isMuted = !isMuted;
        writeStorage(STORAGE_KEYS.mute, isMuted);
        updateMuteUI();
      });

      btnPause.addEventListener('click', togglePause);
      btnResume.addEventListener('click', togglePause);
      btnPauseRestart.addEventListener('click', resetGame);
      btnPlayAgain.addEventListener('click', resetGame);

      btnOpenHangar.addEventListener('click', function () {
        previousMenuState = 'READY';
        state = 'HANGAR';
        renderHangar();
        setOverlayState();
      });

      btnGameoverHangar.addEventListener('click', function () {
        previousMenuState = 'GAME_OVER';
        state = 'HANGAR';
        renderHangar();
        setOverlayState();
      });

      btnCloseHangar.addEventListener('click', function () {
        state = previousMenuState === 'GAME_OVER' ? 'GAME_OVER' : 'READY';
        setOverlayState();
      });

      diffButtons.forEach(function (btn) {
        btn.addEventListener('click', function () {
          currentDifficulty = btn.dataset.diff;
          updateDifficultyUI();
        });
      });

      window.addEventListener('resize', resizeCanvas);

      window.onload = function () {
        initStarfield();
        updateMuteUI();
        updateThemeUI();
        updateHUDValues();
        updateDifficultyUI();
        resizeCanvas();
        resetGame();
        if (rafId) window.cancelAnimationFrame(rafId);
        lastTime = performance.now();
        rafId = window.requestAnimationFrame(loop);
      };
    })();
  </script>
</body>
</html>

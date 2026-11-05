<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
  <title>Parikrama Round Counter</title>
  
  <!-- PWA Metadata -->
  <link rel="manifest" href="manifest.json">
  <meta name="theme-color" content="#f59e0b">
  <meta name="apple-mobile-web-app-capable" content="yes">
  <meta name="apple-mobile-web-app-status-bar-style" content="black-translucent">
  <meta name="apple-mobile-web-app-title" content="Parikrama">

  <script src="https://cdn.tailwindcss.com"></script>
  <script>
    tailwind.config = {
      darkMode: 'class',
      theme: {
        extend: {
          colors: {
            saffron: {
              50: '#fffaf0',
              100: '#feebc8',
              200: '#fbd38d',
              300: '#f6ad55',
              400: '#ed8936',
              500: '#dd6b20',
              600: '#f59e0b',
              700: '#d97706',
              800: '#b45309',
              900: '#78350f',
            }
          }
        }
      }
    };
  </script>
  <style>
    @keyframes spin-slow {
      from { transform: rotate(0deg); }
      to { transform: rotate(360deg); }
    }
    .animate-spin-slow {
      animation: spin-slow 24s linear infinite;
    }
    .custom-scroll::-webkit-scrollbar {
      width: 4px;
    }
    .custom-scroll::-webkit-scrollbar-thumb {
      background: rgba(217, 119, 6, 0.2);
      border-radius: 4px;
    }
    button, input, select, [role="button"] {
      cursor: pointer !important;
      -webkit-tap-highlight-color: rgba(245, 158, 11, 0.2);
    }
  </style>
</head>
<body class="bg-[#fcfaf7] dark:bg-[#120e0b] text-stone-900 dark:text-stone-100 font-sans min-h-screen flex flex-col items-center justify-between transition-colors duration-300 pb-20">

  <!-- Container -->
  <div class="w-full max-w-md mx-auto min-h-screen flex flex-col justify-between p-4">

    <!-- HEADER -->
    <header class="flex items-center justify-between pb-2 border-b border-amber-200/60 dark:border-amber-900/40">
      <div class="flex items-center gap-2">
        <div class="w-9 h-9 rounded-full bg-gradient-to-tr from-amber-500 to-amber-300 flex items-center justify-center text-stone-950 font-bold shadow-xs">
          <svg class="w-5 h-5 text-stone-950 fill-current" viewBox="0 0 24 24"><path d="M12 2a10 10 0 1 0 10 10A10 10 0 0 0 12 2zm1 17.93V17a1 1 0 0 0-2 0v2.93A8 8 0 0 1 4.07 13H7a1 1 0 0 0 0-2H4.07A8 8 0 0 1 11 4.07V7a1 1 0 0 0 2 0V4.07A8 8 0 0 1 19.93 11H17a1 1 0 0 0 0 2h2.93A8 8 0 0 1 13 19.93z"/></svg>
        </div>
        <div>
          <h1 id="templeTitleDisplay" class="text-sm font-extrabold tracking-wide uppercase text-amber-900 dark:text-amber-200">Shri Mandir</h1>
          <p class="text-[10px] text-amber-700/80 dark:text-amber-400 font-medium">Parikrama • Pradakshina</p>
        </div>
      </div>

      <!-- Quick Action Controls -->
      <div class="flex items-center gap-1.5">
        <!-- Screen Awake Button -->
        <button id="wakeLockBtn" onclick="toggleWakeLock()" type="button" class="px-2 py-1 rounded-lg text-[11px] font-semibold border border-amber-300 dark:border-amber-800 bg-amber-50 dark:bg-stone-900 text-stone-700 dark:text-amber-200 flex items-center gap-1 transition">
          <span id="wakeLockIcon" class="w-2 h-2 rounded-full bg-stone-400"></span>
          <span id="wakeLockLabel">Awake: Off</span>
        </button>

        <!-- Sound Toggle -->
        <button id="soundToggleBtn" onclick="toggleSound()" type="button" class="w-8 h-8 rounded-lg border border-amber-300 dark:border-amber-800 bg-amber-50 dark:bg-stone-900 text-amber-800 dark:text-amber-300 flex items-center justify-center transition">
          <span id="soundIcon" class="text-xs font-bold">🔔</span>
        </button>

        <!-- Theme Toggle -->
        <button id="themeToggleBtn" onclick="toggleTheme()" type="button" class="w-8 h-8 rounded-lg border border-amber-300 dark:border-amber-800 bg-amber-50 dark:bg-stone-900 text-amber-800 dark:text-amber-300 flex items-center justify-center transition">
          <span id="themeIcon" class="text-xs">🌙</span>
        </button>
      </div>
    </header>

    <!-- TAB 1: PARIKRAMA ROUND COUNTER -->
    <main id="counterView" class="flex-1 flex flex-col justify-between py-2 transition-opacity duration-150">

      <!-- GPS Quick Status Ribbon -->
      <div id="gpsQuickStatus" class="bg-amber-100/70 dark:bg-[#1a1410] border border-amber-300/60 dark:border-amber-900/40 rounded-2xl p-2.5 shadow-xs flex items-center justify-between">
        <div class="flex items-center gap-2">
          <span class="relative flex h-3 w-3">
            <span id="gpsPulseDot" class="animate-ping absolute inline-flex h-full w-full rounded-full bg-stone-400 opacity-75"></span>
            <span id="gpsSolidDot" class="relative inline-flex rounded-full h-3 w-3 bg-stone-400"></span>
          </span>
          <div>
            <div class="flex items-center gap-1.5">
              <span id="gpsStateBadge" class="text-[11px] font-bold uppercase tracking-wider text-stone-700 dark:text-amber-200">GPS Standby</span>
              <span id="gpsArmedIndicator" class="hidden text-[9px] px-1.5 py-0.5 rounded-full font-bold bg-emerald-500/20 text-emerald-600 dark:text-emerald-400">ARMED</span>
            </div>
            <p id="gpsDistanceHint" class="text-[10px] text-stone-500 dark:text-amber-200/60 font-mono">Gate not set • Click GPS tab or button</p>
          </div>
        </div>
        <button id="quickSetGateBtn" onclick="setCurrentSpotAsGate()" type="button" class="px-2.5 py-1 bg-amber-500 hover:bg-amber-600 active:scale-95 text-stone-950 rounded-xl text-xs font-bold transition flex items-center gap-1 shadow-xs">
          📍 Set Gate
        </button>
      </div>

      <!-- GPS Troubleshooting / Permission Notice Banner -->
      <div id="gpsNoticeBanner" class="hidden my-1 p-2 bg-amber-50 dark:bg-stone-900 border border-amber-300/70 dark:border-amber-800 rounded-xl text-[11px] text-stone-700 dark:text-amber-200 flex items-center justify-between">
        <div class="flex items-center gap-1.5 flex-1 pr-2">
          <span id="gpsNoticeIcon">⚠️</span>
          <span id="gpsNoticeText">GPS waiting for fix...</span>
        </div>
        <button onclick="requestGpsPermissionDirectly()" type="button" class="px-2 py-0.5 bg-amber-500 text-stone-950 rounded-lg font-bold text-[10px] shrink-0">
          Retry / Fix
        </button>
      </div>

      <!-- Target Presets & Current Target Pill -->
      <div class="bg-white/80 dark:bg-[#1a1410] border border-amber-200/70 dark:border-amber-900/40 rounded-2xl p-2.5 shadow-sm mt-1">
        <div class="flex items-center justify-between mb-2">
          <span class="text-[11px] font-bold tracking-wider uppercase text-amber-800 dark:text-amber-300/80 flex items-center gap-1">
            🎯 Target Rounds:
          </span>
          <button id="customTargetBtn" onclick="openTargetModal()" type="button" class="text-[11px] font-semibold text-amber-600 dark:text-amber-400 hover:underline">
            Custom Target
          </button>
        </div>

        <!-- Sacred Presets (3, 7, 9, 11, 21, 108) -->
        <div class="grid grid-cols-6 gap-1" id="presetPillsContainer">
          <button type="button" onclick="setTargetRounds(3)" class="target-chip py-1.5 rounded-lg text-xs font-bold transition bg-amber-100 dark:bg-stone-800 text-stone-700 dark:text-amber-200" data-target="3">3</button>
          <button type="button" onclick="setTargetRounds(7)" class="target-chip py-1.5 rounded-lg text-xs font-bold transition bg-amber-100 dark:bg-stone-800 text-stone-700 dark:text-amber-200" data-target="7">7</button>
          <button type="button" onclick="setTargetRounds(9)" class="target-chip py-1.5 rounded-lg text-xs font-bold transition bg-amber-100 dark:bg-stone-800 text-stone-700 dark:text-amber-200" data-target="9">9</button>
          <button type="button" onclick="setTargetRounds(11)" class="target-chip py-1.5 rounded-lg text-xs font-bold transition bg-amber-100 dark:bg-stone-800 text-stone-700 dark:text-amber-200" data-target="11">11</button>
          <button type="button" onclick="setTargetRounds(21)" class="target-chip py-1.5 rounded-lg text-xs font-bold transition bg-amber-100 dark:bg-stone-800 text-stone-700 dark:text-amber-200" data-target="21">21</button>
          <button type="button" onclick="setTargetRounds(108)" class="target-chip py-1.5 rounded-lg text-xs font-bold transition bg-amber-500 text-stone-950 shadow-xs font-extrabold ring-2 ring-amber-400" data-target="108">108</button>
        </div>
      </div>

      <!-- MAIN PARIKRAMA WHEEL & TOUCH AREA -->
      <div class="relative flex flex-col items-center justify-center my-3">
        
        <!-- Interactive Circle Touch Zone -->
        <div id="parikramaTapZone" onclick="completeOneRound()" class="relative w-64 h-64 sm:w-72 sm:h-72 rounded-full cursor-pointer flex items-center justify-center transition transform active:scale-95 shadow-xl select-none bg-radial from-amber-50 to-amber-100/50 dark:from-stone-900 dark:to-[#1a1410] border border-amber-200/50 dark:border-amber-900/30">
          
          <!-- Outer Rotating Decorative Mandala Border -->
          <svg class="absolute inset-0 w-full h-full text-amber-500/15 dark:text-amber-400/10 animate-spin-slow pointer-events-none" viewBox="0 0 100 100">
            <circle cx="50" cy="50" r="48" fill="none" stroke="currentColor" stroke-width="1.2" stroke-dasharray="3, 3" />
            <circle cx="50" cy="50" r="42" fill="none" stroke="currentColor" stroke-width="0.8" />
            <circle cx="50" cy="50" r="36" fill="none" stroke="currentColor" stroke-width="0.5" stroke-dasharray="1, 4" />
          </svg>

          <!-- SVG Circular Progress Ring -->
          <svg class="absolute inset-0 w-full h-full -rotate-90 pointer-events-none" viewBox="0 0 100 100">
            <circle cx="50" cy="50" r="42" fill="none" stroke="currentColor" class="text-amber-200/50 dark:text-stone-800" stroke-width="5"/>
            <circle id="progressRing" cx="50" cy="50" r="42" fill="none" stroke="currentColor" class="text-amber-500 transition-all duration-500 stroke-cap-round" stroke-width="5" stroke-dasharray="263.89" stroke-dashoffset="263.89"/>
          </svg>

          <!-- Inner Round Numbers & Status -->
          <div class="relative z-10 flex flex-col items-center justify-center text-center pointer-events-none">
            <span class="text-xs uppercase tracking-widest font-extrabold text-amber-800 dark:text-amber-300">Round</span>
            <div id="currentCountDisplay" class="text-6xl sm:text-7xl font-black tracking-tight text-amber-950 dark:text-amber-100 font-mono my-0.5">0</div>
            <div class="flex items-center gap-1 text-xs text-amber-700/80 dark:text-amber-300/70 font-semibold">
              <span>of</span>
              <span id="targetRoundsDisplay" class="font-bold text-amber-900 dark:text-amber-200">108</span>
              <span>Rounds</span>
            </div>
            <span id="progressPercentageDisplay" class="text-[11px] font-mono mt-1 font-bold text-amber-600 dark:text-amber-400">0%</span>
            <span class="text-[10px] text-stone-600 dark:stone-300 mt-1 uppercase tracking-wider font-semibold">Tap to Count</span>
          </div>

          <!-- Touch Ripple Container -->
          <div id="rippleContainer" class="absolute inset-0 rounded-full overflow-hidden pointer-events-none"></div>
        </div>

        <!-- Completion Alert Banner -->
        <div id="completionBanner" class="hidden mt-3 px-4 py-2 bg-emerald-500 text-white rounded-xl shadow-md text-xs font-bold animate-bounce flex items-center gap-2">
          <span>✨</span> All Parikramas Complete! Dhanyavad & Pranam.
        </div>
      </div>

      <!-- STATS METRICS ROW -->
      <div class="grid grid-cols-3 gap-2 bg-white/90 dark:bg-[#1a1410] border border-amber-200/70 dark:border-amber-900/40 rounded-2xl p-2.5 shadow-sm text-center">
        <div>
          <span class="text-[10px] font-semibold text-stone-500 dark:text-amber-200/60 uppercase">Duration</span>
          <div id="sessionTimerDisplay" class="text-sm font-bold font-mono text-stone-800 dark:text-stone-100">00:00</div>
        </div>
        <div>
          <span class="text-[10px] font-semibold text-stone-500 dark:text-amber-200/60 uppercase">Last Lap</span>
          <div id="lastLapTimeDisplay" class="text-sm font-bold font-mono text-stone-800 dark:text-stone-100">--:--</div>
        </div>
        <div>
          <span class="text-[10px] font-semibold text-stone-500 dark:text-amber-200/60 uppercase">Avg Lap</span>
          <div id="avgLapTimeDisplay" class="text-sm font-bold font-mono text-stone-800 dark:text-stone-100">--:--</div>
        </div>
      </div>

      <!-- BOTTOM CONTROL BUTTONS -->
      <div class="flex items-center justify-between gap-2 mt-2">
        <button id="decrementBtn" onclick="decrementRound()" type="button" class="flex-1 py-2 rounded-xl text-xs font-bold border border-stone-300 dark:border-stone-700 bg-white dark:bg-stone-800 text-stone-700 dark:text-stone-200 active:scale-95 transition flex items-center justify-center gap-1">
          ↩ Undo -1
        </button>
        <button id="resetBtn" onclick="resetCurrentParikrama()" type="button" class="flex-1 py-2 rounded-xl text-xs font-bold border border-rose-200 dark:border-rose-900/40 bg-rose-50 dark:bg-rose-950/30 text-rose-700 dark:text-rose-300 active:scale-95 transition flex items-center justify-center gap-1">
          🔄 Reset
        </button>
        <button id="saveSessionBtn" onclick="saveParikramaSession()" type="button" class="flex-1 py-2 rounded-xl text-xs font-bold bg-amber-500 hover:bg-amber-600 text-stone-950 active:scale-95 transition flex items-center justify-center gap-1 shadow-xs">
          💾 Save
        </button>
      </div>

    </main>

    <!-- TAB 2: AUTO-GPS GATE MANAGER -->
    <section id="gpsView" class="hidden flex-1 flex flex-col justify-between py-2 transition-opacity duration-150">
      
      <!-- Radar Screen -->
      <div class="bg-white/90 dark:bg-[#1a1410] border border-amber-200/70 dark:border-amber-900/40 rounded-2xl p-4 shadow-sm flex flex-col items-center">
        <div class="flex items-center justify-between w-full mb-2">
          <h2 class="text-xs font-bold uppercase tracking-wider text-amber-900 dark:text-amber-300 flex items-center gap-1">
            📡 Virtual GPS Finish Line
          </h2>
          <span id="gpsSignalBadge" class="text-[10px] px-2 py-0.5 rounded-full font-mono font-bold bg-stone-100 dark:bg-stone-800 text-stone-600 dark:text-stone-300">Accuracy: --</span>
        </div>

        <!-- Radar Visualizer -->
        <div class="relative w-48 h-48 rounded-full border border-amber-300/40 dark:border-amber-700/30 bg-amber-50/40 dark:bg-stone-900 flex items-center justify-center my-2 overflow-hidden shadow-inner">
          <div class="absolute w-36 h-36 rounded-full border border-dashed border-amber-300/60 dark:border-amber-700/40"></div>
          <div class="absolute w-20 h-20 rounded-full border border-amber-400/80 dark:border-amber-600/60 bg-amber-500/10"></div>
          
          <!-- Crosshairs -->
          <div class="absolute w-full h-[1px] bg-amber-300/30"></div>
          <div class="absolute h-full w-[1px] bg-amber-300/30"></div>

          <!-- Gate Center Pin -->
          <div id="radarGatePin" class="absolute w-4 h-4 rounded-full bg-amber-500 ring-4 ring-amber-300/40 dark:ring-amber-800/60 flex items-center justify-center text-[8px] text-stone-950 font-bold z-10">
            ⛩
          </div>

          <!-- Dynamic Walker Blip -->
          <div id="radarUserBlip" class="absolute w-3 h-3 rounded-full bg-emerald-500 ring-2 ring-white dark:ring-stone-950 transition-all duration-300" style="transform: translate(0px, 0px);"></div>
        </div>

        <!-- Distance & Status Big Text -->
        <div class="text-center mt-2">
          <span class="text-[10px] uppercase font-semibold text-stone-500 dark:text-amber-200/60">Distance to Gate</span>
          <div id="gpsLiveDistance" class="text-3xl font-extrabold font-mono text-stone-900 dark:text-amber-100">-- m</div>
          <p id="gpsStateExplanation" class="text-[11px] text-stone-600 dark:text-amber-200/70 font-medium mt-0.5">Stand at starting spot and tap "Set Current Spot as Gate"</p>
          <div id="gpsRawCoords" class="text-[9px] text-stone-400 font-mono mt-0.5">Lat: -- | Lon: --</div>
        </div>

        <!-- Gate Action Buttons -->
        <div class="grid grid-cols-2 gap-2 w-full mt-4">
          <button id="setGateNowBtn" onclick="setCurrentSpotAsGate()" type="button" class="py-2.5 px-3 bg-amber-500 hover:bg-amber-600 active:scale-95 text-stone-950 rounded-xl text-xs font-bold transition flex items-center justify-center gap-1 shadow-sm">
            📍 Set Gate Here
          </button>
          <button id="clearGateBtn" onclick="clearGpsGate()" type="button" class="py-2.5 px-3 bg-stone-100 hover:bg-stone-200 dark:bg-stone-800 dark:hover:bg-stone-700 text-stone-700 dark:text-stone-200 rounded-xl text-xs font-bold transition flex items-center justify-center gap-1">
            🗑 Clear Gate
          </button>
        </div>
      </div>

      <!-- Indoor Motion / Step Backup Option -->
      <div class="bg-white/90 dark:bg-[#1a1410] border border-amber-200/70 dark:border-amber-900/40 rounded-2xl p-3 shadow-sm space-y-2 mt-2">
        <div class="flex items-center justify-between">
          <div>
            <h3 class="text-xs font-bold uppercase tracking-wider text-stone-700 dark:text-amber-300">Indoor Sanctuary Backup</h3>
            <p class="text-[10px] text-stone-500 dark:text-amber-200/60">Use motion/step detector if GPS has weak stone roof signal</p>
          </div>
          <button id="pedometerToggleBtn" onclick="togglePedometerMode()" type="button" class="px-2.5 py-1 text-xs font-bold rounded-xl border border-amber-400 bg-amber-50 dark:bg-stone-900 text-amber-800 dark:text-amber-300">
            Steps: Off
          </button>
        </div>

        <div id="pedometerControls" class="hidden pt-2 border-t border-amber-100 dark:border-stone-800 flex items-center justify-between text-xs">
          <span class="text-stone-600 dark:text-amber-200/70">Steps per round:</span>
          <div class="flex items-center gap-2">
            <input id="stepsPerRoundInput" type="number" min="20" max="1000" value="85" onchange="updatePedometerGoal()" class="w-16 px-2 py-1 rounded-lg border border-stone-300 dark:border-stone-700 bg-transparent text-center font-mono font-bold">
            <span id="currentStepCount" class="font-mono font-bold text-amber-600 dark:text-amber-400">(0 steps)</span>
          </div>
        </div>
      </div>

      <!-- Sensitivity & Radius Settings -->
      <div class="bg-white/90 dark:bg-[#1a1410] border border-amber-200/70 dark:border-amber-900/40 rounded-2xl p-3 shadow-sm space-y-3 mt-2">
        <h3 class="text-xs font-bold uppercase tracking-wider text-stone-700 dark:text-amber-300">Gate Dimensions</h3>
        
        <div>
          <div class="flex justify-between text-xs mb-1">
            <span class="text-stone-600 dark:text-amber-200/70">Finish Line Trigger Radius</span>
            <span id="triggerRadiusVal" class="font-mono font-bold text-amber-600 dark:text-amber-400">7 m</span>
          </div>
          <input id="triggerRadiusSlider" oninput="updateGpsSettings()" type="range" min="3" max="15" value="7" class="w-full accent-amber-500">
        </div>

        <div>
          <div class="flex justify-between text-xs mb-1">
            <span class="text-stone-600 dark:text-amber-200/70">Arming Distance (Must Walk Beyond)</span>
            <span id="armingDistanceVal" class="font-mono font-bold text-amber-600 dark:text-amber-400">14 m</span>
          </div>
          <input id="armingDistanceSlider" oninput="updateGpsSettings()" type="range" min="8" max="30" value="14" class="w-full accent-amber-500">
        </div>

        <!-- Simulation Tool for indoor testing -->
        <div class="pt-2 border-t border-amber-100 dark:border-stone-800 flex items-center justify-between">
          <span class="text-xs text-stone-500 dark:text-stone-400">Test Indoors (Desktop/Home):</span>
          <button id="toggleSimBtn" onclick="runSimulatedParikrama()" type="button" class="px-2.5 py-1 text-xs font-bold bg-amber-100 hover:bg-amber-200 dark:bg-stone-800 text-amber-800 dark:text-amber-300 rounded-lg">
            ▶ Simulate 1 Round
          </button>
        </div>
      </div>
    </section>

    <!-- TAB 3: PARIKRAMA JOURNAL & HISTORY -->
    <section id="historyView" class="hidden flex-1 flex flex-col justify-between py-2 transition-opacity duration-150">
      <div class="bg-white/90 dark:bg-[#1a1410] border border-amber-200/70 dark:border-amber-900/40 rounded-2xl p-3 shadow-sm flex-1 flex flex-col overflow-hidden">
        
        <div class="flex items-center justify-between pb-2 border-b border-amber-100 dark:border-stone-800 mb-2">
          <h2 class="text-xs font-bold uppercase tracking-wider text-amber-900 dark:text-amber-300 flex items-center gap-1">
            📖 Parikrama Journal
          </h2>
          <div class="flex items-center gap-2">
            <button id="exportCsvBtn" onclick="exportHistoryCsv()" type="button" class="text-[11px] font-bold text-amber-600 hover:underline">
              Export CSV
            </button>
            <button id="clearHistoryBtn" onclick="clearAllHistory()" type="button" class="text-[11px] font-bold text-rose-500 hover:underline">
              Clear
            </button>
          </div>
        </div>

        <!-- Total Stats summary pill -->
        <div class="grid grid-cols-2 gap-2 mb-3 bg-amber-50/60 dark:bg-stone-900 p-2 rounded-xl text-center">
          <div>
            <span class="text-[10px] text-stone-500 dark:text-amber-200/60 uppercase">Lifetime Rounds</span>
            <div id="lifetimeRoundsDisplay" class="text-base font-extrabold font-mono text-amber-900 dark:text-amber-200">0</div>
          </div>
          <div>
            <span class="text-[10px] text-stone-500 dark:text-amber-200/60 uppercase">Total Sessions</span>
            <div id="lifetimeSessionsDisplay" class="text-base font-extrabold font-mono text-amber-900 dark:text-amber-200">0</div>
          </div>
        </div>

        <!-- Scrollable History List -->
        <div id="historyListContainer" class="flex-1 overflow-y-auto space-y-2 pr-1 custom-scroll text-xs">
          <div class="text-center py-8 text-stone-400">No parikrama sessions recorded yet. Complete a round and tap "Save".</div>
        </div>
      </div>
    </section>

    <!-- BOTTOM TAB NAVIGATION BAR -->
    <nav class="fixed bottom-0 left-0 right-0 max-w-md mx-auto bg-white/95 dark:bg-[#150f0c]/95 backdrop-blur-md border-t border-amber-200/70 dark:border-amber-900/40 px-6 py-2 flex items-center justify-between z-30 shadow-lg">
      <button onclick="switchTab('counter')" id="tabBtnCounter" type="button" class="flex flex-col items-center gap-0.5 text-amber-600 dark:text-amber-400 font-bold transition">
        <span class="text-base">⭕</span>
        <span class="text-[10px] tracking-tight">Counter</span>
      </button>

      <button onclick="switchTab('gps')" id="tabBtnGps" type="button" class="flex flex-col items-center gap-0.5 text-stone-400 hover:text-amber-600 dark:hover:text-amber-400 font-medium transition">
        <span class="text-base">📍</span>
        <span class="text-[10px] tracking-tight">GPS Gate</span>
      </button>

      <button onclick="switchTab('history')" id="tabBtnHistory" type="button" class="flex flex-col items-center gap-0.5 text-stone-400 hover:text-amber-600 dark:hover:text-amber-400 font-medium transition">
        <span class="text-base">📜</span>
        <span class="text-[10px] tracking-tight">Journal</span>
      </button>
    </nav>

  </div>

  <!-- CUSTOM TARGET MODAL -->
  <div id="customTargetModal" class="hidden fixed inset-0 bg-black/60 backdrop-blur-xs flex items-center justify-center p-4 z-50">
    <div class="bg-white dark:bg-stone-900 border border-amber-200 dark:border-amber-800 rounded-3xl p-5 w-full max-w-xs shadow-2xl space-y-4">
      <h3 class="text-sm font-bold text-amber-900 dark:text-amber-200 uppercase tracking-wider">Set Parikrama Goal</h3>
      
      <div>
        <label class="block text-xs font-semibold text-stone-600 dark:text-stone-300 mb-1">Temple / Shrine Name</label>
        <input id="modalTempleNameInput" type="text" placeholder="e.g. Tirupati, Somnath, Home Mandir" class="w-full text-xs px-3 py-2 rounded-xl border border-stone-300 dark:border-stone-700 bg-transparent text-stone-900 dark:text-stone-100">
      </div>

      <div>
        <label class="block text-xs font-semibold text-stone-600 dark:text-stone-300 mb-1">Target Rounds</label>
        <input id="modalTargetInput" type="number" min="1" max="10008" placeholder="108" class="w-full text-xs px-3 py-2 rounded-xl border border-stone-300 dark:border-stone-700 bg-transparent text-stone-900 dark:text-stone-100 font-mono">
      </div>

      <div class="flex gap-2 pt-2">
        <button onclick="closeTargetModal()" type="button" class="flex-1 py-2 text-xs font-bold rounded-xl border border-stone-300 dark:border-stone-700 text-stone-600 dark:text-stone-300">
          Cancel
        </button>
        <button onclick="saveCustomTargetModal()" type="button" class="flex-1 py-2 text-xs font-bold rounded-xl bg-amber-500 hover:bg-amber-600 text-stone-950">
          Set Goal
        </button>
      </div>
    </div>
  </div>

  <!-- TOAST NOTIFICATION CONTAINER -->
  <div id="toastMessage" class="pointer-events-none fixed top-5 max-w-xs px-4 py-2 bg-stone-900/90 text-amber-200 text-xs font-semibold rounded-2xl shadow-xl transition-all duration-300 opacity-0 -translate-y-4 z-50 flex items-center gap-2">
    <span>🛈</span> <span id="toastText">Action completed</span>
  </div>

  <script>
    // Register Service Worker for PWA
    if ('serviceWorker' in navigator) {
      window.addEventListener('load', () => {
        navigator.serviceWorker.register('./sw.js')
          .then((reg) => console.log('ServiceWorker registered:', reg.scope))
          .catch((err) => console.warn('ServiceWorker registration failed:', err));
      });
    }

    // CORE APPLICATION STATE
    const appState = {
      count: 0,
      targetRounds: 108,
      templeName: 'Shri Mandir',
      soundEnabled: true,
      hapticEnabled: true,
      startTime: null,
      timerInterval: null,
      elapsedSeconds: 0,
      lapTimes: [],
      lastLapTimestamp: null,
      wakeLockSentinel: null,
      gps: {
        active: false,
        watchId: null,
        gateLat: null,
        gateLon: null,
        userLat: null,
        userLon: null,
        accuracy: null,
        triggerRadius: 7,
        armingDistance: 14,
        state: 'STANDBY',
        lastDistance: null,
        cooldownTimer: null,
        simInterval: null,
        errorCode: null
      },
      pedometer: {
        active: false,
        stepsPerRound: 85,
        currentSteps: 0,
        lastMag: 0,
        listenerBound: false
      },
      history: []
    };

    // Load persisted state
    try {
      const saved = localStorage.getItem('parikrama_pulse_state');
      if (saved) {
        const parsed = JSON.parse(saved);
        appState.targetRounds = parsed.targetRounds || 108;
        appState.templeName = parsed.templeName || 'Shri Mandir';
        appState.soundEnabled = parsed.soundEnabled ?? true;
        if (parsed.gpsGate) {
          appState.gps.gateLat = parsed.gpsGate.lat;
          appState.gps.gateLon = parsed.gpsGate.lon;
          appState.gps.state = 'AT_GATE';
        }
      }
      const savedHistory = localStorage.getItem('parikrama_pulse_history');
      if (savedHistory) {
        appState.history = JSON.parse(savedHistory);
      }
    } catch(e) {
      console.warn("Could not load local storage", e);
    }

    function saveState() {
      try {
        localStorage.setItem('parikrama_pulse_state', JSON.stringify({
          targetRounds: appState.targetRounds,
          templeName: appState.templeName,
          soundEnabled: appState.soundEnabled,
          gpsGate: appState.gps.gateLat ? { lat: appState.gps.gateLat, lon: appState.gps.gateLon } : null
        }));
        localStorage.setItem('parikrama_pulse_history', JSON.stringify(appState.history));
      } catch (err) {
        console.warn("Storage write error", err);
      }
    }

    // AUDIO SYNTHESIZER (Resilient Web Audio API)
    let audioCtx = null;
    function getAudioContext() {
      if (!audioCtx) {
        const AudioContextClass = window.AudioContext || window.webkitAudioContext;
        if (AudioContextClass) {
          audioCtx = new AudioContextClass();
        }
      }
      if (audioCtx && audioCtx.state === 'suspended') {
        audioCtx.resume();
      }
      return audioCtx;
    }

    function playBellSound(isGoal = false) {
      if (!appState.soundEnabled) return;
      try {
        const ctx = getAudioContext();
        if (!ctx) return;

        const fundamental = isGoal ? 440 : 523.25;
        const partials = [1, 2.76, 5.4, 8.93];

        partials.forEach((mult, index) => {
          const osc = ctx.createOscillator();
          const gain = ctx.createGain();

          osc.type = 'sine';
          osc.frequency.setValueAtTime(fundamental * mult, ctx.currentTime);

          const decay = (isGoal ? 3.0 : 1.8) / (index + 1);
          gain.gain.setValueAtTime(0.25 / (index + 1), ctx.currentTime);
          gain.gain.exponentialRampToValueAtTime(0.0001, ctx.currentTime + decay);

          osc.connect(gain);
          gain.connect(ctx.destination);

          osc.start();
          osc.stop(ctx.currentTime + decay);
        });
      } catch (e) {
        console.warn("Audio bell play failure", e);
      }
    }

    function triggerHaptic(type = 'tap') {
      if (!appState.hapticEnabled || !navigator.vibrate) return;
      try {
        if (type === 'tap') navigator.vibrate(35);
        else if (type === 'lap') navigator.vibrate([60, 40, 80]);
        else if (type === 'goal') navigator.vibrate([100, 50, 100, 50, 200]);
      } catch(e) {}
    }

    // ROUND COUNTING LOGIC
    function startWalkTimer() {
      if (!appState.startTime) {
        appState.startTime = Date.now() - (appState.elapsedSeconds * 1000);
        appState.lastLapTimestamp = Date.now();
        clearInterval(appState.timerInterval);
        appState.timerInterval = setInterval(() => {
          appState.elapsedSeconds = Math.floor((Date.now() - appState.startTime) / 1000);
          updateTimerDisplay();
        }, 1000);
      }
    }

    function updateTimerDisplay() {
      const el = document.getElementById('sessionTimerDisplay');
      if (el) el.textContent = formatDuration(appState.elapsedSeconds);
    }

    function completeOneRound(source = 'manual') {
      startWalkTimer();

      const now = Date.now();
      if (appState.lastLapTimestamp) {
        const lapDur = Math.round((now - appState.lastLapTimestamp) / 1000);
        if (lapDur > 0) appState.lapTimes.push(lapDur);
      }
      appState.lastLapTimestamp = now;

      appState.count += 1;
      const isGoal = appState.count >= appState.targetRounds;

      playBellSound(isGoal);
      triggerHaptic(isGoal ? 'goal' : 'lap');
      renderCounter();

      if (source === 'gps') {
        showToast(`Auto-Lap Recorded! Round #${appState.count}`);
      }
    }

    function decrementRound() {
      if (appState.count > 0) {
        appState.count -= 1;
        if (appState.lapTimes.length > 0) appState.lapTimes.pop();
        triggerHaptic('tap');
        renderCounter();
      }
    }

    function resetCurrentParikrama() {
      appState.count = 0;
      appState.elapsedSeconds = 0;
      appState.startTime = null;
      appState.lastLapTimestamp = null;
      appState.lapTimes = [];
      clearInterval(appState.timerInterval);
      appState.timerInterval = null;
      updateTimerDisplay();
      renderCounter();
      showToast("Parikrama reset");
    }

    function setTargetRounds(val) {
      val = parseInt(val, 10);
      if (!isNaN(val) && val > 0) {
        appState.targetRounds = val;
        saveState();
        renderCounter();
        triggerHaptic('tap');
        showToast(`Target set to ${val} rounds`);
      }
    }

    function renderCounter() {
      const countEl = document.getElementById('currentCountDisplay');
      if (countEl) countEl.textContent = appState.count;

      const targetEl = document.getElementById('targetRoundsDisplay');
      if (targetEl) targetEl.textContent = appState.targetRounds;

      const titleEl = document.getElementById('templeTitleDisplay');
      if (titleEl) titleEl.textContent = appState.templeName;

      const radius = 42;
      const circumference = 2 * Math.PI * radius;
      const progressRatio = Math.min(appState.count / (appState.targetRounds || 1), 1);
      const ring = document.getElementById('progressRing');
      if (ring) {
        ring.style.strokeDashoffset = circumference - (circumference * progressRatio);
      }

      const percentEl = document.getElementById('progressPercentageDisplay');
      if (percentEl) {
        percentEl.textContent = `${Math.round(progressRatio * 100)}%`;
      }

      const banner = document.getElementById('completionBanner');
      if (banner) {
        if (appState.count >= appState.targetRounds && appState.targetRounds > 0) {
          banner.classList.remove('hidden');
        } else {
          banner.classList.add('hidden');
        }
      }

      const lastLapEl = document.getElementById('lastLapTimeDisplay');
      const avgLapEl = document.getElementById('avgLapTimeDisplay');
      if (appState.lapTimes.length > 0) {
        const last = appState.lapTimes[appState.lapTimes.length - 1];
        if (lastLapEl) lastLapEl.textContent = formatDuration(last);
        const sum = appState.lapTimes.reduce((a, b) => a + b, 0);
        const avg = Math.round(sum / appState.lapTimes.length);
        if (avgLapEl) avgLapEl.textContent = formatDuration(avg);
      } else {
        if (lastLapEl) lastLapEl.textContent = '--:--';
        if (avgLapEl) avgLapEl.textContent = '--:--';
      }

      const chips = document.querySelectorAll('.target-chip');
      chips.forEach(chip => {
        const tVal = parseInt(chip.getAttribute('data-target'), 10);
        if (tVal === appState.targetRounds) {
          chip.className = 'target-chip py-1.5 rounded-lg text-xs font-bold transition bg-amber-500 text-stone-950 shadow-xs font-extrabold ring-2 ring-amber-400';
        } else {
          chip.className = 'target-chip py-1.5 rounded-lg text-xs font-bold transition bg-amber-100 dark:bg-stone-800 text-stone-700 dark:text-amber-200';
        }
      });
    }

    // MODAL DIALOGS
    function openTargetModal() {
      const modal = document.getElementById('customTargetModal');
      const nameInput = document.getElementById('modalTempleNameInput');
      const targetInput = document.getElementById('modalTargetInput');
      if (nameInput) nameInput.value = appState.templeName;
      if (targetInput) targetInput.value = appState.targetRounds;
      if (modal) modal.classList.remove('hidden');
    }

    function closeTargetModal() {
      const modal = document.getElementById('customTargetModal');
      if (modal) modal.classList.add('hidden');
    }

    function saveCustomTargetModal() {
      const nameInput = document.getElementById('modalTempleNameInput');
      const targetInput = document.getElementById('modalTargetInput');
      if (nameInput && nameInput.value.trim()) {
        appState.templeName = nameInput.value.trim();
      }
      if (targetInput) {
        const val = parseInt(targetInput.value, 10);
        if (!isNaN(val) && val > 0) {
          appState.targetRounds = val;
        }
      }
      saveState();
      renderCounter();
      closeTargetModal();
      showToast("Settings updated");
    }

    // GPS GEOFENCING LOGIC
    function initGpsTracking() {
      if (!('geolocation' in navigator)) {
        showGpsBanner("Geolocation is not supported by this browser.", "error");
        return;
      }

      if (window.self !== window.top) {
        showGpsBanner("Embedded preview detected. If GPS doesn't lock, use 'Simulate 1 Round' or open in external browser window.", "info");
      }

      if (appState.gps.watchId !== null) return;

      appState.gps.active = true;

      navigator.geolocation.getCurrentPosition(
        (pos) => {
          handleGpsPosition(pos.coords);
          hideGpsBanner();
        },
        (err) => handleGpsError(err),
        { enableHighAccuracy: false, maximumAge: 60000, timeout: 15000 }
      );

      try {
        appState.gps.watchId = navigator.geolocation.watchPosition(
          (pos) => {
            handleGpsPosition(pos.coords);
            hideGpsBanner();
          },
          (err) => handleGpsError(err),
          { enableHighAccuracy: true, maximumAge: 3000, timeout: 20000 }
        );
      } catch (e) {
        handleGpsError({ code: -1, message: e.message });
      }
    }

    function handleGpsError(err) {
      appState.gps.errorCode = err.code;
      const accuracyEl = document.getElementById('gpsSignalBadge');
      const pulseDot = document.getElementById('gpsPulseDot');
      const solidDot = document.getElementById('gpsSolidDot');

      if (pulseDot) pulseDot.className = "animate-ping absolute inline-flex h-full w-full rounded-full bg-rose-400 opacity-75";
      if (solidDot) solidDot.className = "relative inline-flex rounded-full h-3 w-3 bg-rose-500";

      let errorMsg = "Unable to get GPS signal.";
      if (err.code === 1) {
        errorMsg = "Location permission denied. Please allow location access in your browser/app settings.";
        if (accuracyEl) accuracyEl.textContent = "Signal: Denied";
      } else if (err.code === 2) {
        errorMsg = "GPS satellite lock unavailable. Make sure Location / GPS is turned ON in your phone's status bar.";
        if (accuracyEl) accuracyEl.textContent = "Signal: No Fix";
      } else if (err.code === 3) {
        errorMsg = "GPS request timed out. Searching for satellites (stand in open sky or test with Simulate button).";
        if (accuracyEl) accuracyEl.textContent = "Signal: Searching...";
      } else {
        errorMsg = err.message || "GPS unavailable. Turn on location permissions.";
        if (accuracyEl) accuracyEl.textContent = "Signal: Error";
      }

      showGpsBanner(errorMsg, "error");
    }

    function showGpsBanner(msg, type = "error") {
      const banner = document.getElementById('gpsNoticeBanner');
      const text = document.getElementById('gpsNoticeText');
      const icon = document.getElementById('gpsNoticeIcon');
      if (banner && text) {
        text.textContent = msg;
        icon.textContent = type === "error" ? "⚠️" : "ℹ️";
        banner.classList.remove('hidden');
      }
    }

    function hideGpsBanner() {
      const banner = document.getElementById('gpsNoticeBanner');
      if (banner) banner.classList.add('hidden');
    }

    function requestGpsPermissionDirectly() {
      if (navigator.permissions && navigator.permissions.query) {
        navigator.permissions.query({ name: 'geolocation' }).then((result) => {
          if (result.state === 'denied') {
            showToast("Open browser settings > Site Settings > Location > Allow");
          }
        }).catch(() => {});
      }
      if (appState.gps.watchId !== null) {
        navigator.geolocation.clearWatch(appState.gps.watchId);
        appState.gps.watchId = null;
      }
      initGpsTracking();
      showToast("Requesting GPS signal...");
    }

    function calculateDistance(lat1, lon1, lat2, lon2) {
      const R = 6371e3;
      const φ1 = lat1 * Math.PI / 180;
      const φ2 = lat2 * Math.PI / 180;
      const Δφ = (lat2 - lat1) * Math.PI / 180;
      const Δλ = (lon2 - lon1) * Math.PI / 180;
      const a = Math.sin(Δφ/2) * Math.sin(Δφ/2) +
                Math.cos(φ1) * Math.cos(φ2) *
                Math.sin(Δλ/2) * Math.sin(Δλ/2);
      const c = 2 * Math.atan2(Math.sqrt(a), Math.sqrt(1-a));
      return R * c;
    }

    function handleGpsPosition(coords) {
      appState.gps.userLat = coords.latitude;
      appState.gps.userLon = coords.longitude;
      appState.gps.accuracy = coords.accuracy;

      const accuracyEl = document.getElementById('gpsSignalBadge');
      if (accuracyEl) {
        accuracyEl.textContent = `Accuracy: ±${Math.round(coords.accuracy)}m`;
      }

      const rawCoords = document.getElementById('gpsRawCoords');
      if (rawCoords) {
        rawCoords.textContent = `Lat: ${coords.latitude.toFixed(5)} | Lon: ${coords.longitude.toFixed(5)}`;
      }

      const pulseDot = document.getElementById('gpsPulseDot');
      const solidDot = document.getElementById('gpsSolidDot');
      if (pulseDot) pulseDot.className = "animate-ping absolute inline-flex h-full w-full rounded-full bg-emerald-400 opacity-75";
      if (solidDot) solidDot.className = "relative inline-flex rounded-full h-3 w-3 bg-emerald-500";

      if (appState.gps.gateLat === null || appState.gps.gateLon === null) {
        updateGpsUi(null);
        return;
      }

      const dist = calculateDistance(coords.latitude, coords.longitude, appState.gps.gateLat, appState.gps.gateLon);
      appState.gps.lastDistance = dist;
      processGpsState(dist);
      updateGpsUi(dist);
    }

    function processGpsState(dist) {
      const { triggerRadius, armingDistance, state } = appState.gps;

      if (state === 'STANDBY' || state === 'AT_GATE') {
        if (dist > triggerRadius) {
          appState.gps.state = 'WALKING_AWAY';
        }
      } else if (state === 'WALKING_AWAY') {
        if (dist >= armingDistance) {
          appState.gps.state = 'ARMED';
          triggerHaptic('tap');
        }
      } else if (state === 'ARMED') {
        if (dist <= triggerRadius) {
          appState.gps.state = 'COOLDOWN';
          completeOneRound('gps');
          clearTimeout(appState.gps.cooldownTimer);
          appState.gps.cooldownTimer = setTimeout(() => {
            appState.gps.state = 'AT_GATE';
            updateGpsUi(appState.gps.lastDistance);
          }, 8000);
        }
      }
    }

    function updateGpsUi(dist) {
      const distEl = document.getElementById('gpsLiveDistance');
      const hintEl = document.getElementById('gpsDistanceHint');
      const badgeEl = document.getElementById('gpsStateBadge');
      const armedBadge = document.getElementById('gpsArmedIndicator');
      const blip = document.getElementById('radarUserBlip');
      const explEl = document.getElementById('gpsStateExplanation');

      if (dist === null) {
        if (distEl) distEl.textContent = "-- m";
        if (hintEl) hintEl.textContent = "Gate not set • Click Set Gate";
        if (badgeEl) badgeEl.textContent = "GPS Standby";
        if (armedBadge) armedBadge.classList.add('hidden');
        if (explEl) explEl.textContent = "Stand at starting spot and tap 'Set Gate Here'";
        return;
      }

      const roundedDist = Math.round(dist);
      if (distEl) distEl.textContent = `${roundedDist} m`;
      if (hintEl) hintEl.textContent = `Gate: ${roundedDist}m away (${appState.gps.state})`;

      if (blip) {
        const maxRadarDist = appState.gps.armingDistance * 1.5;
        const ratio = Math.min(dist / maxRadarDist, 1);
        const pixelOffset = ratio * 70;
        blip.style.transform = `translate(${pixelOffset}px, 0px)`;
      }

      if (appState.gps.state === 'ARMED') {
        if (badgeEl) badgeEl.textContent = "ARMED • Cross Gate";
        if (armedBadge) armedBadge.classList.remove('hidden');
        if (explEl) explEl.textContent = `Walk back within ${appState.gps.triggerRadius}m to trigger round!`;
      } else if (appState.gps.state === 'WALKING_AWAY') {
        if (badgeEl) badgeEl.textContent = "Walking Out";
        if (armedBadge) armedBadge.classList.add('hidden');
        if (explEl) explEl.textContent = `Walk past ${appState.gps.armingDistance}m to arm round`;
      } else if (appState.gps.state === 'COOLDOWN') {
        if (badgeEl) badgeEl.textContent = "Round Registered!";
        if (armedBadge) armedBadge.classList.add('hidden');
        if (explEl) explEl.textContent = "Round recorded! Beginning next lap...";
      } else {
        if (badgeEl) badgeEl.textContent = "At Gate";
        if (armedBadge) armedBadge.classList.add('hidden');
        if (explEl) explEl.textContent = "Walk around the shrine to begin pradakshina";
      }
    }

    function setCurrentSpotAsGate() {
      initGpsTracking();

      if (appState.gps.userLat !== null && appState.gps.userLon !== null) {
        appState.gps.gateLat = appState.gps.userLat;
        appState.gps.gateLon = appState.gps.userLon;
        appState.gps.state = 'AT_GATE';
        saveState();
        updateGpsUi(0);
        triggerHaptic('tap');
        showToast("📍 Starting Gate memorized!");
        return;
      }

      if (!('geolocation' in navigator)) {
        showToast("Geolocation not available on this browser");
        return;
      }

      showToast("Acquiring GPS coordinates...");

      navigator.geolocation.getCurrentPosition(
        (pos) => {
          appState.gps.gateLat = pos.coords.latitude;
          appState.gps.gateLon = pos.coords.longitude;
          appState.gps.userLat = pos.coords.latitude;
          appState.gps.userLon = pos.coords.longitude;
          appState.gps.state = 'AT_GATE';
          saveState();
          updateGpsUi(0);
          triggerHaptic('tap');
          hideGpsBanner();
          showToast("📍 Starting Gate memorized!");
        },
        (err) => {
          handleGpsError(err);
          if (confirm("Could not get GPS fix. Would you like to set a simulated gate at your approximate location for testing?")) {
            appState.gps.gateLat = 16.5062;
            appState.gps.gateLon = 80.6480;
            appState.gps.state = 'AT_GATE';
            saveState();
            updateGpsUi(0);
            showToast("Simulated Gate set. Use 'Simulate 1 Round' to test!");
          }
        },
        { enableHighAccuracy: false, timeout: 12000, maximumAge: 30000 }
      );
    }

    function clearGpsGate() {
      appState.gps.gateLat = null;
      appState.gps.gateLon = null;
      appState.gps.state = 'STANDBY';
      saveState();
      updateGpsUi(null);
      showToast("GPS Gate Cleared");
    }

    // INDOOR STEP / MOTION PEDOMETER FALLBACK
    function togglePedometerMode() {
      appState.pedometer.active = !appState.pedometer.active;
      const btn = document.getElementById('pedometerToggleBtn');
      const ctrls = document.getElementById('pedometerControls');

      if (appState.pedometer.active) {
        if (btn) {
          btn.textContent = "Steps: On";
          btn.className = "px-2.5 py-1 text-xs font-bold rounded-xl border border-emerald-500 bg-emerald-50 dark:bg-emerald-950/40 text-emerald-700 dark:text-emerald-300";
        }
        if (ctrls) ctrls.classList.remove('hidden');
        startPedometerListener();
        showToast("Motion Step Counter Active");
      } else {
        if (btn) {
          btn.textContent = "Steps: Off";
          btn.className = "px-2.5 py-1 text-xs font-bold rounded-xl border border-amber-400 bg-amber-50 dark:bg-stone-900 text-amber-800 dark:text-amber-300";
        }
        if (ctrls) ctrls.classList.add('hidden');
        showToast("Step Counter Deactivated");
      }
    }

    function startPedometerListener() {
      if (appState.pedometer.listenerBound) return;

      if (typeof DeviceMotionEvent !== 'undefined' && typeof DeviceMotionEvent.requestPermission === 'function') {
        DeviceMotionEvent.requestPermission().then(response => {
          if (response === 'granted') {
            bindMotionSensor();
          } else {
            showToast("Motion permission denied");
          }
        }).catch(() => bindMotionSensor());
      } else {
        bindMotionSensor();
      }
    }

    function bindMotionSensor() {
      appState.pedometer.listenerBound = true;
      let lastStepTime = 0;

      window.addEventListener('devicemotion', (e) => {
        if (!appState.pedometer.active) return;
        const acc = e.accelerationIncludingGravity || e.acceleration;
        if (!acc) return;

        const x = acc.x || 0;
        const y = acc.y || 0;
        const z = acc.z || 0;
        const mag = Math.sqrt(x*x + y*y + z*z);
        const delta = Math.abs(mag - appState.pedometer.lastMag);
        appState.pedometer.lastMag = mag;

        const now = Date.now();
        if (delta > 3.2 && (now - lastStepTime) > 330) {
          lastStepTime = now;
          appState.pedometer.currentSteps += 1;
          
          const stepEl = document.getElementById('currentStepCount');
          if (stepEl) stepEl.textContent = `(${appState.pedometer.currentSteps} steps)`;

          if (appState.pedometer.currentSteps >= appState.pedometer.stepsPerRound) {
            appState.pedometer.currentSteps = 0;
            completeOneRound('gps');
            showToast(`Auto-Lap! Completed ${appState.pedometer.stepsPerRound} steps round.`);
          }
        }
      });
    }

    function updatePedometerGoal() {
      const input = document.getElementById('stepsPerRoundInput');
      if (input) {
        const val = parseInt(input.value, 10);
        if (!isNaN(val) && val >= 10) {
          appState.pedometer.stepsPerRound = val;
          showToast(`Pradakshina set to ${val} steps/round`);
        }
      }
    }

    function updateGpsSettings() {
      const trSlider = document.getElementById('triggerRadiusSlider');
      const armSlider = document.getElementById('armingDistanceSlider');
      if (trSlider) {
        appState.gps.triggerRadius = parseInt(trSlider.value, 10);
        const trVal = document.getElementById('triggerRadiusVal');
        if (trVal) trVal.textContent = `${appState.gps.triggerRadius} m`;
      }
      if (armSlider) {
        appState.gps.armingDistance = parseInt(armSlider.value, 10);
        const armVal = document.getElementById('armingDistanceVal');
        if (armVal) armVal.textContent = `${appState.gps.armingDistance} m`;
      }
    }

    function runSimulatedParikrama() {
      let simDistance = 0;
      let phase = 'out';
      showToast("Starting 1-Round simulation...");
      clearInterval(appState.gps.simInterval);

      appState.gps.simInterval = setInterval(() => {
        if (phase === 'out') {
          simDistance += 3;
          if (simDistance >= appState.gps.armingDistance + 4) {
            phase = 'return';
          }
        } else {
          simDistance -= 3;
          if (simDistance <= 2) {
            clearInterval(appState.gps.simInterval);
            processGpsState(1);
            updateGpsUi(1);
            return;
          }
        }
        processGpsState(simDistance);
        updateGpsUi(simDistance);
      }, 500);
    }

    // JOURNAL & SESSIONS
    function saveParikramaSession() {
      if (appState.count === 0) {
        showToast("Complete at least 1 round to save");
        return;
      }

      const session = {
        id: Date.now(),
        date: new Date().toLocaleDateString(undefined, { month: 'short', day: 'numeric', year: 'numeric' }),
        time: new Date().toLocaleTimeString(undefined, { hour: '2-digit', minute: '2-digit' }),
        temple: appState.templeName,
        rounds: appState.count,
        target: appState.targetRounds,
        durationSeconds: appState.elapsedSeconds,
        avgLap: appState.lapTimes.length > 0 ? Math.round(appState.lapTimes.reduce((a, b) => a + b, 0) / appState.lapTimes.length) : 0
      };

      appState.history.unshift(session);
      saveState();
      renderHistory();
      showToast("🙏 Parikrama session saved!");
      resetCurrentParikrama();
    }

    function renderHistory() {
      const container = document.getElementById('historyListContainer');
      const lifetimeRoundsEl = document.getElementById('lifetimeRoundsDisplay');
      const lifetimeSessionsEl = document.getElementById('lifetimeSessionsDisplay');

      const totalRounds = appState.history.reduce((sum, item) => sum + (item.rounds || 0), 0);
      if (lifetimeRoundsEl) lifetimeRoundsEl.textContent = totalRounds;
      if (lifetimeSessionsEl) lifetimeSessionsEl.textContent = appState.history.length;

      if (!container) return;
      if (appState.history.length === 0) {
        container.innerHTML = '<div class="text-center py-8 text-stone-400">No parikrama sessions recorded yet. Complete a round and tap "Save".</div>';
        return;
      }

      container.innerHTML = appState.history.map(item => `
        <div class="p-2.5 rounded-xl border border-amber-100 dark:border-stone-800 bg-white/70 dark:bg-stone-900 flex items-center justify-between shadow-xs">
          <div>
            <div class="font-bold text-stone-800 dark:text-stone-200">${item.temple || 'Shri Mandir'}</div>
            <div class="text-[10px] text-stone-500">${item.date} • ${item.time}</div>
          </div>
          <div class="text-right">
            <span class="font-bold font-mono text-amber-700 dark:text-amber-400 text-sm">${item.rounds} / ${item.target}</span>
            <div class="text-[10px] text-stone-500 font-mono">${formatDuration(item.durationSeconds || 0)}</div>
          </div>
        </div>
      `).join('');
    }

    function clearAllHistory() {
      appState.history = [];
      saveState();
      renderHistory();
      showToast("History cleared");
    }

    function exportHistoryCsv() {
      if (appState.history.length === 0) {
        showToast("No history to export");
        return;
      }
      let csv = "Date,Time,Temple,Rounds,Target,Duration_Sec\n";
      appState.history.forEach(item => {
        csv += `"${item.date}","${item.time}","${item.temple}",${item.rounds},${item.target},${item.durationSeconds}\n`;
      });
      const blob = new Blob([csv], { type: 'text/csv' });
      const url = URL.createObjectURL(blob);
      const a = document.createElement('a');
      a.href = url;
      a.download = `parikrama_journal_${Date.now()}.csv`;
      document.body.appendChild(a);
      a.click();
      document.body.removeChild(a);
      URL.revokeObjectURL(url);
    }

    // TAB NAVIGATION
    function switchTab(tab) {
      const views = {
        counter: document.getElementById('counterView'),
        gps: document.getElementById('gpsView'),
        history: document.getElementById('historyView')
      };
      const buttons = {
        counter: document.getElementById('tabBtnCounter'),
        gps: document.getElementById('tabBtnGps'),
        history: document.getElementById('tabBtnHistory')
      };

      Object.keys(views).forEach(k => {
        if (views[k]) {
          if (k === tab) views[k].classList.remove('hidden');
          else views[k].classList.add('hidden');
        }
      });

      Object.keys(buttons).forEach(k => {
        if (buttons[k]) {
          if (k === tab) {
            buttons[k].className = "flex flex-col items-center gap-0.5 text-amber-600 dark:text-amber-400 font-bold transition";
          } else {
            buttons[k].className = "flex flex-col items-center gap-0.5 text-stone-400 hover:text-amber-600 dark:hover:text-amber-400 font-medium transition";
          }
        }
      });

      if (tab === 'history') renderHistory();
      if (tab === 'gps') initGpsTracking();
    }

    // SCREEN WAKE LOCK
    async function toggleWakeLock() {
      if ('wakeLock' in navigator) {
        if (appState.wakeLockSentinel) {
          appState.wakeLockSentinel.release();
          appState.wakeLockSentinel = null;
          updateWakeLockUi(false);
        } else {
          try {
            appState.wakeLockSentinel = await navigator.wakeLock.request('screen');
            updateWakeLockUi(true);
            appState.wakeLockSentinel.addEventListener('release', () => updateWakeLockUi(false));
          } catch (e) {
            showToast("Screen wake lock denied");
          }
        }
      } else {
        showToast("Wake lock not supported on this browser");
      }
    }

    function updateWakeLockUi(active) {
      const icon = document.getElementById('wakeLockIcon');
      const label = document.getElementById('wakeLockLabel');
      if (active) {
        if (icon) icon.className = "w-2 h-2 rounded-full bg-emerald-500 animate-pulse";
        if (label) label.textContent = "Awake: On";
      } else {
        if (icon) icon.className = "w-2 h-2 rounded-full bg-stone-400";
        if (label) label.textContent = "Awake: Off";
      }
    }

    // THEME & SOUND
    function toggleSound() {
      appState.soundEnabled = !appState.soundEnabled;
      const icon = document.getElementById('soundIcon');
      if (icon) icon.textContent = appState.soundEnabled ? '🔔' : '🔕';
      saveState();
      showToast(appState.soundEnabled ? "Sound Enabled" : "Sound Muted");
    }

    function toggleTheme() {
      const isDark = document.documentElement.classList.toggle('dark');
      const icon = document.getElementById('themeIcon');
      if (icon) icon.textContent = isDark ? '☀️' : '🌙';
    }

    // UTILITIES
    function formatDuration(sec) {
      const m = Math.floor(sec / 60);
      const s = sec % 60;
      return `${String(m).padStart(2, '0')}:${String(s).padStart(2, '0')}`;
    }

    function showToast(msg) {
      const toast = document.getElementById('toastMessage');
      const toastText = document.getElementById('toastText');
      if (toast && toastText) {
        toastText.textContent = msg;
        toast.classList.remove('opacity-0', '-translate-y-4');
        toast.classList.add('opacity-100', 'translate-y-0');
        clearTimeout(toast.timer);
        toast.timer = setTimeout(() => {
          toast.classList.add('opacity-0', '-translate-y-4');
          toast.classList.remove('opacity-100', 'translate-y-0');
        }, 2200);
      }
    }

    // INITIALIZATION
    window.addEventListener('DOMContentLoaded', () => {
      renderCounter();
      renderHistory();
      document.body.addEventListener('click', () => getAudioContext(), { once: true });
      initGpsTracking();
    });
  </script>
</body>
</html>
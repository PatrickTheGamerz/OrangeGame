<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width,initial-scale=1,maximum-scale=1,user-scalable=no" />
  <title>Orange Games - X and O Arena</title>
  
  <link href="https://fonts.googleapis.com/css2?family=Orbitron:wght@500;700;900&family=Inter:wght@400;500;600&display=swap" rel="stylesheet">
  
  <script src="https://cdn.tailwindcss.com"></script>
  <script>
    tailwind.config = {
      theme: {
        extend: {
          colors: {
            brand: { DEFAULT: '#ff6a00', light: '#ff9966', dark: '#cc5500', glow: 'rgba(255, 106, 0, 0.4)' },
            surface: { DEFAULT: 'rgba(21, 24, 34, 0.7)', solid: '#151822' }
          },
          fontFamily: { orbitron: ['Orbitron', 'sans-serif'], inter: ['Inter', 'sans-serif'] },
          animation: { 'float-slow': 'float 15s infinite linear', 'flicker': 'flicker 3s infinite alternate' },
          keyframes: {
            float: { '0%': { transform: 'translateY(100vh) scale(0.5)', opacity: '0' }, '50%': { opacity: '0.6' }, '100%': { transform: 'translateY(-20vh) scale(1.5)', opacity: '0' } },
            flicker: { '0%, 18%, 22%, 25%, 53%, 57%, 100%': { textShadow: '0 0 5px #fff, 0 0 20px #ff6a00, 0 0 40px #ff6a00' }, '20%, 24%, 55%': { textShadow: 'none' } }
          }
        }
      }
    }
  </script>

  <style>
    * { user-select: none; -webkit-user-select: none; -moz-user-select: none; -ms-user-select: none; box-sizing: border-box; }
    input, select { user-select: auto; -webkit-user-select: auto; -moz-user-select: auto; -ms-user-select: auto; }

    body { background-color: #080a0f; color: #f0f0f0; overflow-x: hidden; margin: 0; display: flex; flex-direction: column; min-height: 100vh; font-family: 'Inter', sans-serif; }

    ::-webkit-scrollbar { width: 8px; height: 8px; }
    ::-webkit-scrollbar-track { background: rgba(0,0,0,0.2); border-radius: 4px; }
    ::-webkit-scrollbar-thumb { background: #ff6a00; border-radius: 4px; }

    .ambient-engine { position: fixed; top: 0; left: 0; width: 100vw; height: 100vh; z-index: -2; overflow: hidden; background: radial-gradient(circle at 15% 50%, rgba(255,106,0,0.05), transparent 50%), radial-gradient(circle at 85% 30%, rgba(255,153,102,0.03), transparent 50%); }
    .particle { position: absolute; border-radius: 50%; background: radial-gradient(circle, #ff9966 0%, transparent 70%); opacity: 0.5; filter: blur(4px); }

    .glass-panel { background: rgba(21, 24, 34, 0.6); backdrop-filter: blur(16px); -webkit-backdrop-filter: blur(16px); border: 1px solid rgba(255, 255, 255, 0.08); box-shadow: 0 4px 30px rgba(0, 0, 0, 0.5); }

    .btn-orange { background: linear-gradient(90deg, #ff6a00, #eab308); color: #000; font-family: 'Orbitron', sans-serif; font-weight: 900; text-transform: uppercase; padding: 10px 20px; border-radius: 12px; cursor: pointer; transition: all 0.3s ease; border: none; outline: none; }
    .btn-orange:hover { transform: translateY(-2px); box-shadow: 0 10px 20px rgba(255,106,0,0.4); }
    .btn-orange:active { transform: translateY(0); }
    .btn-dark { background: rgba(0,0,0,0.4); border: 1px solid rgba(255,255,255,0.1); color: #fff; font-family: 'Orbitron', sans-serif; padding: 10px 20px; border-radius: 12px; cursor: pointer; transition: all 0.3s ease; }
    .btn-dark:hover { border-color: #ff6a00; background: rgba(255,106,0,0.1); }

    .modal-overlay { position: fixed; inset: 0; background: rgba(0,0,0,0.8); backdrop-filter: blur(8px); display: none; place-items: center; z-index: 100; opacity: 0; transition: opacity 0.3s ease; }
    .modal-overlay.active { display: grid; opacity: 1; pointer-events: auto; }
    .modal-content { transform: translateY(20px) scale(0.95); transition: all 0.4s cubic-bezier(0.175, 0.885, 0.32, 1.275); background: #151822; border: 1px solid rgba(255,106,0,0.3); border-radius: 24px; padding: 24px; width: 90%; max-width: 500px; box-shadow: 0 10px 40px rgba(255,106,0,0.15); }
    .modal-overlay.active .modal-content { transform: translateY(0) scale(1); }

    .input-field { width: 100%; background: rgba(0,0,0,0.4); border: 1px solid rgba(255,255,255,0.1); border-radius: 12px; color: #fff; padding: 12px; margin-top: 6px; outline: none; transition: all 0.3s ease; }
    .input-field:focus { border-color: #ff6a00; box-shadow: 0 0 15px rgba(255,106,0,0.3); }

    .board-wrap { width: 100%; max-width: 500px; margin: 0 auto; aspect-ratio: 1; position: relative; display: grid; gap: 8px; padding: 12px; border-radius: 20px; border: 1px solid rgba(255,106,0,0.2); background: rgba(21, 24, 34, 0.7); backdrop-filter: blur(10px); box-shadow: 0 10px 30px rgba(0,0,0,0.5); }
    .cell { position: relative; background: linear-gradient(180deg, #1a1e2d, #111420); border: 1px solid rgba(255,255,255,0.05); border-radius: 12px; display: flex; align-items: center; justify-content: center; font-family: 'Orbitron', sans-serif; font-weight: 900; font-size: clamp(30px, 8vw, 60px); cursor: pointer; transition: all 0.2s ease; overflow: hidden; }
    .cell:hover { border-color: rgba(255,106,0,0.4); background: linear-gradient(180deg, #22273b, #151825); transform: scale(0.98); }
    .cell.played { animation: pop 0.2s cubic-bezier(0.2, 0.7, 0.2, 1); }
    @keyframes pop { 0% { transform: scale(0.8); opacity: 0; } 100% { transform: scale(1); opacity: 1; } }

    .glyph { animation: glyphIn 0.3s cubic-bezier(0.175, 0.885, 0.32, 1.275); }
    @keyframes glyphIn { 0% { transform: scale(0); } 100% { transform: scale(1); } }
    
    .x-mark { color: #ff6a00; text-shadow: 0 0 15px rgba(255,106,0,0.5); }
    .o-mark { color: #eab308; text-shadow: 0 0 15px rgba(234,179,8,0.5); }
    .p3-mark { color: #f43f5e; text-shadow: 0 0 15px rgba(244,63,94,0.5); }
    .p4-mark { color: #10b981; text-shadow: 0 0 15px rgba(16,185,129,0.5); }

    .win-pulse { outline: 2px solid #32cd32; box-shadow: 0 0 20px rgba(50,205,50,0.4) inset; animation: winP 1s infinite alternate; }
    @keyframes winP { from { filter: brightness(1); } to { filter: brightness(1.3); } }

    .beam { position: absolute; height: 6px; background: #32cd32; box-shadow: 0 0 20px #32cd32; transform-origin: left center; z-index: 10; border-radius: 10px; pointer-events: none; animation: beamDraw 0.6s cubic-bezier(0.175, 0.885, 0.32, 1.275) forwards; }
    @keyframes beamDraw { from { width: 0; opacity: 0; } to { width: 100%; opacity: 1; } }

    .cards-tray { position: fixed; bottom: 10px; left: 50%; transform: translateX(-50%); display: none; gap: 10px; padding: 12px; background: rgba(10,12,18,0.9); border: 1px solid rgba(255,106,0,0.2); border-radius: 16px; backdrop-filter: blur(10px); max-width: 95vw; overflow-x: auto; z-index: 50; }
    .card-item { min-width: 90px; height: 130px; border-radius: 12px; border: 1px solid rgba(255,255,255,0.1); background: linear-gradient(180deg, #1b2033, #121626); color: #fff; display: flex; flex-direction: column; align-items: center; justify-content: center; cursor: pointer; transition: all 0.2s ease; position: relative; padding: 8px; text-align: center; }
    .card-item:hover { transform: translateY(-8px); border-color: #ff6a00; box-shadow: 0 10px 20px rgba(255,106,0,0.2); }
    .card-name { font-family: 'Orbitron', sans-serif; font-size: 11px; font-weight: 700; margin-bottom: 4px; }
    .card-desc { font-size: 9px; color: #a7b0c0; line-height: 1.2; }
    .rar-bar { position: absolute; bottom: 0; left: 0; right: 0; height: 5px; border-radius: 0 0 12px 12px; }
    .rar-common { background: #9aa0aa; }
    .rar-rare { background: #3b82f6; }
    .rar-epic { background: #a855f7; }
    .rar-legend { background: #eab308; }
    .rar-myth { background: #ef4444; }

    .hud-stat { display: flex; align-items: center; justify-content: center; gap: 8px; background: rgba(0,0,0,0.4); border: 1px solid rgba(255,255,255,0.05); padding: 8px 16px; border-radius: 12px; font-family: 'Orbitron', sans-serif; font-size: 14px; }
    
    .overlay-message { position: fixed; inset: 0; display: none; place-items: center; background: radial-gradient(circle, rgba(0,0,0,0.7), rgba(0,0,0,0.9)); z-index: 200; pointer-events: none; }
    .overlay-message.active { display: grid; }
    .msg-box { background: #151822; border: 2px solid #ff6a00; padding: 20px 40px; border-radius: 20px; text-align: center; box-shadow: 0 0 50px rgba(255,106,0,0.3); animation: popMsg 0.3s cubic-bezier(0.175, 0.885, 0.32, 1.275); }
    @keyframes popMsg { 0% { transform: scale(0.5); opacity: 0; } 100% { transform: scale(1); opacity: 1; } }

    .fog-layer { position: absolute; inset: 0; background: rgba(10,12,18,0.85); backdrop-filter: blur(8px); z-index: 5; opacity: 0; pointer-events: none; transition: opacity 0.3s ease; border-radius: 20px; }
    .fog-layer.active { opacity: 1; }
  </style>
</head>
<body>

  <div class="ambient-engine">
    <div class="particle animate-float-slow" style="left: 10%; width: 40px; height: 40px; animation-duration: 15s;"></div>
    <div class="particle animate-float-slow" style="left: 40%; width: 60px; height: 60px; animation-duration: 20s; animation-delay: -5s;"></div>
    <div class="particle animate-float-slow" style="left: 70%; width: 30px; height: 30px; animation-duration: 12s; animation-delay: -2s;"></div>
    <div class="particle animate-float-slow" style="left: 85%; width: 80px; height: 80px; animation-duration: 25s; animation-delay: -10s;"></div>
  </div>

  <header class="glass-panel sticky top-0 z-50 px-4 py-3 flex items-center justify-between">
    <div class="text-xl md:text-2xl font-orbitron font-black tracking-widest text-transparent bg-clip-text bg-gradient-to-r from-brand to-yellow-400 cursor-pointer" onclick="window.location.href='index.html'">
      <i class="fas fa-chevron-left mr-2 text-brand text-lg"></i> X & O ARENA
    </div>
    <div class="flex gap-3">
      <button class="btn-dark text-sm px-4 py-2" id="btn-setup" data-i18n="setup">Setup</button>
      <button class="btn-dark text-sm px-4 py-2" id="btn-reset" data-i18n="reset">Reset</button>
    </div>
  </header>

  <main class="flex-grow container mx-auto px-4 py-6 flex flex-col items-center">
    
    <div class="w-full max-w-lg flex flex-wrap justify-center gap-3 mb-6">
      <div class="hud-stat text-brand border-brand/30" id="status-display" data-i18n="waiting">Waiting for setup...</div>
      <div class="hud-stat text-white" id="timer-box" style="display: none;"><i class="fas fa-stopwatch text-yellow-500"></i> <span id="timer-val">0</span>s</div>
    </div>

    <div class="board-wrap" id="board">
      <div class="fog-layer" id="fog-layer"></div>
    </div>

    <div class="w-full max-w-lg mt-8 grid grid-cols-2 md:grid-cols-5 gap-3">
      <div class="hud-stat flex-col py-2"><span class="text-brand text-xs">X</span><span id="score-X" class="text-lg">0</span></div>
      <div class="hud-stat flex-col py-2"><span class="text-yellow-500 text-xs">O</span><span id="score-O" class="text-lg">0</span></div>
      <div class="hud-stat flex-col py-2"><span class="text-rose-500 text-xs">■</span><span id="score-P3" class="text-lg">0</span></div>
      <div class="hud-stat flex-col py-2"><span class="text-emerald-500 text-xs">▲</span><span id="score-P4" class="text-lg">0</span></div>
      <div class="hud-stat flex-col py-2 md:col-span-1 col-span-2"><span class="text-gray-400 text-xs" data-i18n="draws">Draws</span><span id="score-D" class="text-lg">0</span></div>
    </div>

  </main>

  <div class="cards-tray custom-scrollbar-mini" id="cards-tray"></div>

  <div class="overlay-message" id="overlay-msg">
    <div class="msg-box">
      <h2 class="font-orbitron text-2xl font-bold text-white mb-2" id="msg-title">Title</h2>
      <p class="text-gray-400 text-sm" id="msg-desc">Description</p>
    </div>
  </div>

  <div class="modal-overlay" id="setup-modal">
    <div class="modal-content">
      <h3 class="font-orbitron text-2xl font-bold text-white mb-6 text-center" data-i18n="game_setup">Game Setup</h3>
      
      <div class="space-y-4">
        <div>
          <label class="text-xs font-bold text-gray-500 uppercase tracking-wider block mb-1" data-i18n="opponent">Opponent</label>
          <select id="cfg-opponent" class="input-field font-orbitron">
            <option value="player" data-i18n="local_players">Local players</option>
            <option value="bot" data-i18n="bots">Bots</option>
          </select>
        </div>

        <div id="row-difficulty" style="display:none;">
          <label class="text-xs font-bold text-gray-500 uppercase tracking-wider flex justify-between mb-1">
            <span data-i18n="difficulty">Difficulty</span> <span id="lbl-difficulty" class="text-brand">2.5</span>
          </label>
          <input type="range" id="cfg-difficulty" min="1" max="8.9" step="0.1" value="2.5" class="w-full accent-brand">
        </div>

        <div>
          <label class="text-xs font-bold text-gray-500 uppercase tracking-wider block mb-1" data-i18n="gamemode">Gamemode</label>
          <select id="cfg-mode" class="input-field font-orbitron">
            <option value="casual" data-i18n="casual">Casual</option>
            <option value="randomizer" data-i18n="chaos_deck">Chaos Deck (Cards)</option>
            <option value="mine" data-i18n="minefield">Minefield</option>
            <option value="blitz" data-i18n="blitz">Blitz (Timer)</option>
            <option value="three" data-i18n="triad">Triad Clash (3P)</option>
            <option value="four" data-i18n="quad">Quad Arena (4P)</option>
          </select>
        </div>

        <div>
          <label class="text-xs font-bold text-gray-500 uppercase tracking-wider block mb-1" data-i18n="players">Players</label>
          <select id="cfg-players" class="input-field font-orbitron">
            <option value="2">2 Players</option>
            <option value="3">3 Players</option>
            <option value="4">4 Players</option>
          </select>
        </div>

        <div id="row-blitz" style="display:none;">
          <label class="text-xs font-bold text-gray-500 uppercase tracking-wider flex justify-between mb-1">
            <span>Blitz Timer</span> <span id="lbl-blitz" class="text-brand">10s</span>
          </label>
          <input type="range" id="cfg-blitz" min="5" max="20" step="1" value="10" class="w-full accent-brand">
        </div>
      </div>

      <div class="flex gap-3 mt-8">
        <button class="btn-dark flex-1" id="btn-cancel" data-i18n="cancel">Cancel</button>
        <button class="btn-orange flex-1" id="btn-start" data-i18n="start">Start</button>
      </div>
    </div>
  </div>

  <script>
    const translations = {
      "English": { "setup": "Setup", "reset": "Reset", "waiting": "Waiting for setup...", "local_players": "Local players", "bots": "Bots", "opponent": "Opponent", "difficulty": "Difficulty", "gamemode": "Gamemode", "casual": "Casual", "chaos_deck": "Chaos Deck (Cards)", "minefield": "Minefield", "blitz": "Blitz (Timer)", "triad": "Triad Clash (3P)", "quad": "Quad Arena (4P)", "players": "Players", "game_setup": "Game Setup", "cancel": "Cancel", "start": "Start", "draws": "Draws", "turn": "to move", "win": "wins!", "draw_game": "Draw Game!", "bot_think": "Bot thinking..." },
      "Español": { "setup": "Ajustes", "reset": "Reiniciar", "waiting": "Esperando...", "local_players": "Jugadores locales", "bots": "Bots", "opponent": "Oponente", "difficulty": "Dificultad", "gamemode": "Modo de juego", "casual": "Casual", "chaos_deck": "Cartas del Caos", "minefield": "Campo de Minas", "blitz": "Blitz (Tiempo)", "triad": "Tríada (3P)", "quad": "Arena Quad (4P)", "players": "Jugadores", "game_setup": "Configuración", "cancel": "Cancelar", "start": "Empezar", "draws": "Empates", "turn": "tu turno", "win": "¡gana!", "draw_game": "¡Empate!", "bot_think": "Bot pensando..." },
      "Français": { "setup": "Paramètres", "reset": "Réinitialiser", "waiting": "En attente...", "local_players": "Joueurs locaux", "bots": "Bots", "opponent": "Adversaire", "difficulty": "Difficulté", "gamemode": "Mode de jeu", "casual": "Occasionnel", "chaos_deck": "Cartes Chaos", "minefield": "Champ de mines", "blitz": "Blitz (Chrono)", "triad": "Triade (3P)", "quad": "Arène Quad (4P)", "players": "Joueurs", "game_setup": "Configuration", "cancel": "Annuler", "start": "Démarrer", "draws": "Égalités", "turn": "de jouer", "win": "gagne !", "draw_game": "Égalité !", "bot_think": "Le bot réfléchit..." },
      "Deutsch": { "setup": "Setup", "reset": "Neustart", "waiting": "Warten...", "local_players": "Lokale Spieler", "bots": "Bots", "opponent": "Gegner", "difficulty": "Schwierigkeit", "gamemode": "Spielmodus", "casual": "Gelegenheitsspiel", "chaos_deck": "Chaos Karten", "minefield": "Minenfeld", "blitz": "Blitz (Zeit)", "triad": "Triade (3P)", "quad": "Quad Arena (4P)", "players": "Spieler", "game_setup": "Spiel-Setup", "cancel": "Abbrechen", "start": "Starten", "draws": "Unentschieden", "turn": "ist dran", "win": "gewinnt!", "draw_game": "Unentschieden!", "bot_think": "Bot denkt..." },
      "Italiano": { "setup": "Impostazioni", "reset": "Ripristina", "waiting": "In attesa...", "local_players": "Giocatori locali", "bots": "Bot", "opponent": "Avversario", "difficulty": "Difficoltà", "gamemode": "Modalità", "casual": "Classica", "chaos_deck": "Carte Caos", "minefield": "Campo Minato", "blitz": "Blitz (Tempo)", "triad": "Triade (3P)", "quad": "Quad Arena (4P)", "players": "Giocatori", "game_setup": "Impostazioni Gioco", "cancel": "Annulla", "start": "Avvia", "draws": "Pareggi", "turn": "tocca a te", "win": "vince!", "draw_game": "Pareggio!", "bot_think": "Il bot pensa..." },
      "Português": { "setup": "Config", "reset": "Reiniciar", "waiting": "Aguardando...", "local_players": "Jogadores locais", "bots": "Bots", "opponent": "Oponente", "difficulty": "Dificuldade", "gamemode": "Modo de jogo", "casual": "Casual", "chaos_deck": "Cartas Caos", "minefield": "Campo Minado", "blitz": "Blitz (Tempo)", "triad": "Tríade (3P)", "quad": "Arena Quad (4P)", "players": "Jogadores", "game_setup": "Configurar Jogo", "cancel": "Cancelar", "start": "Começar", "draws": "Empates", "turn": "sua vez", "win": "venceu!", "draw_game": "Empate!", "bot_think": "Bot pensando..." },
      "Polski": { "setup": "Ustawienia", "reset": "Resetuj", "waiting": "Oczekiwanie...", "local_players": "Gracze lokalni", "bots": "Boty", "opponent": "Przeciwnik", "difficulty": "Poziom trudności", "gamemode": "Tryb gry", "casual": "Klasyczny", "chaos_deck": "Karty Chaosu", "minefield": "Pole Minowe", "blitz": "Blitz (Czas)", "triad": "Triada (3P)", "quad": "Quad Arena (4P)", "players": "Gracze", "game_setup": "Ustawienia Gry", "cancel": "Anuluj", "start": "Start", "draws": "Remisy", "turn": "twój ruch", "win": "wygrywa!", "draw_game": "Remis!", "bot_think": "Bot myśli..." },
      "Русский": { "setup": "Настройки", "reset": "Сброс", "waiting": "Ожидание...", "local_players": "Локально", "bots": "Боты", "opponent": "Противник", "difficulty": "Сложность", "gamemode": "Режим", "casual": "Обычный", "chaos_deck": "Карты Хаоса", "minefield": "Минное поле", "blitz": "Блиц (Время)", "triad": "Триада (3P)", "quad": "Квад (4P)", "players": "Игроки", "game_setup": "Настройка Игры", "cancel": "Отмена", "start": "Старт", "draws": "Ничьи", "turn": "твой ход", "win": "победил!", "draw_game": "Ничья!", "bot_think": "Бот думает..." },
      "Українська": { "setup": "Налаштування", "reset": "Скинути", "waiting": "Очікування...", "local_players": "Локально", "bots": "Боти", "opponent": "Супротивник", "difficulty": "Складність", "gamemode": "Режим", "casual": "Звичайний", "chaos_deck": "Карти Хаосу", "minefield": "Мінне поле", "blitz": "Бліц (Час)", "triad": "Тріада (3P)", "quad": "Квад (4P)", "players": "Гравці", "game_setup": "Налаштування Гри", "cancel": "Скасувати", "start": "Старт", "draws": "Нічиї", "turn": "твій хід", "win": "переміг!", "draw_game": "Нічия!", "bot_think": "Бот думає..." },
      "Türkçe": { "setup": "Ayar", "reset": "Sıfırla", "waiting": "Bekleniyor...", "local_players": "Yerel oyuncular", "bots": "Botlar", "opponent": "Rakip", "difficulty": "Zorluk", "gamemode": "Oyun Modu", "casual": "Klasik", "chaos_deck": "Kaos Kartları", "minefield": "Mayın Tarlası", "blitz": "Yıldırım (Süre)", "triad": "Üçlü (3P)", "quad": "Dörtlü (4P)", "players": "Oyuncular", "game_setup": "Oyun Ayarı", "cancel": "İptal", "start": "Başla", "draws": "Beraberlik", "turn": "hamle sırası", "win": "kazandı!", "draw_game": "Berabere!", "bot_think": "Bot düşünüyor..." },
      "Nederlands": { "setup": "Instellingen", "reset": "Reset", "waiting": "Wachten...", "local_players": "Lokale spelers", "bots": "Bots", "opponent": "Tegenstander", "difficulty": "Moeilijkheid", "gamemode": "Spelmodus", "casual": "Klassiek", "chaos_deck": "Chaos Kaarten", "minefield": "Mijnenveld", "blitz": "Blitz (Tijd)", "triad": "Triade (3P)", "quad": "Quad Arena (4P)", "players": "Spelers", "game_setup": "Spel Setup", "cancel": "Annuleren", "start": "Start", "draws": "Gelijkspel", "turn": "aan de beurt", "win": "wint!", "draw_game": "Gelijkspel!", "bot_think": "Bot denkt..." },
      "Svenska": { "setup": "Inställningar", "reset": "Återställ", "waiting": "Väntar...", "local_players": "Lokala spelare", "bots": "Botar", "opponent": "Motståndare", "difficulty": "Svårighet", "gamemode": "Spelläge", "casual": "Klassisk", "chaos_deck": "Kaos Kort", "minefield": "Minfält", "blitz": "Blitz (Tid)", "triad": "Triad (3P)", "quad": "Quad (4P)", "players": "Spelare", "game_setup": "Spel Setup", "cancel": "Avbryt", "start": "Starta", "draws": "Oavgjort", "turn": "din tur", "win": "vinner!", "draw_game": "Oavgjort!", "bot_think": "Bot tänker..." },
      "中文": { "setup": "设置", "reset": "重置", "waiting": "等待中...", "local_players": "本地玩家", "bots": "机器人", "opponent": "对手", "difficulty": "难度", "gamemode": "模式", "casual": "休闲", "chaos_deck": "混沌卡牌", "minefield": "雷区", "blitz": "闪电战", "triad": "三足鼎立 (3P)", "quad": "四人竞技 (4P)", "players": "玩家", "game_setup": "游戏设置", "cancel": "取消", "start": "开始", "draws": "平局", "turn": "的回合", "win": "获胜！", "draw_game": "平局！", "bot_think": "思考中..." },
      "日本語": { "setup": "設定", "reset": "リセット", "waiting": "待機中...", "local_players": "ローカル", "bots": "ボット", "opponent": "対戦相手", "difficulty": "難易度", "gamemode": "モード", "casual": "カジュアル", "chaos_deck": "カオスカード", "minefield": "地雷原", "blitz": "ブリッツ", "triad": "トライアド(3P)", "quad": "クアッド(4P)", "players": "プレイヤー", "game_setup": "ゲーム設定", "cancel": "キャンセル", "start": "開始", "draws": "引き分け", "turn": "のターン", "win": "の勝利！", "draw_game": "引き分け！", "bot_think": "思考中..." },
      "한국어": { "setup": "설정", "reset": "재설정", "waiting": "대기 중...", "local_players": "로컬 플레이어", "bots": "봇", "opponent": "상대", "difficulty": "난이도", "gamemode": "모드", "casual": "캐주얼", "chaos_deck": "카오스 덱", "minefield": "지뢰밭", "blitz": "블리츠", "triad": "트라이어드(3P)", "quad": "쿼드(4P)", "players": "플레이어", "game_setup": "게임 설정", "cancel": "취소", "start": "시작", "draws": "무승부", "turn": "의 차례", "win": "승리!", "draw_game": "무승부!", "bot_think": "봇 생각 중..." },
      "हिन्दी": { "setup": "सेटअप", "reset": "रीसेट", "waiting": "प्रतीक्षा...", "local_players": "स्थानीय खिलाड़ी", "bots": "बॉट्स", "opponent": "प्रतिद्वंद्वी", "difficulty": "कठिनाई", "gamemode": "गेम मोड", "casual": "कैज़ुअल", "chaos_deck": "केओस कार्ड्स", "minefield": "माइनफील्ड", "blitz": "ब्लिट्ज", "triad": "त्रिक (3P)", "quad": "क्वाड (4P)", "players": "खिलाड़ी", "game_setup": "गेम सेटअप", "cancel": "रद्द करें", "start": "शुरू", "draws": "ड्रॉ", "turn": "की बारी", "win": "जीत गया!", "draw_game": "मैच ड्रॉ!", "bot_think": "बॉट सोच रहा है..." },
      "العربية": { "setup": "إعداد", "reset": "إعادة", "waiting": "في الانتظار...", "local_players": "لاعبين محليين", "bots": "روبوتات", "opponent": "الخصم", "difficulty": "الصعوبة", "gamemode": "الوضع", "casual": "عادي", "chaos_deck": "فوضى البطاقات", "minefield": "حقل ألغام", "blitz": "خاطف", "triad": "ثلاثي", "quad": "رباعي", "players": "اللاعبين", "game_setup": "إعداد اللعبة", "cancel": "إلغاء", "start": "ابدأ", "draws": "تعادل", "turn": "دور", "win": "يفوز!", "draw_game": "تعادل!", "bot_think": "يفكر..." },
      "Tiếng Việt": { "setup": "Cài đặt", "reset": "Chơi lại", "waiting": "Đang chờ...", "local_players": "Người chơi", "bots": "Máy", "opponent": "Đối thủ", "difficulty": "Độ khó", "gamemode": "Chế độ", "casual": "Thường", "chaos_deck": "Thẻ bài", "minefield": "Bãi mìn", "blitz": "Tính giờ", "triad": "Ba người", "quad": "Bốn người", "players": "Số người", "game_setup": "Cài đặt Game", "cancel": "Hủy", "start": "Bắt đầu", "draws": "Hòa", "turn": "đến lượt", "win": "chiến thắng!", "draw_game": "Hòa!", "bot_think": "Máy đang tính..." },
      "ภาษาไทย": { "setup": "ตั้งค่า", "reset": "เริ่มใหม่", "waiting": "รอ...", "local_players": "ผู้เล่นในเครื่อง", "bots": "บอท", "opponent": "คู่ต่อสู้", "difficulty": "ความยาก", "gamemode": "โหมด", "casual": "ทั่วไป", "chaos_deck": "การ์ดพลัง", "minefield": "กับระเบิด", "blitz": "จับเวลา", "triad": "สามคน", "quad": "สี่คน", "players": "ผู้เล่น", "game_setup": "ตั้งค่าเกม", "cancel": "ยกเลิก", "start": "เริ่ม", "draws": "เสมอ", "turn": "ตาของ", "win": "ชนะ!", "draw_game": "เสมอ!", "bot_think": "บอทกำลังคิด..." },
      "Bahasa Indonesia": { "setup": "Atur", "reset": "Ulang", "waiting": "Menunggu...", "local_players": "Pemain lokal", "bots": "Bot", "opponent": "Lawan", "difficulty": "Kesulitan", "gamemode": "Mode", "casual": "Biasa", "chaos_deck": "Kartu Chaos", "minefield": "Ladang Ranjau", "blitz": "Waktu", "triad": "Tiga (3P)", "quad": "Empat (4P)", "players": "Pemain", "game_setup": "Atur Game", "cancel": "Batal", "start": "Mulai", "draws": "Seri", "turn": "giliran", "win": "menang!", "draw_game": "Seri!", "bot_think": "Bot berpikir..." }
    };

    let currentLang = localStorage.getItem('orange_lang') || 'English';
    function t(key) {
      const dict = translations[currentLang] || translations["English"];
      return dict[key] || key;
    }
    
    function applyTranslations() {
      document.querySelectorAll('[data-i18n]').forEach(el => {
        const key = el.getAttribute('data-i18n');
        if (el.tagName === 'INPUT' || el.tagName === 'OPTION') {
          if(el.tagName === 'OPTION') el.text = t(key);
        } else {
          el.innerHTML = t(key);
        }
      });
      if(state.winner === null && state.moves > 0) updateStatus();
    }

    const UI = {
      board: document.getElementById('board'),
      status: document.getElementById('status-display'),
      setupBtn: document.getElementById('btn-setup'),
      resetBtn: document.getElementById('btn-reset'),
      modal: document.getElementById('setup-modal'),
      cancelBtn: document.getElementById('btn-cancel'),
      startBtn: document.getElementById('btn-start'),
      cfgOpponent: document.getElementById('cfg-opponent'),
      cfgDifficulty: document.getElementById('cfg-difficulty'),
      cfgMode: document.getElementById('cfg-mode'),
      cfgPlayers: document.getElementById('cfg-players'),
      rowDifficulty: document.getElementById('row-difficulty'),
      rowBlitz: document.getElementById('row-blitz'),
      lblDifficulty: document.getElementById('lbl-difficulty'),
      cfgBlitz: document.getElementById('cfg-blitz'),
      lblBlitz: document.getElementById('lbl-blitz'),
      timerBox: document.getElementById('timer-box'),
      timerVal: document.getElementById('timer-val'),
      cardsTray: document.getElementById('cards-tray'),
      overlayMsg: document.getElementById('overlay-msg'),
      msgTitle: document.getElementById('msg-title'),
      msgDesc: document.getElementById('msg-desc'),
      fogLayer: document.getElementById('fog-layer')
    };

    let config = { opponent: 'player', difficulty: 2.5, mode: 'casual', players: 2, blitzSec: 10 };
    let state = { size: 3, grid: [], blocked: new Set(), order: [], turnIndex: 0, winner: null, line: [], moves: 0, mines: new Set(), deck: [], hands: new Map(), extraTurns: 0, shields: new Set() };
    let scores = { X: 0, O: 0, P3: 0, P4: 0, D: 0 };
    let timer = null;
    let activeUser = localStorage.getItem('orange_currentUser');

    function updateHubScore() {
      if(!activeUser) return;
      let currentScore = parseInt(localStorage.getItem(`orange_au_score_${activeUser}`)) || 0;
      currentScore += 50; 
      localStorage.setItem(`orange_au_score_${activeUser}`, currentScore);
    }

    UI.setupBtn.onclick = () => { UI.modal.classList.add('active'); applyConfigVisibility(); };
    UI.cancelBtn.onclick = () => UI.modal.classList.remove('active');
    UI.resetBtn.onclick = () => initGame();

    function applyConfigVisibility() {
      UI.rowDifficulty.style.display = UI.cfgOpponent.value === 'bot' ? 'block' : 'none';
      UI.rowBlitz.style.display = UI.cfgMode.value === 'blitz' ? 'block' : 'none';
      if(UI.cfgMode.value === 'three') UI.cfgPlayers.value = '3';
      if(UI.cfgMode.value === 'four') UI.cfgPlayers.value = '4';
    }

    UI.cfgOpponent.onchange = applyConfigVisibility;
    UI.cfgMode.onchange = applyConfigVisibility;
    UI.cfgDifficulty.oninput = (e) => UI.lblDifficulty.textContent = e.target.value;
    UI.cfgBlitz.oninput = (e) => UI.lblBlitz.textContent = e.target.value + 's';

    UI.startBtn.onclick = () => {
      config.opponent = UI.cfgOpponent.value;
      config.difficulty = parseFloat(UI.cfgDifficulty.value);
      config.mode = UI.cfgMode.value;
      config.players = parseInt((UI.cfgMode.value === 'three' || UI.cfgMode.value === 'four') ? (UI.cfgMode.value === 'three' ? '3' : '4') : UI.cfgPlayers.value);
      config.blitzSec = parseInt(UI.cfgBlitz.value);
      UI.modal.classList.remove('active');
      initGame();
    };

    function showMsg(title, desc, duration = 1500) {
      UI.msgTitle.textContent = title;
      UI.msgDesc.textContent = desc;
      UI.overlayMsg.classList.add('active');
      setTimeout(() => UI.overlayMsg.classList.remove('active'), duration);
    }

    function initGame() {
      state.size = config.mode === 'randomizer' ? 5 : (config.players === 4 || config.mode === 'four' ? 5 : (config.players === 3 || config.mode === 'three' ? 4 : 3));
      state.grid = Array(state.size * state.size).fill(null);
      state.blocked.clear();
      state.mines.clear();
      state.hands.clear();
      state.winner = null;
      state.moves = 0;
      state.line = [];
      state.extraTurns = 0;
      state.shields.clear();
      UI.fogLayer.classList.remove('active');

      if(config.players === 4) state.order = ['X', 'O', '■', '▲'];
      else if(config.players === 3) state.order = ['X', 'O', '■'];
      else state.order = ['X', 'O'];
      state.turnIndex = 0;

      if(config.mode === 'mine') {
        let count = config.players === 4 ? 6 : (config.players === 3 ? 4 : 3);
        for(let i=0; i<count; i++) {
          let r; do { r = Math.floor(Math.random() * state.grid.length); } while(state.mines.has(r));
          state.mines.add(r);
        }
      }

      buildDeck();
      for(let p of state.order) state.hands.set(p, [state.deck.pop(), state.deck.pop(), state.deck.pop()]);

      renderBoard();
      updateStatus();
      updateCardsTray();
      renderScores();
      
      if(config.mode === 'blitz') startTimer();
      else { clearInterval(timer); UI.timerBox.style.display = 'none'; }

      if(isBotTurn()) setTimeout(botPlay, 500);
    }

    function renderBoard() {
      Array.from(UI.board.children).forEach(c => { if(c.id !== 'fog-layer') c.remove(); });
      UI.board.style.gridTemplateColumns = `repeat(${state.size}, 1fr)`;
      
      for(let i=0; i<state.size*state.size; i++) {
        let cell = document.createElement('div');
        cell.className = 'cell';
        cell.dataset.idx = i;
        cell.onclick = () => onCellClick(i);
        UI.board.appendChild(cell);
      }
    }

    function symbolClass(sym) {
      if(sym === 'X') return 'x-mark';
      if(sym === 'O') return 'o-mark';
      if(sym === '■') return 'p3-mark';
      if(sym === '▲') return 'p4-mark';
      return '';
    }

    function updateStatus() {
      if(state.winner) return;
      let p = state.order[state.turnIndex];
      if(isBotTurn()) UI.status.textContent = t('bot_think');
      else UI.status.innerHTML = `<span class="${symbolClass(p)}">${p}</span> ${t('turn')}`;
    }

    function isBotTurn() {
      return config.opponent === 'bot' && state.turnIndex > 0;
    }

    function onCellClick(i) {
      if(state.winner || isBotTurn() || state.grid[i] || state.blocked.has(i)) return;

      if(config.mode === 'mine' && state.mines.has(i)) {
        endGame(state.order[(state.turnIndex + 1) % state.order.length], [], true);
        return;
      }

      makeMove(i);
    }

    function makeMove(i) {
      let p = state.order[state.turnIndex];
      state.grid[i] = p;
      state.moves++;

      let cells = Array.from(UI.board.querySelectorAll('.cell'));
      cells[i].innerHTML = `<div class="glyph ${symbolClass(p)}">${p}</div>`;
      cells[i].classList.add('played');

      let winLen = state.size >= 4 ? 4 : 3;
      let win = checkWin(state.grid, state.size, winLen);
      if(win) { endGame(win.player, win.line); return; }

      if(state.moves + state.blocked.size >= state.grid.length) {
        state.winner = 'Draw';
        UI.status.textContent = t('draw_game');
        scores.D++;
        renderScores();
        clearInterval(timer);
        return;
      }

      if(state.extraTurns > 0) state.extraTurns--;
      else state.turnIndex = (state.turnIndex + 1) % state.order.length;

      updateStatus();
      updateCardsTray();
      if(config.mode === 'blitz') startTimer();
      
      if(isBotTurn()) setTimeout(botPlay, Math.random() * 800 + 400);
    }

    function checkWin(g, size, winLen) {
      for(let r=0; r<size; r++) for(let c=0; c<=size-winLen; c++) {
        let p = g[r*size+c]; if(!p) continue;
        let line = [], ok = true;
        for(let k=0; k<winLen; k++) { let idx = r*size+(c+k); line.push(idx); if(g[idx] !== p) ok = false; }
        if(ok) return {player: p, line};
      }
      for(let c=0; c<size; c++) for(let r=0; r<=size-winLen; r++) {
        let p = g[r*size+c]; if(!p) continue;
        let line = [], ok = true;
        for(let k=0; k<winLen; k++) { let idx = (r+k)*size+c; line.push(idx); if(g[idx] !== p) ok = false; }
        if(ok) return {player: p, line};
      }
      for(let r=0; r<=size-winLen; r++) for(let c=0; c<=size-winLen; c++) {
        let p = g[r*size+c]; if(!p) continue;
        let line = [], ok = true;
        for(let k=0; k<winLen; k++) { let idx = (r+k)*size+(c+k); line.push(idx); if(g[idx] !== p) ok = false; }
        if(ok) return {player: p, line};
      }
      for(let r=winLen-1; r<size; r++) for(let c=0; c<=size-winLen; c++) {
        let p = g[r*size+c]; if(!p) continue;
        let line = [], ok = true;
        for(let k=0; k<winLen; k++) { let idx = (r-k)*size+(c+k); line.push(idx); if(g[idx] !== p) ok = false; }
        if(ok) return {player: p, line};
      }
      return null;
    }

    function endGame(winner, line, mine=false) {
      state.winner = winner;
      clearInterval(timer);
      UI.status.innerHTML = mine ? `<span class="text-rose-500">BOOM!</span> <span class="${symbolClass(winner)}">${winner}</span> ${t('win')}` : `<span class="${symbolClass(winner)}">${winner}</span> ${t('win')}`;
      
      let cells = Array.from(UI.board.querySelectorAll('.cell'));
      line.forEach(i => cells[i].classList.add('win-pulse'));

      if(line.length > 0) {
        let first = cells[line[0]].getBoundingClientRect();
        let last = cells[line[line.length-1]].getBoundingClientRect();
        let wrap = UI.board.getBoundingClientRect();
        let beam = document.createElement('div');
        beam.className = 'beam';
        let dy = (last.top+last.bottom)/2 - (first.top+first.bottom)/2;
        let dx = (last.left+last.right)/2 - (first.left+first.right)/2;
        beam.style.width = Math.sqrt(dx*dx + dy*dy) + 'px';
        beam.style.left = ((first.left+first.right)/2 - wrap.left) + 'px';
        beam.style.top = ((first.top+first.bottom)/2 - wrap.top) + 'px';
        beam.style.transform = `rotate(${Math.atan2(dy, dx)}rad)`;
        UI.board.appendChild(beam);
      }

      if(scores[winner] !== undefined) scores[winner]++;
      if(activeUser && winner === 'X') updateHubScore();
      renderScores();
    }

    function renderScores() {
      document.getElementById('score-X').textContent = scores.X;
      document.getElementById('score-O').textContent = scores.O;
      document.getElementById('score-P3').textContent = scores.P3;
      document.getElementById('score-P4').textContent = scores.P4;
      document.getElementById('score-D').textContent = scores.D;
    }

    function startTimer() {
      clearInterval(timer);
      UI.timerBox.style.display = 'flex';
      let tLeft = config.blitzSec;
      UI.timerVal.textContent = tLeft;
      timer = setInterval(() => {
        tLeft--; UI.timerVal.textContent = tLeft;
        if(tLeft <= 0) {
          clearInterval(timer);
          endGame(state.order[(state.turnIndex + 1) % state.order.length], []);
        }
      }, 1000);
    }

    function botPlay() {
      if(state.winner) return;
      let empties = [];
      for(let i=0; i<state.grid.length; i++) if(!state.grid[i] && !state.blocked.has(i)) empties.push(i);
      if(!empties.length) return;
      
      let p = state.order[state.turnIndex];
      let winLen = state.size >= 4 ? 4 : 3;

      for(let i of empties) { state.grid[i] = p; if(checkWin(state.grid, state.size, winLen)) { state.grid[i] = null; makeMove(i); return; } state.grid[i] = null; }
      for(let o of state.order) {
        if(o === p) continue;
        for(let i of empties) { state.grid[i] = o; if(checkWin(state.grid, state.size, winLen)) { state.grid[i] = null; makeMove(i); return; } state.grid[i] = null; }
      }

      let pick = empties[Math.floor(Math.random() * empties.length)];
      if(config.mode === 'mine' && state.mines.has(pick) && Math.random() < (config.difficulty / 10)) {
        let safe = empties.filter(i => !state.mines.has(i));
        if(safe.length) pick = safe[Math.floor(Math.random() * safe.length)];
      }

      if(config.mode === 'mine' && state.mines.has(pick)) {
        endGame(state.order[(state.turnIndex + 1) % state.order.length], [], true);
        return;
      }
      makeMove(pick);
    }

    function buildDeck() {
      state.deck = [
        { name: 'Block', desc: 'Block a random cell', rarity: 'common', act: () => { let e = empty(); if(e.length){ let i = e[Math.floor(Math.random()*e.length)]; state.blocked.add(i); Array.from(UI.board.querySelectorAll('.cell'))[i].style.opacity='0.2'; } } },
        { name: 'Clear', desc: 'Clear random enemy mark', rarity: 'rare', act: () => { let e = filledOpp(); if(e.length){ let i = e[Math.floor(Math.random()*e.length)]; state.grid[i]=null; Array.from(UI.board.querySelectorAll('.cell'))[i].innerHTML=''; } } },
        { name: 'Fog', desc: 'Hide board briefly', rarity: 'epic', act: () => { UI.fogLayer.classList.add('active'); setTimeout(()=>UI.fogLayer.classList.remove('active'), 3000); } },
        { name: 'Double', desc: 'Play 2 marks this turn', rarity: 'legend', act: () => state.extraTurns++ },
        { name: 'Shuffle Mines', desc: 'Moves all mines', rarity: 'myth', act: () => { state.mines.clear(); let c=config.players===4?6:3; for(let i=0;i<c;i++){ let r; do{r=Math.floor(Math.random()*state.grid.length)}while(state.mines.has(r)); state.mines.add(r); } } }
      ];
      for(let i=0;i<20;i++) state.deck.push(state.deck[Math.floor(Math.random()*3)]);
      state.deck.sort(() => Math.random() - 0.5);
    }

    function empty() { let a=[]; for(let i=0;i<state.grid.length;i++) if(!state.grid[i] && !state.blocked.has(i)) a.push(i); return a; }
    function filledOpp() { let a=[]; let me=state.order[state.turnIndex]; for(let i=0;i<state.grid.length;i++) if(state.grid[i] && state.grid[i]!==me) a.push(i); return a; }

    function updateCardsTray() {
      if(config.mode !== 'randomizer' || state.winner || isBotTurn()) { UI.cardsTray.style.display = 'none'; return; }
      UI.cardsTray.style.display = 'flex';
      UI.cardsTray.innerHTML = '';
      let hand = state.hands.get(state.order[state.turnIndex]) || [];
      hand.forEach((c, idx) => {
        let el = document.createElement('div');
        el.className = 'card-item';
        el.innerHTML = `<div class="card-name">${c.name}</div><div class="card-desc">${c.desc}</div><div class="rar-bar rar-${c.rarity}"></div>`;
        el.onclick = () => {
          showMsg("Played: " + c.name, c.desc);
          c.act();
          hand.splice(idx, 1);
          if(!state.extraTurns) { state.turnIndex = (state.turnIndex + 1) % state.order.length; updateStatus(); if(isBotTurn()) setTimeout(botPlay, 600); }
          updateCardsTray();
        };
        UI.cardsTray.appendChild(el);
      });
    }

    applyTranslations();
    initGame();
  </script>
</body>
</html>

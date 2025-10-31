
---

## `README.md`

```markdown
# Games Hub — Selector

This file contains the **game selector shell** with loader and navigation.  
It’s the entry point where you choose which game to play.

---

## Features

- **Topbar navigation**  
- **Game cards** (currently only Tic‑Tac‑Toe)  
- **Load** button (shows loader overlay)  
- **Play** button (navigates to the game view)  
- **Loading overlay** with spinner  

---

## Code

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width,initial-scale=1" />
  <title>Games — Selector</title>
  <style>
    /* styles for selector, cards, loader */
  </style>
</head>
<body>
  <div class="app">
    <header class="topbar">
      <div class="brand">Games</div>
      <div class="spacer"></div>
      <button class="btn" data-nav="selector">Select game</button>
    </header>

    <!-- Selector view -->
    <main id="selector" class="view active">
      <h2>Choose a game</h2>
      <div class="grid">
        <article class="card">
          <h3>X and O (Tic‑Tac‑Toe)</h3>
          <p>Classic 3×3. Two players: X starts, O follows. First to align three wins.</p>
          <div class="actions">
            <button class="btn" data-load="tic-tac-toe">Load</button>
            <button class="btn" data-open="tic-tac-toe">Play</button>
          </div>
        </article>

        <article class="card">
          <h3>Coming soon</h3>
          <p>Placeholder for future games.</p>
          <div class="actions">
            <button class="btn" disabled>Load</button>
            <button class="btn" disabled>Play</button>
          </div>
        </article>
      </div>
    </main>
  </div>

  <!-- Loading overlay -->
  <div id="loading" class="loading">
    <div class="loader">
      <div class="spinner"></div>
      <div class="loader-label">Loading game…</div>
    </div>
  </div>

  <script>
    // JS for loader + navigation
    const loading = document.getElementById('loading');
    function showLoader(flag) {
      loading.classList.toggle('active', !!flag);
    }
    function fakeLoad(ms=700) {
      return new Promise(res => setTimeout(res, ms));
    }
    document.querySelector('[data-load="tic-tac-toe"]').addEventListener('click', async () => {
      showLoader(true);
      await fakeLoad(900);
      showLoader(false);
    });
    document.querySelector('[data-open="tic-tac-toe"]').addEventListener('click', async () => {
      showLoader(true);
      await fakeLoad(600);
      showLoader(false);
      // here you would navigate to Game1.html
      window.location.href = "Game1.html";
    });
  </script>
</body>
</html>

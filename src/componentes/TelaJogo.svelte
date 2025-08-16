<script>
  import { onMount } from 'svelte';

  const DIFFICULTIES = {
    Facil: { size: 10, wordsCount: 3 },
    Medio: { size: 12, wordsCount: 8 },
    Dificil: { size: 14, wordsCount: 10 },
  };

  const WORD_BANK = [
    'SVELTE',
    'COMPONENTE',
    'REACTIVO',
    'JAVASCRIPT',
    'TIPAGEM',
    'FUNCAO',
    'ESTADO',
    'PROPRIEDADE',
    'EVENTO',
    'MODULO',
    'ARQUITETURA',
    'PACOTE',
    'NAVEGADOR',
    'TEMPLATE',
    'OBSERVAVEL',
    'LOJA',
    'PROMISE',
    'ASSINCRONO',
    'MODERNO',
    'FRAMEWORK',
    'WEB',
    'CSS',
    'HTML',
    'DOM',
    'BUNDLE',
    'ROTEADOR',
    'SERVIDOR',
    'CLIENTE',
    'ARQUIVO',
    'IMPORT',
    'EXPORT',
    'BUILD',
    'DEPLOY',
    'NPM',
    'VITE',
    'ROLLUP',
    'ESLINT',
    'NODE',
    'GITHUB',
    'TESTE',
    'CI',
    'CD',
    'CLOUD',
    'API',
    'TOKEN',
  ];

  let difficultyKey = 'Facil';
  let grid = [];
  let size = DIFFICULTIES[difficultyKey].size;
  let words = [];
  let placements = {};
  let found = new Set();
  let selection = [];
  let gameWon = false;

  const DIRS = [
    { dr: 0, dc: 1 },
    { dr: 0, dc: -1 },
    { dr: 1, dc: 0 },
    { dr: -1, dc: 0 },
    { dr: 1, dc: 1 },
    { dr: -1, dc: -1 },
    { dr: 1, dc: -1 },
    { dr: -1, dc: 1 },
  ];

  function randInt(max) {
    return Math.floor(Math.random() * max);
  }
  function choice(arr) {
    return arr[randInt(arr.length)];
  }
  function normalize(w) {
    return w
      .toUpperCase()
      .normalize('NFD')
      .replace(/\p{Diacritic}/gu, '')
      .replace(/[^A-Z]/g, '');
  }
  function pickWords(count) {
    const shuffled = [...WORD_BANK].sort(() => Math.random() - 0.5);
    return shuffled.slice(0, count).map(normalize);
  }
  function emptyGrid(n) {
    return Array.from({ length: n }, () => Array.from({ length: n }, () => ''));
  }
  function inBounds(r, c, n) {
    return r >= 0 && c >= 0 && r < n && c < n;
  }
  function canPlace(grid, word, r0, c0, dir) {
    const n = grid.length;
    for (let i = 0; i < word.length; i++) {
      const r = r0 + dir.dr * i;
      const c = c0 + dir.dc * i;
      if (!inBounds(r, c, n)) return false;
      const cell = grid[r][c];
      if (cell && cell !== word[i]) return false;
    }
    return true;
  }
  function placeWord(grid, word) {
    const n = grid.length;
    for (let tries = 0; tries < 500; tries++) {
      const dir = choice(DIRS);
      const maxR =
        dir.dr === 1 ? n - word.length : dir.dr === -1 ? n - 1 : n - 1;
      const minR = dir.dr === -1 ? word.length - 1 : 0;
      const maxC =
        dir.dc === 1 ? n - word.length : dir.dc === -1 ? n - 1 : n - 1;
      const minC = dir.dc === -1 ? word.length - 1 : 0;
      const r0 = randInt(maxR - minR + 1) + minR;
      const c0 = randInt(maxC - minC + 1) + minC;

      if (!canPlace(grid, word, r0, c0, dir)) continue;

      const coords = [];
      for (let i = 0; i < word.length; i++) {
        const r = r0 + dir.dr * i;
        const c = c0 + dir.dc * i;
        grid[r][c] = word[i];
        coords.push({ r, c });
      }
      return coords;
    }
    return null;
  }
  function fillRandom(grid) {
    const letters = 'ABCDEFGHIJKLMNOPQRSTUVWXYZ';
    for (let r = 0; r < grid.length; r++) {
      for (let c = 0; c < grid.length; c++) {
        if (!grid[r][c]) grid[r][c] = letters[randInt(letters.length)];
      }
    }
  }
  function newGame() {
    found = new Set();
    gameWon = false;
    size = DIFFICULTIES[difficultyKey].size;
    words = pickWords(DIFFICULTIES[difficultyKey].wordsCount);
    placements = {};
    grid = emptyGrid(size);
    const toPlace = [...words].sort(() => Math.random() - 0.5);
    for (const w of toPlace) {
      const forward = Math.random() < 0.5 ? w : [...w].reverse().join('');
      const coords = placeWord(grid, forward);
      if (!coords) return newGame();
      placements[w] = [coords, [...coords].reverse()];
    }
    fillRandom(grid);
  }
  function coordEquals(a, b) {
    return a.r === b.r && a.c === b.c;
  }
  function arraysEqual(a, b) {
    if (a.length !== b.length) return false;
    for (let i = 0; i < a.length; i++)
      if (!coordEquals(a[i], b[i])) return false;
    return true;
  }

  // Ajuste feito aqui para adicionar/remover classe "selected"
  function handleClick(r, c) {
    const idx = selection.findIndex((p) => coordEquals(p, { r, c }));
    if (idx >= 0) {
      selection = selection.filter((_, i) => i !== idx);
    } else {
      selection = [...selection, { r, c }];
    }

    console.log(`Seleção atual: ${JSON.stringify(selection)}`);

    for (const w of words) {
      if (found.has(w)) continue;
      const [forward, backward] = placements[w];
      if (arraysEqual(selection, forward) || arraysEqual(selection, backward)) {
        found.add(w);
        found = new Set(found);
        selection = [];
        if (found.size === words.length) {
          gameWon = true;
        }
        break;
      }
    }
  }

  function isSelected(r, c) {
    return selection.some((p) => p.r === r && p.c === c);
  }
  function isFoundCell(r, c) {
    for (const w of found) {
      for (const path of placements[w]) {
        if (path.some((p) => p.r === r && p.c === c)) return true;
      }
    }
    return false;
  }
  onMount(newGame);
</script>

<div class="wrapper">
  <div class="topbar">
    <label>
      Dificuldade:
      <select bind:value={difficultyKey} on:change={newGame}>
        {#each Object.keys(DIFFICULTIES) as key}
          <option value={key}>{key}</option>
        {/each}
      </select>
    </label>
    <button on:click={newGame}>Novo jogo</button>
    <div class="status">{found.size}/{words.length} palavras</div>
  </div>

  <div class="grid" style={`grid-template-columns: repeat(${size}, 36px);`}>
    {#each Array(size) as _, r}
      {#each Array(size) as __, c}
        <div
          class="cell {isSelected(r, c) ? 'selected' : ''} {isFoundCell(r, c)
            ? 'found'
            : ''}"
          on:click={(event) => {
            event.target.classList.toggle('selected');
            handleClick(r, c);
          }}
        >
          {grid[r]?.[c]}
        </div>
      {/each}
    {/each}
  </div>

  <div class="panel">
    {#each words as w}
      <div class="word {found.has(w) ? 'done' : ''}">{w}</div>
    {/each}
  </div>

  {#if gameWon}
    <div class="victory">🎉 Parabéns! Você encontrou todas as palavras!</div>
  {/if}
</div>

<style>
  body {
    font-family: system-ui;
    margin: 0;
    padding: 0;
    background: #0b0f14;
    color: #e6e6e6;
  }
  .wrapper {
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
  }
  .topbar {
    display: flex;
    gap: 12px;
    align-items: center;
    flex-wrap: wrap;
    margin: 20px 0;
  }
  select,
  button {
    background: #121826;
    color: #e6e6e6;
    border: 1px solid #263046;
    border-radius: 12px;
    padding: 10px 14px;
    font-size: 14px;
    cursor: pointer;
    outline: none;
  }
  button:hover,
  select:hover {
    filter: brightness(1.05);
  }
  .grid {
    margin-top: 16px;
    display: grid;
    gap: 6px;
    user-select: none;
    touch-action: none;
  }
  .cell {
    width: 36px;
    height: 36px;
    display: grid;
    place-items: center;
    font-weight: 700;
    background: #121826;
    border: 1px solid #263046;
    color: white;
    border-radius: 8px;
  }
  .cell.selected {
    outline: 2px solid #6aa0ff;
    background: yellow;
    color: #121826;
  }
  .cell.found {
    background: #14301f;
    border-color: #1f6136;
  }
  .panel {
    margin-top: 16px;
    display: grid;
    grid-template-columns: repeat(auto-fit, minmax(180px, 1fr));
    gap: 8px;
  }
  .word {
    padding: 8px 12px;
    background: #121826;
    border: 1px dashed #263046;
    border-radius: 8px;
    font-weight: 600;
    color: white;
  }
  .word.done {
    text-decoration: line-through;
    opacity: 0.6;
  }
  .status {
    margin-top: 10px;
    font-weight: 600;
  }
  .victory {
    margin-top: 20px;
    font-size: 18px;
    font-weight: bold;
    color: #6aff91;
  }
</style>

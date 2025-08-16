<script lang="ts">
  export let aoSelecionarDificuldade: (
    nivel: 'facil' | 'medio' | 'dificil'
  ) => void;
  export let aoAbrirSobre: () => void;
</script>

<div class="container">
  <h1>Caça Palavras</h1>

  <div class="linha-botoes">
    <a
      style="text-align: center;"
      href="#"
      class="menu"
      on:click|preventDefault={() => aoSelecionarDificuldade('facil')}>Jogar</a
    >
  </div>

  <a href="#" class="menu sobre" on:click|preventDefault={aoAbrirSobre}>
    Sobre
  </a>
</div>

<style>
  .container {
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    height: 100vh;
  }

  h1 {
    font-size: 2em;
    margin-bottom: 20px;
    color: #333;
  }

  .linha-botoes {
    display: flex;
  }

  .menu {
    background-color: #4caf50;
    color: white;
    padding: 10px 25px;
    text-align: center;
    text-decoration: none;
    border: 2px solid #357a38;
    font-size: 1.1em;
    border-radius: 0;
  }

  .menu:first-child {
    border-top-left-radius: 8px;
    border-bottom-left-radius: 8px;
  }

  .menu:last-child {
    border-top-right-radius: 8px;
    border-bottom-right-radius: 8px;
  }

  .menu:hover {
    background-color: #45a049;
  }

  .sobre {
    margin-top: 15px;
    border-radius: 8px;
  }
</style>

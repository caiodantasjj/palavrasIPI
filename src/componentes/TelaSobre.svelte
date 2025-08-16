<script lang="ts">
  export let aoVoltar: () => void;
</script>

<div>
  <h1>Sobre o Projeto</h1>
  <img src="/assets/ifpe-logo.png" class="sobre" alt="Logo IFPE" />
  <p>Autores:</p>
  <ul>
  
   <div class="autores">
    <span>Caio</span>
    <span>Elioenai</span>
    <span>Randson</span>
    <span>Eloísa</span>
    <span>Kevelin</span>
  </div>
  </ul>
  <a href="#" class="menu" on:click|preventDefault={aoVoltar}>
    Voltar ao menu
  </a>
</div>

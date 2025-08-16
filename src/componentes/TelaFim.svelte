<script lang="ts">
  import { createEventDispatcher } from 'svelte';
  const dispatch = createEventDispatcher();

  function voltar() {
    dispatch('voltar');
  }
</script>

<div>
  <h2>Fim do Jogo!</h2>
  <button on:click={voltar}>Voltar ao Início</button>
</div>

<script lang="ts">
  import TelaInicial from './TelaInicial.svelte';
  import TelaJogo from './TelaJogo.svelte';
  import TelaFim from './TelaFim.svelte';
  import TelaSobre from './TelaSobre.svelte';

  let tela: 'inicio' | 'jogo' | 'fim' | 'sobre' = 'inicio';
  let dificuldade: 'facil' | 'medio' | 'dificil' = 'facil';

  function iniciarJogo(nivel: 'facil' | 'medio' | 'dificil') {
    dificuldade = nivel;
    tela = 'jogo';
  }

  function encerrarJogo() {
    tela = 'fim';
  }

  function voltarAoInicio() {
    tela = 'inicio';
  }

  function abrirSobre() {
    tela = 'sobre';
  }
</script>

{#if tela === 'inicio'}
  <TelaInicial
    aoSelecionarDificuldade={iniciarJogo}
    aoAbrirSobre={abrirSobre}
  />
{:else if tela === 'jogo'}
  <TelaJogo {dificuldade} aoEncerrar={encerrarJogo} />
{:else if tela === 'fim'}
  <TelaFim aoVoltar={voltarAoInicio} />
{:else if tela === 'sobre'}
  <TelaSobre aoVoltar={voltarAoInicio} />
{/if}

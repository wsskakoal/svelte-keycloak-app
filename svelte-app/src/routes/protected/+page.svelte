<script>
  import { user } from '../../lib/userStore';
  import { goto } from '$app/navigation';
  import { onMount } from 'svelte';

  onMount(() => {
    if (!$user) {
      goto('/');
    }
  });
</script>

{#if $user}
  <h1>Página Protegida</h1>
  <p>Conteúdo exclusivo para usuários autenticados.</p>
  <p>Nome: {$user.profile.name || $user.profile.sub}</p>
  <p>Email: {$user.profile.email}</p>
  <a href="/">Voltar para Home</a>
{:else}
  <p>Redirecionando...</p>
{/if}

<style>
  h1 {
    text-align: center;
    margin-top: 2rem;
  }
  p {
    text-align: center;
  }
</style>
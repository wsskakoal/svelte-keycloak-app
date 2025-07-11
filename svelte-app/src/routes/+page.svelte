<script>
// @ts-nocheck

  import { onMount } from 'svelte';
  import { UserManager, WebStorageStateStore } from 'oidc-client-ts';
  import { goto } from '$app/navigation';
  import { writable } from 'svelte/store';

  // Configuração do Keycloak OIDC
  const userManager = new UserManager({
    authority: 'http://localhost:8080/realms/myapp-realm',
    client_id: 'svelte-app',
    client_secret: 'FTw96Zd5sZeuKySmJZI4vT0mvY4KMhQz', // Substitua pelo Client Secret do Keycloak
    redirect_uri: 'http://localhost:5173/callback',
    post_logout_redirect_uri: 'http://localhost:5173',
    response_type: 'code',
    scope: 'openid profile email',
    userStore: new WebStorageStateStore({ store: window.localStorage })
  });

  // Store para gerenciar o estado do usuário
  export const user = writable(null);

  // Verificar se o usuário está autenticado ao carregar a página
  onMount(async () => {
    try {
      const userData = await userManager.getUser();
      if (userData) {
        user.set(userData);
      }

      // Lidar com o callback de autenticação
      if (window.location.pathname === '/callback') {
        const userData = await userManager.signinRedirectCallback();
        user.set(userData);
        goto('/');
      }
    } catch (error) {
      console.error('Erro ao carregar usuário:', error);
    }
  });

  // Função para iniciar o login
  async function login() {
    try {
      await userManager.signinRedirect();
    } catch (error) {
      console.error('Erro ao iniciar login:', error);
    }
  }

  // Função para logout
  async function logout() {
    try {
      await userManager.signoutRedirect();
    } catch (error) {
      console.error('Erro ao fazer logout:', error);
    }
  }
</script>

<main>
  {#if $user}
    <h1>Bem-vindo, {$user.profile.name || $user.profile.sub}</h1>
    <p>Email: {$user.profile.email}</p>
    <button on:click={logout}>Logout</button>
    <a href="/protected">Acessar Página Protegida</a>
  {:else}
    <h1>Aplicação Svelte com Keycloak</h1>
    <button on:click={login}>Login</button>
  {/if}
</main>

<style>
  main {
    text-align: center;
    padding: 2rem;
  }
  button {
    padding: 0.5rem 1rem;
    margin: 1rem;
  }
</style>
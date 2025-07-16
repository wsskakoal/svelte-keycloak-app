<script>
// @ts-nocheck
  import { onMount } from 'svelte';
  import { UserManager, WebStorageStateStore } from 'oidc-client-ts';
  import { goto } from '$app/navigation';
  import { user } from '../lib/userStore';
  import {jwtDecode} from 'jwt-decode';

  // @ts-ignore
  let userManager;

  onMount(() => {
    if (typeof window !== 'undefined') {
      userManager = new UserManager({
        authority: 'http://localhost:8080/realms/alfa-contabilidade',
        client_id: 'alfa-contabilidade-openid',
        client_secret: 'CFthSJmeLpsKlSL58uQS8mtCrAoSHpQA', // Substitua pelo Client Secret do Keycloak
        redirect_uri: 'http://localhost:5173/callback',
        post_logout_redirect_uri: 'http://localhost:5173',
        response_type: 'code',
        scope: 'openid profile email',
        userStore: new WebStorageStateStore({ store: window.localStorage })
      });

      userManager.getUser().then(userData => {
        if (userData) {
          // @ts-ignore
          user.set(userData);
      
          // Decodifica o token de acesso
    const decodedToken = jwtDecode(userData.access_token);

    // Exibe as permissões (roles) do realm
    console.log('Realm Roles:', decodedToken.realm_access?.roles);

    // Exibe as permissões por client (se configuradas no client)
    console.log('Client Roles:', decodedToken.resource_access?.['alfa-contabilidade-openid']?.roles);

    // Exemplo: checar se tem uma role específica
    if (decodedToken.realm_access?.roles.includes('financeiro')) {
      console.log('Usuário é financeiro');
    }
        }
      }).catch(error => {
        console.error('Erro ao carregar usuário:', error);
      });
    }

    return () => {
      userManager = null;
    };
  });

  async function login() {
    // @ts-ignore
    if (userManager) {
      try {
        await userManager.signinRedirect();
      } catch (error) {
        console.error('Erro ao iniciar login:', error);
      }
    }
  }

  async function logout() {
    // @ts-ignore
    if (userManager) {
      try {
        await userManager.signoutRedirect();
      } catch (error) {
        console.error('Erro ao fazer logout:', error);
      }
    }
  }
</script>

<main>
  {#if $user}
    <h1>Bem-vindo, {$user.profile.name || $user.profile.sub}</h1>
    <p>Email: {$user.profile.email}</p>
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
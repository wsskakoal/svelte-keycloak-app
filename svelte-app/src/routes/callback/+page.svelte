<script>
  import { onMount } from 'svelte';
  import { UserManager, WebStorageStateStore } from 'oidc-client-ts';
  import { goto } from '$app/navigation';
  import { user } from '../../lib/userStore';

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

      userManager.signinRedirectCallback().then(userData => {
        console.log(userData);

        alert(`Set user: ${(userData.profile.name || userData.profile.sub)}`);
        // @ts-ignore
        user.set(userData);
        goto('/');
      }).catch(error => {
        console.error('Erro no callback de autenticação:', error);
        goto('/'); // Redirecionar mesmo em caso de erro, ou tratar de forma personalizada
      });
    }
  });
</script>

<main>
  <p>Processando login...</p>
</main>

<style>
  main {
    text-align: center;
    padding: 2rem;
  }
</style>
# svelte-keycloak-app


Instruções:

* Substitua SEU_CLIENT_SECRET_AQUI em src/App.svelte pelo Client Secret obtido no Keycloak.

*  src/App.svelte: Página principal com login/logout.
*  src/routes/protected/+page.svelte: Página protegida acessível apenas para usuários autenticados.
*  src/routes/+layout.svelte: Layout base.
*  vite.config.js: Configuração do Vite para rodar na porta 5173.

* Execute a aplicação Svelte com:
npm run dev


Acesse http://localhost:5173 no navegador.

## Testar a Autenticação e Recuperação de Senha
### Login:
* Clique no botão "Login" na aplicação Svelte.
Você será redirecionado para a página de login do Keycloak.
* Faça login com testuser/test123.
Após o login, você será redirecionado para a aplicação Svelte, onde verá as informações do usuário.

### Recuperação de Senha:
* Na página de login do Keycloak (http://localhost:8080/realms/myapp-realm/protocol/openid-connect/auth), clique em "Forgot password".
* Insira o e-mail testuser@example.com.
* Verifique o e-mail configurado (necessário configurar SMTP corretamente) para receber o link de redefinição de senha.
* Siga o link para redefinir a senha.

### Acessar Página Protegida:
* Após o login, clique em "Acessar Página Protegida" para visualizar a página restrita.

# Resumo
* O Keycloak foi configurado com Docker, um realm myapp-realm, um cliente svelte-app e um fluxo de recuperação de senha.
link keyclock: https://github.com/wsskakoal/keycloak-postgress

* A aplicação Svelte usa oidc-client-ts para autenticação OIDC, com uma página inicial para login/logout e uma página protegida.

* Para ambientes de produção, configure um banco de dados robusto (como PostgreSQL em produção) e use HTTPS para o Keycloak e a aplicação Svelte.


* Use o comando docker cp para copiar a pasta myapp-theme:
docker cp keycloak-theme/myapp-theme keyclock-postgress-keycloak-1:/opt/keycloak/themes/

docker cp mytheme keyclock-postgress-keycloak-1:/opt/keycloak/themes/

O exemplo anterior mostrou uma norma início de sessão, que exige que o cliente contactar o fornecedor de identidade e o serviço do Azure de back-end, sempre que a aplicação for iniciada. Este método é ineficaz e podem ter problemas relacionados com a utilização se muitos clientes tentam iniciar a sua aplicação em simultâneo. É uma melhor abordagem para colocar em cache o token de autorização devolvido pelo serviço do Azure e tente utilizar este primeiro antes de utilizar um fornecedor com base no início de sessão.

> [!NOTE]
> Pode colocar em cache o token emitido pelo serviço do Azure, independentemente se estiver a utilizar autenticação de cliente geridos ou geridos pelo serviço de back-end. Este tutorial utiliza a autenticação de serviço gerido.
>
>

1. Abra o ficheiro ToDoActivity.java e adicione as seguintes declarações de importação:

    ```java
    import android.content.Context;
    import android.content.SharedPreferences;
    import android.content.SharedPreferences.Editor;
    ```

2. Adicione as seguintes para o `ToDoActivity` classe.

    ```java
    public static final String SHAREDPREFFILE = "temp";
    public static final String USERIDPREF = "uid";
    public static final String TOKENPREF = "tkn";
    ```

3. No ficheiro ToDoActivity.java, adicione a seguinte definição para o `cacheUserToken` método.

    ```java
    private void cacheUserToken(MobileServiceUser user)
    {
        SharedPreferences prefs = getSharedPreferences(SHAREDPREFFILE, Context.MODE_PRIVATE);
        Editor editor = prefs.edit();
        editor.putString(USERIDPREF, user.getUserId());
        editor.putString(TOKENPREF, user.getAuthenticationToken());
        editor.commit();
    }
    ```

    Este método armazena o ID de utilizador e o token num ficheiro preferência que está marcado como privado. Isto deve proteger o acesso à cache para que as outras aplicações no dispositivo não tem acesso para o token. A preferência é uma para a aplicação. No entanto, se alguém obtiver o acesso ao dispositivo, é possível que estes podem ter acesso à cache de token através de outros meios.

   > [!NOTE]
   > Pode proteger ainda mais o token com a encriptação, se o token acesso aos seus dados é considerado altamente confidencial e alguém obter acesso ao dispositivo. Uma solução completamente segura está fora do âmbito deste tutorial, no entanto e depende dos requisitos de segurança.
   >
   >

4. No ficheiro ToDoActivity.java, adicione a seguinte definição para o `loadUserTokenCache` método.

    ```java
    private boolean loadUserTokenCache(MobileServiceClient client)
    {
        SharedPreferences prefs = getSharedPreferences(SHAREDPREFFILE, Context.MODE_PRIVATE);
        String userId = prefs.getString(USERIDPREF, null);
        if (userId == null)
            return false;
        String token = prefs.getString(TOKENPREF, null);
        if (token == null)
            return false;

        MobileServiceUser user = new MobileServiceUser(userId);
        user.setAuthenticationToken(token);
        client.setCurrentUser(user);

        return true;
    }
    ```

5. No *ToDoActivity.java* de ficheiros, substitua o `authenticate` e `onActivityResult` métodos com os seguintes, que utiliza uma cache de token. Altere o fornecedor de início de sessão se pretender utilizar uma conta diferente da Google.

    ```java
    private void authenticate() {
        // We first try to load a token cache if one exists.
        if (loadUserTokenCache(mClient))
        {
            createTable();
        }
        // If we failed to load a token cache, login and create a token cache
        else
        {
            // Login using the Google provider.
            mClient.login(MobileServiceAuthenticationProvider.Google, "{url_scheme_of_your_app}", GOOGLE_LOGIN_REQUEST_CODE);
        }
    }

    @Override
    protected void onActivityResult(int requestCode, int resultCode, Intent data) {
        // When request completes
        if (resultCode == RESULT_OK) {
            // Check the request code matches the one we send in the login request
            if (requestCode == GOOGLE_LOGIN_REQUEST_CODE) {
                MobileServiceActivityResult result = mClient.onActivityResult(data);
                if (result.isLoggedIn()) {
                    // login succeeded
                    createAndShowDialog(String.format("You are now logged in - %1$2s", mClient.getCurrentUser().getUserId()), "Success");
                    cacheUserToken(mClient.getCurrentUser());
                    createTable();
                } else {
                    // login failed, check the error message
                    String errorMessage = result.getErrorMessage();
                    createAndShowDialog(errorMessage, "Error");
                }
            }
        }
    }
    ```

6. Crie a autenticação de aplicação e teste utilizando uma conta válida. Execute, pelo menos, duas vezes. Durante a primeira execução, deverá receber um pedido para iniciar sessão e criar a cache de token. Depois disso, cada execução tenta carregar a cache de token de autenticação. Não deve ser necessário iniciar sessão.

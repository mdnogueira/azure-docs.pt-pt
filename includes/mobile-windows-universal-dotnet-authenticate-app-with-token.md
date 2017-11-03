
1. No ficheiro de projeto MainPage.xaml.cs, adicione a seguinte **utilizando** instruções:
   
        using System.Linq;        
        using Windows.Security.Credentials;
2. Substitua o **AuthenticateAsync** método com o seguinte código:
   
        private async System.Threading.Tasks.Task<bool> AuthenticateAsync()
        {
            string message;
            bool success = false;
   
            // This sample uses the Facebook provider.
            var provider = MobileServiceAuthenticationProvider.Facebook;
   
            // Use the PasswordVault to securely store and access credentials.
            PasswordVault vault = new PasswordVault();
            PasswordCredential credential = null;
   
            try
            {
                // Try to get an existing credential from the vault.
                credential = vault.FindAllByResource(provider.ToString()).FirstOrDefault();
            }
            catch (Exception)
            {
                // When there is no matching resource an error occurs, which we ignore.
            }
   
            if (credential != null)
            {
                // Create a user from the stored credentials.
                user = new MobileServiceUser(credential.UserName);
                credential.RetrievePassword();
                user.MobileServiceAuthenticationToken = credential.Password;
   
                // Set the user from the stored credentials.
                App.MobileService.CurrentUser = user;
   
                // Consider adding a check to determine if the token is 
                // expired, as shown in this post: http://aka.ms/jww5vp.
   
                success = true;
                message = string.Format("Cached credentials for user - {0}", user.UserId);
            }
            else
            {
                try
                {
                    // Login with the identity provider.
                    user = await App.MobileService
                        .LoginAsync(provider);
   
                    // Create and store the user credentials.
                    credential = new PasswordCredential(provider.ToString(),
                        user.UserId, user.MobileServiceAuthenticationToken);
                    vault.Add(credential);
   
                    success = true;
                    message = string.Format("You are now logged in - {0}", user.UserId);
                }
                catch (MobileServiceInvalidOperationException)
                {
                    message = "You must log in. Login Required";
                }
            }
   
            var dialog = new MessageDialog(message);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
   
            return success;
        }
   
    Nesta versão do **AuthenticateAsync**, a aplicação tenta utilizar as credenciais armazenadas no **PasswordVault** para aceder ao serviço. Um regular início de sessão é também efetuado quando não existe nenhuma credencial armazenada.
   
   > [!NOTE]
   > Um token em cache pode estar expirado e expiração do token também pode ocorrer após a autenticação quando a aplicação está a ser utilizado. Para saber como determinar se um token tiver expirado, consulte [Verifique a existência de tokens de autenticação expirada](http://aka.ms/jww5vp). Para uma solução de processamento de erros de autorização relacionados com a expiração de tokens, consulte o post [colocação em cache e tokens expirados em Mobile Services do Azure de processamento gerido SDK](http://blogs.msdn.com/b/carlosfigueira/archive/2014/03/13/caching-and-handling-expired-tokens-in-azure-mobile-services-managed-sdk.aspx). 
   > 
   > 
3. Reinicie a aplicação duas vezes.
   
    Tenha em atenção que no arranque do primeiro início de sessão com o fornecedor é novamente necessária. No entanto, na segunda reinicialização são utilizadas as credenciais em cache e início de sessão é ignorada. 


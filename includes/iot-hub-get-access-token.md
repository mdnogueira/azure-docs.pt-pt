## <a name="obtain-an-azure-resource-manager-token"></a>Obter um token do Azure Resource Manager
Azure Active Directory tem de autenticar todas as tarefas que executam em recursos utilizando o Azure Resource Manager. O exemplo apresentado aqui utiliza a autenticação de palavra-passe, para ver outras abordagens [pedidos de autenticação do Azure Resource Manager][lnk-authenticate-arm].

1. Adicione o seguinte código para o **Main** método em Program.cs, para obter um token do Azure AD com o id da aplicação e a palavra-passe.
   
    ```
    var authContext = new AuthenticationContext(string.Format  
      ("https://login.microsoftonline.com/{0}", tenantId));
    var credential = new ClientCredential(applicationId, password);
    AuthenticationResult token = authContext.AcquireTokenAsync
      ("https://management.core.windows.net/", credential).Result;
   
    if (token == null)
    {
      Console.WriteLine("Failed to obtain the token");
      return;
    }
    ```
2. Criar um **o ResourceManagementClient** objeto que utiliza o token, adicionando o seguinte código ao fim do **Main** método:
   
    ```
    var creds = new TokenCredentials(token.AccessToken);
    var client = new ResourceManagementClient(creds);
    client.SubscriptionId = subscriptionId;
    ```
3. Cria ou obtém uma referência para o grupo de recursos que está a utilizar:
   
    ```
    var rgResponse = client.ResourceGroups.CreateOrUpdate(rgName,
        new ResourceGroup("East US"));
    if (rgResponse.Properties.ProvisioningState != "Succeeded")
    {
      Console.WriteLine("Problem creating resource group");
      return;
    }
    ```

[lnk-authenticate-arm]: https://msdn.microsoft.com/library/azure/dn790557.aspx
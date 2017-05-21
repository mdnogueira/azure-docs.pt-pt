## <a name="configure-a-deployment-user"></a>Configurar um utilizador de implementação  

Para FTP e Git local, tem de ter configurado um utilizador de implementação no servidor, para autenticar a implementação.

> [!NOTE]
> Para implementação de FTP e Git Local numa Aplicação Web, é necessário um utilizador de implementação.
> O `username` e a `password` são ao nível da conta. São diferentes das credenciais da sua Subscrição do Azure.
>

Execute o comando [az appservice web deployment user set](/cli/azure/appservice/web/deployment/user#set) para criar as credenciais da implementação.

```azurecli
az appservice web deployment user set --user-name <username> --password <password>
```

O nome de utilizador tem de ser exclusivo e a palavra-passe tem de ser segura. Se obtiver o erro ` 'Conflict'. Details: 409`, altere o nome de utilizador. Se obtiver o ` 'Bad Request'. Details: 400` erro, utilize uma palavra-passe mais forte.

Só precisa de criar este utilizador de implementação uma vez; pode utilizá-lo em todas as suas implementações do Azure.

Registe o nome de utilizador e a palavra-passe, pois vão ser utilizados num passo posterior quando implementar a aplicação.
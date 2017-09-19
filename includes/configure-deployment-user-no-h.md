No Cloud Shell, crie credenciais de implementação com o comando [az webapp deployment user set](/cli/azure/webapp/deployment/user#set).

Para implementação de FTP e Git local numa aplicação Web, é necessário um utilizador de implementação. O nome de utilizador e a palavra-passe estão ao nível da conta. _São diferentes das credenciais da sua subscrição do Azure._

No comando a seguir, substitua  *\<nome de utilizador >* e  *\<palavra-passe >* por um novo nome de utilizador e palavra-passe. O nome do utilizador tem de ser exclusivo. A palavra-passe tem de ter, pelo menos, oito carateres, com dois dos seguintes três elementos: letras, números, símbolos. 

```azurecli-interactive
az webapp deployment user set --user-name <username> --password <password>
```

Se obtiver o erro ` 'Conflict'. Details: 409`, altere o nome de utilizador. Se obtiver o ` 'Bad Request'. Details: 400` erro, utilize uma palavra-passe mais forte.

Este utilizador de implementação só é criado uma vez; pode utilizá-lo em todas as suas implementações do Azure.

> [!NOTE]
> Registe o nome de utilizador e palavra-passe. Irá necessitar deles para implementar a aplicação Web mais tarde.
>
>
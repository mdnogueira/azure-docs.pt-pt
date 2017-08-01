Configure a implementação local do Git para a aplicação Web com o comando [az webapp deployment source config-local-git](/cli/azure/webapp/deployment/source#config-local-git).

O Serviço de Aplicações suporta várias formas de implementar conteúdo numa aplicação Web, como FTP, Git local, GitHub, Visual Studio Team Services e Bitbucket. Para este início rápido, pode implementar com o Git local. Isto significa que implementa com um comando do Git para enviar a partir de um repositório local para um repositório no Azure. 

No comando seguinte, substitua  *\<app_name >* pelo nome da sua aplicação Web.

```azurecli-interactive
az webapp deployment source config-local-git --name <app_name> --resource-group myResourceGroup --query url --output tsv
```

A saída tem o seguinte formato:

```bash
https://<username>@<app_name>.scm.azurewebsites.net:443/<app_name>.git
```

O `<username>` é o [utilizador de implementação](#configure-a-deployment-user) que criou num passo anterior.

Copie o URI apresentado. Vai utilizá-lo no próximo passo.

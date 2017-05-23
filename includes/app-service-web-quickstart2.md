Utilize o comando [az appservice web create](/cli/azure/appservice/web#create) para criar a Aplicação Web. No comando seguinte, substitua o nome exclusivo da sua aplicação, onde vir o marcador de posição `<app_name>`. `<app_name>` é utilizado no site DNS predefinido da aplicação Web. Se `<app_name>` não for exclusivo, obtém a mensagem de erro amigável “O Website com o nome <nome_da_aplicação> já existe."

```azurecli
az appservice web create --name <app_name> --resource-group myResourceGroup --plan quickStartPlan
```

Quando a Aplicação Web tiver sido criada, a CLI do Azure mostra informações semelhantes ao seguinte exemplo:

```json
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app_name>.azurewebsites.net",
  "enabled": true,
  "enabledHostNames": [
    "<app_name>.azurewebsites.net",
    "<app_name>.scm.azurewebsites.net"
  ],
  "gatewaySiteName": null,
  "hostNameSslStates": [
    {
      "hostType": "Standard",
      "name": "<app_name>.azurewebsites.net",
      "sslState": "Disabled",
      "thumbprint": null,
      "toUpdate": null,
      "virtualIp": null
    }
    < JSON data removed for brevity. >
}
```

Navegue para o site (`http://<app_name>.azurewebsites.net`) para ver a sua aplicação Web criada recentemente.

![app-service-web-service-created](../articles/app-service-web/media/app-service-web-get-started-nodejs-poc/app-service-web-service-created.png)


## <a name="configure-local-git-deployment"></a>Configurar a implementação do Git local

O Serviço de Aplicações suporta várias formas de implementar conteúdo numa aplicação Web, como FTP, Git local, GitHub, Visual Studio Team Services e Bitbucket. 

Para este início rápido, pode implementar com o Git local. Isto significa que implementa com um comando do Git para enviar a partir de um repositório local para um repositório no Azure. 

Utilize o comando [az appservice web source-control config-local-git](/cli/azure/appservice/web/source-control#config-local-git) para configurar o acesso ao Git local para a aplicação Web.

```azurecli
az appservice web source-control config-local-git --name <app_name> --resource-group myResourceGroup --query url --output tsv
```

A saída tem o seguinte formato:

```
https://<username>@<app_name>.scm.azurewebsites.net:443/<app_name>.git
```

Guarde o URI mostrado. Irá utilizá-lo no próximo passo. O `<username>` é o [utilizador de implementação](#configure-a-deployment-user) que criou num passo anterior.

## <a name="push-to-azure-from-git"></a>Enviar para o Azure a partir do Git

Adicione um Azure remoto para o seu repositório do Git local.

```bash
git remote add azure <URI from previous step>
```

Envie para o Azure remoto para implementar a sua aplicação. É-lhe pedida a palavra-passe que indicou quando criou o utilizador de implementação, anteriormente. Confirme que introduz a palavra-passe que criou em [Configurar um utilizador de implementação](#configure-a-deployment-user) e não a que utilizou para iniciar sessão no portal do Azure.

```azurecli
git push azure master
```

O comando anterior apresenta informações semelhantes ao exemplo seguinte:

```bash
Counting objects: 13, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (11/11), done.
Writing objects: 100% (13/13), 2.07 KiB | 0 bytes/s, done.
Total 13 (delta 2), reused 0 (delta 0)
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id 'cc39b1e4cb'.
remote: Generating deployment script.
remote: Generating deployment script for Web Site
remote: Generated deployment script files
remote: Running deployment command...
remote: Handling Basic Web Site deployment.
remote: KuduSync.NET from: 'D:\home\site\repository' to: 'D:\home\site\wwwroot'
remote: Deleting file: 'hostingstart.html'
remote: Copying file: '.gitignore'
remote: Copying file: 'LICENSE'
remote: Copying file: 'README.md'
remote: Finished successfully.
remote: Running post deployment command(s)...
remote: Deployment successful.
To https://<app_name>.scm.azurewebsites.net/<app_name>.git
 * [new branch]      master -> master
```

### <a name="browse-to-the-app"></a>Navegar para a aplicação


Navegue para a aplicação implementada:

```
http://<app_name>.azurewebsites.net
```


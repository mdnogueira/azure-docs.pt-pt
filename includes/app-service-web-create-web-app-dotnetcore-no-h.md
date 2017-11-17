No Cloud Shell, crie uma [aplicação Web](../articles/app-service/containers/app-service-linux-intro.md) no plano do Serviço de Aplicações `myAppServicePlan` com o comando [az webapp create](/cli/azure/webapp#create). 

No exemplo seguinte, substitua `<app_name>` com um nome de aplicação exclusivo global (carateres válidos são `a-z`, `0-9`, e `-`). O tempo de execução está definido como `dotnetcore|1.1`. Para ver os tempos de execução todos suportados, execute [az webapp lista-tempos de execução](/cli/azure/webapp#list-runtimes). 

```azurecli-interactive
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app_name> --runtime "dotnetcore|1.1" --deployment-local-git
```

Quando a aplicação web tiver sido criada, a CLI do Azure mostra resultado semelhante ao seguinte exemplo:

```json
Local git is configured with url of 'https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git'
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app_name>.azurewebsites.net",
  "deploymentLocalGitUrl": "https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git",
  "enabled": true,
  < JSON data removed for brevity. >
}
```

Criou uma aplicação web em branco no contentor de Linux, com a implementação de git ativada.

> [!NOTE]
> O URL do Git remoto é apresentado no `deploymentLocalGitUrl` propriedade, com o formato `https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git`. Guarde este URL como precisará dela mais tarde.
>

A Shell de nuvem, criar uma aplicação web no `myAppServicePlan` plano de serviço de aplicações com o [az webapp criar](/cli/azure/webapp#create) comando. 

No exemplo seguinte, substitua `<app_name>` com um nome de aplicação exclusivo global (carateres válidos são `a-z`, `0-9`, e `-`). O tempo de execução está definido como `python|3.4`. Para ver os tempos de execução todos suportados, execute [az webapp lista-tempos de execução](/cli/azure/webapp#list-runtimes). 

```azurecli-interactive
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app_name> --runtime "python|3.4" --deployment-local-git
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

Criou uma vazio nova aplicação web, com a implementação de git ativada.

> [!NOTE]
> O URL do Git remoto é apresentado no `deploymentLocalGitUrl` propriedade, com o formato `https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git`. Guarde este URL como precisará dela mais tarde.
>

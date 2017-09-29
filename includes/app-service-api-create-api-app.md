
Crie uma aplicação no plano do Serviço de Aplicações `myAppServicePlan` com o comando [az webapp create](/cli/azure/appservice/web#create). 

A aplicação Web fornece um espaço de alojamento para a API e um URL para ver a aplicação implementada.

No comando a seguir, substitua  *\<app_name >* com um nome exclusivo. Se `<app_name>` não for exclusivo, obtém a mensagem de erro “O Website com o nome <nome_da_aplicação> já existe". O URL predefinido da aplicação Web é `https://<app_name>.azurewebsites.net`. 

```azurecli-interactive
az webapp create --name <app_name> --resource-group myResourceGroup --plan myAppServicePlan
```

Quando a aplicação Web tiver sido criada, a CLI do Azure mostra informações semelhantes ao seguinte exemplo:

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
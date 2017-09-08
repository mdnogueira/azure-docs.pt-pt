Crie uma [aplicação Web](../articles/app-service-web/app-service-web-overview.md) no plano do `myAppServicePlan`Serviço de Aplicações com o comando[az webapp create](/cli/azure/webapp#create). 

A aplicação Web fornece um espaço de alojamento para o código e um URL para ver a aplicação implementada.

No comando a seguir, substitua  *\<app_name>* com um nome exclusivo (os carateres válidos são `a-z`, `0-9` e `-`). Se `<app_name>` não for exclusivo, obtém a mensagem de erro “O Website com o nome <nome_da_aplicação> já existe". O URL predefinido da aplicação Web é `https://<app_name>.azurewebsites.net`. 

```azurecli-interactive
az webapp create --name <app_name> --resource-group myResourceGroup --plan myAppServicePlan --deployment-local-git
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

Navegue para o site para ver a sua aplicação Web criada recentemente.

```bash
http://<app_name>.azurewebsites.net
```

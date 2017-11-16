## <a name="deploy-your-function-code"></a>Implementar o código de função  

Existem várias formas de criar o código de função na sua aplicação Function App nova. Este tópico liga-se a um repositório de exemplos do GitHub. Tal como anteriormente, no código seguinte, substitua o marcador de posição `<app_name>` pelo nome da aplicação Function App que criou. 

```azurecli-interactive
az functionapp deployment source config --name <app_name> --resource-group myResourceGroup --branch master \
--repo-url https://github.com/Azure-Samples/functions-quickstart \
--manual-integration 
```
Depois da origem de implementação tiver sido definida, a CLI do Azure mostra as informações semelhante ao seguinte exemplo (valores nulos removidos para legibilidade):

```json
{
  "branch": "master",
  "deploymentRollbackEnabled": false,
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/...",
  "isManualIntegration": true,
  "isMercurial": false,
  "kind": null,
  "name": "quickstart",
  "repoUrl": "https://github.com/Azure-Samples/functions-quickstart",
  "resourceGroup": "myResourceGroup",
  "type": "Microsoft.Web/sites/sourcecontrols"
}
```

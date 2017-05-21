## <a name="create-a-resource-group"></a>Criar um grupo de recursos

 Os [grupos de recursos do Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups) são contentores lógicos nos quais são geridos recursos como aplicações Web e bases de dados. Os recursos são implementados, atualizados e eliminados nos grupos de recursos.

Crie um grupo de recursos com o comando [az group create](/cli/azure/group#create).

```azurecli
az group create --name myResourceGroup --location westeurope
```

Para ver as localizações disponíveis, utilize o comando `az appservice list-locations`. Geralmente, os recursos são criados numa região perto de si.

## <a name="create-an-azure-app-service-plan"></a>Criar um plano do Serviço de Aplicações do Azure

Crie um [plano do Serviço de Aplicações](../articles/app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md) “GRATUITO” com o comando [az appservice plan create](/cli/azure/appservice/plan#create).

[!INCLUDE [app-service-plan](app-service-plan.md)]

O comando seguinte cria um plano do Serviço de Aplicações com o nome `quickStartPlan`, que utiliza o escalão de preços **Gratuito**.

```azurecli
az appservice plan create --name quickStartPlan --resource-group myResourceGroup --sku FREE
```

Quando o plano do Serviço de Aplicações tiver sido criado, a CLI do Azure mostra informações semelhantes ao seguinte exemplo:

```json
{ 
  "adminSiteName": null,
  "appServicePlanName": "quickStartPlan",
  "geoRegion": "West Europe",
  "hostingEnvironmentProfile": null,
  "id": "/subscriptions/0000-0000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/quickStartPlan",
  "kind": "app",
  "location": "West Europe",
  "maximumNumberOfWorkers": 1,
  "name": "quickStartPlan",
  < JSON data removed for brevity. >
  "targetWorkerSizeId": 0,
  "type": "Microsoft.Web/serverfarms",
  "workerTierName": null
} 
```

## <a name="create-a-web-app"></a>Criar uma aplicação Web
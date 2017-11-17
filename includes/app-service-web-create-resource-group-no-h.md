No Cloud Shell, crie um grupo de recursos com o comando [az group create](/cli/azure/group#create).

[!INCLUDE [resource group intro text](resource-group.md)]

O exemplo seguinte cria um grupo de recursos com o nome *myResourceGroup*, na localização *Europa Ocidental*. Para ver localizações todos suportadas para o App Service, execute o `az appservice list-locations` comando.

```azurecli-interactive
az group create --name myResourceGroup --location "West Europe"
```

Geralmente, o grupo de recursos e os recursos são criados numa região perto de si. 
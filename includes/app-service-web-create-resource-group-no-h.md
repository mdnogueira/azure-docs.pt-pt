No Cloud Shell, crie um grupo de recursos com o comando [az group create](/cli/azure/group#create).

[!INCLUDE [resource group intro text](resource-group.md)]

O exemplo seguinte cria um grupo de recursos com o nome *myResourceGroup*, na localização *Europa Ocidental*.

```azurecli-interactive
az group create --name myResourceGroup --location "West Europe"
```

Geralmente, o grupo de recursos e os recursos são criados numa região perto de si. Para ver todos os locais suportados para aplicações Web do Azure, execute o comando `az appservice list-locations`. 
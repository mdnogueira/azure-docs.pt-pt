## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos com o comando [az group create](/cli/azure/group#create). Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure, como aplicações Function App, bases de dados e contas de armazenamento, são implementados e geridos.

O exemplo seguinte cria um grupo de recursos com o nome `myResourceGroup`.  
Se não estiver a utilizar a Shell de nuvem, inicie sessão com a primeira `az login`.

```azurecli-interactive
az group create --name myResourceGroup --location westeurope
```
Geralmente, o grupo de recursos e os recursos são criados numa região perto de si. Para ver localizações todos suportadas para planos do App Service, execute o [az appservice lista-localizações](/cli/azure/appservice#az_appservice_list_locations) comando.
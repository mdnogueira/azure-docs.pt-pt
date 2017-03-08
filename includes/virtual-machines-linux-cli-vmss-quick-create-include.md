## <a name="prerequisites"></a>Pré-requisitos

Se ainda não o fez, obtenha uma [versão de avaliação gratuita de subscrição do Azure](https://azure.microsoft.com/pricing/free-trial/) e instale a [CLI 2.0 do Azure](https://docs.microsoft.com/cli/azure/install-az-cli2).

## <a name="create-the-scale-set"></a>Crie o conjunto de dimensionamento

Primeiro, crie um grupo de recursos para implementar o conjunto de dimensionamento definido para:

```azurecli
az group create --location westus --name myResourceGroup
```

Agora, utilize o comando `az vmss create` para criar o conjunto de dimensionamento. O exemplo seguinte cria um conjunto de dimensionamento do Linux com o nome `myvmss`, no grupo de recursos com o nome `myrg`:

```azurecli
az vmss create --resource-group myResourceGroup --name myVmss \
    --image UbuntuLTS --admin-username azureuser \
    --authentication-type password --admin-password P4$$w0rd
```

O exemplo seguinte cria um conjunto de dimensionamento do Windows definido com a mesma configuração:

```azurecli
az vmss create --resource-group myResourceGroup --name myVmss \
    --image Win2016Datacenter --admin-username azureuser \
    --authentication-type password --admin-password P4$$w0rd
```

Se pretende escolher uma imagem de OS diferente, pode ver as imagens disponíveis com o comando `az vm image list` ou `az vm image list --all`. Para ver as informações de ligação para as VMs no conjunto de dimensionamento, utilize o comando `az vmss list_instance_connection_info`:

```azurecli
az vmss list_instance_connection_info --resource-group myResourceGroup --name myVmss
```
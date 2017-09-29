## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos do Azure com o comando [az group create](/cli/azure/group#create). Um grupo de recursos é um contentor lógico no qual os recursos do Azure são implementados e geridos.

```azurecli-interactive
az group create \
    --name myResourceGroup \
    --location eastus
```

## <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

Crie uma conta de armazenamento padrão para fins gerais com o comando [az storage account create](/cli/azure/storage/account#create). A conta de armazenamento padrão para fins gerais pode ser utilizada para os quatro serviços: blobs, ficheiros, tabelas e filas. 

```azurecli-interactive
az storage account create \
    --name mystorageaccount \
    --resource-group myResourceGroup \
    --location eastus \
    --sku Standard_LRS \
    --encryption blob
```

## <a name="specify-storage-account-credentials"></a>Especificar as credenciais da conta de armazenamento

O Azure CLI precisa das credenciais da conta de armazenamento para a maioria dos comandos neste tutorial. Apesar de existirem várias opções para o fazer, uma das formas mais simples de as fornecer consiste em definir as variáveis de ambiente `AZURE_STORAGE_ACCOUNT` e `AZURE_STORAGE_ACCESS_KEY`.

Em primeiro lugar, apresente as chaves da conta de armazenamento com o comando [az storage account keys list](/cli/azure/storage/account/keys#list):

```azurecli-interactive
az storage account keys list \
    --account-name mystorageaccount \
    --resource-group myResourceGroup \
    --output table
```

Em seguida, defina as variáveis de ambiente `AZURE_STORAGE_ACCOUNT` e `AZURE_STORAGE_ACCESS_KEY`. Pode fazer isto na shell de deteção com o comando `export`:

```bash
export AZURE_STORAGE_ACCOUNT="mystorageaccountname"
export AZURE_STORAGE_ACCESS_KEY="myStorageAccountKey"
```
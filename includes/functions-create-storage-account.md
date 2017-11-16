## <a name="create-an-azure-storage-account"></a>Criar uma conta de Armazenamento do Azure

As funções utiliza uma conta para fins gerais no armazenamento do Azure para manter o estado e outras informações sobre as suas funções. Criar uma conta do storage para fins gerais no grupo de recursos que criou utilizando o [criar conta de armazenamento az](/cli/azure/storage/account#create) comando.

No comando seguinte, substitua o nome de uma conta de armazenamento globalmente exclusivo onde vir o `<storage_name>` marcador de posição. Os nomes das contas do Storage devem ter entre 3 e 24 carateres de comprimento e apenas podem conter números e letras minúsculas.

```azurecli-interactive
az storage account create --name <storage_name> --location westeurope --resource-group myResourceGroup --sku Standard_LRS
```

Depois de a conta de armazenamento ter sido criada, a CLI do Azure mostra informações semelhantes às do exemplo seguinte:

```json
{
  "creationTime": "2017-04-15T17:14:39.320307+00:00",
  "id": "/subscriptions/bbbef702-e769-477b-9f16-bc4d3aa97387/resourceGroups/myresourcegroup/...",
  "kind": "Storage",
  "location": "westeurope",
  "name": "myfunctionappstorage",
  "primaryEndpoints": {
    "blob": "https://myfunctionappstorage.blob.core.windows.net/",
    "file": "https://myfunctionappstorage.file.core.windows.net/",
    "queue": "https://myfunctionappstorage.queue.core.windows.net/",
    "table": "https://myfunctionappstorage.table.core.windows.net/"
  },
     ....
    // Remaining output has been truncated for readability.
}
```
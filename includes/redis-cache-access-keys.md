Para se ligarem a uma instância da Cache de Redis do Azure, os clientes de cache precisam do nome de anfitrião, das portas e das chaves da cache. Alguns clientes podem mencionar esses itens por nomes ligeiramente diferentes. Pode obter estas informações no portal do Azure ou através das ferramentas da linha de comandos, como a CLI do Azure.

### <a name="retrieve-host-name-ports-and-access-keys-using-the-azure-portal"></a>Obtenha o nome do anfitrião, as portas e as chaves de acesso através do Portal do Azure
Para obter o nome do anfitrião, as portas e as chaves de acesso através do Portal do Azure, [navegue](../articles/redis-cache/cache-configure.md#configure-redis-cache-settings) até à cache no [portal do Azure](https://portal.azure.com) e clique em **Chaves de acesso** e **Propriedades** no **Menu de recursos**. 

![Definições da cache de Redis](media/redis-cache-access-keys/redis-cache-hostname-ports-keys.png)

### <a name="retrieve-host-name-ports-and-access-keys-using-azure-cli"></a>Obtenha o nome do anfitrião, as portas e as chaves de acesso através da CLI do Azure
Para obter o nome do anfitrião e as portas através da CLI 2.0 do Azure, pode chamar para [az redis show](https://docs.microsoft.com/cli/azure/redis#az_redis_show) e para obter as chaves pode chamar para [az redis list-keys](https://docs.microsoft.com/cli/azure/redis#az_redis_list_keys). O script seguinte chama estes dois comandos e ecoa o nome de anfitrião, as portas e as chaves para a consola.

```azurecli
#/bin/bash

# Retrieve the hostname, ports, and keys for contosoCache located in contosoGroup

# Retrieve the hostname and ports for an Azure Redis Cache instance
redis=($(az redis show --name contosoCache --resource-group contosoGroup --query [hostName,enableNonSslPort,port,sslPort] --output tsv))

# Retrieve the keys for an Azure Redis Cache instance
keys=($(az redis list-keys --name contosoCache --resource-group contosoGroup --query [primaryKey,secondaryKey] --output tsv))

# Display the retrieved hostname, keys, and ports
echo "Hostname:" ${redis[0]}
echo "Non SSL Port:" ${redis[2]}
echo "Non SSL Port Enabled:" ${redis[1]}
echo "SSL Port:" ${redis[3]}
echo "Primary Key:" ${keys[0]}
echo "Secondary Key:" ${keys[1]}
```

Para mais informações sobre este script, consulte [Get the hostname, ports, and keys for Azure Redis Cache (Obter o nome de anfitrião, as portas e as chaves para a Cache de Redis do Azure)](../articles/redis-cache/scripts/cache-keys-ports.md). Para obter mais informações sobre a CLI 2.0 do Azure, consulte [Install Azure CLI 2.0 (Instalar a CLI 2.0 do Azure)](https://docs.microsoft.com/cli/azure/install-azure-cli) e [Get started with Azure CLI 2.0 (Introdução à CLI 2.0 do Azure)](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli).

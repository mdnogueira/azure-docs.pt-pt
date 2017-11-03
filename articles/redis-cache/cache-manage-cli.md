---
title: Gerir a Cache de Redis do Azure utilizando a CLI do Azure | Microsoft Docs
description: "Saiba como instalar a CLI do Azure em qualquer plataforma, como utilizá-la para ligar à sua conta do Azure e como criar e gerir uma cache de Redis a partir da CLI do Azure."
services: redis-cache
documentationcenter: 
author: steved0x
manager: douge
editor: 
ms.assetid: 964ff245-859d-4bc1-bccf-62e4b3c1169f
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache-redis
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: sdanie
ms.openlocfilehash: ba078a870a3998568170cc197bd6698b97b7fadb
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-create-and-manage-azure-redis-cache-using-the-azure-command-line-interface-azure-cli"></a>Como criar e gerir a Cache de Redis do Azure utilizando a Interface de linha de comandos do Azure (CLI do Azure)
> [!div class="op_single_selector"]
> * [PowerShell](cache-howto-manage-redis-cache-powershell.md)
> * [CLI do Azure](cache-manage-cli.md)
>
>

A CLI do Azure é uma excelente forma de gerir a infraestrutura do Azure a partir de qualquer plataforma. Este artigo mostra como criar e gerir as instâncias de Cache de Redis do Azure utilizando a CLI do Azure.

> [!NOTE]
> Este artigo aplica-se para uma versão anterior do CLI do Azure. Para os scripts de exemplo mais recentes do Azure CLI 2.0, consulte [exemplos de cache de Redis do Azure CLI](cli-samples.md).
> 
> 

## <a name="prerequisites"></a>Pré-requisitos
Para criar e gerir instâncias de Cache de Redis do Azure utilizando a CLI do Azure, tem de concluir os passos seguintes.

* Tem de ter uma conta do Azure. Se não tiver uma, pode criar um [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) dentro de alguns momentos.
* [Instalar a CLI do Azure](../cli-install-nodejs.md).
* Ligar a instalação da CLI do Azure com uma conta pessoal do Azure, ou com um trabalho ou escola conta do Azure e iniciar sessão a partir da CLI do Azure utilizando o `azure login` comando. Para compreender as diferenças e escolher, consulte [ligar a uma subscrição do Azure a partir da linha de comandos Interface do Azure (CLI do Azure)](../xplat-cli-connect.md).
* Antes de executar qualquer um dos seguintes comandos, mude a CLI do Azure para o modo Resource Manager executando o `azure config mode arm` comando. Para obter mais informações, consulte [utilizar a CLI do Azure para gerir recursos do Azure e os grupos de recursos](../xplat-cli-azure-resource-manager.md).

## <a name="redis-cache-properties"></a>Propriedades da Cache de redis
As seguintes propriedades são utilizadas quando criar e atualizar instâncias de Cache de Redis.

| Propriedade | Comutador | Descrição |
| --- | --- | --- |
| nome |-n, – nome |Nome da Cache de Redis. |
| grupo de recursos |-g, - grupo de recursos |Nome do grupo de recursos. |
| localização |-l, – localização |Localização para criar a cache. |
| Tamanho |-z, - tamanho |Tamanho da Cache de Redis. Os valores válidos: [C0 C1, C2, C3, C4, C5, C6, P1, P2, P3, P4] |
| SKU |-x, - sku |SKU de redis. Deve ser um dos: [básico, Standard, Premium] |
| EnableNonSslPort |-i, - enable-não--porta ssl |Propriedade EnableNonSslPort da Cache de Redis. Adicionar este sinalizador, se pretender ativar a porta de SSL não para a sua cache |
| Configuração de redis |-c - redis-configuração, |Configuração de redis. Introduza uma cadeia de formatação JSON de chaves de configuração e os valores aqui. Formato: "{" ":""," ":" "}" |
| Configuração de redis |-f, - ficheiro de configuração de redis |Configuração de redis. Introduza o caminho de um ficheiro que contém chaves de configuração e os valores aqui. Formato para a entrada de ficheiro: {"": "","": ""} |
| Contagem de partições horizontais |-r, - contagem de partições horizontais |Número de partições horizontais para criar uma Cache de Cluster Premium com clustering. |
| Rede Virtual |-v, - rede virtual |Quando a alojar a cache numa VNET, especifica o ID de recurso ARM exato, da rede virtual para implementar a cache de redis no. Formato de exemplo: /subscriptions/{subid}/resourceGroups/{resourceGroupName}/Microsoft.ClassicNetwork/VirtualNetworks/vnet1 |
| tipo de chave |-t, - tipo de chave |Tipo de chave a renovação. Os valores válidos: [principal, secundária] |
| StaticIP |-p, - ip estático < ip estático > |Quando a alojar a cache numa VNET, especifica um endereço IP exclusivo na sub-rede para a cache. Se não for indicado, um valor é escolhido por si da sub-rede. |
| Subrede |t, – sub-rede<subnet> |Quando a alojar a cache numa VNET, especifica o nome da sub-rede na qual pretende implementar a cache. |
| VirtualNetwork |-v, - rede virtual <-rede virtual > |Quando a alojar a cache numa VNET, especifica o ID de recurso ARM exato, da rede virtual para implementar a cache de redis no. Formato de exemplo: /subscriptions/{subid}/resourceGroups/{resourceGroupName}/Microsoft.ClassicNetwork/VirtualNetworks/vnet1 |
| Subscrição |-s, - subscrição |O identificador de subscrição. |

## <a name="see-all-redis-cache-commands"></a>Ver todos os comandos de Cache de Redis
Para ver todos os comandos de Cache de Redis e os respetivos parâmetros, utilize o `azure rediscache -h` comando.

    C:\>azure rediscache -h
    help:    Commands to manage your Azure Redis Cache(s)
    help:
    help:    Create a Redis Cache
    help:      rediscache create [--name <name> --resource-group <resource-group> --location <location> [options]]
    help:
    help:    Delete an existing Redis Cache
    help:      rediscache delete [--name <name> --resource-group <resource-group> ]
    help:
    help:    List all Redis Caches within your Subscription or Resource Group
    help:      rediscache list [options]
    help:
    help:    Show properties of an existing Redis Cache
    help:      rediscache show [--name <name> --resource-group <resource-group>]
    help:
    help:    Change settings of an existing Redis Cache
    help:      rediscache set [--name <name> --resource-group <resource-group> --redis-configuration <redis-configuration>/--redis-configuration-file <redisConfigurationFile>]
    help:
    help:    Renew the authentication key for an existing Redis Cache
    help:      rediscache renew-key [--name <name> --resource-group <resource-group> ]
    help:
    help:    Lists Primary and Secondary key of an existing Redis Cache
    help:      rediscache list-keys [--name <name> --resource-group <resource-group>]
    help:
    help:    Options:
    help:      -h, --help  output usage information
    help:
    help:    Current Mode: arm (Azure Resource Management)

## <a name="create-a-redis-cache"></a>Criar uma Cache de Redis
Para criar uma Cache de Redis, utilize o seguinte comando:

    azure rediscache create [--name <name> --resource-group <resource-group> --location <location> [options]]

Para obter mais informações sobre este comando, execute o `azure rediscache create -h` comando.

    C:\>azure rediscache create -h
    help:    Create a Redis Cache
    help:
    help:    Usage: rediscache create [--name <name> --resource-group <resource-group> --location <location> [options]]
    help:
    help:    Options:
    help:      -h, --help                                               output usage information
    help:      -v, --verbose                                            use verbose output
    help:      -vv                                                      more verbose with debug output
    help:      --json                                                   use json output
    help:      -n, --name <name>                                        Name of the Redis Cache.
    help:      -g, --resource-group <resource-group>                    Name of the Resource Group
    help:      -l, --location <location>                                Location to create cache.
    help:      -z, --size <size>                                        Size of the Redis Cache. Valid values: [C0, C1, C2, C3, C4, C5, C6, P1, P2, P3, P4]
    help:      -x, --sku <sku>                                          Redis SKU. Should be one of : [Basic, Standard, Premium]
    help:      -e, --enable-non-ssl-port                                EnableNonSslPort property of the Redis Cache. Add this flag if you want to enable the Non SSL Port for your cache
    help:      -c, --redis-configuration <redis-configuration>          Redis Configuration. Enter a JSON formatted string of configuration keys and values here. Format:"{"<key1>":"<value1>","<key2>":"<value2>"}"
    help:      -f, --redis-configuration-file <redisConfigurationFile>  Redis Configuration. Enter the path of a file containing configuration keys and values here. Format for the file entry: {"<key1>":"<value1>","<key2>":"<value2>"}
    help:      -r, --shard-count <shard-count>                          Number of Shards to create on a Premium Cluster Cache
    help:      -v, --virtual-network <virtual-network>                  The exact ARM resource ID of the virtual network to deploy the redis cache in. Example format: /subscriptions/{subid}/resourceGroups/{resourceGroupName}/Microsoft.ClassicNetwork/VirtualNetworks/vnet1
    help:      -t, --subnet <subnet>                                    Required when deploying a redis cache inside an existing Azure Virtual Network
    help:      -p, --static-ip <static-ip>                              Required when deploying a redis cache inside an existing Azure Virtual Network
    help:      -s, --subscription <id>                                  the subscription identifier
    help:
    help:    Current Mode: arm (Azure Resource Management)

## <a name="delete-an-existing-redis-cache"></a>Eliminar uma Cache de Redis existente
Para eliminar uma Cache de Redis, utilize o seguinte comando:

    azure rediscache delete [--name <name> --resource-group <resource-group> ]

Para obter mais informações sobre este comando, execute o `azure rediscache delete -h` comando.

    C:\>azure rediscache delete -h
    help:    Delete an existing Redis Cache
    help:
    help:    Usage: rediscache delete [--name <name> --resource-group <resource-group> ]
    help:
    help:    Options:
    help:      -h, --help                             output usage information
    help:      -v, --verbose                          use verbose output
    help:      -vv                                    more verbose with debug output
    help:      --json                                 use json output
    help:      -n, --name <name>                      Name of the Redis Cache.
    help:      -g, --resource-group <resource-group>  Name of the Resource Group under which the cache exists
    help:      -s, --subscription <subscription>      the subscription identifier
    help:
    help:    Current Mode: arm (Azure Resource Management)

## <a name="list-all-redis-caches-within-your-subscription-or-resource-group"></a>Lista todas as Caches de Redis dentro da sua subscrição ou grupo de recursos
Para listar todas as Caches de Redis dentro da sua subscrição ou grupo de recursos, utilize o seguinte comando:

    azure rediscache list [options]

Para obter mais informações sobre este comando, execute o `azure rediscache list -h` comando.

    C:\>azure rediscache list -h
    help:    List all Redis Caches within your Subscription or Resource Group
    help:
    help:    Usage: rediscache list [options]
    help:
    help:    Options:
    help:      -h, --help                             output usage information
    help:      -v, --verbose                          use verbose output
    help:      -vv                                    more verbose with debug output
    help:      --json                                 use json output
    help:      -g, --resource-group <resource-group>  Name of the Resource Group
    help:      -s, --subscription <subscription>      the subscription identifier
    help:
    help:    Current Mode: arm (Azure Resource Management)

## <a name="show-properties-of-an-existing-redis-cache"></a>Mostrar propriedades de uma Cache de Redis existente
Para mostrar as propriedades de uma Cache de Redis existente, utilize o seguinte comando:

    azure rediscache show [--name <name> --resource-group <resource-group>]

Para obter mais informações sobre este comando, execute o `azure rediscache show -h` comando.

    C:\>azure rediscache show -h
    help:    Show properties of an existing Redis Cache
    help:
    help:    Usage: rediscache show [--name <name> --resource-group <resource-group>]
    help:
    help:    Options:
    help:      -h, --help                             output usage information
    help:      -v, --verbose                          use verbose output
    help:      -vv                                    more verbose with debug output
    help:      --json                                 use json output
    help:      -n, --name <name>                      Name of the Redis Cache.
    help:      -g, --resource-group <resource-group>  Name of the Resource Group
    help:      -s, --subscription <subscription>      the subscription identifier
    help:
    help:    Current Mode: arm (Azure Resource Management)

<a name="scale"></a>

## <a name="change-settings-of-an-existing-redis-cache"></a>Alterar as definições de uma Cache de Redis existente
Para alterar as definições de uma Cache de Redis existente, utilize o seguinte comando:

    azure rediscache set [--name <name> --resource-group <resource-group> --redis-configuration <redis-configuration>/--redis-configuration-file <redisConfigurationFile>]

Para obter mais informações sobre este comando, execute o `azure rediscache set -h` comando.

    C:\>azure rediscache set -h
    help:    Change settings of an existing Redis Cache
    help:
    help:    Usage: rediscache set [--name <name> --resource-group <resource-group> --redis-configuration <redis-configuration>/--redis-configuration-file <redisConfigurationFile>]
    help:
    help:    Options:
    help:      -h, --help                                               output usage information
    help:      -v, --verbose                                            use verbose output
    help:      -vv                                                      more verbose with debug output
    help:      --json                                                   use json output
    help:      -n, --name <name>                                        Name of the Redis Cache.
    help:      -g, --resource-group <resource-group>                    Name of the Resource Group
    help:      -c, --redis-configuration <redis-configuration>          Redis Configuration. Enter a JSON formatted string of configuration keys and values here.
    help:      -f, --redis-configuration-file <redisConfigurationFile>  Redis Configuration. Enter the path of a file containing configuration keys and values here.
    help:      -s, --subscription <subscription>                        the subscription identifier
    help:
    help:    Current Mode: arm (Azure Resource Management)

## <a name="renew-the-authentication-key-for-an-existing-redis-cache"></a>Renovar a chave de autenticação para uma Cache de Redis existente
Para renovar a chave de autenticação para uma Cache de Redis existente, utilize o seguinte comando:

    azure rediscache renew-key [--name <name> --resource-group <resource-group> --key-type <key-type>]

Especifique `Primary` ou `Secondary` para `key-type`.

Para obter mais informações sobre este comando, execute o `azure rediscache renew-key -h` comando.

    C:\>azure rediscache renew-key -h
    help:    Renew the authentication key for an existing Redis Cache
    help:
    help:    Usage: rediscache renew-key [--name <name> --resource-group <resource-group> ]
    help:
    help:    Options:
    help:      -h, --help                             output usage information
    help:      -v, --verbose                          use verbose output
    help:      -vv                                    more verbose with debug output
    help:      --json                                 use json output
    help:      -n, --name <name>                      Name of the Redis Cache.
    help:      -g, --resource-group <resource-group>  Name of the Resource Group under which cache exists
    help:      -t, --key-type <key-type>              type of key to renew. Valid values are: 'Primary', 'Secondary'.
    help:      -s, --subscription <subscription>      the subscription identifier
    help:
    help:    Current Mode: arm (Azure Resource Management)

## <a name="list-primary-and-secondary-keys-of-an-existing-redis-cache"></a>Chaves de lista primário e secundário de uma Cache de Redis existente
A lista principais e secundários as chaves de uma Cache de Redis existente, utilize o seguinte comando:

    azure rediscache list-keys [--name <name> --resource-group <resource-group>]

Para obter mais informações sobre este comando, execute o `azure rediscache list-keys -h` comando.

    C:\>azure rediscache list-keys -h
    help:    Lists Primary and Secondary key of an existing Redis Cache
    help:
    help:    Usage: rediscache list-keys [--name <name> --resource-group <resource-group>]
    help:
    help:    Options:
    help:      -h, --help                             output usage information
    help:      -v, --verbose                          use verbose output
    help:      -vv                                    more verbose with debug output
    help:      --json                                 use json output
    help:      -n, --name <name>                      Name of the Redis Cache.
    help:      -g, --resource-group <resource-group>  Name of the Resource Group under which Cache exists
    help:      -s, --subscription <subscription>      the subscription identifier
    help:
    help:    Current Mode: arm (Azure Resource Management)

---
title: "Introdução ao DNS do Azure com a CLI 1.0 do Azure | Microsoft Docs"
description: "Saiba como criar uma zona DNS e o registar no DNS do Azure. Este é um guia passo a passo para criar e gerir a sua primeira zona DNS e registar com a CLI 1.0 do Azure."
services: dns
documentationcenter: na
author: jtuliani
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: fb0aa0a6-d096-4d6a-b2f6-eda1c64f6182
ms.service: dns
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/10/2017
ms.author: jonatul
ms.openlocfilehash: f7943b71bbd16c36df09436973d92539eb62b210
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-azure-dns-using-azure-cli-10"></a>Introdução ao DNS do Azure com a CLI 1.0 do Azure

> [!div class="op_single_selector"]
> * [Portal do Azure](dns-getstarted-portal.md)
> * [PowerShell](dns-getstarted-powershell.md)
> * [CLI do Azure 1.0](dns-getstarted-cli-nodejs.md)
> * [CLI 2.0 do Azure](dns-getstarted-cli.md)

Este artigo explica-lhe os passos para criar a primeira zona DNS e registar com a CLI 1.0 do Azure de várias plataformas, que está disponível para Windows, Mac e Linux. Também pode executar estes passos com o portal do Azure ou o Azure PowerShell.

Uma zona DNS é utilizada para alojar os registos DNS para um determinado domínio. Para começar a alojar o seu domínio no DNS do Azure, tem de criar uma zona DNS para esse nome de domínio. Cada registo DNS para o seu domínio é então criado no interior desta zona DNS. Por fim, para publicar a zona DNS na Internet, tem de configurar os servidores de nomes do domínio. Cada um destes passos está descrito abaixo.

Estas instruções partem do princípio de que já instalou e iniciou sessão no CLI 1.0 do Azure. Para obter ajuda, consulte [How to manage DNS zones using Azure CLI 1.0 (Como gerir zonas DNS com a CLI 1.0 do Azure)](dns-operations-dnszones-cli-nodejs.md).

## <a name="create-the-resource-group"></a>Criar o grupo de recursos

Antes de criar a zona DNS, um grupo de recursos é criado para conter a zona DNS. O seguinte mostra o comando.

```azurecli
azure group create --name MyResourceGroup --location "West US"
```

## <a name="create-a-dns-zone"></a>Criar uma zona DNS

Uma zona DNS é criada ao utilizar o comando `azure network dns zone create`. Para ver a ajuda deste comando, escreva `azure network dns zone create -h`.

O exemplo seguinte cria uma zona DNS denominada *contoso.com* no grupo de recursos denominado *MyResourceGroup*. Utilize o exemplo para criar uma zona DNS, substituindo os valores pelos seus.

```azurecli
azure network dns zone create MyResourceGroup contoso.com
```


## <a name="create-a-dns-record"></a>Criar um registo DNS

Para criar um registo DNS, utilize o comando `azure network dns record-set add-record`. Para obter ajuda, consulte `azure network dns record-set add-record -h`.

O exemplo seguinte cria um registo com o nome relativo "www" na zona DNS "contoso.com", no grupo de recursos "MyResourceGroup". O nome totalmente qualificado do conjunto de registos é “www.contoso.com”. O tipo de registo é "A", com o endereço IP "1.2.3.4" e é utilizado um TTL predefinido de 3600 segundos (1 hora).

```azurecli
azure network dns record-set add-record MyResourceGroup contoso.com www A -a 1.2.3.4
```

Para outros tipos de registos, para conjuntos de registos com mais de um registo, para os valores de TTL alternativos e para modificar registos existentes, consulte [Manage DNS records and record sets using the Azure CLI 1.0 (Gerir registos DNS e conjuntos de registos com o CLI 1.0 do Azure)](dns-operations-recordsets-cli-nodejs.md).


## <a name="view-records"></a>Ver registos

Para listar os registos DNS na sua zona, utilize:

```azurecli
azure network dns record-set list MyResourceGroup contoso.com
```


## <a name="update-name-servers"></a>Atualizar servidores de nomes

Depois de ter a certeza que a sua zona DNS e os registos foram configurados corretamente, tem de configurar o seu nome de domínio para utilizar os servidores de nomes do DNS do Azure. Isto permite que outros utilizadores na Internet encontrem os seus registos DNS.

Os servidores de nomes para a sua zona são indicados pelo comando `azure network dns zone show`:

```azurecli
azure network dns zone show MyResourceGroup contoso.com

info:    Executing command network dns zone show
+ Looking up the dns zone "contoso.com"
data:    Id                              : /subscriptions/a385a691-bd93-41b0-8084-8213ebc5bff7/resourceGroups/myresourcegroup/providers/Microsoft.Network/dnszones/contoso.com
data:    Name                            : contoso.com
data:    Type                            : Microsoft.Network/dnszones
data:    Location                        : global
data:    Number of record sets           : 3
data:    Max number of record sets       : 5000
data:    Name servers:
data:        ns1-01.azure-dns.com.
data:        ns2-01.azure-dns.net.
data:        ns3-01.azure-dns.org.
data:        ns4-01.azure-dns.info.
data:    Tags                            :
info:    network dns zone show command OK
```

Estes servidores de nomes devem ser configurados com a entidade de registo de nomes de domínio (onde comprou o nome de domínio). A entidade de registo irá oferecer a opção para configurar os servidores de nomes do domínio. Para obter mais informações, consulte [Delegate your domain to Azure DNS (Delegar o seu domínio ao DNS do Azure)](dns-domain-delegation.md).

## <a name="delete-all-resources"></a>Eliminar todos os recursos
 
Para eliminar todos os recursos criados neste artigo, realize o seguinte passo:

```azurecli
azure group delete --name MyResourceGroup
```

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre DNS do Azure, consulte [Azure DNS overview (Descrição geral do DNS do Azure)](dns-overview.md).

Para saber mais sobre a gestão de zonas DNS no DNS do Azure, consulte [Manage DNS zones in Azure DNS using Azure CLI 1.0 (Gerir zonas DNS no DNS do Azure com a CLI 1.0 do Azure)](dns-operations-dnszones-cli-nodejs.md).

Para saber mais sobre a gestão de registos DNS no DNS do Azure, consulte [Manage DNS records and record sets in Azure DNS using Azure CLI 1.0 (Gerir registos DNS e conjuntos de registos no DNS do Azure com a CLI 1.0 do Azure)](dns-operations-recordsets-cli-nodejs.md).


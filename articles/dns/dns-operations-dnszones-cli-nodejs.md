---
title: Gerir zonas DNS no DNS do Azure - CLI do Azure 1.0 | Microsoft Docs
description: Pode gerir zonas DNS, utilizando a CLI do Azure 1.0. Este artigo mostra como atualizar, eliminar e criar zonas DNS no DNS do Azure.
services: dns
documentationcenter: na
author: georgewallace
manager: timlt
ms.assetid: 8ab63bc4-5135-4ed8-8c0b-5f0712b9afed
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/21/2016
ms.author: gwallace
ms.openlocfilehash: 588c87749f049eff5b9e0729f6769c8367ba41e4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-manage-dns-zones-in-azure-dns-using-the-azure-cli-10"></a>Como gerir zonas DNS no DNS do Azure utilizando a CLI do Azure 1.0

> [!div class="op_single_selector"]
> * [Portal](dns-operations-dnszones-portal.md)
> * [PowerShell](dns-operations-dnszones.md)
> * [CLI do Azure 1.0](dns-operations-dnszones-cli-nodejs.md)
> * [CLI 2.0 do Azure](dns-operations-dnszones-cli.md)

Este guia mostra como gerir as zonas DNS, utilizando a plataforma do Azure CLI 1.0, que está disponível para o Windows, Mac e Linux. Também pode gerir as zonas DNS utilizando [Azure PowerShell](dns-operations-dnszones.md) ou o portal do Azure.

## <a name="cli-versions-to-complete-the-task"></a>Versões CLI para concluir a tarefa

Pode concluir a tarefa utilizando uma das seguintes versões CLI:

* [CLI 1.0 do Azure](dns-operations-dnszones-cli-nodejs.md) - CLI para os modelos de implementação de gestão clássica e de recursos.
* [CLI 2.0 do Azure](dns-operations-dnszones-cli.md) - CLI de próxima geração para o modelo de implementação de gestão de recursos.

## <a name="introduction"></a>Introdução

[!INCLUDE [dns-create-zone-about](../../includes/dns-create-zone-about-include.md)]

[!INCLUDE [dns-cli-setup](../../includes/dns-cli-setup-include.md)]

## <a name="getting-help"></a>Obter ajuda

Todos os comandos de CLI 1.0 referentes ao DNS do Azure começar a utilizar `azure network dns`. Ajuda está disponível para cada comando utilizando a `--help` opção (formato curto `-h`).  Por exemplo:

```azurecli
azure network dns -h
azure network dns zone -h
azure network dns zone create -h
```

## <a name="create-a-dns-zone"></a>Criar uma zona DNS

Uma zona DNS é criada ao utilizar o comando `azure network dns zone create`. Para obter ajuda, consulte `azure network dns zone create -h`.

O exemplo seguinte cria uma zona DNS denominada *contoso.com* no grupo de recursos chamado *MyResourceGroup*:

```azurecli
azure network dns zone create MyResourceGroup contoso.com
```

### <a name="to-create-a-dns-zone-with-tags"></a>Para criar uma zona DNS com etiquetas

O exemplo seguinte mostra como criar uma zona DNS com dois [etiquetas do Azure Resource Manager](dns-zones-records.md#tags), *projeto = demonstração* e *env = test*, utilizando o `--tags` (parâmetro formato curto `-t`):

```azurecli
azure network dns zone create MyResourceGroup contoso.com -t "project=demo";"env=test"
```

## <a name="get-a-dns-zone"></a>Obter uma zona DNS

Para obter uma zona DNS, utilize `azure network dns zone show`. Para obter ajuda, consulte `azure network dns zone show -h`.

O exemplo seguinte devolve a zona DNS *contoso.com* e os respetivos dados associados do grupo de recursos *MyResourceGroup*. 

```azurecli
azure network dns zone show MyResourceGroup contoso.com
```

O exemplo seguinte é a resposta.

```
info:    Executing command network dns zone show
+ Looking up the dns zone "contoso.com"
data:    Id                              : /subscriptions/.../contoso.com
data:    Name                            : contoso.com
data:    Type                            : Microsoft.Network/dnszones
data:    Location                        : global
data:    Number of record sets           : 2
data:    Max number of record sets       : 5000
data:    Name servers:
data:        ns1-01.azure-dns.com.
data:        ns2-01.azure-dns.net.
data:        ns3-01.azure-dns.org.
data:        ns4-01.azure-dns.info.
data:    Tags                            : project=demo;env=test
info:    network dns zone show command OK
```

Tenha em atenção que os registos DNS não são devolvidos pelo `azure network dns zone show`. Para listar os registos DNS, utilize `azure network dns record-set list`.


## <a name="list-dns-zones"></a>Zonas DNS de lista

Para enumerar zonas DNS, utilize `azure network dns zone list`. Para obter ajuda, consulte `azure network dns zone list -h`.

Especificar o grupo de recursos lista apenas dessas zonas no grupo de recursos:

```azurecli
azure network dns zone list MyResourceGroup
```

O grupo de recursos a omissão apresenta uma lista de todas as zonas na subscrição:

```azurecli
azure network dns zone list 
```

## <a name="update-a-dns-zone"></a>Atualize uma zona DNS

É possível efetuar alterações a um recurso de zona DNS utilizando `azure network dns zone set`. Para obter ajuda, consulte `azure network dns zone set -h`.

Este comando não atualizar qualquer um dos conjuntos de registos de DNS na zona (consulte [como registos DNS gerir](dns-operations-recordsets-cli-nodejs.md)). Só é utilizado para atualizar propriedades do recurso de zona em si. Estas propriedades são actualmente limitadas à [do Azure Resource Manager 'etiquetas'](dns-zones-records.md#tags) para o recurso de zona.

O exemplo seguinte mostra como atualizar as etiquetas numa zona DNS. As etiquetas existentes são substituídas pelo valor especificado.

```azurecli
azure network dns zone set MyResourceGroup contoso.com -t "team=support"
```

## <a name="delete-a-dns-zone"></a>Eliminar uma zona DNS

Zonas DNS podem ser eliminadas utilizando `azure network dns zone delete`. Para obter ajuda, consulte `azure network dns zone delete -h`.

> [!NOTE]
> Também eliminar uma zona DNS elimina todos os registos DNS na zona. Esta operação não pode ser anulada. Se a zona DNS se encontra em utilização, serviços, utilizando a zona irão falhar quando a zona é eliminada.
>
>Para proteger contra eliminação acidental de zona, consulte [como pretende proteger os registos e zonas DNS](dns-protect-zones-recordsets.md).

Este comando solicita a confirmação. O opcional `--quiet` mudar (formato curto `-q`) suprime esta linha de comandos.

O exemplo seguinte mostra como eliminar a zona *contoso.com* do grupo de recursos *MyResourceGroup*.

```azurecli
azure network dns zone delete MyResourceGroup contoso.com
```

## <a name="next-steps"></a>Passos seguintes

Saiba como [gerir conjuntos de registos e registos](dns-getstarted-create-recordset-cli-nodejs.md) na sua zona DNS.

Saiba como [delegar o domínio ao DNS do Azure](dns-domain-delegation.md).


---
title: Criar um conjunto de registos e registos para uma Zona DNS ao utilizar a CLI| Microsoft Docs
description: "Como criar registos de anfitrião para o DNS do Azure. Configurar conjuntos de registo e registos ao utilizar a CLI"
services: dns
documentationcenter: na
author: georgewallace
manager: timlt
ms.assetid: 02b897d3-e83b-4257-b96d-5c29aa59e843
ms.service: dns
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/16/2016
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 02d720a04fdc0fa302c2cb29b0af35ee92c14b3b
ms.openlocfilehash: f15333cde5304579ff0d0ba9571a870ddde19163

---

# <a name="create-dns-record-sets-and-records-by-using-cli"></a>Criar conjuntos de registos e registos de DNS ao utilizar a CLI

> [!div class="op_single_selector"]
> * [Portal do Azure](dns-getstarted-create-recordset-portal.md)
> * [PowerShell](dns-getstarted-create-recordset.md)
> * [CLI do Azure](dns-getstarted-create-recordset-cli.md)

Este artigo serve de orientação durante o processo de criação de registos e conjuntos de registos ao utilizar a CLI. Depois de criar a zona DNS, terá de adicionar os registos DNS para o seu domínio. Para tal, tem primeiro de compreender os registos e os conjuntos de registos DNS.

[!INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)]

## <a name="create-a-record-set-and-record"></a>Criar um conjunto de registos e registos

Nesta secção, vamos mostrar-lhe como se cria um conjunto de registos e registos. Neste exemplo, irá criar um conjunto de registos que tem o nome relativo “www” na zona DNS “contoso.com”. O nome totalmente qualificado dos registos é “www.contoso.com”. O tipo de registo é “A” e o limite de tempo TTL é de 60 segundos. Depois de concluir este passo, terá criado um conjunto de registos vazio.

Para criar um conjunto de registos no vértice da zona (neste caso, “contoso.com”), utilize o nome do registo "@",, incluindo as aspas. Esta é uma convenção DNS comum.

### <a name="1-create-a-record-set"></a>1. Criar um conjunto de registos

Para criar um conjunto de registos, utilize `azure network dns record-set create`. Especifique o grupo de recursos, o nome da zona, o nome relativo do conjunto de registos, o tipo de registo e o valor de TTL. Se o parâmetro `--ttl` não estiver definido, o valor é predefinido para quatro (em segundos). Depois de concluir este passo, terá um conjunto de registos vazio “www”.

*Utilização: network dns record-set create \<resource-group\> \<dns-zone-name\> \<name\> \<type\> \<ttl\>*

```azurecli
azure network dns record-set create myresourcegroup  contoso.com  www A  60
```

### <a name="2-add-records"></a>2. Adicionar registos

Para utilizar o conjunto de registos “www” recém-criado, tem de adicionar registos ao mesmo. Adicione registos a conjuntos de registos ao utilizar `azure network dns record-set add-record`.

Os parâmetros para adicionar registos a um conjunto de registos variam consoante o tipo do conjunto de registos. Por exemplo, quando utilizar um conjunto de registos do tipo “A”, só poderá especificar registos com o parâmetro `-a <IPv4 address>`.

Pode adicionar registos IPv4 *A* ao conjunto de registos “www” ao utilizar o seguinte comando:

*Utilização: network dns record-set add-record \<resource-group\> \<dns-zone-name\> \<record-set-name\> \<type\>*

```azurecli
azure network dns record-set add-record myresourcegroup contoso.com  www A  -a 134.170.185.46
```

## <a name="additional-record-type-examples"></a>Exemplos de tipo de registo adicionais

Os exemplos seguintes mostram como criar um conjunto de registos de cada tipo de registo. Cada conjunto de registos contém um único registo.

[!INCLUDE [dns-add-record-cli-include](../../includes/dns-add-record-cli-include.md)]

## <a name="next-steps"></a>Passos seguintes

Para gerir o seu conjunto de registos e os registos, consulte [Gerir registos e conjuntos de registos DNS ao utilizar a CLI](dns-operations-recordsets-portal.md).

Para mais informações sobre o DNS do Azure, consulte a [Descrição geral do DNS do Azure](dns-overview.md).




<!--HONumber=Nov16_HO3-->



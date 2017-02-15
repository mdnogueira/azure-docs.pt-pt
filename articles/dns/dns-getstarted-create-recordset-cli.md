---
title: Criar registos DNS com a CLI do Azure| Microsoft Docs
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
ms.date: 12/21/2016
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 18a21cdc0f9641356dfaf6f6d93edfcac11af210
ms.openlocfilehash: 790af1544ed86155f5f864f3914b5fd1c4f42f4b

---

# <a name="create-dns-records-using-the-azure-cli"></a>Criar registos DNS com a CLI do Azure

> [!div class="op_single_selector"]
> * [Portal do Azure](dns-getstarted-create-recordset-portal.md)
> * [PowerShell](dns-getstarted-create-recordset.md)
> * [CLI do Azure](dns-getstarted-create-recordset-cli.md)

Este artigo serve de orientação durante o processo de criação de registos e conjuntos de registos ao utilizar a CLI do Azure.

## <a name="introduction"></a>Introdução

Antes de criar registos DNS no DNS do Azure, terá primeiro de compreender como o DNS do Azure organiza os registos DNS em conjuntos de registos DNS.

[!INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)]

Para obter mais informações sobre os registos DNS no DNS do Azure, veja [Zonas e registos DNS](dns-zones-records.md).

## <a name="create-a-record-set-and-record"></a>Criar um conjunto de registos e registos

Esta secção descreve como criar registos DNS no DNS do Azure. Os exemplos partem do princípio que já [instalou a CLI do Azure, iniciou sessão e criou uma zona DNS](dns-getstarted-create-dnszone-cli.md).

Todos os exemplos nesta página utilizam o tipo de registo de DNS "A". Para obter outros tipos de registo e mais detalhes sobre como gerir os registos DNS e conjuntos de registos, consulte [Manage DNS records and record sets by using the Azure CLI (Gerir registos DNS e conjuntos de registos com a CLI do Azure)](dns-operations-recordsets-cli.md).

## <a name="create-a-dns-record"></a>Criar um registo DNS

Para criar um registo DNS, utilize o comando `azure network dns record-set add-record`. Para obter ajuda, consulte `azure network dns record-set add-record -h`.

Ao criar um registo, tem de especificar o nome do grupo de recursos, o nome da zona, o nome do conjunto de recursos, o tipo de registo e os detalhes do registo que está a ser criado.

Se o conjunto de registos ainda não existir, este comando cria-o por si. Se o conjunto de registos já existir, este comando adiciona o registo que especificar ao conjunto de registos existente. 

Se for criado um novo conjunto de registos, é utilizado um TTL (time-to-live) predefinido de 3600. Para obter instruções sobre como utilizar TTLs diferentes, veja [Gerir registos DNS no DNS do Azure com a CLI do Azure](dns-operations-recordsets-cli.md).

O exemplo seguinte cria um registo A denominado *www* na zona *contoso.com* do grupo de recursos *MyResourceGroup*. O endereço IP do registo A é *1.2.3.4*.

```azurecli
azure network dns record-set add-record MyResourceGroup contoso.com www A -a 1.2.3.4
```

Para criar um conjunto de registos no vértice da zona (neste caso, “contoso.com”), utilize o nome do registo "@",, incluindo as aspas:

```azurecli
azure network dns record-set add-record MyResourceGroup contoso.com "@" A -a 1.2.3.4
```

Os parâmetros utilizados para especificar os dados de registo variam consoante o tipo de registo. Por exemplo, para um registo do tipo "A", especifica o endereço IPv4 com o parâmetro `-a <IPv4 address>`. Consulte `azure network dns record-set add-record -h` para listar os parâmetros para outros tipos de registos. Para obter exemplos para cada tipo de registo, veja [Gerir registos e conjuntos de registos DNS com a CLI do Azure](dns-operations-recordsets-cli.md).


## <a name="verify-name-resolution"></a>Verificar resolução de nomes

Pode testar se os seus registos DNS estão presentes nos nomes de servidores de DNS do Azure ao utilizar as ferramentas DNS como nslookup, dig ou o [cmdlet do PowerShell Resolve-DnsName](https://technet.microsoft.com/library/jj590781.aspx).

Se ainda não tiver delegado o seu domínio para utilizar a nova zona no DNS do Azure, tem de [direcionar a consulta DNS diretamente para um dos servidores de nome da sua zona](dns-getstarted-create-dnszone.md#test-name-servers). Certifique-se de que substitui os valores corretos para a zona dos registos para o comando abaixo.

```
nslookup
> set type=A
> server ns1-01.azure-dns.com
> www.contoso.com

Server:  ns1-01.azure-dns.com
Address:  40.90.4.1

Name:    www.contoso.com
Address:  1.2.3.4
```

## <a name="next-steps"></a>Passos seguintes

Saiba como [delegar o seu nome de domínio aos servidores de nomes DNS do Azure](dns-domain-delegation.md)

Saiba como [gerir zonas DNS com a CLI do Azure](dns-operations-dnszones-cli.md).

Saiba como [gerir registos DNS e conjuntos de registos com a CLI do Azure](dns-operations-recordsets-cli.md).




<!--HONumber=Dec16_HO3-->



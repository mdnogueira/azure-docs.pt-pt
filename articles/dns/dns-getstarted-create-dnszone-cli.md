---
title: Criar uma zona DNS ao utilizar a CLI 2.0 do Azure| Microsoft Docs
description: "Saiba como criar zonas DNS no DNS do Azure. Este é um guia passo a passo para criar e gerir a sua primeira zona DNS com a CLI 2.0 do Azure."
services: dns
documentationcenter: na
author: georgewallace
manager: timlt
ms.assetid: 1514426a-133c-491a-aa27-ee0962cea9dc
ms.service: dns
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/27/2017
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 1481fcb070f383d158c5a6ae32504e498de4a66b
ms.openlocfilehash: 00a4dfbe070129ce6dc84f006d793007d55dba3f
ms.lasthandoff: 02/28/2017

---

# <a name="create-an-azure-dns-zone-using-azure-cli-20"></a>Criar uma zona DNS do Azure ao utilizar a CLI 2.0 do Azure

> [!div class="op_single_selector"]
> * [Portal do Azure](dns-getstarted-create-dnszone-portal.md)
> * [PowerShell](dns-getstarted-create-dnszone.md)
> * [CLI do Azure 1.0](dns-getstarted-create-dnszone-cli-nodejs.md)
> * [CLI 2.0 do Azure](dns-getstarted-create-dnszone-cli.md)

Este artigo explica-lhe os passos para criar uma zona DNS com a CLI do Azure de várias plataformas, que está disponível para Windows, Mac e Linux. Também pode criar uma zona DNS com o [Azure PowerShell](dns-getstarted-create-dnszone.md) ou o [portal do Azure](dns-getstarted-create-dnszone-portal.md).

## <a name="cli-versions-to-complete-the-task"></a>Versões CLI para concluir a tarefa

Pode concluir a tarefa utilizando uma das seguintes versões CLI:

* [CLI 1.0 do Azure](dns-getstarted-create-dnszone-cli-nodejs.md) - CLI para os modelos de implementação de gestão clássica e de recursos.
* [CLI 2.0 do Azure](dns-getstarted-create-dnszone-cli.md) - CLI de próxima geração para o modelo de implementação de gestão de recursos.

## <a name="introduction"></a>Introdução

[!INCLUDE [dns-create-zone-about](../../includes/dns-create-zone-about-include.md)]

## <a name="set-up-azure-cli-20-for-azure-dns"></a>Configurar a CLI 2.0 do Azure para o Azure DNS

### <a name="before-you-begin"></a>Antes de começar

Antes de iniciar a configuração, verifique se tem os seguintes itens.

* Uma subscrição do Azure. Se ainda não tiver uma subscrição do Azure, pode ativar os [Benefícios de subscritor do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou inscrever-se numa [conta gratuita](https://azure.microsoft.com/pricing/free-trial/).

* Instalar a versão mais recente da CLI do Azure, disponível para Windows, Linux ou Mac. Existem mais informações disponíveis em [Instalar a CLI 2.0 do Azure](https://docs.microsoft.com/en-us/cli/azure/install-az-cli2).

### <a name="sign-in-to-your-azure-account"></a>Inicie sessão na sua conta do Azure

Abra uma janela de consola e autentique com as suas credenciais. Para obter mais informações, veja Iniciar sessão no Azure a partir da CLI do Azure

```azurecli
az login
```

### <a name="select-the-subscription"></a>Selecionar a subscrição

Verifique as subscrições da conta.

```azurecli
az account list
```

### <a name="choose-which-of-your-azure-subscriptions-to-use"></a>Escolha qual das suas subscrições do Azure utilizar.

```azurecli
az account set --subscription "subscription name"
```

### <a name="create-a-resource-group"></a>Criar um grupo de recursos

O Azure Resource Manager requer que todos os grupos de recursos especifiquem uma localização. Isto é utilizado como a localização predefinida para recursos nesse grupo de recursos. No entanto, uma vez que todos os recursos DNS são globais, não regionais, a opção de localização do grupo de recursos não tem impacto no DNS do Azure.

Pode ignorar este passo se estiver a utilizar um grupo de recursos existente.

```azurecli
az group create --name myresourcegroup --location "West US"
```

## <a name="create-a-dns-zone"></a>Criar uma zona DNS

Uma zona DNS é criada ao utilizar o comando `az network dns zone create`. Para ver a ajuda deste comando, escreva `az network dns zone create --help`.

O exemplo seguinte cria uma zona DNS denominada *contoso.com* no grupo de recursos denominado *MyResourceGroup*. Utilize o exemplo para criar uma zona DNS, substituindo os valores pelos seus.

```azurecli
az network dns zone create --resource-group myresourcegroup --name contoso.com
```

## <a name="verify-your-dns-zone"></a>Verificar a zona DNS

### <a name="view-records"></a>Ver registos

Criar uma zona DNS também cria os seguintes registos DNS:

* O registo *Início da autoridade* (SOA). Este registo está presente na raiz de cada zona DNS.
* Os registos do servidor de nomes (NS) autoritativo. Estes registos mostram que servidores de nome estão a alojar a zona. O DNS do Azure utiliza um conjunto de servidores de nome e, por isso, diferentes servidores de nomes podem ser atribuídos a diferentes zonas no DNS do Azure. Para obter mais informações, veja [Delegar um domínio ao DNS do Azure](dns-domain-delegation.md).

Para ver estes registos, utilize `az network dns record-set list`:

```azurecli
az network dns record-set list --resource-group MyResourceGroup --zone-name contoso.com
```

A resposta para `az network dns record-set list` é apresentado abaixo:

```json
[
  {
    "etag": "510f4711-8b7f-414e-b8d5-c0383ea3d62e",
    "id": "/subscriptions/147a22e9-2356-4e56-b3de-1f5842ae4a3b/resourceGroups/myresourcegroup/providers/Microsoft.Network/dnszones/contoso.com/NS/@",
    "metadata": null,
    "name": "@",
    "nsRecords": [
      {
        "nsdname": "ns1-04.azure-dns.com."
      },
      {
        "nsdname": "ns2-04.azure-dns.net."
      },
      {
        "nsdname": "ns3-04.azure-dns.org."
      },
      {
        "nsdname": "ns4-04.azure-dns.info."
      }
    ],
    "resourceGroup": "myresourcegroup",
    "ttl": 172800,
    "type": "Microsoft.Network/dnszones/NS"
  },
  {
    "etag": "0e48e82f-f194-4d3e-a864-7e109a95c73a",
    "id": "/subscriptions/147a22e9-2356-4e56-b3de-1f5842ae4a3b/resourceGroups/myresourcegroup/providers/Microsoft.Network/dnszones/contoso.com/SOA/@",
    "metadata": null,
    "name": "@",
    "resourceGroup": "myresourcegroup",
    "soaRecord": {
      "email": "azuredns-hostmaster.microsoft.com",
      "expireTime": 2419200,
      "host": "ns1-04.azure-dns.com.",
      "minimumTtl": 300,
      "refreshTime": 3600,
      "retryTime": 300,
      "serialNumber": 1
    },
    "ttl": 3600,
    "type": "Microsoft.Network/dnszones/SOA"
  }
]
```

> [!NOTE]
> Os conjuntos de registos na raiz (ou *vértice*) de uma Zona DNS utilizam **@** como o nome do conjunto de registos.

### <a name="test-name-servers"></a>Testar servidores de nomes

Pode testar se a sua zona DNS está presente nos nomes de servidores de DNS do Azure ao utilizar as ferramentas DNS como nslookup, dig ou o cmdlet do PowerShell `Resolve-DnsName`.

Se ainda não tiver delegado o seu domínio para utilizar a nova zona no DNS do Azure, tem de direcionar a consulta DNS diretamente para um dos servidores de nome da sua zona. Os servidores de nomes para a sua zona são indicados nos registos NS, como indicado por `az network dns record-set list`.

O exemplo seguinte utiliza a ferramenta "dig" para consultar o domínio contoso.com ao utilizar os servidores de nomes atribuídos à zona DNS. Certifique-se de que substitui os valores corretos para a sua zona.

```
  > dig @ns1-01.azure-dns.com contoso.com
  
  <<>> DiG 9.10.2-P2 <<>> @ns1-01.azure-dns.com contoso.com
(1 server found)
global options: +cmd
  Got answer:
->>HEADER<<- opcode: QUERY, status: NOERROR, id: 60963
  flags: qr aa rd; QUERY: 1, ANSWER: 0, AUTHORITY: 1, ADDITIONAL: 1
  WARNING: recursion requested but not available

  OPT PSEUDOSECTION:
  EDNS: version: 0, flags:; udp: 4000
  QUESTION SECTION:
contoso.com.                        IN      A

  AUTHORITY SECTION:
contoso.com.         3600     IN      SOA     ns1-01.azure-dns.com. azuredns-hostmaster.microsoft.com. 1 3600 300 2419200 300

Query time: 93 msec
SERVER: 208.76.47.5#53(208.76.47.5)
WHEN: Tue Jul 21 16:04:51 Pacific Daylight Time 2015
MSG SIZE  rcvd: 120
```

## <a name="next-steps"></a>Passos seguintes

Depois de criar uma zona DNS, [crie conjuntos de registos e registos DNS](dns-getstarted-create-recordset-cli.md) na sua zona.



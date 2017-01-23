---
title: Criar uma zona DNS ao utilizar a CLI| Microsoft Docs
description: "Saiba como criar zonas DNS no DNS do Azure. Este é um guia passo a passo para criar e gerir a sua primeira zona DNS com a CLI do Azure."
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
ms.date: 12/21/2016
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: f156e4d4c5ffddb7e93ebf21baa75864e0e260e9
ms.openlocfilehash: d00792a4bb19e194dbbcee8b9c11e4a744891388

---

# <a name="create-an-azure-dns-zone-using-cli"></a>Criar uma zona DNS do Azure ao utilizar a CLI

> [!div class="op_single_selector"]
> * [Portal do Azure](dns-getstarted-create-dnszone-portal.md)
> * [PowerShell](dns-getstarted-create-dnszone.md)
> * [CLI do Azure](dns-getstarted-create-dnszone-cli.md)

Este artigo explica-lhe os passos para criar uma zona DNS com a CLI do Azure de várias plataformas, que está disponível para Windows, Mac e Linux. Também pode criar uma zona DNS com o [Azure PowerShell](dns-getstarted-create-dnszone.md) ou o [portal do Azure](dns-getstarted-create-dnszone-portal.md).

[!INCLUDE [dns-create-zone-about](../../includes/dns-create-zone-about-include.md)]

[!INCLUDE [dns-cli-setup](../../includes/dns-cli-setup-include.md)]


## <a name="create-a-dns-zone"></a>Criar uma zona DNS

Uma zona DNS é criada ao utilizar o comando `azure network dns zone create`. Para ver a ajuda deste comando, escreva `azure network dns zone create -h`.

O exemplo seguinte cria uma zona DNS denominada *contoso.com* no grupo de recursos denominado *MyResourceGroup*. Utilize o exemplo para criar uma zona DNS, substituindo os valores pelos seus.

```azurecli
azure network dns zone create MyResourceGroup contoso.com
```

## <a name="verify-your-dns-zone"></a>Verificar a zona DNS

### <a name="view-records"></a>Ver registos

Criar uma zona DNS também cria os seguintes registos DNS:

* O registo *Início da autoridade* (SOA). Este registo está presente na raiz de cada zona DNS.
* Os registos do servidor de nomes (NS) autoritativo. Estes registos mostram que servidores de nome estão a alojar a zona. O DNS do Azure utiliza um conjunto de servidores de nome e, por isso, diferentes servidores de nomes podem ser atribuídos a diferentes zonas no DNS do Azure. Para obter mais informações, veja [Delegar um domínio ao DNS do Azure](dns-domain-delegation.md).

Para ver estes registos, utilize `azure network dns-record-set list`:

```azurecli
azure network dns record-set list MyResourceGroup contoso.com

info:    Executing command network dns record-set list
+ Looking up the DNS Record Sets
data:    Name                            : @
data:    Type                            : NS
data:    TTL                             : 172800
data:    Records:
data:      ns1-01.azure-dns.com.
data:      ns2-01.azure-dns.net.
data:      ns3-01.azure-dns.org.
data:      ns4-01.azure-dns.info.
data:
data:    Name                            : @
data:    Type                            : SOA
data:    TTL                             : 3600
data:    Email                           : azuredns-hostmaster.microsoft.com
data:    Host                            : ns1-01.azure-dns.com.
data:    Serial Number                   : 2
data:    Refresh Time                    : 3600
data:    Retry Time                      : 300
data:    Expire Time                     : 2419200
data:    Minimum TTL                     : 300
data:
info:    network dns record-set list command OK
```

> [!NOTE]
> Os conjuntos de registos na raiz (ou *vértice*) de uma Zona DNS utilizam **@** como o nome do conjunto de registos.

### <a name="test-name-servers"></a>Testar servidores de nomes

Pode testar se a sua zona DNS está presente nos nomes de servidores de DNS do Azure ao utilizar as ferramentas DNS como nslookup, dig ou o cmdlet do PowerShell `Resolve-DnsName`.

Se ainda não tiver delegado o seu domínio para utilizar a nova zona no DNS do Azure, tem de direcionar a consulta DNS diretamente para um dos servidores de nome da sua zona. Os servidores de nomes para a sua zona são indicados nos registos NS, como indicado por `azure network dns record-set list`.

O exemplo seguinte utiliza a ferramenta "dig" para consultar o domínio contoso.com ao utilizar os servidores de nomes atribuídos à zona DNS. Certifique-se de que substitui os valores corretos para a sua zona.

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

## <a name="next-steps"></a>Passos seguintes

Depois de criar uma zona DNS, [crie conjuntos de registos e registos DNS](dns-getstarted-create-recordset-cli.md) na sua zona.




<!--HONumber=Dec16_HO3-->



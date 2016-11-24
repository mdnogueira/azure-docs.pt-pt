---
title: Criar uma zona DNS ao utilizar a CLI| Microsoft Docs
description: "Saiba como criar zonas DNS passo a passo para o DNS do Azure de modo a começar a alojar o seu domínio de DNS ao utilizar a CLI"
services: dns
documentationcenter: na
author: sdwheeler
manager: carmonm
ms.assetid: 1514426a-133c-491a-aa27-ee0962cea9dc
ms.service: dns
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/16/2016
ms.author: sewhee
translationtype: Human Translation
ms.sourcegitcommit: 02d720a04fdc0fa302c2cb29b0af35ee92c14b3b
ms.openlocfilehash: 0dd7bc85776226b7f3a2ad75271a51c22d8205a5

---

# <a name="create-an-azure-dns-zone-using-cli"></a>Criar uma zona DNS do Azure ao utilizar a CLI

> [!div class="op_single_selector"]
> * [Portal do Azure](dns-getstarted-create-dnszone-portal.md)
> * [PowerShell](dns-getstarted-create-dnszone.md)
> * [CLI do Azure](dns-getstarted-create-dnszone-cli.md)

Este artigo descreve os passos para criar uma zona DNS ao utilizar a CLI. Também pode criar uma zona DNS ao utilizar o PowerShell ou o portal do Azure.

[!INCLUDE [dns-create-zone-about](../../includes/dns-create-zone-about-include.md)]

## <a name="before-you-begin"></a>Antes de começar

Estas instruções utilizam a CLI do Microsoft Azure. Certifique-se de que atualiza para a CLI do Azure mais recente (0.9.8 ou posterior) para utilizar os comandos de DNS do Azure. Escreva `azure -v` para verificar qual a versão da CLI do Azure que se encontra instalada no seu computador.

## <a name="step-1---set-up-azure-cli"></a>Passo 1 – Configurar a CLI do Azure

### <a name="1-install-azure-cli"></a>1. Instalar a CLI do Azure

Pode instalar a CLI do Azure para Windows, Linux ou MAC. Os passos que se seguem têm de ser concluídos antes de poder gerir o DNS do Azure ao utilizar a CLI do Azure. Existem mais informações disponíveis em [Instalar a CLI do Azure](../xplat-cli-install.md). Os comandos DNS requerem a versão 0.9.8 ou posterior da CLI do Azure.

Todos os comandos do fornecedor de rede na CLI podem ser encontrados através do seguinte comando:

```azurecli
azure network
```

### <a name="2-switch-cli-mode"></a>2. Alternar o modo da CLI

O DNS do Azure utiliza o Azure Resource Manager. Certifique-se de que alterna o modo da CLI para utilizar comandos do ARM.

```azurecli
azure config mode arm
```

### <a name="3-sign-in-to-your-azure-account"></a>3. Inicie sessão na sua conta do Azure

Ser-lhe-á solicitado para autenticar com as suas credenciais. Tenha em atenção que só pode utilizar contas ORGID.

```azurecli
azure login -u "username"
```

### <a name="4-select-the-subscription"></a>4. Selecionar a subscrição

Escolha qual das suas subscrições do Azure utilizar.

```azurecli
azure account set "subscription name"
```

### <a name="5-create-a-resource-group"></a>5. Criar um grupo de recursos

O Azure Resource Manager requer que todos os grupos de recursos especifiquem uma localização. Isto é utilizado como a localização predefinida para recursos nesse grupo de recursos. No entanto, uma vez que todos os recursos DNS são globais, não regionais, a opção de localização do grupo de recursos não tem impacto no DNS do Azure.

Pode ignorar este passo se estiver a utilizar um grupo de recursos existente.

```azurecli
azure group create -n myresourcegroup --location "West US"
```

### <a name="6-register"></a>6. Registar

O serviço do DNS do Azure é gerido pelo fornecedor de recursos Microsoft.Network. A subscrição do Azure tem de estar registada para utilizar este fornecedor de recursos antes de poder utilizar o DNS do Azure. Esta é uma operação única para cada subscrição.

```azurecli
azure provider register --namespace Microsoft.Network
```

## <a name="step-2---create-a-dns-zone"></a>Passo 2 – Criar uma zona DNS

Uma zona DNS é criada ao utilizar o comando `azure network dns zone create`. Opcionalmente, pode criar uma zona DNS juntamente com etiquetas. As etiquetas são uma lista de pares nome-valor e são utilizadas pelo Azure Resource Manager para recursos de etiquetas para fins de faturação ou agrupamento. Para obter mais informações sobre etiquetas, consulte [Utilizar etiquetas para organizar os recursos do Azure](../resource-group-using-tags.md).

No DNS do Azure, os nomes de zona devem ser especificados sem acabar em **“.”**. Por exemplo, como “**contoso.com**” em vez de “**contoso.com.**”.

### <a name="to-create-a-dns-zone"></a>Criar uma zona DNS

O exemplo abaixo cria uma zona DNS denominada *contoso.com* no grupo de recursos denominado *MyResourceGroup*.

Utilize o exemplo para criar a sua zona DNS, substituindo os valores pelos seus.

```azurecli
azure network dns zone create myresourcegroup contoso.com
```

### <a name="to-create-a-dns-zone-and-tags"></a>Criar uma zona DNS e etiquetas.

A CLI do DNS do Azure suporta etiquetas de zonas DNS especificadas, ao utilizar o parâmetro opcional *-Tag*. O exemplo seguinte mostra como criar uma zona DNS com duas etiquetas, project = demo e env = test.

Utilize o exemplo abaixo para criar uma zona DNS e etiquetas, ao substituir os valores pelos seus.

```azurecli
azure network dns zone create myresourcegroup contoso.com -t "project=demo";"env=test"
```

## <a name="view-records"></a>Ver registos

Criar uma zona DNS também cria os seguintes registos DNS:

* O registo “Início da Autoridade” (SOA). Este registo está presente na raiz da cada zona DNS.
* Os registos do servidor de nomes (NS) autoritativo. Estes mostram que servidores de nome estão a alojar a zona. O DNS do Azure utiliza um conjunto de servidores de nome e, por isso, diferentes servidores de nomes podem ser atribuídos a diferentes zonas no DNS do Azure. Consulte [Delegar um domínio ao DNS do Azure](dns-domain-delegation.md) para obter mais informações.

Para ver estes registos, utilize `azure network dns-record-set show`.

*Utilização: network dns record-set show \<resource-group\> \<dns-zone-name\> \<name\> \<type\>*

No exemplo abaixo, se executar o comando com o grupo de recursos *myresourcegroup*, o nome do conjunto de registos *"@"* (para um registo de raiz) e escrever *SOA*, irá produzir o resultado seguinte:

    azure network dns record-set show myresourcegroup "contoso.com" "@" SOA
    info:    Executing command network dns-record-set show
    + Looking up the DNS record set "@"
    data:    Id                              : /subscriptions/#######################/resourceGroups/myresourcegroup/providers/Microsoft.Network/dnszones/contoso.com/SOA/@
    data:    Name                            : @
    data:    Type                            : Microsoft.Network/dnszones/SOA
    data:    Location                        : global
    data:    TTL                             : 3600
    data:    SOA record:
    data:      Email                         : msnhst.microsoft.com
    data:      Expire time                   : 604800
    data:      Host                          : edge1.azuredns-cloud.net
    data:      Minimum TTL                   : 300
    data:      Refresh time                  : 900
    data:      Retry time                    : 300
    data:                                    :

Para ver os registos NS criados com a zona, utilize o seguinte comando:

    azure network dns record-set show myresourcegroup "contoso.com" "@" NS
    info:    Executing command network dns-record-set show
    + Looking up the DNS record set "@"
    data:    Id                              : /subscriptions/#######################/resourceGroups/myresourcegroup/providers/Microsoft.Network/dnszones/contoso.com/NS/@
    data:    Name                            : @
    data:    Type                            : Microsoft.Network/dnszones/NS
    data:    Location                        : global
    data:    TTL                             : 3600
    data:    NS records
    data:        Name server domain name     : ns1-05.azure-dns.com
    data:        Name server domain name     : ns2-05.azure-dns.net
    data:        Name server domain name     : ns3-05.azure-dns.org
    data:        Name server domain name     : ns4-05.azure-dns.info
    data:
    info:    network dns-record-set show command OK

> [!NOTE]
> Os conjuntos de registos na raiz (ou *vértice*) de uma Zona DNS utilizam **@** como o nome do conjunto de registos.

## <a name="test"></a>Teste

Pode testar a sua zona DNS ao utilizar as ferramentas DNS como nslookup, DIG ou o cmdlet do PowerShell `Resolve-DnsName`.

Se ainda não tiver delegado o seu domínio para utilizar a nova zona no DNS do Azure, tem de direcionar a consulta DNS diretamente para um dos servidores de nome da sua zona. Os servidores de nomes para a sua zona são indicados nos registos do NS, tal como indicado por “azure network dns record-set show” acima. Certifique-se de que substitui os valores corretos para a zona no comando abaixo.

O exemplo seguinte utiliza a ferramenta DIG para consultar o domínio contoso.com ao utilizar os servidores de nomes atribuídos para a zona DNS. A consulta tem de apontar para um servidor de nomes para o qual utilizámos *@\<servidor de nome da zona\>* e o nome da zona com DIG.

     <<>> DiG 9.10.2-P2 <<>> @ns1-05.azure-dns.com contoso.com
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
    contoso.com.         300     IN      SOA     edge1.azuredns-cloud.net.
    msnhst.microsoft.com. 6 900 300 604800 300

    Query time: 93 msec
    SERVER: 208.76.47.5#53(208.76.47.5)
    WHEN: Tue Jul 21 16:04:51 Pacific Daylight Time 2015
    MSG SIZE  rcvd: 120

## <a name="next-steps"></a>Passos seguintes

Depois de criar uma zona DNS, crie [conjuntos de registos e registos](dns-getstarted-create-recordset-cli.md) para iniciar a resolução de nomes para o seu domínio de Internet.




<!--HONumber=Nov16_HO3-->



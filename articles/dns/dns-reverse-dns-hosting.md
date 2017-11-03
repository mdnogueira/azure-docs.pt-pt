---
title: Alojamento inverso zonas de pesquisa DNS no DNS do Azure | Microsoft Docs
description: Saiba como utilizar o DNS do Azure para alojar as zonas de pesquisa inversas do DNS para os intervalos de IP
services: dns
documentationcenter: na
author: jtuliani
manager: timlt
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/29/2017
ms.author: jonatul
ms.openlocfilehash: 3e10b25d2f9b91c96af2958fef6dc6a4fdbff301
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="hosting-reverse-dns-lookup-zones-in-azure-dns"></a>Aloja zonas de pesquisa inversas DNS no DNS do Azure

Este artigo explica como alojar as zonas de pesquisa inversas do DNS para os intervalos IP atribuídos no DNS do Azure. Os intervalos IP representados por zona de pesquisa inversa devem ser atribuídos à sua organização, normalmente pelo seu ISP.

Para configurar o DNS inversa para o endereço IP pertencente ao Azure atribuído ao seu serviço do Azure, consulte [configurar a pesquisa inversa de endereços IP alocados ao seu serviço do Azure](dns-reverse-dns-for-azure-services.md).

Antes de ler este artigo, deve estar familiarizado com esta [descrição geral do DNS inversa e de suporte no Azure](dns-reverse-dns-overview.md).

Este artigo explica os passos para criar a sua primeira zona DNS de pesquisa inversa e de registo no portal do Azure, Azure PowerShell, a CLI do Azure 1.0 ou 2.0 de CLI do Azure.

## <a name="create-a-reverse-lookup-dns-zone"></a>Criar uma zona de pesquisa inversa de DNS

1. Iniciar sessão para o [portal do Azure](https://portal.azure.com)
1. No Hub menu, clique em e clique em **novo** > **redes** > e, em seguida, clique em **zona DNS** para abrir o **zona DNS criar** painel.

   ![Zona DNS](./media/dns-reverse-dns-hosting/figure1.png)

1. No **zona DNS criar** painel, nome da sua zona DNS. O nome da zona é crafted de forma diferente para os prefixos de IPv4 e IPv6. Utilizar qualquer um das instruções para [IPV4](#ipv4) ou [IPv6](#ipv6) para nomear a sua zona. Quando concluir clique **criar** para criar a zona.

### <a name="ipv4"></a>IPv4

O nome de uma zona de pesquisa inversa IPv4 baseia-se no intervalo de IP representa. Deverá estar no seguinte formato: `<IPv4 network prefix in reverse order>.in-addr.arpa`. Para obter exemplos, consulte [descrição geral do DNS inversa e de suporte no Azure](dns-reverse-dns-overview.md#ipv4).

> [!NOTE]
> Quando criar classless zonas de pesquisa inversas de DNS no DNS do Azure, tem de utilizar um hífen (`-`) em vez de uma barra ('/ ') no nome da zona.
>
> Por exemplo, para 192.0.2.128/26 o intervalo IP, tem de utilizar `128-26.2.0.192.in-addr.arpa` como o nome da zona em vez de `128/26.2.0.192.in-addr.arpa`.
>
> Isto acontece porque, embora ambos são suportadas pelo normas DNS, de zona DNS que contém a barra de nomes (`/`) carateres não são suportadas no DNS do Azure.

O exemplo seguinte mostra como criar uma zona DNS inversa 'Classe C' com o nome `2.0.192.in-addr.arpa` no DNS do Azure através do portal do Azure:

 ![criar a zona DNS](./media/dns-reverse-dns-hosting/figure2.png)

A 'localização do grupo de recursos' define a localização para o grupo de recursos e não tem impacto sobre a zona DNS. A localização de zona DNS é sempre 'global' e não é apresentada.

Os exemplos seguintes mostram como concluir esta tarefa com o Azure PowerShell e a CLI do Azure:

#### <a name="powershell"></a>PowerShell

```powershell
New-AzureRmDnsZone -Name 2.0.192.in-addr.arpa -ResourceGroupName MyResourceGroup
```

#### <a name="azure-cli-10"></a>CLI do Azure 1.0

```azurecli
azure network dns zone create MyResourceGroup 2.0.192.in-addr.arpa
```

#### <a name="azure-cli-20"></a>CLI 2.0 do Azure

```azurecli
az network dns zone create -g MyResourceGroup -n 2.0.192.in-addr.arpa
```

### <a name="ipv6"></a>IPv6

O nome de uma zona de pesquisa inversa IPv6 deve estar no seguinte formato: `<IPv6 network prefix in reverse order>.ip6.arpa`.  Para obter exemplos, consulte [descrição geral do DNS inversa e de suporte no Azure](dns-reverse-dns-overview.md#ipv6).


O exemplo seguinte mostra como criar uma IPv6 inversa zona de pesquisa DNS com o nome `0.0.0.0.d.c.b.a.8.b.d.0.1.0.0.2.ip6.arpa` no DNS do Azure através do portal do Azure:

 ![criar a zona DNS](./media/dns-reverse-dns-hosting/figure3.png)

A 'localização do grupo de recursos' define a localização para o grupo de recursos e não tem impacto sobre a zona DNS. A localização de zona DNS é sempre 'global' e não é apresentada.

Os exemplos seguintes mostram como concluir esta tarefa com o Azure PowerShell e a CLI do Azure:

#### <a name="powershell"></a>PowerShell

```powershell
New-AzureRmDnsZone -Name 0.0.0.0.d.c.b.a.8.b.d.0.1.0.0.2.ip6.arpa -ResourceGroupName MyResourceGroup
```

#### <a name="azurecli-10"></a>AzureCLI 1.0

```azurecli
azure network dns zone create MyResourceGroup 0.0.0.0.d.c.b.a.8.b.d.0.1.0.0.2.ip6.arpa
```

#### <a name="azurecli-20"></a>AzureCLI 2.0

```azurecli
az network dns zone create -g MyResourceGroup -n 0.0.0.0.d.c.b.a.8.b.d.0.1.0.0.2.ip6.arpa
```

## <a name="delegate-a-reverse-dns-lookup-zone"></a>Delegar a uma zona de pesquisa inversa DNS

Ter criado a sua zona de pesquisa inversa DNS, tem de garantir que a zona é delegada da zona principal. Delegação de DNS permite que o processo de resolução de nome DNS para localizar os servidores de nomes que alojam a zona de pesquisa inversa DNS. Isto permite que os servidores de nomes responder às consultas inversas de DNS para os endereços IP no intervalo de endereços.

Para zonas de pesquisa direta, o processo de delegação de uma zona DNS é descrito no [delegar o domínio ao DNS do Azure](dns-delegate-domain-azure-dns.md). A delegação de zonas de pesquisa inversa funciona da mesma forma. A única diferença é que tem de configurar os servidores de nomes com o ISP que forneceu o intervalo de IP, em vez da sua entidade de registo de nome de domínio.

## <a name="create-a-dns-ptr-record"></a>Criar um registo DNS PTR

### <a name="ipv4"></a>IPv4

O exemplo seguinte explica o processo de criação de um registo PTR numa zona DNS inversa no DNS do Azure. Para outros tipos de registo e para modificar registos existentes, consulte [Manage DNS records and record sets by using the Azure portal (Gerir registos DNS e conjuntos de registos com o portal do Azure)](dns-operations-recordsets-portal.md).

1.  No topo do painel **Zona DNS**, selecione **+ Conjunto de registos**, para abrir o painel **Adicionar conjunto de registos**.

 ![Zona DNS](./media/dns-reverse-dns-hosting/figure4.png)

1. No **adicionar conjunto de registos** painel. 
1. Selecione **PTR** do registo "**tipo**" menu.  
1. O nome do registo definido para um registo PTR tem de ser o resto do endereço de IPv4 na ordem inversa. Neste exemplo, os três primeiros octetos já estão povoados como parte do nome de zona (.2.0.192). Por conseguinte, apenas o último octeto é fornecido no campo nome. Por exemplo, pode dar nome do conjunto de registos "**15**" para um recurso cujo endereço IP é 192.0.2.15.  
1. No "**nome de domínio**" campo, introduza o nome de domínio completamente qualificado (FQDN) do recurso a utilizar o IP.
1. Selecione **OK** na parte inferior do painel para criar o registo DNS.

 ![adicionar o conjunto de registos](./media/dns-reverse-dns-hosting/figure5.png)

Seguem-se exemplos sobre como concluir esta tarefa com o PowerShell e o AzureCLI:

#### <a name="powershell"></a>PowerShell

```powershell
New-AzureRmDnsRecordSet -Name 15 -RecordType PTR -ZoneName 2.0.192.in-addr.arpa -ResourceGroupName MyResourceGroup -Ttl 3600 -DnsRecords (New-AzureRmDnsRecordConfig -Ptrdname "dc1.contoso.com")
```
#### <a name="azurecli-10"></a>AzureCLI 1.0

```azurecli
azure network dns record-set add-record MyResourceGroup 2.0.192.in-addr.arpa 15 PTR --ptrdname dc1.contoso.com  
```

#### <a name="azurecli-20"></a>AzureCLI 2.0

```azurecli
    az network dns record-set ptr add-record -g MyResourceGroup -z 2.0.192.in-addr.arpa -n 15 --ptrdname dc1.contoso.com
```

### <a name="ipv6"></a>IPv6

O exemplo seguinte explica o processo de criação do novo registo de "PTR". Para outros tipos de registo e para modificar registos existentes, consulte [Manage DNS records and record sets by using the Azure portal (Gerir registos DNS e conjuntos de registos com o portal do Azure)](dns-operations-recordsets-portal.md).

1. Na parte superior a **painel de zona DNS**, selecione **+ conjunto de registos** para abrir o **adicionar conjunto de registos** painel.

  ![Painel de zona DNS](./media/dns-reverse-dns-hosting/figure6.png)

2. No **adicionar conjunto de registos** painel. 
3. Selecione **PTR** do registo "**tipo**" menu.  
4. O nome do registo definido para um registo PTR tem de ser o resto do endereço IPv6 na ordem inversa. Não tem de incluir quaisquer zero compressão. Neste exemplo, os primeiro 64 bits de IPv6 já estão povoados como parte do nome de zona (0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa). Por conseguinte, apenas os últimos 64 bits são fornecidos no campo nome. Os últimos 64 bits do endereço IP são introduzidos na ordem inversa, utilizando um período como delimitador entre cada número hexadecimal. Por exemplo, pode dar nome do conjunto de registos "**e.5.0.4.9.f.a.1.c.b.0.1.4.2.5.f**" para um recurso cujo endereço IP é 2001:0db8:abdc:0000:f524:10bc:1af9:405e.  
5. No "**nome de domínio**" campo, introduza o nome de domínio completamente qualificado (FQDN) do recurso a utilizar o IP.
6. Selecione **OK** na parte inferior do painel para criar o registo DNS.

![adicionar o painel do conjunto de registos](./media/dns-reverse-dns-hosting/figure7.png)

Seguem-se exemplos sobre como concluir esta tarefa com o PowerShell e o AzureCLI:

#### <a name="powershell"></a>PowerShell

```powershell
New-AzureRmDnsRecordSet -Name "e.5.0.4.9.f.a.1.c.b.0.1.4.2.5.f" -RecordType PTR -ZoneName 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa -ResourceGroupName MyResourceGroup -Ttl 3600 -DnsRecords (New-AzureRmDnsRecordConfig -Ptrdname "dc2.contoso.com")
```

#### <a name="azurecli-10"></a>AzureCLI 1.0

```
azure network dns record-set add-record MyResourceGroup 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa e.5.0.4.9.f.a.1.c.b.0.1.4.2.5.f PTR --ptrdname dc2.contoso.com 
```
 
#### <a name="azurecli-20"></a>AzureCLI 2.0

```azurecli
    az network dns record-set ptr add-record -g MyResourceGroup -z 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa -n e.5.0.4.9.f.a.1.c.b.0.1.4.2.5.f --ptrdname dc2.contoso.com
```

## <a name="view-records"></a>Ver registos

Para ver os registos que criou, navegue até à sua zona DNS no portal do Azure. Na parte inferior do **zona DNS** painel, pode ver os registos para a zona DNS. Deverá ver os registos NS e SOA predefinidos, que são criados em cada zona, assim como quaisquer novos registos que tenha criado.

### <a name="ipv4"></a>IPv4

No painel de zona DNS, que mostra os registos IPv4 PTR:

![Painel de zona DNS](./media/dns-reverse-dns-hosting/figure8.png)

Os exemplos seguintes mostram como ver os registos PTR utilizando o PowerShell ou a CLI do Azure:

#### <a name="powershell"></a>PowerShell

```powershell
Get-AzureRmDnsRecordSet -ZoneName 2.0.192.in-addr.arpa -ResourceGroupName MyResourceGroup
```

#### <a name="azure-cli-10"></a>CLI do Azure 1.0

```azurecli
    azure network dns record-set list MyResourceGroup 2.0.192.in-addr.arpa
```

#### <a name="azure-cli-20"></a>CLI 2.0 do Azure

```azurecli
    azure network dns record-set list -g MyResourceGroup -z 2.0.192.in-addr.arpa
```

### <a name="ipv6"></a>IPv6

No painel de zona DNS, que mostra os registos IPv6 PTR:

![Painel de zona DNS](./media/dns-reverse-dns-hosting/figure9.png)

Seguem-se exemplos sobre como ver os registos com o PowerShell e o AzureCLI:

#### <a name="powershell"></a>PowerShell

```powershell
Get-AzureRmDnsRecordSet -ZoneName 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa -ResourceGroupName MyResourceGroup
```

#### <a name="azure-cli-10"></a>CLI do Azure 1.0

```azurecli
    azure network dns record-set list MyResourceGroup 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa
```

#### <a name="azure-cli-20"></a>CLI 2.0 do Azure

```azurecli
    azure network dns record-set list -g MyResourceGroup -z 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa
```

## <a name="faq"></a>FAQ

### <a name="can-i-host-reverse-dns-lookup-zones-for-my-isp-assigned-ip-blocks-on-azure-dns"></a>Pode alojar zonas de pesquisa inversas DNS para os meus blocos IP ISP atribuído no DNS do Azure?

Sim. Que aloja as zonas de pesquisa inversa (ARPA) para os seus próprios intervalos IP no DNS do Azure é totalmente suportada.

Crie a zona de pesquisa inversa no DNS do Azure como explained neste artigo, em seguida, trabalhar com o seu ISP para [delegar a zona](dns-domain-delegation.md).  Em seguida, pode gerir os registos PTR para cada pesquisa inversa da mesma forma que outros tipos de registo.

### <a name="how-much-does-hosting-my-reverse-dns-lookup-zone-cost"></a>É quanto o meu custo de zona de pesquisa DNS inverso de alojamento?

Que aloja a zona de pesquisa inversa DNS para o bloco IP ISP atribuído no DNS do Azure é cobrado [a taxas padrão do DNS do Azure](https://azure.microsoft.com/pricing/details/dns/).

### <a name="can-i-host-reverse-dns-lookup-zones-for-both-ipv4-and-ipv6-addresses-in-azure-dns"></a>Pode alojar zonas de pesquisa DNS inversas para endereços IPv4 e IPv6 no DNS do Azure?

Sim. Este artigo explica como criar IPv4 e IPv6 zonas de pesquisa DNS inversas no DNS do Azure.

### <a name="can-i-import-an-existing-reverse-dns-lookup-zone"></a>Pode importar uma zona de pesquisa DNS inversa existente?

Sim. Pode utilizar a CLI do Azure para importar existentes zonas DNS para o DNS do Azure. Isto funciona para as zonas de pesquisa direta e zonas de pesquisa inversa.

Para obter mais informações, consulte [importar e exportar um ficheiro de zona DNS utilizando a CLI do Azure](dns-import-export.md).

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre inversa DNS, consulte [pesquisa inversa de DNS em Wikipedia](http://en.wikipedia.org/wiki/Reverse_DNS_lookup).
<br>
Saiba como [gerir registos inversas de DNS para os serviços do Azure](dns-reverse-dns-for-azure-services.md).

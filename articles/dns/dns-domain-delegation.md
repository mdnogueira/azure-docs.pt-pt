---
title: Delegar o seu domínio ao DNS do Azure | Microsoft Docs
description: Compreenda como alterar a delegação de domínios e utilizar servidores de nomes de DNS do Azure para fornecer o alojamento de domínios.
services: dns
documentationcenter: na
author: cherylmc
manager: carmonm
editor: ''

ms.service: dns
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/30/2016
ms.author: cherylmc

---
# Delegar um domínio ao DNS do Azure
O DNS do Azure permite-lhe alojar uma zona DNS e gerir os registos de DNS para um domínio no Azure. Para que as consultas do DNS de um domínio alcancem o DNS do Azure, o domínio tem de ser delegado ao DNS do Azure a partir do domínio principal. Tenha em atenção que o DNS do Azure não é a entidade de registo de domínios. Este artigo explica como funciona a delegação de domínios e como delegar domínios ao DNS do Azure.

## Como funciona a delegação ao DNS
### Domínios e zonas
O Sistema de Nomes de Domínio é uma hierarquia de domínios. A hierarquia começa a partir do domínio “raiz”, cujo nome é simplesmente “**.**”.  Abaixo deste domínio surgem os domínios de nível superior, como “com”, “net”, “org”, “pt” ou “fr”.  Abaixo destes estão os domínios de segundo nível, tais como “org.pt” ou “co.uk”.  E assim sucessivamente. Os domínios na hierarquia do DNS estão alojados com recurso a zonas DNS separadas. Estas zonas são distribuídas globalmente, alojadas por servidores de nomes DNS em todo o mundo.

**Zona DNS**

Um domínio é um nome exclusivo no Sistema de Nomes de Domínio, por exemplo, “contoso.com”. Uma zona DNS é utilizada para alojar os registos DNS para um determinado domínio. Por exemplo, o domínio “contoso.com” pode conter vários registos DNS, como “mail.contoso.com” (para um servidor de e-mail) e “www.contoso.com” (para um site).

**Entidade de registo de domínios**

Uma entidade de registo de domínios é uma empresa que pode fornecer nomes de domínios de Internet. A entidade irá verificar se o domínio da Internet que pretende utilizar está disponível e permite-lhe que o compre. Assim que o nome de domínio estiver registado, será o proprietário legal do nome do domínio. Se já tiver um domínio da Internet, irá utilizar a entidade de registo de domínios atual para delegar ao DNS do Azure.

> [!NOTE]
> Para saber mais informações sobre quem é o proprietário de um determinado nome de domínio ou para obter informações sobre como comprar um domínio, consulte [Gestão de domínios da Internet no Azure AD](https://msdn.microsoft.com/library/azure/hh969248.aspx).
> 
> 

### Resolução e delegação
Existem dois tipos de servidores DNS:

* Um servidor DNS *autoritativo* aloja zonas DNS. Responde a consultas DNS para os registos apenas dessas zonas.
* Um servidor DNS *recursivo* não aloja zonas DNS. Responde a todas as consultas DNS ao chamar servidores DNS autoritativos para recolher os dados de que necessita.

> [!NOTE]
> O Azure DNS fornece um serviço DNS autoritativo.  Não fornece um serviço DNS recursivo.
> 
> Os Cloud Services e as VMs no Azure são automaticamente configurados para utilizar serviços DNS recursivos fornecidos separadamente como parte da infraestrutura do Azure.  Para obter informações sobre como alterar estas definições de DNS, veja [Name Resolution in Azure (Resolução de Nomes no Azure)](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server).
> 
> 

Normalmente, os clientes DNS em PCs ou em dispositivos móveis chamam um servidor DNS recursivo para efetuar quaisquer consultas de DNS que as aplicações de cliente necessitem.

Quando um servidor DNS recursivo recebe uma consulta para um registo DNS, tal como “www.contoso.com”, tem primeiro de localizar o nome do servidor que aloja a zona do domínio “contoso.com”. Para tal, começa pelos servidores de nomes de raiz e, a partir daí, localiza os servidores de nomes que alojam a zona “com”. Em seguida, consulta os servidores de nomes “com” para localizar os servidores de nomes que alojam a zona “contoso.com”.  Finalmente, é capaz de consultar estes servidores de nome para “www.contoso.com”.  

Este processo é denominado resolver o nome DNS. Em termos mais rigorosos, a resolução de DNS inclui passos adicionais, tais como o seguimento de CNAMEs, mas que não são importantes para compreender como funciona a delegação ao DNS.

Como é que uma zona de principal “aponta” para os servidores de nomes de uma zona subordinada? Consegue fazê-lo ao utilizar um tipo especial de registo DNS chamado registo NS (NS significa servidor de nomes, “name server” em inglês). Por exemplo, a zona raiz contém registos NS para “com” e mostra os servidores de nomes para a zona “com”. Por sua vez, a zona “com” contém registos NS para “contoso.com”, que mostra os servidores de nomes para a zona “contoso.com”. Configurar os registos NS para uma zona subordinada numa zona principal é denominado delegar o domínio.

![Dns-nameserver](./media/dns-domain-delegation/image1.png)

Cada delegação, na verdade, tem duas cópias dos registos NS; uma na zona principal a apontar para a subordinada e outra na própria zona subordinada. A zona de “contoso.com” contém os registos NS para “contoso.com” (além dos registos NS em “com”). Estes são designados registos NS autoritativos e residem no vértice da zona subordinada.

## Delegação de um domínio ao DNS do Azure
Depois de criar a sua zona DNS no DNS do Azure, terá de configurar os registos NS na zona principal para fazer do DNS do Azure a origem autoritativa para a resolução de nomes para a sua zona. Para os domínios comprados a partir de uma entidade de registo, a entidade de registo irá oferecer a opção para configurar estes registos NS.

> [!NOTE]
> Não é necessário possuir um domínio para criar uma zona DNS com esse nome de domínio no DNS do Azure. No entanto, é necessário possuir o domínio para configurar a delegação ao DNS do Azure com a entidade de registo.
> 
> 

Por exemplo, suponha que compra o domínio “contoso.com” e cria uma zona com o nome “contoso.com” no DNS do Azure. Na qualidade de proprietário do domínio, a sua entidade de registo irá oferecer-lhe a opção de configurar os endereços de servidor de nomes (ou seja, os registos NS) do seu domínio. A entidade de registo armazenará estes registos NS no domínio principal, neste caso, “.com”. Os clientes em todo o mundo são então direcionados para o seu domínio na zona DNS do Azure ao tentar resolver os registos DNS em “contoso.com”.

### Localizar os nomes dos servidores de nome
Antes de pode delegar a zona DNS ao DNS do Azure, terá primeiro de conhecer os nomes dos servidores de nome da sua zona. O DNS do Azure aloca servidores de nomes a partir de um conjunto sempre que é criada uma zona.

A forma mais fácil de ver os servidores de nomes atribuídos à sua zona é através do portal do Azure.  Neste exemplo, foram atribuídos à zona “contoso.net” os servidores de nomes “ns1-01.azure-dns.com”, “ns2-01.azure-dns .net”, “ns3-01.azure-dns.org” e “ns4-01.azure-dns.info”:

 ![Dns-nameserver](./media/dns-domain-delegation/viewzonens500.png)

O DNS do Azure cria automaticamente registos NS autoritativos na sua zona, que contêm os servidores de nomes atribuídos.  Para ver os nomes dos servidores de nome através do Azure PowerShell ou da CLI do Azure, apenas tem de obter estes registos.

Através do Azure PowerShell, os registos NS autoritativos podem ser obtidos da seguinte forma. Tenha em atenção que o nome do registo “@” é utilizado para fazer referência a registos no vértice da zona. 

    PS> $zone = Get-AzureRmDnsZone –Name contoso.net –ResourceGroupName MyResourceGroup
    PS> Get-AzureRmDnsRecordSet –Name “@” –RecordType NS –Zone $zone

    Name              : @
    ZoneName          : contoso.net
    ResourceGroupName : MyResourceGroup
    Ttl               : 3600
    Etag              : 5fe92e48-cc76-4912-a78c-7652d362ca18
    RecordType        : NS
    Records           : {ns1-01.azure-dns.com, ns2-01.azure-dns.net, ns3-01.azure-dns.org,
                        ns4-01.azure-dns.info}
    Tags              : {}

Também pode utilizar a CLI do Azure de várias plataformas para obter os registos NS autoritativos e detetar, por conseguinte, os servidores de nomes atribuídos à sua zona:

    C:\> azure network dns record-set show MyResourceGroup contoso.net @ NS
    info:    Executing command network dns record-set show
        + Looking up the DNS Record Set "@" of type "NS"
    data:    Id                              : /subscriptions/.../resourceGroups/MyResourceGroup/providers/Microsoft.Network/dnszones/contoso.net/NS/@
    data:    Name                            : @
    data:    Type                            : Microsoft.Network/dnszones/NS
    data:    Location                        : global
    data:    TTL                             : 172800
    data:    NS records
    data:        Name server domain name     : ns1-01.azure-dns.com.
    data:        Name server domain name     : ns2-01.azure-dns.net.
    data:        Name server domain name     : ns3-01.azure-dns.org.
    data:        Name server domain name     : ns4-01.azure-dns.info.
    data:
    info:    network dns record-set show command OK

### Para configurar a delegação
Cada entidade de registo tem as suas próprias ferramentas de gestão de DNS para alterar os registos do servidor de nome de um domínio. Na página de gestão do DNS da entidade de registo, edite os registos NS e substitua os registos NS por aqueles que o DNS do Azure criou.

Quando delegar um domínio ao DNS do Azure, tem de utilizar os nomes dos servidores de nome fornecidos pelo DNS do Azure.  Deve sempre utilizar todos os quatro nomes de servidor de nome, independentemente do seu domínio.  A delegação de domínio não necessita que o nome do servidor de nome utilize o mesmo domínio de nível superior que o seu domínio.

Não deve utilizar «registos “glue”» para que apontem para os endereços IP do servidor de nomes do DNS do Azure, uma vez que estes endereços IP podem ser alterados no futuro. As delegações que utilizam nomes de servidores de nome na sua própria zona, por vezes denominados “servidores de nomes personalizados”, não são atualmente suportadas no DNS do Azure.

### Para confirmar que a resolução de nomes está a funcionar
Depois de concluir a delegação, pode confirmar que a resolução de nomes está a funcionar ao utilizar uma ferramenta como a “nslookup” para consultar o registo SOA da sua zona (que também é criado automaticamente quando a zona é criada).

Tenha em atenção que não tem de especificar os servidores de nomes de DNS do Azure, uma vez que o processo de resolução DNS normal irá encontrar os servidores de nomes automaticamente se a delegação tiver sido configurada corretamente.

    nslookup –type=SOA contoso.com

    Server: ns1-04.azure-dns.com
    Address: 208.76.47.4

    contoso.com
    primary name server = ns1-04.azure-dns.com
    responsible mail addr = msnhst.microsoft.com
    serial = 1
    refresh = 900 (15 mins)
    retry = 300 (5 mins)
    expire = 604800 (7 days)
    default TTL = 300 (5 mins)

## Delegação de subdomínios no DNS do Azure
Se pretende configurar uma zona subordinada em separado, pode delegar um subdomínio no DNS do Azure. Por exemplo, ao ter configurado e delegado “contoso.com” no DNS do Azure, suponha que pretende configurar uma zona subordinada em separado, “partners.contoso.com”. 

A configuração de um subdomínio segue um processo semelhante ao de uma delegação normal. A única diferença é que, no passo 3, os registos NS têm de ser criados na zona principal “contoso.com” no DNS do Azure, em vez de serem configurados através de uma entidade de registo de domínios.

1. Crie a zona subordinada “partners.contoso.com” no DNS do Azure.
2. Procure os registos NS autoritativos na zona subordinada para obter os servidores de nomes que alojam a zona subordinada no DNS do Azure.
3. Delegue a zona subordinada ao configurar os registos NS na zona principal a apontar para a zona subordinada.

### Delegar um subdomínio
O exemplo do PowerShell que se segue demonstra como isto funciona. Os mesmos passos podem ser executados através do Portal do Azure ou através da CLI do Azure de várias plataformas.

#### Passo 1. Criar zonas principais e subordinadas
Primeiro, criamos as zonas principais e subordinadas. Estas podem estar no mesmo grupo de recursos ou em grupos de recursos diferentes.

    $parent = New-AzureRmDnsZone -Name contoso.com -ResourceGroupName RG1
    $child = New-AzureRmDnsZone -Name partners.contoso.com -ResourceGroupName RG1

#### Passo 2. Obter registos NS
Em seguida, obtemos os registos NS autoritativos da zona subordinada conforme mostrado no exemplo seguinte.  Estes contêm os servidores de nomes atribuídos à zona subordinada. 

    $child_ns_recordset = Get-AzureRmDnsRecordSet -Zone $child -Name "@" -RecordType NS

#### Passo 3. Delegar a zona subordinada
Crie o conjunto de registos NS correspondente na zona principal para concluir a delegação. Tenha em atenção que o nome do conjunto de registos na zona principal coincide com o nome da zona subordinada, neste caso, “parceiros”.

    $parent_ns_recordset = New-AzureRmDnsRecordSet -Zone $parent -Name "partners" -RecordType NS -Ttl 3600
    $parent_ns_recordset.Records = $child_ns_recordset.Records
    Set-AzureRmDnsRecordSet -RecordSet $parent_ns_recordset

### Para confirmar que a resolução de nomes está a funcionar
Pode confirmar que tudo está configurado corretamente ao procurar o registo SOA da zona subordinada.

    nslookup –type=SOA partners.contoso.com

    Server: ns1-08.azure-dns.com
    Address: 208.76.47.8

    partners.contoso.com
        primary name server = ns1-08.azure-dns.com
        responsible mail addr = msnhst.microsoft.com
        serial = 1
        refresh = 900 (15 mins)
        retry = 300 (5 mins)
        expire = 604800 (7 days)
        default TTL = 300 (5 mins)

## Passos seguintes
[Gerir zonas DNS](dns-operations-dnszones.md)

[Gerir registos DNS](dns-operations-recordsets.md)

<!--HONumber=ago16_HO4-->



---
title: "Delegar o seu domínio ao DNS do Azure | Microsoft Docs"
description: "Compreenda como alterar a delegação de domínios e utilizar servidores de nomes de DNS do Azure para fornecer o alojamento de domínios."
services: dns
documentationcenter: na
author: georgewallace
manager: timlt
ms.assetid: 257da6ec-d6e2-4b6f-ad76-ee2dde4efbcc
ms.service: dns
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/12/2017
ms.author: gwallace
ms.openlocfilehash: 33b3ec24432ff1268860b9a2e9d5098600a8dedc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="delegate-a-domain-to-azure-dns"></a>Delegar um domínio ao DNS do Azure

O DNS do Azure permite-lhe alojar uma zona DNS e gerir os registos de DNS para um domínio no Azure. Para que as consultas do DNS de um domínio alcancem o DNS do Azure, o domínio tem de ser delegado ao DNS do Azure a partir do domínio principal. Tenha em atenção que o DNS do Azure não é a entidade de registo de domínios. Este artigo explica como delegar o seu domínio ao DNS do Azure.

Relativamente aos domínios comprados junto de uma entidade de registo, a entidade de registo irá oferecer a opção para configurar estes registos NS. Não tem de ter um nome de domínio para criar uma zona DNS com esse nome de domínio no DNS do Azure. No entanto, é necessário possuir o domínio para configurar a delegação ao DNS do Azure com a entidade de registo.

Por exemplo, suponha que compra o domínio “contoso.net” e cria uma zona com o nome “contoso.net” no DNS do Azure. Na qualidade de proprietário do domínio, a sua entidade de registo oferece-lhe a opção de configurar os endereços de servidor de nomes (ou seja, os registos NS) do seu domínio. A entidade de registo armazena estes registos NS no domínio principal, neste caso, “.net”. Os clientes em todo o mundo podem, então, ser direcionados para o seu domínio na zona DNS do Azure quando tentam resolver os registos DNS em “contoso.net”.

## <a name="create-a-dns-zone"></a>Criar uma zona DNS

1. Iniciar sessão no portal do Azure
1. No menu Hub, clique em **Novo > Redes >** e, em seguida, clique em **Zona DNS**, para abrir o painel Criar zona DNS.

    ![Zona DNS](./media/dns-domain-delegation/dns.png)

1. No painel **Criar zona DNS**, introduza os valores seguinte e clique em **Criar**:

   | **Definição** | **Valor** | **Detalhes** |
   |---|---|---|
   |**Nome**|contoso.net|O nome da zona DNS|
   |**Subscrição**|[A sua subscrição]|Selecione uma subscrição para criar o gateway de aplicação.|
   |**Grupo de recursos**|**Criar novo:** contosoRG|Crie um grupo de recursos. O nome do grupo de recursos tem de ser exclusivo dentro da subscrição que selecionou. Para saber mais sobre grupos de recursos, veja o artigo de descrição geral do [Resource Manager](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fdns%2ftoc.json#resource-groups).|
   |**Localização**|EUA Oeste||

> [!NOTE]
> Esta definição refere à localização do grupo de recursos e não tem qualquer impacto na zona DNS. A localização da zona DNS é sempre "global" e não está apresentada.

## <a name="retrieve-name-servers"></a>Obter servidores de nomes

Antes de pode delegar a zona DNS ao DNS do Azure, terá primeiro de conhecer os nomes dos servidores de nome da sua zona. O DNS do Azure aloca servidores de nomes a partir de um conjunto sempre que é criada uma zona.

1. Com a zona DNS criada, no painel **Favoritos** do portal do Azure, clique em **Todos os recursos**. Clique na zona DNS **contoso.net**, no painel **Todos os recursos**. Se a subscrição que selecionou já tiver vários recursos, pode introduzir **contoso.net** na caixa Filtrar por nome... para aceder facilmente ao gateway de aplicação. 

1. Obtenha os servidores de nome no painel de zona DNS. Neste exemplo, foram atribuídos à zona “contoso.net” os servidores de nomes “ns1-01.azure-dns.com”, “ns2-01.azure-dns.net”, “ns3-01.azure-dns.org” e “ns4-01.azure-dns.info'”:

 ![Dns-nameserver](./media/dns-domain-delegation/viewzonens500.png)

O DNS do Azure cria automaticamente registos NS autoritativos na sua zona, que contêm os servidores de nomes atribuídos.  Para ver os nomes dos servidores de nome através do Azure PowerShell ou da CLI do Azure, apenas tem de obter estes registos.

Os exemplos seguintes também mostram os passos para obter os servidores de nome de uma zona no DNS do Azure com o PowerShell e a CLI do Azure.

### <a name="powershell"></a>PowerShell

```powershell
# The record name "@" is used to refer to records at the top of the zone.
$zone = Get-AzureRmDnsZone -Name contoso.net -ResourceGroupName contosoRG
Get-AzureRmDnsRecordSet -Name "@" -RecordType NS -Zone $zone
```

O exemplo seguinte é a resposta.

```
Name              : @
ZoneName          : contoso.net
ResourceGroupName : contosorg
Ttl               : 172800
Etag              : 03bff8f1-9c60-4a9b-ad9d-ac97366ee4d5
RecordType        : NS
Records           : {ns1-07.azure-dns.com., ns2-07.azure-dns.net., ns3-07.azure-dns.org.,
                    ns4-07.azure-dns.info.}
Metadata          :
```

### <a name="azure-cli"></a>CLI do Azure

```azurecli
az network dns record-set show --resource-group contosoRG --zone-name contoso.net --type NS --name @
```

O exemplo seguinte é a resposta.

```json
{
  "etag": "03bff8f1-9c60-4a9b-ad9d-ac97366ee4d5",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contosoRG/providers/Microsoft.Network/dnszones/contoso.net/NS/@",
  "metadata": null,
  "name": "@",
  "nsRecords": [
    {
      "nsdname": "ns1-07.azure-dns.com."
    },
    {
      "nsdname": "ns2-07.azure-dns.net."
    },
    {
      "nsdname": "ns3-07.azure-dns.org."
    },
    {
      "nsdname": "ns4-07.azure-dns.info."
    }
  ],
  "resourceGroup": "contosoRG",
  "ttl": 172800,
  "type": "Microsoft.Network/dnszones/NS"
}
```

## <a name="delegate-the-domain"></a>Delegar o domínio

Agora que a zona DNS está criada e que já tem os servidores de nome, o domínio principal tem de ser atualizado com os servidores de nome do DNS do Azure. Cada entidade de registo tem as suas próprias ferramentas de gestão de DNS para alterar os registos do servidor de nome de um domínio. Na página de gestão do DNS da entidade de registo, edite os registos NS e substitua-os por aqueles que o DNS do Azure criou.

Quando delegar um domínio ao DNS do Azure, tem de utilizar os nomes dos servidores de nome fornecidos pelo DNS do Azure. Recomenda-se utilizar todos os quatro nomes de servidor de nomes, independentemente do seu domínio. A delegação de domínio não necessita que o nome do servidor de nome utilize o mesmo domínio de nível superior que o seu domínio.

Não deve utilizar «registos “glue”» para que apontem para os endereços IP do servidor de nomes do DNS do Azure, uma vez que estes endereços IP podem ser alterados no futuro. As delegações que utilizam nomes de servidores de nome na sua própria zona, por vezes denominados “servidores de nomes personalizados”, não são atualmente suportadas no DNS do Azure.

## <a name="verify-name-resolution-is-working"></a>Confirmar que a resolução de nomes está a funcionar

Depois de concluir a delegação, pode confirmar que a resolução de nomes está a funcionar ao utilizar uma ferramenta como a “nslookup” para consultar o registo SOA da sua zona (que também é criado automaticamente quando a zona é criada).

Não tem de especificar os servidores de nomes do DNS do Azure; se a delegação tiver sido configurada corretamente, o processo de resolução de DNS normal encontra os servidores de nomes automaticamente.

```
nslookup -type=SOA contoso.com
```

Segue-se uma resposta de exemplo do comando anterior:

```
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
```

## <a name="delegate-sub-domains-in-azure-dns"></a>Delegar subdomínios no DNS do Azure

Se pretende configurar uma zona subordinada em separado, pode delegar um subdomínio no DNS do Azure. Por exemplo, ao ter configurado e delegado “contoso.net” no DNS do Azure, suponha que pretende configurar uma zona subordinada em separado, “partners.contoso.net”.

1. Crie a zona subordinada “partners.contoso.net” no DNS do Azure.
2. Procure os registos NS autoritativos na zona subordinada para obter os servidores de nomes que alojam a zona subordinada no DNS do Azure.
3. Delegue a zona subordinada ao configurar os registos NS na zona principal a apontar para a zona subordinada.

### <a name="create-a-dns-zone"></a>Criar uma zona DNS

1. Iniciar sessão no portal do Azure
1. No menu Hub, clique em **Novo > Redes >** e, em seguida, clique em **Zona DNS**, para abrir o painel Criar zona DNS.

    ![Zona DNS](./media/dns-domain-delegation/dns.png)

1. No painel **Criar zona DNS**, introduza os valores seguinte e clique em **Criar**:

   | **Definição** | **Valor** | **Detalhes** |
   |---|---|---|
   |**Nome**|partners.contoso.net|O nome da zona DNS|
   |**Subscrição**|[A sua subscrição]|Selecione uma subscrição para criar o gateway de aplicação.|
   |**Grupo de recursos**|**Utilizar existente:** contosoRG|Crie um grupo de recursos. O nome do grupo de recursos tem de ser exclusivo dentro da subscrição que selecionou. Para saber mais sobre grupos de recursos, veja o artigo de descrição geral do [Resource Manager](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fdns%2ftoc.json#resource-groups).|
   |**Localização**|EUA Oeste||

> [!NOTE]
> Esta definição refere à localização do grupo de recursos e não tem qualquer impacto na zona DNS. A localização da zona DNS é sempre "global" e não está apresentada.

### <a name="retrieve-name-servers"></a>Obter servidores de nomes

1. Com a zona DNS criada, no painel **Favoritos** do portal do Azure, clique em **Todos os recursos**. Clique na zona DNS **partners.contoso.net**, no painel **Todos os recursos**. Se a subscrição que selecionou já tiver vários recursos, pode introduzir **partners.contoso.net** na caixa Filtrar por nome... para aceder facilmente à zona DNS.

1. Obtenha os servidores de nome no painel de zona DNS. Neste exemplo, foram atribuídos à zona “contoso.net” os servidores de nomes “ns1-01.azure-dns.com”, “ns2-01.azure-dns.net”, “ns3-01.azure-dns.org” e “ns4-01.azure-dns.info'”:

 ![Dns-nameserver](./media/dns-domain-delegation/viewzonens500.png)

O DNS do Azure cria automaticamente registos NS autoritativos na sua zona, que contêm os servidores de nomes atribuídos.  Para ver os nomes dos servidores de nome através do Azure PowerShell ou da CLI do Azure, apenas tem de obter estes registos.

### <a name="create-name-server-record-in-parent-zone"></a>Criar o registo de servidor de nome na zona principal

1. Navegue para a zona DNS **contoso.net** no portal do Azure.
1. Clique em **+ Conjunto de registos**
1. No painel **Adicionar conjunto de registos**, introduza os valores seguintes e clique em **OK**:

   | **Definição** | **Valor** | **Detalhes** |
   |---|---|---|
   |**Nome**|partners|O nome da zona DNS subordinada|
   |**Tipo**|NS|Utilize NS para registos do servidor de nome.|
   |**TTL**|1|“Time to live”|
   |**Unidade de TTL**|Horas|define a unidade de TTL como horas|
   |**SERVIDOR DE NOME**|{servidores de nome da zona partners.contoso.net}|Introduza os quatro servidores de nome da zona partners.contoso.net. |

   ![Dns-nameserver](./media/dns-domain-delegation/partnerzone.png)


### <a name="delegating-sub-domains-in-azure-dns-with-other-tools"></a>Delegar subdomínios no DNS do Azure com outras ferramentas

Os exemplos seguintes mostram os passos para delegar subdomínios no DNS do Azure com o PowerShell e a CLI:

#### <a name="powershell"></a>PowerShell

O exemplo do PowerShell que se segue demonstra como isto funciona. Os mesmos passos podem ser executados através do portal do Azure ou da CLI do Azure multiplataformas.

```powershell
# Create the parent and child zones. These can be in same resource group or different resource groups as Azure DNS is a global service.
$parent = New-AzureRmDnsZone -Name contoso.net -ResourceGroupName contosoRG
$child = New-AzureRmDnsZone -Name partners.contoso.net -ResourceGroupName contosoRG

# Retrieve the authoritative NS records from the child zone as shown in the next example. This contains the name servers assigned to the child zone.
$child_ns_recordset = Get-AzureRmDnsRecordSet -Zone $child -Name "@" -RecordType NS

# Create the corresponding NS record set in the parent zone to complete the delegation. The record set name in the parent zone matches the child zone name, in this case "partners".
$parent_ns_recordset = New-AzureRmDnsRecordSet -Zone $parent -Name "partners" -RecordType NS -Ttl 3600
$parent_ns_recordset.Records = $child_ns_recordset.Records
Set-AzureRmDnsRecordSet -RecordSet $parent_ns_recordset
```

Utilize `nslookup` para procurar o registo SOA da zona subordinada e confirmar que tudo está configurado corretamente.

```
nslookup -type=SOA partners.contoso.com
```

```
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
```

#### <a name="azure-cli"></a>CLI do Azure

```azurecli
#!/bin/bash

# Create the parent and child zones. These can be in same resource group or different resource groups as Azure DNS is a global service.
az network dns zone create -g contosoRG -n contoso.net
az network dns zone create -g contosoRG -n partners.contoso.net
```

Obtenha os servidores de nome da zona `partners.contoso.net` a partir do resultado.

```
{
  "etag": "00000003-0000-0000-418f-250de2b2d201",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contosorg/providers/Microsoft.Network/dnszones/partners.contoso.net",
  "location": "global",
  "maxNumberOfRecordSets": 5000,
  "name": "partners.contoso.net",
  "nameServers": [
    "ns1-09.azure-dns.com.",
    "ns2-09.azure-dns.net.",
    "ns3-09.azure-dns.org.",
    "ns4-09.azure-dns.info."
  ],
  "numberOfRecordSets": 2,
  "resourceGroup": "contosorg",
  "tags": {},
  "type": "Microsoft.Network/dnszones"
}
```

Crie o conjunto de registos e os registos NS para cada servidor de nome.

```azurecli
#!/bin/bash

# Create the record set
az network dns record-set ns create --resource-group contosorg --zone-name contoso.net --name partners

# Create a ns record for each name server.
az network dns record-set ns add-record --resource-group contosorg --zone-name contoso.net --record-set-name partners --nsdname ns1-09.azure-dns.com.
az network dns record-set ns add-record --resource-group contosorg --zone-name contoso.net --record-set-name partners --nsdname ns2-09.azure-dns.net.
az network dns record-set ns add-record --resource-group contosorg --zone-name contoso.net --record-set-name partners --nsdname ns3-09.azure-dns.org.
az network dns record-set ns add-record --resource-group contosorg --zone-name contoso.net --record-set-name partners --nsdname ns4-09.azure-dns.info.
```

## <a name="delete-all-resources"></a>Eliminar todos os recursos

Para eliminar todos os recursos criados neste artigo, conclua os seguintes passos:

1. No painel **Favoritos** do portal do Azure, clique em **Todos os recursos**. Clique no grupo de recursos **contosorg**, no painel Todos os recursos. Se a subscrição que selecionou já tiver vários recursos, pode introduzir **contosorg** na caixa **Filtrar por nome...** para aceder facilmente ao grupo de recursos.
1. No painel **contosorg**, clique no botão **Eliminar**.
1. O portal requer que escreva o nome do grupo de recursos para confirmar que pretende eliminá-lo. Escreva *contosorg* no nome do grupo de recursos e clique em **Eliminar**. A eliminação de um grupo de recursos elimina todos os recursos dentro do grupo de recursos. Por conseguinte, confirme os conteúdos de um grupo de recursos antes de o eliminar. O portal elimina todos os recursos contidos dentro do grupo de recursos e, em seguida, elimina o próprio grupo de recursos. Este processo demora vários minutos.

## <a name="next-steps"></a>Passos seguintes

[Gerir zonas DNS](dns-operations-dnszones.md)

[Gerir registos DNS](dns-operations-recordsets.md)

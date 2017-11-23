---
title: "Introdução às zonas privadas do DNS do Azure com o PowerShell | Microsoft Docs"
description: "Saiba como criar uma zona DNS privada e o registo no DNS do Azure. Este é um guia passo a passo para criar e gerir a sua primeira zona DNS privada e o registo com o PowerShell."
services: dns
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: dns
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/20/2017
ms.author: kumud
ms.openlocfilehash: d71e2391b6415b2403447479dea4fd0a3b818ed0
ms.sourcegitcommit: 4ea06f52af0a8799561125497f2c2d28db7818e7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2017
---
# <a name="get-started-with-azure-dns-private-zones-using-powershell"></a>Introdução às zonas privadas do DNS do Azure com o PowerShell

Este artigo explica-lhe os passos para criar a sua primeira zona DNS privada e o registo com o Azure PowerShell.

Uma zona DNS é utilizada para alojar os registos DNS para um determinado domínio. Para começar a alojar o seu domínio no DNS do Azure, tem de criar uma zona DNS para esse nome de domínio. Cada registo DNS para o seu domínio é então criado no interior desta zona DNS. Para publicar uma zona DNS privada na sua rede virtual, especifique a lista de redes virtuais autorizadas a resolver registos na zona.  São denominadas ''redes de resolução''.  Também pode especificar um conjunto de redes virtuais para o qual o DNS do Azure vai manter registos de nomes de anfitrião sempre que uma VM for criada, alterar o IP ou for destruída.  São denominadas ''redes de registo''.

Uma vez que esta funcionalidade é atualmente uma pré-visualização gerida, será fornecido um módulo do PowerShell de pré-visualização.

[!INCLUDE [private-dns-preview-notice](../../includes/private-dns-preview-notice.md)]

## <a name="create-the-resource-group"></a>Criar o grupo de recursos

Antes de criar a zona DNS, é criado um grupo de recursos para conter a zona DNS. O seguinte mostra o comando.

```powershell
New-AzureRMResourceGroup -name MyResourceGroup -location "westus"
```

## <a name="create-a-dns-zone"></a>Criar uma zona DNS

Uma zona DNS é criada ao utilizar o cmdlet `New-AzureRmDnsZone`. O exemplo seguinte cria uma zona DNS denominada *contoso.local* no grupo de recursos *MyResourceGroup* e disponibiliza a zona DNS na rede virtual *MyAzureVnet* . Utilize o exemplo para criar uma zona DNS, substituindo os valores pelos seus.

```powershell
$vnet = Get-AzureRmVirtualNetwork -Name MyAzureVnet -ResourceGroupName VnetResourceGroup
New-AzureRmDnsZone -Name contoso.local -ResourceGroupName MyResourceGroup -ZoneType Private -ResolutionVirtualNetworkId @($vnet.Id)
```

Se precisar que o Azure crie automaticamente registos de nomes de anfitrião na zona, utilize o parâmetro *RegistrationVirtualNetworkId* em vez de *ResolutionVirtualNetworkId*.  As redes virtuais de registo são ativadas automaticamente para resolução.

```powershell
$vnet = Get-AzureRmVirtualNetwork -Name MyAzureVnet -ResourceGroupName VnetResourceGroup
New-AzureRmDnsZone -Name contoso.local -ResourceGroupName MyResourceGroup -ZoneType Private -RegistrationVirtualNetworkId @($vnet.Id)
```


## <a name="create-a-dns-record"></a>Criar um registo DNS

Pode criar conjuntos de registos com o cmdlet `New-AzureRmDnsRecordSet`. O exemplo seguinte cria um registo com o nome relativo "db" na Zona DNS "contoso.com", no grupo de recursos "MyResourceGroup". O nome totalmente qualificado do conjunto de registos é "db.contoso.local". O tipo de registo é "A", com o endereço IP "10.0.0.4" e o TTL é de 3600 segundos.

```powershell
New-AzureRmDnsRecordSet -Name db -RecordType A -ZoneName contoso.local -ResourceGroupName MyResourceGroup -Ttl 3600 -DnsRecords (New-AzureRmDnsRecordConfig -IPv4Address "10.0.0.4")
```

Para outros tipos de registos, para conjuntos de registos com mais de um registo e para modificar registos existentes, consulte [Manage DNS records and record sets using the PowerShell (Gerir registos DNS e conjuntos de registos com o Azure PowerShell)](dns-operations-recordsets.md). 


## <a name="view-records"></a>Ver registos

Para listar os registos DNS na sua zona, utilize:

```powershell
Get-AzureRmDnsRecordSet -ZoneName contoso.local -ResourceGroupName MyResourceGroup
```

## <a name="delete-all-resources"></a>Eliminar todos os recursos

Para eliminar todos os recursos criados neste artigo, realize o seguinte passo:

```powershell
Remove-AzureRMResourceGroup -Name MyResourceGroup
```

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre zonas DNS privadas, veja [Utilizar o DNS do Azure para domínios privados](private-dns-overview.md).

Para saber mais sobre a gestão de registos DNS no DNS do Azure, consulte [Manage DNS records and record sets in Azure DNS using PowerShell (Gerir registos DNS e conjuntos de registos no DNS do Azure com o PowerShell)](dns-operations-recordsets.md).


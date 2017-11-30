---
title: "Inversa de DNS para serviços do Azure | Microsoft Docs"
description: "Saiba como configurar pesquisas inversas de DNS para serviços alojados no Azure"
services: dns
documentationcenter: na
author: KumudD
manager: timlt
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/29/2017
ms.author: kumud
ms.openlocfilehash: 0c5d12e9d6b5ddbee2a930e4e537b8180b7a9c7b
ms.sourcegitcommit: cfd1ea99922329b3d5fab26b71ca2882df33f6c2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/30/2017
---
# <a name="configure-reverse-dns-for-services-hosted-in-azure"></a>Configurar o DNS inversa para serviços alojados no Azure

Este artigo explica como configurar pesquisas inversas de DNS para serviços alojados no Azure.

Os serviços no Azure utilizar endereços IP atribuído pelo Azure e pertencente à Microsoft. Estes registos DNS inversas (registos PTR) tem de ser criados as correspondentes pertencentes à Microsoft zonas DNS inversas pesquisa. Este artigo explica como fazê-lo.

Este cenário não deve ser confundido com a capacidade de [alojar as zonas de pesquisa inversas do DNS para os intervalos IP atribuídos no DNS do Azure](dns-reverse-dns-hosting.md). Neste caso, os intervalos IP representados por zona de pesquisa inversa devem ser atribuídos à sua organização, normalmente pelo seu ISP.

Antes de ler este artigo, deve estar familiarizado com esta [descrição geral do DNS inversa e de suporte no Azure](dns-reverse-dns-overview.md).

No DNS do Azure, os recursos de computação (por exemplo, as máquinas virtuais, conjuntos de dimensionamento de máquina virtual ou clusters de Service Fabric) são expostos através de um recurso de PublicIpAddress. Pesquisas inversas de DNS estão configuradas ao utilizar a propriedade 'ReverseFqdn' o PublicIpAddress.


DNS inversa não é atualmente suportado para o App Service do Azure.

## <a name="validation-of-reverse-dns-records"></a>Validação de registos DNS inversas

Uma linha de terceiros não deve ser capaz de criar registos DNS inversas para o respetivo mapeamento de serviço do Azure para os domínios DNS. Para evitar esta situação, Azure apenas permite a criação de um registo DNS inverso onde o nome de domínio especificado no registo DNS inverso é o mesmo ou é resolvido para o nome DNS ou endereço IP de um PublicIpAddress ou serviço em nuvem na mesma subscrição do Azure.

Esta validação só é executada quando o registo DNS inverso é definido ou modificado. Não é efetuada a validação de reativação periódica.

Por exemplo: suponha o recurso de PublicIpAddress tem o endereço IP 23.96.52.53 e contosoapp1.northus.cloudapp.azure.com de nome DNS. O ReverseFqdn para o PublicIpAddress pode ser especificado como:
* O nome DNS para o PublicIpAddress contosoapp1.northus.cloudapp.azure.com
* O nome DNS para um PublicIpAddress diferentes na mesma subscrição, tais como contosoapp2.westus.cloudapp.azure.com
* Um intuitivos nomes DNS, tais como app1.contoso.com, desde que este nome é *primeiro* configurado como um CNAME para contosoapp1.northus.cloudapp.azure.com ou para um PublicIpAddress diferentes na mesma subscrição.
* Um intuitivos nomes DNS, tais como app1.contoso.com, desde que este nome é *primeiro* configurado como um registo para o endereço IP 23.96.52.53 ou para o endereço IP de um PublicIpAddress diferentes na mesma subscrição.

As mesmas restrições aplicam-se para inversa de DNS para serviços em nuvem.


## <a name="reverse-dns-for-publicipaddress-resources"></a>Inversa de DNS para recursos PublicIpAddress

Esta secção fornece instruções detalhadas sobre como configurar o DNS inversa PublicIpAddress recursos no modelo de implementação Resource Manager, utilizando o Azure PowerShell, CLI do Azure 1.0 ou 2.0 de CLI do Azure. Configurar o DNS inversa PublicIpAddress recursos não é atualmente suportada através do portal do Azure.

Azure atualmente suporta inversa DNS apenas para recursos IPv4 PublicIpAddress. Não é suportada para IPv6.

### <a name="add-reverse-dns-to-an-existing-publicipaddresses"></a>Adicionar inversa de DNS para um PublicIpAddresses existente

#### <a name="powershell"></a>PowerShell

Para adicionar inversa de DNS para um PublicIpAddress existente:

```powershell
$pip = Get-AzureRmPublicIpAddress -Name "PublicIp" -ResourceGroupName "MyResourceGroup"
$pip.DnsSettings.ReverseFqdn = "contosoapp1.westus.cloudapp.azure.com."
Set-AzureRmPublicIpAddress -PublicIpAddress $pip
```

Para adicionar inversa de DNS para um PublicIpAddress existente que já não tem um nome DNS, também tem de especificar um nome DNS:

```powershell
$pip = Get-AzureRmPublicIpAddress -Name "PublicIp" -ResourceGroupName "MyResourceGroup"
$pip.DnsSettings = New-Object -TypeName "Microsoft.Azure.Commands.Network.Models.PSPublicIpAddressDnsSettings"
$pip.DnsSettings.DomainNameLabel = "contosoapp1"
$pip.DnsSettings.ReverseFqdn = "contosoapp1.westus.cloudapp.azure.com."
Set-AzureRmPublicIpAddress -PublicIpAddress $pip
```

#### <a name="azure-cli-10"></a>CLI do Azure 1.0

Para adicionar inversa de DNS para um PublicIpAddress existente:

```azurecli
azure network public-ip set -n PublicIp -g MyResourceGroup -f contosoapp1.westus.cloudapp.azure.com.
```

Para adicionar inversa de DNS para um PublicIpAddress existente que já não tem um nome DNS, também tem de especificar um nome DNS:

```azurecli
azure network public-ip set -n PublicIp -g MyResourceGroup -d contosoapp1 -f contosoapp1.westus.cloudapp.azure.com.
```

#### <a name="azure-cli-20"></a>CLI 2.0 do Azure

Para adicionar inversa de DNS para um PublicIpAddress existente:

```azurecli
az network public-ip update --resource-group MyResourceGroup --name PublicIp --reverse-fqdn contosoapp1.westus.cloudapp.azure.com.
```

Para adicionar inversa de DNS para um PublicIpAddress existente que já não tem um nome DNS, também tem de especificar um nome DNS:

```azurecli
az network public-ip update --resource-group MyResourceGroup --name PublicIp --reverse-fqdn contosoapp1.westus.cloudapp.azure.com --dns-name contosoapp1
```

### <a name="create-a-public-ip-address-with-reverse-dns"></a>Criar um endereço IP público com o DNS inversa

Para criar um novo PublicIpAddress com a propriedade inversa de DNS especificada já:

#### <a name="powershell"></a>PowerShell

```powershell
New-AzureRmPublicIpAddress -Name "PublicIp" -ResourceGroupName "MyResourceGroup" -Location "WestUS" -AllocationMethod Dynamic -DomainNameLabel "contosoapp2" -ReverseFqdn "contosoapp2.westus.cloudapp.azure.com."
```

#### <a name="azure-cli-10"></a>CLI do Azure 1.0

```azurecli
azure network public-ip create -n PublicIp -g MyResourceGroup -l westus -d contosoapp3 -f contosoapp3.westus.cloudapp.azure.com.
```

#### <a name="azure-cli-20"></a>CLI 2.0 do Azure

```azurecli
az network public-ip create --name PublicIp --resource-group MyResourceGroup --location westcentralus --dns-name contosoapp1 --reverse-fqdn contosoapp1.westcentralus.cloudapp.azure.com
```

### <a name="view-reverse-dns-for-an-existing-publicipaddress"></a>Vista DNS inverso para um PublicIpAddress existente

Para ver o valor configurado para um PublicIpAddress existente:

#### <a name="powershell"></a>PowerShell

```powershell
Get-AzureRmPublicIpAddress -Name "PublicIp" -ResourceGroupName "MyResourceGroup"
```

#### <a name="azure-cli-10"></a>CLI do Azure 1.0

```azurecli
azure network public-ip show -n PublicIp -g MyResourceGroup
```

#### <a name="azure-cli-20"></a>CLI 2.0 do Azure

```azurecli
az network public-ip show --name PublicIp --resource-group MyResourceGroup
```

### <a name="remove-reverse-dns-from-existing-public-ip-addresses"></a>Remover DNS inversa de endereços IP públicos existentes

Para remover uma propriedade DNS inversa de um PublicIpAddress existente:

#### <a name="powershell"></a>PowerShell

```powershell
$pip = Get-AzureRmPublicIpAddress -Name "PublicIp" -ResourceGroupName "MyResourceGroup"
$pip.DnsSettings.ReverseFqdn = ""
Set-AzureRmPublicIpAddress -PublicIpAddress $pip
```

#### <a name="azure-cli-10"></a>CLI do Azure 1.0

```azurecli
azure network public-ip set -n PublicIp -g MyResourceGroup –f ""
```

#### <a name="azure-cli-20"></a>CLI 2.0 do Azure

```azurecli
az network public-ip update --resource-group MyResourceGroup --name PublicIp --reverse-fqdn ""
```


## <a name="configure-reverse-dns-for-cloud-services"></a>Configurar inversa de DNS para serviços em nuvem

Esta secção fornece instruções detalhadas sobre como configurar o inversa de DNS para serviços em nuvem no modelo de implementação clássica, com o Azure PowerShell. Configurar o inversa de DNS para serviços em nuvem não é suportada através do portal do Azure, CLI do Azure 1.0 ou 2.0 de CLI do Azure.

### <a name="add-reverse-dns-to-existing-cloud-services"></a>Adicionar inversa de DNS para serviços de Cloud existente

Para adicionar um registo DNS inverso para um serviço em nuvem existente:

```powershell
Set-AzureService –ServiceName "contosoapp1" –Description "App1 with Reverse DNS" –ReverseDnsFqdn "contosoapp1.cloudapp.net."
```

### <a name="create-a-cloud-service-with-reverse-dns"></a>Criar um serviço em nuvem com o DNS inversa

Para criar um novo serviço em nuvem com a propriedade inversa de DNS especificada já:

```powershell
New-AzureService –ServiceName "contosoapp1" –Location "West US" –Description "App1 with Reverse DNS" –ReverseDnsFqdn "contosoapp1.cloudapp.net."
```

### <a name="view-reverse-dns-for-existing-cloud-services"></a>Vista DNS inversa para serviços de Cloud existente

Para ver a propriedade inversa de DNS para um serviço em nuvem existente:

```powershell
Get-AzureService "contosoapp1"
```

### <a name="remove-reverse-dns-from-existing-cloud-services"></a>Remover inversa de DNS dos serviços de nuvem existente

Para remover uma propriedade DNS inversa de um serviço em nuvem existente:

```powershell
Set-AzureService –ServiceName "contosoapp1" –Description "App1 with Reverse DNS" –ReverseDnsFqdn ""
```

## <a name="faq"></a>FAQ

### <a name="how-much-do-reverse-dns-records-cost"></a>Quanto inverter o custo de registos DNS?

Se estiverem livres!  Não há sem custos adicionais para inversas de DNS de registos ou consultas.

### <a name="will-my-reverse-dns-records-resolve-from-the-internet"></a>Os meus registos DNS inversas irão resolver através da internet?

Sim. Depois de definir a propriedade inversa de DNS para o serviço do Azure, Azure gere todas as delegações DNS e zonas DNS necessárias para garantir que o registo DNS inverso resolve para todos os utilizadores de Internet.

### <a name="are-default-reverse-dns-records-created-for-my-azure-services"></a>Registos DNS inversas predefinida são criados para os meus serviços do Azure?

Não. Inversa de DNS é uma funcionalidade de recusa. Não existem registos DNS inversas predefinida são criados se optar por não configurá-las.

### <a name="what-is-the-format-for-the-fully-qualified-domain-name-fqdn"></a>O que é o formato para o nome de domínio completamente qualificado (FQDN)?

Estão especificados na ordem de pesquisa direta e FQDNs tem de ser terminadas por um ponto (por exemplo, "app1.contoso.com.").

### <a name="what-happens-if-the-validation-check-for-the-reverse-dns-ive-specified-fails"></a>O que acontece se a verificação de validação do DNS inversa posso especificadas falha?

Em que a verificação de validação do DNS inversa falha, a operação para configurar o registo DNS inverso falhar. Corrija o valor DNS inverso conforme necessário e repita.

### <a name="can-i-configure-reverse-dns-for-azure-app-service"></a>Pode configurar inversa de DNS para o App Service do Azure?

Não. DNS inversa não é suportada para o serviço de aplicações do Azure.

### <a name="can-i-configure-multiple-reverse-dns-records-for-my-azure-service"></a>Pode configurar vários registos DNS inversas para o meu serviço do Azure?

Não. Azure suporta um único registo DNS inverso para cada serviço de nuvem do Azure ou um PublicIpAddress.

### <a name="can-i-configure-reverse-dns-for-ipv6-publicipaddress-resources"></a>Pode configurar inversa de DNS para recursos de IPv6 PublicIpAddress?

Não. Azure atualmente suporta inversa DNS apenas para IPv4 PublicIpAddress recursos e serviços em nuvem.

### <a name="can-i-send-emails-to-external-domains-from-my-azure-compute-services"></a>Posso enviar e-mails para domínios externos dos meus serviços de computação do Azure?

Não. [Serviços de computação do Azure não suportam o envio de mensagens de correio eletrónico para domínios externos](https://blogs.msdn.microsoft.com/mast/2016/04/04/sending-e-mail-from-azure-compute-resource-to-external-domains/)

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre inversa DNS, consulte [pesquisa inversa de DNS em Wikipedia](http://en.wikipedia.org/wiki/Reverse_DNS_lookup).
<br>
Saiba como [alojam a zona de pesquisa inversa para o intervalo de IP ISP atribuído no DNS do Azure](dns-reverse-dns-for-azure-services.md).


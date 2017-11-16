---
title: "Criar um padrão de Balanceador de público de carga com redundância de zona IP público endereço front-end com o PowerShell | Microsoft Docs"
description: "Saiba como criar padrão de Balanceador de carga público com redundância de zona IP público endereço front-end com o PowerShell"
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/20/2017
ms.author: kumud
ms.openlocfilehash: 9987fe1273dc05a2ad10c65325ad1d487d38247e
ms.sourcegitcommit: afc78e4fdef08e4ef75e3456fdfe3709d3c3680b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/16/2017
---
#  <a name="create-a-public-load-balancer-standard-with-zone-redundant-public-ip-address-frontend-using-powershell"></a>Criar um padrão de Balanceador de público de carga com redundância de zona IP público endereço front-end com o PowerShell

Os passos neste artigo através da criação de um público [padrão de Balanceador de carga](https://aka.ms/azureloadbalancerstandard) com um front-end com redundância de zona utilizando um endereço IP público padrão.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="register-for-availability-zones-load-balancer-standard-and-public-ip-standard-preview"></a>Registar-se na pré-visualização padrão zonas de disponibilidade, o padrão de Balanceador de carga e o IP público

Este artigo requer que tem a versão 4.4.0 ou superior do módulo AzureRM instalado. Para localizar a versão, execute `Get-Module -ListAvailable AzureRM`. Se precisar de instalar ou atualizar, instale a versão mais recente do módulo de AzureRM o [galeria do PowerShell](https://www.powershellgallery.com/packages/AzureRM).

>[!NOTE]
[SKU Standard do Balanceador de carga](https://aka.ms/azureloadbalancerstandard) está atualmente em pré-visualização. Durante a pré-visualização, a funcionalidade poderá não ter o mesmo nível de disponibilidade e fiabilidade oferecido na versão de disponibilidade geral. Para obter mais informações, consulte [Termos de Utilização Suplementares do Microsoft Azure para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Utilizar geralmente disponível [SKU básico de Balanceador de carga](load-balancer-overview.md) para os serviços de produção. 

> [!NOTE]
> Zonas de disponibilidade estão em pré-visualização e estão prontas para o seu desenvolvimento e teste cenários. O suporte está disponível para famílias de tamanho VM, selecionados de recursos do Azure e regiões. Para obter mais informações sobre como começar a utilizar e os recursos do Azure, regiões e famílias de tamanho VM pode tentar zonas de disponibilidade com, consulte [zonas de descrição geral da disponibilidade](https://docs.microsoft.com/azure/availability-zones/az-overview). Para obter suporte, pode contactar-nos no [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-availability-zones) ou [abrir um pedido de suporte do Azure](../azure-supportability/how-to-create-azure-support-request.md?toc=%2fazure%2fvirtual-network%2ftoc.json).  

Antes de selecionar uma zona ou a opção com redundância de zona para o endereço IP público de front-end de Balanceador de carga, primeiro tem de concluir os passos em [registar-se para a pré-visualização de zonas de disponibilidade](https://docs.microsoft.com/azure/availability-zones/az-overview).

## <a name="log-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão na sua subscrição do Azure com o comando `Login-AzureRmAccount` e siga as instruções no ecrã.

```powershell
Login-AzureRmAccount
```

## <a name="create-resource-group"></a>Criar grupo de recursos

Crie um grupo de recursos com o seguinte comando:

```powershell
New-AzureRmResourceGroup -Name myResourceGroup -Location westeurope
```

## <a name="create-a-public-ip-standard"></a>Criar um padrão de IP público 
Crie um Public IP Standard utilizando o seguinte comando:

```powershell
$publicIp = New-AzureRmPublicIpAddress -ResourceGroupName myResourceGroup -Name 'myPublicIP' `
  -Location westeurope -AllocationMethod Static -Sku Standard 
```

## <a name="create-a-front-end-ip-configuration-for-the-website"></a>Criar uma configuração de IP Front-end para o Web site

Crie uma configuração de IP de front-end utilizando o seguinte comando:

```powershell
$feip = New-AzureRmLoadBalancerFrontendIpConfig -Name 'myFrontEndPool' -PublicIpAddress $publicIp
```

## <a name="create-the-back-end-address-pool"></a>Criar o conjunto de endereços de back-end

Crie um conjunto de endereços de back-end utilizando o seguinte comando:

```powershell
$bepool = New-AzureRmLoadBalancerBackendAddressPoolConfig -Name 'myBackEndPool'
```

## <a name="create-a-load-balancer-probe-on-port-80"></a>Criar uma sonda do Balanceador de carga na porta 80

Crie uma sonda do Estado de funcionamento na porta 80 para o Balanceador de carga utilizando o seguinte comando:

```powershell
$probe = New-AzureRmLoadBalancerProbeConfig -Name 'myHealthProbe' -Protocol Http -Port 80 `
  -RequestPath / -IntervalInSeconds 360 -ProbeCount 5
```

## <a name="create-a-load-balancer-rule"></a>Criar uma regra de Balanceador de carga
 Crie uma regra de Balanceador de carga utilizando o seguinte comando:

```powershell
   $rule = New-AzureRmLoadBalancerRuleConfig -Name HTTP -FrontendIpConfiguration $feip -BackendAddressPool  $bepool -Probe $probe -Protocol Tcp -FrontendPort 80 -BackendPort 80
```

## <a name="create-a-load-balancer"></a>Criar um balanceador de carga
Crie uma carga balanceador Standard utilizando o seguinte comando:

```powershell
$lb = New-AzureRmLoadBalancer -ResourceGroupName myResourceGroup -Name 'MyLoadBalancer' -Location westeurope `
  -FrontendIpConfiguration $feip -BackendAddressPool $bepool `
  -Probe $probe -LoadBalancingRule $rule -Sku Standard
```

## <a name="next-steps"></a>Passos seguintes
- Saiba como [criar um IP público numa zona de disponibilidade](../virtual-network/create-public-ip-availability-zone-portal.md)




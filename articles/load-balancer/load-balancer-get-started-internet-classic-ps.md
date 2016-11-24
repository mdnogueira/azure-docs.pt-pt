---
title: "Introdução à criação de um balanceador de carga com acesso à Internet no modo clássico com o PowerShell | Microsoft Docs"
description: "Saiba como criar um balanceador de carga com acesso à Internet no modo clássico com o PowerShell"
services: load-balancer
documentationcenter: na
author: sdwheeler
manager: carmonm
tags: azure-service-management
ms.assetid: 73e8bfa4-8086-4ef0-9e35-9e00b24be319
ms.service: load-balancer
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/05/2016
ms.author: sewhee
translationtype: Human Translation
ms.sourcegitcommit: cf1eafc7bca5bddeb32f1e1e05e660d6877ed805
ms.openlocfilehash: 16ebe82b866565f676fb33bfa915465c08a5ea89

---

# <a name="get-started-creating-an-internet-facing-load-balancer-classic-in-powershell"></a>Introdução à criação de um balanceador de carga com acesso à Internet (modo clássico) no PowerShell

> [!div class="op_single_selector"]
> * [Portal Clássico do Azure](../load-balancer/load-balancer-get-started-internet-classic-portal.md)
> * [PowerShell](../load-balancer/load-balancer-get-started-internet-classic-ps.md)
> * [CLI do Azure](../load-balancer/load-balancer-get-started-internet-classic-cli.md)
> * [Serviços em Nuvem do Azure](../load-balancer/load-balancer-get-started-internet-classic-cloud.md)

[!INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

> [!IMPORTANT]
> Para trabalhar com recursos do Azure, é importante compreender que o Azure tem atualmente dois modelos de implementação: o Azure Resource Manager e a implementação clássica. Confirme que compreende os [modelos e ferramentas de implementação](../azure-classic-rm.md) antes de trabalhar com qualquer recurso do Azure. Pode ver a documentação de diversas ferramentas clicando nos separadores na parte superior deste artigo. Este artigo abrange o modelo de implementação clássica. Também pode [saber como criar um balanceador de carga com acesso à Internet com o Azure Resource Manager](load-balancer-get-started-internet-arm-ps.md).

[!INCLUDE [load-balancer-get-started-internet-scenario-include.md](../../includes/load-balancer-get-started-internet-scenario-include.md)]

## <a name="set-up-load-balancer-using-powershell"></a>Configurar o balanceador de carga com o PowerShell

Para configurar um balanceador de carga com o PowerShell, siga os passos abaixo:

1. Se nunca tiver utilizado o Azure PowerShell, veja [How to Install and Configure Azure PowerShell (Como instalar e configurar o Azure PowerShell)](../powershell-install-configure.md) e siga as instruções até ao fim para iniciar sessão no Azure e selecionar a sua subscrição.
2. Depois de criar uma máquina virtual, pode utilizar os cmdlets do PowerShell para adicionar um balanceador de carga a uma máquina virtual no mesmo serviço em nuvem.

No exemplo seguinte, irá adicionar um conjunto de balanceadores de carga denominado "webfarm" ao serviço em nuvem "mytestcloud" (ou myctestcloud.cloudapp.net) ao adicionar os pontos finais do balanceador de carga às máquinas virtuais com o nome "web1" e "web2". O balanceador de carga recebe o tráfego de rede na porta 80 e efetua o balanceamento de carga entre as máquinas virtuais definidas pelo ponto final local (neste caso, a porta 80) através de TCP.

### <a name="step-1"></a>Passo 1

Criar um ponto final com balanceamento de carga para a primeira VM "web1"

```powershell
Get-AzureVM -ServiceName "mytestcloud" -Name "web1" | Add-AzureEndpoint -Name "HttpIn" -Protocol "tcp" -PublicPort 80 -LocalPort 80 -LBSetName "WebFarm" -ProbePort 80 -ProbeProtocol "http" -ProbePath '/' | Update-AzureVM
```

### <a name="step-2"></a>Passo 2

Criar outro ponto final para a segunda VM "web2" com o mesmo nome de conjunto de balanceadores de carga

```powershell
Get-AzureVM -ServiceName "mytestcloud" -Name "web2" | Add-AzureEndpoint -Name "HttpIn" -Protocol "tcp" -PublicPort 80 -LocalPort 80 -LBSetName "WebFarm" -ProbePort 80 -ProbeProtocol "http" -ProbePath '/' | Update-AzureVM
```

## <a name="remove-a-virtual-machine-from-a-load-balancer"></a>Remover uma máquina virtual de um balanceador de carga

Pode utilizar Remove-AzureEndpoint para remover um ponto final de máquina virtual do balanceador de carga

```powershell
Get-azureVM -ServiceName mytestcloud  -Name web1 |Remove-AzureEndpoint -Name httpin | Update-AzureVM
```

## <a name="next-steps"></a>Passos seguintes

Também pode [começar a criar um balanceador de carga interno](load-balancer-get-started-ilb-classic-ps.md) e configurar o tipo de [modo de distribuição](load-balancer-distribution-mode.md) para um comportamento de tráfego de rede do balanceador de carga específico.

Se a sua aplicação precisar de manter as ligações ativas para os servidores protegidos por um balanceador de carga, pode compreender melhor as [definições de tempo limite TCP inativo para um balanceador de carga](load-balancer-tcp-idle-timeout.md). Irá ajudá-lo a saber mais sobre o comportamento de ligação inativa quando está a utilizar o Balanceador de Carga do Azure.



<!--HONumber=Nov16_HO3-->



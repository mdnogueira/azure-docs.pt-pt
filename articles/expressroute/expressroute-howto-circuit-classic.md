---
title: "Modificar um circuito ExpressRoute: PowerShell: clássico do Azure | Microsoft Docs"
description: "Este artigo explica os passos para verificar o estado, update ou delete e retirar o aprovisionamento do seu circuito de modelo de implementação clássica do ExpressRoute."
documentationcenter: na
services: expressroute
author: ganesr
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 0134d242-6459-4dec-a2f1-4657c3bc8b23
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/08/2017
ms.author: ganesr;cherylmc
ms.openlocfilehash: 457bb74fa15d31fecbf668038ac880cafb8a897d
ms.sourcegitcommit: dcf5f175454a5a6a26965482965ae1f2bf6dca0a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/10/2017
---
# <a name="modify-an-expressroute-circuit-using-powershell-classic"></a>Modificar um circuito ExpressRoute com o PowerShell (clássica)

> [!div class="op_single_selector"]
> * [Portal do Azure](expressroute-howto-circuit-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-circuit-arm.md)
> * [CLI do Azure](howto-circuit-cli.md)
> * [Vídeo - portal do Azure](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit)
> * [PowerShell (clássico)](expressroute-howto-circuit-classic.md)
>

Este artigo também mostra como verificar o estado, update ou delete e desaprovisionar um circuito ExpressRoute.

[!INCLUDE [expressroute-classic-end-include](../../includes/expressroute-classic-end-include.md)]

**Acerca dos modelos de implementação do Azure**

[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## <a name="before-you-begin"></a>Antes de começar

Instalar as versões mais recentes dos módulos do PowerShell de gestão de serviço do Azure (SM), siga as instruções em [introdução aos cmdlets do Azure PowerShell](/powershell/azure/overview) para obter orientações passo a passo sobre como configurar o computador para utilizar o Módulos do PowerShell do Azure.

## <a name="get-the-status-of-a-circuit"></a>Obter o estado de um circuito

Pode obter estas informações em qualquer altura utilizando o `Get-AzureCircuit` cmdlet. Efetuar a chamada sem quaisquer parâmetros apresenta uma lista de todos os circuitos.

```powershell
Get-AzureDedicatedCircuit

Bandwidth                        : 200
CircuitName                      : MyTestCircuit
Location                         : Silicon Valley
ServiceKey                       : *********************************
ServiceProviderName              : equinix
ServiceProviderProvisioningState : Provisioned
Sku                              : Standard
Status                           : Enabled

Bandwidth                        : 1000
CircuitName                      : MyAsiaCircuit
Location                         : Singapore
ServiceKey                       : #################################
ServiceProviderName              : equinix
ServiceProviderProvisioningState : Provisioned
Sku                              : Standard
Status                           : Enabled
```

Pode obter informações sobre um circuito de ExpressRoute específico transferindo a chave do serviço como um parâmetro para a chamada.

```powershell
Get-AzureDedicatedCircuit -ServiceKey "*********************************"

Bandwidth                        : 200
CircuitName                      : MyTestCircuit
Location                         : Silicon Valley
ServiceKey                       : *********************************
ServiceProviderName              : equinix
ServiceProviderProvisioningState : Provisioned
Sku                              : Standard
Status                           : Enabled
```

Pode obter descrições detalhadas do todos os parâmetros executando o seguinte exemplo:

```powershell
get-help get-azurededicatedcircuit -detailed
```

## <a name="modify-a-circuit"></a>Modificar um circuito

Pode modificar algumas propriedades de um circuito ExpressRoute sem afetar a conectividade.

Pode efetuar as seguintes tarefas sem período de indisponibilidade:

* Ativar ou desativar um suplemento ExpressRoute premium para o seu circuito do ExpressRoute.
* Aumente a largura de banda do circuito do ExpressRoute fornecido está capacidade disponível na porta. Desatualização de largura de banda de um circuito não é suportada. 
* Altere o plano de medição de dados limitados para dados ilimitados. Não é suportada a alteração do plano de medição de dados ilimitados dados limitados.
* Pode ativar e desativar *permitir operações clássicas*.

Consulte o [FAQ do ExpressRoute](expressroute-faqs.md) para obter mais informações sobre limites e limitações.

### <a name="enable-the-expressroute-premium-add-on"></a>Ativar o suplemento ExpressRoute premium

Pode ativar o suplemento ExpressRoute premium para o seu circuito existente utilizando o cmdlet PowerShell seguinte:

```powershell
Set-AzureDedicatedCircuitProperties -ServiceKey "*********************************" -Sku Premium

Bandwidth                        : 1000
CircuitName                      : TestCircuit
Location                         : Silicon Valley
ServiceKey                       : *********************************
ServiceProviderName              : equinix
ServiceProviderProvisioningState : Provisioned
Sku                              : Premium
Status                           : Enabled
```

O circuito terá agora as funcionalidades de suplemento ExpressRoute premium ativadas. Assim que o comando foi executado com êxito, começa a faturação para a capacidade de suplemento premium.

### <a name="disable-the-expressroute-premium-add-on"></a>Desativar o suplemento ExpressRoute premium

> [!IMPORTANT]
> Esta operação pode falhar se estiver a utilizar recursos que são maiores que o que é permitido para o circuito standard.
> 
> 

#### <a name="considerations"></a>Considerações

* Certifique-se de que o número de redes virtuais ligadas ao circuito é inferior a 10 antes de mudar de premium para standard. Se não fizer isto, o pedido de actualização falha e são cobradas a taxas de premium.
* Tem de desassociar todas as redes virtuais noutras regiões geopolíticas. Se não, o pedido de actualização falha e são cobradas a taxas de premium.
* A tabela de rota tem de ser inferior a 4000 rotas para peering privado. Se o tamanho da tabela de rota for superior a 4000 rotas, a sessão de BGP ignora e não será possível reenabled até que o número de prefixos anunciados fica abaixo 4,000.

#### <a name="to-disable-the-premium-add-on"></a>Para desativar o suplemento premium

Pode desativar o suplemento ExpressRoute premium para o seu circuito existente utilizando o cmdlet PowerShell seguinte:

```powershell

Set-AzureDedicatedCircuitProperties -ServiceKey "*********************************" -Sku Standard

Bandwidth                        : 1000
CircuitName                      : TestCircuit
Location                         : Silicon Valley
ServiceKey                       : *********************************
ServiceProviderName              : equinix
ServiceProviderProvisioningState : Provisioned
Sku                              : Standard
Status                           : Enabled
```

### <a name="update-the-expressroute-circuit-bandwidth"></a>Atualizar a largura de banda do circuito do ExpressRoute

Verifique o [FAQ do ExpressRoute](expressroute-faqs.md) opções de largura de banda para o fornecedor de suporte. Pode escolher qualquer dimensão que é superior ao tamanho do seu circuito existente, desde que permite que a porta física (em que o circuito é criado).

> [!IMPORTANT]
> Poderá ter de recriar o circuito do ExpressRoute, se existir capacidade inadequada na porta existente. Não é possível atualizar o circuito se não houver nenhuma capacidade adicional nessa localização.
>
> Não é possível reduzir a largura de banda de um circuito de ExpressRoute sem interrupção. Desatualização de largura de banda requer a retirar o aprovisionamento do circuito ExpressRoute e, em seguida, reaprovisionar um circuito de ExpressRoute novo.
> 
> 

#### <a name="resize-a-circuit"></a>Redimensionar um circuito

Depois de decidir que precisa de tamanho, pode utilizar o seguinte comando para redimensionar o seu circuito:

```powershell
Set-AzureDedicatedCircuitProperties -ServiceKey ********************************* -Bandwidth 1000

Bandwidth                        : 1000
CircuitName                      : TestCircuit
Location                         : Silicon Valley
ServiceKey                       : *********************************
ServiceProviderName              : equinix
ServiceProviderProvisioningState : Provisioned
Sku                              : Standard
Status                           : Enabled
```

Assim que o seu circuito tem sido tamanho cópias de segurança do lado do Microsoft, tem de contactar o seu fornecedor de conectividade para atualizar as configurações no seu lado para fazer corresponder esta alteração. Faturação começa para a opção de largura de banda atualizado a partir deste ponto no.

Se vir o erro seguinte ao aumentar a largura de banda do circuito, significa que não existe nenhum largura de banda suficiente for deixada na porta física em que é criado o seu circuito existente. Tem de eliminar este circuito e criar um circuito do tamanho que tem de novo.

```powershell
Set-AzureDedicatedCircuitProperties : InvalidOperation : Insufficient bandwidth available to perform this circuit
update operation
At line:1 char:1
+ Set-AzureDedicatedCircuitProperties -ServiceKey ********************* ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
  + CategoryInfo          : CloseError: (:) [Set-AzureDedicatedCircuitProperties], CloudException
  + FullyQualifiedErrorId : Microsoft.WindowsAzure.Commands.ExpressRoute.SetAzureDedicatedCircuitPropertiesCommand
```

## <a name="deprovision-and-delete-a-circuit"></a>Desaprovisionar e eliminar um circuito

### <a name="considerations"></a>Considerações

* Tem de desassociar todas as redes virtuais do circuito ExpressRoute para esta operação tenha êxito. Verifique se tem redes virtuais que estejam ligadas ao circuito se falhar esta operação.
* Se o fornecedor de serviço de circuito de ExpressRoute estado de aprovisionamento é **aprovisionamento** ou **aprovisionado** tem de trabalhar com o fornecedor de serviços para retirar o aprovisionamento do circuito no seu lado. Vamos continuar a reserva de recursos e faturar-lhe até que o fornecedor do serviço concluída desaprovisionamento o circuito e notifica-nos.
* Se o fornecedor de serviços tem desaprovisionada o circuito (o fornecedor do serviço de estado de aprovisionamento está definido como **não aprovisionado**), em seguida, pode eliminar o circuito. Este deixa de faturação para o circuito.

#### <a name="delete-a-circuit"></a>Eliminar um circuito

Pode eliminar o circuito do ExpressRoute, executando o seguinte comando:

```powershell
Remove-AzureDedicatedCircuit -ServiceKey "*********************************"
```
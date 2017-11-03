---
title: "Criar e modificar um circuito ExpressRoute: PowerShell: clássico do Azure | Microsoft Docs"
description: "Este artigo explica os passos para criar e aprovisionar um circuito ExpressRoute. Este artigo também mostra como verificar o estado, update ou delete e retirar o aprovisionamento do seu circuito."
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
ms.date: 03/21/2017
ms.author: ganesr;cherylmc
ms.openlocfilehash: 93ddc2975db34053c6a776d1c3b931536f3f8ec7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="create-and-modify-an-expressroute-circuit-using-powershell-classic"></a>Criar e modificar um circuito ExpressRoute com o PowerShell (clássica)
> [!div class="op_single_selector"]
> * [Portal do Azure](expressroute-howto-circuit-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-circuit-arm.md)
> * [CLI do Azure](howto-circuit-cli.md)
> * [Vídeo - portal do Azure](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit)
> * [PowerShell (clássico)](expressroute-howto-circuit-classic.md)
>

Este artigo explica os passos para criar um circuito ExpressRoute do Azure, utilizando os cmdlets do PowerShell e o modelo de implementação clássica. Este artigo também mostra como verificar o estado, update ou delete e desaprovisionar um circuito ExpressRoute.

[!INCLUDE [expressroute-classic-end-include](../../includes/expressroute-classic-end-include.md)]


**Acerca dos modelos de implementação do Azure**

[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## <a name="before-you-begin"></a>Antes de começar
### <a name="step-1-review-the-prerequisites-and-workflow-articles"></a>Passo 1. Reveja os pré-requisitos e artigos de fluxo de trabalho
Certifique-se de que reviu a [pré-requisitos](expressroute-prerequisites.md) e [fluxos de trabalho](expressroute-workflows.md) antes de iniciar a configuração.  

### <a name="step-2-install-the-latest-versions-of-the-azure-service-management-sm-powershell-modules"></a>Passo 2. Instalar as versões mais recentes dos módulos do PowerShell de gestão de serviço do Azure (SM)
Siga as instruções em [introdução aos cmdlets do Azure PowerShell](/powershell/azure/overview) para obter orientações passo a passo sobre como configurar o computador para utilizar os módulos do PowerShell do Azure.

### <a name="step-3-log-in-to-your-azure-account-and-select-a-subscription"></a>Passo 3. Inicie sessão na sua conta do Azure e selecionar uma subscrição
1. Abra a consola do PowerShell com direitos elevados e ligue-se à sua conta. Utilize o exemplo seguinte para o ajudar na ligação:

        Login-AzureRmAccount

2. Verifique as subscrições da conta.

        Get-AzureRmSubscription

3. Se tiver mais do que uma subscrição, selecione a subscrição que pretende utilizar.

        Select-AzureSubscription -SubscriptionId "Replace_with_your_subscription_id"

4. Confirme se o id de subscrição selecionada é definido como predefinição.

        Get-AzureSubscription -default

## <a name="create-and-provision-an-expressroute-circuit"></a>Criar e aprovisionar um circuito do ExpressRoute
### <a name="step-1-import-the-powershell-modules-for-expressroute"></a>Passo 1. Importe os módulos do PowerShell para o ExpressRoute
 Se ainda não o tiver feito, tem de importar os módulos do Azure e ExpressRoute para a sessão do PowerShell para começar a utilizar os cmdlets ExpressRoute. Importe os módulos na localização de instalação para o computador local. Consoante o método utilizado para instalar os módulos, poderá ser uma localização diferente o seguinte exemplo mostra. Modificar o exemplo, se necessário.  

    Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\Azure.psd1'
    Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\ExpressRoute\ExpressRoute.psd1'

### <a name="step-2-get-the-list-of-supported-providers-locations-and-bandwidths"></a>Passo 2. Obter a lista de fornecedores suportados, as localizações e larguras de banda
Antes de criar um circuito do ExpressRoute, terá da lista de fornecedores de conectividade suportados, as localizações e as opções de largura de banda.

O cmdlet do PowerShell `Get-AzureDedicatedCircuitServiceProvider` devolve estas informações, que irá utilizar em passos posteriores:

    Get-AzureDedicatedCircuitServiceProvider

Verifique se o seu fornecedor de conectividade é listada aqui. Anote as seguintes informações porque precisará dela mais tarde quando criar um circuito:

* Nome
* PeeringLocations
* BandwidthsOffered

Agora, está pronto para criar um circuito ExpressRoute.         

### <a name="step-3-create-an-expressroute-circuit"></a>Passo 3. Criar um circuito do ExpressRoute
O exemplo seguinte mostra como criar uma Mbps 200 circuito de ExpressRoute através de Equinix em Silicon Valley. Se estiver a utilizar um fornecedor diferente e definições diferentes, substitua essa informação quando efetua o pedido.

> [!IMPORTANT]
> O circuito de ExpressRoute será cobrado a partir do momento em que é emitida uma chave de serviço. Certifique-se de que efetuar esta operação quando o fornecedor de conectividade está pronto para aprovisionar o circuito.
> 
> 

Segue-se um exemplo de pedido para uma nova chave de serviço:

    $Bandwidth = 200
    $CircuitName = "MyTestCircuit"
    $ServiceProvider = "Equinix"
    $Location = "Silicon Valley"

    New-AzureDedicatedCircuit -CircuitName $CircuitName -ServiceProviderName $ServiceProvider -Bandwidth $Bandwidth -Location $Location -sku Standard -BillingType MeteredData

Em alternativa, se pretender criar um circuito ExpressRoute com o suplemento premium, utilize o seguinte exemplo. Consulte o [FAQ do ExpressRoute](expressroute-faqs.md) para obter mais detalhes sobre o suplemento premium.

    New-AzureDedicatedCircuit -CircuitName $CircuitName -ServiceProviderName $ServiceProvider -Bandwidth $Bandwidth -Location $Location -sku Premium - BillingType MeteredData


A resposta irá conter a chave do serviço. Pode obter descrições detalhadas do todos os parâmetros executando o seguinte:

    get-help new-azurededicatedcircuit -detailed

### <a name="step-4-list-all-the-expressroute-circuits"></a>Passo 4. Listar todos os circuitos do ExpressRoute
Pode executar o `Get-AzureDedicatedCircuit` comando para obter uma lista de todos os circuitos ExpressRoute que criou:

    Get-AzureDedicatedCircuit

A resposta será algo semelhante ao seguinte exemplo:

    Bandwidth                        : 200
    CircuitName                      : MyTestCircuit
    Location                         : Silicon Valley
    ServiceKey                       : *********************************
    ServiceProviderName              : equinix
    ServiceProviderProvisioningState : NotProvisioned
    Sku                              : Standard
    Status                           : Enabled

Pode obter estas informações em qualquer altura utilizando o `Get-AzureDedicatedCircuit` cmdlet. Efetuar a chamada sem quaisquer parâmetros apresenta uma lista de todos os circuitos. A chave do serviço será listada no *ServiceKey* campo.

    Get-AzureDedicatedCircuit

    Bandwidth                        : 200
    CircuitName                      : MyTestCircuit
    Location                         : Silicon Valley
    ServiceKey                       : *********************************
    ServiceProviderName              : equinix
    ServiceProviderProvisioningState : NotProvisioned
    Sku                              : Standard
    Status                           : Enabled

Pode obter descrições detalhadas do todos os parâmetros executando o seguinte:

    get-help get-azurededicatedcircuit -detailed

### <a name="step-5-send-the-service-key-to-your-connectivity-provider-for-provisioning"></a>Passo 5. Enviar a chave do serviço para o seu fornecedor de conectividade para aprovisionamento
*ServiceProviderProvisioningState* fornece informações sobre o estado atual do aprovisionamento do lado do fornecedor de serviços. *Estado* fornece o estado do lado do Microsoft. Para obter mais informações sobre os Estados de aprovisionamento do circuito, consulte o [fluxos de trabalho](expressroute-workflows.md#expressroute-circuit-provisioning-states) artigo.

Quando cria um novo circuito do ExpressRoute, o circuito estará no seguinte estado:

    ServiceProviderProvisioningState : NotProvisioned
    Status                           : Enabled


O circuito passará para o seguinte estado quando o fornecedor de conectividade está no processo de ativação-lo por si:

    ServiceProviderProvisioningState : Provisioning
    Status                           : Enabled

Um circuito ExpressRoute tem de estar no seguinte estado para poder utilizá-la:

    ServiceProviderProvisioningState : Provisioned
    Status                           : Enabled


### <a name="step-6-periodically-check-the-status-and-the-state-of-the-circuit-key"></a>Passo 6. Verificar periodicamente o estado e o estado da chave do circuito
Isto permite-lhe saber quando o fornecedor tem ativado o seu circuito. Depois do circuito tiver sido configurado, *ServiceProviderProvisioningState* será apresentado como *aprovisionado* conforme mostrado no exemplo seguinte:

    Get-AzureDedicatedCircuit

    Bandwidth                        : 200
    CircuitName                      : MyTestCircuit
    Location                         : Silicon Valley
    ServiceKey                       : *********************************
    ServiceProviderName              : equinix
    ServiceProviderProvisioningState : Provisioned
    Sku                              : Standard
    Status                           : Enabled

### <a name="step-7-create-your-routing-configuration"></a>Passo 7. Criar a configuração de encaminhamento
Consulte o [configuração de encaminhamento de circuito de ExpressRoute (criar e modificar peerings do circuito)](expressroute-howto-routing-classic.md) artigo para obter instruções passo a passo.

> [!IMPORTANT]
> Estas instruções aplicam-se apenas aos circuitos criados com fornecedores de serviços que oferecem serviços de 2 conectividade de camada. Se estiver a utilizar um fornecedor de serviço que oferece gerido layer 3 serviços (normalmente, uma VPN de IP, como MPLS), o seu fornecedor de conectividade irá configurar e gerir encaminhamento por si.
> 
> 

### <a name="step-8-link-a-virtual-network-to-an-expressroute-circuit"></a>Passo 8. Ligar uma rede virtual a um circuito ExpressRoute
Em seguida, ligar uma rede virtual para o circuito do ExpressRoute. Consulte [circuitos do ExpressRoute ligar às redes virtuais](expressroute-howto-linkvnet-classic.md) para obter instruções passo a passo. Se precisar de criar uma rede virtual com o modelo de implementação clássica para o ExpressRoute, consulte [criar uma rede virtual para o ExpressRoute](expressroute-howto-vnet-portal-classic.md).

## <a name="getting-the-status-of-an-expressroute-circuit"></a>Obter o estado de um circuito ExpressRoute
Pode obter estas informações em qualquer altura utilizando o `Get-AzureCircuit` cmdlet. Efetuar a chamada sem quaisquer parâmetros apresenta uma lista de todos os circuitos.

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

Pode obter informações sobre um circuito de ExpressRoute específico transferindo a chave do serviço como um parâmetro para a chamada.

    Get-AzureDedicatedCircuit -ServiceKey "*********************************"

    Bandwidth                        : 200
    CircuitName                      : MyTestCircuit
    Location                         : Silicon Valley
    ServiceKey                       : *********************************
    ServiceProviderName              : equinix
    ServiceProviderProvisioningState : Provisioned
    Sku                              : Standard
    Status                           : Enabled


Pode obter descrições detalhadas do todos os parâmetros executando o seguinte exemplo:

    get-help get-azurededicatedcircuit -detailed

## <a name="modifying-an-expressroute-circuit"></a>Modificar um circuito do ExpressRoute
Pode modificar algumas propriedades de um circuito ExpressRoute sem afetar a conectividade.

Pode fazer o seguinte sem período de indisponibilidade:

* Ativar ou desativar um suplemento ExpressRoute premium para o seu circuito do ExpressRoute.
* Aumente a largura de banda do circuito do ExpressRoute fornecido está capacidade disponível na porta. Tenha em atenção que a desatualização de largura de banda de um circuito não é suportado. 
* Altere o plano de medição de dados limitados para dados ilimitados. Tenha em atenção que a alteração do plano de medição de dados ilimitados dados limitados não é suportado.
* Pode ativar e desativar *permitir operações clássicas*.

Consulte o [FAQ do ExpressRoute](expressroute-faqs.md) para obter mais informações sobre limites e limitações.

### <a name="to-enable-the-expressroute-premium-add-on"></a>Para ativar o suplemento ExpressRoute premium
Pode ativar o suplemento ExpressRoute premium para o seu circuito existente utilizando o cmdlet PowerShell seguinte:

    Set-AzureDedicatedCircuitProperties -ServiceKey "*********************************" -Sku Premium

    Bandwidth                        : 1000
    CircuitName                      : TestCircuit
    Location                         : Silicon Valley
    ServiceKey                       : *********************************
    ServiceProviderName              : equinix
    ServiceProviderProvisioningState : Provisioned
    Sku                              : Premium
    Status                           : Enabled

O circuito terá agora as funcionalidades de suplemento ExpressRoute premium ativadas. Tenha em atenção que iremos iniciará de faturação para a capacidade de suplemento premium, assim que o comando foi executada com êxito.

### <a name="to-disable-the-expressroute-premium-add-on"></a>Para desativar o suplemento ExpressRoute premium
> [!IMPORTANT]
> Esta operação pode falhar se estiver a utilizar recursos que são maiores que o que é permitido para o circuito standard.
> 
> 

#### <a name="considerations"></a>Considerações

* Tem de se certificar de que o número de redes virtuais ligadas ao circuito é inferior a 10 antes de mudar de premium para standard. Se não fizer isto, irá falhar o pedido de atualização e será faturado as taxas de premium.
* Tem de desassociar todas as redes virtuais noutras regiões geopolíticas. Se não fizer isto, irá falhar o pedido de atualização e será faturado as taxas de premium.
* A tabela de rota tem de ser inferior a 4000 rotas para peering privado. Se o tamanho da tabela de rota for superior a 4000 rotas, a sessão de BGP será drop e não será possível reenabled até que o número de prefixos anunciados fica abaixo 4,000.

#### <a name="disable-the-premium-add-on"></a>Desativar o suplemento premium
Pode desativar o suplemento ExpressRoute premium para o seu circuito existente utilizando o cmdlet PowerShell seguinte:

    Set-AzureDedicatedCircuitProperties -ServiceKey "*********************************" -Sku Standard

    Bandwidth                        : 1000
    CircuitName                      : TestCircuit
    Location                         : Silicon Valley
    ServiceKey                       : *********************************
    ServiceProviderName              : equinix
    ServiceProviderProvisioningState : Provisioned
    Sku                              : Standard
    Status                           : Enabled



### <a name="to-update-the-expressroute-circuit-bandwidth"></a>Para atualizar a largura de banda do circuito do ExpressRoute
Verifique o [FAQ do ExpressRoute](expressroute-faqs.md) opções de largura de banda para o fornecedor de suporte. Pode escolher qualquer dimensão que é superior ao tamanho do seu circuito existente, desde que permite que a porta física (em que o circuito é criado).

> [!IMPORTANT]
> Poderá ter de recriar o circuito do ExpressRoute, se existir capacidade inadequada na porta existente. Não é possível atualizar o circuito se não houver nenhuma capacidade adicional nessa localização.
>
> Não é possível reduzir a largura de banda de um circuito de ExpressRoute sem interrupção. Desatualização de largura de banda requer a retirar o aprovisionamento do circuito ExpressRoute e, em seguida, reaprovisionar um circuito de ExpressRoute novo.
> 
> 

#### <a name="resize-a-circuit"></a>Redimensionar um circuito

Depois de decidir que precisa de tamanho, pode utilizar o seguinte comando para redimensionar o seu circuito:

    Set-AzureDedicatedCircuitProperties -ServiceKey ********************************* -Bandwidth 1000

    Bandwidth                        : 1000
    CircuitName                      : TestCircuit
    Location                         : Silicon Valley
    ServiceKey                       : *********************************
    ServiceProviderName              : equinix
    ServiceProviderProvisioningState : Provisioned
    Sku                              : Standard
    Status                           : Enabled

O circuito irá ter foi tamanho cópias de segurança do lado do Microsoft. Tem de contactar o seu fornecedor de conectividade para atualizar as configurações no seu lado para fazer corresponder esta alteração. Tenha em atenção que iremos iniciará de faturação para a opção de largura de banda atualizado a partir deste ponto no.

Se vir o erro seguinte ao aumentar a largura de banda do circuito, significa que não existe nenhum largura de banda suficiente for deixada na porta física em que é criado o seu circuito existente. Tem de eliminar este circuito e criar um circuito do tamanho que tem de novo. 

    Set-AzureDedicatedCircuitProperties : InvalidOperation : Insufficient bandwidth available to perform this circuit
    update operation
    At line:1 char:1
    + Set-AzureDedicatedCircuitProperties -ServiceKey ********************* ...
    + ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
        + CategoryInfo          : CloseError: (:) [Set-AzureDedicatedCircuitProperties], CloudException
        + FullyQualifiedErrorId : Microsoft.WindowsAzure.Commands.ExpressRoute.SetAzureDedicatedCircuitPropertiesCommand


## <a name="deprovisioning-and-deleting-an-expressroute-circuit"></a>Desaprovisionamento e eliminar um circuito do ExpressRoute

### <a name="considerations"></a>Considerações

* Tem de desassociar todas as redes virtuais do circuito ExpressRoute para esta operação tenha êxito. Verifique se tem redes virtuais que estejam ligadas ao circuito se falhar esta operação.
* Se o fornecedor de serviço de circuito de ExpressRoute estado de aprovisionamento é **aprovisionamento** ou **aprovisionado** tem de trabalhar com o fornecedor de serviços para retirar o aprovisionamento do circuito no seu lado. Vamos continuar a reserva de recursos e faturar-lhe até que o fornecedor do serviço concluída desaprovisionamento o circuito e notifica-nos.
* Se o fornecedor de serviços tem desaprovisionada o circuito (o fornecedor do serviço de estado de aprovisionamento está definido como **não aprovisionado**), em seguida, pode eliminar o circuito. Isto irá parar faturação para o circuito.

#### <a name="delete-a-circuit"></a>Eliminar um circuito

Pode eliminar o circuito do ExpressRoute, executando o seguinte comando:

    Remove-AzureDedicatedCircuit -ServiceKey "*********************************"



## <a name="next-steps"></a>Passos seguintes
Depois de criar o seu circuito, certifique-se de que pode fazer o seguinte:

* [Criar e modificar o encaminhamento para o seu circuito do ExpressRoute](expressroute-howto-routing-classic.md)
* [Ligar a rede virtual para o circuito do ExpressRoute](expressroute-howto-linkvnet-classic.md)


---
title: "Configurar a atualização de uma aplicação de Service Fabric | Microsoft Docs"
description: "Saiba como configurar as definições para atualizar uma aplicação de Service Fabric com o Microsoft Visual Studio."
services: service-fabric
documentationcenter: na
author: mikkelhegn
manager: mfussell
editor: tglee
ms.assetid: 1757ba85-0b7b-4f16-8a23-2ddaa61c86c6
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 06/29/2017
ms.author: mikkelhegn
ms.openlocfilehash: 314b29a56e4651222822f40a116af97a7372ff2c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="configure-the-upgrade-of-a-service-fabric-application-in-visual-studio"></a>Configurar a atualização de uma aplicação de Service Fabric no Visual Studio
Visual Studio tools para Azure Service Fabric fornecem suporte de atualização para a publicação de clusters locais ou remotos. Existem três cenários em que pretende atualizar a sua aplicação para uma versão mais recente em vez de substituir a aplicação durante o teste e depuração:

* Dados da aplicação não será interrompidos durante a atualização.
* Disponibilidade permanece elevada para haverá qualquer interrupção do serviço durante a atualização, se existirem que suficiente instâncias de serviço distribuídos por domínios de atualização.
* Testes podem ser executados em relação a uma aplicação enquanto esta está a ser atualizada.

## <a name="parameters-needed-to-upgrade"></a>Parâmetros necessários para atualizar
Pode escolher entre dois tipos de implementação: regular ou atualização. Uma implementação regular apague quaisquer informações de implementação anterior e dados no cluster, enquanto uma implementação de atualização preserva-lo. Quando atualizar uma aplicação de Service Fabric no Visual Studio, terá de fornecer parâmetros de atualização de aplicações e estado de funcionamento a verificação de políticas. Parâmetros de atualização da aplicação ajudam a controlar a atualização, enquanto as políticas de verificação do Estado de funcionamento determinam se a atualização foi concluída com êxito. Consulte [atualização da aplicação de Service Fabric: atualizar parâmetros](service-fabric-application-upgrade-parameters.md) para obter mais detalhes.

Existem três modos de atualização: *monitorizado*, *UnmonitoredAuto*, e *UnmonitoredManual*.

* Uma atualização monitorizado automatiza a atualização e verificação de estado de funcionamento da aplicação.
* Uma atualização UnmonitoredAuto automatiza a atualização, mas ignora a verificação de estado de funcionamento da aplicação.
* Quando efetuar uma atualização UnmonitoredManual, terá de atualizar manualmente cada domínio de atualização.

Cada modo de atualização requer diferentes conjuntos de parâmetros. Consulte [parâmetros de atualização da aplicação](service-fabric-application-upgrade-parameters.md) para saber mais sobre as opções de atualização disponíveis.

## <a name="upgrade-a-service-fabric-application-in-visual-studio"></a>Atualizar uma aplicação de Service Fabric no Visual Studio
Se estiver a utilizar as ferramentas do Visual Studio Service Fabric para atualizar uma aplicação de Service Fabric, pode especificar um processo de publicação para uma atualização em vez de uma implementação normais, verificando o **atualizar a aplicação** caixa de verificação.

### <a name="to-configure-the-upgrade-parameters"></a>Para configurar os parâmetros de atualização
1. Clique em de **definições** botão junto à caixa de verificação. O **Editar parâmetros atualizar** é apresentada a caixa de diálogo. O **Editar parâmetros atualizar** os modos de atualização monitorizados, UnmonitoredAuto e UnmonitoredManual suporta a caixa de diálogo.
2. Selecione o modo de atualização que pretende utilizar e, em seguida, preencha a grelha de parâmetro.

    Cada parâmetro tem valores predefinidos. O parâmetro opcional *DefaultServiceTypeHealthPolicy* demora uma entrada de tabela hash. Eis um exemplo do formato de entrada da tabela hash para *DefaultServiceTypeHealthPolicy*:

    ```
    @{ ConsiderWarningAsError = "false"; MaxPercentUnhealthyDeployedApplications = 0; MaxPercentUnhealthyServices = 0; MaxPercentUnhealthyPartitionsPerService = 0; MaxPercentUnhealthyReplicasPerPartition = 0 }
    ```

    *ServiceTypeHealthPolicyMap* é outro parâmetro opcional que assume um valor de tabela hash no seguinte formato:

    ```    
    @ {"ServiceTypeName" : "MaxPercentUnhealthyPartitionsPerService,MaxPercentUnhealthyReplicasPerPartition,MaxPercentUnhealthyServices"}
    ```

    Eis um exemplo de reais:

    ```
    @{ "ServiceTypeName01" = "5,10,5"; "ServiceTypeName02" = "5,5,5" }
    ```
3. Se selecionar o modo de atualização de UnmonitoredManual, tem de iniciar manualmente uma consola do PowerShell para continuar e concluir o processo de atualização. Consulte [atualização da aplicação de Service Fabric: avançadas tópicos](service-fabric-application-upgrade-advanced.md) para saber a atualização manual como funciona.

## <a name="upgrade-an-application-by-using-powershell"></a>Atualizar uma aplicação utilizando o PowerShell
Pode utilizar os cmdlets do PowerShell para atualizar uma aplicação de Service Fabric. Consulte [tutorial de atualização de aplicação de Service Fabric](service-fabric-application-upgrade-tutorial.md) e [início ServiceFabricApplicationUpgrade](https://msdn.microsoft.com/library/mt125975.aspx) para obter informações detalhadas.

## <a name="specify-a-health-check-policy-in-the-application-manifest-file"></a>Especifique uma política de verificação do Estado de funcionamento no ficheiro de manifesto da aplicação
Cada serviço numa aplicação de Service Fabric pode ter o seus próprio parâmetros de política de estado de funcionamento que substituem os valores predefinidos. Pode fornecer estes valores de parâmetro no ficheiro de manifesto da aplicação.

O exemplo seguinte mostra como aplicar uma política de verificação do Estado de funcionamento exclusivo para cada serviço no manifesto da aplicação.

```xml
<Policies>
    <HealthPolicy ConsiderWarningAsError="false" MaxPercentUnhealthyDeployedApplications="20">
        <DefaultServiceTypeHealthPolicy MaxPercentUnhealthyServices="20"               
                MaxPercentUnhealthyPartitionsPerService="20"
                MaxPercentUnhealthyReplicasPerPartition="20" />
        <ServiceTypeHealthPolicy ServiceTypeName="ServiceTypeName1"
                MaxPercentUnhealthyServices="20"
                MaxPercentUnhealthyPartitionsPerService="20"
                MaxPercentUnhealthyReplicasPerPartition="20" />      
    </HealthPolicy>
</Policies>
```
## <a name="next-steps"></a>Passos seguintes
Para obter mais informações sobre como implementar uma aplicação, consulte [implementar uma aplicação existente no Azure Service Fabric](service-fabric-deploy-existing-app.md).
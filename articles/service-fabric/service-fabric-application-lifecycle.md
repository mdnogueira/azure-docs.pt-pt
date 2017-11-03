---
title: "Ciclo de vida de aplicação no Service Fabric | Microsoft Docs"
description: "Descreve a desenvolver, implementar, testar, atualizar, mantendo em simultâneo e remoção das aplicações de Service Fabric."
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 08837cca-5aa7-40da-b087-2b657224a097
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/29/2017
ms.author: ryanwi
ms.openlocfilehash: d892c5979d0dec54767fe5a9ef84b05739683604
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="service-fabric-application-lifecycle"></a>Ciclo de vida de aplicação de Service Fabric
Com outras plataformas, uma aplicação no Azure Service Fabric normalmente realiza as seguintes fases: estrutura, desenvolvimento, teste, implementação, a atualização, manutenção e remoção. O Service Fabric fornece suporte de primeira classe para o ciclo de vida de aplicação completa das aplicações em nuvem, de desenvolvimento através da implementação, gestão diária e manutenção para desativar eventual. O modelo de serviço permite várias funções diferentes para participar independentemente no ciclo de vida de aplicação. Este artigo fornece uma descrição geral das APIs e como são utilizados pelas funções de diferentes durante as fases do ciclo de vida de aplicação de Service Fabric.

[!INCLUDE [links to azure cli and service fabric cli](../../includes/service-fabric-sfctl.md)]

O vídeo Microsoft Virtual Academy seguinte descreve como gerir o ciclo de vida de aplicação:<center><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=My3Ka56yC_6106218965">
<img src="./media/service-fabric-application-lifecycle/AppLifecycleVid.png" WIDTH="360" HEIGHT="244">
</a></center>

## <a name="service-model-roles"></a>Funções de modelo de serviço
As funções de modelo de serviço são:

* **Serviço Programador**: desenvolvidas pela organização modulares e genéricos serviços que podem ser reaproveitados e utilizados em várias aplicações do mesmo tipo ou tipos diferentes. Por exemplo, um serviço de fila pode ser utilizado para criar uma aplicação de emissão de permissões (suporte técnico) ou uma aplicação de comércio eletrónico (carrinho de compras).
* **Programador de aplicações**: Cria aplicações através da integração de uma coleção de serviços para satisfazer determinados requisitos específicos ou cenários. Por exemplo, um Web site de comércio eletrónico pode integrar ""JSON sem monitorização de estado front-end serviço, "Serviço de monitorização de estado de Auction" e "Serviço de monitorização de estado de fila" para criar uma solução auctioning.
* **Administrador da aplicação**: toma decisões relativamente à configuração da aplicação (preencher os parâmetros de modelo de configuração), a implementação (mapeamento de recursos disponíveis) e a qualidade de serviço. Por exemplo, o administrador de uma aplicação decide a região de idioma (inglês para os Estados Unidos) ou japonês para o Japão, por exemplo, da aplicação. Uma aplicação implementada diferentes pode ter definições diferentes.
* **Operador**: implementa as aplicações com base na configuração da aplicação e os requisitos especificados pelo administrador da aplicação. Por exemplo, um operador Aprovisiona e implementa a aplicação e assegura que está em execução no Azure. Os operadores de monitorizar as informações de estado de funcionamento e desempenho da aplicação e manter a infraestrutura física conforme necessário.

## <a name="develop"></a>Programar
1. A *serviço Programador* desenvolvidas pela organização tipos diferentes de serviços utilizando a [Reliable Actors](service-fabric-reliable-actors-introduction.md) ou [Reliable Services](service-fabric-reliable-services-introduction.md) modelo de programação.
2. A *serviço Programador* forma declarativa descreve os tipos de serviço programadas num ficheiro de manifesto serviço consiste num ou mais pacotes de código, configuração e dados.
3. Um *programador da aplicação* , em seguida, cria uma aplicação utilizando tipos de serviço diferente.
4. Um *programador da aplicação* forma declarativa descreve o tipo de aplicação no manifesto de aplicação pelos manifestos de serviço dos serviços que constituem a referenciar adequadamente a substituir e parameterizing diferentes definições de configuração e implementação dos serviços que constituem.

Consulte [introdução a Reliable Actors](service-fabric-reliable-actors-get-started.md) e [introdução a Reliable Services](service-fabric-reliable-services-quick-start.md) para obter exemplos.

## <a name="deploy"></a>Implementação
1. Um *administrador da aplicação* tailors o tipo de aplicação a uma aplicação específica para ser implementada para um cluster do Service Fabric, especificando os parâmetros adequados do **ApplicationType** elemento no manifesto da aplicação.
2. Um *operador* carrega o pacote de aplicação para o arquivo de imagens do cluster utilizando o [ **CopyApplicationPackage** método](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient#System_Fabric_FabricClient_ApplicationManagementClient_CopyApplicationPackage_System_String_System_String_System_String_) ou [ **cópia ServiceFabricApplicationPackage** cmdlet](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps). O pacote de aplicação contém o manifesto da aplicação e a coleção de pacotes de serviços. Serviço de recursos de infraestrutura implementa aplicações a partir do pacote de aplicação armazenado no arquivo de imagem, que pode ser um arquivo de Blobs do Azure ou o serviço de sistema do Service Fabric.
3. O *operador* , em seguida, Aprovisiona o tipo de aplicação no cluster de destino da utilização de pacote de aplicação foi carregada o [ **ProvisionApplicationAsync** método](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient#System_Fabric_FabricClient_ApplicationManagementClient_ProvisionApplicationAsync_System_String_System_TimeSpan_System_Threading_CancellationToken_), a [ **Register-ServiceFabricApplicationType** cmdlet](https://docs.microsoft.com/powershell/servicefabric/vlatest/register-servicefabricapplicationtype), ou o [ **aprovisionar uma aplicação** operação REST](https://docs.microsoft.com/rest/api/servicefabric/provision-an-application).
4. Após o aprovisionamento da aplicação, um *operador* inicia a aplicação com os parâmetros fornecidos pelo *administrador da aplicação* utilizando o [ **CreateApplicationAsync** método](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient#System_Fabric_FabricClient_ApplicationManagementClient_CreateApplicationAsync_System_Fabric_Description_ApplicationDescription_System_TimeSpan_System_Threading_CancellationToken_), a [ **New-ServiceFabricApplication** cmdlet](https://docs.microsoft.com/powershell/servicefabric/vlatest/new-servicefabricapplication), ou o [ **Criar aplicação** operação REST](https://docs.microsoft.com/rest/api/servicefabric/create-an-application).
5. Depois da aplicação foi implementada, uma *operador* utiliza o [ **CreateServiceAsync** método](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.servicemanagementclient#System_Fabric_FabricClient_ServiceManagementClient_CreateServiceAsync_System_Fabric_Description_ServiceDescription_System_TimeSpan_System_Threading_CancellationToken_), a [ **New-ServiceFabricService** cmdlet](https://docs.microsoft.com/powershell/servicefabric/vlatest/new-servicefabricservice), ou o [ **criar serviço** operação REST](https://docs.microsoft.com/rest/api/servicefabric/create-a-service) para criar novas instâncias de serviço para a aplicação com base nos tipos de serviço disponível.
6. A aplicação está agora em execução no cluster de Service Fabric.

Consulte [implementar uma aplicação](service-fabric-deploy-remove-applications.md) para obter exemplos.

## <a name="test"></a>Teste
1. Após a implementação para o cluster de desenvolvimento local ou um cluster de teste, uma *serviço Programador* executa o cenário de teste de ativação pós-falha incorporado utilizando o [ **FailoverTestScenarioParameters** ](https://docs.microsoft.com/dotnet/api/system.fabric.testability.scenario.failovertestscenarioparameters#System_Fabric_Testability_Scenario_FailoverTestScenarioParameters) e [ **FailoverTestScenario** ](https://docs.microsoft.com/dotnet/api/system.fabric.testability.scenario.failovertestscenario#System_Fabric_Testability_Scenario_FailoverTestScenario) classes, ou o [ **Invoke-ServiceFabricFailoverTestScenario** cmdlet](/powershell/module/servicefabric/invoke-servicefabricfailovertestscenario?view=azureservicefabricps). O cenário de teste de ativação pós-falha é executado um serviço especificado através de transições importantes e as ativações pós-falha para se certificar de que está ainda disponível e a funcionar.
2. O *serviço Programador* , em seguida, executa o teste de chaos incorporada cenário utilizando o [ **ChaosTestScenarioParameters** ](https://docs.microsoft.com/dotnet/api/system.fabric.testability.scenario.chaostestscenarioparameters#System_Fabric_Testability_Scenario_ChaosTestScenarioParameters) e [ **ChaosTestScenario** ](https://docs.microsoft.com/dotnet/api/system.fabric.testability.scenario.chaostestscenario#System_Fabric_Testability_Scenario_ChaosTestScenario) classes, ou o [ **Invoke-ServiceFabricChaosTestScenario** cmdlet](/powershell/module/servicefabric/invoke-servicefabricchaostestscenario?view=azureservicefabricps). O cenário de teste chaos induces aleatoriamente vários nós, o pacote do código e falhas de réplica para o cluster.
3. O *serviço Programador* [testa a comunicação de serviço a serviço](service-fabric-testability-scenarios-service-communication.md) ao criar cenários de teste que movem réplicas primárias em torno do cluster.

Consulte [introdução para o serviço de análise de falhas](service-fabric-testability-overview.md) para obter mais informações.

## <a name="upgrade"></a>Atualizar
1. A *serviço Programador* atualiza os serviços constituintes da aplicação com instâncias criadas e/ou corrige erros e fornece uma nova versão do manifesto de serviço.
2. Um *programador da aplicação* substituições e parameterizes as definições de configuração e implementação dos serviços consistentes e fornece uma nova versão do manifesto da aplicação. O programador da aplicação, em seguida, incorpora as novas versões do serviço manifestos da aplicação e fornece uma nova versão do tipo de aplicação num pacote de aplicação atualizado.
3. Um *administrador da aplicação* incorpora a nova versão do tipo de aplicação a aplicação de destino, atualizando os parâmetros adequados.
4. Um *operador* carrega o pacote de aplicação atualizado para a imagem de cluster arquivo utilizando o [ **CopyApplicationPackage** método](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient#System_Fabric_FabricClient_ApplicationManagementClient_CopyApplicationPackage_System_String_System_String_System_String_) ou [ **ServiceFabricApplicationPackage cópia** cmdlet](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps). O pacote de aplicação contém o manifesto da aplicação e a coleção de pacotes de serviços.
5. Um *operador* Aprovisiona a nova versão da aplicação no cluster de destino utilizando o [ **ProvisionApplicationAsync** método](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient#System_Fabric_FabricClient_ApplicationManagementClient_ProvisionApplicationAsync_System_String_System_TimeSpan_System_Threading_CancellationToken_), a [ **Register-ServiceFabricApplicationType** cmdlet](https://docs.microsoft.com/powershell/servicefabric/vlatest/register-servicefabricapplicationtype), ou o [ **aprovisionar uma aplicação** operação REST](https://docs.microsoft.com/rest/api/servicefabric/provision-an-application).
6. Um *operador* atualiza a aplicação de destino para a nova versão utilizando a [ **UpgradeApplicationAsync** método](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient#System_Fabric_FabricClient_ApplicationManagementClient_UpgradeApplicationAsync_System_Fabric_Description_ApplicationUpgradeDescription_System_TimeSpan_System_Threading_CancellationToken_), a [ **início ServiceFabricApplicationUpgrade** cmdlet](https://docs.microsoft.com/powershell/servicefabric/vlatest/start-servicefabricapplicationupgrade), ou o [ **atualizar uma aplicação** operação REST](https://docs.microsoft.com/rest/api/servicefabric/upgrade-an-application).
7. Um *operador* verifica o progresso da atualização utilizando o [ **GetApplicationUpgradeProgressAsync** método](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient#System_Fabric_FabricClient_ApplicationManagementClient_GetApplicationUpgradeProgressAsync_System_Uri_System_TimeSpan_System_Threading_CancellationToken_), a [ **Get-ServiceFabricApplicationUpgrade** cmdlet](https://docs.microsoft.com/powershell/servicefabric/vlatest/get-servicefabricapplicationupgrade), ou o [ **obter progresso da atualização aplicação** operação REST](https://docs.microsoft.com/rest/api/servicefabric/get-the-progress-of-an-application-upgrade1).
8. Se necessário, o *operador* modifica e volta os parâmetros da atual aplicação fazer a actualização utilizando o [ **UpdateApplicationUpgradeAsync** método](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient#System_Fabric_FabricClient_ApplicationManagementClient_UpdateApplicationUpgradeAsync_System_Fabric_Description_ApplicationUpgradeUpdateDescription_System_TimeSpan_System_Threading_CancellationToken_), a [ **atualização ServiceFabricApplicationUpgrade** cmdlet](https://docs.microsoft.com/powershell/servicefabric/vlatest/update-servicefabricapplicationupgrade), ou o [ **atualização da aplicação de atualização** operação REST](https://docs.microsoft.com/rest/api/servicefabric/update-an-application-upgrade).
9. Se necessário, o *operador* reverte a aplicação atual atualizar utilizando o [ **RollbackApplicationUpgradeAsync** método](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient#System_Fabric_FabricClient_ApplicationManagementClient_RollbackApplicationUpgradeAsync_System_Uri_System_TimeSpan_System_Threading_CancellationToken_), a [ **ServiceFabricApplicationRollback início** cmdlet](https://docs.microsoft.com/powershell/servicefabric/vlatest/start-servicefabricapplicationrollback), ou o [ **atualização da aplicação de reversão** operação REST](https://docs.microsoft.com/rest/api/servicefabric/rollback-an-application-upgrade).
10. Service Fabric atualiza a aplicação de destino em execução no cluster sem perda de disponibilidade de qualquer um dos respetivos serviços constituintes.

Consulte o [tutorial de atualização da aplicação](service-fabric-application-upgrade-tutorial.md) para obter exemplos.

## <a name="maintain"></a>Manter
1. Para atualização do sistema operativo e patches, Service Fabric das interfaces com a infraestrutura do Azure para garantir a disponibilidade de todas as aplicações em execução no cluster.
2. Para as atualizações e correções de erros para a plataforma de Service Fabric, o Service Fabric atualiza-se sem perda de disponibilidade de qualquer uma das aplicações em execução no cluster.
3. Um *administrador da aplicação* aprova a adição ou remoção de nós de um cluster depois de analisar os dados de utilização de capacidade histórica e a pedido futura prevista.
4. Um *operador* adiciona e remove nós especificado pelo *administrador da aplicação*.
5. Quando são adicionados novos nós ou nós existentes são removidos do cluster, Service Fabric automaticamente carga balanceia as aplicações em execução em todos os nós do cluster para alcançar um desempenho ideal.

## <a name="remove"></a>Remover
1. Um *operador* pode eliminar uma instância específica de um serviço em execução no cluster sem remover a aplicação completa a utilizar o [ **DeleteServiceAsync** método](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.servicemanagementclient#System_Fabric_FabricClient_ServiceManagementClient_DeleteServiceAsync_System_Fabric_Description_DeleteServiceDescription_System_TimeSpan_System_Threading_CancellationToken_), a [ **remover ServiceFabricService** cmdlet](https://docs.microsoft.com/powershell/servicefabric/vlatest/remove-servicefabricservice), ou o [ **eliminação de serviços** operação REST](https://docs.microsoft.com/rest/api/servicefabric/delete-a-service).  
2. Um *operador* também pode eliminar uma instância de aplicação e todos os seus serviços utilizando a [ **DeleteApplicationAsync** método](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient#System_Fabric_FabricClient_ApplicationManagementClient_DeleteApplicationAsync_System_Fabric_Description_DeleteApplicationDescription_System_TimeSpan_System_Threading_CancellationToken_), a [ **remover ServiceFabricApplication** cmdlet](https://docs.microsoft.com/powershell/servicefabric/vlatest/remove-servicefabricapplication), ou o [ **eliminar aplicação** operação REST](https://docs.microsoft.com/rest/api/servicefabric/delete-an-application).
3. Depois das aplicações e serviços tem parado, o *operador* pode anular o provisionamento o tipo de aplicação utilizando o [ **UnprovisionApplicationAsync** método](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient#System_Fabric_FabricClient_ApplicationManagementClient_UnprovisionApplicationAsync_System_String_System_String_System_TimeSpan_System_Threading_CancellationToken_), a [ **Unregister-ServiceFabricApplicationType** cmdlet](https://docs.microsoft.com/powershell/servicefabric/vlatest/unregister-servicefabricapplicationtype), ou o [ **anular o aprovisionamento de uma aplicação** operação REST](https://docs.microsoft.com/rest/api/servicefabric/unprovision-an-application). O tipo de aplicação de anulação de aprovisionamento não remover o pacote de aplicações do arquivo de imagens. Tem de remover manualmente o pacote de aplicações.
4. Um *operador* remove o pacote de aplicações do arquivo de imagens com o [ **RemoveApplicationPackage** método](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient#System_Fabric_FabricClient_ApplicationManagementClient_RemoveApplicationPackage_System_String_System_String_) ou [ **remover ServiceFabricApplicationPackage** cmdlet](/powershell/module/servicefabric/remove-servicefabricapplicationpackage?view=azureservicefabricps).

Consulte [implementar uma aplicação](service-fabric-deploy-remove-applications.md) para obter exemplos.

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações sobre desenvolver, testar e gerir aplicações de Service Fabric e serviços, consulte:

* [Reliable Actors](service-fabric-reliable-actors-introduction.md)
* [Reliable Services](service-fabric-reliable-services-introduction.md)
* [Implementar uma aplicação](service-fabric-deploy-remove-applications.md)
* [Atualização da aplicação](service-fabric-application-upgrade.md)
* [Descrição geral de teste](service-fabric-testability-overview.md)

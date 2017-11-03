---
title: "Resolução de problemas de atualizações de aplicações | Microsoft Docs"
description: "Este artigo abrange alguns problemas comuns em torno de atualização de uma aplicação de Service Fabric e como resolvê-los."
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: 
ms.assetid: 19ad152e-ec50-4327-9f19-065c875c003c
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/03/2017
ms.author: subramar
ms.openlocfilehash: acfd26674aafab4ed1925d6b33967f917058b1be
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshoot-application-upgrades"></a>Resolver problemas da atualização da aplicação
Este artigo aborda alguns dos problemas comuns à volta a atualizar uma aplicação de Service Fabric do Azure e como resolvê-los.

## <a name="troubleshoot-a-failed-application-upgrade"></a>Resolver problemas de uma atualização da aplicação falhou
Quando uma atualização a falhar, o resultado a **Get-ServiceFabricApplicationUpgrade** comando contém informações adicionais para a falha de depuração.  A lista seguinte especifica como as informações adicionais podem ser utilizadas:

1. Identifique o tipo de falha.
2. Identifica o motivo da falha.
3. Isole um ou mais componentes de falha para uma investigação mais aprofundada.

Esta informação está disponível quando o Service Fabric Deteta a falha independentemente o **FailureAction** é reverter ou suspender a atualização.

### <a name="identify-the-failure-type"></a>Identifique o tipo de falha
Na saída do **Get-ServiceFabricApplicationUpgrade**, **FailureTimestampUtc** identifica timestamp, (em UTC), que foi detetada uma falha de atualização pelo Service Fabric e  **FailureAction** foi acionada. **Motivofalha** identifica um dos três causas possíveis de alto nível da falha:

1. UpgradeDomainTimeout - indica que um determinado domínio de atualização demorou demasiado tempo a concluir e **UpgradeDomainTimeout** expirou.
2. OverallUpgradeTimeout - indica que a atualização geral demorou demasiado tempo a concluir e **UpgradeTimeout** expirou.
3. HealthCheck - indica que depois de atualizar um domínio de atualização, a aplicação permanecer mau estado de funcionamento, de acordo com as políticas de estado de funcionamento especificado e **HealthCheckRetryTimeout** expirou.

Estas entradas apenas apareçam no resultado quando a atualização falhará e começa a reversão. Informações adicionais são apresentadas consoante o tipo de falha.

### <a name="investigate-upgrade-timeouts"></a>Investigar tempos limite de atualização
Atualize o tempo limite de falhas são normalmente causadas por problemas de disponibilidade do serviço. A saída seguindo este parágrafo é típica de atualizações em que as réplicas do serviço ou instâncias não conseguir iniciar na nova versão de código. O **UpgradeDomainProgressAtFailure** campo captura um instantâneo de qualquer trabalho de atualização pendentes no momento da falha.

```
PS D:\temp> Get-ServiceFabricApplicationUpgrade fabric:/DemoApp

ApplicationName                : fabric:/DemoApp
ApplicationTypeName            : DemoAppType
TargetApplicationTypeVersion   : v2
ApplicationParameters          : {}
StartTimestampUtc              : 4/14/2015 9:26:38 PM
FailureTimestampUtc            : 4/14/2015 9:27:05 PM
FailureReason                  : UpgradeDomainTimeout
UpgradeDomainProgressAtFailure : MYUD1

                                 NodeName            : Node4
                                 UpgradePhase        : PostUpgradeSafetyCheck
                                 PendingSafetyChecks :
                                     WaitForPrimaryPlacement - PartitionId: 744c8d9f-1d26-417e-a60e-cd48f5c098f0

                                 NodeName            : Node1
                                 UpgradePhase        : PostUpgradeSafetyCheck
                                 PendingSafetyChecks :
                                     WaitForPrimaryPlacement - PartitionId: 4b43f4d8-b26b-424e-9307-7a7a62e79750
UpgradeState                   : RollingBackCompleted
UpgradeDuration                : 00:00:46
CurrentUpgradeDomainDuration   : 00:00:00
NextUpgradeDomain              :
UpgradeDomainsStatus           : { "MYUD1" = "Completed";
                                 "MYUD2" = "Completed";
                                 "MYUD3" = "Completed" }
UpgradeKind                    : Rolling
RollingUpgradeMode             : UnmonitoredAuto
ForceRestart                   : False
UpgradeReplicaSetCheckTimeout  : 00:00:00
```

Neste exemplo, a atualização falhou no domínio de atualização *MYUD1* e duas partições (*744c8d9f-1d26-417e-a60e-cd48f5c098f0* e *4b43f4d8-b26b-424e-9307-7a7a62e79750*) foram bloqueadas. As partições foram bloqueadas porque o tempo de execução não foi possível colocar réplicas primárias (*WaitForPrimaryPlacement*) em nós de destino *Nó1* e *Nó4*.

O **Get-ServiceFabricNode** comando pode ser utilizado para verificar se estes dois nós são no domínio de atualização *MYUD1*. O *UpgradePhase* indica *PostUpgradeSafetyCheck*, o que significa que estas verificações de segurança estão a ocorrer depois de todos os nós do domínio de atualização tem concluído a atualização. Todas estas informações aponta para um potencial problema com a nova versão do código da aplicação. Os problemas mais comuns são erros de serviço na open ou promoção para caminhos de código principal.

Um *UpgradePhase* de *PreUpgradeSafetyCheck* significa ocorreram problemas antes que foi efetuada a preparar o domínio de atualização. Neste caso, os problemas mais comuns são erros de serviço na fechar ou despromoção de caminhos de código principal.

Atual **UpgradeState** é *RollingBackCompleted*, por isso, a atualização original tem de ter sido executada com uma reversão **FailureAction**, que foi revertida automaticamente fazer uma cópia da atualização após a falha. Se a atualização original foi efetuada com uma manual **FailureAction**, em seguida, a atualização seria em vez disso, estar num estado suspenso para permitir que em direto depuração da aplicação.

Em casos raros, os **UpgradeDomainProgressAtFailure** campo pode estar vazio se a atualização geral expirar tal como o sistema estiver concluída todo o trabalho para o domínio de atualização atual. Se isto acontecer, tente aumentar o **UpgradeTimeout** e **UpgradeDomainTimeout** Atualize os valores de parâmetros e repita a atualização.

### <a name="investigate-health-check-failures"></a>Investigar falhas de verificação do Estado de funcionamento
Falhas de verificação do Estado de funcionamento podem ser acionadas por vários problemas que podem surgir todos os nós de um domínio de atualização concluída a atualização e passar todas as verificações de segurança. A saída seguindo este parágrafo é típica de uma falha de atualização devido a verificações de estado de funcionamento falhou. O **UnhealthyEvaluations** campo captura um instantâneo de verificações de estado de funcionamento que falharam no momento da atualização, de acordo com a especificado [política de estado de funcionamento](service-fabric-health-introduction.md).

```
PS D:\temp> Get-ServiceFabricApplicationUpgrade fabric:/DemoApp

ApplicationName                         : fabric:/DemoApp
ApplicationTypeName                     : DemoAppType
TargetApplicationTypeVersion            : v4
ApplicationParameters                   : {}
StartTimestampUtc                       : 4/24/2015 2:42:31 AM
UpgradeState                            : RollingForwardPending
UpgradeDuration                         : 00:00:27
CurrentUpgradeDomainDuration            : 00:00:27
NextUpgradeDomain                       : MYUD2
UpgradeDomainsStatus                    : { "MYUD1" = "Completed";
                                          "MYUD2" = "Pending";
                                          "MYUD3" = "Pending" }
UnhealthyEvaluations                    :
                                          Unhealthy services: 50% (2/4), ServiceType='PersistedServiceType', MaxPercentUnhealthyServices=0%.

                                          Unhealthy service: ServiceName='fabric:/DemoApp/Svc3', AggregatedHealthState='Error'.

                                              Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                                              Unhealthy partition: PartitionId='3a9911f6-a2e5-452d-89a8-09271e7e49a8', AggregatedHealthState='Error'.

                                                  Error event: SourceId='Replica', Property='InjectedFault'.

                                          Unhealthy service: ServiceName='fabric:/DemoApp/Svc2', AggregatedHealthState='Error'.

                                              Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                                              Unhealthy partition: PartitionId='744c8d9f-1d26-417e-a60e-cd48f5c098f0', AggregatedHealthState='Error'.

                                                  Error event: SourceId='Replica', Property='InjectedFault'.

UpgradeKind                             : Rolling
RollingUpgradeMode                      : Monitored
FailureAction                           : Manual
ForceRestart                            : False
UpgradeReplicaSetCheckTimeout           : 49710.06:28:15
HealthCheckWaitDuration                 : 00:00:00
HealthCheckStableDuration               : 00:00:10
HealthCheckRetryTimeout                 : 00:00:10
UpgradeDomainTimeout                    : 10675199.02:48:05.4775807
UpgradeTimeout                          : 10675199.02:48:05.4775807
ConsiderWarningAsError                  :
MaxPercentUnhealthyPartitionsPerService :
MaxPercentUnhealthyReplicasPerPartition :
MaxPercentUnhealthyServices             :
MaxPercentUnhealthyDeployedApplications :
ServiceTypeHealthPolicyMap              :
```

Primeiro investigar falhas de verificação do Estado de funcionamento requer o conhecimento do modelo de estado de funcionamento de Service Fabric. Mas mesmo sem essa conhecimento aprofundado, é possível ver que os dois serviços são mau estado de funcionamento: *fabric: / DemoApp/Svc3* e *recursos de infraestrutura: DemoApp/Svc2*, juntamente com os relatórios de estado de funcionamento de erro ("InjectedFault" Neste caso). Neste exemplo, dois fora de quatro serviços são mau estado de funcionamento, que é inferior ao objetivo a predefinição de 0% mau estado de funcionamento (*MaxPercentUnhealthyServices*).

A atualização foi suspenso após a falha, especificando um **FailureAction** de manual quando iniciar a atualização. Este modo permite-nos investigar o sistema em direto do Estado de falha antes de efetuar outras ação.

### <a name="recover-from-a-suspended-upgrade"></a>Recuperar a partir de uma atualização suspensa
Com uma reversão **FailureAction**, não há nenhum recuperação necessária, uma vez que a atualização automaticamente reverte após a falha. Com uma manual **FailureAction**, existem várias opções de recuperação:

1.  acionar uma reversão
2. Avance manualmente o resto da atualização
3. Retomar a atualização monitorizada

O **início ServiceFabricApplicationRollback** comando pode ser utilizado em qualquer altura para iniciar a reverter a aplicação. Depois do comando devolve com êxito, o pedido de reversão foi registado no sistema e inicia imediatamente após essa data.

O **retomar ServiceFabricApplicationUpgrade** comandos podem ser utilizados para prosseguir doravante a atualização manualmente, um domínio de atualização a uma hora. Neste modo, são efetuadas verificações de segurança apenas pelo sistema. Verificações de estado de funcionamento não são executadas. Este comando só pode ser utilizado quando o *UpgradeState* mostra *RollingForwardPending*, que significa que o domínio de atualização atual concluiu a atualização, mas não foi iniciado (pendente) dos seguinte.

O **atualização ServiceFabricApplicationUpgrade** comando pode ser utilizado para retomar a atualização com ambos os segurança monitorizada e estado de funcionamento verifica a ser efetuada.

```
PS D:\temp> Update-ServiceFabricApplicationUpgrade fabric:/DemoApp -UpgradeMode Monitored

UpgradeMode                             : Monitored
ForceRestart                            :
UpgradeReplicaSetCheckTimeout           :
FailureAction                           :
HealthCheckWaitDuration                 :
HealthCheckStableDuration               :
HealthCheckRetryTimeout                 :
UpgradeTimeout                          :
UpgradeDomainTimeout                    :
ConsiderWarningAsError                  :
MaxPercentUnhealthyPartitionsPerService :
MaxPercentUnhealthyReplicasPerPartition :
MaxPercentUnhealthyServices             :
MaxPercentUnhealthyDeployedApplications :
ServiceTypeHealthPolicyMap              :

PS D:\temp>
```

A atualização continua a partir do domínio de atualização em que foi suspenso pela última vez e utilize o mesmo atualizar parâmetros e as políticas de estado de funcionamento como antes. Se for necessário, qualquer um dos parâmetros de atualização e as políticas de estado de funcionamento apresentadas no resultado anterior podem ser alterados no mesmo comando, quando a atualização de retoma. Neste exemplo, a atualização foi retomada no modo de monitorizados, com os parâmetros e as políticas de estado de funcionamento inalteradas.

## <a name="further-troubleshooting"></a>Resolução de problemas
### <a name="service-fabric-is-not-following-the-specified-health-policies"></a>Recursos de infraestrutura de serviço não está a seguir as políticas de estado de funcionamento especificado
Uma causa possível 1:

Service Fabric traduz percentagens de todos os para o número real de entidades (por exemplo, as réplicas, partições e serviços) para avaliação de estado de funcionamento e sempre Arredonda por excesso para entidades toda. Por exemplo, se o número máximo *MaxPercentUnhealthyReplicasPerPartition* é de 21% e existirem cinco réplicas, em seguida, o Service Fabric permite até duas réplicas mau estado de funcionamento (ou seja,`Math.Ceiling (5*0.21)`). Assim, as políticas de estado de funcionamento devem ser definidas em conformidade.

Uma causa possível 2:

Políticas de estado de funcionamento são especificadas em termos de percentagens de serviços totais e instâncias de serviço não específicas. Por exemplo, antes de uma atualização, se uma aplicação tem quatro service instâncias A, B, C e D, onde o serviço D mau estado de funcionamento, mas com e pouco impacto para a aplicação. Queremos ignorar o serviço de estado de funcionamento incorreto conhecido D durante a atualização e defina o parâmetro *MaxPercentUnhealthyServices* seja 25%, partindo do princípio de apenas A, B e C têm de ser bom estado de funcionamento.

No entanto, durante a atualização, D podem tornar-se em bom estado enquanto C torna-se danificado. A atualização seria ainda assim ter sucesso porque apenas 25% dos serviços estão em mau estado de funcionamento. No entanto, poderá resultar em erros não antecipados devido a C a ser inesperadamente mau estado de funcionamento em vez de D. Nesta situação, D deve ser modelada como um tipo de serviço diferentes de A, B e C. Uma vez que as políticas de estado de funcionamento são especificadas por tipo de serviço, limiares diferentes percentagem mau estado de funcionamento podem ser aplicados a diferentes serviços. 

### <a name="i-did-not-specify-a-health-policy-for-application-upgrade-but-the-upgrade-still-fails-for-some-time-outs-that-i-never-specified"></a>Posso não especificou uma política de estado de funcionamento para a atualização da aplicação, mas a atualização ainda falha por algum tempos limite que posso nunca especificado
Quando as políticas de estado de funcionamento não são fornecidos para o pedido de atualização, estes são obtidas a partir de *ApplicationManifest.xml* da versão de aplicação atual. Por exemplo, se estiver a atualizar a aplicação X da versão 1.0 para a versão 2.0, políticas de estado de funcionamento de aplicações especificado para a versão 1.0 são utilizadas. Se uma política de estado de funcionamento de diferentes deve ser utilizada para a atualização, a política tem de ser especificado como parte da chamada de API de atualização de aplicação. As políticas especificadas como parte da chamada de API aplicam-se apenas durante a atualização. Assim que a atualização estiver concluída, as políticas especificadas no *ApplicationManifest.xml* são utilizados.

### <a name="incorrect-time-outs-are-specified"></a>Está especificados tempos limite incorreto
Pode ter wondered sobre o que acontece quando tempos limite está definidos de forma inconsistente. Por exemplo, pode ter um *UpgradeTimeout* da inferior à *UpgradeDomainTimeout*. A resposta é que é devolvido um erro. São devolvidos erros se o *UpgradeDomainTimeout* é menor que a soma de *HealthCheckWaitDuration* e *HealthCheckRetryTimeout*, ou se  *UpgradeDomainTimeout* é menor que a soma de *HealthCheckWaitDuration* e *HealthCheckStableDuration*.

### <a name="my-upgrades-are-taking-too-long"></a>A minha atualizações estão a demorar demasiado tempo
O tempo para uma atualização concluir depende as verificações de estado de funcionamento e tempos limite especificado. Verificações de estado de funcionamento e tempos limite depende quanto tempo demora para copiar, implementar e stabilize a aplicação. A ser demasiado agressiva com tempos limite poderá significar mais falhadas atualizações, pelo que recomendamos que comece moderadamente com tempos limite de tempo.

Eis uma revisão rápida na forma como os tempos limite interagem com os tempos de atualização:

Atualizado para um domínio de atualização não conseguiu concluir mais rápida do que *HealthCheckWaitDuration* + *HealthCheckStableDuration*.

Falha na atualização não pode ocorrer mais rápida do que *HealthCheckWaitDuration* + *HealthCheckRetryTimeout*.

A hora de atualização de um domínio de atualização é limitada pela *UpgradeDomainTimeout*.  Se *HealthCheckRetryTimeout* e *HealthCheckStableDuration* são ambos diferente de zero e o estado de funcionamento da aplicação mantém mudar novamente estabelecido e, em seguida, a atualização, eventualmente, o tempo limite no *UpgradeDomainTimeout*. *UpgradeDomainTimeout* inicia contando para baixo de uma vez, a atualização para o domínio de atualização atual começa.

## <a name="next-steps"></a>Passos seguintes
[Atualizar a aplicação utilizando o Visual Studio](service-fabric-application-upgrade-tutorial.md) orienta-o através de uma atualização da aplicação com o Visual Studio.

[Atualizar a sua aplicação através do Powershell](service-fabric-application-upgrade-tutorial-powershell.md) orienta-o através de uma atualização da aplicação através do PowerShell.

Controlar a forma como a aplicação atualiza utilizando [atualizar parâmetros](service-fabric-application-upgrade-parameters.md).

Faça as atualizações de aplicações compatíveis aprender a utilizar [dados serialização](service-fabric-application-upgrade-data-serialization.md).

Saiba como utilizar a funcionalidade avançada ao atualizar a sua aplicação ao referir-se para [tópicos avançados](service-fabric-application-upgrade-advanced.md).

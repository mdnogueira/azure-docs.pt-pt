---
title: "Atualização da aplicação: atualizar parâmetros | Microsoft Docs"
description: "Descreve os parâmetros relacionados com a atualização de uma aplicação de Service Fabric, incluindo as verificações de estado de funcionamento para executar e políticas a anular automaticamente a atualização."
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: 
ms.assetid: a4170ac6-192e-44a8-b93d-7e39c92a347e
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 8/9/2017
ms.author: subramar
ms.openlocfilehash: f09dad590f32c10f75484bba9afb7ea60f29d81e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="application-upgrade-parameters"></a>Parâmetros da atualização da aplicação
Este artigo descreve os vários parâmetros que se aplicam durante a atualização de uma aplicação de Service Fabric do Azure. Os parâmetros de incluir o nome e versão da aplicação. São botões que controlam os tempos limite e verificações de estado de funcionamento que são aplicadas durante a atualização, e especificarem as políticas que tem de ser aplicadas quando uma atualização falha.

<br>

| Parâmetro | Descrição |
| --- | --- |
| ApplicationName |Nome da aplicação que está a ser atualizado. Exemplos: fabric: / VisualObjects, fabric: / ClusterMonitor |
| TargetApplicationTypeVersion |A versão da aplicação do tipo de que os destinos de atualização. |
| FailureAction |A ação tomada pelo Service Fabric quando a atualização falhar. A aplicação poderá ser revertida para a versão de pré-atualização (reversão) ou a atualização poderá ser interrompida no domínio de atualização atual. No caso desta última opção, o modo de atualização também é alterado para Manual. Valores permitidos são reversão e Manual. |
| HealthCheckWaitDurationSec |O tempo de espera (em segundos) após a atualização foi concluída no domínio de atualização antes de Service Fabric avalia o estado de funcionamento da aplicação. Esta duração também pode ser considerada como o tempo de que uma aplicação deve estar em execução antes de pode ser considerado em bom estado. Se a verificação de estado de funcionamento passar, continua o processo de atualização para o domínio de atualização seguinte.  Se falhar a verificação de estado de funcionamento, Service Fabric tem de aguardar durante um intervalo (a UpgradeHealthCheckInterval) antes de tentar novamente a verificação de estado de funcionamento até que o HealthCheckRetryTimeout for atingido. O valor predefinido e recomendado é de 0 segundos. |
| HealthCheckRetryTimeoutSec |A duração (em segundos) que Service Fabric continua a fazer a avaliação de estado de funcionamento antes de ser a atualização como falhado. A predefinição é 600 segundos. Esta duração inicia após o HealthCheckWaitDuration for atingido. Dentro deste HealthCheckRetryTimeout Service Fabric pode executar várias verificações de estado de funcionamento do Estado de funcionamento de aplicação. O valor predefinido é de 10 minutos e deve ser personalizado adequadamente para a sua aplicação. |
| HealthCheckStableDurationSec |A duração (em segundos) para verificar se a aplicação está estável antes de mover para o domínio de atualização seguinte ou concluir a atualização. Esta duração de espera é utilizada para impedir determinadas alterações de estado de funcionamento correto após a verificação de estado de funcionamento é efetuada. O valor predefinido é de 120 segundos e deve ser personalizado adequadamente para a sua aplicação. |
| UpgradeDomainTimeoutSec |Tempo máximo (em segundos) para atualizar um único domínio de atualização. Se este limite for atingido, a atualização interrompe e continua com base na definição de UpgradeFailureAction. O valor predefinido é nunca (infinito) e deve ser personalizadas adequadamente para a sua aplicação. |
| UpgradeTimeout |Um limite de tempo (em segundos) que aplica-se para a atualização de toda. Se este limite for atingido, a atualização interrompe e UpgradeFailureAction é acionado. O valor predefinido é nunca (infinito) e deve ser personalizadas adequadamente para a sua aplicação. |
| UpgradeHealthCheckInterval |A frequência que é verificado o estado de funcionamento. Este parâmetro for especificado na secção ClusterManager o *cluster* *manifesto*e não está especificado como parte do cmdlet atualização. O valor predefinido é de 60 segundos. |

<br>

## <a name="service-health-evaluation-during-application-upgrade"></a>Avaliação de estado de funcionamento do serviço durante a atualização da aplicação
<br>
Os critérios de avaliação do Estado de funcionamento são opcionais. Se não forem especificados critérios de avaliação de estado de funcionamento quando uma atualização é iniciado, o Service Fabric utiliza as políticas de estado de funcionamento de aplicações especificadas na ApplicationManifest.xml da instância da aplicação.

<br>

| Parâmetro | Descrição |
| --- | --- |
| ConsiderWarningAsError |Valor predefinido é falso. Processe os eventos de estado de funcionamento de aviso para a aplicação como erros ao avaliar o estado de funcionamento da aplicação durante a atualização. Por predefinição, o Service Fabric não avalia eventos de estado de funcionamento de aviso para falhas (erros), que a atualização para poder continuar, mesmo se existem eventos de aviso. |
| MaxPercentUnhealthyDeployedApplications |Valor predefinido e recomendado é 0. Especifique o número máximo de aplicações (consulte o [secção de estado de funcionamento](service-fabric-health-introduction.md)) que pode ser mau estado de funcionamento antes da aplicação é considerada em mau estado de funcionamento e falha da atualização. Este parâmetro define o estado de funcionamento da aplicação no nó e ajuda a detetar problemas durante a atualização. Normalmente, as réplicas de aplicação obter com balanceamento de carga para outro nó, que permite que a aplicação apareça em bom funcionamento, permitindo assim que a atualização continuar. Ao especificar um Estado de funcionamento MaxPercentUnhealthyDeployedApplications strict, Service Fabric pode detetar um problema com o pacote de aplicação rapidamente e ajudar a produzir um falhar a atualização. |
| MaxPercentUnhealthyServices |Valor predefinido e recomendado é 0. Especifique o número máximo de serviços na instância da aplicação que pode ser mau estado de funcionamento antes da aplicação é considerada em mau estado de funcionamento e falha da atualização. |
| MaxPercentUnhealthyPartitionsPerService |Valor predefinido e recomendado é 0. Especifique o número máximo de partições num serviço que pode ser mau estado de funcionamento antes do serviço é considerado em mau estado de funcionamento. |
| MaxPercentUnhealthyReplicasPerPartition |Valor predefinido e recomendado é 0. Especifique o número máximo de réplicas de partição que pode ser mau estado de funcionamento antes da partição é considerada em mau estado de funcionamento. |
| UpgradeReplicaSetCheckTimeout |**Sem estado**– dentro de um único domínio de atualização, tenta Certifique-se de que estão disponíveis instâncias adicionais do serviço do Service Fabric. Se a contagem de instâncias de destino é mais do que um, o Service Fabric aguarda mais do que uma instância disponível, até um valor de tempo limite máximo. Este limite de tempo é especificado através da propriedade UpgradeReplicaSetCheckTimeout. Se o tempo limite expirar, o Service Fabric continua com a atualização, independentemente do número de instâncias de serviço. Se a contagem de instâncias de destino, o Service Fabric não aguarda e imediatamente continua com a atualização. **Serviço com estado**– dentro de um único domínio de atualização, tenta Certifique-se de que o conjunto de réplicas tem um quórum de Service Fabric. Service Fabric aguarda para um quórum de estar disponível, até um valor de limite de tempo máximo (especificado pela propriedade UpgradeReplicaSetCheckTimeout). Se o tempo limite expirar, o Service Fabric continua com a atualização, independentemente de quórum. Esta definição é definido como nunca (infinito) ao implementar uma reencaminhar e segundos, 900 quando a reversão. |
| ForceRestart |Se atualizar uma configuração ou o pacote de dados sem atualizar o código do serviço, o serviço for reiniciado apenas se a propriedade ForceRestart está definida como true. Quando a atualização estiver concluída, o Service Fabric notifica o serviço que está disponível um novo pacote de configuração ou o pacote de dados. O serviço é responsável por aplicar as alterações. Se necessário, pode reiniciar o serviço próprio. |

<br>
<br>
Os critérios de MaxPercentUnhealthyServices, MaxPercentUnhealthyPartitionsPerService e MaxPercentUnhealthyReplicasPerPartition podem ser especificados por tipo de serviço para uma instância de aplicação. Definir estes parâmetros por-serviço permite a uma aplicação que contém tipos diferentes de serviços com as políticas de avaliação diferentes. Por exemplo, um tipo de serviço de gateway sem monitorização de estado pode ter um MaxPercentUnhealthyPartitionsPerService que é diferente de um tipo de serviço do motor com monitorização de estado para uma instância de aplicação em particular.

## <a name="next-steps"></a>Passos seguintes
[Atualizar a aplicação utilizando o Visual Studio](service-fabric-application-upgrade-tutorial.md) orienta-o através de uma atualização da aplicação com o Visual Studio.

[Atualizar a sua aplicação através do Powershell](service-fabric-application-upgrade-tutorial-powershell.md) orienta-o através de uma atualização da aplicação através do PowerShell.

[Atualizar a sua aplicação utilizar a CLI de recursos de infraestrutura de serviço no Linux](service-fabric-application-lifecycle-sfctl.md#upgrade-application) orienta-o através de uma atualização da aplicação ao utilizar a CLI de recursos de infraestrutura de serviço.

[Atualizar a sua aplicação utilizando o plug-in do Eclipse de recursos de infraestrutura do serviço](service-fabric-get-started-eclipse.md#upgrade-your-service-fabric-java-application)

Faça as atualizações de aplicações compatíveis aprender a utilizar [dados serialização](service-fabric-application-upgrade-data-serialization.md).

Saiba como utilizar a funcionalidade avançada ao atualizar a sua aplicação ao referir-se para [tópicos avançados](service-fabric-application-upgrade-advanced.md).

Resolva problemas comuns nas atualizações de aplicações ao referir-se os passos descritos para [resolução de problemas de atualizações de aplicação](service-fabric-application-upgrade-troubleshooting.md).

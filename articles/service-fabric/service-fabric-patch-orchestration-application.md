---
title: "Aplicação de orquestração de patch de Service Fabric do Azure | Microsoft Docs"
description: "Aplicação para automatizar a aplicação de patches de sistema operativo num cluster de Service Fabric."
services: service-fabric
documentationcenter: .net
author: novino
manager: timlt
editor: 
ms.assetid: de7dacf5-4038-434a-a265-5d0de80a9b1d
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 5/9/2017
ms.author: nachandr
ms.openlocfilehash: 13c11902e275d1023e474d717800b3a36a6b31f2
ms.sourcegitcommit: 93902ffcb7c8550dcb65a2a5e711919bd1d09df9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/09/2017
---
# <a name="patch-the-windows-operating-system-in-your-service-fabric-cluster"></a>Correção do sistema operativo Windows no seu cluster do Service Fabric

A aplicação de orquestração de patch é uma aplicação de Service Fabric do Azure que automatiza o sistema operativo num cluster de Service Fabric sem períodos de indisponibilidade a aplicação de patches.

A aplicação de orquestração de patch fornece o seguinte:

- **Instalação da atualização automática do sistema operativo**. Atualizações do sistema operativo são transferidas e instaladas automaticamente. Nós de cluster são reiniciados, conforme necessário, sem período de indisponibilidade do cluster.

- **Integração de aplicação de patches e estado de funcionamento com suporte para cluster**. Ao aplicar as atualizações, a aplicação de orquestração de patch monitoriza o estado de funcionamento de nós do cluster. Nós de cluster são um nó atualizado uma hora ou um domínio de atualização de cada vez. Se o estado de funcionamento do cluster de ficar inativo devido ao processo de aplicação de patches, a aplicação de patches está parada para impedir a aggravating o problema.

## <a name="internal-details-of-the-app"></a>Internos detalhes da aplicação

A aplicação de orquestração de patch é composta pelos subcomponentes seguintes:

- **Serviço de coordenador**: este serviço de monitorização de estado é responsável por:
    - Coordenar a tarefa de atualização do Windows em todo o cluster.
    - Armazenar o resultado das operações do Windows Update foi concluídas.
- **Serviço de agente de nó**: este serviço sem monitorização de estado é executado em todos os nós de cluster do Service Fabric. O serviço é responsável por:
    - Bootstrapping NTService de agente de nó.
    - O NTService de agente de nó de monitorização.
- **Nó agente NTService**: Windows NT este serviço é executado num nível mais elevado privilégio (sistema). Em contrapartida, o serviço de agente de nó e o serviço Coordenador de executam um privilégio de nível inferior (serviço de rede). O serviço é responsável por efetuar as seguintes tarefas de atualização do Windows em todos os nós do cluster:
    - Desativar a atualização automática do Windows no nó.
    - Transferir e instalar o Windows Update, de acordo com a política de utilizador forneceu.
    - Reiniciar o post máquina instalação do Windows Update.
    - A carregar os resultados de atualizações do Windows para o serviço de coordenador.
    - Os relatórios de estado de relatórios no caso de uma operação falhou após esgotamento todas as tentativas.

> [!NOTE]
> A aplicação de orquestração de patch utiliza o serviço de sistema do Service Fabric reparação manager para desativar ou ativar o nó e executar verificações do Estado de funcionamento. A tarefa de reparação criada pela aplicação de orquestração patch controla o progresso do Windows Update para cada nó.

## <a name="prerequisites"></a>Pré-requisitos

### <a name="enable-the-repair-manager-service-if-its-not-running-already"></a>Ativar o serviço do Gestor de reparação (se este não está em execução já)

A aplicação de orquestração de patch requer o serviço de sistema de manager reparação esteja ativada no cluster.

#### <a name="azure-clusters"></a>Clusters do Azure

Os clusters do Azure no escalão de durabilidade prata têm o serviço do Gestor de reparação ativado por predefinição. Clusters do Azure no escalão de durabilidade gold poderão ou poderão não ter o serviço do Gestor de reparação ativado, consoante quando esses clusters foram criados. Clusters do Azure no escalão bronze durabilidade, por predefinição, é necessário o serviço do Gestor de reparação ativado. Se o serviço já está ativado, pode ver funcionar na secção de serviços de sistema no Service Fabric Explorer.

##### <a name="azure-portal"></a>Portal do Azure
Pode ativar o Gestor de reparação a partir do portal do Azure no momento da configuração de cluster. Selecione **incluem o Gestor de reparação** opção em **adicionar funcionalidades** no momento da configuração de cluster.
![Imagem de ativar o Gestor de reparação a partir do portal do Azure](media/service-fabric-patch-orchestration-application/EnableRepairManager.png)

##### <a name="azure-resource-manager-template"></a>Modelo Azure Resource Manager
Em alternativa, pode utilizar o [modelo Azure Resource Manager](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm) para ativar o serviço do Gestor de reparação em clusters de Service Fabric novas e existentes. Obter o modelo para o cluster que pretende implementar. Pode utilizar os modelos de exemplo ou criar um modelo personalizado do Gestor de recursos. 

Para ativar o Gestor de reparação serviço utilizando [modelo Azure Resource Manager](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm):

1. Primeiro, verifique se o `apiversion` está definido como `2017-07-01-preview` para o `Microsoft.ServiceFabric/clusters` recursos, conforme mostrado no seguinte fragmento. Se for diferente, em seguida, tem de atualizar o `apiVersion` ao valor `2017-07-01-preview`:

    ```json
    {
        "apiVersion": "2017-07-01-preview",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        ...
    }
    ```

2. Ativar agora o serviço do Gestor de reparação adicionando o seguinte `addonFeatures` secção após o `fabricSettings` secção:

    ```json
    "fabricSettings": [
        ...      
    ],
    "addonFeatures": [
        "RepairManager"
    ],
    ```

3. Depois de atualizar o modelo de cluster com estas alterações, aplicá-las e permita que a atualização concluída. Agora, pode ver o serviço de sistema reparação manager em execução no seu cluster. É denominado `fabric:/System/RepairManagerService` na secção de serviços de sistema no Service Fabric Explorer. 

### <a name="standalone-on-premises-clusters"></a>Autónomo no local clusters

Pode utilizar o [definições de configuração do cluster do Windows autónomo](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-manifest) para ativar o serviço do Gestor de reparação no cluster do Service Fabric nova e existente.

Para ativar o serviço do Gestor de reparação:

1. Primeiro, verifique se o `apiversion` no [configurações de cluster geral](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-manifest#general-cluster-configurations) está definido como `04-2017` ou superior:

    ```json
    {
        "name": "SampleCluster",
        "clusterConfigurationVersion": "1.0.0",
        "apiVersion": "04-2017",
        ...
    }
    ```

2. Ativar agora o serviço do Gestor de reparação adicionando o seguinte `addonFeatures` secção após o `fabricSettings` secção conforme mostrado abaixo:

    ```json
    "fabricSettings": [
        ...      
    ],
    "addonFeatures": [
        "RepairManager"
    ],
    ```

3. Atualize o manifesto do cluster com estas alterações, utilizando o manifesto do cluster atualizado [criar um novo cluster](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-for-windows-server) ou [atualizar a configuração do cluster](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-upgrade-windows-server#Upgrade-the-cluster-configuration). Depois do cluster está em execução com manifesto do cluster atualizada, agora, pode ver o serviço de sistema reparação manager em execução no seu cluster, o que é chamado `fabric:/System/RepairManagerService`, em sistema de serviços de secção no Explorador do Service Fabric.

### <a name="disable-automatic-windows-update-on-all-nodes"></a>Desativar a atualização automática do Windows em todos os nós

As atualizações automáticas do Windows podem originar perda de disponibilidade porque vários nós de cluster podem reiniciar ao mesmo tempo. A aplicação de patch de orquestração, por predefinição, tenta desativar a atualização automática do Windows em cada nó de cluster. No entanto, se as definições são geridas por um administrador ou a política de grupo, recomendamos que defina a política do Windows Update para "Notificar antes de transferir" explicitamente.

## <a name="download-the-app-package"></a>Transferir o pacote de aplicação

Transferir a aplicação a [transferir ligação](https://go.microsoft.com/fwlink/P/?linkid=849590).

## <a name="configure-the-app"></a>Configurar a aplicação

O comportamento da aplicação de orquestração patch pode ser configurado para satisfazer as suas necessidades. Substitua os valores predefinidos mediante a transmissão no parâmetro de aplicação durante a criação de aplicação ou atualização. Podem ser fornecidos parâmetros de aplicação, especificando `ApplicationParameter` para o `Start-ServiceFabricApplicationUpgrade` ou `New-ServiceFabricApplication` cmdlets.

|**Parâmetro**        |**Tipo**                          | **Detalhes**|
|:-|-|-|
|MaxResultsToCache    |Longo                              | Número máximo de resultados do Windows Update, que devem ser colocados em cache. <br>Valor predefinido é 3000 partindo do princípio de: <br> -Número de nós é 20. <br> -O número de atualizações a acontecer num nó por mês é cinco. <br> -Número de resultados por operação pode ter 10. <br> -Devem ser armazenados resultados durante os últimos três meses. |
|TaskApprovalPolicy   |Enum <br> {NodeWise, UpgradeDomainWise}                          |TaskApprovalPolicy indica a política que está a ser utilizado pelo serviço de coordenador para instalar as atualizações do Windows em todos os nós de cluster do Service Fabric.<br>                         Valores permitidos são: <br>                                                           <b>NodeWise</b>. Windows Update está instalado um nó de cada vez. <br>                                                           <b>UpgradeDomainWise</b>. Windows Update está instalado um domínio de atualização de cada vez. (No máximo, podem aceder todos os nós que pertencem a um domínio de atualização para o Windows Update.)
|LogsDiskQuotaInMB   |Longo  <br> (Predefinição: 1024)               |Tamanho máximo das patch orchestration aplicação regista em MB, que pode ser persistente localmente em nós.
| WUQuery               | Cadeia<br>(Predefinição: "IsInstalled = 0")                | Consulta para obter atualizações do Windows. Para obter mais informações, consulte [WuQuery.](https://msdn.microsoft.com/library/windows/desktop/aa386526(v=vs.85).aspx)
| InstallWindowsOSOnlyUpdates | bool <br> (predefinição: VERDADEIRO)                 | Este sinalizador permite que as atualizações do sistema operativo Windows ser instalada.            |
| WUOperationTimeOutInMinutes | Int <br>(Predefinição: 90).                   | Especifica o tempo limite em nenhuma operação de atualização do Windows (pesquisa ou transferir ou instalar). Se não for possível concluir a operação dentro do tempo limite especificado, foi abortada.       |
| WURescheduleCount     | Int <br> (Predefinição: 5).                  | O número máximo de vezes que o serviço reschedules do Windows update no caso de uma operação falha de forma permanente.          |
| WURescheduleTimeInMinutes | Int <br>(Predefinição: 30). | O intervalo no qual o serviço reschedules o Windows update no caso de falha persistir. |
| WUFrequency           | Cadeia separada por vírgulas (predefinição: "Semanal, quarta-feira, 7:00:00")     | A frequência para a instalação do Windows Update. Os valores de formato e possíveis são: <br>-Mensais, DD, hh: mm:, por exemplo, mensalmente, 5, 12: 22:32. <br> -Semanal, dia, hh: mm:, de exemplo, semanalmente, Terça-feira, 12:22:32.  <br> -Diárias, hh: mm:, por exemplo, diariamente, 12:22:32.  <br> -None indica que o Windows Update não deve ser efetuada.  <br><br> Tenha em atenção que todas as horas são em UTC.|
| AcceptWindowsUpdateEula | bool <br>(Predefinição: VERDADEIRO) | Ao definir este sinalizador, a aplicação aceita o contrato de licença de utilizador final para o Windows Update em nome do proprietário da máquina.              |

> [!TIP]
> Se pretender que o Windows Update para ocorrer imediatamente, defina `WUFrequency` relativamente ao momento da implementação de aplicação. Por exemplo, suponha que tem um cluster de teste de cinco nós e planeie a implementar a aplicação em aproximadamente 5:00 PM UTC. Se parte do princípio de que a atualização da aplicação ou implementação demora 30 minutos no máximo, defina o WUFrequency como "Diariamente, 17:30:00."

## <a name="deploy-the-app"></a>Implementar a aplicação

1. Conclua todos os passos de pré-requisitos para preparar o cluster.
2. Implemente a aplicação de orquestração de patch como qualquer outra aplicação de Service Fabric. Pode implementar a aplicação utilizando o PowerShell. Siga os passos no [implementar e remover aplicações utilizando o PowerShell](https://docs.microsoft.com/azure/service-fabric/service-fabric-deploy-remove-applications).
3. Para configurar a aplicação no momento da implementação, passar o `ApplicationParamater` para o `New-ServiceFabricApplication` cmdlet. Para sua comodidade, fornecemos o script Deploy.ps1 juntamente com a aplicação. Para utilizar o script:

    - Ligar a um cluster do Service Fabric utilizando `Connect-ServiceFabricCluster`.
    - Execute o script de PowerShell Deploy.ps1 com apropriados `ApplicationParameter` valor.

> [!NOTE]
> Mantenha o script e a pasta de aplicação PatchOrchestrationApplication no mesmo diretório.

## <a name="upgrade-the-app"></a>Atualizar a aplicação

Para atualizar uma aplicação de orquestração de patch existente utilizando o PowerShell, siga os passos no [atualização da aplicação de Service Fabric com o PowerShell](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-upgrade-tutorial-powershell).

## <a name="remove-the-app"></a>Remova a aplicação

Para remover a aplicação, siga os passos no [implementar e remover aplicações utilizando o PowerShell](https://docs.microsoft.com/azure/service-fabric/service-fabric-deploy-remove-applications).

Para sua comodidade, fornecemos o script Undeploy.ps1 juntamente com a aplicação. Para utilizar o script:

  - Ligar a um cluster do Service Fabric utilizando ```Connect-ServiceFabricCluster```.

  - Execute o script de PowerShell Undeploy.ps1.

> [!NOTE]
> Mantenha o script e a pasta de aplicação PatchOrchestrationApplication no mesmo diretório.

## <a name="view-the-windows-update-results"></a>Ver os resultados do Windows Update

A aplicação de orquestração de patch expõe as APIs REST para apresentar os resultados históricos ao utilizador. Um exemplo do resultado JSON:
```json
[
  {
    "NodeName": "_stg1vm_1",
    "WindowsUpdateOperationResults": [
      {
        "OperationResult": 0,
        "NodeName": "_stg1vm_1",
        "OperationTime": "2017-05-21T11:46:52.1953713Z",
        "UpdateDetails": [
          {
            "UpdateId": "7392acaf-6a85-427c-8a8d-058c25beb0d6",
            "Title": "Cumulative Security Update for Internet Explorer 11 for Windows Server 2012 R2 (KB3185319)",
            "Description": "A security issue has been identified in a Microsoft software product that could affect your system. You can help protect your system by installing this update from Microsoft. For a complete listing of the issues that are included in this update, see the associated Microsoft Knowledge Base article. After you install this update, you may have to restart your system.",
            "ResultCode": 0
          }
        ],
        "OperationType": 1,
        "WindowsUpdateQuery": "IsInstalled=0",
        "WindowsUpdateFrequency": "Daily,10:00:00",
        "RebootRequired": false
      }
    ]
  },
  ...
]
```

Os campos de JSON são descritos abaixo.

Campo | Valores | Detalhes
-- | -- | --
OperationResult | 0 - foi concluída com êxito<br> 1 - foi concluída com êxito com erros<br> 2 - falha<br> 3 - abortada<br> 4 - abortado com tempo limite | Indica o resultado da operação global (geralmente que envolve a instalação de atualizações de um ou mais).
resultCode | Mesmo que OperationResult | Este campo indica o resultado da operação de instalação para a atualização individual.
OperationType | 1 - instalação<br> 0 - procurar e transferir.| A instalação é a única OperationType que deverá ser apresentada nos resultados por predefinição.
WindowsUpdateQuery | Predefinição é "IsInstalled = 0" |Windows update a consulta que foi utilizada para procurar atualizações. Para obter mais informações, consulte [WuQuery.](https://msdn.microsoft.com/library/windows/desktop/aa386526(v=vs.85).aspx)
RebootRequired | VERDADEIRO - foi necessário reiniciar o computador<br> FALSO - não foi necessário reiniciar o computador | Indica se reiniciar o computador foi necessário para concluir a instalação das atualizações.

Se não existe nenhuma atualização está agendada ainda, o resultado JSON está vazio.

Inicie sessão no cluster de consulta do Windows Update resultados. Em seguida, determinar o endereço de réplica para o principal do serviço de coordenador e atingiu o URL do browser: http://&lt;IP de RÉPLICA&gt;:&lt;ApplicationPort&gt;/PatchOrchestrationApplication/v1 / GetWindowsUpdateResults.

O ponto final REST para o serviço de coordenador possui uma porta dinâmica. Para verificar o URL exato, consulte o Service Fabric Explorer. Por exemplo, os resultados estão disponíveis em `http://10.0.0.7:20000/PatchOrchestrationApplication/v1/GetWindowsUpdateResults`.

![Imagem do ponto final REST](media/service-fabric-patch-orchestration-application/Rest_Endpoint.png)


Se o proxy reverso estiver ativado no cluster, pode aceder ao URL de fora do cluster, bem como.
O ponto final que tem de ser acessos é http://&lt;SERVERURL&gt;:&lt;REVERSEPROXYPORT&gt;/PatchOrchestrationApplication/CoordinatorService/v1/GetWindowsUpdateResults.

Para ativar o proxy inverso no cluster, siga os passos no [proxy no Azure Service Fabric inverso](https://docs.microsoft.com/azure/service-fabric/service-fabric-reverseproxy). 

> 
> [!WARNING]
> Depois do proxy reverso estiver configurado, todos os serviços micro no cluster que expõem um ponto final de HTTP são endereçáveis a partir de fora do cluster.

## <a name="diagnosticshealth-events"></a>Eventos de diagnóstico/estado de funcionamento

### <a name="diagnostic-logs"></a>Registos de diagnósticos

Os registos de aplicação de orquestração de patch são recolhidos como parte dos registos de tempo de execução do Service Fabric.

No caso de pretender capturar registos através de diagnóstico ferramenta por pipeline à sua escolha. Aplicação de orquestração de patch utiliza abaixo fornecedor fixo IDs de eventos através de registo [eventsource](https://docs.microsoft.com/dotnet/api/system.diagnostics.tracing.eventsource?view=netframework-4.5.1)

- e39b723c-590c-4090-abb0-11e3e6616346
- fc0028ff-bfdc-499f-80dc-ed922c52c5e9
- 24afa313-0d3b-4c7c-b485-1047fd964b60
- 05dc046c-60e9-4ef7-965e-91660adffa68

### <a name="health-reports"></a>Relatórios de estado de funcionamento

A aplicação de orquestração de correção também publica relatórios de estado de funcionamento contra o serviço de coordenador ou o serviço de agente de nó nos seguintes casos:

#### <a name="a-windows-update-operation-failed"></a>Uma falha na operação de atualização do Windows

Se uma operação de atualização do Windows falhar um nó, é gerado um relatório de estado de funcionamento contra o serviço de agente de nó. Detalhes do relatório de estado de funcionamento contém o nome de nó problemáticas.

Aplicação de patches esteja concluída com êxito no nó problemático, o relatório é automaticamente eliminado.

#### <a name="the-node-agent-ntservice-is-down"></a>O NTService de agente de nó está inativo

Se o NTService de agente de nó para baixo num nó, é gerado um relatório de estado de funcionamento do nível de aviso contra o serviço de agente de nó.

#### <a name="the-repair-manager-service-is-not-enabled"></a>O serviço do Gestor de reparação não está ativado

Se o serviço do Gestor de reparação não foi encontrado no cluster, é gerado um relatório de estado de funcionamento do nível de aviso para o serviço de coordenador.

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

Q. **Por que vejo o meu cluster num Estado de erro quando a aplicação de orquestração de correção está em execução?**

A. Durante o processo de instalação, a aplicação de orquestração de patch desativa ou reinicia nós, que podem resultar temporariamente no estado de funcionamento do cluster ficar.

Com base na política para a aplicação, o ou um nó pode aceder durante uma operação de aplicação de patches *ou* um domínio de atualização completo pode aceder em simultâneo para baixo.

No final da instalação do Windows Update, os nós são reenabled após o reinício.

No exemplo seguinte, o cluster correu para um Estado de erro temporariamente porque foram dois nós para baixo e a política de MaxPercentageUnhealthNodes foi violada. O erro é temporário até que a aplicação de patches operação está em curso.

![Imagem do cluster mau estado de funcionamento](media/service-fabric-patch-orchestration-application/MaxPercentage_causing_unhealthy_cluster.png)

Se o problema persistir, consulte a secção de resolução de problemas.

Q. **Aplicação de orquestração de patch se encontra num Estado de aviso**

A. Certifique-se de um relatório de estado de funcionamento publicado contra a aplicação estiver a causa raiz. Normalmente, o aviso contém detalhes do problema. Se o problema é transitório, a aplicação deverá auto-recuperar deste Estado.

Q. **O que posso fazer se a minha cluster está danificada e necessário efetuar uma atualização do sistema de operativo urgente?**

A. A aplicação de orquestração de patch não instala atualizações enquanto o cluster está danificado. Tente traga o seu cluster para um bom estado de funcionamento para desbloquear o fluxo de trabalho de aplicação de orquestração patch.

Q. **Por que motivo aplicação de patches em clusters de tomar tempo para executar?**

A. O tempo necessário para a aplicação de orquestração de patch principalmente está dependente os seguintes fatores:

- A política do serviço de coordenador. 
  - A política predefinida, `NodeWise`, resulta numa aplicação de patches apenas um nó de cada vez. Especialmente no caso dos clusters maiores, recomendamos que utilize o `UpgradeDomainWise` política para alcançar a aplicação de patches mais rápidos entre clusters.
- O número de atualizações disponíveis para transferência e instalação. 
- O tempo médio necessário para transferir e instalar uma atualização, que não deve ter mais duas horas.
- O desempenho da VM e rede de largura de banda.

Q. **Por que razão vejo algumas atualizações nos resultados do Windows Update obtidos através da API REST, mas não sob o histórico do Windows Update no computador?**

A. Algumas atualizações do produto tem de ser verificado no respetivo histórico de atualização respetivos/patch. Por exemplo, atualizações do Windows Defender não aparecer no histórico do Windows Update no Windows Server 2016.

## <a name="disclaimers"></a>Exclusão de Responsabilidade

- A aplicação de orquestração de patch aceita o contrato de licença de utilizador final do Windows Update em nome do utilizador. Opcionalmente, a definição pode ser desativada na configuração da aplicação.

- A aplicação de orquestração de patch recolhe telemetria para controlar a utilização e desempenho. A telemetria da aplicação segue a definição da definição de telemetria o runtime Service Fabric (que está ativada por predefinição).

## <a name="troubleshooting"></a>Resolução de problemas

### <a name="a-node-is-not-coming-back-to-up-state"></a>Um nó é não regressam ao estado de segurança

**O nó pode estar bloqueado no estado de Desativação porque**:

Uma verificação de segurança está pendente. Para resolver esta situação, certifique-se de que estão disponíveis suficientes nós em bom estado.

**O nó pode estar bloqueado no estado desativado porque**:

- O nó foi desativado manualmente.
- O nó foi desativado devido a uma tarefa de infraestrutura do Azure em curso.
- O nó foi desativado temporariamente pela aplicação de orquestração do patch para corrigir o nó.

**O nó pode estar bloqueado no estado de indisponibilidade porque**:

- O nó foi colocado em estado de indisponibilidade manualmente.
- O nó está sob um reinício (o que poderá ser acionado pela aplicação de orquestração do patch).
- O nó não está disponível devido a uma VM defeituosa ou problemas de conectividade de rede ou de máquina.

### <a name="updates-were-skipped-on-some-nodes"></a>As atualizações foram ignoradas em alguns nós

A aplicação de orquestração de patch tenta instalar uma atualização do Windows, de acordo com a política rescheduling. O serviço tenta recuperar o nó e ignorar a atualização de acordo com as políticas de aplicação.

Nesse caso, é gerado um relatório de estado de funcionamento do nível de aviso contra o serviço de agente de nó. O resultado para o Windows Update também contém o motivo possível da falha.

### <a name="the-health-of-the-cluster-goes-to-error-while-the-update-installs"></a>O estado de funcionamento do cluster entra em erro durante a instalação da atualização

Pode colocar uma atualização do Windows defeituosa para baixo do Estado de funcionamento de uma aplicação ou um cluster num determinado nó ou domínio de atualização. A aplicação de orquestração de patch discontinues qualquer operação subsequente do Windows Update, até que o cluster está em bom estado de novo.

Um administrador tem intervene e determine o motivo pelo qual a aplicação ou o cluster ter ficado danificado devido a atualização do Windows.

## <a name="release-notes"></a>Notas de Versão

### <a name="version-110"></a>Versão 1.1.0
- Versão pública

### <a name="version-111"></a>Versão 1.1.1
- Corrigido um erro no SetupEntryPoint de NodeAgentService que impediu a instalação do NodeAgentNTService.

### <a name="version-120-latest"></a>Versão 1.2.0 (mais recentes)

- Correções de erros em torno do sistema de reiniciar o fluxo de trabalho.
- Correção de erro na criação de tarefas RM devido ao estado de funcionamento do que a verificação durante a preparação de tarefas de reparação não foi acontecer conforme esperado.
- Alterar o modo de arranque de serviço do windows POANodeSvc da implementação como auto atrasado.

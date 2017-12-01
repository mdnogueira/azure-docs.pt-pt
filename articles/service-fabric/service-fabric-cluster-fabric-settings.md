---
title: "Alterar as definições de cluster do Service Fabric do Azure | Microsoft Docs"
description: "Este artigo descreve as definições de recursos de infraestrutura e as políticas de atualização de recursos de infraestrutura que pode personalizar."
services: service-fabric
documentationcenter: .net
author: chackdan
manager: timlt
editor: 
ms.assetid: 7ced36bf-bd3f-474f-a03a-6ebdbc9677e2
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/15/2017
ms.author: chackdan
ms.openlocfilehash: 076d4d95db21f0a1c1500ae7766392547a441d1b
ms.sourcegitcommit: 5a6e943718a8d2bc5babea3cd624c0557ab67bd5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/01/2017
---
# <a name="customize-service-fabric-cluster-settings-and-fabric-upgrade-policy"></a>Personalizar as definições de cluster do Service Fabric e política de atualização do Fabric
Este documento indica como personalizar as várias definições de recursos de infraestrutura e os recursos de infraestrutura atualizar a política para o cluster do Service Fabric. Pode personalizá-los através do [portal do Azure](https://portal.azure.com) ou através de um modelo Azure Resource Manager.

> [!NOTE]
> Nem todas as definições estão disponíveis no portal. No caso de uma definição listada abaixo não está disponível através do portal de personalizá-lo a utilizar um modelo Azure Resource Manager.
> 

## <a name="customize-cluster-settings-using-resource-manager-templates"></a>Personalizar as definições de cluster através de modelos do Resource Manager
Os passos abaixo mostram como adicionar uma nova definição *MaxDiskQuotaInMB* para o *diagnóstico* secção.

1. Aceda a https://resources.azure.com
2. Navegue até à sua subscrição, expandindo **subscrições** -> **grupos de recursos** -> **Microsoft.ServiceFabric**  ->   **\<o nome do Cluster >**
3. No canto superior direito, selecione **leitura/escrita.**
4. Selecione **editar** e atualizar o `fabricSettings` elemento JSON e adicione um novo elemento de:

```
      {
        "name": "Diagnostics",
        "parameters": [
          {
            "name": "MaxDiskQuotaInMB",
            "value": "65536"
          }
        ]
      }
```

Segue-se uma lista de recursos de infraestrutura, as definições que pode personalizar, organizados por secção.

### <a name="section-name-diagnostics"></a>Secção nome: diagnóstico
| **Parâmetro** | **Valores permitidos** | **Política de atualização** | **Documentação de orientação ou breve descrição** |
| --- | --- | --- | --- |
| ConsumerInstances |Cadeia | Dinâmica |A lista de instâncias de consumidor DCA. |
| ProducerInstances |Cadeia | Dinâmica |A lista de instâncias de produtor DCA. |
| AppEtwTraceDeletionAgeInDays |Int, a predefinição é 3 | Dinâmica |Número de dias após o qual podemos eliminar os ficheiros ETL antigos que contém os rastreios ETW de aplicação. |
| AppDiagnosticStoreAccessRequiresImpersonation |Booleana, a predefinição é verdadeiro | Dinâmica |Se pretende ou não representação é necessária para aceder ao diagnóstico armazena em nome da aplicação. |
| MaxDiskQuotaInMB |Int, a predefinição é 65536 | Dinâmica |Quota de disco nos ficheiros de registo do MB Windows Fabric. |
| DiskFullSafetySpaceInMB |Int, a predefinição é 1024 | Dinâmica |Espaço em disco restante em MB para proteger contra a utilização por DCA. |
| ApplicationLogsFormatVersion |Int, a predefinição é 0 | Dinâmica |Formato de registo de versão para a aplicação. Os valores suportados são 0 e 1. Versão 1 inclui mais campos no registo de eventos ETW à versão 0. |
| ClusterId |Cadeia | Dinâmica |O id exclusivo do cluster. Este é gerado quando o cluster for criado. |
| Ativar telemetria |Booleana, a predefinição é verdadeiro | Dinâmica |Isto vai ativar ou desativar a telemetria. |
| EnableCircularTraceSession |Booleana, a predefinição é false | Estático |Sinalizador indica se devem ser utilizadas sessões de rastreio circular. |

### <a name="section-name-traceetw"></a>Nome de secção: Rastreio/Etw
| **Parâmetro** | **Valores permitidos** | **Política de atualização** | **Documentação de orientação ou breve descrição** |
| --- | --- | --- | --- |
| Nível |Int, a predefinição é 4 | Dinâmica |Rastreio etw nível pode demorar valores 1, 2, 3, 4. Para ser suportado é necessário manter o nível de rastreio em 4 |

### <a name="section-name-performancecounterlocalstore"></a>Secção nome: PerformanceCounterLocalStore
| **Parâmetro** | **Valores permitidos** | **Política de atualização** | **Documentação de orientação ou breve descrição** |
| --- | --- | --- | --- |
| IsEnabled |Booleana, a predefinição é verdadeiro | Dinâmica |Sinalizador indica se a recolha do contador de desempenho no nó local está ativada. |
| SamplingIntervalInSeconds |Int, a predefinição é 60 | Dinâmica |Intervalo de amostragem para contadores de desempenho estão a ser recolhidos. |
| Contadores |Cadeia | Dinâmica |Lista separada por vírgulas de contadores de desempenho para recolher. |
| MaxCounterBinaryFileSizeInMB |Int, a predefinição é 1 | Dinâmica |Tamanho máximo (em MB) para cada ficheiro binário do contador de desempenho. |
| NewCounterBinaryFileCreationIntervalInMinutes |Int, a predefinição é 10 | Dinâmica |Intervalo máximo (em segundos) após o qual é criado um novo ficheiro binário de contador de desempenho. |

### <a name="section-name-setup"></a>Secção nome: a configuração
| **Parâmetro** | **Valores permitidos** | **Política de atualização** | **Documentação de orientação ou breve descrição** |
| --- | --- | --- | --- |
| A variável FabricDataRoot |Cadeia | Não Permitido |Diretório de raiz de dados do Service Fabric. Predefinido para o Azure é d:\svcfab |
| FabricLogRoot |Cadeia | Não Permitido |Diretório de raiz do registo de recursos de infraestrutura do serviço. Este é onde os registos de SF e rastreios são colocados. |
| ServiceRunAsAccountName |Cadeia | Não Permitido |O nome da conta para executar o serviço de anfitrião de recursos de infraestrutura. |
| SkipFirewallConfiguration |Booleana, a predefinição é false | Não Permitido |Especifica se as definições de firewall tem de ser definido pelo sistema ou não. Isto aplica-se apenas se estiver a utilizar a firewall do windows. Se estiver a utilizar firewalls de terceiros, em seguida, tem de abrir as portas para o sistema e as aplicações utilizem |
|NodesToBeRemoved|cadeia, a predefinição é ""| Dinâmica |Os nós que devem ser removidos como parte da atualização da configuração. (Apenas para implementações autónomas)|
|ContainerNetworkSetup|booleana, a predefinição é FALSE| Estático |Se pretende configurar uma rede de contentor.|
|ContainerNetworkName|cadeia, a predefinição é L""| Estático |O nome de rede a utilizar quando configurar uma rede de contentor.|

### <a name="section-name-transactionalreplicator"></a>Secção nome: TransactionalReplicator
| **Parâmetro** | **Valores permitidos** | **Política de atualização** | **Documentação de orientação ou breve descrição** |
| --- | --- | --- | --- |
| MaxCopyQueueSize |Uint, a predefinição é 16384 | Estático |Este é o máximo valor define o tamanho inicial para a fila que mantém as operações de replicação. Tenha em atenção que tem de ser uma potência de 2. Se o crescimentos durante o tempo de execução para esta operação de tamanho de fila serão limitadas entre os de replicadores primários e secundários. |
| BatchAcknowledgementInterval | Tempo em segundos, a predefinição é 0.015 | Estático | Especifique timespan em segundos. Determina a quantidade de tempo que o replicador aguarda depois de receber uma operação antes de devolver uma confirmação. Outras operações recebidas durante este período de tempo terão as respetivas confirmações enviadas novamente numa única mensagem -> reduzir o tráfego de rede, mas reduzindo potencialmente o débito do replicador. |
| MaxReplicationMessageSize |Uint, a predefinição é 52428800 | Estático | Tamanho da mensagem máximo de operações de replicação. A predefinição é 50MB. |
| ReplicatorAddress |cadeia, a predefinição é "localhost:0" | Estático | O ponto final na forma de uma cadeia, 'IP:Port', que é utilizado pelo Windows Fabric replicador para estabelecer ligações com outras réplicas para operações de envio/receção. |
| InitialPrimaryReplicationQueueSize |Uint, a predefinição é 64 | Estático |Este valor define o tamanho inicial para a fila que mantém as operações de replicação principal. Tenha em atenção que tem de ser uma potência de 2.|
| MaxPrimaryReplicationQueueSize |Uint, a predefinição é 8192 | Estático |Este é o número máximo de operações que pode existir na fila de replicação primária. Tenha em atenção que tem de ser uma potência de 2. |
| MaxPrimaryReplicationQueueMemorySize |Uint, a predefinição é 0 | Estático |Este é o valor máximo da fila de replicação primária em bytes. |
| InitialSecondaryReplicationQueueSize |Uint, a predefinição é 64 | Estático |Este valor define o tamanho inicial para a fila que mantém as operações de replicação no secundário. Tenha em atenção que tem de ser uma potência de 2. |
| MaxSecondaryReplicationQueueSize |Uint, a predefinição é 16384 | Estático |Este é o número máximo de operações que pode existir na fila de replicação secundário. Tenha em atenção que tem de ser uma potência de 2. |
| MaxSecondaryReplicationQueueMemorySize |Uint, a predefinição é 0 | Estático |Este é o valor máximo da fila de replicação secundária em bytes. |
| SecondaryClearAcknowledgedOperations |Booleana, a predefinição é false | Estático |Booleano que controla se as operações nos replicador secundária estão desmarcadas uma vez são confirmadas principal (libertado no disco). Definições de esta opção para verdadeiro pode resultar em leituras de disco adicional no principal novo, durante a deteção de segurança de réplicas após uma ativação pós-falha. |
| MaxMetadataSizeInKB |Int, a predefinição é 4 |Não Permitido|Tamanho máximo dos metadados de sequência do registo. |
| MaxRecordSizeInKB |Uint, a predefinição é 1024 |Não Permitido| Tamanho máximo de um registo de fluxo. |
| CheckpointThresholdInMB |Int, predefinido é 50 |Estático|Um ponto de verificação será iniciado quando a utilização do registo excede este valor. |
| MaxAccumulatedBackupLogSizeInMB |Int, a predefinição é 800 |Estático|Máx. acumulados tamanho (em MB) de cópia de segurança registos numa cadeia de cópia de segurança de registo especificado. Um pedidos de cópia de segurança incremental falharão se a cópia de segurança incremental irá gerar um registo de cópia de segurança que faria com que os registos de cópia de segurança acumulados desde a relevante cópia de segurança completa ser maior do que este tamanho. Nestes casos, é pedido ao utilizador para efetuar uma cópia de segurança completa. |
| MaxWriteQueueDepthInKB |Int, a predefinição é 0 |Não Permitido| Int para máximo escrever profundidade de fila do registador de núcleos pode utilizar conforme especificado em quilobytes para o registo que está associado esta réplica. Este valor é o número máximo de bytes que podem ser pendentes durante as atualizações de registo principal. Pode ser 0 para o registo de núcleos para um valor adequado de computação ou um múltiplo de 4. |
| SharedLogId |Cadeia |Não Permitido|Identificador de registo partilhado. Este é um guid e deve ser exclusivo para cada registo partilhado. |
| SharedLogPath |Cadeia |Não Permitido|Caminho para o registo partilhado. Se este valor está vazio, em seguida, é utilizado o registo partilhado predefinido. |
| SlowApiMonitoringDuration |Tempo em segundos, a predefinição é 300 |Estático| Especificar a duração para api antes de que desencadeou o evento de estado de funcionamento de aviso.|
| MinLogSizeInMB |Int, a predefinição é 0 |Estático|Tamanho mínimo do registo transacional. O registo não será permitido para truncar para um tamanho inferior esta definição. 0 indica que o replicador determinará o tamanho do registo mínimo, de acordo com as outras definições. Aumento deste valor aumenta a possibilidade de fazer cópias parciais e cópias de segurança incrementais desde possibilidades de registos de registo relevantes fossem truncados é lowered. |

### <a name="section-name-fabricclient"></a>Secção nome: FabricClient
| **Parâmetro** | **Valores permitidos** | **Política de atualização** | **Documentação de orientação ou breve descrição** |
| --- | --- | --- | --- |
| NodeAddresses |cadeia, a predefinição é "" |Estático|Uma coleção de endereços (cadeias de ligação) em nós diferentes que podem ser utilizadas para comunicar com o serviço de nomenclatura. Inicialmente o cliente liga-se selecionar um dos endereços aleatoriamente. Se é fornecida mais do que uma cadeia de ligação e uma ligação falha devido a um erro de tempo limite ou a comunicação os comutadores de cliente para utilizar o seguinte endereço sequencialmente. Ver o endereço do serviço de nomenclatura Repita secção para obter detalhes sobre a semântica de tentativas. |
| ConnectionInitializationTimeout |Tempo em segundos, a predefinição é 2 |Dinâmica|Especifique timespan em segundos. Intervalo de tempo limite de ligação para cada cliente tempo tenta abrir uma ligação para o gateway. |
| PartitionLocationCacheLimit |Int, a predefinição é 100000 |Estático|Número de partições em cache para a resolução de serviço (definida como 0 para nenhum limite). |
| ServiceChangePollInterval |Tempo em segundos, a predefinição é 120 |Dinâmica|Especifique timespan em segundos. Altera o intervalo entre inquéritos consecutivos para o serviço do cliente para o gateway para chamadas de retorno de notificações de alteração de serviço registado. |
| KeepAliveIntervalInSeconds |Int, a predefinição é 20 |Estático|O intervalo no qual o transporte de FabricClient envia mensagens keep-alive ao gateway. Para 0; keepAlive está desativada. Tem de ser um valor positivo. |
| HealthOperationTimeout |Tempo em segundos, a predefinição é 120 |Dinâmica|Especifique timespan em segundos. O tempo limite de uma mensagem de relatório enviado para o Gestor de estado de funcionamento. |
| HealthReportSendInterval |Tempo em segundos, a predefinição é 30 |Dinâmica|Especifique timespan em segundos. O intervalo de relatórios componente envia relatórios de estado de funcionamento acumulado ao Gestor de estado de funcionamento. |
| HealthReportRetrySendInterval |Tempo em segundos, a predefinição é 30 |Dinâmica|Especifique timespan em segundos. O intervalo de relatórios componente envia novamente relatórios de estado de funcionamento acumulado ao Gestor de estado de funcionamento. |
| RetryBackoffInterval |Tempo em segundos, a predefinição é 3 |Dinâmica|Especifique timespan em segundos. O intervalo término antes de repetir a operação. |
| MaxFileSenderThreads |Uint, a predefinição é 10 |Estático|O número máximo de ficheiros que são transferidos em paralelo. |

### <a name="section-name-common"></a>Secção nome: comuns
| **Parâmetro** | **Valores permitidos** | **Política de atualização** | **Documentação de orientação ou breve descrição** |
| --- | --- | --- | --- |
| PerfMonitorInterval |Tempo em segundos, a predefinição é 1 |Dinâmica|Especifique timespan em segundos. Intervalo de monitorização de desempenho. Definição para o valor negativo ou igual a 0 desativa a monitorização. |

### <a name="section-name-healthmanager"></a>Secção nome: HealthManager
| **Parâmetro** | **Valores permitidos** | **Política de atualização** | **Documentação de orientação ou breve descrição** |
| --- | --- | --- | --- |
| EnableApplicationTypeHealthEvaluation |Booleana, a predefinição é false |Estático|Política de avaliação do Estado de funcionamento do cluster: Ativar por avaliação de estado de funcionamento do tipo de aplicação. |

### <a name="section-name-nodedomainids"></a>Secção nome: NodeDomainIds
| **Parâmetro** | **Valores permitidos** | **Política de atualização** | **Documentação de orientação ou breve descrição** |
| --- | --- | --- | --- |
| UpgradeDomainId |cadeia, a predefinição é "" |Estático|Descreve um nó pertence o domínio de atualização. |
| PropertyGroup |NodeFaultDomainIdCollection |Estático|Descreve os domínios de falhas que pertence um nó. O domínio de falhas é definido através de um URI que descreve a localização do nó no Centro de dados.  Os URIs de domínio de falhas estão no formato DF: / DF/seguido de um segmento de caminho URI.|

### <a name="section-name-nodeproperties"></a>Secção nome: NodeProperties
| **Parâmetro** | **Valores permitidos** | **Política de atualização** | **Documentação de orientação ou breve descrição** |
| --- | --- | --- | --- |
| PropertyGroup |NodePropertyCollectionMap |Estático|Uma coleção de pares chave-valor de cadeia para propriedades de nó. |

### <a name="section-name-nodecapacities"></a>Secção nome: NodeCapacities
| **Parâmetro** | **Valores permitidos** | **Política de atualização** | **Documentação de orientação ou breve descrição** |
| --- | --- | --- | --- |
| PropertyGroup |NodeCapacityCollectionMap |Estático|Uma coleção de nó as capacidades para as métricas de diferentes. |

### <a name="section-name-fabricnode"></a>Secção nome: FabricNode
| **Parâmetro** | **Valores permitidos** | **Política de atualização** | **Documentação de orientação ou breve descrição** |
| --- | --- | --- | --- |
| StateTraceInterval |Tempo em segundos, a predefinição é 300 |Estático|Especifique timespan em segundos. O intervalo para o estado de nó em cada nó e cópia de segurança nós no FM/FMM de rastreio. |
| StartApplicationPortRange |Int, a predefinição é 0 |Estático|Iniciar as portas de aplicações geridas pelo subsistema de alojamento. Necessário se EndpointFilteringEnabled é verdadeiro na alojamento. |
| EndApplicationPortRange |Int, a predefinição é 0 |Estático|Fim (não inclusivo) de portas a aplicação gerida pelo subsistema de alojamento. Necessário se EndpointFilteringEnabled é verdadeiro na alojamento. |
| ClusterX509StoreName |cadeia, a predefinição é "Os meus" |Dinâmica|Nome do arquivo de certificados x. 509 que contém o certificado de cluster para proteger a comunicação entre clusters. |
| ClusterX509FindType |cadeia, a predefinição é "FindByThumbprint" |Dinâmica|Indica como procurar o certificado de cluster no arquivo de especificado pelos valores ClusterX509StoreName suportado: "FindByThumbprint"; "FindBySubjectName" com "FindBySubjectName"; Quando existem vários correspondências; o com a expiração furthest é utilizada. |
| ClusterX509FindValue |cadeia, a predefinição é "" |Dinâmica|Valor de filtro de pesquisa utilizado para localizar o certificado de cluster. |
| ClusterX509FindValueSecondary |cadeia, a predefinição é "" |Dinâmica|Valor de filtro de pesquisa utilizado para localizar o certificado de cluster. |
| ServerAuthX509StoreName |cadeia, a predefinição é "Os meus" |Dinâmica|Nome do arquivo de certificados x. 509 que contém o certificado de servidor para o serviço de entrée. |
| ServerAuthX509FindType |cadeia, a predefinição é "FindByThumbprint" |Dinâmica|Indica como procurar o certificado de servidor no arquivo de especificado pelo valor suportado ServerAuthX509StoreName: FindByThumbprint; FindBySubjectName. |
| ServerAuthX509FindValue |cadeia, a predefinição é "" |Dinâmica|Valor de filtro de pesquisa utilizado para localizar o certificado de servidor. |
| ServerAuthX509FindValueSecondary |cadeia, a predefinição é "" |Dinâmica|Valor de filtro de pesquisa utilizado para localizar o certificado de servidor. |
| ClientAuthX509StoreName |cadeia, a predefinição é "Os meus" |Dinâmica|Nome do arquivo de certificados x. 509 que contém o certificado para a função de administrador predefinida FabricClient. |
| ClientAuthX509FindType |cadeia, a predefinição é "FindByThumbprint" |Dinâmica|Indica como procurar o certificado no arquivo de especificado pelo valor suportado ClientAuthX509StoreName: FindByThumbprint; FindBySubjectName. |
| ClientAuthX509FindValue |cadeia, a predefinição é "" | Dinâmica|Valor de filtro de pesquisa utilizado para localizar o certificado para a função de administrador predefinida FabricClient. |
| ClientAuthX509FindValueSecondary |cadeia, a predefinição é "" |Dinâmica|Valor de filtro de pesquisa utilizado para localizar o certificado para a função de administrador predefinida FabricClient. |
| UserRoleClientX509StoreName |cadeia, a predefinição é "Os meus" |Dinâmica|Nome do arquivo de certificados x. 509 que contém o certificado para a função de utilizador predefinido FabricClient. |
| UserRoleClientX509FindType |cadeia, a predefinição é "FindByThumbprint" |Dinâmica|Indica como procurar o certificado no arquivo de especificado pelo valor suportado UserRoleClientX509StoreName: FindByThumbprint; FindBySubjectName. |
| UserRoleClientX509FindValue |cadeia, a predefinição é "" |Dinâmica|Valor de filtro de pesquisa utilizado para localizar o certificado para a função de utilizador predefinido FabricClient. |
| UserRoleClientX509FindValueSecondary |cadeia, a predefinição é "" |Dinâmica|Valor de filtro de pesquisa utilizado para localizar o certificado para a função de utilizador predefinido FabricClient. |

### <a name="section-name-paas"></a>Secção nome: Paas
| **Parâmetro** | **Valores permitidos** | **Política de atualização** | **Documentação de orientação ou breve descrição** |
| --- | --- | --- | --- |
| ClusterId |cadeia, a predefinição é "" |Não Permitido|Utilizado por recursos de infraestrutura para proteção da configuração do arquivo de certificados X509. |

### <a name="section-name-fabrichost"></a>Secção nome: FabricHost
| **Parâmetro** | **Valores permitidos** | **Política de atualização** | **Documentação de orientação ou breve descrição** |
| --- | --- | --- | --- |
| StopTimeout |Tempo em segundos, a predefinição é 300 |Dinâmica|Especifique timespan em segundos. O tempo limite para ativação do serviço alojado; desativação e a atualização. |
| StartTimeout |Tempo em segundos, a predefinição é 300 |Dinâmica|Especifique timespan em segundos. Tempo limite de arranque fabricactivationmanager. |
| ActivationRetryBackoffInterval |Tempo em segundos, a predefinição é 5 |Dinâmica|Especifique timespan em segundos. Intervalo de término em cada caso de falha de ativação; em cada caso de falha de ativação contínua que o sistema irá tentar a ativação de até o MaxActivationFailureCount. O intervalo entre tentativas em cada tente é um produto de falha de ativação contínua e o intervalo de término de ativação. |
| ActivationMaxRetryInterval |Tempo em segundos, a predefinição é 300 |Dinâmica|Especifique timespan em segundos. Intervalo de repetição de máx. para a ativação. Em cada caso de falha contínuo no intervalo entre tentativas é calculado como mín. (ActivationMaxRetryInterval; Número de falhas de contínua * ActivationRetryBackoffInterval). |
| ActivationMaxFailureCount |Int, a predefinição é 10 |Dinâmica|Esta é a contagem máxima para a qual sistema será novamente a ativação falha antes de desistir. |
| EnableServiceFabricAutomaticUpdates |Booleana, a predefinição é false |Dinâmica|Isto é permitir atualização automática de recursos de infraestrutura através do Windows Update. |
| EnableServiceFabricBaseUpgrade |Booleana, a predefinição é false |Dinâmica|Isto é ativar a atualização de base para o servidor. |
| EnableRestartManagement |Booleana, a predefinição é false |Dinâmica|Isto é permitir o reinício do servidor. |


### <a name="section-name-failovermanager"></a>Secção nome: FailoverManager
| **Parâmetro** | **Valores permitidos** | **Política de atualização** | **Documentação de orientação ou breve descrição** |
| --- | --- | --- | --- |
| UserReplicaRestartWaitDuration |Tempo em segundos, a predefinição é 60.0 * 30 |Dinâmica|Especifique timespan em segundos. Quando uma réplica persistente fica inativo; Windows Fabric tem de aguardar durante este período de tempo para a réplica voltar atrás cópias de segurança antes de criar novas réplicas de substituição (que iriam exigir uma cópia do Estado de). |
| QuorumLossWaitDuration |Tempo em segundos, a predefinição é MaxValue |Dinâmica|Especifique timespan em segundos. Esta é a duração máxima para o qual é permitir que uma partição para estar em estado de perda de quórum. Se a partição ainda está em perda de quórum após esta duração; a partição é recuperou de perda de quórum, ponderando as réplicas baixo como perdido. Tenha em atenção que isto pode potencialmente implicar a perda de dados. |
| UserStandByReplicaKeepDuration |Tempo em segundos, a predefinição é 3600.0 * 24 * 7 |Dinâmica|Especifique timespan em segundos. Quando uma réplica persistente voltar atrás de um Estado de indisponibilidade; Pode já ter foi substituído. Este temporizador determina quanto a FM irá manter a réplica em modo de espera antes de eliminá-lo. |
| UserMaxStandByReplicaCount |Int, a predefinição é 1 |Dinâmica|O número máximo predefinido de réplicas do modo de espera que o sistema mantém para serviços de utilizador. |
| ExpectedClusterSize|Int, a predefinição é 1|Dinâmica|Quando o cluster é inicialmente iniciado; o FM espera que isto nós muitos relatórios próprios cópias de segurança antes de iniciar a colocação de outros serviços; incluindo os serviços do sistema, como a atribuição de nomes.  Aumento deste valor aumenta o tempo que demora um cluster de arranque; mas impede os nós precoce de se tornar sobrecarregado e também a move adicional que irá ser necessário como mais nós fique online.  Este valor, geralmente, deve ser definido como algumas pequena fração do tamanho do cluster inicial. |
|ClusterPauseThreshold|Int, a predefinição é 1|Dinâmica|Se o número de nós no sistema ficarem abaixo este valor, em seguida, Colocação; carregar balanceamento; e parar a ativação pós-falha. |
|TargetReplicaSetSize|Int, a predefinição é 7|Não Permitido|Este é o número de destino de réplicas de FM que vai manter os recursos de infraestrutura do Windows.  Um número mais alto resulta numa maior fiabilidade dos dados FM; com um compromisso do desempenho pequeno. |
|MinReplicaSetSize|Int, a predefinição é 3|Não Permitido|Este é o tamanho de conjunto de réplicas mínima para o FM.  Se o número de réplicas do Active Directory FM descerem abaixo este valor; o FM irão rejeitar as alterações para o cluster até, pelo menos, o número mínimo de réplicas é recuperado |
|ReplicaRestartWaitDuration|TimeSpan, a predefinição é Common::TimeSpan::FromSeconds(60.0 * 30)|Não Permitido|Especifique timespan em segundos. Este é o ReplicaRestartWaitDuration para o FMService |
|StandByReplicaKeepDuration|TimeSpan, a predefinição é Common::TimeSpan::FromSeconds(3600.0 * 24 * 7)|Não Permitido|Especifique timespan em segundos. Este é o StandByReplicaKeepDuration para o FMService |
|PlacementConstraints|cadeia, a predefinição é L""|Não Permitido|As restrições de posicionamento para as réplicas de Gestor de ativação pós-falha |
|ExpectedNodeFabricUpgradeDuration|TimeSpan, a predefinição é Common::TimeSpan::FromSeconds(60.0 * 30)|Dinâmica|Especifique timespan em segundos. Esta é a duração esperada para um nó a ser atualizado durante a atualização do Windows Fabric. |
|ExpectedReplicaUpgradeDuration|TimeSpan, a predefinição é Common::TimeSpan::FromSeconds(60.0 * 30)|Dinâmica|Especifique timespan em segundos. Esta é a duração esperada para todas as réplicas ser actualizado num nó durante a atualização da aplicação. |
|ExpectedNodeDeactivationDuration|TimeSpan, a predefinição é Common::TimeSpan::FromSeconds(60.0 * 30)|Dinâmica|Especifique timespan em segundos. Esta é a duração esperada para um nó concluir a desativação de no. |
|IsSingletonReplicaMoveAllowedDuringUpgrade|booleana, a predefinição é verdadeiro|Dinâmica|Se definido como true; as réplicas com um tamanho de conjunto de réplicas de destino de 1 serão permitidas para mover durante a atualização. |
|ReconfigurationTimeLimit|TimeSpan, a predefinição é Common::TimeSpan::FromSeconds(300)|Dinâmica|Especifique timespan em segundos. O limite de tempo de reconfiguração; após o qual será iniciado a um relatório de estado de funcionamento de aviso |
|BuildReplicaTimeLimit|TimeSpan, a predefinição é Common::TimeSpan::FromSeconds(3600)|Dinâmica|Especifique timespan em segundos. O limite de tempo para a criação de uma réplica de monitorização de estado após o qual será iniciado a um relatório de estado de funcionamento de aviso |
|CreateInstanceTimeLimit|TimeSpan, a predefinição é Common::TimeSpan::FromSeconds(300)|Dinâmica|Especifique timespan em segundos. O limite de tempo para criar uma instância sem monitorização de estado; após o qual será iniciado a um relatório de estado de funcionamento de aviso |
|PlacementTimeLimit|TimeSpan, a predefinição é Common::TimeSpan::FromSeconds(600)|Dinâmica|Especifique timespan em segundos. O limite de tempo para atingir a contagem de réplicas de destino; após o qual será iniciado a um relatório de estado de funcionamento de aviso |

### <a name="section-name-namingservice"></a>Secção nome: NamingService
| **Parâmetro** | **Valores permitidos** | **Política de atualização** | **Documentação de orientação ou breve descrição** |
| --- | --- | --- | --- |
| TargetReplicaSetSize |Int, a predefinição é 7 |Não Permitido|Define o número de réplica para cada partição do arquivo do serviço de nomenclatura. Aumento do número de conjuntos de réplicas aumenta o nível de fiabilidade para obter as informações no arquivo do serviço de nomes; diminuir a alteração que as informações serão perdidas devido a falhas de nó um custo de aumento de carga em recursos de infraestrutura do Windows e a quantidade de tempo que demora a efetuar atualizações para os dados de nomenclatura.|
|MinReplicaSetSize | Int, a predefinição é 3 |Não Permitido| O número mínimo de réplicas de nomenclatura serviço necessário para escrever na conclusão de uma atualização. Se existirem menos réplicas mais ativos no sistema o sistema de fiabilidade nega atualizações para o arquivo do serviço de nomenclatura até as réplicas são restauradas. Este valor nunca deve ser superior a TargetReplicaSetSize. |
|ReplicaRestartWaitDuration | Tempo em segundos, a predefinição é (60.0 * 30)|Não Permitido| Especifique timespan em segundos. Quando uma réplica Naming Service fica inativo; Este temporizador é iniciado.  Quando este expirar o FM começará substituir as réplicas que estão em baixo (não ainda considera perdido). |
|QuorumLossWaitDuration | Tempo em segundos, a predefinição é MaxValue |Não Permitido| Especifique timespan em segundos. Quando um serviço de nomenclatura obtém em perda de quórum Este temporizador é iniciado.  Quando este expirar o FM considerará as réplicas como perdido; baixo e tentar recuperar o quórum. Não Isto pode resultar na perda de dados. |
|StandByReplicaKeepDuration | Tempo em segundos, a predefinição é 3600.0 * 2 |Não Permitido| Especifique timespan em segundos. Quando uma réplica Naming Service voltar atrás de um Estado de indisponibilidade; Pode já ter foi substituído.  Este temporizador determina quanto a FM irá manter a réplica em modo de espera antes de eliminá-lo. |
|PlacementConstraints | cadeia, a predefinição é "" |Não Permitido| Restrição de posicionamento para o serviço de nomenclatura. |
|ServiceDescriptionCacheLimit | Int, a predefinição é 0 |Estático| O número máximo de entradas mantido na cache de descrição de serviço LRU, o serviço de arquivo de nomenclatura (definido como 0 para nenhum limite). |
|RepairInterval | Tempo em segundos, a predefinição é 5 |Estático| Especifique timespan em segundos. Intervalo no qual será iniciada a nomenclatura reparação de inconsistência entre o proprietário de autoridade e o proprietário do nome. |
|MaxNamingServiceHealthReports | Int, a predefinição é 10 |Dinâmica|O número máximo de operações lentas que armazenam Naming service relatórios mau estado de funcionamento de uma só vez. Se for 0; todas as operações lentas são enviadas. |
| MaxMessageSize |Int, a predefinição é 4\*1024\*1024 |Estático|O tamanho máximo de mensagem para a comunicação de nó de cliente ao utilizar a atribuição de nomes. LINHA ataques alleviation; valor predefinido é 4MB. |
| MaxFileOperationTimeout |Tempo em segundos, a predefinição é 30 |Dinâmica|Especifique timespan em segundos. Tempo limite máximo permitido para a operação de serviço de arquivo de ficheiros. Especificar o tempo limite de pedidos serão rejeitados. |
| Maxoperationtimeout da |Tempo em segundos, a predefinição é 600 |Dinâmica|Especifique timespan em segundos. Tempo limite máximo permitido para operações de cliente. Especificar o tempo limite de pedidos serão rejeitados. |
| MaxClientConnections |Int, a predefinição é 1000 |Dinâmica|O máximo permitido de número de ligações de cliente por gateway. |
| ServiceNotificationTimeout |Tempo em segundos, a predefinição é 30 |Dinâmica|Especifique timespan em segundos. O tempo limite utilizado quando a entrega de notificações de serviço para o cliente. |
| MaxOutstandingNotificationsPerClient |Int, a predefinição é 1000 |Dinâmica|O número máximo de pendentes notificações antes de um registo de cliente é forçado a fechar o gateway. |
| MaxIndexedEmptyPartitions |Int, a predefinição é 1000 |Dinâmica|O número máximo de partições vazia que permanecerá indexado na cache de notificação para sincronizar os clientes de restabelecer a ligação. Quaisquer partições vazias acima este número serão removidas do índice por pesquisa versão ordem ascendente. Restabelecer a ligação de clientes pode ainda sincronizar e receção de atualizações de partição vazia em falta mas o protocolo de sincronização torna-se mais dispendioso. |
| GatewayServiceDescriptionCacheLimit |Int, a predefinição é 0 |Estático|O número máximo de entradas mantido na cache de descrição de serviço LRU no Gateway de nomenclatura (definido como 0 para nenhum limite). |
| PartitionCount |Int, a predefinição é 3 |Não Permitido|O número de partições do serviço de nomenclatura de armazena a ser criado. Cada partição possui uma chave de partição única que corresponde ao respetivo índice; por isso, as chaves de partição [0; PartitionCount) existe. Definir o aumento do número de aumentos de partições Naming Service escala a que o serviço de nomenclatura pode efetuar ao diminuir a quantidade média de dados retidos pela qualquer réplica de cópia de segurança; Custo uma maior utilização de recursos (desde o PartitionCount * ReplicaSetSize réplicas de serviço têm de ser mantidas).|

### <a name="section-name-runas"></a>Secção nome: RunAs
| **Parâmetro** | **Valores permitidos** | **Política de atualização** | **Documentação de orientação ou breve descrição** |
| --- | --- | --- | --- |
| RunAsAccountName |cadeia, a predefinição é "" |Dinâmica|Indica o nome da conta RunAs. Isto só é necessário para a conta "DomainUser" ou "ManagedServiceAccount" tipo. Os valores válidos são "domínio \ utilizador" ou "user@domain". |
|RunAsAccountType|cadeia, a predefinição é "" |Dinâmica|Indica o tipo de conta RunAs. Isto é necessário para os valores válidos de secção RunAs são "DomainUser/NetworkService/ManagedServiceAccount/LocalSystem".|
|RunAsPassword|cadeia, a predefinição é "" |Dinâmica|Indica a palavra-passe da conta de RunAs. Isto apenas é necessário para o tipo de conta "DomainUser". |

### <a name="section-name-runasfabric"></a>Secção nome: RunAs_Fabric
| **Parâmetro** | **Valores permitidos** | **Política de atualização** | **Documentação de orientação ou breve descrição** |
| --- | --- | --- | --- |
| RunAsAccountName |cadeia, a predefinição é "" |Dinâmica|Indica o nome da conta RunAs. Isto só é necessário para a conta "DomainUser" ou "ManagedServiceAccount" tipo. Os valores válidos são "domínio \ utilizador" ou "user@domain". |
|RunAsAccountType|cadeia, a predefinição é "" |Dinâmica|Indica o tipo de conta RunAs. Isto é necessário para os valores válidos de secção RunAs são "Utilizador_local/DomainUser/NetworkService/ManagedServiceAccount/LocalSystem". |
|RunAsPassword|cadeia, a predefinição é "" |Dinâmica|Indica a palavra-passe da conta de RunAs. Isto apenas é necessário para o tipo de conta "DomainUser". |

### <a name="section-name-runashttpgateway"></a>Secção nome: RunAs_HttpGateway
| **Parâmetro** | **Valores permitidos** | **Política de atualização** | **Documentação de orientação ou breve descrição** |
| --- | --- | --- | --- |
| RunAsAccountName |cadeia, a predefinição é "" |Dinâmica|Indica o nome da conta RunAs. Isto só é necessário para a conta "DomainUser" ou "ManagedServiceAccount" tipo. Os valores válidos são "domínio \ utilizador" ou "user@domain". |
|RunAsAccountType|cadeia, a predefinição é "" |Dinâmica|Indica o tipo de conta RunAs. Isto é necessário para os valores válidos de secção RunAs são "Utilizador_local/DomainUser/NetworkService/ManagedServiceAccount/LocalSystem". |
|RunAsPassword|cadeia, a predefinição é "" |Dinâmica|Indica a palavra-passe da conta de RunAs. Isto apenas é necessário para o tipo de conta "DomainUser". |

### <a name="section-name-runasdca"></a>Secção nome: RunAs_DCA
| **Parâmetro** | **Valores permitidos** | **Política de atualização** | **Documentação de orientação ou breve descrição** |
| --- | --- | --- | --- |
| RunAsAccountName |cadeia, a predefinição é "" |Dinâmica|Indica o nome da conta RunAs. Isto só é necessário para a conta "DomainUser" ou "ManagedServiceAccount" tipo. Os valores válidos são "domínio \ utilizador" ou "user@domain". |
|RunAsAccountType|cadeia, a predefinição é "" |Dinâmica|Indica o tipo de conta RunAs. Isto é necessário para os valores válidos de secção RunAs são "Utilizador_local/DomainUser/NetworkService/ManagedServiceAccount/LocalSystem". |
|RunAsPassword|cadeia, a predefinição é "" |Dinâmica|Indica a palavra-passe da conta de RunAs. Isto apenas é necessário para o tipo de conta "DomainUser". |

### <a name="section-name-httpgateway"></a>Secção nome: HttpGateway
| **Parâmetro** | **Valores permitidos** | **Política de atualização** | **Documentação de orientação ou breve descrição** |
| --- | --- | --- | --- |
|IsEnabled|Booleana, a predefinição é false |Estático| Ativa/desativa o HttpGateway. HttpGateway está desativada por predefinição. |
|ActiveListeners |Uint, predefinido é 50 |Estático| Número de leituras por oposição a publicar para a fila de servidor de http. Isto controla o número de pedidos em simultâneo que podem ser satisfeitos pelo HttpGateway. |
|MaxEntityBodySize |Uint, a predefinição é 4194304 |Dinâmica|Fornece o tamanho máximo de corpo que pode esperar de um pedido de http. Valor predefinido é 4MB. Httpgateway falhará um pedido, se tiver um corpo de tamanho > Este valor. Tamanho de segmento de leitura mínimo é 4096 bytes. Para que este tem de ser > = 4096. |
|HttpGatewayHealthReportSendInterval |Tempo em segundos, a predefinição é 30 |Estático|Especifique timespan em segundos. O intervalo no qual o Gateway de Http envia o estado de funcionamento acumulado relatórios para o Gestor de estado de funcionamento. |

### <a name="section-name-ktllogger"></a>Secção nome: KtlLogger
| **Parâmetro** | **Valores permitidos** | **Política de atualização** | **Documentação de orientação ou breve descrição** |
| --- | --- | --- | --- |
|AutomaticMemoryConfiguration |Int, a predefinição é 1 |Dinâmica|Sinalizador que indica se as definições de memória devem ser automaticamente e dinamicamente configuradas. Se zero, em seguida, as definições de configuração de memória são utilizadas diretamente e não mudam com base nas condições de sistema. Se um, em seguida, as definições de memória são automaticamente configuradas e podem ser alterados com base nas condições de sistema. |
|WriteBufferMemoryPoolMinimumInKB |Int, a predefinição é 8388608 |Dinâmica|O número de KB inicialmente alocar para o conjunto de memória de memória intermédia de escrita. Utilize 0 para indicar sem limite predefinido deve ser consistente com SharedLogSizeInMB abaixo. |
|WriteBufferMemoryPoolMaximumInKB | Int, a predefinição é 0 |Dinâmica|O número de KB para permitir que o conjunto de memória de memória intermédia de escrita a crescer até. Utilize 0 para indicar sem limite. |
|MaximumDestagingWriteOutstandingInKB | Int, a predefinição é 0 |Dinâmica|O número de KB para permitir o início de sessão partilhado ao produzir antes do registo dedicado. Utilize 0 para indicar sem limite.
|SharedLogPath |cadeia, a predefinição é "" |Estático|Caminho e nome de ficheiro para a localização para colocar o contentor de registo partilhado. Utilize "" para utilizar o caminho predefinido na raiz de dados de recursos de infraestrutura. |
|SharedLogId |cadeia, a predefinição é "" |Estático|Guid exclusivo para o contentor de registo partilhado. Utilize "" se utilizar o caminho predefinido na raiz de dados de recursos de infraestrutura. |
|SharedLogSizeInMB |Int, a predefinição é 8192 |Estático|O número de MB para alocar o contentor de registo partilhado. |

### <a name="section-name-applicationgatewayhttp"></a>Nome de secção: ApplicationGateway/Http
| **Parâmetro** | **Valores permitidos** | **Política de atualização** | **Documentação de orientação ou breve descrição** |
| --- | --- | --- | --- |
|IsEnabled |Booleana, a predefinição é false |Estático| Ativa/desativa o HttpApplicationGateway. HttpApplicationGateway está desativada por predefinição e esta configuração tem de ser definida para ativá-la. |
|NumberOfParallelOperations | Uint, a predefinição é 5000 |Estático|Número de leituras por oposição a publicar para a fila de servidor de http. Isto controla o número de pedidos em simultâneo que podem ser satisfeitos pelo HttpGateway. |
|DefaultHttpRequestTimeout |Tempo em segundos. predefinição é 120 |Dinâmica|Especifique timespan em segundos.  Fornece o tempo de limite de pedido predefinido para os pedidos de http que está a ser processados no gateway de aplicação de http. |
|ResolveServiceBackoffInterval |Tempo em segundos, a predefinição é 5 |Dinâmica|Especifique timespan em segundos.  Oferece resolver o intervalo predefinido término antes de repetir uma falha na operação de serviço. |
|BodyChunkSize |Uint, a predefinição é 16384 |Dinâmica| Fornece o tamanho para o segmento em bytes utilizados para ler o corpo. |
|GatewayAuthCredentialType |cadeia, a predefinição é "None" |Estático| Indica o tipo de credenciais de segurança a utilizar durante os http aplicação gateway endpoint os valores válidos são "nenhum / X 509. |
|GatewayX509CertificateStoreName |cadeia, a predefinição é "Os meus" |Dinâmica| Nome do arquivo de certificados x. 509 que contém o certificado para o gateway de aplicação de http. |
|GatewayX509CertificateFindType |cadeia, a predefinição é "FindByThumbprint" |Dinâmica| Indica como procurar o certificado no arquivo de especificado pelo valor suportado GatewayX509CertificateStoreName: FindByThumbprint; FindBySubjectName. |
|GatewayX509CertificateFindValue | cadeia, a predefinição é "" |Dinâmica| Valor de filtro de pesquisa utilizado para localizar o certificado de gateway de aplicação de http. Este certificado é configurado no ponto final de https e também pode ser utilizado para verificar a identidade da aplicação se for necessário pelos serviços. FindValue é consultado primeiro; e se que não existe FindValueSecondary é pesquisado. |
|GatewayX509CertificateFindValueSecondary | cadeia, a predefinição é "" |Dinâmica|Valor de filtro de pesquisa utilizado para localizar o certificado de gateway de aplicação de http. Este certificado é configurado no ponto final de https e também pode ser utilizado para verificar a identidade da aplicação se for necessário pelos serviços. FindValue é consultado primeiro; e se que não existe FindValueSecondary é pesquisado.|
|HttpRequestConnectTimeout|TimeSpan, a predefinição é Common::TimeSpan::FromSeconds(5)|Dinâmica|Especifique timespan em segundos.  Fornece o tempo limite de ligar para os pedidos de http que está a ser enviados do gateway de aplicação de http.  |
|RemoveServiceResponseHeaders|cadeia, a predefinição é L "data; Servidor"|Estático|Vírgula semiestruturados / separados por vírgulas lista de cabeçalhos de resposta será removido da resposta do serviço; antes do reencaminhamento-lo para o cliente. Se isto estiver definido como uma cadeia vazia; passar todos os cabeçalhos devolvidos pelo serviço como-é. revertidos Não substituir a data e o servidor |
|ApplicationCertificateValidationPolicy|cadeia, a predefinição é L "None"|Estático| ApplicationCertificateValidationPolicy: Nenhum: não validar o certificado de servidor; concluída com êxito o pedido. ServiceCertificateThumbprints: Consulte a configuração ServiceCertificateThumbprints para obter a lista de valores separados por vírgulas de thumbprints dos certificados remotos que pode confiar do proxy inverso. ServiceCommonNameAndIssuer: Consulte a configuração ServiceCommonNameAndIssuer para o thumbprint de nome e o emissor de requerente dos certificados remotos que pode confiar do proxy inverso. |
|ServiceCertificateThumbprints|cadeia, a predefinição é L""|Dinâmica| |
|CrlCheckingFlag|uint, a predefinição é 0x40000000 |Dinâmica| Sinalizadores de validação da cadeia de certificados de aplicações/serviços; Por exemplo, 0x10000000 de verificação de CRL CERT_CHAIN_REVOCATION_CHECK_END_CERT 0x20000000 CERT_CHAIN_REVOCATION_CHECK_CHAIN 0x40000000 CERT_CHAIN_REVOCATION_CHECK_CHAIN_EXCLUDE_ROOT 0x80000000 CERT_CHAIN_REVOCATION_CHECK_CACHE_ONLY definição a 0 Desativa a CRL a verificar se a obter uma lista completa de valores suportados é documentada dwFlags de CertGetCertificateChain: http://msdn.microsoft.com/library/windows/desktop/aa376078 (v=vs.85).aspx  |
|IgnoreCrlOfflineError|booleana, a predefinição é verdadeiro|Dinâmica|Indica se deve ignorar erro offline de CRL para a verificação de certificado de aplicações/serviços. |
|SecureOnlyMode|booleana, a predefinição é FALSE|Dinâmica| SecureOnlyMode: true: Proxy inverso só irá reencaminhar para os serviços que publicar pontos finais segurados. FALSO: Proxy inverso pode reencaminhar pedidos para os pontos finais secure/não segura.  |
|ForwardClientCertificate|booleana, a predefinição é FALSE|Dinâmica| |

### <a name="section-name-applicationgatewayhttpservicecommonnameandissuer"></a>Nome de secção: ApplicationGateway/Http/ServiceCommonNameAndIssuer
| **Parâmetro** | **Valores permitidos** | **Política de atualização** | **Documentação de orientação ou breve descrição** |
| --- | --- | --- | --- |
|PropertyGroup|X509NameMap, a predefinição é nenhum|Dinâmica|  |

### <a name="section-name-management"></a>Secção nome: gestão
| **Parâmetro** | **Valores permitidos** | **Política de atualização** | **Documentação de orientação ou breve descrição** |
| --- | --- | --- | --- |
| ImageStoreConnectionString |SecureString |Estático|Cadeia de ligação para a raiz para o arquivo de imagens. |
| ImageStoreMinimumTransferBPS | Int, a predefinição é 1024 |Dinâmica|A velocidade de transferência mínimo entre o cluster e o arquivo de imagens. Este valor é utilizado para determinar o tempo limite ao aceder o arquivo de imagens externo. Altere este valor apenas se a latência entre o cluster e o arquivo de imagens é elevada aguarde mais algum tempo para o cluster transferir a partir do arquivo de imagens externo. |
|AzureStorageMaxWorkerThreads | Int, a predefinição é 25 |Dinâmica|O número máximo de threads de trabalho em paralelo. |
|AzureStorageMaxConnections | Int, a predefinição é 5000 |Dinâmica|O número máximo de ligações simultâneas do armazenamento do azure. |
|AzureStorageOperationTimeout | Tempo em segundos, a predefinição é 6000 |Dinâmica|Especifique timespan em segundos. Tempo limite xstore conclusão da operação. |
|ImageCachingEnabled | Booleana, a predefinição é verdadeiro |Estático|Esta configuração permite-nos ativar ou desativar a colocação em cache. |
|DisableChecksumValidation | Booleana, a predefinição é false |Estático| Esta configuração permite-nos ativar ou desativar a validação de soma de verificação durante o aprovisionamento de aplicações. |
|DisableServerSideCopy | Booleana, a predefinição é false |Estático|Esta configuração ativa ou desativa o lado do servidor de cópia do pacote de aplicação em ImageStore durante o aprovisionamento de aplicações. |

### <a name="section-name-healthmanagerclusterhealthpolicy"></a>Nome de secção: HealthManager/ClusterHealthPolicy
| **Parâmetro** | **Valores permitidos** | **Política de atualização** | **Documentação de orientação ou breve descrição** |
| --- | --- | --- | --- |
| ConsiderWarningAsError |Booleana, a predefinição é false |Estático|Política de avaliação do Estado de funcionamento do cluster: avisos são tratados como erros. |
| MaxPercentUnhealthyNodes | Int, a predefinição é 0 |Estático|Política de avaliação do Estado de funcionamento do cluster: percentagem de máxima de nós mau estado de funcionamento permitido para o cluster ser bom estado de funcionamento. |
| MaxPercentUnhealthyApplications | Int, a predefinição é 0 |Estático|Política de avaliação do Estado de funcionamento do cluster: percentagem de máxima de mau estado de funcionamento aplicações permitidas para o cluster ser bom estado de funcionamento. |

### <a name="section-name-healthmanagerclusterupgradehealthpolicy"></a>Nome de secção: HealthManager/ClusterUpgradeHealthPolicy
| **Parâmetro** | **Valores permitidos** | **Política de atualização** | **Documentação de orientação ou breve descrição** |
| --- | --- | --- | --- |
|MaxPercentDeltaUnhealthyNodes|Int, a predefinição é 10|Estático|Política de avaliação do Estado de funcionamento de atualização do cluster: percentagem de máxima de nós de mau estado de funcionamento de diferenças permitido para o cluster ser bom estado de funcionamento |
|MaxPercentUpgradeDomainDeltaUnhealthyNodes|Int, a predefinição é 15|Estático|Política de avaliação do Estado de funcionamento de atualização do cluster: percentagem de máxima de diferenças de nós mau estado de funcionamento de um domínio de atualização permitido para o cluster ser bom estado de funcionamento |

### <a name="section-name-faultanalysisservice"></a>Secção nome: FaultAnalysisService
| **Parâmetro** | **Valores permitidos** | **Política de atualização** | **Documentação de orientação ou breve descrição** |
| --- | --- | --- | --- |
| TargetReplicaSetSize |Int, a predefinição é 0 |Estático|NOT_PLATFORM_UNIX_START TargetReplicaSetSize para FaultAnalysisService. |
| MinReplicaSetSize |Int, a predefinição é 0 |Estático|MinReplicaSetSize para FaultAnalysisService. |
| ReplicaRestartWaitDuration |Tempo em segundos, a predefinição é 60 minutos|Estático|Especifique timespan em segundos. ReplicaRestartWaitDuration para FaultAnalysisService. |
| QuorumLossWaitDuration | Tempo em segundos, a predefinição é MaxValue |Estático|Especifique timespan em segundos. QuorumLossWaitDuration para FaultAnalysisService. |
| StandByReplicaKeepDuration| Tempo em segundos, a predefinição é (60*24*7) minutos |Estático|Especifique timespan em segundos. StandByReplicaKeepDuration para FaultAnalysisService. |
| PlacementConstraints | cadeia, a predefinição é ""|Estático| PlacementConstraints para FaultAnalysisService. |
| StoredActionCleanupIntervalInSeconds | Int, a predefinição é 3600 |Estático|Esta é a frequência o arquivo irá ser limpa.  Apenas as ações num Estado terminal; e que foi concluída, pelo menos, CompletedActionKeepDurationInSeconds há será removido. |
| CompletedActionKeepDurationInSeconds | Int, a predefinição é 604800 |Estático| Isto é aproximadamente quanto a manter as ações que estão num Estado terminal.  Isto também depende da StoredActionCleanupIntervalInSeconds; uma vez que o trabalho a limpeza só é feito desse intervalo. 604800 é de 7 dias. |
| StoredChaosEventCleanupIntervalInSeconds | Int, a predefinição é 3600 |Estático|Esta é a frequência o arquivo irá ser auditado para limpeza; Se o número de eventos é mais do que 30000; a limpeza irá iniciar. |
|DataLossCheckWaitDurationInSeconds|Int, a predefinição é 25|Estático|A quantidade total de tempo; em segundos; Se o sistema irá aguardar perda de dados ocorrer.  Isto é utilizado internamente quando a api de StartPartitionDataLossAsync() é chamado. |
|DataLossCheckPollIntervalInSeconds|int, a predefinição é 5|Estático|Este é o tempo entre as verificações que do sistema executa ao aguardar a perda de dados ocorrer.  O número de vezes que o número de perda de dados será verificado por iteração interna é DataLossCheckWaitDurationInSeconds este. |
|ReplicaDropWaitDurationInSeconds|int, a predefinição é 600|Estático|Este parâmetro é utilizado quando a perda de dados api é chamado.  Controla quanto o sistema irá aguardar que uma réplica para obter removido depois de remover réplica internamente é invocada no mesmo. |

### <a name="section-name-filestoreservice"></a>Secção nome: FileStoreService
| **Parâmetro** | **Valores permitidos** | **Política de atualização** | **Documentação de orientação ou breve descrição** |
| --- | --- | --- | --- |
| NamingOperationTimeout |Tempo em segundos, a predefinição é 60 |Dinâmica|Especifique timespan em segundos. O tempo limite para efetuar a operação de nomenclatura. |
| QueryOperationTimeout | Tempo em segundos, a predefinição é 60 |Dinâmica|Especifique timespan em segundos. O tempo limite para efetuar a operação de consulta. |
| MaxCopyOperationThreads | Uint, a predefinição é 0 |Dinâmica| Pode copiar o número máximo de ficheiros paralelos que secundário do primário. '0' = = número de núcleos. |
| MaxFileOperationThreads | Uint, a predefinição é 100 |Estático| O número máximo de threads paralelas autorizado a efetuar FileOperations (Copy/Move) no principal. '0' = = número de núcleos. |
| MaxStoreOperations | Uint, a predefinição é 4096 |Estático|O número máximo de operações da transação de arquivo paralelo permitido no principal. '0' = = número de núcleos. |
| MaxRequestProcessingThreads | Uint, a predefinição é 200 |Estático|O número máximo de threads paralelas permitida para processar pedidos no principal. '0' = = número de núcleos. |
| MaxSecondaryFileCopyFailureThreshold | Uint, a predefinição é 25|Dinâmica|O número máximo de repetições de cópia de ficheiros em secundário antes de desistir. |
| AnonymousAccessEnabled | Booleana, a predefinição é verdadeiro |Estático|Ativar/desativar o acesso anónimo para as partilhas de FileStoreService. |
| PrimaryAccountType | cadeia, a predefinição é "" |Estático|O principal AccountType do principal a ACL de FileStoreService partilha. |
| PrimaryAccountUserName | cadeia, a predefinição é "" |Estático|A conta primária nome de utilizador de principal para a ACL de FileStoreService partilha. |
| PrimaryAccountUserPassword | SecureString, a predefinição é em branco |Estático|A palavra-passe de conta principal do principal a ACL de FileStoreService partilha. |
| PrimaryAccountNTLMPasswordSecret | SecureString, a predefinição é em branco |Estático| O segredo de palavra-passe que utilizado como base para a mesma palavra-passe gerada ao utilizar a autenticação NTLM. |
| PrimaryAccountNTLMX509StoreLocation | cadeia, a predefinição é "LocalMachine"|Estático| A localização do arquivo do X509 certificado utilizado para gerar HMAC no PrimaryAccountNTLMPasswordSecret quando utilizar a autenticação NTLM. |
| PrimaryAccountNTLMX509StoreName | cadeia, a predefinição é "MY"|Estático| O nome de arquivo de X509 certificado utilizado para gerar HMAC no PrimaryAccountNTLMPasswordSecret quando utilizar a autenticação NTLM. |
| PrimaryAccountNTLMX509Thumbprint | cadeia, a predefinição é ""|Estático|O thumbprint de X509 do certificado utilizado para gerar HMAC no PrimaryAccountNTLMPasswordSecret quando utilizar a autenticação NTLM. |
| SecondaryAccountType | cadeia, a predefinição é ""|Estático| O elemento secundário AccountType do principal a ACL de FileStoreService partilha. |
| SecondaryAccountUserName | cadeia, a predefinição é ""| Estático|A conta secundária do nome de utilizador de principal para a ACL de FileStoreService partilha. |
| SecondaryAccountUserPassword | SecureString, a predefinição é em branco |Estático|A palavra-passe de conta secundária do principal a ACL de FileStoreService partilha.  |
| SecondaryAccountNTLMPasswordSecret | SecureString, a predefinição é em branco |Estático| O segredo de palavra-passe que utilizado como base para a mesma palavra-passe gerada ao utilizar a autenticação NTLM. |
| SecondaryAccountNTLMX509StoreLocation | cadeia, a predefinição é "LocalMachine" |Estático|A localização do arquivo do X509 certificado utilizado para gerar HMAC no SecondaryAccountNTLMPasswordSecret quando utilizar a autenticação NTLM. |
| SecondaryAccountNTLMX509StoreName | cadeia, a predefinição é "MY" |Estático|O nome de arquivo de X509 certificado utilizado para gerar HMAC no SecondaryAccountNTLMPasswordSecret quando utilizar a autenticação NTLM. |
| SecondaryAccountNTLMX509Thumbprint | cadeia, a predefinição é ""| Estático|O thumbprint de X509 do certificado utilizado para gerar HMAC no SecondaryAccountNTLMPasswordSecret quando utilizar a autenticação NTLM. |
|CommonNameNtlmPasswordSecret|SecureString, a predefinição é Common::SecureString(L"")| Estático|O segredo de palavra-passe que utilizado como base para a mesma palavra-passe gerada ao utilizar a autenticação NTLM |
|CommonName1Ntlmx509StoreLocation|cadeia, a predefinição é L "LocalMachine"|Estático|A localização do arquivo do X509 certificado utilizado para gerar HMAC no CommonName1NtlmPasswordSecret quando utilizar a autenticação NTLM |
|CommonName1Ntlmx509StoreName|cadeia, a predefinição é L "MY"| Estático|O nome de arquivo de X509 certificado utilizado para gerar HMAC no CommonName1NtlmPasswordSecret quando utilizar a autenticação NTLM |
|CommonName1Ntlmx509CommonName|cadeia, a predefinição é L""|Estático| O nome comum do X509 certificado utilizado para gerar HMAC no CommonName1NtlmPasswordSecret quando utilizar a autenticação NTLM |
|CommonName2Ntlmx509StoreLocation|cadeia, a predefinição é L "LocalMachine"| Estático|A localização do arquivo do X509 certificado utilizado para gerar HMAC no CommonName2NtlmPasswordSecret quando utilizar a autenticação NTLM |
|CommonName2Ntlmx509StoreName|cadeia, a predefinição é L "MY"|Estático| O nome de arquivo de X509 certificado utilizado para gerar HMAC no CommonName2NtlmPasswordSecret quando utilizar a autenticação NTLM |
|CommonName2Ntlmx509CommonName|cadeia, a predefinição é L""|Estático|O nome comum do X509 certificado utilizado para gerar HMAC no CommonName2NtlmPasswordSecret quando utilizar a autenticação NTLM |

### <a name="section-name-imagestoreservice"></a>Secção nome: ImageStoreService
| **Parâmetro** | **Valores permitidos** | **Política de atualização** | **Documentação de orientação ou breve descrição** |
| --- | --- | --- | --- |
| Ativado |Booleana, a predefinição é false |Estático|O sinalizador de ativado para ImageStoreService. Predefinição: false |
| TargetReplicaSetSize | Int, a predefinição é 7 |Estático|TargetReplicaSetSize para ImageStoreService. |
| MinReplicaSetSize | Int, a predefinição é 3 |Estático|MinReplicaSetSize para ImageStoreService. |
| ReplicaRestartWaitDuration | Tempo em segundos, a predefinição é 60.0 * 30 |Estático|Especifique timespan em segundos. ReplicaRestartWaitDuration para ImageStoreService. |
| QuorumLossWaitDuration | Tempo em segundos, a predefinição é MaxValue |Estático| Especifique timespan em segundos. QuorumLossWaitDuration para ImageStoreService. |
| StandByReplicaKeepDuration | Tempo em segundos, a predefinição é 3600.0 * 2 |Estático| Especifique timespan em segundos. StandByReplicaKeepDuration para ImageStoreService. |
| PlacementConstraints | cadeia, a predefinição é "" |Estático| PlacementConstraints para ImageStoreService. |

### <a name="section-name-imagestoreclient"></a>Secção nome: ImageStoreClient
| **Parâmetro** | **Valores permitidos** | **Política de atualização** | **Documentação de orientação ou breve descrição** |
| --- | --- | --- | --- |
| ClientUploadTimeout |Tempo em segundos, a predefinição é 1800 |Dinâmica|Especifique timespan em segundos. Valor de tempo limite para o pedido de carregamento de nível superior para o serviço de arquivo de imagens. |
| ClientCopyTimeout | Tempo em segundos, a predefinição é 1800 |Dinâmica| Especifique timespan em segundos. Valor de tempo limite para o pedido de cópia de nível superior para o serviço de arquivo de imagens. |
|ClientDownloadTimeout | Tempo em segundos, a predefinição é 1800 |Dinâmica| Especifique timespan em segundos. Valor de tempo limite para o pedido de transferência de nível superior para o serviço de arquivo de imagens. |
|ClientListTimeout | Tempo em segundos, a predefinição é 600 |Dinâmica|Especifique timespan em segundos. Valor de tempo limite para o pedido de lista de nível superior para o serviço de arquivo de imagens. |
|ClientDefaultTimeout | Tempo em segundos, a predefinição é 180 |Dinâmica| Especifique timespan em segundos. Valor de tempo limite para todos os pedidos não-carregamento/não-download (por exemplo, existe; eliminar) para o serviço de arquivo de imagens. |

### <a name="section-name-tokenvalidationservice"></a>Secção nome: TokenValidationService
| **Parâmetro** | **Valores permitidos** | **Política de atualização** | **Documentação de orientação ou breve descrição** |
| --- | --- | --- | --- |
| Fornecedores |cadeia, a predefinição é "DSTS" |Estático|Lista separada por vírgulas de ativar os fornecedores de validação de token (fornecedores válidos são: DSTS; AAD). Atualmente apenas um único fornecedor pode ser ativado em qualquer altura. |

### <a name="section-name-upgradeorchestrationservice"></a>Secção nome: UpgradeOrchestrationService
| **Parâmetro** | **Valores permitidos** | **Política de atualização** | **Documentação de orientação ou breve descrição** |
| --- | --- | --- | --- |
| TargetReplicaSetSize |Int, a predefinição é 0 |Estático |TargetReplicaSetSize para UpgradeOrchestrationService. |
| MinReplicaSetSize |Int, a predefinição é 0 |Estático |MinReplicaSetSize para UpgradeOrchestrationService.
| ReplicaRestartWaitDuration | Tempo em segundos, a predefinição é 60 minutos|Estático| Especifique timespan em segundos. ReplicaRestartWaitDuration para UpgradeOrchestrationService. |
| QuorumLossWaitDuration | Tempo em segundos, a predefinição é MaxValue |Estático| Especifique timespan em segundos. QuorumLossWaitDuration para UpgradeOrchestrationService. |
| StandByReplicaKeepDuration | Tempo em segundos, a predefinição é 60*24*7 minutos |Estático| Especifique timespan em segundos. StandByReplicaKeepDuration para UpgradeOrchestrationService. |
| PlacementConstraints | cadeia, a predefinição é "" |Estático| PlacementConstraints para UpgradeOrchestrationService. |
| AutoupgradeEnabled | Booleana, a predefinição é verdadeiro |Estático| Consulta automática e a ação de atualização com base num ficheiro de estado de objetivos. |
| UpgradeApprovalRequired | Booleana, a predefinição é false | Estático|Definição para fazer a atualização de código exigir aprovação do administrador antes de continuar. |

### <a name="section-name-upgradeservice"></a>Secção nome: UpgradeService
| **Parâmetro** | **Valores permitidos** | **Política de atualização** | **Documentação de orientação ou breve descrição** |
| --- | --- | --- | --- |
| PlacementConstraints |cadeia, a predefinição é "" |Não Permitido|PlacementConstraints para o serviço de atualização. |
| TargetReplicaSetSize | Int, a predefinição é 3 |Não Permitido| TargetReplicaSetSize para UpgradeService. |
| MinReplicaSetSize | Int, a predefinição é 2 |Não Permitido| MinReplicaSetSize para UpgradeService. |
| CoordinatorType | cadeia, a predefinição é "WUTest"|Não Permitido|CoordinatorType para UpgradeService. |
| BaseUrl | cadeia, a predefinição é "" |Estático|BaseUrl para UpgradeService. |
| ClusterId | cadeia, a predefinição é "" |Estático|ClusterId para UpgradeService. |
| X509StoreName | cadeia, a predefinição é "Os meus"|Dinâmica|X509StoreName para UpgradeService. |
| X509StoreLocation | cadeia, a predefinição é "" |Dinâmica| X509StoreLocation para UpgradeService. |
| X509FindType | cadeia, a predefinição é ""|Dinâmica| X509FindType para UpgradeService. |
| X509FindValue | cadeia, a predefinição é "" |Dinâmica| X509FindValue para UpgradeService. |
| X509SecondaryFindValue | cadeia, a predefinição é "" |Dinâmica| X509SecondaryFindValue para UpgradeService. |
| OnlyBaseUpgrade | Booleana, a predefinição é false |Dinâmica|OnlyBaseUpgrade para UpgradeService. |
| TestCabFolder | cadeia, a predefinição é "" |Estático| TestCabFolder para UpgradeService. |

### <a name="section-name-security"></a>Secção nome: segurança
| **Parâmetro** | **Valores permitidos** |**Política de atualização**| **Documentação de orientação ou breve descrição** |
| --- | --- | --- | --- |
|ClusterCredentialType|cadeia, a predefinição é L "None"|Não Permitido|Indica o tipo de credenciais de segurança a utilizar para proteger o cluster. Os valores válidos são "Nenhum/X509/Windows" |
|ServerAuthCredentialType|cadeia, a predefinição é L "None"|Estático|Indica o tipo de credenciais de segurança a utilizar para proteger a comunicação entre FabricClient e o Cluster. Os valores válidos são "Nenhum/X509/Windows" |
|ClientRoleEnabled|booleana, a predefinição é FALSE|Estático|Indica se a função de cliente está ativada; Quando definido como true; os clientes são atribuídos a funções com base na respetiva identidades. Para V2; Ativar Isto significa que o cliente não se encontra na AdminClientCommonNames/AdminClientIdentities só é possível executar operações só de leitura. |
|ClusterCertThumbprints|cadeia, a predefinição é L""|Dinâmica|Thumbprints de certificados permitidos para aderirem ao cluster; uma lista de nomes de valores separados por vírgulas. |
|ServerCertThumbprints|cadeia, a predefinição é L""|Dinâmica|Thumbprints de certificados de servidor utilizados pelo cluster para comunicar com os clientes; os clientes utilizam isto para autenticar o cluster. É uma lista de nomes de valores separados por vírgulas. |
|ClientCertThumbprints|cadeia, a predefinição é L""|Dinâmica|Thumbprints de certificados utilizados por clientes para comunicar com o cluster cluster utiliza este autorizar a ligação de entrada. É uma lista de nomes de valores separados por vírgulas. |
|AdminClientCertThumbprints|cadeia, a predefinição é L""|Dinâmica|Thumbprints de certificados utilizados por clientes numa função de administrador. É uma lista de nomes de valores separados por vírgulas. |
|CrlCheckingFlag|uint, a predefinição é 0x40000000|Dinâmica|Sinalizador de validação da cadeia do certificado predefinida; pode ser substituído pelo sinalizador específica do componente; Por exemplo, Federação/X509CertChainFlags 0x10000000 CERT_CHAIN_REVOCATION_CHECK_END_CERT 0x20000000 CERT_CHAIN_REVOCATION_CHECK_CHAIN 0x40000000 CERT_CHAIN_REVOCATION_CHECK_CHAIN_EXCLUDE_ROOT 0x80000000 CERT_CHAIN_REVOCATION_CHECK_CACHE_ APENAS a definição de 0 desativa a verificação de CRL completa lista de valores suportados é documentada dwFlags de CertGetCertificateChain: http://msdn.microsoft.com/library/windows/desktop/aa376078 (v=vs.85).aspx |
|IgnoreCrlOfflineError|booleana, a predefinição é FALSE|Dinâmica|Se pretende ignorar o erro offline da CRL ao lado do servidor verifica certificados de cliente recebidos |
|IgnoreSvrCrlOfflineError|booleana, a predefinição é verdadeiro|Dinâmica|Se pretende ignorar o erro offline CRL aquando do lado do cliente verifica os certificados de servidor de receção; predefinido como true. Ataques de certificados de servidor revogados requerem comprometer DNS; mais difícil que com certificados de cliente revogados. |
|CrlDisablePeriod|TimeSpan, a predefinição é Common::TimeSpan::FromMinutes(15)|Dinâmica|Especifique timespan em segundos. Quanto a verificação CRL está desativada para um determinado certificado depois de encontrar erro offline; Se o erro offline CRL pode ser ignorado. |
|CrlOfflineHealthReportTtl|TimeSpan, a predefinição é Common::TimeSpan::FromMinutes(1440)|Dinâmica|Especifique timespan em segundos. |
|CertificateHealthReportingInterval|TimeSpan, a predefinição é Common::TimeSpan::FromSeconds(3600 * 8)|Estático|Especifique timespan em segundos. Especifique o intervalo para relatórios de estado de funcionamento de certificado; predefinida para 8 horas; definição de 0 desativa a relatórios de estado de funcionamento do certificado |
|CertificateExpirySafetyMargin|TimeSpan, a predefinição é Common::TimeSpan::FromMinutes(43200)|Estático|Especifique timespan em segundos. Margem de segurança para a expiração do certificado; Estado de relatório de estado de funcionamento do certificado é alterado de OK para aviso quando expiração estiver próximo mais. Predefinição é 30 dias. |
|ClientClaimAuthEnabled|booleana, a predefinição é FALSE|Estático|Indica se a autenticação baseada em afirmações está ativada nos clientes; definir este verdadeiro implicitamente define ClientRoleEnabled. |
|ClientClaims|cadeia, a predefinição é L""|Dinâmica|Todas as afirmações possíveis esperadas a partir de clientes para ligar ao gateway. Esta é uma lista de 'Ou': ClaimsEntry || ClaimsEntry || ClaimsEntry... cada ClaimsEntry é uma lista de "E": ClaimType = ClaimValue & & ClaimType = ClaimValue & & ClaimType = ClaimValue... |
|AdminClientClaims|cadeia, a predefinição é L""|Dinâmica|Todas as afirmações possíveis esperadas a partir de clientes de admin; o mesmo formato que ClientClaims; Esta lista internamente obtém adicionada ao ClientClaims; por isso, não é necessário adicionar também as entradas do mesmas ao ClientClaims. |
|ClusterSpn|cadeia, a predefinição é L""|Não Permitido|Nome principal do serviço de cluster; Quando os recursos de infraestrutura é executado como um utilizador de domínio único (conta de utilizador de domínio/gMSA). É o SPN da escuta de concessão e os serviços de escuta em fabric.exe: serviços de escuta de Federação; Serviços de escuta de replicação interna; serviço de escuta de serviço de tempo de execução e o serviço de escuta de gateway nomenclatura. Isto deve ser deixado em branco quando recursos de infraestrutura é executada em contas de computador ligar caso em que o serviço de escuta de computação de lado de SPN do serviço de escuta do transporte endereço. |
|ClusterIdentities|cadeia, a predefinição é L""|Dinâmica|Identidades de Windows de nós de cluster utilizado para autorização de associação de cluster. É uma lista separada por vírgulas; cada entrada é um nome de conta de domínio ou o nome do grupo |
|ClientIdentities|cadeia, a predefinição é L""|Dinâmica|Identidades de Windows de FabricClient; gateway de atribuição de nomes utiliza-o para autorizar ligações de entrada. É uma lista separada por vírgulas; cada entrada é um nome de conta de domínio ou o nome do grupo. Para sua comodidade; a conta que executa fabric.exe automaticamente é permitida. por isso, são grupo ServiceFabricAllowedUsers e ServiceFabricAdministrators. |
|AdminClientIdentities|cadeia, a predefinição é L""|Dinâmica|Identidades de Windows de clientes de recursos de infraestrutura na função de administrador; utilizado para autorizar operações privilegiadas de recursos de infraestrutura. É uma lista separada por vírgulas; cada entrada é um nome de conta de domínio ou o nome do grupo. Para sua comodidade; a conta que executa fabric.exe é atribuída automaticamente a função de administrador por isso, é grupo ServiceFabricAdministrators. |
|AADTenantId|cadeia, a predefinição é L""|Estático|ID do inquilino (GUID) |
|AADClusterApplication|cadeia, a predefinição é L""|Estático|Nome da aplicação Web API ou ID que representa o cluster |
|AADClientApplication|cadeia, a predefinição é L""|Estático|Nome da aplicação cliente nativo ou ID que representa os clientes de recursos de infraestrutura |
|X509Folder|cadeia, a predefinição é /var/lib/waagent|Estático|Pasta onde X509 certificados e chaves privadas estão localizadas |
|FabricHostSpn| cadeia, a predefinição é L"" |Estático| Nome principal do serviço de FabricHost; Quando os recursos de infraestrutura é executado como um utilizador de domínio único (conta de utilizador de domínio/gMSA) e FabricHost é executado sob a conta de computador. É o serviço de escuta de SPN do IPC de FabricHost; que, por predefinição deve ser deixado em branco, uma vez que FabricHost é executado sob a conta de computador |
|DisableFirewallRuleForPublicProfile| booleana, a predefinição é verdadeiro | Estático|Indica se a regra de firewall não deve estar ativada para o perfil público |
|DisableFirewallRuleForPrivateProfile| booleana, a predefinição é verdadeiro |Estático| Indica se a regra de firewall não deve estar ativada para perfil privado | 
|DisableFirewallRuleForDomainProfile| booleana, a predefinição é verdadeiro |Estático| Indica se a regra de firewall não deve estar ativada para o perfil de domínio |
|SettingsX509StoreName| cadeia, a predefinição é L "MY"| Dinâmica|Utilizado por recursos de infraestrutura para proteção da configuração do arquivo de certificados X509 |

### <a name="section-name-securityadminclientx509names"></a>Nome de secção: Segurança/AdminClientX509Names
| **Parâmetro** | **Valores permitidos** | **Política de atualização** | **Documentação de orientação ou breve descrição** |
| --- | --- | --- | --- |
|PropertyGroup|X509NameMap, a predefinição é nenhum|Dinâmica| |

### <a name="section-name-securityclientx509names"></a>Nome de secção: Segurança/ClientX509Names
| **Parâmetro** | **Valores permitidos** | **Política de atualização** | **Documentação de orientação ou breve descrição** |
| --- | --- | --- | --- |
PropertyGroup|X509NameMap, a predefinição é nenhum|Dinâmica| |

### <a name="section-name-securityclusterx509names"></a>Nome de secção: Segurança/ClusterX509Names
| **Parâmetro** | **Valores permitidos** | **Política de atualização** | **Documentação de orientação ou breve descrição** |
| --- | --- | --- | --- |
PropertyGroup|X509NameMap, a predefinição é nenhum|Dinâmica| |

### <a name="section-name-securityserverx509names"></a>Nome de secção: Segurança/ServerX509Names
| **Parâmetro** | **Valores permitidos** | **Política de atualização** | **Documentação de orientação ou breve descrição** |
| --- | --- | --- | --- |
PropertyGroup|X509NameMap, a predefinição é nenhum|Dinâmica| |

### <a name="section-name-securityclientaccess"></a>Nome de secção: Segurança/ClientAccess
| **Parâmetro** | **Valores permitidos** | **Política de atualização** | **Documentação de orientação ou breve descrição** |
| --- | --- | --- | --- |
| CreateName |cadeia, a predefinição é "Admin" |Dinâmica|Configuração de segurança para a criação de URI de nomenclatura. |
| DeleteName |cadeia, a predefinição é "Admin" |Dinâmica|Configuração de segurança para eliminação de URI de nomenclatura. |
| PropertyWriteBatch |cadeia, a predefinição é "Admin" |Dinâmica|Operações de escrita de configurações de segurança para a propriedade de nomenclatura. |
| CreateService |cadeia, a predefinição é "Admin" |Dinâmica| Configuração de segurança para a criação do serviço. |
| CreateServiceFromTemplate |cadeia, a predefinição é "Admin" |Dinâmica|Configuração de segurança para a criação do serviço do modelo. |
| UpdateService |cadeia, a predefinição é "Admin" |Dinâmica|Configuração de segurança para atualizações de serviço. |
| DeleteService  |cadeia, a predefinição é "Admin" |Dinâmica|Configuração de segurança para eliminação do serviço. |
| ProvisionApplicationType |cadeia, a predefinição é "Admin" |Dinâmica| Configuração de segurança para o aprovisionamento de tipo de aplicação. |
| CreateApplication |cadeia, a predefinição é "Admin" | Dinâmica|Configuração de segurança para a criação da aplicação. |
| DeleteApplication |cadeia, a predefinição é "Admin" |Dinâmica| Configuração de segurança para eliminação de aplicação. |
| UpgradeApplication |cadeia, a predefinição é "Admin" |Dinâmica| Configuração de segurança para iniciar ou interromper as atualizações de aplicações. |
| RollbackApplicationUpgrade |cadeia, a predefinição é "Admin" |Dinâmica| Configuração de segurança para reverter as atualizações de aplicações. |
| UnprovisionApplicationType |cadeia, a predefinição é "Admin" |Dinâmica| Configuração de segurança para anulação do tipo de aplicação aprovisionamento. |
| MoveNextUpgradeDomain |cadeia, a predefinição é "Admin" |Dinâmica| Configuração de segurança para retomar as atualizações de aplicações com um domínio de atualização explícita. |
| ReportUpgradeHealth |cadeia, a predefinição é "Admin" |Dinâmica| Configuração de segurança para retomar as atualizações de aplicações com o progresso da atualização atual. |
| ReportHealth |cadeia, a predefinição é "Admin" |Dinâmica| Configuração de segurança para os relatórios do Estado de funcionamento. |
| ProvisionFabric |cadeia, a predefinição é "Admin" |Dinâmica| Configuração de segurança para o aprovisionamento manifesto MSI e/ou de Cluster. |
| UpgradeFabric |cadeia, a predefinição é "Admin" |Dinâmica| Configuração de segurança para iniciar as atualizações de cluster. |
| RollbackFabricUpgrade |cadeia, a predefinição é "Admin" |Dinâmica| Configuração de segurança para reverter a atualização do cluster. |
| UnprovisionFabric |cadeia, a predefinição é "Admin" |Dinâmica| Configuração de segurança para o Cluster / MSI manifesto anulação de aprovisionamento. |
| MoveNextFabricUpgradeDomain |cadeia, a predefinição é "Admin" |Dinâmica| Configuração de segurança para retomar a atualizações de cluster com um explicity domínio de atualização. |
| ReportFabricUpgradeHealth |cadeia, a predefinição é "Admin" |Dinâmica| Configuração de segurança para retomar a atualizações de cluster com o progresso da atualização atual. |
| StartInfrastructureTask |cadeia, a predefinição é "Admin" | Dinâmica|Configuração de segurança para iniciar tarefas de infraestrutura. |
| FinishInfrastructureTask |cadeia, a predefinição é "Admin" |Dinâmica| Configuração de segurança para tarefas de infraestrutura adira agora. |
| ActivateNode |cadeia, a predefinição é "Admin" |Dinâmica| Configuração de segurança para a ativação de um nó. |
| DeactivateNode |cadeia, a predefinição é "Admin" |Dinâmica| Configuração de segurança para desativar um nó. |
| DeactivateNodesBatch |cadeia, a predefinição é "Admin" |Dinâmica| Configuração de segurança para desativar vários nós. |
| RemoveNodeDeactivations |cadeia, a predefinição é "Admin" |Dinâmica| Configuração de segurança para a desativação de reversão em vários nós. |
| GetNodeDeactivationStatus |cadeia, a predefinição é "Admin" |Dinâmica| Configuração de segurança para a verificação do Estado de Desativação. |
| NodeStateRemoved |cadeia, a predefinição é "Admin" |Dinâmica| Configuração de segurança para os relatórios do Estado do nó removido. |
| RecoverPartition |cadeia, a predefinição é "Admin" | Dinâmica|Configuração de segurança para recuperar uma partição. |
| RecoverPartitions |cadeia, a predefinição é "Admin" | Dinâmica|Configuração de segurança para recuperar as partições. |
| RecoverServicePartitions |cadeia, a predefinição é "Admin" |Dinâmica| Configuração de segurança para recuperar as partições de serviço. |
| RecoverSystemPartitions |cadeia, a predefinição é "Admin" |Dinâmica| Configuração de segurança para recuperar as partições de serviço do sistema. |
| ReportFault |cadeia, a predefinição é "Admin" |Dinâmica| Configuração de segurança para relatórios de falhas. |
| InvokeInfrastructureCommand |cadeia, a predefinição é "Admin" |Dinâmica| Configuração de segurança para comandos de gestão de tarefas de infraestrutura. |
| FileContent |cadeia, a predefinição é "Admin" |Dinâmica| Transferência de ficheiros de cliente (externo para cluster) do arquivo de configuração de segurança para a imagem. |
| FileDownload |cadeia, a predefinição é "Admin" |Dinâmica| Configuração de segurança para imagem arquivo cliente ficheiro transferência início (externo para cluster). |
| InternalList |cadeia, a predefinição é "Admin" | Dinâmica|Operação de lista de ficheiros de cliente (interna) do arquivo de configuração de segurança para a imagem. |
| Eliminar |cadeia, a predefinição é "Admin" |Dinâmica| Configurações de segurança para a imagem do arquivo de operação de eliminação do cliente. |
| Carregar |cadeia, a predefinição é "Admin" | Dinâmica|A operação de carregamento do cliente do arquivo de configuração de segurança para a imagem. |
| GetStagingLocation |cadeia, a predefinição é "Admin" |Dinâmica| Cliente de obtenção de localização de transição do arquivo de configuração de segurança para a imagem. |
| GetStoreLocation |cadeia, a predefinição é "Admin" |Dinâmica| Obtenção de localização de arquivo de cliente do arquivo de configuração de segurança para a imagem. |
| NodeControl |cadeia, a predefinição é "Admin" |Dinâmica| Configuração de segurança para iniciar; parar; e reiniciar nós. |
| CodePackageControl |cadeia, a predefinição é "Admin" |Dinâmica| Configuração de segurança para reiniciar os pacotes de código. |
| UnreliableTransportControl |cadeia, a predefinição é "Admin" |Dinâmica| Transporte não fiável ativado para adição e remoção de comportamentos. |
| MoveReplicaControl |cadeia, a predefinição é "Admin" | Dinâmica|Mova a réplica. |
| PredeployPackageToNode |cadeia, a predefinição é "Admin" |Dinâmica| Api de pré-implementação. |
| StartPartitionDataLoss |cadeia, a predefinição é "Admin" |Dinâmica| Induces perda de dados numa partição. |
| StartPartitionQuorumLoss |cadeia, a predefinição é "Admin" |Dinâmica| Induces perda de quórum numa partição. |
| StartPartitionRestart |cadeia, a predefinição é "Admin" |Dinâmica| Reinicia em simultâneo algumas ou todas as réplicas de uma partição. |
| CancelTestCommand |cadeia, a predefinição é "Admin" |Dinâmica| Cancela uma TestCommand específico - se estiver em trânsito. |
| StartChaos |cadeia, a predefinição é "Admin" |Dinâmica| Inicia Chaos - se de que ainda não estiver iniciado. |
| StopChaos |cadeia, a predefinição é "Admin" |Dinâmica| Interrompe Chaos - se de que foi iniciado. |
| StartNodeTransition |cadeia, a predefinição é "Admin" |Dinâmica| Configuração de segurança para iniciar uma transição de nó. |
| StartClusterConfigurationUpgrade |cadeia, a predefinição é "Admin" |Dinâmica| Induces StartClusterConfigurationUpgrade numa partição. |
| GetUpgradesPendingApproval |cadeia, a predefinição é "Admin" |Dinâmica| Induces GetUpgradesPendingApproval numa partição. |
| StartApprovedUpgrades |cadeia, a predefinição é "Admin" |Dinâmica| Induces StartApprovedUpgrades numa partição. |
| Ping |cadeia, a predefinição é "Admin\|\|Utilizador" |Dinâmica| Configuração de segurança para pings de cliente. |
| Consulta |cadeia, a predefinição é "Admin\|\|Utilizador" |Dinâmica| Configuração de segurança para consultas. |
| NameExists |cadeia, a predefinição é "Admin\|\|Utilizador" | Dinâmica|Verifica a existência de URI de nomenclatura, a configuração de segurança. |
| EnumerateSubnames |cadeia, a predefinição é "Admin\|\|Utilizador" |Dinâmica| Configuração de segurança para a enumeração de URI de nomenclatura. |
| EnumerateProperties |cadeia, a predefinição é "Admin\|\|Utilizador" | Dinâmica|Configuração de segurança para atribuir nomes a enumeração da propriedade. |
| PropertyReadBatch |cadeia, a predefinição é "Admin\|\|Utilizador" |Dinâmica| Operações de leitura de configuração de segurança para a propriedade de nomenclatura. |
| GetServiceDescription |cadeia, a predefinição é "Admin\|\|Utilizador" |Dinâmica| Configuração de segurança para notificações de serviço de inquérito longo e descrições dos serviços de leitura. |
| ResolveService |cadeia, a predefinição é "Admin\|\|Utilizador" |Dinâmica| Configuração de segurança para a resolução de serviço com base em conformidade. |
| ResolveNameOwner |cadeia, a predefinição é "Admin\|\|Utilizador" | Dinâmica|Configuração de segurança para resolver o proprietário de URI de nomenclatura. |
| ResolvePartition |cadeia, a predefinição é "Admin\|\|Utilizador" | Dinâmica|Configuração de segurança para resolver os serviços do sistema. |
| ServiceNotifications |cadeia, a predefinição é "Admin\|\|Utilizador" |Dinâmica| Configuração de segurança para notificações de serviço com base em eventos. |
| PrefixResolveService |cadeia, a predefinição é "Admin\|\|Utilizador" |Dinâmica| Configuração de segurança para a resolução do prefixo de serviço com base em conformidade. |
| GetUpgradeStatus |cadeia, a predefinição é "Admin\|\|Utilizador" |Dinâmica| Configuração de segurança para a consulta o estado de atualização da aplicação. |
| GetFabricUpgradeStatus |cadeia, a predefinição é "Admin\|\|Utilizador" |Dinâmica| Configuração de segurança para a consulta o estado de atualização do cluster. |
| InvokeInfrastructureQuery |cadeia, a predefinição é "Admin\|\|Utilizador" | Dinâmica|Configuração de segurança para consultar as tarefas de infraestrutura. |
| Lista |cadeia, a predefinição é "Admin\|\|Utilizador" | Dinâmica|A operação de lista de ficheiros de cliente do arquivo de configuração de segurança para a imagem. |
| ResetPartitionLoad |cadeia, a predefinição é "Admin\|\|Utilizador" |Dinâmica| Configuração de segurança para reposição de carga para um failoverUnit. |
| ToggleVerboseServicePlacementHealthReporting | cadeia, a predefinição é "Admin\|\|Utilizador" |Dinâmica| Configuração de segurança para ativando ou desativando HealthReporting de ServicePlacement verboso. |
| GetPartitionDataLossProgress | cadeia, a predefinição é "Admin\|\|Utilizador" | Dinâmica|Obtém o progresso de uma chamada de api de perda de dados de invoke. |
| GetPartitionQuorumLossProgress | cadeia, a predefinição é "Admin\|\|Utilizador" |Dinâmica| Obtém o progresso de uma chamada de api de perda de quórum invoke. |
| GetPartitionRestartProgress | cadeia, a predefinição é "Admin\|\|Utilizador" |Dinâmica| Obtém o progresso de uma chamada de api de partição de reinício. |
| GetChaosReport | cadeia, a predefinição é "Admin\|\|Utilizador" |Dinâmica| Obtém o estado de Chaos dentro de um intervalo de tempo especificado. |
| GetNodeTransitionProgress | cadeia, a predefinição é "Admin\|\|Utilizador" |Dinâmica| Configuração de segurança para obter o progresso num comando de transição de nó. |
| GetClusterConfigurationUpgradeStatus | cadeia, a predefinição é "Admin\|\|Utilizador" |Dinâmica| Induces GetClusterConfigurationUpgradeStatus numa partição. |
| GetClusterConfiguration | cadeia, a predefinição é "Admin\|\|Utilizador" | Dinâmica|Induces GetClusterConfiguration numa partição. |
|CreateComposeDeployment|cadeia, a predefinição é L "Admin"| Dinâmica|Cria uma implementação de compose descrita através de ficheiros de compose |
|DeleteComposeDeployment|cadeia, a predefinição é L "Admin"| Dinâmica|Elimina a implementação de compose |
|UpgradeComposeDeployment|cadeia, a predefinição é L "Admin"| Dinâmica|Atualiza a implementação de compose |
|ResolveSystemService|cadeia, a predefinição é L "Admin\|\|Utilizador"|Dinâmica| Configuração de segurança para resolver os serviços do sistema |
|GetUpgradeOrchestrationServiceState|cadeia, a predefinição é L "Admin"| Dinâmica|Induces GetUpgradeOrchestrationServiceState numa partição |
|SetUpgradeOrchestrationServiceState|cadeia, a predefinição é L "Admin"| Dinâmica|Induces SetUpgradeOrchestrationServiceState numa partição |

### <a name="section-name-reconfigurationagent"></a>Secção nome: ReconfigurationAgent
| **Parâmetro** | **Valores permitidos** | **Política de atualização** | **Documentação de orientação ou breve descrição** |
| --- | --- | --- | --- |
| ApplicationUpgradeMaxReplicaCloseDuration | Tempo em segundos, a predefinição é 900 |Dinâmica|Especifique timespan em segundos. Feche a duração para o qual o sistema irá aguardar antes de terminar os anfitriões que tenham as réplicas que está bloqueada no durante a atualização da aplicação.|
| ServiceApiHealthDuration | Tempo em segundos, a predefinição é 30 minutos |Dinâmica| Especifique timespan em segundos. ServiceApiHealthDuration define quanto iremos aguardar por uma API de serviço executar antes de elaboramos relatórios-mau estado de funcionamento. |
| ServiceReconfigurationApiHealthDuration | Tempo em segundos, a predefinição é 30 |Dinâmica| Especifique timespan em segundos. ServiceReconfigurationApiHealthDuration define quanto iremos aguardar por uma API de serviço executar antes de elaboramos relatórios de estado de funcionamento incorreto. Isto aplica-se para chamadas API que afetam a disponibilidade.|
| PeriodicApiSlowTraceInterval | Tempo em segundos, a predefinição é 5 minutos |Dinâmica| Especifique timespan em segundos. PeriodicApiSlowTraceInterval define o intervalo durante o qual irão ser retraced chamadas de API lentas pelo monitor de API. |
| NodeDeactivationMaxReplicaCloseDuration | Tempo em segundos, a predefinição é 900 |Dinâmica|Especifique timespan em segundos. Feche a duração para o qual o sistema irá aguardar antes de terminar os anfitriões que tenham as réplicas que está bloqueada no durante a desativação de nós. |
| FabricUpgradeMaxReplicaCloseDuration | Tempo em segundos, a predefinição é 900 |Dinâmica| Especifique timespan em segundos. Feche a duração para o qual o sistema irá aguardar antes de terminar os anfitriões que tenham as réplicas que está bloqueada no durante a atualização do fabric. |
|GracefulReplicaShutdownMaxDuration|TimeSpan, a predefinição é Common::TimeSpan::FromSeconds(120)|Dinâmica|Especifique timespan em segundos. A duração para o qual o sistema irá aguardar antes de terminar os anfitriões que tenham as réplicas que está bloqueada no feche. Se este valor é definido como 0, as réplicas serão não ser deu instruções ao fechar.|
|ReplicaChangeRoleFailureRestartThreshold|Int, a predefinição é 10|Dinâmica| Número inteiro. Especifique o número de falhas de API durante a promoção principal após o qual será aplicada a ação de mitigação automática (réplica reiniciar). |
|ReplicaChangeRoleFailureWarningReportThreshold|int, a predefinição é 2147483647|Dinâmica| Número inteiro. Especifique o número de falhas de API durante a promoção principal após o qual o relatório de estado de funcionamento de aviso é acionado.|

### <a name="section-name-placementandloadbalancing"></a>Secção nome: PlacementAndLoadBalancing
| **Parâmetro** | **Valores permitidos** | **Política de atualização** | **Documentação de orientação ou breve descrição** |
| --- | --- | --- | --- |
| TraceCRMReasons |Booleana, a predefinição é verdadeiro |Dinâmica|Especifica se pretende rastrear razões para CRM emitido movimentos para o canal de eventos operacionais. |
| ValidatePlacementConstraint | Booleana, a predefinição é verdadeiro |Dinâmica| Especifica se pretende ou não a expressão PlacementConstraint para um serviço é validada quando ServiceDescription um serviço é atualizado. |
| PlacementConstraintValidationCacheSize | Int, a predefinição é 10000 |Dinâmica| Limita o tamanho da tabela utilizado para a validação rápida e a colocação em cache de expressões de restrição de posicionamento. |
|VerboseHealthReportLimit | Int, a predefinição é 20 | Dinâmica|Define o número de vezes que uma réplica tem de ir unplaced antes de um aviso de estado de funcionamento é comunicado para a mesma (se o relatório de estado de funcionamento verboso está ativado). |
|ConstraintViolationHealthReportLimit | Int, predefinido é 50 |Dinâmica| Define o número de vezes que uma réplica de violação de restrição tem de ser forma permanente não corrigidos antes de diagnóstico é efetuado e relatórios de estado de funcionamento são emitidos. |
|DetailedConstraintViolationHealthReportLimit | Int, a predefinição é 200 |Dinâmica| Define o número de vezes que uma réplica de violação de restrição tem de ser forma permanente não corrigidos antes de diagnóstico é a realização de um e detalhadas de estado de funcionamento relatórios são emitidos. |
|DetailedVerboseHealthReportLimit | Int, a predefinição é 200 | Dinâmica|Define o número de vezes que uma réplica unplaced tem de ser unplaced de forma permanente antes de relatórios de estado de funcionamento detalhadas são emitidos. |
|ConsecutiveDroppedMovementsHealthReportLimit | Int, a predefinição é 20 | Dinâmica|Define o número de vezes consecutivas que emitido ResourceBalancer movimentos são ignorados antes de diagnóstico é efetuado e avisos de estado de funcionamento são emitidos. Negativo: Sem avisos emitidos sob esta condição. |
|DetailedNodeListLimit | Int, a predefinição é 15 |Dinâmica| Define o número de nós por restrição para incluir antes de truncagem nos relatórios de réplica Unplaced. |
|DetailedPartitionListLimit | Int, a predefinição é 15 |Dinâmica| Define o número de partições por entrada de diagnóstico para uma restrição incluir antes de truncagem no Diagnostics. |
|DetailedDiagnosticsInfoListLimit | Int, a predefinição é 15 |Dinâmica| Define o número de entradas de diagnóstico (com informações detalhadas) por restrição para incluir antes de truncagem no Diagnostics.|
|PLBRefreshGap | Tempo em segundos, a predefinição é 1 |Dinâmica| Especifique timespan em segundos. Define a quantidade mínima de tempo que deve passar antes de PLB atualiza o estado de novo. |
|MinPlacementInterval | Tempo em segundos, a predefinição é 1 |Dinâmica| Especifique timespan em segundos. Define a quantidade mínima de tempo que deve passar antes de dois Arredonda de posicionamento consecutivos. |
|MinConstraintCheckInterval | Tempo em segundos, a predefinição é 1 |Dinâmica| Especifique timespan em segundos. Define a quantidade mínima de tempo que deve passar antes Arredonda de verificação de dois restrição consecutivos. |
|MinLoadBalancingInterval | Tempo em segundos, a predefinição é 5 |Dinâmica| Especifique timespan em segundos. Define a quantidade mínima de tempo que deve passar antes de dois Arredonda balanceamento consecutivas. |
|BalancingDelayAfterNodeDown | Tempo em segundos, a predefinição é 120 |Dinâmica|Especifique timespan em segundos. Não se iniciam balanceamento atividades durante este período após um nó para baixo de eventos. |
|BalancingDelayAfterNewNode | Tempo em segundos, a predefinição é 120 |Dinâmica|Especifique timespan em segundos. Não se iniciam balanceamento atividades durante este período depois de adicionar um novo nó. |
|ConstraintFixPartialDelayAfterNodeDown | Tempo em segundos, a predefinição é 120 |Dinâmica| Especifique timespan em segundos. Fazê-lo não as violações de restrição FaultDomain corrigir e UpgradeDomain durante este período após um nó para baixo de eventos. |
|ConstraintFixPartialDelayAfterNewNode | Tempo em segundos, a predefinição é 120 |Dinâmica| Especifique timespan em segundos. DDo corrigir FaultDomain não e violações de restrição de UpgradeDomain durante este período depois de adicionar um novo nó. |
|GlobalMovementThrottleThreshold | Uint, a predefinição é 1000 |Dinâmica| Número máximo de movimentos permitido na fase de balanceamento no último intervalo indicado pelo GlobalMovementThrottleCountingInterval. |
|GlobalMovementThrottleThresholdForPlacement | Uint, a predefinição é 0 |Dinâmica| Número máximo de movimentos permitido na fase de colocação do intervalo passado indicado pelo GlobalMovementThrottleCountingInterval.0 indica sem limite.|
|GlobalMovementThrottleThresholdForBalancing | Uint, a predefinição é 0 | Dinâmica|Número máximo de movimentos permitido na fase de balanceamento do intervalo passado indicado pelo GlobalMovementThrottleCountingInterval. 0 indica sem limite. |
|GlobalMovementThrottleCountingInterval | Tempo em segundos, a predefinição é 600 |Estático| Especifique timespan em segundos. Indica o comprimento do intervalo passado para o qual controlar por Movimentos de réplica de domínio (utilizados juntamente com GlobalMovementThrottleThreshold). Pode ser definido como 0 para ignorar completamente a limitação global. |
|MovementPerPartitionThrottleThreshold | Uint, predefinido é 50 |Dinâmica| Não existem movimento relacionados com o balanceamento ocorrerá para uma partição se o número de balanceamento de movimentos relacionados para réplicas dessa partição tiver atingido ou excedido MovementPerFailoverUnitThrottleThreshold no último intervalo indicado pelo MovementPerPartitionThrottleCountingInterval. |
|MovementPerPartitionThrottleCountingInterval | Tempo em segundos, a predefinição é 600 |Estático| Especifique timespan em segundos. Indica o comprimento do intervalo passado para o qual pretende controlar movimentos de réplica para cada partição (utilizado juntamente com MovementPerPartitionThrottleThreshold). |
|PlacementSearchTimeout | Tempo em segundos, a predefinição é 0,5 |Dinâmica| Especifique timespan em segundos. Quando a colocação de serviços; Procure no máximo, este longa antes de o devolver um resultado. |
|UseMoveCostReports | Booleana, a predefinição é false | Dinâmica|Dá instruções ao LB para ignorar o elemento de custo da função classificação; resultante número potencialmente grande de move para melhor equilibrada colocação. |
|PreventTransientOvercommit | Booleana, a predefinição é false | Dinâmica|Determina a deve PLB imediatamente contar em recursos que irão ser libertados cópias de segurança pelo move iniciada. Por predefinição; PLB pode iniciar a movimentação terminar e sobreconsolidar movimentação no mesmo nó que pode criar transitório. Definir este parâmetro como true, irá impedir que os tipos de overcommits e será defrag a pedido (aka placementWithMove) desativada. |
|InBuildThrottlingEnabled | Booleana, a predefinição é false |Dinâmica| Determine se a limitação de compilação em está ativada. |
|InBuildThrottlingAssociatedMetric | cadeia, a predefinição é "" |Estático| O nome da métrica associado para esta limitação. |
|InBuildThrottlingGlobalMaxValue | Int, a predefinição é 0 |Dinâmica|O número garantido de réplicas de compilação em permitido global. |
|SwapPrimaryThrottlingEnabled | Booleana, a predefinição é false|Dinâmica| Determine se a limitação de comutação primário está ativada. |
|SwapPrimaryThrottlingAssociatedMetric | cadeia, a predefinição é ""|Estático| O nome da métrica associado para esta limitação. |
|SwapPrimaryThrottlingGlobalMaxValue | Int, a predefinição é 0 |Dinâmica| O número garantido de réplicas de principais de comutação permitido global. |
|PlacementConstraintPriority | Int, a predefinição é 0 | Dinâmica|Determina a prioridade de restrição de posicionamento: 0: rígido; 1: recuperável; negativo: Ignorar. |
|PreferredLocationConstraintPriority | Int, a predefinição é 2| Dinâmica|Determina a prioridade de restrição de localização preferencial: 0: rígido; 1: recuperável; 2: Otimização; negativo: Ignorar |
|CapacityConstraintPriority | Int, a predefinição é 0 | Dinâmica|Determina a prioridade de restrição de capacidade: 0: rígido; 1: recuperável; negativo: Ignorar. |
|AffinityConstraintPriority | Int, a predefinição é 0 | Dinâmica|Determina a prioridade de restrição de afinidade: 0: rígido; 1: recuperável; negativo: Ignorar. |
|FaultDomainConstraintPriority | Int, a predefinição é 0 |Dinâmica| Determina a prioridade de restrição de domínio de falhas: 0: rígido; 1: recuperável; negativo: Ignorar. |
|UpgradeDomainConstraintPriority | Int, a predefinição é 1| Dinâmica|Determina a prioridade de restrição de domínio de atualização: 0: rígido; 1: recuperável; negativo: Ignorar. |
|ScaleoutCountConstraintPriority | Int, a predefinição é 0 |Dinâmica| Determina a prioridade de restrição de contagem de scaleout: 0: rígido; 1: recuperável; negativo: Ignorar. |
|ApplicationCapacityConstraintPriority | Int, a predefinição é 0 | Dinâmica|Determina a prioridade de restrição de capacidade: 0: rígido; 1: recuperável; negativo: Ignorar. |
|MoveParentToFixAffinityViolation | Booleana, a predefinição é false |Dinâmica| Definição que determina se as réplicas principal podem ser movidas para corrigir as restrições de afinidade.|
|MoveExistingReplicaForPlacement | Booleana, a predefinição é verdadeiro |Dinâmica|Definição que determina se mover de réplica existente durante a colocação. |
|UseSeparateSecondaryLoad | Booleana, a predefinição é verdadeiro | Dinâmica|Definição que determina se utilizar diferentes de carga secundário. |
|PlaceChildWithoutParent | Booleana, a predefinição é verdadeiro | Dinâmica|Pode ser colocado a definição que determina se subordinado serviço réplica se nenhum réplica principal está ativo. |
|PartiallyPlaceServices | Booleana, a predefinição é verdadeiro |Dinâmica| Determina se todas as réplicas de serviço no cluster devem ser colocadas "todas ou nada" fornecido limitados nós adequado para os mesmos.|
|InterruptBalancingForAllFailoverUnitUpdates | Booleana, a predefinição é false | Dinâmica|Determina se a qualquer tipo de atualização de unidade de ativação pós-falha deve interrupção rápida ou lenta balanceamento executar. Com especificado "false" balanceamento executar será interrompido se FailoverUnit: é criado/eliminado; tenha em falta réplicas; alterar a localização de réplica primária ou alterado número de réplicas. Balanceamento de execução não será interrompido noutros casos - se FailoverUnit: possui réplicas adicionais; alterar o sinalizador qualquer réplica; alterar apenas a versão de partição ou quaisquer outras maiúsculas e minúsculas. |
|GlobalMovementThrottleThresholdPercentage|Double, a predefinição é 0|Dinâmica|Número máximo de movimentos totais permitido em fases de balanceamento e o posicionamento (expressados em percentagem do número total de réplicas do cluster) no último intervalo indicado pelo GlobalMovementThrottleCountingInterval. 0 indica sem limite. Se ambos os isto e GlobalMovementThrottleThreshold são especificadas; em seguida, é utilizado mais conservador limite.|
|GlobalMovementThrottleThresholdPercentageForBalancing|Double, a predefinição é 0|Dinâmica|Número máximo de movimentos permitido na fase de balanceamento de mensagens em fila (expressa em percentagem do número total de réplicas no PLB) o intervalo de últimos indicado pelo GlobalMovementThrottleCountingInterval. 0 indica sem limite. Se ambos os isto e GlobalMovementThrottleThresholdForBalancing são especificadas; em seguida, é utilizado mais conservador limite.|
|AutoDetectAvailableResources|booleana, a predefinição é verdadeiro|Estático|Esta configuração aciona a deteção automática de recursos disponíveis no nó (CPU e memória) quando esta configuração está definida como true - iremos ler as capacidades reais e corrija-os se o utilizador especificado as capacidades de nó incorreto ou não defini-las em todas as se esta configuração está definida como falso - iremos  um aviso que o utilizador especificado as capacidades de nó incorreto; de rastreio mas não podemos irá corrigi-los; o que significa que o utilizador pretende ter as capacidades especificadas como > que realmente tem o nó ou se as capacidades são indefinidas; assumir ilimitada capacidade |

### <a name="section-name-hosting"></a>Secção nome: de alojamento
| **Parâmetro** | **Valores permitidos** | **Política de atualização** | **Documentação de orientação ou breve descrição** |
| --- | --- | --- | --- |
| ServiceTypeRegistrationTimeout |Tempo em segundos, a predefinição é 300 |Dinâmica|Tempo máximo permitido para o ServiceType registado com recursos de infraestrutura |
| ServiceTypeDisableFailureThreshold |Número inteiro, predefinido é 1 |Dinâmica|Este é o limiar para o número de falhas de após o qual FailoverManager (FM) é notificado para desativar o tipo de serviço nesse nó e tente um nó diferente para colocação. |
| ActivationRetryBackoffInterval |Tempo em segundos, a predefinição é 5 |Dinâmica|Intervalo de término em cada caso de falha de ativação; Em cada caso de falha de ativação contínua, o sistema repete a ativação de até o MaxActivationFailureCount. O intervalo entre tentativas em cada tente é um produto de falha de ativação contínua e o intervalo de término de ativação. |
| ActivationMaxRetryInterval |Tempo em segundos, a predefinição é 300 |Dinâmica|Em cada caso de falha de ativação contínua, o sistema repete a ativação de até ActivationMaxFailureCount. ActivationMaxRetryInterval Especifica o intervalo de tempo de espera antes de repetir após cada falha de ativação |
| ActivationMaxFailureCount |Número inteiro, predefinição é 10 |Dinâmica|Número de vezes que a ativação antes de desistir de falha de tentativas de sistema |
|ActivationTimeout| TimeSpan, a predefinição é Common::TimeSpan::FromSeconds(180)|Dinâmica| Especifique timespan em segundos. O tempo limite para ativação da aplicação; desativação e a atualização. |
|ApplicationHostCloseTimeout| TimeSpan, a predefinição é Common::TimeSpan::FromSeconds(120)|Dinâmica| Especifique timespan em segundos. Quando a saída de recursos de infraestrutura é detetada num self ativado processos; FabricRuntime fechar todas as réplicas no processo de anfitrião (applicationhost) do utilizador. Este é o tempo limite para a operação de fecho. |
|ApplicationUpgradeTimeout| TimeSpan, a predefinição é Common::TimeSpan::FromSeconds(360)|Dinâmica| Especifique timespan em segundos. O tempo limite para a atualização da aplicação. Se o tempo limite é inferior ao implementador "ActivationTimeout" irá falhar. |
|CreateFabricRuntimeTimeout|TimeSpan, a predefinição é Common::TimeSpan::FromSeconds(120)|Dinâmica| Especifique timespan em segundos. O valor de tempo limite para a sincronização FabricCreateRuntime chamada |
|DeploymentMaxFailureCount|Int, a predefinição é 20| Dinâmica|Implementação de aplicação será repetida para DeploymentMaxFailureCount vezes antes de efetuar a implementação dessa aplicação no nó.| 
|DeploymentMaxRetryInterval| TimeSpan, a predefinição é Common::TimeSpan::FromSeconds(3600)|Dinâmica| Especifique timespan em segundos. Intervalo de repetição de máx. para a implementação. Em cada caso de falha contínuo no intervalo entre tentativas é calculado como mín. (DeploymentMaxRetryInterval; Número de falhas de contínua * DeploymentRetryBackoffInterval) |
|DeploymentRetryBackoffInterval| TimeSpan, a predefinição é Common::TimeSpan::FromSeconds(10)|Dinâmica|Especifique timespan em segundos. Intervalo término para a falha de implementação. Em cada caso de falha de implementação contínua, o sistema irá repetir a implementação para até o MaxDeploymentFailureCount. O intervalo entre tentativas é um produto de falha de implementação contínua e o intervalo de término de implementação. |
|EnableActivateNoWindow| booleana, a predefinição é FALSE|Dinâmica| O processo ativado é criado em segundo plano, sem qualquer consola. |
|EnableProcessDebugging|booleana, a predefinição é FALSE|Dinâmica| Permite que anfitriões de aplicação no depurador a iniciar |
|EndpointProviderEnabled| booleana, a predefinição é FALSE|Estático| Ativa a gestão de recursos de ponto final por recursos de infraestrutura. Requer a especificação de início e de fim intervalo de portas de aplicação no FabricNode. |
|FabricContainerAppsEnabled| booleana, a predefinição é FALSE|Estático| |
|FirewallPolicyEnabled|booleana, a predefinição é FALSE|Estático| Permite a abertura de portas de firewall para recursos de ponto final com portas explícitas especificadas na ServiceManifest |
|GetCodePackageActivationContextTimeout|TimeSpan, a predefinição é Common::TimeSpan::FromSeconds(120)|Dinâmica|Especifique timespan em segundos. O valor de tempo limite para as chamadas de CodePackageActivationContext. Não é aplicável aos serviços do ad-hoc. |
|IPProviderEnabled|booleana, a predefinição é FALSE|Estático|Ativa a gestão de endereços IP. |
|NTLMAuthenticationEnabled|booleana, a predefinição é FALSE|Estático| Ativa o suporte para a utilização de NTLM, os pacotes de código que estão em execução com outros utilizadores para que os processos em máquinas podem comunicar de forma segura. |
|NTLMAuthenticationPasswordSecret|SecureString, a predefinição é Common::SecureString(L"")|Estático|É um hash encriptado que é utilizado para gerar a palavra-passe para os utilizadores NTLM. Tem de ser definida se NTLMAuthenticationEnabled for VERDADEIRO. Validadas pelo implementador. |
|NTLMSecurityUsersByX509CommonNamesRefreshInterval|TimeSpan, a predefinição é Common::TimeSpan::FromMinutes(3)|Dinâmica|Especifique timespan em segundos. Definições de específico do ambiente, o intervalo periódico que alojamento verifica a existência de novos certificados a utilizar para a configuração de FileStoreService NTLM. |
|NTLMSecurityUsersByX509CommonNamesRefreshTimeout|TimeSpan, a predefinição é Common::TimeSpan::FromMinutes(4)|Dinâmica| Especifique timespan em segundos. O tempo limite para a configuração de utilizadores NTLM, utilizando nomes comum do certificado. Os utilizadores NTLM são necessárias para partilhas de FileStoreService. |
|RegisterCodePackageHostTimeout|TimeSpan, a predefinição é Common::TimeSpan::FromSeconds(120)|Dinâmica| Especifique timespan em segundos. O valor de tempo limite para a chamada de sincronização FabricRegisterCodePackageHost. Isto é aplicável apenas várias código pacote anfitriões de aplicações como FWP |
|RequestTimeout|TimeSpan, a predefinição é Common::TimeSpan::FromSeconds(30)|Dinâmica| Especifique timespan em segundos. Representa o tempo limite para a comunicação entre o anfitrião de aplicações e processos de recursos de infraestrutura para várias operações relacionadas com alojamento, tais como o registo de fábrica do utilizador registo de tempo de execução. |
|RunAsPolicyEnabled| booleana, a predefinição é FALSE|Estático| Permite que os pacotes de código a ser executado como um utilizador local que não seja o utilizador em que recursos de infraestrutura processo está em execução. Para ativar esta política de Fabric tem de executar como sistema ou utilizador que tenha SeAssignPrimaryTokenPrivilege. |
|ServiceFactoryRegistrationTimeout| TimeSpan, a predefinição é Common::TimeSpan::FromSeconds(120)|Dinâmica|Especifique timespan em segundos. O valor de tempo limite para a chamada de registo (Stateless/Stateful) ServiceFactory de sincronização |
|ServiceTypeDisableGraceInterval|TimeSpan, a predefinição é Common::TimeSpan::FromSeconds(30)|Dinâmica|Especifique timespan em segundos. Intervalo de tempo após o qual o tipo de serviço pode ser desativado |

### <a name="section-name-federation"></a>Secção nome: Federação
| **Parâmetro** | **Valores permitidos** | **Política de atualização** | **Documentação de orientação ou breve descrição** |
| --- | --- | --- | --- |
| LeaseDuration |Tempo em segundos, a predefinição é 30 |Dinâmica|Duração do que uma concessão dura entre um nó e respetivos vizinhos. |
| LeaseDurationAcrossFaultDomain |Tempo em segundos, a predefinição é 30 |Dinâmica|Duração do que uma concessão dura entre um nó e respectivos vizinhos entre domínios de falhas. |

### <a name="section-name-clustermanager"></a>Secção nome: ClusterManager
| **Parâmetro** | **Valores permitidos** | **Política de atualização** | **Documentação de orientação ou breve descrição** |
| --- | --- | --- | --- |
| UpgradeStatusPollInterval |Tempo em segundos, a predefinição é 60 |Dinâmica|A frequência de consulta para o estado de atualização da aplicação. Este valor determina a frequência de atualização para qualquer chamada GetApplicationUpgradeProgress |
| UpgradeHealthCheckInterval |Tempo em segundos, a predefinição é 60 |Dinâmica|A frequência de estado de funcionamento verifica durante uma atualização da aplicação monitorizada |
| FabricUpgradeStatusPollInterval |Tempo em segundos, a predefinição é 60 |Dinâmica|A frequência de consulta para o estado de atualização de recursos de infraestrutura. Este valor determina a frequência de atualização para qualquer chamada GetFabricUpgradeProgress |
| FabricUpgradeHealthCheckInterval |Tempo em segundos, a predefinição é 60 |Dinâmica|A frequência de estado de funcionamento de verificação durante uma atualização do Fabric monitorizada |
|InfrastructureTaskProcessingInterval | Tempo em segundos, a predefinição é 10 |Dinâmica|Especifique timespan em segundos. O intervalo de processamento utilizado pela tarefa de infraestrutura na máquina de estado de processamento. |
|TargetReplicaSetSize |Int, a predefinição é 7 |Não Permitido|TargetReplicaSetSize para ClusterManager. |
|MinReplicaSetSize |Int, a predefinição é 3 |Não Permitido|MinReplicaSetSize para ClusterManager. |
|ReplicaRestartWaitDuration |Tempo em segundos, a predefinição é (60.0 * 30)|Não Permitido|Especifique timespan em segundos. ReplicaRestartWaitDuration para ClusterManager. |
|QuorumLossWaitDuration |Tempo em segundos, a predefinição é MaxValue |Não Permitido| Especifique timespan em segundos. QuorumLossWaitDuration para ClusterManager. |
|StandByReplicaKeepDuration | Tempo em segundos, a predefinição é (3600.0 * 2)|Não Permitido|Especifique timespan em segundos. StandByReplicaKeepDuration para ClusterManager. |
|PlacementConstraints | cadeia, a predefinição é "" |Não Permitido|PlacementConstraints para ClusterManager. |
|SkipRollbackUpdateDefaultService | Booleana, a predefinição é false |Dinâmica|O CM irá ignorar reverter serviços de predefinição atualizado durante a reversão de atualização de aplicação. |
|EnableDefaultServicesUpgrade | Booleana, a predefinição é false |Dinâmica|Ative os serviços de predefinição atualizar durante a atualização da aplicação. Descrições de serviço predefinido serão substituídas após a atualização. |
|InfrastructureTaskHealthCheckWaitDuration |Tempo em segundos, a predefinição é 0|Dinâmica| Especifique timespan em segundos. A quantidade de tempo de espera antes de iniciar as verificações de estado de funcionamento após o processamento pós-cópia uma tarefa de infraestrutura. |
|InfrastructureTaskHealthCheckStableDuration | Tempo em segundos, a predefinição é 0|Dinâmica| Especifique timespan em segundos. A quantidade de tempo para observar as verificações de estado de funcionamento transmitido consecutivas antes de pós-processamento de uma tarefa de infraestrutura é concluída com êxito. Observar uma verificação de estado de funcionamento falhou irá repor este temporizador. |
|InfrastructureTaskHealthCheckRetryTimeout | Tempo em segundos, a predefinição é 60 |Dinâmica|Especifique timespan em segundos. A quantidade de tempo gaste repetir falhas de verificação de estado de funcionamento durante o processamento pós-cópia de uma tarefa de infraestrutura. Observar uma verificação de estado de funcionamento transmitido irá repor este temporizador. |
|ImageBuilderTimeoutBuffer |Tempo em segundos, a predefinição é 3 |Dinâmica|Especifique timespan em segundos. A quantidade de tempo para permitir a erros de tempo limite específico de construtor de imagens devolver ao cliente. Se esta memória intermédia é demasiado pequena; em seguida, o cliente exceder o tempo limite antes do servidor e obtém um erro de tempo limite genérico. |
|MinOperationTimeout | Tempo em segundos, a predefinição é 60 |Dinâmica|Especifique timespan em segundos. O mínimo tempo limite global para operações em ClusterManager de processamento internamente. |
|Maxoperationtimeout da |Tempo em segundos, a predefinição é MaxValue |Dinâmica| Especifique timespan em segundos. O máximo tempo limite global para operações em ClusterManager de processamento internamente. |
|MaxTimeoutRetryBuffer | Tempo em segundos, a predefinição é 600 |Dinâmica|Especifique timespan em segundos. O tempo limite da operação máximo ao repetir internamente devido a tempos limite é <Original Time out>  +  <MaxTimeoutRetryBuffer>. Limite de tempo adicional é adicionada em incrementos de MinOperationTimeout. |
|MaxCommunicationTimeout |Tempo em segundos, a predefinição é 600 |Dinâmica|Especifique timespan em segundos. O tempo limite máximo para comunicação interna entre ClusterManager e outro sistema de serviços (ou seja, Serviço de atribuição de nomes; O Gestor de ativação pós-falha e etc.). Este tempo limite deve ser inferior a maxoperationtimeout da global (uma vez que podem existir vários comunicações entre componentes do sistema para cada operação de cliente). |
|MaxDataMigrationTimeout |Tempo em segundos, a predefinição é 600 |Dinâmica|Especifique timespan em segundos. O tempo limite máximo para operações de recuperação para migração de dados após uma atualização do Fabric tiver sido efetuada. |
|MaxOperationRetryDelay |Tempo em segundos, a predefinição é 5|Dinâmica| Especifique timespan em segundos. O atraso máximo de tentativas internas quando forem encontradas falhas. |
|ReplicaSetCheckTimeoutRollbackOverride |Tempo em segundos, a predefinição é 1200 |Dinâmica| Especifique timespan em segundos. Se ReplicaSetCheckTimeout estiver definido como o valor máximo de DWORD; em seguida, é substituído com o valor desta configuração para fins de reversão. O valor utilizado para reencaminhar roll nunca é substituído. |
|ImageBuilderJobQueueThrottle |Int, a predefinição é 10 |Dinâmica|Limitação de contagem de fila de tarefas do construtor de imagens proxy nos pedidos de aplicação de thread. |
|MaxExponentialOperationRetryDelay|TimeSpan, a predefinição é Common::TimeSpan::FromSeconds(30)|Dinâmica|Especifique timespan em segundos. O atraso exponencial máximo de tentativas internas quando falhas forem encontradas repetidamente |

### <a name="section-name-defragmentationemptynodedistributionpolicy"></a>Secção nome: DefragmentationEmptyNodeDistributionPolicy
| **Parâmetro** | **Valores permitidos** |**Política de atualização**| **Documentação de orientação ou breve descrição** |
| --- | --- | --- | --- |
|PropertyGroup|KeyIntegerValueMap, a predefinição é nenhum|Dinâmica|Especifica que a desfragmentação de política segue quando emptying nós. Para uma determinada métrica 0 indica que SF deve tentar desfragmentar nós uniformemente em UDs e FDs; 1 indica apenas que os nós têm de ser defragmented |

### <a name="section-name-defragmentationmetrics"></a>Secção nome: DefragmentationMetrics
| **Parâmetro** | **Valores permitidos** |**Política de atualização**| **Documentação de orientação ou breve descrição** |
| --- | --- | --- | --- |
|PropertyGroup|KeyBoolValueMap, a predefinição é nenhum|Dinâmica|Determina o conjunto de métricas que devem ser utilizadas para desfragmentação e não para o balanceamento de carga. |

### <a name="section-name-defragmentationmetricspercentornumberofemptynodestriggeringthreshold"></a>Secção nome: DefragmentationMetricsPercentOrNumberOfEmptyNodesTriggeringThreshold
| **Parâmetro** | **Valores permitidos** |**Política de atualização**| **Documentação de orientação ou breve descrição** |
| --- | --- | --- | --- |
|PropertyGroup|KeyDoubleValueMap, a predefinição é nenhum|Dinâmica|Determina o número de nós livres que são necessárias para considerar cluster defragmented, especificando ambos por cento no intervalo [0.0-1.0) ou número de nós vazias como número de > = 1.0 |

### <a name="section-name-dnsservice"></a>Secção nome: DnsService
| **Parâmetro** | **Valores permitidos** |**Política de atualização**| **Documentação de orientação ou breve descrição** |
| --- | --- | --- | --- |
|IsEnabled|booleana, a predefinição é FALSE|Estático| |
|InstanceCount|int, a predefinição é de -1|Estático|  |

### <a name="section-name-metricactivitythresholds"></a>Secção nome: MetricActivityThresholds
| **Parâmetro** | **Valores permitidos** |**Política de atualização**| **Documentação de orientação ou breve descrição** |
| --- | --- | --- | --- |
|PropertyGroup|KeyIntegerValueMap, a predefinição é nenhum|Dinâmica|Determina o conjunto de MetricActivityThresholds para as métricas no cluster. Balanceamento funcionará se maxNodeLoad for superior a MetricActivityThresholds. Para desfragmentação métricas define a quantidade de carga igual ou inferior qual Service Fabric irá considerar o nó vazio |

### <a name="section-name-metricbalancingthresholds"></a>Secção nome: MetricBalancingThresholds
| **Parâmetro** | **Valores permitidos** |**Política de atualização**| **Documentação de orientação ou breve descrição** |
| --- | --- | --- | --- |
|PropertyGroup|KeyDoubleValueMap, a predefinição é nenhum|Dinâmica|Determina o conjunto de MetricBalancingThresholds para as métricas no cluster. Balanceamento funcionará se maxNodeLoad/minNodeLoad for superior a MetricBalancingThresholds. Desfragmentação funcionará se maxNodeLoad/minNodeLoad em, pelo menos, um DF ou UD é menor do que MetricBalancingThresholds. |

### <a name="section-name-nodebufferpercentage"></a>Secção nome: NodeBufferPercentage
| **Parâmetro** | **Valores permitidos** |**Política de atualização**| **Documentação de orientação ou breve descrição** |
| --- | --- | --- | --- |
|PropertyGroup|KeyDoubleValueMap, a predefinição é nenhum|Dinâmica|Percentagem de capacidade de nó pelo nome da métrica; utilizado como uma memória intermédia de forma a manter algumas local livre num nó para o cenário de ativação pós-falha. |

### <a name="section-name-replication"></a>Secção nome: replicação
| **Parâmetro** | **Valores permitidos** | **Política de atualização**| **Documentação de orientação ou breve descrição** |
| --- | --- | --- | --- |
|MaxCopyQueueSize|Uint, a predefinição é 1024|Estático|Este é o máximo valor define o tamanho inicial para a fila que mantém as operações de replicação.  Tenha em atenção que tem de ser uma potência de 2.  Se o crescimentos durante o tempo de execução para esta operação de tamanho de fila serão limitadas entre os de replicadores primários e secundários.|
|BatchAcknowledgementInterval|TimeSpan, a predefinição é Common::TimeSpan::FromMilliseconds(15)|Estático|Especifique timespan em segundos. Determina a quantidade de tempo que o replicador aguarda depois de receber uma operação antes de devolver uma confirmação. Outras operações recebidas durante este período de tempo terão as respetivas confirmações enviadas novamente numa única mensagem -> reduzir o tráfego de rede, mas reduzindo potencialmente o débito do replicador.|
|MaxReplicationMessageSize|Uint, a predefinição é 52428800|Estático|Tamanho da mensagem máximo de operações de replicação. A predefinição é 50MB.|
|ReplicatorAddress|cadeia, a predefinição é L "localhost:0"|Estático|O ponto final na forma de uma cadeia, 'IP:Port', que é utilizado pelo Windows Fabric replicador para estabelecer ligações com outras réplicas para operações de envio/receção.|
|ReplicatorListenAddress|cadeia, a predefinição é L "localhost:0"|Estático|O ponto final na forma de uma cadeia, 'IP:Port', que é utilizado pelo Windows Fabric replicador para receber as operações de outras réplicas.|
|ReplicatorPublishAddress|cadeia, a predefinição é L "localhost:0"|Estático|O ponto final na forma de uma cadeia, 'IP:Port', que é utilizada pelo replicador de recursos de infraestrutura do Windows para enviar operações para outras réplicas.|
|MaxPrimaryReplicationQueueSize|Uint, a predefinição é 1024|Estático|Este é o número máximo de operações que pode existir na fila de replicação primária. Tenha em atenção que tem de ser uma potência de 2.|
|MaxPrimaryReplicationQueueMemorySize|Uint, a predefinição é 0|Estático|Este é o valor máximo da fila de replicação primária em bytes.|
|MaxSecondaryReplicationQueueSize|uint, a predefinição é de 2048|Estático|Este é o número máximo de operações que pode existir na fila de replicação secundário. Tenha em atenção que tem de ser uma potência de 2.|
|MaxSecondaryReplicationQueueMemorySize|Uint, a predefinição é 0|Estático|Este é o valor máximo da fila de replicação secundária em bytes.|
|QueueHealthMonitoringInterval|TimeSpan, a predefinição é Common::TimeSpan::FromSeconds(30)|Estático|Especifique timespan em segundos. Este valor determina o período de tempo utilizado pelo replicador para monitorizar os eventos de estado de funcionamento de aviso/erro nas filas de operação de replicação. Um valor de '0' desativa a monitorização de estado de funcionamento |
|QueueHealthWarningAtUsagePercent|uint, predefinida é 80|Estático|Este valor determina a utilização de fila replicação (em percentagem) após o qual elaboramos relatórios de aviso sobre a utilização elevada da fila. Podemos fazê-lo Depois de um intervalo de tolerância de QueueHealthMonitoringInterval. Se a utilização de fila recai abaixo esta percentagem do intervalo de tolerância|
|RetryInterval|TimeSpan, a predefinição é Common::TimeSpan::FromSeconds(5)|Estático|Especifique timespan em segundos. Quando uma operação é perdida ou rejeitado este temporizador determina com que frequência o replicador tentará enviar novamente a operação.|

### <a name="section-name-transport"></a>Secção nome: transporte
| **Parâmetro** | **Valores permitidos** |**Política de atualização** |**Documentação de orientação ou breve descrição** |
| --- | --- | --- | --- |
|ResolveOption|cadeia, a predefinição é L "especificado"|Estático|Determina a forma como o FQDN for resolvido.  Os valores válidos são "não especificados/ipv4/ipv6". |
|ConnectionOpenTimeout|TimeSpan, a predefinição é Common::TimeSpan::FromSeconds(60)|Estático|Especifique timespan em segundos. Tempo limite para a configuração de ligação no lado de receção e aceitar (incluindo a negociação de segurança em modo seguro) |

## <a name="next-steps"></a>Passos seguintes
Estes artigos para obter mais informações sobre a gestão de clusters de leitura:

[Adicionar, rollover, remover certificados a partir do seu cluster do Azure](service-fabric-cluster-security-update-certs-azure.md) 


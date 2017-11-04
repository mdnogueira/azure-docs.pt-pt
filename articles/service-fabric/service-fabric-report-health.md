---
title: "Adicionar relatórios de estado de funcionamento personalizados do Service Fabric | Microsoft Docs"
description: "Descreve como enviar relatórios de estado de funcionamento personalizados para entidades de estado de funcionamento do Azure Service Fabric. Fornece recomendações para estruturar e implementar os relatórios de estado de funcionamento de qualidade."
services: service-fabric
documentationcenter: .net
author: oanapl
manager: timlt
editor: 
ms.assetid: 0a00a7d2-510e-47d0-8aa8-24c851ea847f
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/19/2017
ms.author: oanapl
ms.openlocfilehash: ed10eef347d4d93012078456b3a145589e66d30e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="add-custom-service-fabric-health-reports"></a>Adicionar relatórios personalizados de estado de funcionamento do Service Fabric
Azure Service Fabric apresenta um [modelo de estado de funcionamento](service-fabric-health-introduction.md) concebido para sinalizador cluster mau estado de funcionamento e condições de aplicação no entidades específicas. O modelo de estado de funcionamento utiliza **Informadores de estado de funcionamento** (componentes de sistema e watchdogs). O objetivo é rápido e fácil de diagnóstico e de reparação. Os escritores de serviço tem de pensar compromisso sobre estado de funcionamento. Qualquer condição que pode afetar o estado de funcionamento deve ser comunicada, especialmente se pode ajudar a problemas de sinalizador próximo raiz. As informações de estado de funcionamento podem poupar tempo e esforço na depuração e investigação. Não é especialmente limpar a utilidade assim que o serviço está a funcionar à escala na nuvem (privada ou do Azure).

O monitor do Service Fabric Informadores identificados condições de interesse. Estes relatórios sobre as condições com base na respetiva vista local. O [arquivo de estado de funcionamento](service-fabric-health-introduction.md#health-store) agrega dados de estado de funcionamento enviados por todos os Informadores para determinar se entidades são bom estado de funcionamento global. O modelo se destina a ser avançado, flexíveis e fáceis de utilizar. A qualidade dos relatórios de estado de funcionamento determina a precisão da vista de estado de funcionamento do cluster. Falsos positivos que mostram erradamente problemas mau estado de funcionamento podem afetar negativamente as atualizações ou outros serviços que utilizam dados de estado de funcionamento. Exemplos desses serviços são serviços de reparação e mecanismos de alertas. Por conseguinte, algumas profundamente é necessário para fornecer relatórios que capturam as condições de interesse da melhor forma possível.

Conceber e implementar o relatório de estado de funcionamento, watchdogs e tem de componentes do sistema:

* Defina a condição que estão interessados, a forma como é monitorizado e o impacto na funcionalidade de cluster ou aplicação. Com base nestas informações, opte por utilizar a propriedade de relatório de estado de funcionamento e o estado de funcionamento.
* Determinar o [entidade](service-fabric-health-introduction.md#health-entities-and-hierarchy) aos quais se aplica o relatório.
* Determinar onde o relatório é efectuada, partir do serviço ou a partir de um watchdog do internos ou externos.
* Defina uma origem utilizada para identificar o relatório.
* Escolha uma estratégia de criação de relatórios, periodicamente ou em transições. A forma recomendada é periodicamente, necessita de código simples e menos propenso a erros.
* Determine quanto o relatório para condições de mau estado de funcionamento deve permanecer no arquivo de estado de funcionamento e como devem ser desmarcada. Utilizando estas informações, decida tempo o relatório em direto e o comportamento de remover no expiração.

Conforme mencionado, Reporting Services podem ser efetuada a partir da:

* A réplica de serviço do Service Fabric monitorizada.
* Watchdogs internos implementados como um serviço do Service Fabric (por exemplo, um serviço sem monitorização de estado serviço Fabric que monitoriza condições e relatórios de problemas). Os watchdogs podem ser implementado um todos os nós ou pode ser com afinidade com o serviço monitorizado.
* Watchdogs internos executam em nós de Service Fabric, mas que estão *não* implementado como serviços do Service Fabric.
* Watchdogs externos que sonda o recurso de *fora* o cluster do Service Fabric (por exemplo, serviço de monitorização, como Gomez).

> [!NOTE]
> A Box, o cluster é preenchido com os relatórios de estado de funcionamento enviados pelos componentes do sistema. Leia mais em [utilizar o estado de funcionamento do sistema de relatórios para resolução de problemas](service-fabric-understand-and-troubleshoot-with-system-health-reports.md). Os relatórios do utilizador devem ser enviados [entidades de estado de funcionamento](service-fabric-health-introduction.md#health-entities-and-hierarchy) que já foram criadas pelo sistema.
> 
> 

Uma vez o estado de funcionamento reporting design está desmarcado, os relatórios de estado de funcionamento podem ser enviados facilmente. Pode utilizar [FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient) para Estado de funcionamento do relatório se o cluster não for [segura](service-fabric-cluster-security.md) ou se o cliente de fabric tem privilégios de administrador. Relatórios podem ser feito através da API por utilizando [FabricClient.HealthManager.ReportHealth](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.reporthealth), através do PowerShell ou através de REST. Botões de configuração do batch relatórios para um melhor desempenho.

> [!NOTE]
> Estado de funcionamento do relatório é síncrono e representa o trabalho de validação no lado do cliente. O facto de que o relatório é aceite pelo cliente do Estado de funcionamento ou `Partition` ou `CodePackageActivationContext` objetos não significa que é aplicada no arquivo. É enviada de forma assíncrona e, possivelmente, em lotes com outros relatórios. O processamento no servidor ainda poderão falhar: o número de sequência pode ser obsoleto, a entidade no qual o relatório tem de ser aplicado foi eliminado, etc.
> 
> 

## <a name="health-client"></a>Cliente do Estado de funcionamento
Os relatórios de estado de funcionamento são enviados para o arquivo de estado de funcionamento através de um cliente do Estado de funcionamento, que se encontrem dentro de cliente de recursos de infraestrutura. O cliente do Estado de funcionamento pode ser configurado com as seguintes definições:

* **HealthReportSendInterval**: O atraso entre a hora do relatório é adicionado ao cliente e a hora é enviado para o arquivo de estado de funcionamento. Utilizado para relatórios do batch numa única mensagem, em vez de enviar uma mensagem para cada relatório. A criação de batches melhora o desempenho. Predefinição: 30 segundos.
* **HealthReportRetrySendInterval**: O intervalo no qual o cliente do Estado de funcionamento reenvia o estado de funcionamento acumulado relatórios para o arquivo de estado de funcionamento. Predefinição: 30 segundos.
* **HealthOperationTimeout**: O período de tempo limite para um relatório de mensagem enviado para o arquivo de estado de funcionamento. Se uma mensagem exceder o tempo limite, o cliente do Estado de funcionamento repete-lo até o arquivo de estado de funcionamento confirma que o relatório foi processado. Predefinição: dois minutos.

> [!NOTE]
> Quando os relatórios são em lotes, o cliente de recursos de infraestrutura deve ser mantido activa para, pelo menos, o HealthReportSendInterval para garantir que são enviadas. Se a mensagem for perdida ou o arquivo de estado de funcionamento não é possível aplicar-lhes erros transitórios, o cliente de recursos de infraestrutura deve ser mantido activa já para atribuir-lhe a possibilidade da repetir.
> 
> 

A colocação em memória intermédia no cliente demora a exclusividade dos relatórios em consideração. Por exemplo, se um relatório incorreto específico estiver a comunicar 100 relatórios por segundo na mesma propriedade da mesma entidade, os relatórios são substituídos com a última versão. No máximo uma esses relatórios existe na fila de cliente. Se a criação de batches é configurada, o número de relatórios enviados para o arquivo de estado de funcionamento é apenas um por cada intervalo de envio. Este relatório é o último relatório adicionado, que reflete o estado mais recente da entidade.
Especifique parâmetros de configuração quando `FabricClient` é criado através da transmissão [FabricClientSettings](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclientsettings) com os valores pretendidos para entradas relacionadas com o estado de funcionamento.

O exemplo seguinte cria um cliente de recursos de infraestrutura e especifica que os relatórios devem ser enviados quando são adicionados. Em tempos limite e erros que podem ser repetidos, tentativas acontecer cada 40 segundos.

```csharp
var clientSettings = new FabricClientSettings()
{
    HealthOperationTimeout = TimeSpan.FromSeconds(120),
    HealthReportSendInterval = TimeSpan.FromSeconds(0),
    HealthReportRetrySendInterval = TimeSpan.FromSeconds(40),
};
var fabricClient = new FabricClient(clientSettings);
```

Recomendamos que mantenha a predefinição do cliente de fabric as definições, definiram `HealthReportSendInterval` para 30 segundos. Esta definição garante um desempenho ideal devido a criação de batches. Para os relatórios críticos que devem ser enviados logo que possível, utilize `HealthReportSendOptions` com Immediate `true` no [FabricClient.HealthClient.ReportHealth](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.reporthealth) API. Relatórios de imediato ignorar o intervalo de lotes. Utilize este sinalizador com cuidado; Queremos tirar partido do cliente do Estado de funcionamento de lotes sempre que possível. Enviar imediata também é útil quando está a fechar o cliente de recursos de infraestrutura (por exemplo, o processo determinou estado inválido e tem de encerrar para impedir que os efeitos secundários). Assegura um envio de melhor esforço dos relatórios acumulados. Quando é adicionado um relatório com o sinalizador de imediato, o cliente do Estado de funcionamento lotes todos os relatórios acumulados desde o último envio.

É possível especificar parâmetros mesmos quando é criada uma ligação para um cluster através do PowerShell. O exemplo seguinte inicia uma ligação a um cluster local:

```powershell
PS C:\> Connect-ServiceFabricCluster -HealthOperationTimeoutInSec 120 -HealthReportSendIntervalInSec 0 -HealthReportRetrySendIntervalInSec 40
True

ConnectionEndpoint   :
FabricClientSettings : {
                       ClientFriendlyName                   : PowerShell-1944858a-4c6d-465f-89c7-9021c12ac0bb
                       PartitionLocationCacheLimit          : 100000
                       PartitionLocationCacheBucketCount    : 1024
                       ServiceChangePollInterval            : 00:02:00
                       ConnectionInitializationTimeout      : 00:00:02
                       KeepAliveInterval                    : 00:00:20
                       HealthOperationTimeout               : 00:02:00
                       HealthReportSendInterval             : 00:00:00
                       HealthReportRetrySendInterval        : 00:00:40
                       NotificationGatewayConnectionTimeout : 00:00:00
                       NotificationCacheUpdateTimeout       : 00:00:00
                       }
GatewayInformation   : {
                       NodeAddress                          : localhost:19000
                       NodeId                               : 1880ec88a3187766a6da323399721f53
                       NodeInstanceId                       : 130729063464981219
                       NodeName                             : Node.1
                       }
```

De forma semelhante à API, relatórios podem ser enviados utilizando `-Immediate` comutador para ser enviado imediatamente, independentemente do `HealthReportSendInterval` valor.

Para REST, os relatórios são enviados para o gateway de Service Fabric, que tem um cliente de recursos de infraestrutura interna. Por predefinição, este cliente está configurado para enviar relatórios em lotes cada 30 segundos. Pode alterar o intervalo de batch com a definição de configuração de cluster `HttpGatewayHealthReportSendInterval` no `HttpGateway`. Conforme mencionado, uma melhor opção é enviar os relatórios com `Immediate` verdadeiro. 

> [!NOTE]
> Para garantir que não autorizado não é possível de relatório do Estado de funcionamento contra as entidades no cluster, configure o servidor para aceitar pedidos apenas a partir de clientes protegidos. O `FabricClient` utilizada para os relatórios têm de ter com segurança ativada para conseguir comunicar com o cluster (por exemplo, com o Kerberos ou autenticação de certificados). Leia mais sobre [cluster segurança](service-fabric-cluster-security.md).
> 
> 

## <a name="report-from-within-low-privilege-services"></a>Relatório de dentro de serviços de baixo privilégio
Se os serviços do Service Fabric não dispõe de acesso de administrador para o cluster, pode comunicar o estado de funcionamento entidades do contexto atual através de `Partition` ou `CodePackageActivationContext`.

* Para os serviços sem monitorização de estado, utilize [IStatelessServicePartition.ReportInstanceHealth](https://docs.microsoft.com/dotnet/api/system.fabric.istatelessservicepartition.reportinstancehealth) para elaborar relatórios sobre a instância de serviço atual.
* Para os serviços com monitorização de estado, utilize [IStatefulServicePartition.ReportReplicaHealth](https://docs.microsoft.com/dotnet/api/system.fabric.istatefulservicepartition.reportreplicahealth) para elaborar relatórios sobre réplica atual.
* Utilize [IServicePartition.ReportPartitionHealth](https://docs.microsoft.com/dotnet/api/system.fabric.iservicepartition.reportpartitionhealth) para elaborar relatórios sobre a entidade de partição atual.
* Utilize [CodePackageActivationContext.ReportApplicationHealth](https://docs.microsoft.com/dotnet/api/system.fabric.codepackageactivationcontext.reportapplicationhealth) para elaborar relatórios sobre a aplicação atual.
* Utilize [CodePackageActivationContext.ReportDeployedApplicationHealth](https://docs.microsoft.com/dotnet/api/system.fabric.codepackageactivationcontext.reportdeployedapplicationhealth) para elaborar relatórios sobre a aplicação atual implementada no nó atual.
* Utilize [CodePackageActivationContext.ReportDeployedServicePackageHealth](https://docs.microsoft.com/dotnet/api/system.fabric.codepackageactivationcontext.reportdeployedservicepackagehealth) para elaborar relatórios sobre um pacote de serviço para a aplicação implementada no nó atual.

> [!NOTE]
> Internamente, o `Partition` e `CodePackageActivationContext` conter um cliente do Estado de funcionamento configurado com predefinições. Conforme explicado para o [cliente do Estado de funcionamento](service-fabric-report-health.md#health-client), relatórios são compilados em batch e enviados um temporizador. Os objetos devem ser mantidos activa ter uma oportunidade para enviar o relatório.
> 
> 

Pode especificar `HealthReportSendOptions` quando enviar relatórios através de `Partition` e `CodePackageActivationContext` APIs de estado de funcionamento. Se tiver relatórios críticos que devem ser enviados logo que possível, utilize `HealthReportSendOptions` com Immediate `true`. Relatórios de imediato ignorar o intervalo de lotes do cliente interna de estado de funcionamento. Tal como mencionado anteriormente, utilize este sinalizador com cuidado; Queremos tirar partido do cliente do Estado de funcionamento de lotes sempre que possível.

## <a name="design-health-reporting"></a>Relatórios de estado de funcionamento de design
O primeiro passo na geração de relatórios de alta qualidade está a identificar as condições que podem afetar o estado de funcionamento do serviço. Quaisquer condições que podem ajudar a problemas de sinalizador num cluster de serviço ou quando é iniciado – ou mesmo melhor, antes de um problema ocorre – pode potencialmente guardar billions dos utilizados no compromisso. Os benefícios incluem menor tempo, menos noite horas despendem a investigar e reparar problemas e superior satisfação do cliente.

Depois das condições são identificadas, escritores watchdog tem de descobrir a melhor forma de monitorizá-las para equilíbrio entre sobrecarga e utilidade. Por exemplo, considere um serviço que cálculos complexos que utilizam alguns ficheiros temporários numa partilha. Um watchdog do foi possível monitorizar a partilha de Certifique-se de que existe espaço suficiente disponível. Foi possível escutar a para notificações de alterações do ficheiro ou diretório. -Foi reportar um aviso se for atingido um limiar de compromisso e reportar um erro se a partilha está cheia. Num aviso, um sistema de reparação foi possível iniciar da limpeza dos ficheiros mais antigos na partilha. Um erro, um sistema de reparação foi possível mover a réplica de serviço para outro nó. Tenha em atenção de que forma os Estados de condição são descritos em termos de estado de funcionamento: o estado da condição que pode ser considerados como estando em bom estado (ok) ou mau estado de funcionamento (aviso ou erro).

Assim que estiverem definir os detalhes de monitorização, necessita de um escritor watchdog saber como implementar o watchdog do. Se as condições podem ser determinadas a partir do serviço, o watchdog do pode fazer parte do próprio serviço monitorizado. Por exemplo, o código do serviço pode Verifique a utilização de partilha e, em seguida, comunicar sempre que tentar escrever um ficheiro. A vantagem desta abordagem é que o Reporting Services é simples. Deve ter cuidado para impedir que os erros de watchdog afetar a funcionalidade do serviço.

Relatórios a partir do serviço monitorizado não é sempre uma opção. Um Watchdog no âmbito do serviço pode não conseguir detetar as condições. Poderá não ter os dados para tomar a determinação ou lógica. A sobrecarga de monitorização as condições pode ser elevada. As condições também podem não ser específicas a um serviço, mas em vez disso, afetar as interações entre os serviços. Outra opção consiste em ter watchdogs no cluster como processos separados. Os watchdogs monitorizar as condições e o relatório, sem afetar os serviços de principais de qualquer forma. Por exemplo, estes watchdogs ser implementados como serviços sem monitorização de estado na mesma aplicação, implementados em todos os nós ou em nós do mesmos que o serviço.

Por vezes, um Watchdog em execução no cluster não é uma opção:. Se a condição monitorizada a disponibilidade ou a funcionalidade do serviço de como os utilizadores veem-lo, é melhor ter os watchdogs o mesmo que os clientes de utilizador. Não existe, estes podem testar as operações da mesma forma que os utilizadores chamá-los. Por exemplo, pode ter um watchdog do que se encontra fora do cluster, emite os pedidos para o serviço e verifica a latência e a correção do resultado. (Para um serviço de calculadora, por exemplo, 2 + 2 devolver 4 dentro de um período de tempo razoável?)

Depois de tem sido finalizados os detalhes de watchdog, deve decidir num ID de origem que identifica exclusivamente. Se vários watchdogs do mesmo tipo são living no cluster, tem um relatório sobre entidades diferentes, ou se poderem elaborar relatórios sobre a mesma entidade, utilize a propriedade ou um ID de origem diferente. Desta forma, os relatórios podem coexistir. A propriedade do relatório de estado de funcionamento deve capturar a condição monitorizada. (Por exemplo acima, a propriedade pode ser **ShareSize**.) Se aplicam vários relatórios à condição do mesma, a propriedade deve conter algumas informações dinâmicas que permite que os relatórios para coexistir. Por exemplo, se múltiplas partilhas têm de ser monitorizados, o nome da propriedade pode ser **ShareSize sharename**.

> [!NOTE]
> Efetue *não* utilizar o arquivo de estado de funcionamento para manter as informações de estado. Apenas as informações relacionadas com o estado de funcionamento devem reportadas como estado de funcionamento, como estas informações tem impacto sobre a avaliação de estado de funcionamento de uma entidade. O arquivo de estado de funcionamento não foi concebido como um arquivo para fins gerais. Utiliza a lógica de avaliação do Estado de funcionamento para agregar todos os dados para o estado de funcionamento. Enviar informações não relacionado com o estado de funcionamento (como comunicar o estado com um Estado de funcionamento de OK) não tem impacto o estado de funcionamento agregados, mas pode afetar negativamente o desempenho do arquivo de estado de funcionamento.
> 
> 

O próximo ponto de decisão é que a entidade ao relatório no. Maioria do tempo, a condição claramente idetifies a entidade. Escolha a entidade com melhores granularidade possíveis. Se uma condição afeta todas as réplicas numa partição, um relatório sobre a partição, não no serviço. Existem onde mais profundamente for necessária, apesar de nos casos extremos. Se a condição tem impacto sobre uma entidade, tal como uma réplica, mas a intenção é ter a condição sinalizados para mais do que a duração de vida de réplica, em seguida, deve ser declarada na partição. Caso contrário, quando a réplica é eliminada, o arquivo de estado de funcionamento limpa todos os seus relatórios. Escritores Watchdog tem de pensar sobre durações de entidade e o relatório. Tem de ser encriptado quando um relatório deve ser limpa a partir de um arquivo (por exemplo, quando um erro comunicado numa entidade já não se aplica).

Vamos ver um exemplo que coloca em conjunto os pontos de que posso descrito. Considere que uma aplicação de Service Fabric é composto por um serviço com monitorização de estado de persistente principal e secundários serviços sem monitorização de estado implementados em todos os nós (um tipo de serviço secundário para cada tipo de tarefa). O mestre tem uma fila de processamento que contém comandos para ser executado por secundárias. As bases de dados secundárias executar pedidos recebidos e enviar sinais de back-confirmação. Uma condição que foi monitorizada é o comprimento da fila de processamento mestre. Se o comprimento da fila principal atingir um limiar, é reportado um aviso. O aviso indica que as réplicas secundárias não é possível processar a carga. Se a fila atinge o comprimento máximo e os comandos são ignorados, é comunicado um erro, como o serviço não é possível recuperar. Os relatórios podem ser na propriedade **QueueStatus**. O watchdog do que se encontrem dentro de serviço e é enviada periodicamente na réplica primária principal. O tempo TTL é dois minutos e é enviada periodicamente a cada 30 segundos. Se o site primário ficar inativo, o relatório é automaticamente limpa da loja. Se a réplica de serviço está a funcionar, mas é a está bloqueada ou ter outros problemas, o relatório expira no arquivo de estado de funcionamento. Neste caso, a entidade é avaliada no erro.

Outra condição que pode ser monitorizada é o tempo de execução de tarefas. O mestre de distribui as tarefas sejam secundárias consoante o tipo de tarefa. Dependendo da estrutura, o mestre foi consultar secundárias para o estado da tarefa. Foi também aguarde pela para bases de dados secundárias enviar sinais de back-confirmação quando estes terminado. No segundo caso, deve ter cuidado para detetar situações em que as réplicas secundárias die ou mensagens sejam perdidas. Uma das alternativas consiste no mestre Enviar um pedido de ping para o mesmo secundária, o que envia o estado do. Se não for recebido nenhuma Estado, o mestre de considera-la uma falha e reschedules a tarefa. Este comportamento parte do princípio de que as tarefas são idempotent.

A condição monitorizada pode ser convertida como um aviso se a tarefa não é efetuada num determinado período de tempo (**t1**, por exemplo, 10 minutos). Se a tarefa não é concluída no tempo (**t2**, por exemplo, 20 minutos), a condição monitorizada pode ser convertida como erro. Este relatório pode ser feito de várias formas:

* A réplica primária mestre os relatórios no próprio periodicamente. Pode ter uma propriedade para todas as tarefas pendentes na fila. Se, pelo menos, uma tarefa demora mais, o estado do relatório na propriedade **PendingTasks** é um aviso ou erro, conforme apropriado. Se não existem tarefas pendentes ou todas as tarefas iniciou a execução, o estado do relatório está OK. As tarefas sejam persistentes. Se o site primário ficar inativo, primário recentemente promovido pode continuar a comunicar corretamente.
* Outro processo watchdog (na nuvem ou externa) verifica as tarefas (do fora, com base no resultado da tarefa pretendida) para ver se estão concluídas. Se não respeitamos aos limiares, um relatório é enviado o serviço principal. Também é enviado um relatório de cada tarefa que inclui o identificador de tarefas, como **PendingTask + taskId**. Relatórios devem ser enviados apenas em mau estado de funcionamento Estados. Definir a hora em direto para alguns minutos e marcar os relatórios a serem removidos quando expirarem para se certificar de limpeza.
* Relatórios secundário que está a executar uma tarefa quando demora mais que o esperado executá-la. Esta comunica na instância do serviço na propriedade **PendingTasks**. O relatório pinpoints a instância de serviço tem problemas, mas não captura a situação em que a instância dies. Os relatórios são, em seguida, limpa. Foi de relatório no serviço secundário. Se o elemento secundário for concluída a tarefa, a instância secundária limpa o relatório a partir da loja. O relatório não captura a situação em que a mensagem de confirmação é perdida e a tarefa estiver concluída não do ponto de vista do mestre.

No entanto, o relatório é feito nos casos descritos acima, os relatórios são capturados no estado de funcionamento da aplicação quando o estado de funcionamento é avaliado.

## <a name="report-periodically-vs-on-transition"></a>Relatório periodicamente vs em transição
Ao utilizar o modelo de relatório de estado de funcionamento, watchdogs pode enviar relatórios periodicamente ou transições. A forma recomendada para watchdog de relatórios é periodicamente, porque o código é muito mais simples e menos propensa a erros. Os watchdogs devem esforçar-nos ser tão simples quanto possível para evitar erros que acionam relatórios incorretos. Incorreto *mau estado de funcionamento* relatórios afetam os cenários de com base no estado de funcionamento, incluindo atualizações e avaliações do Estado de funcionamento. Incorreto *bom* relatórios ocultar problemas no cluster, o que não for pretendida.

Para periódica de relatórios, o watchdog do pode ser implementada com um temporizador. Uma chamada de retorno do temporizador, o watchdog do pode verificar o estado e enviar um relatório com base no estado atual. Não é necessário para ver o relatório foi enviado anteriormente ou efetuar quaisquer otimizações em termos de mensagens. O cliente do Estado de funcionamento tem lógica para o ajudar com um desempenho de criação de batches. Enquanto o cliente do Estado de funcionamento é mantido activa, internamente repete até que o relatório é reconhecido pelo arquivo de estado de funcionamento ou o watchdog do gera um relatório mais recente com a mesma entidade, propriedade e origem.

Transições de relatórios requer processamento tenha o cuidado de estado. O watchdog do monitoriza algumas condições e apenas quando as condições de alterar os relatórios. Upside desta abordagem é que são necessários menos relatórios. O downside está a lógica do watchdog do complexa. O watchdog do tem de manter as condições ou os relatórios, para que pode ser inspecionadas para determinar as alterações de estado. Na ativação pós-falha, deve ter cuidado com relatórios adicionado, mas ainda não foi enviada para o arquivo de estado de funcionamento. O número de sequência tem de ser alguma vez aumentar. Caso contrário, os relatórios são rejeitados como obsoleto. Nos casos raros onde é tarifas de perda de dados, a sincronização pode ser necessária entre o estado do relatório e o estado do arquivo de estado de funcionamento.

Elaboração de relatórios em transições faz sentido para elaboração de relatórios em si próprios, através de serviços `Partition` ou `CodePackageActivationContext`. Quando o objeto local (réplica ou de um pacote de serviço implementado / implementado aplicação) é removida, todos os respetivos relatórios são também removidos. Este limpeza automática relaxes a necessidade de sincronização entre o relatório e o arquivo de estado de funcionamento. Se o relatório destina-se a partição principal ou a aplicação principal, deve ter cuidado na ativação pós-falha para evitar relatórios obsoletos no arquivo de estado de funcionamento. Tem de ser adicionada lógica para manter o estado correto e desmarque o relatório a partir da loja quando já não é necessária.

## <a name="implement-health-reporting"></a>Implementar o relatório de estado de funcionamento
Depois dos detalhes de entidade e relatório estiverem encriptados, enviar relatórios de estado de funcionamento pode ser feito através da API, PowerShell ou REST.

### <a name="api"></a>API
Para comunicar através da API, terá de criar um relatório de estado de funcionamento específico para o tipo de entidade que pretende que seja um relatório sobre. Atribua o relatório para um cliente do Estado de funcionamento. Em alternativa, crie uma informações de estado de funcionamento e transmiti-lo para corrigir os métodos de criação de relatórios no `Partition` ou `CodePackageActivationContext` para elaborar relatórios sobre entidades atuais.

O exemplo seguinte mostra periódico de relatórios a partir de um Watchdog no cluster. O watchdog do verifica se um recurso externo pode ser acedido a partir dentro de um nó. O recurso é necessário um manifesto de serviço na aplicação. Se o recurso está indisponível, os outros serviços na aplicação podem funcionar corretamente. Por conseguinte, o relatório é enviado a entidade de pacote de serviço implementado cada 30 segundos.

```csharp
private static Uri ApplicationName = new Uri("fabric:/WordCount");
private static string ServiceManifestName = "WordCount.Service";
private static string NodeName = FabricRuntime.GetNodeContext().NodeName;
private static Timer ReportTimer = new Timer(new TimerCallback(SendReport), null, 30 * 1000, 30 * 1000);
private static FabricClient Client = new FabricClient(new FabricClientSettings() { HealthReportSendInterval = TimeSpan.FromSeconds(0) });

public static void SendReport(object obj)
{
    // Test whether the resource can be accessed from the node
    HealthState healthState = this.TestConnectivityToExternalResource();

    // Send report on deployed service package, as the connectivity is needed by the specific service manifest
    // and can be different on different nodes
    var deployedServicePackageHealthReport = new DeployedServicePackageHealthReport(
        ApplicationName,
        ServiceManifestName,
        NodeName,
        new HealthInformation("ExternalSourceWatcher", "Connectivity", healthState));

    // TODO: handle exception. Code omitted for snippet brevity.
    // Possible exceptions: FabricException with error codes
    // FabricHealthStaleReport (non-retryable, the report is already queued on the health client),
    // FabricHealthMaxReportsReached (retryable; user should retry with exponential delay until the report is accepted).
    Client.HealthManager.ReportHealth(deployedServicePackageHealthReport);
}
```

### <a name="powershell"></a>PowerShell
Enviar relatórios de estado de funcionamento com  **envio ServiceFabric*EntityType*HealthReport * *.

O exemplo seguinte mostra periódico de relatórios nos valores de CPU num nó. Os relatórios devem ser enviados a cada 30 segundos e têm um tempo TTL de dois minutos. Se expirarem, o relatório tem problemas, pelo que o nó é avaliado no erro. Quando a CPU está acima de um limiar, o relatório tem um Estado de funcionamento de aviso. Quando a CPU permanece acima de um limiar de mais do que o período de tempo configurado, é reportado como um erro. Caso contrário, o relatório envia um Estado de funcionamento de OK.

```powershell
PS C:\> Send-ServiceFabricNodeHealthReport -NodeName Node.1 -HealthState Warning -SourceId PowershellWatcher -HealthProperty CPU -Description "CPU is above 80% threshold" -TimeToLiveSec 120

PS C:\> Get-ServiceFabricNodeHealth -NodeName Node.1
NodeName              : Node.1
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='PowershellWatcher', Property='CPU', HealthState='Warning', ConsiderWarningAsError=false.

HealthEvents          :
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 5
                        SentAt                : 4/21/2015 8:01:17 AM
                        ReceivedAt            : 4/21/2015 8:02:12 AM
                        TTL                   : Infinite
                        Description           : Fabric node is up.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/21/2015 8:02:12 AM

                        SourceId              : PowershellWatcher
                        Property              : CPU
                        HealthState           : Warning
                        SequenceNumber        : 130741236814913394
                        SentAt                : 4/21/2015 9:01:21 PM
                        ReceivedAt            : 4/21/2015 9:01:21 PM
                        TTL                   : 00:02:00
                        Description           : CPU is above 80% threshold
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Warning = 4/21/2015 9:01:21 PM
```

O exemplo seguinte produz um aviso transitório numa réplica. Obtém primeiro o ID de partição e, em seguida, o ID de réplica para o serviço que é interessado no. Em seguida, envia um relatório na **PowershellWatcher** na propriedade **ResourceDependency**. O relatório é de interesse para apenas dois minutos e este é removido do arquivo de automaticamente.

```powershell
PS C:\> $partitionId = (Get-ServiceFabricPartition -ServiceName fabric:/WordCount/WordCount.Service).PartitionId

PS C:\> $replicaId = (Get-ServiceFabricReplica -PartitionId $partitionId | where {$_.ReplicaRole -eq "Primary"}).ReplicaId

PS C:\> Send-ServiceFabricReplicaHealthReport -PartitionId $partitionId -ReplicaId $replicaId -HealthState Warning -SourceId PowershellWatcher -HealthProperty ResourceDependency -Description "The external resource that the primary is using has been rebooted at 4/21/2015 9:01:21 PM. Expect processing delays for a few minutes." -TimeToLiveSec 120 -RemoveWhenExpired

PS C:\> Get-ServiceFabricReplicaHealth  -PartitionId $partitionId -ReplicaOrInstanceId $replicaId


PartitionId           : 8f82daff-eb68-4fd9-b631-7a37629e08c0
ReplicaId             : 130740415594605869
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='PowershellWatcher', Property='ResourceDependency', HealthState='Warning', ConsiderWarningAsError=false.

HealthEvents          :
                        SourceId              : System.RA
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 130740768777734943
                        SentAt                : 4/21/2015 8:01:17 AM
                        ReceivedAt            : 4/21/2015 8:02:12 AM
                        TTL                   : Infinite
                        Description           : Replica has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/21/2015 8:02:12 AM

                        SourceId              : PowershellWatcher
                        Property              : ResourceDependency
                        HealthState           : Warning
                        SequenceNumber        : 130741243777723555
                        SentAt                : 4/21/2015 9:12:57 PM
                        ReceivedAt            : 4/21/2015 9:12:57 PM
                        TTL                   : 00:02:00
                        Description           : The external resource that the primary is using has been rebooted at 4/21/2015 9:01:21 PM. Expect processing delays for a few minutes.
                        RemoveWhenExpired     : True
                        IsExpired             : False
                        Transitions           : ->Warning = 4/21/2015 9:12:32 PM
```

### <a name="rest"></a>REST
Envie relatórios de estado de funcionamento através de REST com pedidos POST, vá para a entidade pretendida e que tenham no corpo da descrição do relatório de estado de funcionamento. Por exemplo, consulte como enviar REST [relatórios de estado de funcionamento do cluster](https://docs.microsoft.com/rest/api/servicefabric/report-the-health-of-a-cluster) ou [relatórios de estado de funcionamento do serviço](https://docs.microsoft.com/rest/api/servicefabric/report-the-health-of-a-service). Todas as entidades são suportadas.

## <a name="next-steps"></a>Passos seguintes
Com base nos dados de estado de funcionamento, escritores de serviço e os administradores de cluster/aplicação podem considerar formas para consumir as informações. Por exemplo, se podem configurar alertas com base no estado de funcionamento para detetar problemas graves antes de poderem provoke falhas. Os administradores podem também configurar sistemas de reparação para corrigir problemas automaticamente.

[Introdução ao estado de funcionamento do Service Fabric monitorização](service-fabric-health-introduction.md)

[Ver relatórios de estado de funcionamento do Service Fabric](service-fabric-view-entities-aggregated-health.md)

[Como comunicar e verifique o estado de funcionamento do serviço](service-fabric-diagnostics-how-to-report-and-check-service-health.md)

[Utilizar relatórios de estado de funcionamento do sistema para resolução de problemas](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)

[Monitorizar e diagnosticar os serviços localmente](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Atualização da aplicação de Service Fabric](service-fabric-application-upgrade.md)


---
title: "Utilizar a análise de armazenamento do Azure para recolher dados de registos e as métricas | Microsoft Docs"
description: "Análise de armazenamento permite-lhe para controlar os dados de métricas para todos os serviços de armazenamento e para recolher registos de tabela, fila e Blob storage."
services: storage
documentationcenter: 
author: tamram
manager: timlt
editor: tysonn
ms.assetid: 7894993b-ca42-4125-8f17-8f6dfe3dca76
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/03/2017
ms.author: tamram
ms.openlocfilehash: 9ae9dd0b078911a695d441cd3891be720dc204ac
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="storage-analytics"></a>Análise de Armazenamento

A Análise de Armazenamento do Azure leva a cabo o registo e fornece dados métricos relativamente a uma conta de armazenamento. Pode utilizar estes dados para rastrear pedidos, analisar tendências de utilização e diagnosticar problemas relacionados com a sua conta de armazenamento.

Para utilizar a análise de armazenamento, terá de a ativar individualmente para cada serviço que pretende monitorizar. Pode ativar ao [Portal do Azure](https://portal.azure.com). Para obter mais informações, consulte [monitorizar uma conta de armazenamento no Portal do Azure](storage-monitor-storage-account.md). Também pode ativar a análise de armazenamento através de programação através da API REST ou biblioteca de clientes. Utilize o [obter propriedades de serviço Blob](https://msdn.microsoft.com/library/hh452239.aspx), [obter propriedades de serviço fila](https://msdn.microsoft.com/library/hh452243.aspx), [obter propriedades de serviço tabela](https://msdn.microsoft.com/library/hh452238.aspx), e [obter propriedades de serviço do ficheiro](https://msdn.microsoft.com/library/mt427369.aspx) operações para ativar a análise de armazenamento para cada serviço.

Os dados agregados são armazenados num blob bem conhecido (para o registo) e em tabelas bem conhecidas (para métricas), que podem ser acedidas através do serviço Blob e APIs de serviço tabela.

Análise de armazenamento tem um limite de 20 TB, a quantidade de dados armazenados, que é independentes do limite total para a sua conta de armazenamento. Para obter mais informações sobre as políticas de retenção de dados e de faturação, consulte [faturação e de análise de armazenamento](https://msdn.microsoft.com/library/hh360997.aspx). Para mais informações sobre limites de conta de armazenamento, consulte [metas de desempenho e escalabilidade do Storage do Azure](storage-scalability-targets.md).

Para obter um guia aprofundado sobre a utilização de análise de armazenamento e outras ferramentas para identificar, diagnosticar e resolver problemas relacionados com o Storage do Azure, consulte [monitorizar, diagnosticar e resolver problemas de armazenamento do Microsoft Azure](storage-monitoring-diagnosing-troubleshooting.md).

## <a name="about-storage-analytics-logging"></a>Sobre o registo de análise de armazenamento
Análise de armazenamento regista informações detalhadas sobre pedidos com êxito e a um serviço de armazenamento. Estas informações podem ser utilizadas para monitorizar os pedidos individuais e diagnosticar problemas com um serviço de armazenamento. Pedidos são registados numa base de melhor esforço.

Entradas de registo são criadas apenas se não houver atividade do serviço de armazenamento. Por exemplo, se uma conta de armazenamento tiver atividade no seu serviço Blob, mas não no respetivos serviços de tabelas ou filas, apenas os registos relacionados com o serviço Blob serão criados.

O registo de análise de armazenamento não está disponível para ficheiros do Azure.

### <a name="logging-authenticated-requests"></a>Pedidos de registo autenticado
Os seguintes tipos de pedidos autenticados são registados:

* Pedidos com êxito.
* Não foi possível pedidos, incluindo o tempo limite, limitação, rede, autorização e outros erros.
* Pedidos utilizando um acesso assinatura partilhado (SAS), incluindo pedidos falhados e bem-sucedidas.
* Pedidos de dados de análise.

Pedidos efetuados pelo armazenamento Analytics ela própria, tal como criação de registo ou eliminação, não são registados. Uma lista completa dos dados com sessão iniciada está documentada no [operações de registadas análise de armazenamento e as mensagens de estado](https://msdn.microsoft.com/library/hh343260.aspx) e [formato de registo de análise do armazenamento](https://msdn.microsoft.com/library/hh343259.aspx) tópicos.

### <a name="logging-anonymous-requests"></a>Registo de pedidos anónimos
Os seguintes tipos de pedidos anónimos estão registados:

* Pedidos com êxito.
* Erros de servidor.
* Erros de tempo limite para o cliente e o servidor.
* GET pedidos falhados com o código de erro 304 (não modificados).

Todos os outros pedidos anónimos falhados não são registados. Uma lista completa dos dados com sessão iniciada está documentada no [operações de registadas análise de armazenamento e as mensagens de estado](https://msdn.microsoft.com/library/hh343260.aspx) e [formato de registo de análise do armazenamento](https://msdn.microsoft.com/library/hh343259.aspx) tópicos.

### <a name="how-logs-are-stored"></a>Como os registos são armazenados
Todos os registos são armazenados em blobs de blocos num contentor com o nome $logs, que é criado automaticamente quando a análise de armazenamento está ativada para uma conta de armazenamento. O contentor de $logs está localizado no espaço de nomes blob da conta de armazenamento, por exemplo: `http://<accountname>.blob.core.windows.net/$logs`. Não é possível eliminar este contentor assim que tiver sido ativada a análise de armazenamento, embora o respetivo conteúdo pode ser eliminado.

> [!NOTE]
> O contentor de $logs não é apresentado quando um contentor de operação de listagem é efetuado, tais como o [ListContainers](https://msdn.microsoft.com/library/azure/dd179352.aspx) método. Tem de ser acedido diretamente. Por exemplo, pode utilizar o [ListBlobs](https://msdn.microsoft.com/library/azure/dd135734.aspx) método para aceder aos blobs a `$logs` contentor.
> Como os pedidos são registados, análise de armazenamento irá carregar resultados intermédios como blocos. Periodicamente, análise de armazenamento será confirmar estes blocos e disponibilizá-los como um blob.
> 
> 

Registos duplicados podem existir para os registos criados na mesma hora. Pode determinar se um registo é um duplicado, verificando o **RequestId** e **operação** número.

### <a name="log-naming-conventions"></a>As convenções de nomenclatura de registo
Cada registo será escrito no seguinte formato.

    <service-name>/YYYY/MM/DD/hhmm/<counter>.log

A tabela seguinte descreve cada atributo no nome do registo.

| Atributo | Descrição |
| --- | --- |
| < nome do serviço > |O nome do serviço de armazenamento. Por exemplo: blob, tabela ou fila. |
| AAAA |O ano de quatro dígitos para o registo. Por exemplo: 2011. |
| MM |O mês de dois dígitos para o registo. Por exemplo: 07. |
| DD |O mês de dois dígitos para o registo. Por exemplo: 07. |
| hh |A hora de dois dígitos que indica a hora de início para os registos, em formato UTC de 24 horas. Por exemplo: 18. |
| mm |O número de dois dígitos que indica o minuto inicial para os registos. Este valor não é suportado na versão atual da análise de armazenamento e o valor será sempre 00. |
| <counter> |Um contador com base em zero com seis dígitos que indica o número de blobs de registo gerados para o serviço de armazenamento numa hora período de tempo. Este contador é iniciado em 000000. Por exemplo: 000001. |

Segue-se um nome de registo de exemplo completo que combina os exemplos anteriores.

    blob/2011/07/31/1800/000001.log

Segue-se um exemplo URI que pode ser utilizada para aceder ao registo anterior.

    https://<accountname>.blob.core.windows.net/$logs/blob/2011/07/31/1800/000001.log

Quando um pedido de armazenamento é registado, o nome do registo resultante está correlacionada com a hora quando concluir a operação pedida. Por exemplo, se foi concluída uma requisição de GetBlob às 18:30:00 no 31/7/2011, o registo seria escrito com o seguinte prefixo:`blob/2011/07/31/1800/`

### <a name="log-metadata"></a>Metadados de registo
Todos os blobs de registo são armazenados com metadados que podem ser utilizado para identificar que dados de registo que contém o blob. A tabela seguinte descreve cada atributo de metadados.

| Atributo | Descrição |
| --- | --- |
| LogType |Descreve se o registo contém informações relativas a ler, escrever ou eliminar operações. Este valor pode incluir um tipo ou uma combinação de três, separados por vírgulas. Exemplo 1: escrever; Exemplo 2: ler, escrever; Exemplo 3: ler, escrever, eliminar. |
| StartTime |A hora mais antigo do que uma entrada no registo, no formato AAAA-MM-Aaaathh. Por exemplo: 2011-07-31T18:21:46Z. |
| endTime |A hora mais recente de uma entrada no registo, no formato AAAA-MM-Aaaathh. Por exemplo: 2011-07-31T18:22:09Z. |
| LogVersion |A versão do formato de registo. Atualmente, o único valor suportado é 1.0. |

A lista seguinte apresenta os metadados de exemplo completo utilizando os exemplos anteriores.

* LogType = escrita
* StartTime = 2011-07-31T18:21:46Z
* EndTime = 2011-07-31T18:22:09Z
* LogVersion = 1.0

### <a name="accessing-logging-data"></a>Aceder aos dados de registo
Todos os dados no `$logs` contentor podem ser acedidos utilizando as APIs do serviço Blob, incluindo as APIs de .NET fornecido pelo Azure biblioteca gerida. O administrador de conta de armazenamento pode ler e eliminar os registos, mas não é possível criar ou atualizá-las. Os metadados do registo e o nome do registo podem ser utilizados quando consultar um registo. É possível que os registos de uma determinada hora aparecer fora de ordem, mas os metadados sempre Especifica o período de tempo de entradas de registo num registo. Por conseguinte, pode utilizar uma combinação de metadados e os nomes dos registos ao procurar um registo específico.

## <a name="about-storage-analytics-metrics"></a>Sobre métricas da análise de armazenamento
Análise de armazenamento pode armazenar métricas que incluem dados de capacidade e estatísticas agregadas de transações sobre pedidos para um serviço de armazenamento. As transações são reportadas ao ambos o nível de operação de API, bem como a nível de serviço de armazenamento e a capacidade é comunicada a nível de serviço de armazenamento. Dados de métricas, podem ser utilizados para analisar a utilização do serviço de armazenamento, diagnosticar problemas com pedidos efetuados contra o serviço de armazenamento e para melhorar o desempenho das aplicações que utilizam um serviço.

Para utilizar a análise de armazenamento, terá de a ativar individualmente para cada serviço que pretende monitorizar. Pode ativar ao [Portal do Azure](https://portal.azure.com). Para obter mais informações, consulte [monitorizar uma conta de armazenamento no Portal do Azure](storage-monitor-storage-account.md). Também pode ativar a análise de armazenamento através de programação através da API REST ou biblioteca de clientes. Utilize o **obter propriedades de serviço** operações para ativar a análise de armazenamento para cada serviço.

### <a name="transaction-metrics"></a>Métricas de transação
Um conjunto robusto de dados é registado em intervalos por hora ou minutos, para cada serviço de armazenamento e a operação de API, incluindo a entrada/saída, disponibilidade, erros, solicitada e categorizado percentagens de pedido. Pode ver uma lista completa dos detalhes da transação no [armazenamento esquema da tabela de métricas de análise](https://msdn.microsoft.com/library/hh343264.aspx) tópico.

Dados de transação são registados em dois níveis – o nível de serviço e o nível de operação de API. O nível de serviço, estatísticas resumir todas pedida operações de API são escritas para uma entidade de tabela a cada hora, mesmo que não existem pedidos foram efetuados para o serviço. Ao nível da operação de API, as estatísticas só são escritas para uma entidade se a operação pedida dentro dessa hora.

Por exemplo, se efetuar uma **GetBlob** operação no serviço de Blob, as métricas do Storage Analytics irá iniciar o pedido e incluí-la nos dados agregados para o serviço Blob, bem como o **GetBlob** operação. No entanto, se não **GetBlob** operação é solicitada durante a hora, uma entidade será não ser escrita para o `$MetricsTransactionsBlob` para essa operação.

Métricas de transação são registadas para pedidos de utilizadores e pedidos efetuados através da análise de armazenamento em si. Por exemplo, são registados pedidos por análise de armazenamento para escrever os registos e as entidades da tabela. Para obter mais informações sobre a forma como estes pedidos são cobrados, consulte [faturação e de análise de armazenamento](https://msdn.microsoft.com/library/hh360997.aspx).

### <a name="capacity-metrics"></a>Métricas de capacidade
> [!NOTE]
> Atualmente, as métricas de capacidade só estão disponíveis para o serviço Blob. Métricas de capacidade para o serviço tabela e o serviço de fila estará disponíveis em versões futuras de análise de armazenamento.
> 
> 

Dados de capacidade estão registados diariamente para serviço de Blob de uma conta de armazenamento e duas entidades de tabela são escritas. Uma entidade fornece estatísticas para dados de utilizador e o outro fornece estatísticas sobre a `$logs` contentor de blob utilizado pela análise de armazenamento. O `$MetricsCapacityBlob` tabela inclui as estatísticas seguintes:

* **Capacidade**: A quantidade de armazenamento utilizada pelo serviço de Blob a conta de armazenamento, em bytes.
* **ContainerCount**: O número de contentores do blob no serviço de Blob a conta de armazenamento.
* **ObjectCount**: O número de consolidada e não consolidados página de blocos ou blobs no serviço de Blob a conta de armazenamento.

Para obter mais informações sobre as métricas de capacidade, consulte [armazenamento esquema da tabela de métricas de análise](https://msdn.microsoft.com/library/hh343264.aspx).

### <a name="how-metrics-are-stored"></a>Como as métricas são armazenadas
Todos os dados de métricas para cada um dos serviços de armazenamento são armazenados nas três tabelas reservadas para que o serviço: uma tabela para informações sobre transações, uma tabela para informações sobre transações minuto e outra tabela para obter informações de capacidade. Informações de transação de transação e minuto constituem em dados de pedido e resposta e informações de capacidade consiste em armazenamento dados de utilização. Métricas de hora, minutos métricas e capacidade para o serviço de Blob de uma conta de armazenamento podem ser acedidos em tabelas com o nome, tal como descrito na seguinte tabela.

| Nível de métricas | Nomes das tabelas | Versões suportadas |
| --- | --- | --- |
| Métricas de hora a hora, localização principal |$MetricsTransactionsBlob <br/>$MetricsTransactionsTable <br/> $MetricsTransactionsQueue |Versões anteriores a 2013-08-15 apenas. Embora estes nomes ainda sejam suportados, recomenda-se que mude para utilizar as tabelas listadas abaixo. |
| Métricas de hora a hora, localização principal |$MetricsHourPrimaryTransactionsBlob <br/>$MetricsHourPrimaryTransactionsTable <br/>$MetricsHourPrimaryTransactionsQueue |Todas as versões, incluindo 2013-08-15. |
| Métricas de minutos, localização principal |$MetricsMinutePrimaryTransactionsBlob <br/>$MetricsMinutePrimaryTransactionsTable <br/>$MetricsMinutePrimaryTransactionsQueue |Todas as versões, incluindo 2013-08-15. |
| Métricas de hora a hora, localização secundária |$MetricsHourSecondaryTransactionsBlob <br/>$MetricsHourSecondaryTransactionsTable <br/>$MetricsHourSecondaryTransactionsQueue |Todas as versões, incluindo 2013-08-15. Deve ser ativada a replicação georredundante de acesso de leitura. |
| Métricas de minutos, localização secundária |$MetricsMinuteSecondaryTransactionsBlob <br/>$MetricsMinuteSecondaryTransactionsTable <br/>$MetricsMinuteSecondaryTransactionsQueue |Todas as versões, incluindo 2013-08-15. Deve ser ativada a replicação georredundante de acesso de leitura. |
| Capacidade (apenas ao serviço Blob) |$MetricsCapacityBlob |Todas as versões, incluindo 2013-08-15. |

Estas tabelas são criadas automaticamente quando a análise de armazenamento está ativada para uma conta de armazenamento. Estes são acedidos através de espaço de nomes da conta de armazenamento, por exemplo:`https://<accountname>.table.core.windows.net/Tables("$MetricsTransactionsBlob")`

### <a name="accessing-metrics-data"></a>Aceder aos dados de métricas
Todos os dados nas tabelas métricas podem ser acedidos utilizando as APIs do serviço tabela, incluindo as APIs de .NET fornecido pelo Azure biblioteca gerida. O administrador de conta de armazenamento pode ler e eliminar entidades de tabela, mas não é possível criar ou atualizá-las.

## <a name="billing-for-storage-analytics"></a>Faturação para análise de armazenamento
Todos os dados de métricas é escrito pelos serviços de uma conta de armazenamento. Como resultado, cada operação de escrita efetuada através da análise de armazenamento está sujeito a faturação. Além disso, a quantidade de armazenamento utilizado por dados de métricas também está sujeito a faturação.

As seguintes ações executadas pelo armazenamento Analytics estão sujeito a faturação:

* Pedidos para criar os blobs para o registo. 
* Pedidos para criar as entidades da tabela de métricas.

Se tiver configurado uma política de retenção de dados, não lhe serem cobrados para eliminar transações quando a análise de armazenamento elimina dados antigos de métricas e registo. No entanto, transações de eliminação de um cliente estão sujeito a faturação. Para obter mais informações sobre as políticas de retenção, consulte [definir uma política de retenção de dados de análise do armazenamento](https://msdn.microsoft.com/library/azure/hh343263.aspx).

### <a name="understanding-billable-requests"></a>Noções sobre pedidos faturáveis
Todos os pedidos efetuados ao serviço de uma conta de armazenamento está sujeito a faturação ou não faturável. Os registos de análise de armazenamento de cada pedido individual efetuado a um serviço, incluindo uma mensagem de estado que indica como o pedido foi processado. Da mesma forma, a análise de armazenamento armazena as métricas para um serviço e as operações de API do que o serviço, incluindo o percentagens e a contagem de determinadas mensagens de estado. Em conjunto, estas funcionalidades podem ajudar a analisar os pedidos de sujeito a faturação, fazer melhorias na sua aplicação e diagnosticar problemas com pedidos para os serviços. Para mais informações sobre faturação, consulte [compreender Azure Storage de faturação - largura de banda, as transações e a capacidade](http://blogs.msdn.com/b/windowsazurestorage/archive/2010/07/09/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity.aspx).

Quando observar os dados de análise de armazenamento, pode utilizar as tabelas a [operações de registadas análise de armazenamento e as mensagens de estado](https://msdn.microsoft.com/library/azure/hh343260.aspx) tópico para determinar os pedidos são sujeito a faturação. Em seguida, pode comparar os registos e dados de métricas para as mensagens de estado para ver se foram cobrados para um pedido específico. Também pode utilizar as tabelas existentes no tópico anterior para investigar disponibilidade para um serviço de armazenamento ou a operação de API individual.

## <a name="next-steps"></a>Passos seguintes
### <a name="setting-up-storage-analytics"></a>Configurar a análise de armazenamento
* [Monitorizar uma conta de armazenamento no Portal do Azure](storage-monitor-storage-account.md)
* [Ativar e configurar a análise de armazenamento](https://msdn.microsoft.com/library/hh360996.aspx)

### <a name="storage-analytics-logging"></a>Registo de análise do armazenamento
* [Sobre o registo de análise de armazenamento](https://msdn.microsoft.com/library/hh343262.aspx)
* [Formato de registo de análise de armazenamento](https://msdn.microsoft.com/library/hh343259.aspx)
* [Análise de armazenamento registadas operações e mensagens de estado](https://msdn.microsoft.com/library/hh343260.aspx)

### <a name="storage-analytics-metrics"></a>Métricas do Storage Analytics
* [Sobre as métricas do Storage Analytics](https://msdn.microsoft.com/library/hh343258.aspx)
* [Esquema de tabela de métricas de análise de armazenamento](https://msdn.microsoft.com/library/hh343264.aspx)
* [Análise de armazenamento registadas operações e mensagens de estado](https://msdn.microsoft.com/library/hh343260.aspx)  


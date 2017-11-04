---
title: Mover dados entre bases de dados de nuvem de escalamento horizontal | Microsoft Docs
description: "Explica como manipular shards e mover dados através de um serviço alojado automática utilizando APIs de base de dados elástica."
services: sql-database
documentationcenter: 
manager: jhubbard
author: ddove
ms.assetid: 204fd902-0397-4185-985a-dea3ed7c7d9f
ms.service: sql-database
ms.custom: scale out apps
ms.workload: Inactive
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2016
ms.author: ddove
ms.openlocfilehash: 328989c4fc1f9a404d4c048eb148a95e9105bdf5
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/31/2017
---
# <a name="moving-data-between-scaled-out-cloud-databases"></a>Mover dados entre bases de dados de nuvem aumentadas horizontalmente
Se for um Software como um programador de serviço e subitamente a sua aplicação sofre a pedido imenso, terá de acomodar o crescimento. Para adicionar mais bases de dados (shards). Como redistribuir os dados para as novas bases de dados sem perturbar a integridade dos dados? Utilize o **ferramenta de intercalação de divisão** para mover dados de bases de dados restrita para as bases de dados de novo.  

A ferramenta de divisão de intercalação é executado como um serviço web do Azure. Um administrador ou programador utiliza a ferramenta para mover shardlets (dados a partir de um ID de partição horizontal) entre bases de dados diferentes (shards). A ferramenta utiliza a gestão de mapa de partições horizontais para manter a base de dados de metadados do serviço e certifique-se consistente mapeamentos.

![Descrição geral][1]

## <a name="download"></a>Transferência
[Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge](http://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge/)

## <a name="documentation"></a>Documentação
1. [Tutorial de ferramenta de intercalação de divisão de base de dados elástica](sql-database-elastic-scale-configure-deploy-split-and-merge.md)
2. [Configuração de segurança de divisão de intercalação](sql-database-elastic-scale-split-merge-security-configuration.md)
3. [Considerações de segurança de divisão de intercalação](sql-database-elastic-scale-split-merge-security-configuration.md)
4. [Gestão de mapas de partições horizontais](sql-database-elastic-scale-shard-map-management.md)
5. [Migrar bases de dados existentes para aumentar horizontalmente](sql-database-elastic-convert-to-use-elastic-tools.md)
6. [Ferramentas de base de dados elástica](sql-database-elastic-scale-introduction.md)
7. [Glossário de ferramentas de base de dados elástico](sql-database-elastic-scale-glossary.md)

## <a name="why-use-the-split-merge-tool"></a>Porquê utilizar a ferramenta de intercalação de divisão?
**Flexibilidade**

As aplicações precisam de forma flexível stretch que ultrapassem os limites de uma única base de dados de BD SQL do Azure. Utilize a ferramenta para mover os dados conforme necessário para novas bases de dados, mantendo o integridade.

**Dividir a crescer** 

Precisa de aumentar a capacidade global para processar explosive crescimento. Para tal, crie capacidade adicional através da fragmentação os dados e distribui-lo em bases de dados de forma incremental mais até que as necessidades de capacidade são verdadeiras. Este é um exemplo de prime da funcionalidade 'Dividir'. 

**Intercalar para reduzir**

Capacidade tem de reduzir devido à natureza sazonais de uma empresa. A ferramenta permite-lhe reduzir verticalmente para unidades de escala menos quando abrandar de negócio. A funcionalidade 'merge' no serviço de intercalação de divisão do dimensionamento elástico aborda este requisito. 

**Gerir hotspots movendo shardlets**

Com múltiplos inquilinos por base de dados, a alocação de shardlets para shards pode levar a congestionamentos de capacidade em algumas shards. É necessário voltar a atribuir shardlets ou movendo shardlets ocupado para novos ou menos sobreutilizados shards. 

## <a name="concepts--key-features"></a>Conceitos e as principais funcionalidades
**Serviços alojados de cliente**

A intercalação de divisão é fornecida como um serviço alojado de cliente. Tem de implementar e alojar o serviço na sua subscrição do Microsoft Azure. O pacote que transferiu a partir do NuGet contém um modelo de configuração para concluir com as informações para a sua implementação específica. Consulte o [tutorial de divisão de intercalação](sql-database-elastic-scale-configure-deploy-split-and-merge.md) para obter mais detalhes. Uma vez que o serviço é executado na sua subscrição do Azure, pode controlar e configurar a maioria dos aspetos de segurança do serviço. O modelo predefinido inclui opções para configurar o SSL, autenticação de cliente baseada em certificados, a encriptação de credenciais armazenadas, DoS guarding e restrições de IP. Pode encontrar mais informações sobre os aspetos de segurança no documento seguinte [configuração de segurança de divisão de intercalação](sql-database-elastic-scale-split-merge-security-configuration.md).

A predefinição implementado execuções de serviço com um trabalho e uma função da web. Cada utiliza o tamanho da A1 VM serviços Cloud do Azure. Enquanto que não pode modificar estas definições quando implementar o pacote, pode alterá-los após uma implementação com êxito no serviço em nuvem em execução, (através do portal do Azure). Tenha em atenção que a função de trabalho não deve ser configurada mais de uma única instância, por motivos de técnicos. 

**Integração de mapa de partições horizontais**

O serviço de divisão de intercalação interage com o mapa de partições horizontais da aplicação. Quando utilizar o serviço de divisão de intercalação para dividir ou intervalos de intercalação ou mover shardlets entre shards, o serviço automaticamente mantém o mapa de partições horizontais atualizado. Para tal, o serviço estabelece ligação com o ID de partição horizontal mapa Gestor da base de dados da aplicação e mantém intervalos e mapeamentos como progresso de divisão/intercalação/mover pedidos. Isto garante que o mapa de partições horizontais sempre apresenta uma vista atualizada quando são acontecimentos operações de divisão de intercalação. As operações de movimento de intercalação e shardlet dividir, são implementadas por mover um lote de shardlets de ID de partição horizontal de origem para as partições horizontais de destino. Durante a operação de movimento shardlet shardlets sujeitos o lote atual estão marcadas como offline no mapa de partições horizontais e não estão disponíveis para ligações de encaminhamento de dados dependentes utilizando o **OpenConnectionForKey** API. 

**Ligações de shardlet consistente**

Quando inicia o movimento de dados para um lote novo de shardlets, quaisquer ligações de encaminhamento de dados dependentes de mapa de partições horizontais fornecido para a partição horizontal armazenar o shardlet são desativadas e subsequentes ligações a partir do mapa de partições horizontais APIs para esses shardlets estão bloqueadas enquanto o movimento de dados está em curso para evitar inconsistências. Ligações para outros shardlets no ID de partição horizontal mesmo também irá obter desativadas, mas será concluída com êxito novamente imediatamente da repetição. Assim que o batch for movido, os shardlets estão marcados online novamente para o ID de partição horizontal destino e a origem de dados é removida de partições horizontais de origem. O serviço realiza estes passos para cada lote até que todos os shardlets terem sido movidos. Isto direciona para várias operações de eliminação de ligação no decorrer de concluir a operação de intercalação/divisão/mover.  

**Gerir a disponibilidade de shardlet**

Limitar a ligação o cancelamento de lote atual de shardlets conforme mencionado acima restringe o âmbito de indisponibilidade para um lote de shardlets cada vez. Este é preferencial através de uma abordagem em que o ID de partição horizontal concluída permanecerá offline para todos os respetivos shardlets no decorrer de uma operação de intercalação ou divisão. O tamanho do lote, definido como o número de shardlets distinta para mover um momento, é um parâmetro de configuração. Pode ser definido para cada operação de divisão e intercalação consoante as necessidades de disponibilidade e o desempenho da aplicação. Tenha em atenção que o intervalo que está a ser bloqueado no mapa de partições horizontais pode ser maior do que o tamanho do lote especificado. Isto acontece porque o serviço escolhe o tamanho do intervalo de forma a que o número real de valores de chave de fragmentação nos dados aproximadamente corresponde o tamanho de lote. Isto é importante lembrar-se em particular para as chaves de fragmentação sparsely povoada. 

**Armazenamento de metadados**

O serviço de divisão de intercalação utiliza uma base de dados para manter o respetivo estado e manter registos durante o processamento do pedido. O utilizador cria esta base de dados na sua subscrição e fornece a cadeia de ligação para o mesmo no ficheiro de configuração para a implementação de serviço. Os administradores da organização do utilizador também podem ligar a esta base de dados para rever o progresso de pedido e para obter informações detalhadas sobre potenciais falhas de investigar.

**Deteção de fragmentação**

O serviço de divisão de intercalação distingue entre (1) a tabelas, as tabelas de referência (2) e (3) normais tabelas. A semântica de uma operação de intercalação/divisão/movimentação dependem do tipo de tabela utilizado e é definida do seguinte modo: 

* **Tabelas em partição horizontal**: as operações de movimentação, intercalação e divisão movem shardlets de origem para partições horizontais de destino. Após a conclusão com êxito do pedido geral, esses shardlets já não estão presentes na origem. Tenha em atenção que as tabelas de destino têm de existir no ID de partição horizontal de destino e não pode conter os dados no intervalo de destino antes do processamento da operação. 
* **As tabelas de referência**: as tabelas de referência, a divisão de intercalação e mover operações copiar os dados da origem para a partição horizontal de destino. Tenha em atenção, no entanto, de que não existem alterações ocorrem de partições horizontais destino para uma determinada tabela se qualquer linha já se encontra presente nesta tabela no destino. A tabela tem de estar em branco em nenhuma operação de cópia de tabela de referência processadas.
* **Outras tabelas**: outras tabelas podem estar presentes na origem ou destino de uma operação de divisão e intercalação. O serviço de divisão de intercalação disregards estas tabelas para o movimento de dados ou operações de cópia. Tenha em atenção, no entanto, que pode interferir com estas operações em caso de restrições.

As informações de referência vs tabelas em partição horizontal são fornecidas pelo **SchemaInfo** APIs no mapa de partições horizontais. O exemplo seguinte ilustra a utilização destas APIs numa determinada partição horizontal mapa manager objeto smm: 

    // Create the schema annotations 
    SchemaInfo schemaInfo = new SchemaInfo(); 

    // Reference tables 
    schemaInfo.Add(new ReferenceTableInfo("dbo", "region")); 
    schemaInfo.Add(new ReferenceTableInfo("dbo", "nation")); 

    // Sharded tables 
    schemaInfo.Add(new ShardedTableInfo("dbo", "customer", "C_CUSTKEY")); 
    schemaInfo.Add(new ShardedTableInfo("dbo", "orders", "O_CUSTKEY")); 

    // Publish 
    smm.GetSchemaInfoCollection().Add(Configuration.ShardMapName, schemaInfo); 

As tabelas 'region' e 'nation' estão definidas como tabelas de referência e serão copiados com operações de divisão/intercalação/mover. 'Cliente' e 'ordens' por sua vez são definidas como tabelas em partição horizontal. C_CUSTKEY e O_CUSTKEY servem como a chave de fragmentação. 

**Integridade referencial**

O serviço de divisão de intercalação analisa as dependências entre tabelas e utiliza as relações de chave primária de chave externas para testar as operações para mover as tabelas de referência e shardlets. Em geral, as tabelas de referência são copiadas primeiro por ordem de dependência, em seguida, shardlets são copiados por ordem das respetivas dependências em cada lote. Isto é necessário para que as restrições de FK-PK no ID de partição horizontal destino são cumpridas como os novos dados são recebidos. 

**Consistência de mapa de partições horizontais e Eventual conclusão**

Na presença de falhas, o serviço de divisão de intercalação retoma operações após qualquer falha e visa conclua quaisquer nos pedidos de progresso. No entanto, poderão existir situações irrecuperáveis, por exemplo, quando as partições horizontais de destino for roubado ou comprometido para além de reparação. Esses circunstâncias, alguns shardlets que foram deveria ser movida pode continuar a residir no ID de partição horizontal de origem. O serviço garante que shardlet mapeamentos são actualizados apenas depois dos dados necessários foram copiados com êxito para o destino. Shardlets apenas são eliminados na origem depois de todos os respetivos dados foi copiado para o destino e os mapeamentos de correspondentes foram atualizados com êxito. A operação de eliminação ocorre em segundo plano enquanto o intervalo já se encontra online no ID de partição horizontal de destino. O serviço de divisão de intercalação garante sempre correcção dos mapeamentos armazenados no mapa de partições horizontais.

## <a name="the-split-merge-user-interface"></a>A interface de utilizador de divisão de intercalação
O pacote de serviço de divisão de intercalação inclui uma função de trabalho e uma função da web. A função da web é utilizada para submeter pedidos de divisão de intercalação de uma forma interativa. Componentes principais da interface de utilizador são os seguintes:

* Tipo de operação: O tipo de operação é um botão de opção que controla o tipo de operação efetuada pelo serviço para este pedido. Pode escolher entre a divisão de intercalação e mover cenários. Também pode cancelar uma operação anteriormente submetida. Pode utilizar a divisão, intercalar e mover pedidos para o intervalo de partição horizontal maps. ID de partição horizontal lista mapeia apenas operações de movimentação de suporte.
* Mapa de partições horizontais: A secção seguinte dos parâmetros do pedido abrangem informações sobre o mapa de partições horizontais e a base de dados que aloja o mapa de partições horizontais. Em particular, terá de fornecer o nome do servidor da SQL Database do Azure e da base de dados que aloja o shardmap, as credenciais para ligar à base de dados de mapa de partições horizontais e, finalmente, o nome do mapa de partições horizontais. Atualmente, a operação só aceita um único conjunto de credenciais. Estas credenciais têm de ter permissões suficientes para efetuar alterações para o mapa de partições horizontais, bem como os dados de utilizador de partições horizontais.
* Intervalo de origem (de divisão e intercalação): uma operação de divisão e intercalação processa um intervalo utilizando a respetiva chave baixa e alta. Para especificar uma operação com um valor de chave elevado unbounded, marque a caixa de verificação "chave superior é máximo" e deixar o campo de chave elevado vazio. Os valores de chave do intervalo que especificou não é necessário corresponder precisamente um mapeamento e os respetivos limites no seu mapa de partições horizontais. Se não especificar quaisquer limites de intervalo de todo o serviço irá inferir o intervalo mais próximo para si automaticamente. Pode utilizar o script do GetMappings.ps1 PowerShell para obter os mapeamentos de atuais num mapa de partições horizontais indicado.
* Comportamento de origem de divisão (divisão): para operações de divisão, definir o ponto de dividir o intervalo de origem. Para tal, fornecendo a chave de fragmentação onde pretende que a divisão ocorrer. Utilize o botão de opção de especificar se pretende que a parte inferior do intervalo (excluindo a chave de divisão) para mover, ou se pretender que a parte superior para mover (incluindo a chave de divisão).
* Shardlet (mover) de origem: mover operações são diferentes das operações de intercalação ou divisão como não necessitam de um intervalo para descrever a origem. Uma origem para mover simplesmente é identificada pelo valor de chave de fragmentação planear mover.
* Destino de partições horizontais (divisão): uma vez que forneceu as informações na origem da sua operação dividido, tem de definir onde pretende que os dados a ser copiado para ao fornecer o nome de servidor e base de dados de BD SQL do Azure para o destino.
* Intervalo de destino (intercalação): operações de intercalação mover shardlets para um ID de partição horizontal existente. Identificar o ID de partição horizontal existente, fornecendo os limites de intervalo do intervalo existente que pretenda a intercalação com.
* Tamanho do lote: O tamanho do lote controla o número de shardlets passará offline num momento durante o movimento de dados. Este é um valor inteiro em que pode utilizar valores mais pequenos quando estiver confidenciais longos períodos de indisponibilidade para shardlets. Valores maiores irão aumentar o tempo de um determinado shardlet mas offline poderão melhorar o desempenho.
* Id da operação (Cancelar): Se tiver uma operação em curso que não são necessários, pode cancelar a operação, fornecendo o respetivo ID de operação neste campo. Pode obter o ID de operação da tabela de estado de pedido (consulte a secção 8.1) ou da saída no browser onde submeteu o pedido.

## <a name="requirements-and-limitations"></a>Requisitos e limitações
A implementação atual do serviço de divisão de intercalação está sujeita aos seguintes requisitos e limitações: 

* Os shards tem de existir e estar registados no mapa de partições horizontais antes de uma operação de intercalação de divisão nestes shards pode ser efetuada. 
* O serviço não criar tabelas ou outros objetos de base de dados automaticamente como parte das suas operações. Isto significa que o esquema para a todas as tabelas e as tabelas de referência tem de existir nas partições horizontais destino antes de qualquer operação de intercalação/divisão/movimentação. Tabelas em partição horizontal em particular têm de estar em branco no intervalo onde shardlets novo estão a ser adicionado por uma operação de intercalação/divisão/mover. Caso contrário, a operação falhará a verificação de consistência inicial no ID de partição horizontal de destino. Tenha também em atenção que dados são copiados de apenas se a referência de tabela estiver vazia e que não existem sem garantias de consistência em relação a outras simultâneas operações de escrita em tabelas de referência de referência. Recomendamos esta: ao executar operações de divisão/intercalação, sem outras operações de escrita altera as tabelas de referência.
* O serviço baseia-se na identidade de linha estabelecida através de um índice exclusivo ou uma chave que inclua a chave de fragmentação para melhorar o desempenho e fiabilidade para shardlets grandes. Isto permite que o serviço mover dados granularidade melhorar, mesmo que apenas o valor da chave de fragmentação. Isto ajuda a reduzir a quantidade máxima de espaço de registo e de bloqueios que são necessários durante a operação. Considere criar um índice exclusivo ou uma chave primária, incluindo a chave de fragmentação numa determinada tabela se pretende utilizar essa tabela com pedidos de divisão/intercalação/mover. Por motivos de desempenho, a chave de fragmentação deve ser a coluna à esquerda na chave ou o índice.
* No decorrer do processamento do pedido, alguns dados shardlet podem existir ambos na origem e de partições horizontais de destino. Isto é necessário para proteger contra falhas durante o movimento shardlet. A integração da divisão de intercalação com o mapa de partições horizontais assegura que as ligações através de dados dependentes encaminhamento APIs utilizando o **OpenConnectionForKey** método no mapa do ID de partição horizontal não vir quaisquer Estados intermédios inconsistentes. No entanto, ao estabelecer ligação com a origem ou os shards de destino sem utilizar o **OpenConnectionForKey** método, Estados intermédios inconsistentes poderão estar visíveis quando são acontecimentos divisão/intercalação/mover pedidos. Estas ligações podem mostrar resultados parciais ou duplicados, consoante o período de tempo ou de partições horizontais subjacente de ligação. Esta limitação atualmente inclui as ligações efetuadas pelo horizontal elástico várias-Shard-consultas.
* A base de dados de metadados para o serviço de divisão de intercalação não tem de ser partilhada entre diferentes funções. Por exemplo, uma função do serviço de divisão de intercalação em execução no tem de apontar para uma base de dados de metadados diferentes que a função de produção de teste.

## <a name="billing"></a>Faturação
O serviço de divisão de intercalação é executado como um serviço em nuvem na sua subscrição do Microsoft Azure. Por conseguinte para serviços em nuvem são aplicáveis encargos à sua instância do serviço. A menos que frequentemente efetuar operações de divisão/intercalação/mover, recomendamos a que eliminar o serviço de nuvem de divisão de intercalação. Que guarda os custos para executar ou implementados instâncias de serviço de nuvem. Pode voltar a implementar e iniciar a configuração prontamente passíveis de execução sempre que necessitar de efetuar operações de intercalação ou divisão. 

## <a name="monitoring"></a>Monitorização
### <a name="status-tables"></a>Tabelas de estado
O serviço de divisão de intercalação fornece o **RequestStatus** tabela na base de dados de arquivo de metadados para a monitorização de pedidos concluídos e contínuas. A tabela apresenta uma linha para cada pedido de divisão de intercalação que foi submetido para esta instância do serviço de divisão de intercalação. Proporciona as seguintes informações para cada pedido:

* **Timestamp**: A hora e data quando o pedido foi iniciado.
* **OperationId**: um GUID que identifica exclusivamente o pedido. Este pedido também pode ser utilizado para cancelar a operação enquanto está ainda em curso.
* **Estado**: O estado atual do pedido. Para pedidos em curso, também lista fase atual no qual é o pedido.
* **CancelRequest**: um sinalizador que indica se o pedido foi cancelado.
* **Progresso**: uma estimativa de percentagem de conclusão para a operação. Um valor de 50 indica que a operação é aproximadamente 50% concluída.
* **Detalhes**: valor de um XML que fornece um relatório de progresso mais detalhado. O relatório de progresso é atualizado periodicamente como conjuntos de linhas são copiados a partir da origem de destino. Em caso de falhas ou exceções, esta coluna também inclui informações mais detalhadas sobre a falha.

### <a name="azure-diagnostics"></a>Diagnóstico do Azure
O serviço de divisão de intercalação utiliza baseado no Azure SDK 2.5 de monitorização e diagnóstico do diagnóstico do Azure. Controlar a configuração de diagnósticos, conforme explicado aqui: [ativar diagnósticos no Cloud Services do Azure e máquinas virtuais](../cloud-services/cloud-services-dotnet-diagnostics.md). O pacote de transferência inclui duas configurações de diagnóstico - uma para a função da web e outra para a função de trabalho. Estas configurações de diagnóstico para o serviço, siga as orientações de [Noções básicas do serviço de nuvem no Microsoft Azure](https://code.msdn.microsoft.com/windowsazure/Cloud-Service-Fundamentals-4ca72649). Inclui as definições para registar os contadores de desempenho, registos IIS, registos de eventos do Windows e registos de eventos da aplicação de divisão de intercalação. 

## <a name="deploy-diagnostics"></a>Implementar o diagnóstico
Para ativar a monitorização e diagnóstico de utilização da configuração de diagnóstico para as funções web e de trabalho que forneceu o pacote NuGet, execute os seguintes comandos com o Azure PowerShell: 

    $storage_name = "<YourAzureStorageAccount>" 

    $key = "<YourAzureStorageAccountKey" 

    $storageContext = New-AzureStorageContext -StorageAccountName $storage_name -StorageAccountKey $key  


    $config_path = "<YourFilePath>\SplitMergeWebContent.diagnostics.xml" 

    $service_name = "<YourCloudServiceName>" 

    Set-AzureServiceDiagnosticsExtension -StorageContext $storageContext -DiagnosticsConfigurationPath $config_path -ServiceName $service_name -Slot Production -Role "SplitMergeWeb" 


    $config_path = "<YourFilePath>\SplitMergeWorkerContent.diagnostics.xml" 

    $service_name = "<YourCloudServiceName>" 

    Set-AzureServiceDiagnosticsExtension -StorageContext $storageContext -DiagnosticsConfigurationPath $config_path -ServiceName $service_name -Slot Production -Role "SplitMergeWorker" 

Pode encontrar mais informações sobre como configurar e implementar as definições de diagnóstico aqui: [ativar diagnósticos no Cloud Services do Azure e máquinas virtuais](../cloud-services/cloud-services-dotnet-diagnostics.md). 

## <a name="retrieve-diagnostics"></a>Obter um diagnóstico
Pode aceder facilmente o diagnóstico do Visual Studio no Explorador de servidores na parte da árvore Explorador de servidores do Azure. Abra uma instância do Visual Studio e, na barra de menus clique vista e o Explorador de servidores. Clique no ícone do Azure para ligar à sua subscrição do Azure. Em seguida, navegue para o Azure -> armazenamento -> <your storage account> -> tabelas-WADLogsTable >. Para obter mais informações, consulte [navegação nos recursos de armazenamento com o Explorador de servidores](http://msdn.microsoft.com/library/azure/ff683677.aspx). 

![WADLogsTable][2]

WADLogsTable realçado na figura acima contém os eventos detalhados do registo de aplicação do serviço de divisão de intercalação. Tenha em atenção que a configuração predefinida do pacote transferido é adaptada para uma implementação de produção. Por conseguinte, o intervalo no qual os registos e contadores são retirados das instâncias do serviço é grande (5 minutos). Para desenvolvimento e teste, reduzi o intervalo ao ajustar as definições de diagnóstico da web ou a função de trabalho para as suas necessidades. Faça duplo clique na função no Explorador de servidor do Visual Studio (consultar acima) e, em seguida, ajuste o período de transferência de mensagens em fila na caixa de diálogo para as definições de configuração de diagnóstico: 

![Configuração][3]

## <a name="performance"></a>Desempenho
Em geral, um melhor desempenho é de esperar de maior, mais performant escalões de serviço na SQL Database do Azure. Superiores alocações de e/s, CPU e memória para os escalões de serviço superiores beneficiam a cópia em massa e eliminar operações que utiliza o serviço de divisão de intercalação. Por esse motivo, aumente a camada de serviço apenas para essas bases de dados durante um período definido, limitado de tempo.

O serviço também efetua consultas de validação como parte do respetivas operações normais. Estas consultas de validação verificar inesperada presença de dados no intervalo de destino e certifique-se de que nenhuma operação de intercalação/divisão/movimentação inicia a partir de um estado consistente. Estas consultas de todos os funcionar através de intervalos de chaves de fragmentação definidos pelo âmbito da operação e o tamanho do lote fornecido como parte da definição de pedido. Estas consultas melhor efetuar quando é de um índice que tenha a chave de fragmentação como a coluna à esquerda. 

Além disso, uma propriedade de exclusividade com a chave de fragmentação como a coluna à esquerda permitirá que o serviço para utilizar uma abordagem otimizada que limita o consumo de recursos em termos de memória e espaço de registo. Esta propriedade de exclusividade é necessário para mover os tamanhos de dados de grandes dimensões (normalmente acima 1GB). 

## <a name="how-to-upgrade"></a>Como atualizar
1. Siga os passos no [implementar um serviço de divisão de intercalação](sql-database-elastic-scale-configure-deploy-split-and-merge.md).
2. Altere o ficheiro de configuração do serviço de nuvem para a implementação de divisão de intercalação refletir os parâmetros de configuração de novo. Um novo parâmetro necessário é a informação sobre o certificado utilizado para encriptação. É uma forma fácil de fazê-lo para comparar o novo ficheiro de modelo de configuração de transferência em relação a sua configuração existente. Certifique-se de que adicionar as definições de "DataEncryptionPrimaryCertificateThumbprint" e "DataEncryptionPrimary" para o web e a função de trabalho.
3. Antes de implementar a atualização para o Azure, certifique-se de que todas as operações de intercalação divisão atualmente em execução tem concluído. Pode facilmente fazê-consultando os RequestStatus e PendingWorkflows tabelas na base de dados de metadados de intercalação de divisão de pedidos em curso.
4. Atualize a implementação de serviço em nuvem existente para a divisão de intercalação na sua subscrição do Azure com o novo pacote e o ficheiro de configuração de serviço atualizado.

Não é necessário aprovisionar uma nova base de dados de metadados de intercalação de divisão a atualização. A nova versão irá atualizar automaticamente a base de dados de metadados existente para a nova versão. 

## <a name="best-practices--troubleshooting"></a>Melhores práticas e resolução de problemas
* Definir um inquilino de teste e exercer as operações de divisão/intercalação/mover mais importantes com o inquilino de teste em várias partições horizontais. Certifique-se de que todos os metadados está definido corretamente no seu mapa de partições horizontais e que as operações não violam restrições ou chaves externas.
* Manter o inquilino de teste problemas relacionados com o tamanho de dados acima o tamanho máximo de dados do seu inquilino maior para garantir que não estão a identificar o tamanho dos dados. Isto ajuda-o a avaliar um limite superior no tempo que demora para mover um único inquilino à volta. 
* Certifique-se de que o esquema permite eliminações. O serviço de divisão de intercalação requer a capacidade de remover dados de partições horizontais origem depois dos dados foram copiados com êxito para o destino. Por exemplo, **eliminar acionadores** pode impedir que o serviço de eliminar os dados na origem e pode provocar operações falha.
* A chave de fragmentação deve ser a coluna à esquerda na chave primária ou definição de índice exclusivo. Garante que o melhor desempenho para as consultas de validação de intercalação ou de divisão e para as operações de movimento e a eliminação de dados reais que operam sempre em intervalos de chaves de fragmentação.
* Colocar o serviço de divisão de intercalação no Centro de dados e a região onde residem as bases de dados. 

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Anchors-->
<!--Image references-->
[1]:./media/sql-database-elastic-scale-overview-split-and-merge/split-merge-overview.png
[2]:./media/sql-database-elastic-scale-overview-split-and-merge/diagnostics.png
[3]:./media/sql-database-elastic-scale-overview-split-and-merge/diagnostics-config.png


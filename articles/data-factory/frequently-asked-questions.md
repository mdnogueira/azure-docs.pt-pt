---
title: 'O Azure Data Factory: Perguntas mais frequentes | Microsoft Docs'
description: "Obtenha respostas às perguntas mais frequentes sobre o Azure Data Factory."
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: monicar
ms.assetid: 532dec5a-7261-4770-8f54-bfe527918058
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/23/2017
ms.author: shlo
ms.openlocfilehash: 197ecff6728a7a2ce78cc6ca2861a10222a6b56b
ms.sourcegitcommit: 659cc0ace5d3b996e7e8608cfa4991dcac3ea129
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2017
---
# <a name="azure-data-factory-faq"></a>FAQ do Azure Data Factory
Este artigo aplica-se a versão 2 do serviço do Azure Data Factory. Fornece respostas às perguntas mais frequentes sobre o Data Factory.  

> [!NOTE]
> Este artigo aplica-se à versão 2 do Data Factory, que está atualmente em pré-visualização. Se estiver a utilizar a versão 1 do serviço do Data Factory, o que é geralmente disponível (DG), consulte [perguntas mais frequentes sobre a versão 1 do Data Factory](v1/data-factory-faq.md).

## <a name="what-is-azure-data-factory"></a>O que é o Azure Data Factory? 
Fábrica de dados é um serviço de integração de dados completamente gerido, baseado na nuvem, que automatiza o movimento e a transformação de dados. Como uma fábrica que opera equipamento para transformar raw materiais em bens acabados, do Azure Data Factory orquestra os serviços existentes que recolhem dados não processados e transformação-los em informações prontas a utilizar. 

Ao utilizar o Azure Data Factory, pode criar fluxos de trabalho condicionada por dados para mover dados entre no local e na nuvem arquivos de dados. Pode processar e serviços, tais como o tempo de execução de integração do Azure HDInsight, o Azure Data Lake Analytics e o SQL Server Integration Services (SSIS) de computação de transformação de dados através da utilização. 

Com o Data Factory, pode executar o processamento de dados num serviço em nuvem baseado no Azure ou no seu próprio ambiente de computação personalizada alojada, tais como SSIS, SQL Server ou Oracle. Depois de criar um pipeline que executa a ação que é necessário, pode agendar para executar periodicamente (por exemplo, horário, diário ou semanal) ou acionar o pipeline de uma ocorrência de evento. Para obter mais informações, consulte [introdução ao Azure Data Factory](introduction.md).

## <a name="whats-different-in-version-2"></a>Quais são as diferenças na versão 2?
A versão 2 do Azure Data Factory tem por base o serviço de movimento e de transformação de dados original do Azure Data Factory, alargando-se para um conjunto mais vasto de cenários de integração de dados cloud-first. O Azure Data Factory versão 2 oferece as seguintes capacidades:

- Fluxos de controlo e escala
- Implementar e executar pacotes do SSIS no Azure

Após o lançamento da versão 1, iremos reconhecer que os clientes têm de design complexa, cenários de integração de dados de híbridos que necessitem de movimento de dados e processamento na nuvem, no local e em nuvem máquinas de virtuais (VMs). Estes requisitos colocar uma necessidade para transferir e processar os dados dentro de ambientes de rede virtual protegida e para aumentar horizontalmente com a potência de processamento a pedido.

Como pipelines de dados estão a tornar-se uma parte fundamental mais de uma estratégia de análise de negócio, observamos que estas atividades exigem agendamento flexível para suportar cargas de dados incrementais e execuções de evento acionado. À medida que aumenta a complexidade, por isso, demasiado o requisito para o serviço suportar paradigmas comuns do fluxo de trabalho que incluem a ramificação, criar ciclos e processamento condicional.

Com a versão 2, também pode migrar pacotes do SSIS existentes para a cloud. Esta ação lifts e desvia SSIS como um serviço Azure que é gerido dentro do Data Factory, que utiliza uma funcionalidade nova do tempo de execução de integração. Por girar segurança de um tempo de execução de integração de SSIS versão 2, pode executar, gerir, monitorizar e criar pacotes SSIS na nuvem.

### <a name="control-flows-and-scale"></a>Fluxos de controlo e escala 
Para suportar a integração de diversos fluxos e padrões no armazém de dados modernas, fábrica de dados tem de ativar um modelo de pipeline de dados nova e flexível, que já não está associado aos dados de séries de tempo. Com esta versão, pode conditionals, ramo no fluxo de controlo de um pipeline de dados, de modelo e transmita explicitamente parâmetros dentro e entre estes fluxos.

Agora, ter a liberdade para modelar qualquer estilo de fluxo que tem necessárias para a integração de dados e que podem ser distribuídos a pedido ou repetidamente numa agenda. Alguns fluxos comuns agora possíveis e que não o eram antes incluem:   

- Fluxos de controlo:
    - Atividades de cadeia numa sequência dentro de um pipeline.
    - Atividades de sucursal dentro de um pipeline.
    - Parâmetros
        - Definir os parâmetros ao nível do pipeline e passar os argumentos ao invocar o pipeline a pedido ou a partir de um acionador.
        - As atividades podem consumir os argumentos transmitidos para o pipeline
    - Transmissão de estado personalizado
        - Os resultados das atividades, incluindo o estado, podem ser consumidos por uma atividade subsequente no pipeline.
    - Contentores de ciclo
        - For-each 
- Fluxos com base no acionador:
    - Podem ser acionadas pipelines a pedido ou tempo de relógio lateral.
- Fluxos de diferenças:
    - Utilizar parâmetros e definir o nível máximo para a cópia de diferenças ao mover as tabelas de dimensões ou de referência de um arquivo relacional, no local ou na nuvem, para carregar os dados para o lake. 

Para obter mais informações, consulte [Tutorial: controlar fluxos](tutorial-control-flow.md).

### <a name="deploy-ssis-packages-to-azure"></a>Implementar pacotes do SSIS no Azure 
Se pretender mover as cargas de trabalho SSIS, pode criar uma fábrica de dados versão 2 e aprovisionar um tempo de execução de integração de SSIS do Azure. O tempo de execução de integração de SSIS do Azure é um cluster completamente gerido de VMs do Azure (nós) que estão dedicados para executar os pacotes SSIS na nuvem. Para obter instruções passo a passo, consulte o [pacotes SSIS implementar no Azure](tutorial-deploy-ssis-packages-azure.md) tutorial. 
 

### <a name="sdks"></a>SDKs
Se for um utilizador avançado e à procura de uma interface programática, versão 2 fornece um conjunto avançado de SDKs que pode utilizar para criar, gerir ou monitorizar pipelines utilizando o IDE favorito.

- **SDK .NET**: oSDK .NET é atualizado para a versão 2. 
- **PowerShell**: os cmdlets do PowerShell são atualizados para a versão 2. Os cmdlets da versão 2 têm *DataFactoryV2* no nome. Por exemplo, *Get-AzureRmDataFactoryV2*. 
- **Python SDK**: este SDK é novo na versão 2.
- **API REST**: a API REST é atualizada para a versão 2.  

Os SDKs que são atualizados para a versão 2 não são compatíveis com versões anteriores com clientes de versão 1. 

### <a name="monitoring"></a>Monitorização
Atualmente, a versão 2 suporta monitorização de fábricas de dados com apenas SDKs. O portal ainda não tem o suporte para a monitorização de fábricas de dados de versão 2. 

## <a name="what-is-integration-runtime"></a>O que é o tempo de execução de integração?
Tempo de execução de integração é a infraestrutura de computação que é utilizada pelo Azure Data Factory para fornecer as seguintes capacidades de integração de dados em vários ambientes de rede:

- **Movimento de dados**: mover dados entre os arquivos de dados num público arquivos de dados e de rede numa rede privada (no local ou rede privada virtual). Oferece suporte para conectores incorporados, conversão de formatos, mapeamento de colunas e transferência de dados dimensionável e de desempenho elevado.
- **Emitir atividades**: computação de emissão e monitorizar atividades de transformação que estejam a executar numa variedade de serviços, como o Azure HDInsight, do Azure Machine Learning, SQL Database do Azure, SQL Server e muito mais.
- **Executar pacotes SSIS**: nativamente executa pacotes SSIS num ambiente gerido de computação do Azure.

Pode implementar uma ou várias instâncias do tempo de execução de integração conforme necessário para mover e transforme dados. Tempo de execução de integração pode executar numa rede pública do Azure ou numa rede privada (no local, rede Virtual do Azure ou nuvem privada virtual Amazon Web Services [VPC]). 

Para obter mais informações, consulte [integração tempo de execução no Azure Data Factory](concepts-integration-runtime.md).

## <a name="what-is-the-limit-on-the-number-of-integration-runtimes"></a>O que é o limite no número de tempos de execução de integração?
Não há nenhum disco rígido limite no número de instâncias de tempo de execução de integração que pode ter uma fábrica de dados. No entanto, há um limite no número de núcleos VM que o tempo de execução de integração, pode utilizar por subscrição para a execução de pacotes SSIS. Para obter mais informações, consulte [limita a fábrica de dados](../azure-subscription-service-limits.md#data-factory-limits).

## <a name="when-should-i-use-version-2-rather-than-version-1"></a>Quando devo utilizar a versão 2 em vez de versão 1? 
Se estiver familiarizado com o Azure Data Factory, inicie diretamente com a versão 2. Se já estiver a utilizar a versão 1, reconstrua as fábricas de dados na versão 2.

> [!WARNING]
> Versão 2 do Data Factory está em pré-visualização e não está em geral (DG) de disponibilidade. Por conseguinte, não se encontra sob os mesmo compromissos de (SLA) de contrato de nível de serviço do Azure como versão 1 do Data Factory, o que está a ser depois da disponibilidade geral 

## <a name="what-are-the-top-level-concepts-of-version-2"></a>Quais são os conceitos de nível superior da versão 2?
Uma subscrição do Azure pode ter uma ou mais instâncias do Azure Data Factory (ou fábricas de dados). O Azure Data Factory contém quatro componentes chave que funcionam em conjunto como uma plataforma em que pode compor condicionada por dados fluxos de trabalho com os passos de mover e transforme dados.

### <a name="pipelines"></a>Pipelines
Uma fábrica de dados pode ter um ou mais pipelines. Um pipeline é um agrupamento lógico de atividades para efetuar uma unidade de trabalho. Em conjunto, as atividades num pipeline executam tarefas. Por exemplo, um pipeline pode conter um grupo de atividades que ingestão de dados de um blob do Azure e, em seguida, executar uma consulta do Hive num cluster do HDInsight para criar partições de dados. A vantagem é que pode utilizar um pipeline para gerir as atividades como um conjunto em vez de ter que gerir individualmente cada atividade. Em conjunto pode encadeiam as atividades num pipeline operá-los sequencialmente, ou pode operá-los de forma independente, em paralelo.

### <a name="activity"></a>Atividade
As atividades representam uma fase de processamento num pipeline. Por exemplo, pode utilizar um *cópia* atividade para copiar dados de um arquivo de dados para outro arquivo de dados. Da mesma forma, pode utilizar uma atividade do ramo de registo, que executa uma consulta do Hive num cluster do Azure HDInsight para transformar ou analisar os dados. O Data Factory suporta três tipos de atividades: atividades de movimento de dados, atividades de transformação de dados e atividades de controlo.

### <a name="data-sets"></a>Conjuntos de dados
Conjuntos de dados representam as estruturas de dados dentro de arquivos de dados, o que simplesmente para ou do ponto de referenciam a dados que pretende utilizar nas suas atividades como entradas ou saídas. 

### <a name="linked-services"></a>Serviços ligados
Os serviços ligados são muito semelhantes às cadeias de ligação, que definem as informações de ligação necessárias para que o Data Factory se possa ligar a recursos externos. Considerá-lo desta forma: um serviço ligado define a ligação à origem de dados e um conjunto de dados representa a estrutura dos dados. Por exemplo, um serviço ligado do Storage do Azure Especifica a cadeia de ligação para ligar à conta do Storage do Azure. E um conjunto de dados de Blobs do Azure Especifica a pasta que contém os dados e o contentor de blob.

Serviços ligados tem duas finalidades no Data Factory:

- Para representar um *arquivo de dados* que inclui, mas não está limitado a uma instância do SQL Server no local, uma instância de base de dados Oracle, uma partilha de ficheiros ou uma conta de armazenamento de Blobs do Azure. Para obter uma lista dos arquivos de dados suportadas, consulte [atividade de cópia no Azure Data Factory](copy-activity-overview.md).
- Para representar um *recurso de computação* que pode alojar a execução de uma atividade. Por exemplo, a atividade do ramo de registo do HDInsight é executada num cluster do HDInsight Hadoop. Para obter uma lista de atividades de transformação e ambientes de computação suportados, consulte [transformar dados no Azure Data Factory](transform-data.md).

### <a name="triggers"></a>Acionadores
Acionadores representam unidades de processamento que determinam quando é arrancou uma execução de pipeline. Existem diferentes tipos de acionadores para diferentes tipos de eventos. Para a pré-visualização, suportamos um acionador de programador do relógio do padrão de fundo. 


### <a name="pipeline-runs"></a>Execuções de pipeline
Um pipeline executar é uma instância de uma execução de pipeline. Normalmente, instanciar um pipeline executar transferindo argumentos para os parâmetros que são definidos no pipeline. Pode passar os argumentos manualmente ou na definição do acionador.

### <a name="parameters"></a>Parâmetros
Os parâmetros são pares chave-valor numa configuração só de leitura. Definir os parâmetros de um pipeline e passar os argumentos para os parâmetros definidos durante a execução de um contexto de execução. O contexto de execução é criado por um acionador ou a partir de um pipeline que executar manualmente. As atividades dentro do pipeline consomem os valores dos parâmetros.

Um conjunto de dados é um parâmetro com tipo seguro e uma entidade que pode reutilizar ou de referência. Uma atividade pode referenciar conjuntos de dados e pode consumir as propriedades que são definidas na definição do conjunto de dados.

Um serviço ligado também é um parâmetro com tipo seguro que contém informações de ligação para um arquivo de dados ou num ambiente de computação. Também é uma entidade que pode reutilizar ou de referência.

### <a name="control-flows"></a>Fluxos de controlo
Atividades de pipeline, que incluem o encadeamento atividades numa sequência, ramificação, parâmetros definidos ao nível do pipeline, orquestram a fluxos de controlo e argumentos que passar como invocam o pipeline a pedido ou a partir de um acionador. Controlo fluxos também incluem Estado personalizadas a transmitir e ciclo contentores (ou seja, para cada iterators).


Para obter mais informações sobre os conceitos do Data Factory, veja os artigos seguintes:

- [Conjunto de dados e serviços ligados](concepts-datasets-linked-services.md)
- [Pipelines e atividades](concepts-pipelines-activities.md)
- [Integration runtime](concepts-integration-runtime.md) (Runtime de integração)

## <a name="what-is-the-pricing-model-for-data-factory"></a>O que é o modelo de preços do Data Factory?
Para o Azure Data Factory detalhes de preços, consulte [detalhes de preços do Data Factory](https://azure.microsoft.com/pricing/details/data-factory/).

## <a name="what-regions-support-azure-data-factory-version-2"></a>As regiões suportam a versão 2 do Azure Data Factory?
Atualmente, pode criar fábricas de dados da versão 2 nas regiões EUA leste, EUA Leste 2 e na Europa Ocidental. No entanto, uma fábrica de dados pode utilizar o runtime de integração noutra região para mover dados entre os arquivos de dados, distribuição de atividades contra os serviços de computação ou pacotes SSIS de emissão. Para obter mais informações, consulte [localizações do Data Factory](concepts-integration-runtime.md#integration-runtime-location).

## <a name="how-can-i-stay-up-to-date-with-information-about-data-factory"></a>Como posso continuar a atualizado com informações sobre o Data Factory?
Para obter as informações mais atualizadas sobre o Azure Data Factory, vá para os seguintes sites:

- [Blogue](https://azure.microsoft.com/blog/tag/azure-data-factory/)
- [Home page da documentação](/azure/data-factory)
- [Home page do produto](https://azure.microsoft.com/services/data-factory/)

## <a name="technical-deep-dive"></a>Descrição detalhada da técnica 

### <a name="can-version-1-and-version-2-pipelines-run-side-by-side"></a>Pode versão 1 e pipelines de versão 2 executadas lado?
Não. Versão 2 e fábricas de dados de versão 1 não podem conter entidades (por exemplo, serviços ligados, conjuntos de dados ou pipelines) da versão de outro.   

### <a name="do-i-still-need-to-define-data-sets-in-version-2"></a>É ainda necessário definir os conjuntos de dados na versão 2?
Um conjunto de dados já não é uma entidade obrigatória para a maioria das atividades. É necessário para copiar, aprendizagem, pesquisa, validação e atividades personalizadas que utilizam o esquema e outras informações de metadados no conjunto de dados de transformação. O resto das atividades já não necessita de conjuntos de dados.

### <a name="can-i-chain-two-activities-without-a-data-set-in-version-2"></a>Posso encadeiam duas atividades sem um conjunto de dados na versão 2?
Sim. Pode encadeiam atividades de versão 2 sem necessidade de conjuntos de dados. Cadeia atividades utilizando o **dependsOn** propriedade na definição do JSON do pipeline. 

### <a name="are-all-the-version-1-activities-supported-in-version-2"></a>Todas as versões são atividades 1 suportado na versão 2? 
Sim, todas as atividades de versão 1 são suportadas na versão 2.

### <a name="how-can-i-schedule-a-version-2-pipeline"></a>Como agendar a um pipeline de versão 2? 
Pode utilizar o acionador de programador para agendar um pipeline de versão 2. O acionador utiliza uma agenda de calendário do relógio de firewall e pode utilizá-la para agendar pipelines periodicamente ou através da utilização baseada em Calendário periódica padrões (por exemplo, semanalmente nos segundas às 18: 00 e às quintas-em 9 PM). Para obter mais informações, consulte [acionadores e da execução de Pipeline](concepts-pipeline-execution-triggers.md).

### <a name="can-i-pass-parameters-to-a-pipeline-run-in-version-2"></a>Pode passar parâmetros para um pipeline executar na versão 2?
Sim, os parâmetros são um conceito de primeira classe, nível superior na versão 2. Pode definir os parâmetros ao nível do pipeline e passar os argumentos como executar o pipeline de executar a pedido ou utilizando um acionador.  

### <a name="can-i-define-default-values-for-the-pipeline-parameters"></a>Pode definir os valores predefinidos para os parâmetros de pipeline? 
Sim. Pode definir os valores predefinidos para os parâmetros nos pipelines. 

### <a name="can-an-activity-in-a-pipeline-consume-arguments-that-are-passed-to-a-pipeline-run"></a>Uma atividade num pipeline pode consumir a argumentos que são transmitidos para um pipeline executar? 
Sim. Cada atividade no pipeline de pode consumir o valor do parâmetro que foi transmitido para o pipeline e execute com a `@parameter` construir. 

### <a name="can-an-activity-output-property-be-consumed-in-another-activity"></a>Uma propriedade de saída da atividade pode ser utilizada noutra atividade? 
Sim. Uma saída da atividade pode ser utilizada numa atividade subsequente com a `@activity` construir.
 
### <a name="how-do-i-gracefully-handle-null-values-in-an-activity-output"></a>Como posso lidar graciosamente com valores nulos existentes numa saída da atividade? 
Pode utilizar o `@coalesce` construir em expressões de lidar graciosamente com valores nulos. 

### <a name="can-i-use-retry-and-timeout-at-the-activity-level-in-version-2"></a>Posso utilizar repetições e tempo limite ao nível de atividade numa versão 2?
Sim. Para regulamentar a execução de atividades de versão 2, tal como na versão 1, pode configurar a repetição e tempo limite ao nível de atividade. 

## <a name="next-steps"></a>Passos seguintes
Para obter instruções passo a passo Criar uma fábrica de dados da versão 2, consulte os seguintes tutoriais:

- [Início rápido: Criar uma fábrica de dados](quickstart-create-data-factory-dot-net.md)
- [Tutorial: Copiar dados na nuvem](tutorial-copy-data-dot-net.md)


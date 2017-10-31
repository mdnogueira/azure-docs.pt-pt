---
title: "Introdução ao Azure Data Factory | Microsoft Docs"
description: "Saiba mais sobre o Azure Data Factory, um serviço de integração de dados na cloud que orquestra e automatiza o movimento e a transformação de dados."
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/29/2017
ms.author: shlo
ms.openlocfilehash: 9ed89261b7050bb41d49b827e02d24535983160f
ms.sourcegitcommit: 5735491874429ba19607f5f81cd4823e4d8c8206
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2017
---
# <a name="introduction-to-azure-data-factory"></a>Introdução ao Azure Data Factory 
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versão 1 - GA](v1/data-factory-introduction.md)
> * [Versão 2 - Pré-visualização](introduction.md)

No mundo dos macrodados, muitas vezes, são armazenados dados desorganizados e não processados em sistemas relacionais, não relacionais, entre outros. No entanto, por si só, os dados não processados não têm o contexto ou o significado adequado para disponibilizar informações significativas para analistas, cientistas de dados ou decisores empresariais. 

Os macrodados precisam de um serviço que orquestre e operacionalize processos para refinar estes enormes arquivos de dados não processados em informações comerciais acionáveis. O Azure Data Factory é um serviço na cloud gerido criado para estes projetos complexos e híbridos de extract-transform-load (ETL), extract-load-transform (ELT) e integração de dados.

Por exemplo, imagine uma empresa de jogos que recolhe petabytes de registos de jogos que são produzidos por jogos na cloud. A empresa quer analisar estes registos para obter informações sobre as preferências dos clientes, dados demográficos e comportamentos de utilização. Quer também identificar as oportunidades de vendas superiores e vendas cruzadas, desenvolver funcionalidades novas e apelativas, impulsionar o crescimento do negócio e proporcionar uma melhor experiência aos clientes.

Para analisar estes registos, a empresa tem de utilizar dados de referência, como informações do cliente, informações do jogo, informações de campanhas de marketing, que se encontram num arquivo de dados no local. A empresa quer utilizar estes dados a partir do arquivo de dados no local e combiná-los com os dados de registos adicionais que tem num arquivo de dados na cloud. 

Para extrair informações, espera processar os dados combinados mediante a utilização de um cluster do Spark na cloud (Azure HDInsight) e, por fim, publicar os dados transformados num armazém de dados na cloud, como o Azure SQL Data Warehouse, para criar facilmente um relatório com base no mesmo. Pretende automatizar este fluxo de trabalho e monitorizá-lo e geri-lo com base numa agenda diária. Também quer executá-lo quando são adicionados ficheiros a um contentor de arquivo de blobs.

O Azure Data Factory é a plataforma que resolve estes cenários de dados. É um *serviço de integração de dados baseado na cloud que permite criar fluxos de trabalho orientados por dados na cloud para orquestrar e automatizar o movimento e a transformação de dados*. Ao utilizar o Azure Data Factory, pode criar e agendar fluxos de trabalho orientados por dados (denominados pipelines) que podem ingerir dados a partir de arquivos de dados diferentes. Este serviço pode processar e transformar os dados através de serviços de computação como o Azure HDInsight, o Hadoop, o Spark, o Azure Data Lake Analytics e o Azure Machine Learning. 

Além disso, pode publicar os dados de saída em arquivos de dados como o Azure SQL Data Warehouse para consumo por parte de aplicações de business intelligence (BI). Em última análise, através do Azure Data Factory, os dados não processados podem ser organizados em arquivos de dados com significado e em data lakes para uma melhor tomada de decisões empresariais.

![Vista de nível superior do Data Factory](media/introduction/big-picture.png)

> [!NOTE]
> Este artigo aplica-se à versão 2 do Data Factory, que está atualmente em pré-visualização. Se estiver a utilizar a versão 1 do serviço Data Factory, que está em disponibilidade geral (GA), veja [Introdução à versão 1 do Data Factory](v1/data-factory-introduction.md).

## <a name="how-does-it-work"></a>Como funciona?
Normalmente, os pipelines (fluxos de trabalho orientados por dados) no Azure Data Factory executam os quatro passos seguintes:

![Quatro passos de um fluxo de trabalho orientado por dados](media/introduction/four-steps-of-a-workflow.png)

### <a name="connect-and-collect"></a>Ligar e recolher

As empresas têm dados dos mais diversos tipos localizados em diferentes origens, seja no local, na cloud, estruturadas, não estruturadas e semi-estruturadas, que são recebidos em intervalos e velocidades distintos. 

O primeiro passo na criação de um sistema de produção de informação é ligar a todas as origens de dados e processamento necessários, como serviços de software como serviços de software como serviço (SaaS), bases de dados, partilhas de ficheiros e serviços Web de FTP. O passo seguinte é mover os dados conforme necessário para uma localização centralizada, para processamento subsequente.

Sem o Data Factory, as empresas têm de criar componentes de movimento de dados personalizados ou escrever serviços personalizados para integrar essas origens de dados e esse processamento. Integrar e manter estes sistemas é dispendioso e difícil. Além disso, muitas vezes, não têm monitorização, alertas e controlos de nível empresarial que um serviço totalmente gerido pode oferecer.

Com o Data Factory, pode utilizar a [Atividade de Cópia](copy-activity-overview.md) num pipeline de dados para mover dados de arquivos de dados no local e na cloud para um arquivo centralizado na cloud, para análises adicionais. Por exemplo, pode recolher dados no Azure Data Lake Store e transformá-los posteriormente com um serviço de computação do Azure Data Lake Analytics. Também pode recolher dados no armazenamento de blobs do Azure e transformá-los mais tarde com um cluster do Azure HDInsight Hadoop.

### <a name="transform-and-enrich"></a>Transformar e enriquecer
Assim que os dados estiverem presentes num arquivo de dados centralizado na cloud, processe ou transforme os dados recolhidos através da utilização de serviços de computação, tais como o HDInsight Hadoop, o Spark, o Data Lake Analytics e o Machine Learning. Pretende produzir de forma fiável os dados transformados numa agenda controlada e sustentável, para alimentar ambientes de produção com dados fidedignos.

### <a name="publish"></a>Publicar
Quando os dados não processados estiverem refinados e prontos para consumo por parte das empresas, carregue os dados para o Azure Data Warehouse, para a Base de Dados SQL do Azure, para o Azure CosmosDB ou para qualquer que seja o motor de análise para o qual os seus utilizadores empresariais podem apontar a partir as ferramentas de business intelligence que utilizam.

### <a name="monitor"></a>Monitorizar
Depois de criar e implementar com êxito o seu pipeline de integração de dados, proporcionando valor comercial a partir dos dados refinados, monitorize as atividades e os pipelines agendados relativamente às taxas de êxito e falha. O Azure Data Factory tem suporte incorporado para monitorização de pipelines através do Azure Monitor, de APIs, do PowerShell, do Microsoft Operations Management Suite e dos painéis de estado de funcionamento no portal do Azure.

## <a name="whats-different-in-version-2"></a>Quais são as diferenças na versão 2?
A versão 2 do Azure Data Factory tem por base o serviço de movimento e de transformação de dados original do Azure Data Factory, alargando-se para um conjunto mais vasto de cenários de integração de dados cloud-first. A versão 2 do Azure Data Factory oferece as capacidades seguintes:

- Controlar o fluxo e o dimensionamento
- Implementar e executar os pacotes do SQL Server Integration Services (SSIS) no Azure

Após o lançamento da versão 1, reconhecemos que os clientes precisam de desenhar cenários de integração de dados complexos e híbridos, os quais requerem movimento e processamento de dados na cloud, no local e em VMs na cloud. Estes requisitos trouxeram consigo a necessidade de transferir e processar dados em ambientes de redes virtuais seguros e de aumentar horizontalmente com poder de processamento a pedido.

À medida que os pipelines de dados se tornam uma parte crítica das estratégias de análise empresarial, vimos que estas atividades de dados vitais requerem um agendamento flexível para suportar cargas de dados incrementais e execuções acionadas por eventos. Por último, à medida que estas operações se tornam mais complexas, também a necessidade de o serviço suportar paradigmas de fluxos de trabalho comuns aumenta, incluindo ramificações, ciclos e processamento condicional.

Com a versão 2, também pode migrar pacotes do SSIS existentes para a cloud. Pode fazer a migração lift-and-shift do SSIS como um serviço do Azure que é gerido no ADF mediante a utilização da funcionalidade nova “Runtimes de Integração” (IR). Ao configurar um IR do SSIS na versão 2, tem a capacidade de executar, gerir, monitorizar e criar pacotes do SSIS na cloud.

### <a name="control-flow-and-scale"></a>Controlar o fluxo e o dimensionamento 
Para suportar os diversos fluxos e padrões de integração nos armazéns de dados modernos, o Data Factory disponibiliza um novo modelo de pipeline de dados flexível, que já não está associado a dados de séries de tempo. Com esta versão, pode modelar a lógica condicional e de ramificação no fluxo de controlo de um pipeline de dados e transmitir, explicitamente, parâmetros dentro e transversalmente nesses fluxos.

Agora, tem a liberdade de poder modelar qualquer estilo de fluxo necessário para a integração de dado, que pode ser distribuído a pedido ou repetidamente com base numa agenda. Alguns fluxos comuns agora possíveis e que não o eram antes incluem:   

- Fluxo de controlo:
    - Encadear atividades numa sequência dentro de um pipeline
    - Ramificar atividades dentro de um pipeline
    - Parâmetros
        - Os parâmetros podem ser definidos ao nível do pipeline e os argumentos podem ser transmitidos quando é invocado o pipeline a pedido ou a partir de um acionador.
        - As atividades podem consumir os argumentos transmitidos para o pipeline
    - Transmissão de estado personalizado
        - Os resultados das atividades, incluindo o estado, podem ser consumidos por uma atividade subsequente no pipeline.
    - Contentores de ciclo
        - For-each 
- Fluxos baseados em acionadores
    - Os pipelines podem ser adicionados a pedido ou com base no tempo total de execução.
- Fluxos delta
    - Utilize parâmetros e defina o limite máximo para as cópias delta à medida que move tabelas de dimensão ou de referência de um arquivo relacional no local ou na cloud para carregar os dados para o lake. 

Para obter mais informações, veja [Atividades de ramificação e encadeamento num pipeline do Data Factory](tutorial-control-flow.md).

### <a name="deploy-ssis-packages-to-azure"></a>Implementar pacotes do SSIS no Azure 
Se quiser mover as cargas de trabalho do SSIS, pode criar uma fábrica de dados da versão 2 e aprovisionar um Runtime de Integração (IR) Azure-SSIS. O IR Azure-SSIS é um cluster totalmente gerido de VMs do Azure (nós) dedicadas à execução dos pacotes do SSIS na cloud. Para obter instruções passo a passo, veja o tutorial [Implementar pacotes de SQL Server Integration Services no Azure](tutorial-deploy-ssis-packages-azure.md). 
 

### <a name="sdks"></a>SDKs
Se for utilizador avançado e quiser uma interface programática, a versão 2 proporciona um conjunto avançado de SDKs que podem ser utilizados para criar, gerir e monitorizar pipelines com o seu IDE preferido.

- *SDK .NET*: oSDK .NET é atualizado para a versão 2. 
- *PowerShell*: os cmdlets do PowerShell são atualizados para a versão 2. Os cmdlets da versão 2 têm **DataFactoryV2** no nome. Por exemplo: Get-AzureRmDataFactoryV2. 
- *Python SDK*: este SDK é novo na versão 2.
- *API REST*: a API REST é atualizada para a versão 2.  

Os SDKs que são atualizados para a versão 2 não são compatíveis com versões anteriores com clientes de versão 1. 

### <a name="monitoring"></a>Monitorização
Atualmente, a versão 2 suporta a monitorização de fábricas de dados com SDKs apenas. O portal ainda não tem o suporte para a monitorização de fábricas de dados de versão 2. 

## <a name="load-the-data-into-a-lake"></a>Carregar os dados para um lake
O Data Factory tem mais de 30 conectores, através dos quais pode carregar dados de ambientes híbridos e heterogéneos para o Azure. Veja os últimos resultados de desempenho dos testes internos e as sugestões de desempenho no [guia Performance and tuning](copy-activity-performance.md) (Desempenho e otimização). 

Além disso, ativámos recentemente a elevada disponibilidade e a escalabilidade no Runtime de Integração autoalojado que instalar num ambiente de rede privada. Esta alteração dá resposta aos requisitos de grandes empresas clientes relativamente a melhor disponibilidade e escalabilidade.

## <a name="top-level-concepts-in-version-2"></a>Principais conceitos da versão 2
As subscrições do Azure podem ter uma ou várias instâncias do Azure Data Factory (ou fábricas de dados). O Azure Data Factory é constituído por quatro componentes principais. Estes componentes funcionam em conjunto para fornecer a plataforma na qual pode compor fluxos de trabalho orientados por dados com passos para mover e transformar dados.

### <a name="pipeline"></a>Pipeline
Uma fábrica de dados pode ter um ou mais pipelines. Os pipelines são agrupamentos lógicos de atividades que realizam uma unidade de trabalho. Em conjunto, as atividades num pipeline executam tarefas. Por exemplo, um pipeline pode conter um grupo de atividades que ingere dados de um blob do Azure e, em seguida, executa uma consulta de Hive num cluster do HDInsight para particionar os dados. 

A vantagem neste caso é que o pipeline lhe permite gerir as atividades como um conjunto, em vez de individualmente. As atividades num pipeline podem ser encadeadas para funcionar sequencialmente ou podem funcionar de forma independente em paralelo

### <a name="activity"></a>Atividade
As atividades representam uma fase de processamento num pipeline. Por exemplo, pode utilizar uma atividade de cópia para copiar dados de um arquivo de dados para outro. Da mesma forma, pode utilizar uma atividade do Hive, que executa uma consulta do Hive num cluster do Azure HDInsight, para transformar ou analisar os seus dados. O Data Factory suporta três tipos de atividades: atividades de movimento de dados, atividades de transformação de dados e atividades de controlo.

### <a name="datasets"></a>Conjuntos de dados
Os conjuntos de dados representam estruturas de dados nos arquivos de dados, que simplesmente apontam ou referenciam os dados que pretende utilizar nas suas atividades como entrada ou saída. 

### <a name="linked-services"></a>Serviços ligados
Os serviços ligados são muito semelhantes às cadeias de ligação, que definem as informações de ligação necessárias para que o Data Factory se possa ligar a recursos externos. Encare da seguinte forma: um serviço ligado define a ligação à origem de dados e um conjunto de dados representa a estrutura dos dados. Por exemplo, um serviço ligado do Armazenamento do Azure especifica a cadeia de ligação para ligar à conta do Armazenamento do Azure. Além disso, um conjunto de dados de blobs do Azure especifica o contentor de blobs e a pasta que contém os dados.

Os serviços ligados são utilizados para duas finalidades no Data Factory:

- Para representar um **arquivo de dados**, que inclua, entre outros, uma base de dados do SQL Server no local, uma base de dados Oracle, uma partilha de ficheiros ou uma conta de armazenamento de blobs do Azure. Para obter uma lista dos arquivos de dados suportados, veja o artigo [copy activity](copy-activity-overview.md) (Atividade de Cópia).

- Para representar um **recurso de computação** que pode alojar a execução de uma atividade. Por exemplo, a Atividade HDInsightHive é executada num cluster do HDInsight Hadoop. Para obter uma lista das atividades de transformação e os ambientes de computação suportados, veja o artigo [Transform data](transform-data.md) (Transformar dados).

### <a name="triggers"></a>Acionadores
Os acionadores representam a unidade de processamento que determina quando é que uma execução de pipeline tem de arrancar. Existem diferentes tipos de acionadores para diferentes tipos de eventos. Na pré-visualização, suportamos o acionador do agendador de tempo total de execução. 


### <a name="pipeline-runs"></a>Execuções de pipeline
As instâncias de pipeline são instâncias da execução dos pipelines. Normalmente, as execuções de pipeline são instanciadas pela transmissão de argumentos aos parâmetros definidos nos pipelines. Os argumentos podem ser transmitidos manualmente ou na definição do acionador.

### <a name="parameters"></a>Parâmetros
Os parâmetros são pares chave-valor de configuração só de leitura.  Os parâmetros são definidos no pipeline. Os argumentos para os parâmetros definidos são transmitidos durante a execução a partir do contexto da instância criado por um acionador ou por um pipeline executado manualmente. As atividades dentro do pipeline consomem os valores dos parâmetros.

Os conjuntos de dados são parâmetros inflexíveis e uma entidade reutilizável/referenciável. As atividades podem referenciar conjuntos de dados e podem consumir as propriedades definidas na definição do conjunto de dados

Os serviços ligados também são um parâmetro inflexível que contém as informações de ligação a um arquivo de dados ou a um ambiente de computação. Também é uma entidade reutilizável/referenciável.

### <a name="control-flow"></a>Fluxo de controlo
O fluxo de controlo é uma orquestração das atividades dos pipelines que incluem o encadeamento de atividades numa sequência, a ramificação, a definição de parâmetros ao nível do pipeline e a transmissão de argumentos quando é invocado o pipeline a pedido ou a partir de um acionador. Também inclui a transmissão de estado personalizado e os contentores de ciclo, ou seja, iteradores For-each.


Para obter mais informações sobre os conceitos do Data Factory, veja os artigos seguintes:

- [Dataset and linked services](concepts-datasets-linked-services.md) (Conjuntos de dados e serviços ligados)
- [Pipelines e atividades](concepts-pipelines-activities.md)
- [Integration runtime](concepts-integration-runtime.md) (Runtime de integração)

## <a name="supported-regions"></a>Regiões suportadas

Atualmente, pode criar fábricas de dados nas regiões E.U.A. Leste e E.U.A. Leste 2. No entanto, uma fábrica de dados pode aceder aos arquivos de dados e serviços de computação noutras regiões do Azure para mover dados entre os arquivos de dados ou processar dados com serviços de computação.

O Azure Data Factory propriamente dito não armazena quaisquer dados. Permite-lhe criar fluxos de trabalho orientados por dados para orquestrar o movimento dos dados entre arquivos de dados suportados e o processamento de dados com serviços de computação noutras regiões ou num ambiente no local. Também lhe permite utilizar mecanismos programáticos e de IU para monitorizar e gerir os fluxos de trabalho.

Apesar de o Data Factory estar disponível apenas nas regiões E.U.A. Leste e E.U.A. Leste 2, o serviço que alimenta o movimento de dados no Data Factory está disponível globalmente em várias regiões. Caso um arquivo de dados se encontre protegido por uma firewall, os dados são movidos pelo Data Management Gateway que está instalado no seu ambiente no local.

Por exemplo, vamos presumir que os seus ambientes de computação, como o cluster do Azure HDInsight e o Azure Machine Learning, estão a ficar sem a região Europa Ocidental. Pode criar e utilizar uma instância do Azure Data Factory na Europa do Norte e utilizá-la para agendar tarefas nos seus ambientes de computação na Europa Ocidental. O Data Factory demora alguns milissegundos a acionar a tarefa no seu ambiente de computação, mas o tempo de execução da tarefa nesse ambiente não sofre alterações.

## <a name="next-steps"></a>Passos seguintes
Saiba como criar uma fábrica de dados com as instruções passo a passo nos Inícios Rápidos [PowerShell](quickstart-create-data-factory-powershell.md), [.NET](quickstart-create-data-factory-dot-net.md), [Python](quickstart-create-data-factory-python.md), [REST API](quickstart-create-data-factory-rest-api.md) e portal do Azure. 

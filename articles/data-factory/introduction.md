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
ms.date: 09/19/2017
ms.author: shlo
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 09e514aee503b7cb045c81d8ddcb855ced9b072b
ms.contentlocale: pt-pt
ms.lasthandoff: 09/25/2017

---
# <a name="introduction-to-azure-data-factory"></a>Introdução ao Azure Data Factory 
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versão 1 - GA](v1/data-factory-introduction.md)
> * [Versão 2 - Pré-visualização](introduction.md)

No mundo dos macrodados, muitas vezes, são armazenados dados desorganizados e não processados em sistemas relacionais, não relacionais, entre outros. No entanto, por si só, os dados não processados não têm o contexto ou o significado adequado para disponibilizar informações significativas para analistas, cientistas de dados e decisores empresariais. O que é preciso é um serviço que permita a orquestração e a operacionalização de processos para refinar estes enormes arquivos de dados não processados em informações comerciais acionáveis. O Azure Data Factory é um serviço de cloud gerido criado para estes projetos complexos e híbridos de extract-transform-load (ETL), extract-load-transform (ELT) e integração de dados.

Por exemplo, imagine uma empresa de jogos que recolhe petabytes de registos de jogos produzidos por jogos na cloud. A empresa quer analisar estes registos para obter informações sobre as preferências dos clientes, dados demográficos e comportamentos de utilização, para identificar oportunidades de venda superior e venda cruzada, desenvolver novas funcionalidades apelativas, impulsionar o crescimento do negócio e proporcionar uma melhor experiência aos clientes.

Para analisar estes registos, a empresa tem de utilizar dados de referência, como informações do cliente, informações do jogo, informações de campanhas de marketing, que se encontram num arquivo de dados no local. A empresa quer utilizar estes dados a partir do arquivo de dados no local e combiná-los com os dados de registos adicionais que tem num arquivo de dados na cloud. Para extrair informações, espera processar os dados combinados mediante a utilização de um cluster do Spark na cloud (HDInsight) e, por fim, publicar os dados transformados num armazém de dados na cloud, como o Azure SQL Data Warehouse, para criar facilmente um relatório com base no mesmo. Este fluxo de trabalho vai ser automatizado, monitorizado e gerido com base numa agenda diária e executado quando os ficheiros são colocados num contentor de arquivo de blobs.

O Azure Data Factory é a plataforma que resolve estes cenários de dados. É um **serviço de integração de dados baseado na cloud que permite criar fluxos de trabalho condicionados por dados na cloud para orquestrar e automatizar o movimento e a transformação de dados**. Com o Azure Data Factory, poderá criar e agendar fluxos de trabalho condicionados por dados (denominados pipelines) que são capazes de ingerir dados provenientes de diferentes arquivos de dados, processar/transformar os dados através de serviços de computação, tais como o Azure HDInsight Hadoop, Spark, Azure Data Lake Analytics e Azure Machine Learning, e publicar os dados de saída em arquivos de dados como o Azure SQL Data Warehouse para serem consumidos por aplicações de business intelligence (BI). Em última análise, através do Azure Data Factory, os dados não processados podem ser organizados em arquivos de dados com significado e em data lakes para uma melhor tomada de decisões empresariais.

![vista de diagrama do Data Factory](media/introduction/big-picture.png)

> [!NOTE]
> Este artigo aplica-se à versão 2 do Data Factory, que está atualmente em pré-visualização. Se estiver a utilizar a versão 1 do serviço Data Factory, que está disponível em geral (GA), veja [Introduction to Data Factory version 1](v1/data-factory-introduction.md) (Introdução à versão 1 do Data Factory).

## <a name="how-does-it-work"></a>Como funciona?
Normalmente, os pipelines (fluxos de trabalho orientados por dados) no Azure Data Factory executam os quatro passos seguintes:

![Quatro passos de um fluxo de trabalho orientado por dados](media/introduction/four-steps-of-a-workflow.png)

### <a name="connect-and-collect"></a>Ligar e recolher

As empresas têm dados dos mais diversos tipos localizados em diferentes origens, seja no local, na cloud, estruturadas, não estruturadas e semi-estruturadas, que são recebidos em intervalos e velocidades distintos. O primeiro passo na criação de um sistema de produção de informação é ligar a todas as origens de dados e processamento necessários, como serviços de software como serviço (SaaS), bases de dados, partilhas de ficheiros, FTP, serviços Web, e mover os dados para uma localização centralizada, conforme necessário, para processamento subsequente.

Sem o Data Factory, as empresas têm de criar componentes de movimento de dados personalizados ou escrever serviços personalizados para integrar essas origens de dados e esse processamento. É uma opção dispendiosa e estes sistemas são de difícil integração e manutenção e, muitas vezes, não têm a monitorização e os alertas de nível empresarial nem os controlos que os serviços totalmente geridos podem oferecer.

Com o Data Factory, pode utilizar a [Atividade de Cópia](copy-activity-overview.md) num pipeline de dados para mover dados de arquivos de dados no local e na cloud para um arquivo centralizado na cloud, para análises adicionais. Por exemplo, pode recolher dados numa fábrica do Azure Data Lake Store e transformá-los posteriormente, utilizando um serviço de computação do Azure Data Lake Analytics. Em alternativa, pode recolher dados no Armazenamento de Blobs do Azure e transformá-los mais tarde, utilizando um cluster do Azure HDInsight Hadoop.

### <a name="transform-and-enrich"></a>Transformar e enriquecer
Assim que os dados estiverem presentes num arquivo de dados centralizado na cloud, pretende que os dados recolhidos sejam processados ou transformados através da utilização de serviços de computação, tais como o HDInsight Hadoop, Spark, Data Lake Analytics e Machine Learning. Pretende produzir de forma fiável os dados transformados numa agenda controlada e sustentável, para alimentar ambientes de produção com dados fidedignos.

### <a name="publish"></a>Publicar
Agora que os dados não processados foram refinados e estão prontos para consumo por parte das empresas, carregue os dados para o Azure Data Warehouse, para a BD SQL do Azure, para o Azure Cosmos DB ou para qualquer que seja o motor de análise para o qual os seus utilizadores empresariais podem apontar a partir as ferramentas de business intelligence que utilizam.

### <a name="monitor"></a>Monitorizar
Depois de criar e implementar com êxito o seu pipeline de integração de dados, proporcionando valor comercial a partir dos dados refinados, é útil monitorizar as atividades e os pipelines agendados relativamente às taxas de êxito e falha. O Azure Data Factory tem suporte incorporado para monitorização de pipelines através do Azure Monitor, de APIs, do PowerShell, do OMS e dos painéis de estado de funcionamento no portal do Azure.

## <a name="whats-different-in-version-2"></a>Quais são as diferenças na versão 2?
A versão 2 do Azure Data Factory tem por base o serviço de movimento e de transformação de dados original do Azure Data Factory, alargando-se para um conjunto mais vasto de cenários de integração de dados cloud-first. O Azure Data Factory V2 oferece as capacidades seguintes:

- Controlar o Flow e o Dimensionamento
- Implementar e executar pacotes do SSIS no Azure

Após o lançamento da versão 1, reconhecemos que os clientes precisam de desenhar cenários de integração de dados complexos e híbridos, os quais requerem movimento e processamento de dados na cloud, no local e em VMs na cloud. Estes requisitos trouxeram consigo a necessidade de transferir e processar dados em ambientes de VNet seguros e de aumentar horizontalmente com poder de processamento a pedido.

À medida que os pipelines de dados se tornam uma parte crítica das estratégias de análise empresarial, vimos que estas atividades de dados vitais requerem um agendamento flexível para suportar cargas de dados incrementais e execuções acionadas por eventos. Por último, como as complexidades destas orquestrações aumentam, também a necessidade de o serviço suportar paradigmas de fluxos de trabalho comuns aumenta, incluindo ramificações, ciclos e processamento condicional.

Com a versão 2, também pode migrar pacotes existentes dos SQL Server Integration Services (SSIS) para a cloud para migrar o SSIS através de lift and shift como um serviço do Azure gerido no ADF, mediante a utilização de uma funcionalidade nova de “Integration Runtimes” (IR). Ao configurar um IR do SSIS na versão 2, tem a capacidade de executar, gerir, monitorizar e criar pacotes do SSIS na cloud.

### <a name="control-flow-and-scale"></a>Controlar o fluxo e o dimensionamento 
Para suportar os diversos fluxos e padrões de integração nos armazéns de dados modernos, o Data Factory disponibiliza um novo modelo de pipeline de dados flexível, que já não está associado a dados de séries de tempo. Com esta versão, pode modelar a lógica condicional e de ramificação no fluxo de controlo de um pipeline de dados e transmitir, explicitamente, parâmetros dentro e transversalmente nesses fluxos

Agora, tem a liberdade de poder modelar qualquer estilo de fluxo necessário para a integração de dados do mesmo e distribuí-lo a pedido ou repetidamente com base numa agenda. Alguns fluxos comuns agora possíveis e que não o eram antes incluem:   

- Fluxo de Controlo:
    - Encadear atividades numa sequência dentro de um pipeline
    - Ramificar atividades dentro de um pipeline
    - Parâmetros
        - Os parâmetros podem ser definidos ao nível do pipeline e os argumentos transmitidos ao invocar o pipeline a pedido ou a partir de um acionador
        - As atividades podem consumir os argumentos transmitidos para o pipeline
    - Transmissão de estado personalizado
        - Os resultados das atividades, incluindo o estado, podem ser consumidos por uma atividade subsequente no pipeline
    - Contentores de ciclo
        - For-each 
- Fluxos baseados em acionadores
    - Os pipelines podem ser adicionados a pedido ou com base no tempo total de execução
- Fluxos delta
    - Utilize parâmetros e defina o limite máximo para as cópias delta à medida que move tabelas de dimensão ou de referência de um arquivo relacional no local ou na cloud para carregar os dados para o lake 

Para obter mais informações, veja [tutorial; controlo flow](tutorial-control-flow.md) (tutorial: fluxo de controlo).

### <a name="deploy-ssis-packages-to-azure"></a>Implementar pacotes do SSIS no Azure 
Se quiser mover as cargas de trabalho do SSIS, pode criar uma fábrica de dados da versão 2 e aprovisionar um Runtime de Integração (IR) Azure-SSIS. O IR Azure-SSIS é um cluster totalmente gerido de VMs do Azure (nós) dedicadas à execução dos pacotes do SSIS na cloud. Para obter instruções passo a passo, veja o tutorial: [deploy SSIS packages to Azure](tutorial-deploy-ssis-packages-azure.md) (Implementar pacotes do SSIS no Azure). 
 

## <a name="rich-cross-platform-sdks"></a>SDKs multiplataformas avançados
Se for utilizador avançado e quiser uma interface programática, a versão 2 proporciona um conjunto avançado de SDKs que podem ser utilizados para criar, gerir e monitorizar pipelines com o seu IDE preferido.

- SDK .NET
- PowerShell
- SDK Python

Também pode utilizar as APIs REST para criar fábricas de dados. 

## <a name="load-the-data-into-a-lake"></a>Carregar os dados para um lake
O Data Factory tem mais de 30 conectores, através dos quais pode carregar dados de ambientes híbridos e heterogéneos para o Azure.  Veja os últimos resultados de desempenho dos testes internos e das sugestões de desempenho no [Guia de Desempenho e Otimização](copy-activity-performance.md). Além disso, ativámos recentemente a Elevada Disponibilidade e a Escalabilidade no Runtime de Integração autoalojado que instala num ambiente de rede privada, de modo a dar resposta aos requisitos dos clientes empresariais grandes quanto a melhor disponibilidade e escalabilidade.

## <a name="top-level-concepts-in-version-2"></a>Principais conceitos da versão 2
As subscrições do Azure podem ter uma ou várias instâncias do Azure Data Factory (ou fábricas de dados). O Azure Data Factory é composto por quatro componentes principais que funcionam em conjunto para fornecer a plataforma na qual pode compor fluxos de trabalho condicionados por dados com passos para mover e transformar dados.

### <a name="pipeline"></a>Pipeline
Uma fábrica de dados pode ter um ou mais pipelines. Os pipelines são agrupamentos lógicos de atividades para realizar uma unidade de trabalho. Em conjunto, as atividades num pipeline executam tarefas. Por exemplo, um pipeline pode conter um grupo de atividades que ingere dados de um blob do Azure e, em seguida, executa uma consulta de Hive num cluster do HDInsight para particionar os dados. A vantagem neste caso é que o pipeline lhe permite gerir as atividades como um conjunto, em vez de individualmente. As atividades num pipeline podem ser encadeadas para funcionar sequencialmente ou podem funcionar de forma independente em paralelo

### <a name="activity"></a>Atividade
As atividades representam uma fase de processamento num pipeline. Por exemplo, pode utilizar uma atividade de Cópia para copiar dados de um arquivo de dados para outro. Da mesma forma, pode utilizar uma Atividade do Hive, que executa uma consulta do Hive num cluster do Azure HDInsight, para transformar ou analisar os seus dados. O Data Factory suporta três tipos de atividades: atividades de movimento de dados, atividades de transformação de dados e atividades de controlo

### <a name="datasets"></a>Conjuntos de dados
Os conjuntos de dados representam estruturas de dados nos arquivos de dados, que simplesmente apontam ou referenciam os dados que pretende utilizar nas suas atividades como entrada ou saída. 

### <a name="linked-services"></a>Serviços ligados
Os serviços ligados são muito semelhantes às cadeias de ligação, que definem as informações de ligação necessárias para que o Data Factory se possa ligar a recursos externos. Encare da seguinte forma: um serviço ligado define a ligação à origem de dados e um conjunto de dados representa a estrutura dos dados. Por exemplo, um serviço ligado do Armazenamento do Azure especifica a cadeia de ligação para ligar à conta do Armazenamento do Azure. Além disso, um conjunto de dados de Blob do Azure especifica o contentor de blob e a pasta que contém os dados.

Os serviços ligados são utilizados para duas finalidades no Data Factory:

- Para representar um **arquivo de dados**, incluindo, entre outros, num SQL Server no local, numa base de dados Oracle, numa partilha de ficheiros ou numa conta do Armazenamento de Blobs do Azure. Para obter uma lista dos arquivos de dados suportados, veja o artigo [copy activity](copy-activity-overview.md) (Atividade de Cópia).
- Para representar um **recurso de computação** que pode alojar a execução de uma atividade. Por exemplo, a Atividade HDInsightHive é executada num cluster do HDInsight Hadoop. Para obter uma lista das atividades de transformação e os ambientes de computação suportados, veja o artigo [transform data](transform-data.md) (transformar dados).

### <a name="triggers"></a>Acionadores
Os acionadores representam a unidade de processamento que determina quando é que uma execução de pipeline tem de arrancar. Existem diferentes tipos de acionadores para diferentes tipos de eventos; na pré-visualização, suportamos o acionador Scheduler de tempo de execução. 


### <a name="pipeline-runs"></a>Instâncias de Pipeline
As instâncias de pipeline são instâncias da execução dos pipelines. Normalmente, as Instâncias de Pipeline são instanciadas pela transmissão de argumentos ais parâmetros definidos nos pipelines. Os argumentos podem ser transmitidos manualmente ou na definição do acionador

### <a name="parameters"></a>Parâmetros
Os parâmetros são pares chave-valor de configuração só de leitura.  Os parâmetros são definidos no pipeline e os argumentos para os parâmetros definidos são transmitidos durante a execução a partir do contexto da instância criado por um acionador ou por um pipeline executado manualmente. As atividades dentro do pipeline consomem os valores dos parâmetros.
Os Conjuntos de Dados são parâmetros inflexíveis e uma entidade reutilizável/referenciável. As atividades podem referenciar conjuntos de dados e podem consumir as propriedades definidas na definição do Conjunto de Dados

Os serviços ligados também são um parâmetro inflexível que contém as informações de ligação a um arquivo de dados ou a um ambiente de computação. Também é uma entidade reutilizável/referenciável.

### <a name="control-flow"></a>Fluxo de Controlo
Orquestração das atividades dos pipelines que incluem o encadeamento de atividades numa sequência, a ramificação e parâmetros que podem ser definidos ao nível do pipeline e argumentos transmitidos ao invocar o pipeline a pedido ou a partir de um acionador. Também inclui a transmissão de estado personalizado e os contentores de ciclo, ou seja, iteradores For-each.


Para obter mais informações sobre os conceitos do Data Factory, veja os artigos seguintes:

- [Dataset and linked services](concepts-datasets-linked-services.md) (Conjuntos de dados e serviços ligados)
- [Pipelines e atividades](concepts-pipelines-activities.md)
- [Integration runtime](concepts-integration-runtime.md) (Runtime de integração)

## <a name="supported-regions"></a>Regiões Suportadas:

Atualmente, pode criar fábricas de dados nas regiões E.U.A. Leste e E.U.A. Leste 2. No entanto, uma fábrica de dados pode aceder aos arquivos de dados e serviços de computação noutras regiões do Azure para mover dados entre os arquivos de dados ou processar dados com serviços de computação.

O Azure Data Factory propriamente dito não armazena quaisquer dados. Permite-lhe criar fluxos de trabalho orientados por dados para orquestrar o movimento dos dados entre arquivos de dados suportados e o processamento de dados com serviços de computação noutras regiões ou num ambiente no local. Também lhe permite monitorizar e gerir fluxos de trabalho com mecanismos programáticos e de IU.

Apesar de o Data Factory estar disponível apenas nas regiões E.U.A. Leste e E.U.A. Leste 2, o serviço que alimenta o movimento de dados no Data Factory está disponível globalmente em várias regiões. Caso um arquivo de dados se encontre protegido por uma firewall, então, os dados são movidos por um gateway do Data Management Gateway instalado no seu ambiente no local.

Por exemplo, vamos assumir que os seus ambientes de computação, tais como o cluster Azure HDInsight e o Azure Machine Learning, estão a ficar sem a região Europa Ocidental. Pode criar e utilizar uma instância do Azure Data Factory na Europa do Norte e utilizá-la para agendar tarefas nos seus ambientes de computação na Europa Ocidental. Demora alguns milissegundos até o Data Factory acionar a tarefa no seu ambiente de computação, mas o tempo de execução da tarefa no seu ambiente de computação não sofre alterações.

## <a name="next-steps"></a>Passos seguintes
Saiba como criar uma fábrica de dados com as instruções passo a passo nos Inícios Rápidos [PowerShell](quickstart-create-data-factory-powershell.md), [.NET](quickstart-create-data-factory-dot-net.md), [Python](quickstart-create-data-factory-python.md), [REST API](quickstart-create-data-factory-rest-api.md) e portal do Azure. 


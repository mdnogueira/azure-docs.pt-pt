---
title: "Introdução ao Data Factory, um serviço de integração de dados | Microsoft Docs"
description: "Saiba o que o Azure Data Factory é: um serviço de integração de dados em nuvem que orquestra e automatiza o movimento e a transformação de dados."
keywords: data integration, cloud data integration, what is azure data factory
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: monicar
ms.assetid: cec68cb5-ca0d-473b-8ae8-35de949a009e
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 04/21/2017
ms.author: shlo
translationtype: Human Translation
ms.sourcegitcommit: 260208e7c7a08110eb3c885ef86ec4c18ff42fc9
ms.openlocfilehash: 40552b5d3cea5b04826c08e7b4b1d046a9fcefba
ms.lasthandoff: 04/23/2017


---
# <a name="introduction-to-azure-data-factory-service-a-data-integration-service-in-the-cloud"></a>Introdução ao Azure Data Factory Service, um serviço de integração de dados em nuvem
## <a name="what-is-azure-data-factory"></a>O que é o Azure Data Factory?
No mundo dos macrodados, de que modo é que as empresas tiram partido dos dados existentes? É possível utilizar dados de referência de origens de dados no local ou de outras origens de dados diferentes para enriquecer os dados gerados na cloud? Assim, o que é preciso é uma plataforma para agregar e processar dados de várias origens. O Azure Data Factory é um serviço de integração de dados baseado na cloud que orquestra e automatiza o **movimento** e a **transformação** de dados. Pode criar soluções de integração de dados capazes de ingerir dados de entrada provenientes de diferentes arquivos de dados, transformar/processar os dados e publicar os dados de saída noutros arquivos de dados. 

![Diagrama: descrição geral do Data Factory Overview, um serviço de integração de dados](./media/data-factory-introduction/what-is-azure-data-factory.png)

**Figura1.** Ingira dados de várias origens de dados, prepare-os, transforme-os e analise-os e, depois, publique dados prontos a utilizar para consumo.


## <a name="what-does-it-offer"></a>O que oferece? 
Tradicionalmente, os projetos de integração de dados desenvolvem-se em torno da criação de processos de Extração, Transformação e Carregamento (ETL) que extraem dados de várias origens de dados de uma organização, transformam os dados para estarem em conformidade com o esquema de destino de um Armazém de Dados Empresarial (EDW) e os carregam para um EDW, conforme mostrado na imagem seguinte. Em seguida, o EDW é acedido como sendo a única origem de verdade para as soluções de análise de BI.

![ETL Tradicional](media/data-factory-introduction/traditional-etl.png)
**Traditional ETL**

O cenário atual relativo aos dados para as empresas continua a crescer exponencialmente em volume, variedade e complexidade, conforme mostrado na imagem seguinte. É mais diverso do que alguma vez foi, com dados gerados no local e na cloud, de diferentes formas e ritmos. O processamento de dados tem de ser feito em localizações geográficas e inclui uma combinação de software de código aberto, soluções comerciais e serviços de processamento personalizados, que são dispendiosos e difíceis de integrar e manter. A agilidade necessária à adaptação ao atual cenário mutável dos macrodados é uma oportunidade para dotar o EDW tradicional com as capacidades exigidas pelos sistemas de produção de informação modernos. O Azure Data Factory é a plataforma de composição para trabalhar em vários EDWs tradicionais e no cenário mutável dos dados, para capacitar as empresas a tirar partido de todos os dados disponíveis para a tomada de decisões orientada por dados.

![Novo cenário de macrodados](media/data-factory-introduction/new-big-data-landscape.png)
**New big data landscape**

O serviço Azure Data Factory capacita as empresas a aproveitar esta diversidade, ao disponibilizar uma plataforma para **compor serviços de processamento, armazenamento e movimento de dados em pipelines de produção de informação** e gerir ativos de dados fiáveis.

O serviço Azure Data Factory permite-lhe:
- **Trabalhar facilmente com vários sistemas de armazenamento e processamento de dados**. 

    As empresas têm dados de diferentes tipos localizados em diferentes origens. O primeiro passo na criação de um sistema de produção de informação é ligar a todas as origens de dados e processamento necessários, como serviços SaaS, partilhas de ficheiros, FTP, serviços Web, e mover os dados para uma localização centralizada, conforme necessário, para processamento subsequente.

    Sem o Data Factory, as empresas têm de criar componentes de movimento de dados personalizados ou escrever serviços personalizados para integrar essas origens de dados e esse processamento. Esta é uma opção dispendiosa e estes sistemas são de difícil integração e manutenção e, muitas vezes, não têm a monitorização e os alertas de nível empresarial nem os controlos que os serviços totalmente geridos podem oferecer.

    Com o Data Factory, pode utilizar a Atividade de Cópia num pipeline de dados para mover dados de arquivos de dados no local e na cloud para um arquivo centralizado na cloud, para análises adicionais. Por exemplo, pode recolher dados numa fábrica do Azure Data Lake Store e transformá-los posteriormente, utilizando um serviço de computação do Azure Data Lake Analytics. Em alternativa, pode recolher dados no Armazenamento de Blobs do Azure e transformá-los mais tarde, utilizando um cluster do Azure HDInsight Hadoop.
- **Transformar dados em informações fidedignas**. 

    Quando os dados estiverem presentes num arquivo de dados centralizado na cloud, é útil criar e implementar pipelines de dados para produzir, de forma fiável, dados transformados de acordo com uma agenda, que pode ser mantida e controlada, para alimentar os ambientes de produção com dados fidedignos. A transformação de dados no Azure Data Factory é realizada por atividades de transformação, como atividades de Hive, Pig, MapReduce, Azure Machine Learning Batch Execution e C# personalizado em execução no cluster do Azure HDInsight Hadoop, em VMs do Azure Machine Learning ou em conjuntos de VMs do Azure Batch.
- **Monitorizar pipelines de dados num único local**.

    Com um portefólio de dados diversificado, é importante ter uma visão fidedigna e completa dos seus serviços de armazenamento, processamento e movimentos de dados. O Data Factory ajuda-o a avaliar rapidamente o estado de funcionamento do pipeline de dados ponto a ponto, identificar problemas e tomar medidas corretivas, se necessário. Monitorize visualmente a linhagem de dados e as relações entre os dados em todas as suas origens. Veja todos os registos históricos da execução do trabalho, estado de funcionamento do sistema e dependências a partir de um único dashboard de monitorização.

## <a name="how-does-it-work"></a>Como funciona? 
Existem três fases de produção de informações numa fábrica do Azure Data Factory:

![Três fases de produção de informações](media/data-factory-introduction/three-information-production-stages.png)

- **Ligar e Recolher**: nesta fase, são recolhidos numa única localização dados de várias origens.
- **Transformar e Enriquecer**: nesta fase, os dados recolhidos são processados ou transformados.
- **Publicar**. Nesta fase, os dados são publicados, para que possam ser consumidos pelas ferramentas de BI, por ferramentas de análise e por outras aplicações.

## <a name="key-components"></a>Componentes principais
As subscrições do Azure podem ter uma ou várias instâncias do Azure Data Factory (ou fábricas de dados). O Azure Data Factory é composto por quatro componentes principais que funcionam em conjunto para fornecer a plataforma na qual pode compor movimentos de dados e orquestrações de transformação simples ou complexos para o seu fluxo de dados.

### <a name="activity"></a>Atividade
As Atividades definem as ações a efetuar nos seus dados. Por exemplo, pode utilizar uma atividade de Cópia para copiar dados de um arquivo de dados para outro. Da mesma forma, pode utilizar uma Atividade do Hive, que executa uma consulta do Hive num cluster do Azure HDInsight, para transformar ou analisar os seus dados. O Data Factory suporta dois tipos de atividades -- atividades de movimento de dados e atividades de transformação de dados.

Cada atividade pode ter zero ou mais conjuntos de dados de entrada e produzir um ou mais conjuntos de dados de saída. 


### <a name="data-movement-activities"></a>Atividades de movimento de dados
A Atividade de Cópia no Data Factory copia os dados de um arquivo de dados de origem para um arquivo de dados sink. O Data Factory suporta os arquivos de dados seguintes. Os dados de qualquer origem podem ser escritos em qualquer sink. Clique num arquivo de dados para saber como copiar dados de e para esse arquivo.

[!INCLUDE [data-factory-supported-data-stores](../../includes/data-factory-supported-data-stores.md)]

Para obter mais informações, veja o artigo [Data Movement Activities (Atividades de Movimento de Dados)](data-factory-data-movement-activities.md).

### <a name="data-transformation-activities"></a>Atividades de transformação de dados
[!INCLUDE [data-factory-transformation-activities](../../includes/data-factory-transformation-activities.md)]

Para obter mais informações, veja o artigo [Data Transformation Activities (Atividades de Transformação de Dados)](data-factory-data-transformation-activities.md).

Se precisar de mover dados de/para um arquivo de dados que a Atividade de Cópia não suporte ou de transformar dados com a sua própria lógica, crie uma **atividade .NET personalizada**. Para obter detalhes sobre criar e utilizar uma atividade personalizada, veja [Use custom activities in an Azure Data Factory pipeline (Utilizar atividades personalizadas num pipeline do Azure Data Factory)](data-factory-use-custom-activities.md).

### <a name="pipeline"></a>Pipeline
Um pipeline é um grupo de atividades. Em conjunto, as atividades num pipeline executam tarefas. Por exemplo, um pipeline pode conter um grupo de atividades que ingere dados de um blob do Azure e, em seguida, executa uma consulta de Hive num cluster do HDInsight para particionar os dados de registo. A vantagem neste caso é que o pipeline lhe permite gerir as atividades como um conjunto, em vez de individualmente. Por exemplo, pode implementar e agendar o pipeline em vez das atividades individualmente.

### <a name="datasets"></a>Conjuntos de dados
Os conjuntos de dados representam estruturas de dados nos arquivos de dados, que simplesmente apontam ou referenciam os dados que pretende utilizar nas suas atividades como entrada ou saída. Por exemplo, um conjunto de dados do Blob do Azure especifica o contentor de blobs e a pasta no Armazenamento de Blobs do Azure a partir dos quais o pipeline deve ler os dados. 

### <a name="linked-services"></a>Serviços ligados
Os serviços ligados são muito semelhantes às cadeias de ligação, que definem as informações de ligação necessárias para que o Data Factory se possa ligar a recursos externos. Encare da seguinte forma: o conjunto de dados representa a estrutura dos dados e o serviço ligado define a ligação à origem de dados.  Os serviços ligados são utilizados para duas finalidades no Data Factory:

* Para representar um **arquivo de dados**, incluindo, entre outros, num SQL Server no local, numa base de dados Oracle, numa partilha de ficheiros ou numa conta do Armazenamento de Blobs do Azure. Veja a secção [Data movement activities (Atividades de movimento de dados)](#data-movement-activities) para obter uma lista dos arquivos de dados suportados.
* Para representar um **recurso de computação** que pode alojar a execução de uma atividade. Por exemplo, a Atividade HDInsightHive é executada num cluster do HDInsight Hadoop. Veja a secção [Atividades de transformação de dados](#data-transformation-activities) para obter uma lista dos ambientes de computação suportados.

### <a name="relationship-between-data-factory-entities"></a>Relação entre as entidades do Data Factory
![Diagrama: Data Factory, um serviço de integração de dados na nuvem - Conceitos-chave](./media/data-factory-introduction/data-integration-service-key-concepts.png)
**Figura 2.** Relações entre o Conjunto de Dados, Atividade, Pipeline e Serviço Ligado

## <a name="supported-regions"></a>Regiões suportadas
Atualmente, pode criar fábricas de dados nas regiões **E.U.A. Oeste**, **E.U.A. Leste** e **Europa do Norte**. No entanto, uma fábrica de dados pode aceder aos arquivos de dados e serviços de computação noutras regiões do Azure para mover dados entre os arquivos de dados ou processar dados com serviços de computação.

O Azure Data Factory propriamente dito não armazena quaisquer dados. Este permite-lhe criar fluxos de trabalho para orquestrar o movimento dos dados entre [arquivos de dados suportados](data-factory-data-movement-activities.md#supported-data-stores-and-formats) e o processamento de dados com [serviços de computação](data-factory-compute-linked-services.md) noutras regiões ou num ambiente no local. Também permite [monitorizar e gerir fluxos de trabalho](data-factory-monitor-manage-pipelines.md) com mecanismos programáticos e de IU.

Apesar de o Data Factory estar disponível apenas nas regiões **E.U.A. Oeste**, **E.U.A. Leste** e **Europa do Norte**, o serviço que alimenta o movimento de dados no Data Factory está disponível [globalmente](data-factory-data-movement-activities.md#global) em várias regiões. Caso um arquivo de dados se encontre protegido por uma firewall, então, um [Data Management Gateway](data-factory-move-data-between-onprem-and-cloud.md) instalado no seu ambiente no local move os dados.

Por exemplo, vamos assumir que os seus ambientes de computação, tais como o cluster Azure HDInsight e o Azure Machine Learning, estão a ficar sem a região Europa Ocidental. Pode criar e utilizar uma instância do Azure Data Factory na Europa do Norte e utilizá-la para agendar tarefas nos seus ambientes de computação na Europa Ocidental. Demora alguns milissegundos até o Data Factory acionar a tarefa no seu ambiente de computação, mas o tempo de execução da tarefa no seu ambiente de computação não sofre alterações.

Pretendemos ter o Azure Data Factory em cada geografia suportada pelo Azure no futuro.

## <a name="next-steps"></a>Passos seguintes
Para saber como criar fábricas de dados com pipelines de dados, siga as instruções passo a passo nos tutoriais seguintes:

| Tutorial | Descrição |
| --- | --- |
| [Move data between two cloud data stores (Mover dados entre dois arquivos de dados na cloud)](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) |Neste tutorial, vai criar uma fábrica de dados com um pipeline que **move dados** do armazenamento de Blobs para a base de dados SQL. |
| [Transform data using Hadoop cluster (Utilizar o cluster do Hadoop para transformar dados)](data-factory-build-your-first-pipeline.md) |Neste tutorial, vai criar a primeira fábrica de dados do Azure com um pipeline de dados que **processa dados** através da execução do script Hive num cluster do Azure HDInsight (Hadoop). |
| [Move data between an on-premises data store and a cloud data store using Data Management Gateway (Utilizar o Data Management Gateway para mover dados entre um arquivo de dados no local e um arquivo de dados na cloud)](data-factory-move-data-between-onprem-and-cloud.md) |Neste tutorial, vai criar uma fábrica de dados com um pipeline que **move dados** de uma base de dados do SQL Server **no local** para um blob do Azure. Como parte das instruções, vai instalar e configurar o Data Management Gateway no seu computador. |


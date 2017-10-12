---
title: "Introdução ao Data Factory, um serviço de integração de dados | Microsoft Docs"
description: "Saiba o que o Azure Data Factory é: um serviço de integração de dados em nuvem que orquestra e automatiza o movimento e a transformação de dados."
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
ms.date: 08/14/2017
ms.author: shlo
robots: noindex
ms.openlocfilehash: 8d4d574dee4b993d8a464482b244e1f63ade8a57
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="introduction-to-azure-data-factory"></a>Introdução ao Azure Data Factory 
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versão 1 - GA](data-factory-introduction.md)
> * [Versão 2 - Pré-visualização](../introduction.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory, que está geralmente disponível (GA). Se estiver a utilizar a versão 2 do serviço Data Factory, que está em pré-visualização, consulte [Introdução ao Data Factory V2](../introduction.md).


## <a name="what-is-azure-data-factory"></a>O que é o Azure Data Factory?
No mundo dos macrodados, de que modo é que as empresas tiram partido dos dados existentes? É possível utilizar dados de referência de origens de dados no local ou de outras origens de dados diferentes para enriquecer os dados gerados na cloud? Por exemplo, uma empresa de jogos recolhe muitos registos produzidos pelos jogos na cloud. Pretende analisar estes registos para obter informações sobre as preferências dos clientes, dados demográficos, etc. para identificar oportunidades de venda superior e venda cruzada, desenvolver novas funcionalidades apelativas para impulsionar o crescimento do negócio e proporcionar uma melhor experiência aos clientes. 

Para analisar estes registos, a empresa tem de utilizar os dados de referência, tais como informações do cliente, informações do jogo, informações de campanhas de marketing, que se encontram num arquivo de dados no local. Por isso, a empresa pretende ingerir dados de registo provenientes do arquivo de dados na cloud e dados de referência provenientes do arquivo de dados no local. Em seguida, processar os dados através do Hadoop na cloud (Azure HDInsight) e publicar os dados de resultado num armazém de dados na cloud, como o Azure SQL Data Warehouse, ou um arquivo de dados no local, como o SQL Server. Pretende que este fluxo de trabalho seja executado uma vez por semana. 

É necessário uma plataforma que permita à empresa criar um fluxo de trabalho que consiga ingerir dados provenientes de arquivos de dados no local e na cloud, e transformar ou processar os dados através dos serviços de computação existentes, como o Hadoop, e publicar os resultados num arquivo de dados no local ou na cloud para serem consumidos por aplicações de BI. 

![Descrição geral do Data Factory](media/data-factory-introduction/what-is-azure-data-factory.png) 

O Azure Data Factory é a plataforma para este tipo de cenários. É um **serviço de integração de dados baseado na cloud que permite criar fluxos de trabalho condicionados por dados na cloud para orquestrar e automatizar o movimento e a transformação de dados**. Com o Azure Data Factory, poderá criar e agendar fluxos de trabalho condicionados por dados (denominados pipelines) que são capazes de ingerir dados provenientes de diferentes arquivos de dados, processar/transformar os dados através de serviços de computação, tais como o Azure HDInsight Hadoop, Spark, Azure Data Lake Analytics e Azure Machine Learning, e publicar os dados de saída em arquivos de dados como o Azure SQL Data Warehouse para serem consumidos por aplicações de business intelligence (BI).  

É mais uma plataforma de Extração e Carregamento (EL) e, em seguida, Transformação e Carregamento (TL) do que uma plataforma de Extração, Transformação e Carregamento (ETL) tradicional. As transformações realizadas servem para transformar/processar dados através de serviços de computação em vez de realizar transformações como aquelas para adicionar colunas derivadas, contar o número de linhas, ordenar dados, etc. 

Atualmente, no Azure Data Factory, os dados que são consumidos e produzidos pelos fluxos de trabalho são **dados repartidos pelo tempo** (de hora a hora, diariamente, semanalmente, etc.). Por exemplo, um pipeline pode ler dados de entrada, processar dados e produzir dados de saída uma vez por dia. Também pode executar um fluxo de trabalho apenas uma vez.  
  

## <a name="how-does-it-work"></a>Como funciona? 
Os pipelines (fluxos de trabalho condicionados por dados) no Azure Data Factory executam, normalmente, os três passos seguintes:

![Três fases do Azure Data Factory](media/data-factory-introduction/three-information-production-stages.png)

### <a name="connect-and-collect"></a>Ligar e recolher
As empresas têm dados de diversos tipos localizados em diferentes origens. O primeiro passo na criação de um sistema de produção de informação é ligar a todas as origens de dados e processamento necessários, como serviços SaaS, partilhas de ficheiros, FTP, serviços Web, e mover os dados para uma localização centralizada, conforme necessário, para processamento subsequente.

Sem o Data Factory, as empresas têm de criar componentes de movimento de dados personalizados ou escrever serviços personalizados para integrar essas origens de dados e esse processamento. É uma opção dispendiosa e estes sistemas são de difícil integração e manutenção e, muitas vezes, não têm a monitorização e os alertas de nível empresarial nem os controlos que os serviços totalmente geridos podem oferecer.

Com o Data Factory, pode utilizar a Atividade de Cópia num pipeline de dados para mover dados de arquivos de dados no local e na cloud para um arquivo centralizado na cloud, para análises adicionais. Por exemplo, pode recolher dados numa fábrica do Azure Data Lake Store e transformá-los posteriormente, utilizando um serviço de computação do Azure Data Lake Analytics. Em alternativa, pode recolher dados no Armazenamento de Blobs do Azure e transformá-los mais tarde, utilizando um cluster do Azure HDInsight Hadoop.

### <a name="transform-and-enrich"></a>Transformar e enriquecer
Assim que os dados estiverem presentes num arquivo de dados centralizado na cloud, pretende que os dados recolhidos sejam processados ou transformados através da utilização de serviços de computação, tais como o HDInsight Hadoop, Spark, Data Lake Analytics e Machine Learning. Pretende produzir de forma fiável os dados transformados numa agenda controlada e sustentável, para alimentar ambientes de produção com dados fidedignos. 

### <a name="publish"></a>Publicar 
Forneça dados transformados a partir da cloud para origens no local como o SQL Server ou mantenha-os nas origens de armazenamento da cloud para consumo pelo business intelligence (BI) e ferramentas de análise e outras aplicações.

## <a name="key-components"></a>Componentes principais
As subscrições do Azure podem ter uma ou várias instâncias do Azure Data Factory (ou fábricas de dados). O Azure Data Factory é composto por quatro componentes principais que funcionam em conjunto para fornecer a plataforma na qual pode compor fluxos de trabalho condicionados por dados com passos para mover e transformar dados. 

### <a name="pipeline"></a>Pipeline
Uma fábrica de dados pode ter um ou mais pipelines. Um pipeline é um grupo de atividades. Em conjunto, as atividades num pipeline executam tarefas. Por exemplo, um pipeline pode conter um grupo de atividades que ingere dados de um blob do Azure e, em seguida, executa uma consulta de Hive num cluster do HDInsight para particionar os dados. A vantagem neste caso é que o pipeline lhe permite gerir as atividades como um conjunto, em vez de individualmente. Por exemplo, pode implementar e agendar o pipeline em vez das atividades individualmente. 

### <a name="activity"></a>Atividade
Um pipeline pode ter uma ou mais atividades. As Atividades definem as ações a efetuar nos seus dados. Por exemplo, pode utilizar uma atividade de Cópia para copiar dados de um arquivo de dados para outro. Da mesma forma, pode utilizar uma Atividade do Hive, que executa uma consulta do Hive num cluster do Azure HDInsight, para transformar ou analisar os seus dados. O Data Factory suporta dois tipos de atividades -- atividades de movimento de dados e atividades de transformação de dados.

### <a name="data-movement-activities"></a>Atividades de movimento de dados
A Atividade de Cópia no Data Factory copia os dados de um arquivo de dados de origem para um arquivo de dados sink. O Data Factory suporta os arquivos de dados seguintes. Os dados de qualquer origem podem ser escritos em qualquer sink. Clique num arquivo de dados para saber como copiar dados de e para esse arquivo.

[!INCLUDE [data-factory-supported-data-stores](../../../includes/data-factory-supported-data-stores.md)]

Para obter mais informações, veja o artigo [Data Movement Activities (Atividades de Movimento de Dados)](data-factory-data-movement-activities.md).

### <a name="data-transformation-activities"></a>Atividades de transformação de dados
[!INCLUDE [data-factory-transformation-activities](../../../includes/data-factory-transformation-activities.md)]

Para obter mais informações, veja o artigo [Data Transformation Activities (Atividades de Transformação de Dados)](data-factory-data-transformation-activities.md).

### <a name="custom-net-activities"></a>Atividades .NET personalizadas
Se precisar de mover dados de/para um arquivo de dados que a Atividade de Cópia não suporte ou de transformar dados com a sua própria lógica, crie uma **atividade .NET personalizada**. Para obter detalhes sobre criar e utilizar uma atividade personalizada, veja [Use custom activities in an Azure Data Factory pipeline (Utilizar atividades personalizadas num pipeline do Azure Data Factory)](data-factory-use-custom-activities.md).

### <a name="datasets"></a>Conjuntos de dados
Uma atividade aceita zero ou mais conjuntos de dados como entradas e um ou mais conjuntos de dados como saídas. Os conjuntos de dados representam estruturas de dados nos arquivos de dados, que simplesmente apontam ou referenciam os dados que pretende utilizar nas suas atividades como entrada ou saída. Por exemplo, um conjunto de dados do Blob do Azure especifica o contentor de blobs e a pasta no Armazenamento de Blobs do Azure a partir dos quais o pipeline deve ler os dados. Em alternativa, um conjunto de dados de Tabela SQL do Azure especifica a tabela no qual os dados de saída são escritos pela atividade. 

### <a name="linked-services"></a>Serviços ligados
Os serviços ligados são muito semelhantes às cadeias de ligação, que definem as informações de ligação necessárias para que o Data Factory se possa ligar a recursos externos. Encare da seguinte forma: um serviço ligado define a ligação à origem de dados e um conjunto de dados representa a estrutura dos dados. Por exemplo, um serviço ligado do Armazenamento do Azure especifica a cadeia de ligação para ligar à conta do Armazenamento do Azure. Além disso, um conjunto de dados de Blob do Azure especifica o contentor de blob e a pasta que contém os dados.   

Os serviços ligados são utilizados para duas finalidades no Data Factory:

* Para representar um **arquivo de dados**, incluindo, entre outros, num SQL Server no local, numa base de dados Oracle, numa partilha de ficheiros ou numa conta do Armazenamento de Blobs do Azure. Veja a secção [Data movement activities (Atividades de movimento de dados)](#data-movement-activities) para obter uma lista dos arquivos de dados suportados.
* Para representar um **recurso de computação** que pode alojar a execução de uma atividade. Por exemplo, a Atividade HDInsightHive é executada num cluster do HDInsight Hadoop. Veja a secção [Atividades de transformação de dados](#data-transformation-activities) para obter uma lista dos ambientes de computação suportados.

### <a name="relationship-between-data-factory-entities"></a>Relação entre as entidades do Data Factory
![Diagrama: Data Factory, um serviço de integração de dados na nuvem - Conceitos-chave](./media/data-factory-introduction/data-integration-service-key-concepts.png)
**Figura 2.** Relações entre o Conjunto de Dados, Atividade, Pipeline e Serviço Ligado

## <a name="supported-regions"></a>Regiões suportadas
Atualmente, pode criar fábricas de dados nas regiões **E.U.A. Oeste**, **E.U.A. Leste** e **Europa do Norte**. No entanto, uma fábrica de dados pode aceder aos arquivos de dados e serviços de computação noutras regiões do Azure para mover dados entre os arquivos de dados ou processar dados com serviços de computação.

O Azure Data Factory propriamente dito não armazena quaisquer dados. Permite-lhe criar fluxos de trabalho condicionados por dados para orquestrar o movimento dos dados entre [arquivos de dados suportados](#data-movement-activities) e o processamento de dados com [serviços de computação](#data-transformation-activities) noutras regiões ou num ambiente no local. Também permite [monitorizar e gerir fluxos de trabalho](data-factory-monitor-manage-pipelines.md) com mecanismos programáticos e de IU.

Apesar de o Data Factory estar disponível apenas nas regiões **E.U.A. Oeste**, **E.U.A. Leste** e **Europa do Norte**, o serviço que alimenta o movimento de dados no Data Factory está disponível [globalmente](data-factory-data-movement-activities.md#global) em várias regiões. Caso um arquivo de dados se encontre protegido por uma firewall, então, um [Gateway de Gestão de Dados](data-factory-move-data-between-onprem-and-cloud.md) instalado no seu ambiente no local move os dados.

Por exemplo, vamos assumir que os seus ambientes de computação, tais como o cluster Azure HDInsight e o Azure Machine Learning, estão a ficar sem a região Europa Ocidental. Pode criar e utilizar uma instância do Azure Data Factory na Europa do Norte e utilizá-la para agendar tarefas nos seus ambientes de computação na Europa Ocidental. Demora alguns milissegundos até o Data Factory acionar a tarefa no seu ambiente de computação, mas o tempo de execução da tarefa no seu ambiente de computação não sofre alterações.

## <a name="get-started-with-creating-a-pipeline"></a>Introdução à criação de um pipeline
Pode utilizar uma destas ferramentas ou APIs para criar pipelines de dados no Azure Data Factory: 

- Portal do Azure
- Visual Studio
- PowerShell
- API .NET
- API REST
- Modelo do Azure Resource Manager. 

Para saber como criar fábricas de dados com pipelines de dados, siga as instruções passo a passo nos tutoriais seguintes:

| Tutorial | Descrição |
| --- | --- |
| [Move data between two cloud data stores (Mover dados entre dois arquivos de dados na cloud)](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) |Neste tutorial, vai criar uma fábrica de dados com um pipeline que **move dados** do armazenamento de Blobs para a base de dados SQL. |
| [Transform data using Hadoop cluster (Utilizar o cluster do Hadoop para transformar dados)](data-factory-build-your-first-pipeline.md) |Neste tutorial, vai criar a primeira fábrica de dados do Azure com um pipeline de dados que **processa dados** através da execução do script Hive num cluster do Azure HDInsight (Hadoop). |
| [Move data between an on-premises data store and a cloud data store using Data Management Gateway (Utilizar o Data Management Gateway para mover dados entre um arquivo de dados no local e um arquivo de dados na cloud)](data-factory-move-data-between-onprem-and-cloud.md) |Neste tutorial, vai criar uma fábrica de dados com um pipeline que **move dados** de uma base de dados do SQL Server **no local** para um blob do Azure. Como parte das instruções, vai instalar e configurar o Data Management Gateway no seu computador. |

<properties 
    pageTitle="Introdução ao Data Factory, um serviço de integração de dados | Microsoft Azure" 
    description="Saiba o que o Azure Data Factory é: um serviço de integração de dados em nuvem que orquestra e automatiza o movimento e a transformação de dados." 
    keywords="data integration, cloud data integration, what is azure data factory"
    services="data-factory" 
    documentationCenter="" 
    authors="spelluru" 
    manager="jhubbard" 
    editor="monicar"/>

<tags 
    ms.service="data-factory" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="get-started-article" 
    ms.date="09/22/2016" 
    ms.author="spelluru"/>


# Introdução ao Azure Data Factory Service, um serviço de integração de dados em nuvem

## O que é o Azure Data Factory? 
O Data Factory é um serviço de integração de dados baseado na nuvem que orquestra e automatiza o **movimento** e a **transformação** de dados. Pode criar soluções de integração de dados com o serviço Data Factory, que pode ingerir dados de vários arquivos de dados, transformá-los/processá-los e publicar os dados resultantes nos arquivos de dados. 

O serviço Data Factory permite-lhe criar pipelines de dados que movimentam e transformam dados e, depois, executar esses pipelines numa agenda especificada (hora a hora, diariamente, semanalmente, etc.). Também proporciona visualizações ricas para apresentar a linhagem e as dependências entre os pipelines de dados e monitorizá-los a partir de uma vista unificada única, para identificar facilmente problemas e configurar alertas de monitorização.

![Diagrama: descrição geral do Data Factory, um serviço de integração de dados](./media/data-factory-introduction/what-is-azure-data-factory.png)
**Figure1.** Ingira dados de várias origens de dados, prepare-os, transforme-os e analise-os e, depois, publique dados prontos a utilizar para consumo.

## Pipelines e atividades
Numa solução do Data Factory, pode criar um ou mais **pipelines** de dados. Os pipelines são agrupamentos lógicos de atividades. São utilizados para agrupar atividades numa unidade que, em conjunto, executa uma tarefa. 

As **atividades** definem as ações a efetuar nos seus dados. Por exemplo, pode utilizar uma atividade de Cópia para copiar dados de um arquivo de dados para outro. Da mesma forma, pode utilizar uma Atividade do Hive, que executa uma consulta do Hive num cluster do Azure HDInsight, para transformar ou analisar os seus dados. O Data Factory suporta dois tipos de atividades -- atividades de movimento de dados e atividades de transformação de dados. 
  
## Atividades de movimento de dados 
[AZURE.INCLUDE [data-factory-supported-data-stores](../../includes/data-factory-supported-data-stores.md)]

Veja o artigo [Data Movement Activities (Atividades de Movimentos de Dados)](data-factory-data-movement-activities.md) para obter mais detalhes. 

## Atividades de transformação de dados
[AZURE.INCLUDE [data-factory-transformation-activities](../../includes/data-factory-transformation-activities.md)]

Veja o artigo [Data Transformation Activities (Atividades de Transformação de Dados)](data-factory-data-transformation-activities.md) para obter mais detalhes.

Se precisar de mover dados de/para um arquivo de dados que a Atividade de Cópia não suporte ou de transformar dados com a sua própria lógica, crie uma **atividade .NET personalizada**. Para obter detalhes sobre criar e utilizar uma atividade personalizada, veja [Use custom activities in an Azure Data Factory pipeline (Utilizar atividades personalizadas num pipeline do Azure Data Factory)](data-factory-use-custom-activities.md).

## Serviços ligados
Os serviços ligados definem as informações necessárias para que o Data Factory se ligue a recursos externos (exemplos: Armazenamento do Azure, SQL Server no local, Azure HDInsight). Os serviços ligados são utilizados para duas finalidades no Data Factory:

- Para representar um **arquivo de dados**, incluindo, entre outros, num SQL Server no local, numa base de dados Oracle, numa partilha de ficheiros ou numa conta do Armazenamento de Blobs do Azure. Veja a secção [Data movement activities (Atividades de movimento de dados)](data-factory-data-movement-activities.md) para obter uma lista dos arquivos de dados suportados. 
- Para representar um **recurso de computação** que pode alojar a execução de uma atividade. Por exemplo, a Atividade HDInsightHive é executada num cluster do HDInsight Hadoop. Veja a secção [Data transformation activities (Atividades de transformação de dados)](data-factory-data-transformation-activities.md) para obter uma lista dos ambientes de computação suportados. 

## Conjuntos de dados 
Os serviços ligados ligam os arquivos de dados a uma fábrica de dados do Azure. Os conjuntos de dados representam estruturas de dados dentro dos arquivos de dados. Por exemplo, um serviço ligado do Armazenamento do Azure fornece informações de ligação para que o Data Factory se ligue a uma conta do Armazenamento do Azure. Um conjunto de dados do Blob do Azure especifica o contentor de blobs e a pasta no Armazenamento de Blobs do Azure a partir dos quais o pipeline deve ler os dados. Do mesmo modo, um serviço ligado do SQL Azure fornece informações de ligação para uma base de dados SQL do Azure e um conjunto de dados do SQL Azure especifica a tabela que contém os dados.   

## Relação entre as entidades do Data Factory
O Data Factory tem algumas entidades-chave que funcionam em conjunto para definir os dados de entrada e saída, eventos de processamento e a agenda e os recursos necessários para executar o fluxo de dados pretendido.

![Diagrama: Data Factory, um serviço de integração de dados na nuvem – Conceitos-chave](./media/data-factory-introduction/data-integration-service-key-concepts.png)
**Figure 2.** Relações entre o Conjunto de Dados, Atividade, Pipeline e Serviço Ligado

Com os quatro conceitos simples de serviços ligados, conjuntos de dados, atividades e pipelines, está pronto para começar! Pode [criar o seu primeiro pipeline](data-factory-build-your-first-pipeline.md). 

## Regiões suportadas
Atualmente, pode criar fábricas de dados nas regiões **E.U.A. Oeste**, **E.U.A. Leste** e **Europa do Norte**. No entanto, uma fábrica de dados pode aceder aos arquivos de dados e serviços de computação noutras regiões do Azure para mover dados entre os arquivos de dados ou processar dados com serviços de computação. 

O Azure Data Factory propriamente dito não armazena quaisquer dados. Este permite-lhe criar fluxos de trabalho para orquestrar o movimento dos dados entre [arquivos de dados suportados](data-factory-data-movement-activities.md#supported-data-stores) e o processamento de dados com [serviços de computação](data-factory-compute-linked-services.md) noutras regiões ou num ambiente no local. Também permite [monitorizar e gerir fluxos de trabalho](data-factory-monitor-manage-pipelines.md) com mecanismos programáticos e de IU. 

Apesar de o Azure Data Factory estar disponível apenas nas regiões **E.U.A. Oeste**, **E.U.A. Leste** e **Europa do Norte**, o serviço que alimenta o movimento de dados no Data Factory está disponível [globalmente](data-factory-data-movement-activities.md#global) em várias regiões. Caso um arquivo de dados se encontre protegido por uma firewall, então, um [Data Management Gateway](data-factory-move-data-between-onprem-and-cloud.md) instalado no seu ambiente no local move os dados. 

Por exemplo, vamos assumir que os seus ambientes de computação, tais como o cluster Azure HDInsight e o Azure Machine Learning, estão a ficar sem a região Europa Ocidental. Pode criar e utilizar uma instância do Azure Data Factory na Europa do Norte e utilizá-la para agendar tarefas nos seus ambientes de computação na Europa Ocidental. Demora alguns milissegundos até o Data Factory acionar a tarefa no seu ambiente de computação, mas o tempo de execução da tarefa no seu ambiente de computação não sofre alterações.

Pretendemos ter o Azure Data Factory em cada geografia suportada pelo Azure no futuro.
  
## Passos seguintes
Para saber como criar fábricas de dados com pipelines de dados, siga as instruções passo a passo nos tutoriais seguintes. 

Tutorial | Descrição
-------- | -----------
[Criar um pipeline de dados que processa os dados que utilizam o cluster do Hadoop](data-factory-build-your-first-pipeline.md) | Neste tutorial, vai criar a primeira fábrica de dados do Azure com um pipeline de dados que **processa dados** através da execução do script Hive num cluster do Azure HDInsight (Hadoop). |
[Criar um pipeline de dados para mover dados entre dois arquivos de dados em nuvem](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) | Neste tutorial, vai criar uma fábrica de dados com um pipeline que **move dados** do armazenamento de Blobs para a base de dados SQL.
[Criar um pipeline de dados para mover dados entre um arquivo de dados no local e um arquivo de dados em nuvem utilizando o Data Management Gateway](data-factory-move-data-between-onprem-and-cloud.md) | Neste tutorial, vai criar uma fábrica de dados com um pipeline que **move dados** de uma base de dados do SQL Server **no local** para um blob do Azure. Como parte das instruções, vai instalar e configurar o Data Management Gateway no seu computador. 


<!--HONumber=Sep16_HO4-->



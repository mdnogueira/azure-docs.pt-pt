<properties 
    pageTitle="O que é o Data Factory? Serviço de integração de dados | Microsoft Azure" 
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
    ms.date="07/12/2016" 
    ms.author="spelluru"/>

# Introdução ao Azure Data Factory Service, um serviço de integração de dados em nuvem

## O que é o Azure Data Factory? 
Data Factory é um serviço de integração de dados baseado na nuvem que orquestra e automatiza o movimento e a transformação de dados. Tal como uma fábrica de produção que opera equipamento para processar matéria-prima e transformá-la em bens acabados, o Data Factory orquestra os serviços existentes que recolhem dados não processados e os transforma em informações prontas a utilizar. 

O Data Factory funciona em fontes de dados e SaaS na nuvem e no local para inserir, preparar, transformar, analisar e publicar os seus dados.  Utilize o Data Factory para compor serviços nos pipelines geridos de fluxo de dados para transformar os seus dados com serviços, como [HDInsight (Hadoop) da Azure](http://azure.microsoft.com/documentation/services/hdinsight/) e [Azure Batch](https://azure.microsoft.com/documentation/services/batch/), para as suas necessidades de computação de macrodados e com o [Azure Machine Learning](https://azure.microsoft.com/documentation/services/machine-learning/) para operacionalizar as soluções de análise.  Vá além apenas de uma vista de monitorização de tabela e utilize as visualizações otimizadas do Data Factory para visualizar rapidamente a linhagem e as dependências entre os pipelines de dados. Monitorize todos os pipelines de fluxo de dados a partir de uma vista unificada única para identificar facilmente os problemas e alertas de monitorização de configuração.

![Diagrama: descrição geral do Data Factory Overview, um serviço de integração de dados](./media/data-factory-introduction/what-is-azure-data-factory.png)

**Figura 1.** Recolha dados de várias origens de dados diferentes no local, insira e prepare-os, organize e analise-os com um intervalo de transformações e, em seguida, publicar dados prontos a utilizar para consumo.

Pode utilizar o Data Factory sempre que precisar para recolher dados de diferentes formas e tamanhos, transformar e publicá-los para extrair conhecimentos aprofundados – tudo numa agenda fiável. O Data Factory serve para criar pipelines de fluxo de dados altamente disponíveis para muitos cenários em diferentes indústrias para as suas necessidades de pipeline de análise.  Os retalhistas online utilizam-no para gerar [recomendações de produto](data-factory-product-reco-usecase.md) personalizadas com base no comportamento de navegação do cliente. Os estúdios de jogos utilizam-no para compreender a [eficácia das suas campanhas de marketing](data-factory-customer-profiling-usecase.md). Saiba diretamente a partir dos nossos clientes como e porque utilizam o Data Factory revendo [Casos Práticos de Clientes](data-factory-customer-case-studies.md). 

> [AZURE.VIDEO azure-data-factory-overview]

## Conceitos-chave

O Azure Data Factory tem algumas entidades-chave que funcionam em conjunto para definir a entrada e saída de dados, eventos de processamento e a agenda e os recursos necessários para executar o fluxo de dados pretendido.

![Diagrama: Data Factory, um serviço de integração de dados em nuvem – Conceitos Chave](./media/data-factory-introduction/data-integration-service-key-concepts.png)

**Figura 2.** Relações entre o Conjunto de Dados, Atividade, Pipeline e Serviço Ligado


### Atividades
As Atividades definem as ações a efetuar nos seus dados. Cada atividade aceita zero ou mais [conjuntos de dados](data-factory-create-datasets.md) como entradas e produz um ou mais conjuntos de dados como saídas. Uma atividade é uma unidade da orquestração no Azure Data Factory. Por exemplo, pode utilizar [Copiar atividade](data-factory-data-movement-activities.md) para orquestrar a cópia de dados de um conjunto de dados para outro. Da mesma forma, pode utilizar uma [Atividade do Hive](data-factory-data-transformation-activities.md) que executará uma consulta do Hive num cluster do Azure HDInsight para transformar ou analisar os seus dados. O Azure Data Factory fornece uma vasta gama de atividades de transformação, análise e movimentos de dados. 

### Pipelines
[Pipelines](data-factory-create-pipelines.md) são agrupamentos lógicos de Atividades. São utilizados para agrupar atividades numa unidade que, em conjunto, executa uma tarefa. Por exemplo, pode ser necessária uma sequência de várias Atividades de transformação para limpar os dados do ficheiro de registo.  Esta sequência pode ter uma agenda complexa e dependências que precisam de ser orquestradas e automatizadas. Todas estas atividades podem ser agrupadas para um único Pipeline com o nome “CleanLogFiles”.  Os “CleanLogFiles” podem, então, ser implementados, agendados ou eliminados como uma única unidade, ao invés de gerir cada atividade individual de forma independente.

### Conjuntos de dados
[Conjuntos de dados](data-factory-create-datasets.md) são referências/ponteiros com nomes para os dados que pretende utilizar como entrada ou saída de uma Atividade. Os conjuntos de dados identificam estruturas de dados dentro de diferentes arquivos de dados, incluindo tabelas, ficheiros, pastas e documentos.

### Serviço ligado
Os serviços ligados definem as informações necessárias para que o Data Factory estabeleça uma ligação a recursos externos.  Os serviços ligados são utilizados para duas finalidades no Data Factory:

- Para representar um arquivo de dados, incluindo, mas não limitado a, um Servidor SQL no local, Oracle DB, Partilha de ficheiros ou uma conta do Blob Storage do Azure.   Conforme mencionado acima, os Conjuntos de dados representam as estruturas dentro dos arquivos de dados ligados ao Data Factory através de um Serviço ligado.
- Para representar um recurso de computação que pode alojar a execução de uma Atividade.  Por exemplo, a “Atividade HDInsightHive” é executada num cluster HDInsight Hadoop.

Com os quatro conceitos simples dos conjuntos de dados, atividades, pipelines e serviços ligados, está pronto para começar!  Pode [construir o seu primeiro pipeline](data-factory-build-your-first-pipeline.md) do zero ou implementar uma amostra pronta a utilizar seguindo as instruções no nosso artigo [Amostras do Data Factory](data-factory-samples.md). 

## Regiões suportadas
Neste momento, pode criar fábricas de dados nas regiões **EUA Oeste**, **EUA Leste** e **Europa do Norte**. No entanto, uma fábrica de dados pode aceder aos arquivos de dados e serviços de computação noutras regiões do Azure para mover dados entre os arquivos de dados ou processar dados com serviços de computação. 

O Azure Data Factory propriamente dito não armazena quaisquer dados. Este permite-lhe criar fluxos de trabalho para orquestrar o movimento dos dados entre [arquivos de dados suportados](data-factory-data-movement-activities.md#supported-data-stores) e o processamento de dados com [serviços de computação](data-factory-compute-linked-services.md) noutras regiões ou num ambiente no local. Também permite [monitorizar e gerir fluxos de trabalho](data-factory-monitor-manage-pipelines.md) com mecanismos programáticos e de IU. 

Tenha em atenção que, apesar do Azure Data Factory estar disponível apenas nas regiões **EUA Oeste**, **EUA Leste** e **Europa do Norte**, o serviço que alimenta o movimento de dados no Data Factory está disponível [globalmente](data-factory-data-movement-activities.md#global) em várias regiões. Caso um arquivo de dados se encontre protegido por uma firewall, então, um [Data Management Gateway](data-factory-move-data-between-onprem-and-cloud.md) instalado no seu ambiente no local move os dados. 

Por exemplo, vamos assumir que os ambientes de computação, tais como o cluster Azure HDInsight e o Azure Machine Learning, estão a ficar sem a região Europa Ocidental. Pode criar e tirar partido de uma instância do Azure Data Factory na Europa do Norte e utilizá-la para agendar tarefas nos seus ambientes de computação na Europa Ocidental. Demora alguns milissegundos para o serviço do Data Factory acionar a tarefa no seu ambiente de computação, mas não se altera o tempo de execução da tarefa no seu ambiente informático.

Pretendemos ter o Azure Data Factory em cada geografia suportada pelo Azure no futuro.
  
## Passos seguintes
Siga as instruções passo a passo nos tutoriais seguintes para saber como criar fábricas de dados com pipelines de dados. 

Tutorial | Descrição
-------- | -----------
[Criar um pipeline de dados que processa os dados que utilizam o cluster do Hadoop](data-factory-build-your-first-pipeline.md) | Neste tutorial, irá criar a primeira fábrica de dados do Azure com um pipeline de dados que **processa dados** executando o script Hive num cluster do Azure HDInsight (Hadoop). |
[Criar um pipeline de dados para mover dados entre dois arquivos de dados em nuvem](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) | Neste tutorial, criará uma fábrica de dados com um pipeline que **move dados** do armazenamento de Blobs para base de dados SQL.
[Criar um pipeline de dados para mover dados entre um arquivo de dados no local e um arquivo de dados em nuvem utilizando o Data Management Gateway](data-factory-move-data-between-onprem-and-cloud.md) |  Neste tutorial, criaár uma fábrica de dados com um pipeline que **move dados** a partir de uma base de dados do SQL Server **no local** para um blob do Azure. Como parte de instruções, irá instalar e configurar o Data Management Gateway no computador. 


<!--HONumber=Aug16_HO1-->



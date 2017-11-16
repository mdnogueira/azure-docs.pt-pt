---
title: "O que é o Azure Databricks? | Microsoft Docs"
description: "Saiba mais sobre o que é o Azure Databricks e como implica Spark no Databricks no Azure. Databricks do Azure é uma plataforma de análise baseada no Apache Spark otimizada para a plataforma de serviços de nuvem do Microsoft Azure."
services: azure-databricks
documentationcenter: 
author: nitinme
manager: cgronlun
editor: cgronlun
ms.service: azure-databricks
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/15/2017
ms.author: nitinme
ms.openlocfilehash: 7ced38cda2669cf03e51f50fbbbeea0344da9277
ms.sourcegitcommit: afc78e4fdef08e4ef75e3456fdfe3709d3c3680b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/16/2017
---
# <a name="what-is-azure-databricks"></a>O que é o Azure Databricks?

Databricks do Azure é uma plataforma de análise baseada no Apache Spark otimizada para a plataforma de serviços de nuvem do Microsoft Azure. Databricks concebidas com founders do Apache Spark, está integrado com o Azure para fornecer a configuração de um clique, fluxos de trabalho simplificados e uma área de trabalho interativa que permite a colaboração entre cientistas de dados, os engenheiros de dados e os analistas de negócios.

![O que é o Azure Databricks? ] (./media/what-is-azure-databricks/azure-databricks-overview.png "Novidades Databricks do Azure?")

## <a name="apache-spark-based-analytics-platform"></a>Plataforma do Apache Spark com base na análise

Azure Databricks compreende as capacidades e concluídas tecnologias de cluster do Apache Spark de open source. O Spark no Azure Databricks inclui os seguintes componentes:

![O Apache Spark no Azure Databricks](./media/what-is-azure-databricks/apache-spark-ecosystem-databricks.png "Apache Spark no Azure Databricks")

* **O Spark SQL e DataFrames**: Spark SQL é o módulo de Spark para trabalhar com dados estruturados. Um DataFrame é uma coleção distribuída dos dados organizados em colunas com nome. É, essencialmente, equivalente a uma tabela da base de dados relacional ou um intervalo de dados no R/Python.

* **Transmissão em fluxo**: análise para aplicações analíticas e interativas e processamento de dados em tempo real. Integra-se com HDFS, Flume e Kafka.

* **MLib**: biblioteca de Machine Learning constituídas por comuns e utilitários de algoritmos de aprendizagem, incluindo classificação, regressão, clustering, filtragem de colaboração, redução dimensionalidade, bem como subjacente primitivos de otimização.

* **GraphX**: gráficos e de cálculo de gráfico para um âmbito abrangente de utilizar cenários de análise cognitivos exploração de dados.

* **O Spark Core API**: inclui suporte para R, SQL Server, Python, Scala e Java.

## <a name="apache-spark-in-azure-databricks"></a>O Apache Spark no Azure Databricks

Azure Databricks baseia-se nas capacidades do Spark, fornecendo uma plataforma de nuvem de gestão de zero que inclui:

- Clusters do Spark completamente geridos
- Uma área de trabalho interativa de exploração e visualização
- Uma plataforma para a ligar a aplicações baseada no Spark favoritas

### <a name="fully-managed-apache-spark-clusters-in-the-cloud"></a>Clusters do Apache Spark completamente geridos na nuvem

Databricks do Azure tem um ambiente de produção seguras e fiáveis na nuvem, geridos e suportado pela especialistas de Spark. Pode:

* Crie clusters em segundos.
* Dinamicamente o dimensionamento automático e reduzir verticalmente, clusters, incluindo clusters sem servidor e partilhá-los entre equipas. 
* Utilize clusters através de programação utilizando as APIs REST. 
* Utilize as capacidades de integração de dados seguros baseadas no Spark que lhe permite uniformizar os dados sem centralização. 
* Obter acesso instantânea para as funcionalidades mais recentes do Apache Spark com cada versão.

### <a name="databricks-runtime"></a>Tempo de execução Databricks
O tempo de execução Databricks baseia-se do Apache Spark e nativamente foi concebido para a nuvem do Azure. 

Com o **Serverless** opção, Azure Databricks deduz totalmente a complexidade de infraestrutura e a necessidade de conhecimentos especializados configurar e configurar a sua infraestrutura de dados. A opção sem servidor ajuda dados cientistas de iterar rapidamente como uma equipa.

Para engenheiros de dados, se preocupar com o desempenho das tarefas de produção, o Azure Databricks fornece um Spark motor que é mais rápido e performant através de vários otimizações na camada de e/s e processamento camada (Databricks e/s).

### <a name="workspace-for-collaboration"></a>Área de trabalho para colaboração

Através de um ambiente de colaboração e integrado, Azure Databricks simplifica o processo de explorar os dados, fazer o protótipo e aplicações em execução condicionada por dados no Spark.

* Determine como utilizar os dados com exploração de dados fácil.
* Documente o seu progresso em blocos de notas no R, Python, Scala ou SQL Server.
* Visualizar dados em apenas alguns cliques e utilizar ferramentas familiares como Matplotlib, ggoplot ou d3.
* Utilize dashboards interativos para criar relatórios dinâmicos.
* Utilizar o Spark e interagir com os dados em simultâneo.

## <a name="enterprise-security"></a>Segurança da empresa

Databricks do Azure fornece segurança do Azure, incluindo a integração do Active Directory do Azure, controlos baseada em funções e SLAs que protegem os dados e a sua empresa de nível empresarial.

* Integração com o Azure Active Directory permite-lhe executar soluções completas baseadas no Azure utilizando o Azure Databricks.
* Acesso baseado em funções no Azure Databricks permite permissões de utilizador de detalhado para blocos de notas, clusters, tarefas e dados.
* SLA de nível empresarial. 

## <a name="integration-with-azure-services"></a>Integração com os serviços do Azure

Databricks do Azure integra-se profundamente com bases de dados do Azure e armazena: SQL Data Warehouse, Cosmos DB, Data Lake Store e armazenamento de Blobs. 

## <a name="integration-with-power-bi"></a>Integração com o Power BI
Através da integração otimizada com o Power BI, o Azure Databricks permite-lhe detetar e partilhar as suas informações impactful rápida e facilmente. Pode utilizar outras ferramentas de BI, bem como o Tableau Software através de pontos finais de cluster JDBC/ODBC.

## <a name="next-steps"></a>Passos seguintes

* [Início rápido: Executar uma tarefa do Spark no Azure Databricks](quickstart-create-databricks-workspace-portal.md)
* [Trabalhar com clusters do Spark](https://docs.azuredatabricks.net/user-guide/clusters/index.html)
* [Trabalhar com blocos de notas](https://docs.azuredatabricks.net/user-guide/notebooks/index.html)
* [Criar Spark tarefas](https://docs.azuredatabricks.net/user-guide/jobs.html)

 










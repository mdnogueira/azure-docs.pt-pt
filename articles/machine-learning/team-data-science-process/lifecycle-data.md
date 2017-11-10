---
title: "Aquisição de dados e a fase de compreender o ciclo de vida do processo de ciência de dados de equipa - Azure | Microsoft Docs"
description: "Os objetivos, tarefas e materiais a entregar para a aquisição de dados e a fase de compreensão dos seus projetos de ciência de dados"
services: machine-learning
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: 
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/04/2017
ms.author: bradsev;
ms.openlocfilehash: 9618653e8f77c69a3a95fe27ee55c4f05c55a66e
ms.sourcegitcommit: 93902ffcb7c8550dcb65a2a5e711919bd1d09df9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/09/2017
---
# <a name="data-acquisition-and-understanding"></a>Aquisição e compreensão de dados

Este artigo descreve os objetivos, tarefas e materiais a entregar associada à aquisição de dados e a fase de compreensão de equipa dados ciência processo (TDSP). Este processo fornece um ciclo de vida recomendado que pode utilizar para estruturar seus projetos de ciência de dados. O ciclo de vida destaca as fases principais que projetos normalmente executar, muitas vezes, iteratively:

   1. **Compreensão de negócio**
   2. **Aquisição de dados e a compreensão**
   3. **Modelação**
   4. **Implementação**
   5. **Aceitação de cliente**

Eis uma representação visual do ciclo de vida TDSP: 

![Ciclo de vida TDSP](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goals"></a>Objetivos
* Produz um conjunto de dados limpo, de alta qualidade cuja relação para as variáveis de destino é compreendida. Localize o conjunto de dados no ambiente de análise adequado para está pronto para o modelo.
* Desenvolva uma arquitetura de solução do pipeline de dados que é atualizada e pontuações regularmente os dados.

## <a name="how-to-do-it"></a>Como fazê-lo
Existem três tarefas principais resolvidas nesta fase:

   * **Os dados de inserção** para o ambiente de análise de destino.
   * **Explorar os dados** para determinar se a qualidade de dados é suficiente responder à pergunta. 
   * **Configurar um pipeline de dados** Pontuar novos ou regularmente atualizada dados.

### <a name="ingest-the-data"></a>Ingestão de dados
Defina o processo de mover os dados a partir de localizações de origem para as localizações de destino em que executa operações de análise, tal como a formação e predições. Para detalhes técnicos e as opções sobre como mover os dados com vários serviços de dados do Azure, consulte [carregar dados para ambientes de armazenamento para análise](ingest-data.md). 

### <a name="explore-the-data"></a>Explorar os dados
Antes de preparar seus modelos, tem de desenvolver uma compreensão som dos dados. Conjuntos de dados do mundo real, muitas vezes, são inúteis, estão em falta valores ou tem um anfitrião de outros discrepâncias. Pode utilizar o resumo de dados e de visualização para a qualidade dos seus dados de auditoria e forneça as informações necessárias processar os dados antes de ser preparado para a modelação. Este processo é frequentemente iterativo.

TDSP fornece um utilitário automatizado, denominado [IDEAR](https://github.com/Azure/Azure-TDSP-Utilities/blob/master/DataScienceUtilities/DataReport-Utils), para ajudar a visualizar os dados e preparar os relatórios de resumo de dados. Recomendamos que inicie com IDEAR primeiro para explorar os dados para o ajudar a desenvolver compreensão inicial de dados interativamente sem codificação. Em seguida, pode escrever código personalizado para e a visualização de exploração de dados. Para obter orientações sobre a limpeza de dados, consulte [tarefas para preparar os dados para melhorado machine learning](prepare-data.md).  

Depois de estiver satisfeito com a qualidade dos dados cleansed, o passo seguinte é compreender melhor os padrões que são inerentes nos dados. Isto ajuda a escolha e desenvolver um modelo preditivo adequado para o destino. Procure para provas como boa ligação de dados é o destino. Em seguida, determine se existe dados suficientes para avançar com os passos de modelação. Novamente, este processo é frequentemente iterativo. Poderá ter de encontrar novas origens de dados com dados mais exatas ou mais relevantes para aumentar o conjunto de dados inicialmente identificado na fase anterior. 

### <a name="set-up-a-data-pipeline"></a>Configurar um pipeline de dados
Para além de ingestão inicial e a limpeza dos dados, normalmente, tem de definir um processo para pontuar novos dados ou atualizar os dados regularmente como parte de um processo de aprendizagem em curso. Pode fazê-lo ao definir um pipeline de dados ou o fluxo de trabalho. O [mover dados de uma instância do SQL Server no local para a base de dados do Azure SQL com o Azure Data Factory](move-sql-azure-adf.md) artigo fornece um exemplo de como configurar um pipeline com [do Azure Data Factory](https://azure.microsoft.com/services/data-factory/). 

Nesta fase, pode desenvolve uma arquitetura de solução do pipeline de dados. Desenvolver o pipeline em paralelo com a próxima fase do projeto de ciência de dados. Consoante as necessidades de negócio e as restrições dos sistemas de existentes para o qual está a ser integrada esta solução, o pipeline pode ser um dos seguintes: 

   * Com base em batch
   * Transmissão em fluxo ou em tempo real 
   * Uma versão híbrida 

## <a name="artifacts"></a>Artefactos
Seguem-se a concentrarem nesta fase:

   * [Relatório de qualidade de dados](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/DataReport/DataSummaryReport.md): este relatório inclui resumos de dados, as relações entre cada atributo e de destino, classificação variável e muito mais. O [IDEAR](https://github.com/Azure/Azure-TDSP-Utilities/blob/master/DataScienceUtilities/DataReport-Utils) ferramenta fornecida como parte da TDSP rapidamente pode gerar este relatório em qualquer conjunto de dados em tabela, como um ficheiro CSV ou uma tabela relacional. 
   * **Arquitetura de solução**: arquitetura da solução pode ser um diagrama ou descrição dos seus dados de pipeline que utilizam para executar a classificação ou predições em novos dados depois de ter criado um modelo. Também contém o pipeline de reparametrização do seu modelo com base em novos dados. Armazene documentos no [projeto](https://github.com/Azure/Azure-TDSP-ProjectTemplate/tree/master/Docs/Project) diretório quando utilizar o modelo de estrutura de diretório TDSP.
   * **Decisão de ponto de verificação**: antes de começar a funcionalidade completa de engenharia e criação de modelo, pode reevaluate o projeto para determinar se o valor esperado é suficiente para continuar a pursing-lo. Pode, por exemplo, estar pronto para continuar, tem de recolher mais dados ou abandonar o projeto como os dados não existe responder à pergunta.

## <a name="next-steps"></a>Passos seguintes

Seguem-se ligações para cada passo no ciclo de vida do TDSP:

   1. [Compreensão de negócio](lifecycle-business-understanding.md)
   2. [Aquisição de dados e a compreensão](lifecycle-data.md)
   3. [Modelação](lifecycle-modeling.md)
   4. [Implementação](lifecycle-deployment.md)
   5. [Aceitação de cliente](lifecycle-acceptance.md)

Fornecemos instruções completa ponto-a-ponto que demonstram todos os passos no processo de cenários específicos. O [instruções de exemplo](walkthroughs.md) artigo fornece uma lista dos cenários com ligações e descrições em miniatura. As instruções mostram como combinar em nuvem, ferramentas no local e serviços para um fluxo de trabalho ou pipeline para criar uma aplicação inteligente. 

Para obter exemplos de como executar os passos no TDSPs que utilizam o Azure Machine Learning Studio, consulte [utilizar o TDSP com o Azure Machine Learning](http://aka.ms/datascienceprocess).

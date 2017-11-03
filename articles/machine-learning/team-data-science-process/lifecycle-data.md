---
title: "Aquisição de dados e a fase de compreensão de equipa dados ciência processo ciclo de vida - Azure | Microsoft Docs"
description: "Os objetivos, tarefas e materiais a entregar para a aquisição de dados e a fase de compreensão dos seus projetos de ciência de dados."
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
ms.date: 09/02/2017
ms.author: bradsev;
ms.openlocfilehash: eea3c357ebf6ad920c0ddebdb979aa07aece4794
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="data-acquisition-and-understanding"></a>Aquisição e compreensão de dados

Este tópico descreve os objetivos, tarefas, e associados à materiais a entregar o **aquisição de dados e a fase de compreender** do processo de ciência de dados de equipa. Este processo fornece um ciclo de vida recomendado que pode utilizar para estruturar seus projetos de ciência de dados. O ciclo de vida destaca as fases principais que projetos normalmente executar, muitas vezes, iteratively:

* **Compreensão de negócio**
* **Aquisição de dados e a compreensão**
* **Modelação**
* **Implementação**
* **Aceitação de cliente**

Eis uma representação visual do **ciclo de vida do processo de ciência de dados de equipa**. 

![TDSP Lifecycle2](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goals"></a>Objetivos
* Limpo, de alta qualidade conjunto de dados cujas relações para as variáveis de destino estão compreendidas que estão localizados no ambiente de análise adequado, pronto para o modelo.
* Foi desenvolveu uma arquitetura de solução do pipeline de dados para atualizar e Pontuar dados regularmente.

## <a name="how-to-do-it"></a>Como fazê-lo
Existem três tarefas principais resolvidas nesta fase:

* **Os dados de inserção** para o ambiente de análise de destino.
* **Explorar os dados** para determinar se a qualidade de dados é suficiente responder à pergunta. 
* **Configurar um pipeline de dados** Pontuar novos ou regularmente atualizada dados.

### <a name="21-ingest-the-data"></a>2.1 incorporar os dados
Configure o processo de mover os dados a partir de localizações de origem para as localizações de destino onde operações de análise que formação e predições estão a ser executado. Para detalhes técnicos e as opções sobre como fazê-lo com vários serviços de dados do Azure, consulte [carregar dados para ambientes de armazenamento para análise](ingest-data.md). 

### <a name="22-explore-the-data"></a>2.2 explorar os dados
Antes de preparar seus modelos, tem de desenvolver uma compreensão som dos dados. Conjuntos de dados do mundo real, muitas vezes, são inúteis ou estão em falta valores ou tem um anfitrião de outros discrepâncias. Resumo de dados e visualização podem ser utilizados para a qualidade dos seus dados de auditoria e forneça as informações necessárias para processar os dados antes de ser preparado para a modelação. Este processo é frequentemente iterativo.

TDSP fornece um utilitário automatizado chamado [IDEAR](https://github.com/Azure/Azure-TDSP-Utilities/blob/master/DataScienceUtilities/DataReport-Utils) para ajudar a visualizar os dados e preparar os relatórios de resumo de dados. Recomendamos que comece com IDEAR primeiro para explorar os dados para ajudar a desenvolver compreensão inicial de dados interativamente sem codificação e, em seguida, escrever código personalizado para e a visualização de exploração de dados. Para obter orientações sobre a limpeza de dados, consulte [tarefas para preparar os dados para melhorado machine learning](prepare-data.md).  

Quando estiver satisfeito com a qualidade dos dados cleansed, o passo seguinte é compreender melhor os padrões que são inerentes nos dados que o ajudam a escolherem e desenvolver um modelo preditivo adequado para o destino. Procure para provas para quão bem ligado os dados é para o destino e se contém dados suficientes para avançar com os passos de modelação. Novamente, este processo é frequentemente iterativo. Poderá ter de encontrar novas origens de dados com dados mais exatas ou mais relevantes para aumentar o conjunto de dados inicialmente identificado na fase anterior.  

### <a name="23-set-up-a-data-pipeline"></a>2.3 configurar um pipeline de dados
Para além de ingestão inicial e a limpeza dos dados, normalmente, tem de definir um processo para pontuar novos dados ou atualizar os dados regularmente como parte de um processo de aprendizagem em curso. Isto pode ser feito através da definição de um pipeline de dados ou o fluxo de trabalho. Eis um [exemplo](move-sql-azure-adf.md) de como configurar um pipeline com [do Azure Data Factory](https://azure.microsoft.com/services/data-factory/). 

Uma arquitetura de solução do pipeline de dados for desenvolvida nesta fase. O pipeline também for desenvolvido em paralelo com as seguintes fases do projeto de ciência de dados. O pipeline pode ser baseado no lote ou transmissão em fluxo/real-time ou uma híbrida consoante as necessidades de negócio e as restrições dos sistemas de existentes para o qual está a ser integrada esta solução. 

## <a name="artifacts"></a>Artefactos
Seguem-se a concentrarem nesta fase.

* [**Relatório de qualidade de dados**](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/DataReport/DataSummaryReport.md): este relatório contém resumos de dados, as relações entre cada atributo e de destino, variável classificação etc. O [IDEAR](https://github.com/Azure/Azure-TDSP-Utilities/blob/master/DataScienceUtilities/DataReport-Utils) ferramenta fornecida como parte da TDSP rapidamente pode gerar este relatório qualquer conjunto de dados em tabela como um ficheiro CSV ou uma tabela relacional. 
* **Arquitetura de solução**: Isto pode ser um diagrama ou a descrição do pipeline de dados utilizada para executar a classificação ou predições em novos dados uma vez que tiver criado um modelo. Também contém o pipeline de reparametrização do seu modelo com base em novos dados. O documento é armazenado no [projeto](https://github.com/Azure/Azure-TDSP-ProjectTemplate/tree/master/Docs/Project) diretório ao utilizar o modelo de estrutura de diretório TDSP.
* **Decisão de ponto de verificação**: antes de começar a engenharia da funcionalidade completa e criação de modelo, pode reevaluate o projeto para determinar se o valor esperado é suficiente para continuar a pursing-lo. Por exemplo, poderá, estar pronto para continuar, tem de recolher mais dados ou abandonar o projeto como os dados não existe responder à pergunta.

## <a name="next-steps"></a>Passos seguintes

Seguem-se ligações para cada passo no ciclo de vida do processo de ciência de dados de equipa:

* [1. Compreensão de negócio](lifecycle-business-understanding.md)
* [2. Aquisição de dados e a compreensão](lifecycle-data.md)
* [3. Modelação](lifecycle-modeling.md)
* [4. Implementação](lifecycle-deployment.md)
* [5. Aceitação de cliente](lifecycle-acceptance.md)

Total de instruções ponto-a-ponto que demonstram todos os passos no processo de **cenários específicos** também são fornecidos. São listados e ligados com descrições de miniaturas no [instruções de exemplo](walkthroughs.md) tópico. Estes mostram como combinar em nuvem, ferramentas no local e serviços para um fluxo de trabalho ou pipeline para criar uma aplicação inteligente.  

Para obter exemplos executar os passos no processo de ciência de dados de equipa que utilizam o Azure Machine Learning Studio, consulte o [com o Azure ML](http://aka.ms/datascienceprocess) percurso de aprendizagem.
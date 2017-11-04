---
title: "O que é o processo de ciência de dados de equipa? | Microsoft Docs"
description: "Fornece uma metodologia de ciência de dados para fornecer soluções de Análise Preditiva e aplicações inteligentes."
services: machine-learning
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: b1f677bb-eef5-4acb-9b3b-8a5819fb0e78
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/20/2017
ms.author: bradsev;
ms.openlocfilehash: 75e170f29f9a6abeeb3393e43e6a4c355749044d
ms.sourcegitcommit: cf4c0ad6a628dfcbf5b841896ab3c78b97d4eafd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/21/2017
---
# <a name="what-is-the-team-data-science-process"></a>O que é o processo de ciência de dados de equipa?

O processo de ciência de dados de equipa (TDSP) é uma metodologia de ciência de dados seja ágil e interativo para fornecer soluções de Análise Preditiva e aplicações inteligentes de forma eficiente. TDSP ajuda a melhorar a colaboração de equipa e de aprendizagem. Contém um distillation das melhores práticas e estruturas da Microsoft e outras pessoas na indústria que facilitam a implementação com êxito de iniciativas de ciência de dados. O objetivo é ajudar as empresas totalmente tenha em consideração as vantagens do seu programa de análise.

Este artigo fornece uma descrição geral da TDSP seus principais componentes. Podemos fornecer uma descrição genérica do processo aqui que pode ser implementada com uma variedade de ferramentas. Uma descrição mais detalhada das tarefas de projeto e funções envolvidos no ciclo de vida do processo é fornecida no adicionais Tópicos relacionados. Também é fornecida orientação sobre como implementar o TDSP utilizando um conjunto específico de ferramentas da Microsoft e a infraestrutura que utilizamos para implementar o TDSP no nosso equipas.

## <a name="key-components-of-the-tdsp"></a>Componentes principais do TDSP

Compreende TDSP dos seguintes componentes principais:

- A **ciclo de vida de ciência de dados** definição
- A **padronizado estrutura do projeto**
- **Infraestrutura e de recursos** para projetos de ciência de dados
- **Ferramentas e utilitários** para execução do projeto


## <a name="data-science-lifecycle"></a>Ciclo de vida de ciência de dados

O processo de ciência de dados de equipa (TDSP) fornece um ciclo de vida para estruturar o desenvolvimento dos seus projetos de ciência de dados. O ciclo de vida descreve os passos, do início ao fim, que projetos siga, normalmente, quando estes são executados.

Se estiver a utilizar o ciclo de vida das ciências de dados outro, tal como [CRISP-DM](https://wikipedia.org/wiki/Cross_Industry_Standard_Process_for_Data_Mining), [KDD](https://wikipedia.org/wiki/Data_mining#Process) ou o processo personalizado da organização, pode continuar a utilizar o TDSP baseado em tarefas no contexto desses ciclos de vida de desenvolvimento. Um nível elevado, estes metodologias diferentes têm muito em comum. 

Este ciclo de vida tem foi concebido para projetos de ciência de dados que são enviados como parte das aplicações inteligentes. Estas aplicações implementar machine learning ou artificial intelligence modelos para Análise Preditiva. Projetos de ciência de dados exploratórias ou projetos de análise ad hoc também podem beneficiar da utilização deste processo. Mas nestes casos alguns dos passos descritos podem não ser necessária.    

O ciclo de vida destaca as fases principais que projetos normalmente executar, muitas vezes, iteratively:

* **Compreensão de negócio**
* **Aquisição de dados e a compreensão**
* **Modelação**
* **Implementação**
* **Aceitação de cliente**

Eis uma representação visual do **ciclo de vida do processo de ciência de dados de equipa**. 

![TDSP Lifecycle2](./media/overview/tdsp-lifecycle2.png) 

Os objetivos, tarefas e artefactos de documentação para cada fase do ciclo de vida da TDSP descritos o [ciclo de vida do processo de ciência de dados de equipa](lifecycle.md) tópico. Estas tarefas e os artefactos estão associados com funções do projeto:

- Arquitetos de soluções
- Gestor de projeto
- Cientista de dados
- Líder de projeto 

O diagrama seguinte fornece uma vista de grelha de tarefas (azul) e artefactos (em verde) associados em cada fase do ciclo de vida (no eixo horizontal) para estas funções (no eixo vertical). 

![TDSP-funções-e-tarefas](./media/overview/tdsp-tasks-by-roles.png)

## <a name="standardized-project-structure"></a>Estrutura do projeto normalizado

Ter todos os projetos de partilhar uma estrutura de diretórios e utilizar modelos para documentos do projeto torna mais fácil para os membros da equipa encontrar informações sobre os seus projetos. Todos os código e os documentos são armazenados num sistema de controlo de versão (VCS) como o Git, do TFS ou Subversion para permitir a colaboração de equipa. Controlar as tarefas e funcionalidades num projeto seja ágil sistema como Jira de controlo, Rally, Visual Studio Team Services permite próximo de controlo de código para funcionalidades individuais. Esse controlo também permite que as equipas obter melhor estimativas de custos. TDSP recomenda a criação de um repositório separado para cada projeto no VCS para controlo de versões, a segurança de informações e colaboração. A estrutura padronizada para todos os projetos de ajuda a criar dados de conhecimento institutional em toda a organização.

Podemos fornecer modelos de estrutura de pastas e documentos necessários em localizações padrão. Esta estrutura de pasta organiza os ficheiros que contêm código de exploração de dados e de extração de funcionalidade e que registe iterações de modelo. Estes modelos tornam mais fácil para os membros da equipa para compreender o trabalho feito por outras pessoas e adicionar novos membros para equipas. É fácil ver e atualizar modelos de documento no formato de markdown. Utilize modelos para fornecer as listas de verificação com questões-chave para cada projeto a assegurar que o problema é definido especificamente e que os materiais a entregar satisfazem a qualidade esperada. Os exemplos incluem:

- um charter projeto documentar o problema empresarial e o âmbito do projeto
- relatórios de dados para a estrutura do documento e as estatísticas dos dados não processados
- relatórios de modelo para as funcionalidades derivadas de documentos
- métricas de desempenho de modelo como curvas ROC ou MSE


![TDSP diretórios](./media/overview/tdsp-dir-structure.png)

A estrutura do diretório pode ser clonada a partir do [Github](https://github.com/Azure/Azure-TDSP-ProjectTemplate).

## <a name="infrastructure-and-resources-for-data-science-projects"></a>Infraestrutura e de recursos para projetos de ciência de dados  

TDSP fornece recomendações para gerir a análise partilhado e a infraestrutura de armazenamento, tais como:

- sistemas de ficheiros em nuvem para armazenar conjuntos de dados 
- bases de dados
- clusters de macrodados (Hadoop ou Spark) 
- serviços do Machine learning. 

A infraestrutura de armazenamento e de análise pode ser na nuvem ou no local. Este é o local onde os conjuntos de dados não processados e processados são armazenados. Esta infraestrutura permite reproduzível Analysis Services. Também evita a duplicação, que pode levar a inconsistências e custos de infraestrutura desnecessários. As ferramentas são fornecidas para aprovisionar os recursos partilhados, controlá-los e permitir que cada membro do agrupamento ligar em segurança a esses recursos. Também é uma boa prática ter membros de projeto, crie um ambiente de computação consistente. Os membros da equipa diferentes, em seguida, podem replicar e validar experimentações.

Eis um exemplo de uma equipa partilha vários componentes de infraestrutura de análise de nuvem e de trabalhar em vários projetos.

![Infraestrutura de TDSP](./media/overview/tdsp-analytics-infra.png)


## <a name="tools-and-utilities-for-project-execution"></a>Ferramentas e utilitários para execução do projeto

Introdução ao processos na maioria das organizações é um desafio. Ferramentas fornecidas para implementar a ajuda de processo e o ciclo de vida da ciência de dados inferiores as barreiras as eficazes para e aumentar a consistência da respetiva adoção. TDSP fornece um conjunto de ferramentas e scripts para iniciar a adoção de TDSP dentro de uma equipa inicial. Também ajuda a automatizar alguns das tarefas comuns no ciclo de vida de ciência de dados, tais como a exploração de dados e modelação de linha de base. Há uma estrutura bem definida fornecida para indivíduos contribuir partilhadas ferramentas e utilitários no repositório de código partilhado da sua equipa. Estes recursos, em seguida, podem ser aproveitados pelas outros projetos dentro da equipa ou organização. TDSP também planos permitir contribuições de ferramentas e utilitários para a Comunidade de todo. Os utilitários TDSP podem ser clonados a partir do [Github](https://github.com/Azure/Azure-TDSP-Utilities).


## <a name="next-steps"></a>Passos seguintes

[Processo de ciência de dados do agrupamento: Funções e tarefas](https://github.com/Azure/Microsoft-TDSP/blob/master/Docs/roles-tasks.md) descreve as funções de chave técnicos e as respetivas tarefas associadas para uma equipa de ciência de dados uniformiza neste processo. 

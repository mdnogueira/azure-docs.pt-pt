---
title: "Estrutura projetos com o modelo de processo de ciência de dados de equipa | Microsoft Docs"
description: "Como instanciar modelos de processo de ciência de dados de equipa (TDSP) no Azure Machine Learning estrutura projetos de colaboração"
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
ms.date: 10/16/2017
ms.author: bradsev
ms.openlocfilehash: efb681b85d3d7434e3114b8576abc64d00891f03
ms.sourcegitcommit: 9ae92168678610f97ed466206063ec658261b195
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/17/2017
---
# <a name="structure-projects-with-the-team-data-science-process-template"></a>Projetos de estrutura com o modelo de processo de ciência de dados de equipa

Este documento fornece instruções sobre como criar projetos de ciência de dados no Azure Machine Learning com modelos de processo de ciência de dados de equipa (TDSP). Estes modelos ajudam a projetos de estrutura de colaboração e reproducibility. 


## <a name="what-is-the-team-data-science-process"></a>O que é o processo de ciência de dados de equipa?
O TDSP é um processo de ciência de dados seja ágil e interativo, para executar e fornecer soluções de análise avançadas. Foi concebido para melhorar a colaboração e a eficiência das equipas de ciência de dados em organizações de empresa. Suporta estes objetivos com quatro componentes principais:

   * Um padrão [ciclo de vida de ciência de dados](../team-data-science-process/lifecycle.md) definição.
   * Uma estrutura de projeto padronizado [documentação e modelos de relatórios do projeto](https://github.com/Azure/Azure-TDSP-ProjectTemplate).
   * Uma infraestrutura e os recursos para a execução do projeto, por exemplo, respetivamente, um armazenamento e computação infraestrutura e o código repositórios.
   * [Ferramentas e utilitários](https://github.com/Azure/Azure-TDSP-Utilities) de ciência de dados do projeto tarefas, tais como:
      - Controlo de versão colaboração
      - Revisão de código
      - Exploração de dados e modelação de
      - Planeamento de trabalho

Para ver um debate mais completo do TDSP, consulte o [descrição geral do processo de ciência de dados de equipa](../team-data-science-process/overview.md).

## <a name="why-should-you-use-the-tdsp-structure-and-templates"></a>Por que motivo deve utilizar a estrutura TDSP e modelos?
Uniformização da estrutura, o ciclo de vida e a documentação de projetos de ciência de dados é importante para facilitar o início efetiva colaboração em equipas de ciência de dados. Crie projetos do machine learning com o modelo TDSP para fornecer uma arquitetura de tal teamwork coordenada.

Anteriormente, foi lançado um [repositório do GitHub para a estrutura de projeto TDSP e modelos](https://github.com/Azure/Azure-TDSP-ProjectTemplate) para ajudar a alcançar estes objetivos. Mas não foi possível, até agora, ao instanciar a estrutura TDSP e modelos dentro de uma ferramenta de ciência de dados. Agora é possível criar um projeto de aprendizagem que cria uma instância os modelos de estrutura e a documentação do TDSP. 

## <a name="things-to-note-before-creating-a-new-project"></a>Coisas a salientar antes de criar um novo projeto
Reveja os seguintes itens *antes* criar um novo projeto:
* Reveja o Machine Learning TDSP [modelo](https://aka.ms/tdspamlgithubrepo).
* O conteúdo (que não seja o que já se encontra presente na pasta "docs") têm de ter menos de 25 MB de tamanho. Consulte a nota que se segue nesta lista.
* O exemplo\_pasta de dados é apenas para ficheiros de dados pequenos (menos de 5 MB) com o qual pode testar o seu código ou iniciar o desenvolvimento antecipado.
* Armazenar, como o Word e PowerPoint ficheiros, pode aumentar o tamanho da pasta "docs" substancialmente. Aconselhamos que localizar um Wiki colaboração, [SharePoint](https://products.office.com/en-us/sharepoint/collaboration), ou outros recursos de colaboração para armazenar esses ficheiros.
* Para saber como processar ficheiros grandes e saídas no Machine Learning, leia [a persistência de alterações e processamento de ficheiros grandes](http://aka.ms/aml-largefiles).

> [!NOTE]
> Todos os relacionados com a documentação do conteúdo (texto, markdowns, imagens e outros ficheiros de documentos) que é *não* utilizado durante a execução do projeto, exceto ficheiros readme.md, tem de residir na pasta com o nome "docs" (todo em minúsculas). A pasta "docs" é uma pasta especial ignorada pela execução de Machine Learning, para que o conteúdo nesta pasta não obter copiado a computação destinos desnecessariamente. Objetos nesta pasta também não é considerado a extremidade de 25 MB para o tamanho de projeto. A pasta "docs", por exemplo, é o local para armazenar ficheiros de imagem grande necessários na sua documentação. Estes ficheiros ainda são registados pelo Git através do histórico de execução. 

## <a name="instantiate-the-tdsp-structure-and-templates-from-the-machine-learning-template-gallery"></a>Instanciar a estrutura TDSP e modelos na Galeria de modelo do Machine Learning
Para criar um novo projeto com os modelos de estrutura e a documentação do TDSP, conclua os procedimentos seguintes.

### <a name="create-a-new-project"></a>Criar um novo projeto
Para criar um novo projeto, abra o Azure Machine Learning. Em **projetos** no painel superior esquerdo, selecione o sinal de adição (**+**) e, em seguida, selecione **novo projeto**.

![Novo projeto](./media/how-to-use-tdsp-in-azure-ml/instantiation-1.png)


### <a name="create-a-new-tdsp-structured-project"></a>Criar um novo projeto TDSP-estruturados
   1. Especifique os parâmetros e as informações na caixa relevante ou lista:

      - Nome do projeto
      - Diretório do projeto
      - Descrição do projeto
      - Um caminho de repositório de Git vazio
      - Nome da área de trabalho

   2. Em seguida, no **pesquisa** box, introduza **TDSP**. 
   3. Quando o **estruturar um projeto com TDSP** opção seja apresentada, selecione de que o modelo. 
   4. Selecione o **criar** botão para criar o novo projeto com uma estrutura TDSP. Se fornecer um repositório de Git vazio quando cria o projeto (na caixa de texto adequado), em seguida, desse repositório será preenchido com a estrutura de projeto e conteúdo após a criação do projeto.

![Criar um projeto TDSP](./media/how-to-use-tdsp-in-azure-ml/instantiation-2.png)


## <a name="examine-the-tdsp-project-structure"></a>Examine a estrutura de projeto TDSP
Depois de ter sido criado o seu novo projeto, pode examinar a estrutura (consulte o painel esquerdo na figura seguinte). Contém todos os aspetos da documentação padronizado para compreender o negócio. Estes itens incluem as fases do ciclo de vida TDSP, a localização de dados, definições e a arquitetura deste modelo de documentação. 

A estrutura mostrada deriva a estrutura TDSP que é publicada no [modelos de estrutura, documentos e artefactos de projeto do TDSP](https://github.com/Azure/Azure-TDSP-ProjectTemplate), com algumas alterações. Por exemplo, vários modelos de documento são intercalados no um markdown, nomeadamente, [ProjectReport](https://aka.ms/tdspamlgithubrepoprojectreport). 

### <a name="project-folder-structure"></a>Estrutura de pasta do projeto
O modelo de projeto TDSP contém as seguintes pastas de nível superior:
   - **código**: contém o código.
   - **Docs**: contém necessária documentação sobre o projeto (por exemplo, ficheiros markdown e suportes de dados relacionados).
   - **sample_data**: contém **exemplo (breve)** dados que pode utilizar para antecipado programação ou testes. Normalmente, estes conjuntos não são maiores do que vários (5) MB. Esta pasta não está para completos ou grandes conjuntos de dados.

![Dados de exemplo](./media/how-to-use-tdsp-in-azure-ml/instantiation-3.png)


## <a name="use-the-tdsp-structure-and-templates"></a>Utilizar a estrutura TDSP e modelos
Tem de adicionar informações específicas de projeto para a estrutura e modelos. Está a esperada para preencher estes com o código e as informações necessárias para executar e fornecer o seu projeto. O [ProjectReport](https://aka.ms/tdspamlgithubrepoprojectreport) ficheiro é um modelo que necessitar de modificar com informações relevantes para o seu projeto. Inclui um conjunto de questões que o ajudam a preencher as informações para cada um dos quatro fases o [TDSP ciclo de vida](../team-data-science-process/lifecycle.md).

Um exemplo de uma estrutura de projeto aspeto durante a execução ou de conclusão é mostrada no painel esquerdo na figura seguinte. Este projeto é a partir de [projeto de exemplo do processo de ciência de dados do agrupamento: classificar incomes dos dados de census E.U.A. no Azure Machine Learning](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome) projeto de exemplo.

![Estrutura do projeto de exemplo](./media/how-to-use-tdsp-in-azure-ml/instantiation-4.png)

## <a name="document-your-project"></a>Documente o seu projeto
Consulte o [modelos de documentação TDSP](https://github.com/Azure/Azure-TDSP-ProjectTemplate) para obter informações sobre como o projeto de documentos. O modelo de documentação do Machine Learning TDSP atual, recomendamos que inclui todas as informações no [ProjectReport](https://aka.ms/tdspamlgithubrepoprojectreport) ficheiro. Este modelo deve ser preenchido com informações específicas ao projecto. 

Fornecemos também uma [ProjectLearnings](https://aka.ms/tdspamlgithubrepoprojectlearnings) modelo. Pode utilizar este modelo para incluir quaisquer informações que não está incluído no documento de projeto principal, mas que ainda útil para documentos. 

### <a name="example-project-report"></a>Relatório de projeto de exemplo
Pode obter um [relatório de projeto de exemplo](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome/blob/master/docs/deliverable_docs/ProjectReport.md). Este relatório de projeto para a [projeto de exemplo de classificação de receitas E.U.A.](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome) mostra como instanciar e utilizar o modelo TDSP para um projeto de ciência de dados.

## <a name="next-steps"></a>Passos seguintes
Para facilitar a compreensão sobre como utilizar a estrutura TDSP e modelos em projetos de Machine Learning, fornecemos vários exemplos de projeto concluída na documentação do Machine Learning:

- Para um exemplo que mostra como criar um projeto TDSP no Machine Learning, consulte [projeto de exemplo do processo de ciência de dados do agrupamento: classificar incomes dos dados nos Census no Azure Machine Learning](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome).
- Para um exemplo que utiliza a aprendizagem profunda numa linguagem natural processamento (NLP) num projeto com instâncias criadas TDSP no Machine Learning, consulte [Bio médicas reconhecimento de entidade com linguagem natural processamento com learning profunda](https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction).


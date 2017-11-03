---
title: "Fase de compreensão de negócio da equipa dados ciência processo ciclo de vida - Azure | Microsoft Docs"
description: "Os objetivos, tarefas e materiais a entregar para a fase de compreensão de empresas dos seus projetos de ciência de dados."
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
ms.openlocfilehash: 2adce61f8185bd86a6a870bb5752fe936701b0af
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="business-understanding"></a>Noções sobre empresas

Este tópico descreve os objetivos, tarefas, e associados à materiais a entregar o **fase de compreensão de negócio** do processo de ciência de dados de equipa. Este processo fornece um ciclo de vida recomendado que pode utilizar para estruturar seus projetos de ciência de dados. O ciclo de vida destaca as fases principais que projetos normalmente executar, muitas vezes, iteratively:

* **Compreensão de negócio**
* **Aquisição de dados e a compreensão**
* **Modelação**
* **Implementação**
* **Aceitação de cliente**

Eis uma representação visual do **ciclo de vida do processo de ciência de dados de equipa**. 

![TDSP Lifecycle2](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goals"></a>Objetivos
* O **chave variáveis** são especificado que está a servir o **destinos de modelo** e cujas métricas relacionadas são utilizadas determinar o sucesso para o projeto.
* O relevante **origens de dados** são identificados que a empresa tem acesso ao ou tem de obter.

## <a name="how-to-do-it"></a>Como fazê-lo
Existem duas tarefas principais resolvidas nesta fase: 

* **Definir objetivos**: trabalhar com o seu cliente e outros intervenientes para compreender e identificar os problemas empresariais. Formular perguntas que defina os objetivos de negócio e podem direcionadas para as técnicas de ciência de dados.
* **Identificar as origens de dados**: localizar dados relevantes que o ajuda a responder às perguntas que definem os objetivos do projeto.

### <a name="11-define-objectives"></a>1.1 definir objetivos

1. Um central objetivo deste passo é identificar a chave **variáveis negócio** que tem de prever a análise. Estas variáveis são referidas como o **modelo destinos** e as métricas associadas são utilizadas para determinar o êxito do projeto. Dois exemplos de tais destinos são vendas previsão ou a probabilidade de uma ordem a ser fraudulentas.

2. Definir o **projeto objetivos** ao pedir e limitar "sharp" perguntas relevantes e específicos e inequívoca. Ciência de dados é o processo de utilização de nomes e números para responder a estas perguntas. Para obter mais informações sobre perguntas sharp, consulte [como fazê-lo ciência de dados](https://blogs.technet.microsoft.com/machinelearning/2016/03/28/how-to-do-data-science/) blogue. Ciência de dados / aprendizagem é normalmente utilizada para responder a cinco tipos de questões:
 
   * Quantidade ou quantos? (regressão)
   * A categoria? (classificação)
   * Qual o grupo? (clustering)
   * Este é estranho? (deteção de anomalias)
   * Qual é a opção deverão ser executada? (recomendação)

    Determine que estas perguntas estão a pedir e como responder alcança seus objetivos empresariais.

3. Definir o **projeto de equipa** especificando as funções e responsabilidades de membros. Desenvolva um plano de referência de alto nível reanalisa como são detetadas mais informações.  

4. **Definir métricas de sucesso**. Por exemplo: alcançar precisão de predição de volume de alterações do cliente do X % no final deste projeto de 3 meses, pelo que iremos pode oferecer promoções para reduzir o volume de alterações. A métrica tem de ser **INTELIGENTE**: 
   * **S**pecíficas 
   * **M**easurable
   * **A**chievable 
   * **R**elevant 
   * **T**ime vinculados 

### <a name="12-identify-data-sources"></a>1.2 identificar as origens de dados
Identifica as origens de dados que contenham conhecidos exemplos de respostas às suas perguntas sharp. Procure os dados seguintes:

* Dados que são **Relevant** à pergunta. Temos medidas de destino e as funcionalidades que estão relacionados com o destino?
* Dados que é um **medidas corretas** do nosso destino do modelo e as funcionalidades de interesse.

Não é invulgar, por exemplo, para que os sistemas existentes necessário recolher e registar adicionais tipos de dados para resolver o problema e atingir os objetivos de projeto. Neste caso, pode querer serve para origens de dados externas ou atualizar os sistemas de recolher novos dados.

## <a name="artifacts"></a>Artefactos
Seguem-se a concentrarem nesta fase:

* [**Charter documento**](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Project/Charter.md): é fornecido um modelo padrão na definição de estrutura de projeto do TDSP. Este é um documento de maior duração é atualizado em todo o projeto como novas deteções são efetuadas e como empresariais os requisitos se alteram. A chave é iterar após neste documento, a adição de mais detalhadamente, à medida que o processo de deteção. Manter o cliente e outros intervenientes envolvido na efetuar as alterações e claramente comunicam as razões para que as alterações aos mesmos.  
* [**Origens de dados**](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/DataReport/Data%20Defintion.md#raw-data-sources): Este é o **origens de dados não processados** secção o **definições de dados** relatório que está presente no projeto TDSP **derelatóriodedados** pasta. Especifica as localizações originais e de destino para os dados não processados. Em fases posteriores, preencha detalhes adicionais, como scripts para mover os dados para o seu ambiente de análise.  
* [**Dados dicionários**](https://github.com/Azure/Azure-TDSP-ProjectTemplate/tree/master/Docs/DataDictionaries): este documento fornece descrições dos dados que são fornecidos pelo cliente. Estas descrições incluem informações sobre o esquema (tipos de dados, obter informações sobre as regras de validação, se aplicável) e os diagramas de relação da entidade, se disponível.

## <a name="next-steps"></a>Passos seguintes

Seguem-se ligações para cada passo no ciclo de vida do processo de ciência de dados de equipa:

* [1. Compreensão de negócio](lifecycle-business-understanding.md)
* [2. Aquisição de dados e a compreensão](lifecycle-data.md)
* [3. Modelação](lifecycle-modeling.md)
* [4. Implementação](lifecycle-deployment.md)
* [5. Aceitação de cliente](lifecycle-acceptance.md)

Total de instruções ponto-a-ponto que demonstram todos os passos no processo de **cenários específicos** também são fornecidos. São listados e ligados com descrições de miniaturas no [instruções de exemplo](walkthroughs.md) tópico. Estes mostram como combinar em nuvem, ferramentas no local e serviços para um fluxo de trabalho ou pipeline para criar uma aplicação inteligente. 

Para obter exemplos executar os passos no processo de ciência de dados de equipa que utilizam o Azure Machine Learning Studio, consulte o [com o Azure ML](http://aka.ms/datascienceprocess) percurso de aprendizagem.
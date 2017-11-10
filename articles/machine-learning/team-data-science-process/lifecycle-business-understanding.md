---
title: "Fase de compreensão de negócio o ciclo de vida do processo de ciência de dados de equipa - Azure | Microsoft Docs"
description: "Os objetivos, tarefas e materiais a entregar para a fase de compreensão de empresas dos seus projetos de ciência de dados"
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
ms.openlocfilehash: 82784cbfd5f12297c376a395b54a817a2ae915a5
ms.sourcegitcommit: 93902ffcb7c8550dcb65a2a5e711919bd1d09df9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/09/2017
---
# <a name="business-understanding"></a>Noções sobre empresas

Este artigo descreve os objetivos, tarefas e materiais a entregar associada a fase de compreensão de negócio da equipa de dados de ciência processo (TDSP). Este processo fornece um ciclo de vida recomendado que pode utilizar para estruturar seus projetos de ciência de dados. O ciclo de vida destaca as fases principais que projetos normalmente executar, muitas vezes, iteratively:

   1. **Compreensão de negócio**
   2. **Aquisição de dados e a compreensão**
   3. **Modelação**
   4. **Implementação**
   5. **Aceitação de cliente**

Eis uma representação visual do ciclo de vida TDSP: 

![Ciclo de vida TDSP](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goals"></a>Objetivos
* Especifique as variáveis de chaves que está a servir como os destinos de modelo e cujas métricas relacionadas são utilizadas determinam o êxito do projeto.
* Identifica as origens de dados relevantes que o negócio tem acesso para ou a obter.

## <a name="how-to-do-it"></a>Como fazê-lo
Existem duas tarefas principais resolvidas nesta fase: 

   * **Definir objetivos**: trabalhar com o seu cliente e outros intervenientes para compreender e identificar os problemas empresariais. Formular perguntas que definem os objetivos de negócio podem apontar para as técnicas de ciência de dados.
   * **Identificar as origens de dados**: localizar dados relevantes que o ajuda a responder às perguntas que definem os objetivos do projeto.

### <a name="define-objectives"></a>Definir os objetivos
1. Um central objetivo deste passo é identificar as variáveis de empresarial fundamentais a análise tem de prever. Vamos referir-se a estas variáveis, como o *modelo destinos*, e as métricas associadas são utilizadas para determinar o êxito do projeto. Dois exemplos de tais destinos são previsões de vendas ou a probabilidade de uma ordem a ser fraudulentas.

2. Defina os objetivos de projeto, pedir e limitar as perguntas "sharp", que são relevantes, específico e inequívoca. Ciência de dados é um processo que utiliza os nomes e números para responder a estas perguntas. Para obter mais informações sobre perguntas sharp, consulte o [como fazê-lo ciência de dados](https://blogs.technet.microsoft.com/machinelearning/2016/03/28/how-to-do-data-science/) blogue. Normalmente utiliza ciência de dados ou de aprendizagem automática responder a cinco tipos de questões:
 
   * Quantidade ou quantos? (regressão)
   * A categoria? (classificação)
   * Qual o grupo? (clustering)
   * Este é estranho? (deteção de anomalias)
   * Qual é a opção deverão ser executada? (recomendação)

   Determine que estas perguntas está a pedir e como responder alcança seus objetivos empresariais.

3. Defina a equipa de projeto, especificando as funções e responsabilidades de membros. Desenvolva um plano de referência de alto nível reanalisa como descobrir mais informações. 

4. Defina as métricas de sucesso. Por exemplo, pode querer alcançar uma predição de volume de alterações do cliente. Precisa de uma taxa de precisão de "x" por cento no final deste projeto de três meses. Com estes dados, pode oferecer churn promoções de cliente para reduzir. A métrica tem de ser **INTELIGENTE**: 

   * **S**pecíficas 
   * **M**easurable
   * **A**chievable 
   * **R**elevant 
   * **T**ime vinculados 

### <a name="identify-data-sources"></a>Identificar as origens de dados
Identifica as origens de dados que contenham conhecidos exemplos de respostas às suas perguntas sharp. Procure os dados seguintes:

* Dados que são relevantes para a pergunta. Tiver medidas de destino e as funcionalidades que estão relacionados com o destino?
* Dados que são uma medida de destino modelo exata e as funcionalidades de interesse.

Por exemplo, poderá achar que os sistemas existentes tem de recolher e registar adicionais tipos de dados para resolver o problema e atingir os objetivos de projeto. Nesta situação, pode querer serve para origens de dados externas ou atualizar os sistemas de recolher novos dados.

## <a name="artifacts"></a>Artefactos
Seguem-se a concentrarem nesta fase:

   * [Documento charter](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Project/Charter.md): é fornecido um modelo padrão na definição de estrutura de projeto do TDSP. O documento de charter é um documento de maior duração. Atualizar o modelo em todo o projeto como novas deteções e os como empresariais os requisitos se alteram. A chave é iterar após neste documento, a adição de mais detalhadamente, à medida que o processo de deteção. Manter o cliente e outros intervenientes envolvido na efetuar as alterações e claramente comunicam as razões para que as alterações aos mesmos.  
   * [Origens de dados](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/DataReport/Data%20Defintion.md#raw-data-sources): O **origens de dados não processados** secção o **definições de dados** relatório que está presente no projeto TDSP **relatório de dados** pasta contém o origens de dados. Esta secção especifica as localizações originais e de destino para os dados não processados. Em fases posteriores, preencha detalhes adicionais, como os scripts para mover os dados para o seu ambiente de análise.  
   * [Dicionários dados](https://github.com/Azure/Azure-TDSP-ProjectTemplate/tree/master/Docs/Data_Dictionaries): este documento fornece descrições dos dados que são fornecidos pelo cliente. Estas descrições incluem informações sobre o esquema (os tipos de dados e obter informações sobre as regras de validação, se aplicável) e os diagramas de relação da entidade, se disponível.

## <a name="next-steps"></a>Passos seguintes

Seguem-se ligações para cada passo no ciclo de vida do TDSP:

   1. [Compreensão de negócio](lifecycle-business-understanding.md)
   2. [Aquisição de dados e a compreensão](lifecycle-data.md)
   3. [Modelação](lifecycle-modeling.md)
   4. [Implementação](lifecycle-deployment.md)
   5. [Aceitação de cliente](lifecycle-acceptance.md)

Fornecemos instruções completa ponto-a-ponto que demonstram todos os passos no processo de cenários específicos. O [instruções de exemplo](walkthroughs.md) artigo fornece uma lista dos cenários com ligações e descrições em miniatura. As instruções mostram como combinar em nuvem, ferramentas no local e serviços para um fluxo de trabalho ou pipeline para criar uma aplicação inteligente. 

Para obter exemplos de como executar os passos no TDSPs que utilizam o Azure Machine Learning Studio, consulte [utilizar o TDSP com o Azure Machine Learning](http://aka.ms/datascienceprocess).

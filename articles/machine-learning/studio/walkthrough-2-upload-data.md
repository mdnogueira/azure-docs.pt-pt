---
title: "Passo 2: Carregar dados para uma experimentação do Machine Learning | Microsoft Docs"
description: "Passo 2 da desenvolver uma solução preditiva explicação passo a passo: carregamento armazenados dados públicos no Azure Machine Learning Studio."
services: machine-learning
documentationcenter: 
author: garyericson
manager: jhubbard
editor: cgronlun
ms.assetid: 9f4bc52e-9919-4dea-90ea-5cf7cc506d85
ms.service: machine-learning
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/23/2017
ms.author: garye
ms.openlocfilehash: 469c94f6115f99bc4cf067e9c8f0e55c64990358
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="walkthrough-step-2-upload-existing-data-into-an-azure-machine-learning-experiment"></a>Passo 2 das Instruções: Carregar dados existentes para uma experimentação do Azure Machine Learning
Este é o segundo passo de instruções, [desenvolver uma solução de Análise Preditiva no Azure Machine Learning](walkthrough-develop-predictive-solution.md)

1. [Criar uma área de trabalho do Machine Learning](walkthrough-1-create-ml-workspace.md)
2. **Carregar os dados existentes**
3. [Criar uma nova experimentação](walkthrough-3-create-new-experiment.md)
4. [Dar formação e avaliar os modelos](walkthrough-4-train-and-evaluate-models.md)
5. [Implementar o serviço Web](walkthrough-5-publish-web-service.md)
6. [Aceder ao serviço Web](walkthrough-6-access-web-service.md)

- - -
Para desenvolver um modelo preditivo para risco de crédito, precisamos de dados que podemos utilizar para formação e, em seguida, testar o modelo. Nestas instruções, iremos utilizar "UCI Statlog (alemão crédito dados) conjunto de dados" do repositório UC Irvine Machine Learning. Pode encontrá-lo aqui:  
<a href="http://archive.ics.uci.edu/ml/datasets/Statlog+(German+Credit+Data)">http://Archive.ICS.uci.edu/ml/DataSets/Statlog+(German+Credit+data)</a>

Iremos utilizar o ficheiro denominado **german.data**. Transferir este ficheiro para o disco rígido local.  

O **german.data** conjunto de dados contém linhas de 20 variáveis para 1000 applicants passados para crédito. Estas 20 variáveis representam o conjunto do conjunto de dados de funcionalidades (o *vetor de funcionalidade*), que fornece as características de identificação para cada candidato de crédito. Uma coluna adicional em cada linha representa o risco de crédito calculado do candidato, com 700 applicants identificados como um risco de crédito baixa e 300 como um risco elevado.

O Web site UCI fornece uma descrição dos atributos do vetor de funcionalidade para estes dados. Isto inclui informações financeiras, histórico de crédito, estado de emprego e informações pessoais. Para cada candidato, uma classificação binária foi indicado que indica se são um pouco ou elevado risco de crédito. 

Utilizaremos estes dados para preparar um modelo de Análise Preditiva. Quando são terminados o nosso modelo deve ser capaz aceitar um vetor de funcionalidade para uma nova individuais e prever se for um risco de crédito baixa ou alta.  

Eis um twist interessante. A descrição do conjunto de dados no site da UCI menciona suportadas que custos de se misclassify podemos risco de crédito de uma pessoa.
Se o modelo prevê um risco de crédito elevada para alguém que é um risco de crédito baixa, o modelo foi efetuada uma misclassification.
Mas o misclassification inversa é a instituição financeira dispendiosa mais de cinco vezes: se o modelo prevê um risco de crédito baixa para alguém que é um risco de crédito elevada.

Por isso, queremos preparar o nosso modelo para que o custo deste tipo última de misclassification é cinco vezes superior ao misclassifying a outra forma.
Uma forma simple para efetuar este procedimento quando se prepara o modelo na nossa experiência é duplicar (cinco vezes) para essas entradas que representam alguém com um risco de crédito elevada. Em seguida, se o modelo misclassifies alguém como um risco de crédito baixa quando estiverem efetivamente um risco elevado, o modelo faz esse mesmo misclassification cinco vezes, uma vez por cada duplicado. Isto irá aumentar os custos deste erro nos resultados de formação.


## <a name="convert-the-dataset-format"></a>Converter o formato de conjunto de dados
O conjunto de dados original utiliza um formato de valores separados por em branco. O Machine Learning Studio funciona melhor com um ficheiro de valores separados por vírgulas (CSV), pelo que iremos irá converter o conjunto de dados, substituindo espaços com vírgulas.  

Existem várias formas para converter estes dados. Uma forma é utilizando o seguinte comando do Windows PowerShell:   

    cat german.data | %{$_ -replace " ",","} | sc german.csv  

Outra forma é através do comando de po Unix:  

    sed 's/ /,/g' german.data > german.csv  

Em ambos os casos, criámos uma versão de valores separados por vírgulas dos dados num ficheiro denominado **german.csv** que podemos utilizar na nossa experiência.

## <a name="upload-the-dataset-to-machine-learning-studio"></a>Carregar o conjunto de dados para o Machine Learning Studio
Depois dos dados foi convertidos em formato CSV, precisamos de carregá-la no Machine Learning Studio. 

1. Abrir a home page do Machine Learning Studio ([https://studio.azureml.net](https://studio.azureml.net)). 

2. Clique no menu ![Menu][1] no canto superior esquerdo da janela, clique em **Azure Machine Learning**, selecione **Studio**e iniciar sessão.

3. Clique em **+ novo** na parte inferior da janela.

4. Selecione **DATASET**.

5. Selecione **partir do ficheiro LOCAL**.

    ![Adicionar um conjunto de dados a partir de um ficheiro local][2]

6. No **carregue um novo conjunto de dados** caixa de diálogo, clique em **procurar** e localize o **german.csv** ficheiro que criou.

7. Introduza um nome para o conjunto de dados. Nestas instruções, chamam "UCI alemão cartão de crédito dados".

8. Tipo de dados, selecione **ficheiro CSV genérico com nenhum cabeçalho (. nh.csv)**.

9. Se gostaria de adicionar uma descrição.

10. Clique em de **OK** marca de verificação.  

    ![Carregar o conjunto de dados][3]

Este carrega os dados para um módulo de conjunto de dados que podemos utilizar numa experiência.

Pode gerir conjuntos de dados que carregar para o Studio clicando a **conjuntos de dados** separador à esquerda da janela Studio.

![Gerir conjuntos de dados][4]

Para obter mais informações sobre a importar outros tipos de dados para uma experimentação, consulte [importar os dados de formação para o Azure Machine Learning Studio](import-data.md).

**Seguinte: [criar uma nova experimentação](walkthrough-3-create-new-experiment.md)**

[1]: media/walkthrough-2-upload-data/menu.png
[2]: media/walkthrough-2-upload-data/add-dataset.png
[3]: media/walkthrough-2-upload-data/upload-dataset.png
[4]: media/walkthrough-2-upload-data/dataset-list.png

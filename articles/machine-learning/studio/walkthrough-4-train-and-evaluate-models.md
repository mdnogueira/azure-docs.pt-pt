---
title: "Passo 4: Dar formação e avaliar os modelos de análise preditivos | Microsoft Docs"
description: "Passo 4 da desenvolver uma solução preditiva explicação passo a passo: preparar, Pontuar e avaliar vários modelos no Azure Machine Learning Studio."
services: machine-learning
documentationcenter: 
author: garyericson
manager: jhubbard
editor: cgronlun
ms.assetid: d905f6b3-9201-4117-b769-5f9ed5ee1cac
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/23/2017
ms.author: garye
ms.openlocfilehash: 5a545b9f9f7f935be91fed743779ebd6b4d930a6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="walkthrough-step-4-train-and-evaluate-the-predictive-analytic-models"></a>Passo 4 das Instruções: Preparar e avaliar os modelos de análise preditiva
Este tópico contém o quarto passo de instruções, [desenvolver uma solução de Análise Preditiva no Azure Machine Learning](walkthrough-develop-predictive-solution.md)

1. [Criar uma área de trabalho do Machine Learning](walkthrough-1-create-ml-workspace.md)
2. [Carregar os dados existentes](walkthrough-2-upload-data.md)
3. [Criar uma nova experimentação](walkthrough-3-create-new-experiment.md)
4. **Dar formação e avaliar os modelos**
5. [Implementar o serviço Web](walkthrough-5-publish-web-service.md)
6. [Aceder ao serviço Web](walkthrough-6-access-web-service.md)

- - -
Uma das vantagens de utilizar o Azure Machine Learning Studio para criar modelos de machine learning é a capacidade mais do que um tipo de modelo de um momento a uma único experimentação e comparar os resultados. Este tipo de experimentação ajuda-o a determinar a melhor solução para o seu problema.

A experimentação, que iremos estiver a desenvolver esta explicação passo a passo, vamos criar dois tipos diferentes de modelos e, em seguida, compare os resultados de classificação para decidir qual algoritmo que pretende utilizar na nossa experimentação final.  

Existem vários modelos, pode escolher a partir do. Para ver os modelos disponíveis, expanda o **Machine Learning** nó na paleta do módulo e, em seguida, expanda **inicializar modelo** e os nós que está abaixo. Para efeitos desta fase experimental, iremos selecionar o [máquina de Vetor com suporte de duas classes] [ two-class-support-vector-machine] (SVM) e o [árvore de decisões elevada de duas classes] [ two-class-boosted-decision-tree] módulos.    

> [!TIP]
> Para obter ajuda a decidir qual algoritmo do Machine Learning melhor se adequa ao problema específico que está a tentar resolver, consulte [como escolher algoritmos para o Microsoft Azure Machine Learning](algorithm-choice.md).
> 
> 

## <a name="train-the-models"></a>Preparar os modelos

Vamos adicionar ambos os [árvore de decisões elevada de duas classes] [ two-class-boosted-decision-tree] módulo e [máquina de Vetor com suporte de duas classes] [ two-class-support-vector-machine] módulo nesta fase experimental.

### <a name="two-class-boosted-decision-tree"></a>Árvore de decisões elevada de duas classes

Em primeiro lugar, vamos configurar o modelo de árvore de decisões elevada.

1. Localizar o [árvore de decisões elevada de duas classes] [ two-class-boosted-decision-tree] módulo na paleta do módulo e arraste-o para a tela.

2. Localizar o [preparar modelo] [ train-model] módulo, arraste-o para a tela e, em seguida, ligar o resultado a [árvore de decisões elevada de duas classes] [ two-class-boosted-decision-tree] porta de entrada do módulo para a esquerda a [preparar modelo] [ train-model] módulo.
   
   O [árvore de decisões elevada de duas classes] [ two-class-boosted-decision-tree] módulo inicializa o modelo de genérico e [modelo de formação] [ train-model] utiliza dados de formação para preparar o modelo. 

3. Ligar a saída à esquerda da esquerda [executar Script do R] [ execute-r-script] porta de entrada do módulo para a direita a [preparar modelo] [ train-model] módulo (decidimos no [passo 3](walkthrough-3-create-new-experiment.md) destas instruções para utilizar os dados provenientes do lado esquerdo do módulo dividir dados para formação).
   
   > [!TIP]
   > Já não precisamos duas entradas e uma das saídas do [executar Script do R] [ execute-r-script] módulo para esta fase experimental, pelo que iremos pode deixá-los desanexados. 
   > 
   > 

Esta parte da experimentação agora aspeto semelhante ao seguinte:  

![Um modelo de preparação][1]

Agora precisamos de dizer o [preparar modelo] [ train-model] módulo que pretende é que o modelo para prever o valor de risco de crédito.

1. Selecione o [preparar modelo] [ train-model] módulo. No **propriedades** painel, clique em **Seletor de coluna**.

2. No **Selecione uma coluna** caixa de diálogo, escreva "risco de crédito" no campo de pesquisa em **colunas disponíveis**, selecione "Risco de crédito" abaixo e clique no botão de seta para a direita (**>**) para mover "Risco de crédito" para **colunas selecionadas**. 

    ![Selecionar a coluna de risco de crédito para o módulo preparar modelo][0]

3. Clique em de **OK** marca de verificação.

### <a name="two-class-support-vector-machine"></a>Máquina de Vetores de Suporte de Duas Classes

Em seguida, iremos configurar o modelo SVM.  

Primeiro, um pequeno explicação sobre SVM. Árvores de decisões elevada funcionam bem com funcionalidades de qualquer tipo. No entanto, uma vez que o módulo SVM gera um classificador linear, do modelo que gera tem o erro de teste melhor quando todas as funcionalidades numérico têm a mesma escala. Converter todas as funcionalidades numérico para a mesma escala, utilizamos uma transformação "Tanh" (com o [normalizar dados] [ normalize-data] módulo). Isto transforma nosso números em intervalo [0,1]. O módulo SVM converte funcionalidades de cadeia para funcionalidades categórico e, em seguida, para funcionalidades de 0/1 binárias, pelo que não temos de transformação manualmente as funcionalidades de cadeia. Além disso, não queremos transformar a risco de crédito coluna (21) - é numérico, mas é o valor que está a prepara o modelo para prever, pelo que temos de deixá-lo individualmente.  

Para configurar o modelo SVM, efetue o seguinte:

1. Localizar o [máquina de Vetor com suporte de duas classes] [ two-class-support-vector-machine] módulo na paleta do módulo e arraste-o para a tela.

2. Clique com botão direito do [preparar modelo] [ train-model] módulo, selecione **cópia**e, em seguida, clique com botão direito a tela e selecione **colar**. A cópia do [preparar modelo] [ train-model] módulo tem da seleção de coluna mesmo que o original.

3. Ligar o resultado a [máquina de Vetor com suporte de duas classes] [ two-class-support-vector-machine] a porta da segunda de entrada do módulo para a esquerda [preparar modelo] [ train-model] módulo.

4. Localizar o [normalizar dados] [ normalize-data] módulo e arraste-o para a tela.

5. Ligar a saída à esquerda da esquerda [executar Script do R] [ execute-r-script] módulo para a entrada deste módulo (tenha em atenção que a porta de saída de um módulo pode ser ligada à mais do que um outro módulo).

6. Ligue a porta de saída à esquerda do [normalizar dados] [ normalize-data] a porta da segunda de entrada do módulo para a direita [preparar modelo] [ train-model] módulo.

Esta parte do nosso experimentação deve agora algo semelhante ao seguinte:  

![O segundo modelo de preparação][2]  

Configurar agora o [normalizar dados] [ normalize-data] módulo:

1. Clique para selecionar o [normalizar dados] [ normalize-data] módulo. No **propriedades** painel, selecione **Tanh** para o **método de transformação** parâmetro.

2. Clique em **Seletor de coluna**, selecione "Colunas" para **começar com**, selecione **incluir** na primeira lista pendente, selecione **tipo da coluna** na segunda pendente e selecione **numérico** na lista pendente terceira. Esta ação Especifica que todas as colunas numéricas (e apenas numérica) são transformados.

3. Clique no sinal de menos (+) para a direita desta linha - esta ação cria uma linha com as dropdowns. Selecione **excluir** na primeira lista pendente, selecione **os nomes das colunas** na lista pendente segundo e introduza "Risco de crédito" no campo de texto. Esta ação Especifica que a coluna de risco de crédito deve ser ignorada (é necessário fazê-lo porque esta coluna é numérico e para, teria de ser transformados se podemos não excluí-lo).

4. Clique em de **OK** marca de verificação.  

    ![Selecionar colunas para o módulo de normalizar dados][5]

O [normalizar dados] [ normalize-data] módulo está agora definido para efetuar uma transformação Tanh em todas as colunas numéricas, exceto para a coluna de risco de crédito.  

## <a name="score-and-evaluate-the-models"></a>Pontuar e avaliar os modelos

Utilizamos os dados de teste que foi separados enviados pelo [dividir dados] [ split] módulo para pontuar o nosso modelos de formação. Vamos, em seguida, pode comparar os resultados dos dois modelos para ver que gerado melhores resultados.  

### <a name="add-the-score-model-modules"></a>Adicionar os módulos do modelo de pontuação

1. Localizar o [Pontuar modelo] [ score-model] módulo e arraste-o para a tela.

2. Ligar o [preparar modelo] [ train-model] módulo que está ligado à [árvore de decisões elevada de duas classes] [ two-class-boosted-decision-tree] porta de entrada do módulo para a esquerda a [Pontuar modelo] [ score-model] módulo.

3. Ligar à direita [executar Script do R] [ execute-r-script] porta de entrada do módulo (nossos dados de teste) para a direita a [Pontuar modelo] [ score-model] módulo.

    ![Módulo do modelo de pontuação ligado][6]
   
   O [Pontuar modelo] [ score-model] módulo agora pode efetuar as informações de crédito de dados de teste, execute-o utilizando o modelo e comparar serão as predições gera o modelo com a coluna de risco de crédito real dos dados de teste.

4. Copie e cole o [Pontuar modelo] [ score-model] módulo para criar uma segunda cópia.

5. Ligar o resultado do modelo SVM (ou seja, a porta de saída do [preparar modelo] [ train-model] módulo que está ligado à [máquina de Vetor com suporte de duas classes] [ two-class-support-vector-machine] módulo) para a porta de entrada do segundo [Pontuar modelo] [ score-model] módulo.

6. Para o modelo SVM, temos de fazer a transformação mesma para os dados de teste, como fizemos para os dados de preparação. Por isso, copie e cole o [normalizar dados] [ normalize-data] módulo para criar uma segunda cópia e ligá-lo para a direita [executar Script do R] [ execute-r-script] módulo.

7. Ligar a saída à esquerda do segundo [normalizar dados] [ normalize-data] a porta da segunda de entrada do módulo para a direita [Pontuar modelo] [ score-model] módulo.

    ![Ambos os módulos de modelo de pontuação ligados][7]

### <a name="add-the-evaluate-model-module"></a>Adicionar o módulo de modelo de avaliação

Para avaliar os dois resultados de classificação e compará-los, utilizamos uma [avaliar modelo] [ evaluate-model] módulo.  

1. Localizar o [avaliar modelo] [ evaluate-model] módulo e arraste-o para a tela.

2. Ligue a porta de saída do [Pontuar modelo] [ score-model] módulo associado ao modelo de árvore de decisões elevada para a porta de entrada à esquerda do [avaliar modelo] [ evaluate-model] módulo.

3. Ligar a outra [Pontuar modelo] [ score-model] porta de entrada do módulo para a direita.  

    ![Avaliar modelo módulo ligado][8]

### <a name="run-the-experiment-and-check-the-results"></a>Execute a experimentação e verificar os resultados

Para executar a experimentação, clique em de **executar** no botão abaixo da tela. Pode demorar alguns minutos. Mostra um indicador de girar em cada módulo que está a ser executado e, em seguida, uma marca de verificação verde mostra quando o módulo for concluído. Quando todos os módulos têm uma marca de verificação, a experimentação concluiu a execução.

A experimentação deve agora algo semelhante ao seguinte:  

![Avaliar ambos os modelos][3]

Para verificar os resultados, clique na porta de saída do [avaliar modelo] [ evaluate-model] módulo e selecione **visualizar**.  

O [avaliar modelo] [ evaluate-model] módulo produz um par de curvas e métricas que permitem-lhe comparar os resultados dos dois modelos classificados. Pode ver os resultados como curvas Recetor operador característica (ROC), curvas precisão/devolução de chamada ou curvas de comparação de precisão. Dados adicionais apresentados incluem uma matriz de confusão, cumulativas valores para a área sob a curva (AUC) e outras métricas. Pode alterar o valor do limiar, mova o controlo de deslize esquerda ou direita e ver como afetará o conjunto de métricas.  

À direita do gráfico, clique em **classificada dataset** ou **classificada conjunto de dados para comparar** para realçar a curva associada e a apresentação das métricas associadas abaixo. Na legenda para as curvas, "Classificada o conjunto de dados" corresponde à porta de entrada à esquerda do [avaliar modelo] [ evaluate-model] módulo - no nosso caso, este é o modelo de árvore de decisões elevada. "Classificada o conjunto de dados para comparar" corresponde à porta de entrada à direita - o modelo SVM no nosso caso. Quando clica destas etiquetas, a curva para esse modelo é realçada e são apresentadas as métricas correspondentes, conforme mostrado no seguinte gráfico.  

![Curvas ROC para modelos][4]

Ao examinar estes valores, pode decidir o modelo mais se aproxima dando-lhe os resultados da que procura. Pode voltar atrás e iterar sua experimentação, alterando os valores de parâmetros nos modelos diferentes. 

A ciência e art de interpretar estes resultados e otimizar o desempenho do modelo está fora do âmbito destas instruções. Para obter ajuda adicional, pode ler os artigos seguintes:
- [Como avaliar o desempenho de modelo no Azure Machine Learning](evaluate-model-performance.md)
- [Selecione parâmetros para otimizar o seu algoritmos no Azure Machine Learning](algorithm-parameters-optimize.md)
- [Interpretar os resultados de modelo no Azure Machine Learning](interpret-model-results.md)

> [!TIP]
> Sempre que executar a experimentação desse iteração um registo é mantido no histórico de execução. Pode ver estes iterações e voltar a qualquer um deles, clicando **ver histórico de EXECUÇÕES** abaixo da tela. Também pode clicar em **executar o anterior** no **propriedades** painel para regressar ao iteração imediatamente anterior aquele tiver aberto.
> 
> Pode efetuar uma cópia de qualquer iteração da sua experimentação, clicando em **guardar como** abaixo da tela. 
> Utilize a experimentação **resumo** e **Descrição** propriedades para manter um registo das quais já tentou no seu iterações de experimentação.
> 
> Para obter mais detalhes, consulte [gerir iterações de experimentação no Azure Machine Learning Studio](manage-experiment-iterations.md).  
> 
> 

- - -
**Seguinte: [implementar o serviço web](walkthrough-5-publish-web-service.md)**

[0]: ./media/walkthrough-4-train-and-evaluate-models/train-model-select-column.png
[1]: ./media/walkthrough-4-train-and-evaluate-models/experiment-with-train-model.png
[2]: ./media/walkthrough-4-train-and-evaluate-models/svm-model-added.png
[3]: ./media/walkthrough-4-train-and-evaluate-models/final-experiment.png
[4]: ./media/walkthrough-4-train-and-evaluate-models/roc-curves.png
[5]: ./media/walkthrough-4-train-and-evaluate-models/normalize-data-select-column.png
[6]: ./media/walkthrough-4-train-and-evaluate-models/score-model-connected.png
[7]: ./media/walkthrough-4-train-and-evaluate-models/both-score-models-added.png
[8]: ./media/walkthrough-4-train-and-evaluate-models/evaluate-model-added.png


<!-- Module References -->
[evaluate-model]: https://msdn.microsoft.com/library/azure/927d65ac-3b50-4694-9903-20f6c1672089/
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[normalize-data]: https://msdn.microsoft.com/library/azure/986df333-6748-4b85-923d-871df70d6aaf/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
[two-class-boosted-decision-tree]: https://msdn.microsoft.com/library/azure/e3c522f8-53d9-4829-8ea4-5c6a6b75330c/
[two-class-support-vector-machine]: https://msdn.microsoft.com/library/azure/12d8479b-74b4-4e67-b8de-d32867380e20/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/

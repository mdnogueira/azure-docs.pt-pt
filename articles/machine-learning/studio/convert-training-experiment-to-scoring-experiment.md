---
title: "Como preparar o modelo de implementação no Azure Machine Learning Studio | Microsoft Docs"
description: "Como preparar o modelo treinado para implementação como um serviço web, convertendo a sua experimentação de preparação do Machine Learning Studio para uma experimentação preditiva."
services: machine-learning
documentationcenter: 
author: garyericson
manager: jhubbard
editor: cgronlun
ms.assetid: eb943c45-541a-401d-844a-c3337de82da6
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/28/2017
ms.author: garye
ms.openlocfilehash: 96434d6c5a2cf73e425c02d35d546ab33b7ac13d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-prepare-your-model-for-deployment-in-azure-machine-learning-studio"></a>Como preparar o modelo de implementação no Azure Machine Learning Studio

Azure Machine Learning Studio dá-lhe as ferramentas que necessárias para desenvolver um modelo de Análise Preditiva e operacionalizá-lo, em seguida, implementando-o como um serviço web do Azure.

Para tal, utilize Studio para criar uma experimentação - chamada um *experimentação de preparação* - onde formação, Pontuar e editar o seu modelo. Quando estiver satisfeito, obter o seu modelo preparado para implementar convertendo a sua experimentação de preparação para um *experimentação preditiva* que está configurada para dados de utilizador de pontuação.

Pode ver um exemplo deste processo no [instruções: desenvolver uma solução de Análise Preditiva para a avaliação de riscos de crédito no Azure Machine Learning](walkthrough-develop-predictive-solution.md).

Este artigo assume uma descrição profunda para os detalhes de como uma experimentação de preparação é convertida para uma experimentação preditiva e a forma como esse experimentação preditiva é implementada. Por compreender estes detalhes, pode saber como configurar o seu modelo implementado para o tornar mais eficiente.

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="overview"></a>Descrição geral 

O processo de conversão de uma experimentação de preparação para uma experimentação preditiva envolve três passos:

1. Substitua a módulos do algoritmo com o modelo treinado de aprendizagem.
2. Corte a experimentação para apenas os módulos que são necessários para a classificação. Uma experimentação de formação inclui um número de módulos que são necessários para formação, mas não são necessárias quando o modelo está preparado.
3. Defina como o seu modelo irá aceitar dados do utilizador de serviço web e os dados que vai ser devolvidos.

> [!TIP]
> Na sua experimentação de preparação, tiver sido preocupado com formação e classificação do modelo, utilizando os seus próprios dados. Mas, depois de implementada, os utilizadores enviará novos dados para o seu modelo e irá devolver resultados de predição. Por isso, como converter sua experimentação de preparação para uma experimentação preditiva para preparar a implementação, tenha em atenção como o modelo será utilizado por outras pessoas.
> 
> 

## <a name="set-up-web-service-button"></a>Botão de segurança de serviço Web
Depois de executar a experimentação (clique **executar** na parte inferior da tela de experimentação), clique no **segurança serviço Web** botão (selecionar o **preditiva serviço Web** opção). **Segurança de serviço Web** executa para os três passos de conversão da sua experimentação de preparação para uma experimentação preditiva:

1. Guarde o modelo treinado o **modelos de formação** secção da paleta do módulo (para a esquerda da tela de experimentação). Em seguida, substitui o algoritmo do machine learning e [preparar modelo] [ train-model] módulos com o modelo treinado guardado.
2. Este analisa a sua experimentação e remove os módulos que claramente foram utilizados apenas para formação e já não são necessárias.
3. -Insere _Web service entrada_ e _saída_ módulos para as localizações predefinidas na sua experimentação (estes módulos aceitarem e devolvem dados de utilizador).

Por exemplo, a experimentação seguinte trains um modelo de árvore de decisões elevada de duas classes com dados de census de exemplo:

![Experimentação de preparação][figure1]

Os módulos nesta fase experimental efetuar basicamente quatro diferentes funções:

![Funções de módulo][figure2]

Quando converter este experimentação de preparação para uma experimentação preditiva, alguns destes módulos já não são necessários ou agora servem um objetivo diferente:

* **Dados** -os dados deste conjunto de dados de exemplo não são utilizados durante a classificação - o utilizador do serviço web irá fornecer os dados para classificar. No entanto, os metadados a partir deste conjunto de dados, tais como tipos de dados, é utilizado pelo modelo de formação. Por isso terá de manter o conjunto de dados na experimentação preditiva para que possa fornecer estes metadados.

* **Preparação** -, consoante os dados de utilizador que irá ser submetido para classificação, estes módulos podem ou não poderão ser necessários para processar os dados de entrada. O **segurança serviço Web** botão não touch estes - tem de decidir como pretende lidar com os mesmos.
  
    Por exemplo, neste exemplo, o conjunto de dados de exemplo pode ter valores em falta, pelo que um [apagar dados em falta] [ clean-missing-data] módulo foi incluído para lidar com os mesmos. Além disso, o conjunto de dados de exemplo inclui colunas que não são necessários para preparar o modelo. Por isso, um [selecionar colunas no conjunto de dados] [ select-columns] módulo foi incluído a excluir esses colunas adicionais do fluxo de dados. Se souber que os dados que irão ser submetidos para a classificação de através do serviço web não terão valores em falta, em seguida, pode remover o [apagar dados em falta] [ clean-missing-data] módulo. No entanto, dado que o [selecionar colunas no conjunto de dados] [ select-columns] módulo ajuda a definir as colunas de dados que o modelo treinado espera, esse módulo tem de permanecer.

* **Preparar** -estes módulos são utilizados para preparar o modelo. Ao clicar em **segurança serviço Web**, estes módulos são substituídos por um módulo único que contém o modelo que preparado. Este novo módulo é guardado no **modelos de formação** secção da paleta do módulo.

* **Pontuação** - neste exemplo, o [dividir dados] [ split] módulo é utilizado para dividir o fluxo de dados em dados de formação e dados de teste. Na experimentação preditiva, iremos estiver a não formação, por isso [dividir dados] [ split] pode ser removido. Da mesma forma, o segundo [Pontuar modelo] [ score-model] módulo e a [avaliar modelo] [ evaluate-model] módulo são utilizados para comparar os resultados de dados de teste, por isso, Estes módulos não são necessários na experimentação preditiva. O restante [Pontuar modelo] [ score-model] módulo, no entanto, é necessário para devolver um resultado de modelo de pontuação através do serviço web.

Eis o aspeto do nosso exemplo depois de clicar em **segurança serviço Web**:

![Converter a experimentação preditiva][figure3]

O trabalho feito **segurança serviço Web** pode ser suficiente para preparar a sua experimentação para ser implementado como um serviço web. No entanto, poderá querer fazer alguns cálculos adicionais específicos à sua experimentação.

### <a name="adjust-input-and-output-modules"></a>Ajustar os módulos de entrada e de saída
Na sua experimentação de preparação, utilizar um conjunto de dados de formação e, em seguida, tiver algumas processamento para obter os dados num formato que o algoritmo do machine learning necessários. Se os dados esperados para receber através do serviço web não serão necessário deste processamento, pode ignorar: ligar o resultado a **módulo entrado do serviço Web** a um módulo diferente na sua experimentação. Os dados do utilizador serão agora chegam no modelo de nesta localização.

Por exemplo, por predefinição **segurança serviço Web** coloca o **Web service entrada** módulo na parte superior do seu fluxo de dados, conforme mostrado na figura acima. Mas iremos manualmente pode posicionar o **Web service entrada** passado os módulos de processamento de dados:

![Mover a entrada de serviço web][figure4]

Os dados de entrada fornecidos através do serviço web serão agora passar diretamente para o módulo de modelo de pontuação sem qualquer pré-processamentos.

Da mesma forma, por predefinição **segurança serviço Web** coloca o módulo de saída do serviço Web na parte inferior do seu fluxo de dados. Neste exemplo, o serviço web irá devolver ao utilizador o resultado a [Pontuar modelo] [ score-model] módulo, que inclui o vetor de dados de entrada completa plus os resultados de classificação.
No entanto, se preferir devolve algo diferente, em seguida, pode adicionar módulos adicionais antes do **Web saída de serviço** módulo. 

Por exemplo, para devolver apenas os resultados de classificação e não o vetor de completo de dados de entrada, adicione um [selecionar colunas no conjunto de dados] [ select-columns] módulo para excluir todas as colunas, exceto os resultados de classificação. Em seguida, mova o **Web saída de serviço** módulo à saída do [selecionar colunas no conjunto de dados] [ select-columns] módulo. A experimentação tem o seguinte aspeto:

![Mover a saída de serviço web][figure5]

### <a name="add-or-remove-additional-data-processing-modules"></a>Adicionar ou remover módulos de processamento de dados adicionais
Se existirem mais módulos na sua experimentação que sabe que não serão necessários durante a classificação, estes podem ser removidos. Por exemplo, porque foi movido o **Web service entrada** módulo para um ponto após os módulos de processamento de dados, pode remover o [apagar dados em falta] [ clean-missing-data] módulo a partir do experimentação preditiva.

A nossa experimentação preditiva agora este aspeto:

![Ao remover o módulo adicional][figure6]


### <a name="add-optional-web-service-parameters"></a>Adicionar parâmetros de serviço Web opcionais
Em alguns casos, poderá querer permitir que o utilizador do seu serviço web alterar o comportamento dos módulos quando o serviço é acedido. *Os parâmetros do serviço de Web* permitem-lhe fazê-lo.

Um exemplo comum está a configurar um [importar dados] [ import-data] módulo, pelo que o utilizador do serviço web implementado pode especificar uma origem de dados diferente quando o serviço web é acedido. Ou a configuração de um [exportar dados] [ export-data] módulo para que pode ser especificado um destino diferente.

Pode definir os parâmetros do serviço Web e associá-las com um ou mais parâmetros do módulo, e pode especificar se estão necessária ou opcional. O utilizador do serviço web fornece valores para estes parâmetros quando o serviço é acedido e as ações de módulo são modificadas em conformidade.

Para obter mais informações sobre quais são os parâmetros do serviço Web e como utilizá-los, consulte [utilizando parâmetros de serviço de Web do Azure Machine Learning][webserviceparameters].

[webserviceparameters]: web-service-parameters.md


## <a name="deploy-the-predictive-experiment-as-a-web-service"></a>Implementar a experimentação preditiva como um serviço web
Agora que tenha sido preparada suficientemente a experimentação preditiva, pode implementá-lo como um serviço web do Azure. Utilizando o serviço web, os utilizadores podem enviar dados para o seu modelo e o modelo irá devolver as predições.

Para obter mais informações sobre o processo de implementação completa, consulte [implementar um serviço web do Azure Machine Learning][deploy]

[deploy]: publish-a-machine-learning-web-service.md


<!-- Images -->
[figure1]:./media/convert-training-experiment-to-scoring-experiment/figure1.png
[figure2]:./media/convert-training-experiment-to-scoring-experiment/figure2.png
[figure3]:./media/convert-training-experiment-to-scoring-experiment/figure3.png
[figure4]:./media/convert-training-experiment-to-scoring-experiment/figure4.png
[figure5]:./media/convert-training-experiment-to-scoring-experiment/figure5.png
[figure6]:./media/convert-training-experiment-to-scoring-experiment/figure6.png


<!-- Module References -->
[clean-missing-data]: https://msdn.microsoft.com/library/azure/d2c5ca2f-7323-41a3-9b7e-da917c99f0c4/
[evaluate-model]: https://msdn.microsoft.com/library/azure/927d65ac-3b50-4694-9903-20f6c1672089/
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
[export-data]: https://msdn.microsoft.com/library/azure/7a391181-b6a7-4ad4-b82d-e419c0d6522c/

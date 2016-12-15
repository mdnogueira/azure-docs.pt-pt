---
title: "Copiar experimentações de exemplo no Machine Learning | Microsoft Docs"
description: "Saiba como utilizar experimentações de exemplo do Machine Learning para criar novas experimentações com o Cortana Intelligence Gallery e com o Microsoft Azure Machine Learning."
services: machine-learning
documentationcenter: 
author: cjgronlund
manager: jhubbard
editor: cgronlun
ms.assetid: 81e6c1d8-682c-4db3-bfd5-d7bfb1150ff3
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 11/11/2016
ms.author: cgronlun;olgali
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 9c710e6f959afd8a4f4b965931ed4868d94c6d25


---
# <a name="copy-sample-experiments-to-create-new-machine-learning-experiments"></a>Copiar experimentações de exemplo para criar novas experimentações no Machine Learning
Saiba como iniciar experimentações de exemplo a partir do [Cortana Intelligence Gallery](https://gallery.cortanaintelligence.com/) em vez de criar experimentações do zero no Machine Learning. Pode utilizar os exemplos para criar a sua própria solução de Machine Learning.

Na galeria são apresentados exemplos de experimentações da equipa do Microsoft Azure Machine Learning, bem como exemplos partilhados pela Comunidade do Machine Learning. Também pode colocar questões ou publicar comentários sobre experimentações.

Para saber como utilizar a galeria, veja o vídeo de 3 minutos [Copy other people's work to do data science (Copiar o trabalho de outras pessoas para efetuar ciência de dados)](machine-learning-data-science-for-beginners-copy-other-peoples-work-to-do-data-science.md) da série [Data Science for Beginners (Ciência de Dados para Principiantes)](machine-learning-data-science-for-beginners-the-5-questions-data-science-answers.md).

[!INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## <a name="find-an-experiment-to-copy-in-cortana-intelligence-gallery"></a>Localizar uma experimentação para copiar no Cortana Intelligence Gallery
Para ver as experimentações que estão disponíveis, aceda à [Galeria](https://gallery.cortanaintelligence.com/) e clique em **Experimentações** na parte superior da página.

### <a name="find-the-newest-or-most-popular-experiments"></a>Localizar as experimentações mais recentes ou mais populares
Nesta página, pode ver as experimentações **Recentemente adicionadas** ou deslocar para baixo para ver **O que é popular** ou as **experimentações Microsoft populares** mais recentes.

### <a name="look-for-an-experiment-that-meets-specific-requirements"></a>Procurar uma experimentação que cumpra os requisitos específicos
Para procurar todas as experimentações:

1. Clique em **Procurar tudo** na parte superior da página.
2. Em **Refinar por**, selecione **Experimentação** para ver todas as experimentações na Galeria.
3. Pode encontrar experimentações que cumprem os requisitos de duas formas diferentes:
   * **Selecione filtros no lado esquerdo.** Por exemplo, para procurar experimentações que utilizem um algoritmo de deteção de anomalias baseado em PCA, selecione **Experimentação** em **Categorias** e **Deteção de Anomalias com Base em PCA** em **Algoritmos Utilizados**. (Se não vir esse algoritmo, clique em **Mostrar tudo** na parte inferior da lista.)<br></br>
     ![Selecionar filtros](./media/machine-learning-sample-experiments/refine-the-view.png)
   * **Utilize a caixa de pesquisa.** Por exemplo, para procurar experimentações que tenham feito parte da Microsoft e estejam relacionadas com o reconhecimento de dígitos que utilizam um algoritmo da máquina de vetor com suporte de classe dois, introduza "reconhecimento de dígitos" na caixa de pesquisa. Em seguida, selecione os filtros **Experimentação**, **Apenas conteúdos da Microsoft** e **Máquina de Vetor com Suporte de Classe Dois**: ![Utilizar a caixa de pesquisa](./media/machine-learning-sample-experiments/search-for-experiments.png)
4. Clique numa experimentação para saber mais acerca da mesma.
5. Para executar e/ou modificar a experimentação, clique em **Abrir no Studio** na página de experimentação.

   > [!NOTE]
   > Para abrir uma experimentação no Machine Learning Studio, tem de iniciar sessão com as credenciais da sua conta Microsoft. Se ainda não tem uma área de trabalho do Machine Learning, é criada uma área de trabalho de avaliação gratuita. [Saiba o que está incluído na versão de avaliação gratuita do Machine Learning](https://azure.microsoft.com/pricing/details/machine-learning/)
   >
   >

    ![Experimentação de exemplo](./media/machine-learning-sample-experiments/example-experiment.png)

## <a name="use-a-template-in-machine-learning-studio"></a>Utilizar um modelo no Machine Learning Studio
Também pode criar uma nova experimentação no Machine Learning Studio, utilizando um exemplo da galeria como um modelo.

1. Inicie sessão com as credenciais da sua conta Microsoft no [Studio](https://studio.azureml.net) e, em seguida, clique em **Novo** para criar uma nova experimentação.
2. Navegue no conteúdo de exemplos e clique num exemplo.

É criada uma nova experimentação na sua área de trabalho, utilizando a experimentação de exemplo como um modelo.

## <a name="next-steps"></a>Passos seguintes
* [Prepare os seus dados](machine-learning-data-science-import-data.md)
* [Experimente utilizar R na sua experimentação](machine-learning-r-quickstart.md)
* [Rever experimentações de exemplo de R](machine-learning-r-csharp-web-service-examples.md)
* [Criar uma API de serviço Web](machine-learning-publish-a-machine-learning-web-service.md)



<!--HONumber=Dec16_HO1-->



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
ms.date: 02/13/2017
ms.author: cgronlun;olgali
translationtype: Human Translation
ms.sourcegitcommit: 25e1def880dbb8431e1a0cae9d8b84c6f5906315
ms.openlocfilehash: 7b2bca108770a79a693a408a628ccc6370c36b7c


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
2. Em **Refinar por** na secção **Categorias**, selecione **Experimentação** para ver todas as experimentações na Galeria.
3. Pode encontrar experimentações que cumprem os requisitos de duas formas diferentes:
   * **Selecione filtros no lado esquerdo.** Por exemplo, para procurar experimentações que utilizem um algoritmo de deteção de anomalias baseado em PCA, selecione **Experimentação** em **Categorias** e **Deteção de Anomalias com Base em PCA** em **Algoritmos Utilizados**. (Se não vir esse algoritmo, clique em **Mostrar tudo** na parte inferior da lista.)<br></br>
     ![Selecionar filtros](./media/machine-learning-sample-experiments/refine-the-view.png)
   * **Utilize a caixa de pesquisa.** Por exemplo, para procurar experimentações que tenham feito parte da Microsoft e estejam relacionadas com o reconhecimento de dígitos que utilizam um algoritmo da máquina de vetor com suporte de classe dois, introduza "reconhecimento de dígitos" na caixa de pesquisa. Em seguida, selecione os filtros **Experimentação**, **Apenas conteúdos da Microsoft** e **Máquina de Vetor com Suporte de Classe Dois**:<br></br>
     ![Utilize a caixa de pesquisa](./media/machine-learning-sample-experiments/search-for-experiments.png)
4. Clique numa experimentação para saber mais acerca da mesma.
5. Para executar e/ou modificar a experimentação, clique em **Abrir no Studio** na página de experimentação. <br></br>

    ![Experimentação de exemplo](./media/machine-learning-sample-experiments/example-experiment.png)

    > [!NOTE]
    > Quando abre uma experimentação no Machine Learning Studio pela primeira vez, pode experimentar gratuitamente ou comprar uma subscrição do Azure. [Saiba mais sobre a avaliação gratuita do Machine Learning Studio vs. serviço pago](https://azure.microsoft.com/pricing/details/machine-learning/)
    >
    >

## <a name="use-a-template-in-machine-learning-studio"></a>Utilizar um modelo no Machine Learning Studio
Também pode criar uma nova experimentação no Machine Learning Studio, utilizando um exemplo da galeria como um modelo.

1. Inicie sessão com as credenciais da sua conta Microsoft no [Studio](https://studio.azureml.net) e, em seguida, clique em **Novo** para criar uma experimentação.
2. Navegue no conteúdo de exemplos e clique num exemplo.

É criada uma nova experimentação na sua área de trabalho, utilizando a experimentação de exemplo como um modelo.

## <a name="next-steps"></a>Passos seguintes
* [Importar dados de várias origens](machine-learning-data-science-import-data.md)
* [Tutorial de início rápido para a linguagem R no Machine Learning](machine-learning-r-quickstart.md)
* [Implementar um serviço Web Machine Learning](machine-learning-publish-a-machine-learning-web-service.md)



<!--HONumber=Feb17_HO2-->



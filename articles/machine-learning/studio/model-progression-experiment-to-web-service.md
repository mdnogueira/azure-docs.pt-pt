---
title: "Como um modelo do Azure Machine Learning torna-se um serviço web | Microsoft Docs"
description: "Uma descrição geral de mechanics de como a avança de ser do modelo do Azure Machine Learning a partir de um desenvolvimento experimentação para um serviço Web operacionalizado."
services: machine-learning
documentationcenter: 
author: garyericson
manager: jhubbard
editor: cgronlun
ms.assetid: 25e0c025-f8b0-44ab-beaf-d0f2d485eb91
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2017
ms.author: garye
ms.openlocfilehash: 383f0a466f92a230e49c3d1e96d306a0b7d67da2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-a-machine-learning-model-progresses-from-an-experiment-to-an-operationalized-web-service"></a>Como um modelo de Machine Learning avança a partir de uma experimentação para um serviço Web operacionalizado
Azure Machine Learning Studio fornece uma tela interativa que lhe permite desenvolver, executar, testar e iterar um ***experimentação*** que representa um modelo de Análise Preditiva. Existem uma grande variedade de módulos que podem:

* Dados de entrada na sua experiência
* Manipular os dados
* Dar formação sobre um modelo, utilizando algoritmos do machine learning
* Pontuar o modelo
* Avaliar os resultados
* Valores finais de saída

Quando estiver satisfeito com a sua experimentação, pode implementá-la como uma ***serviço clássico do Azure o Machine Learning Web*** ou um ***serviço novo Azure Machine Learning Web*** para que os utilizadores podem enviar dados novo e receber os resultados de back.

Neste artigo, vamos dar uma descrição geral do mechanics de como o seu avança de ser do modelo do Machine Learning a partir de um desenvolvimento experimentação para um serviço Web operacionalizado.

> [!NOTE]
> Existem outras formas para desenvolver e implementar modelos de machine learning, mas este artigo concentra-se sobre como utilizar o Machine Learning Studio. Por exemplo, para ler uma descrição de como criar um serviço Web preditivo clássico com R, consulte a mensagem de blogue [compilação & implementar preditiva Web Apps utilizando RStudio e do Azure ML](http://blogs.technet.com/b/machinelearning/archive/2015/09/25/build-and-deploy-a-predictive-web-app-using-rstudio-and-azure-ml.aspx).
> 
> 

Enquanto o Azure Machine Learning Studio é concebido para ajudar a desenvolver e implementar uma *modelo de Análise Preditiva*, é possível utilizar o Studio para desenvolver uma experimentação que não inclui um modelo de Análise Preditiva. Por exemplo, uma experimentação poderá apenas dados de entrada, a manipulação-la e, em seguida, enviar os resultados. Tal como uma experimentação de Análise Preditiva, pode implementar esta fase experimental não preditiva como um serviço Web, mas é um processo mais simples porque a experimentação não é de preparação ou a classificação de um modelo de machine learning. Embora não seja típicas para utilizar o Studio desta forma, iremos irá inclui no debate para que iremos pode dar uma explicação completa como funciona o Studio.

## <a name="developing-and-deploying-a-predictive-web-service"></a>Desenvolver e implementar um serviço Web preditivo
Seguem-se as fases que se segue uma solução típica como desenvolver e implementá-la utilizando o Machine Learning Studio:

![Fluxo de implementação](./media/model-progression-experiment-to-web-service/model-stages-from-experiment-to-web-service.png)

*Figura 1 - fases de um modelo de Análise Preditiva típica*

### <a name="the-training-experiment"></a>A experimentação de preparação
O ***experimentação de preparação*** é a fase inicial de desenvolver o seu serviço Web no Machine Learning Studio. O objetivo da experimentação de preparação é dão-lhe um local para desenvolver, testar, iterar e, eventualmente, dar formação sobre um modelo de machine learning. Pode ainda dar formação sobre vários modelos em simultâneo como procurar a melhor solução mas, depois de concluir a conseguirmos uma irá selecionar um único preparado modelo e eliminar o resto da experimentação. Para obter um exemplo de desenvolvimento de uma experimentação de Análise Preditiva, consulte [desenvolver uma solução de Análise Preditiva para a avaliação de riscos de crédito no Azure Machine Learning](walkthrough-develop-predictive-solution.md).

### <a name="the-predictive-experiment"></a>A experimentação preditiva
Depois de ter um modelo preparado na experimentação de preparação, clique em **segurança serviço Web** e selecione **preditiva serviço Web** no Machine Learning Studio para iniciar o processo de conversão sua experimentação de preparação para um ***experimentação preditiva***. O objetivo a experimentação preditiva consiste em utilizar o modelo treinado para pontuar novos dados, com o objetivo de acaba por se tornar operacionalizado como um serviço Web do Azure.

Esta conversão é feita por si, os seguintes passos:

* Converter o conjunto de módulos utilizados para formação para um módulo único e guardá-lo como um modelo preparado
* Eliminar quaisquer módulos supérfluas não relacionadas com a classificação
* Adicione as portas de entrada e de saída que irá utilizar o serviço Web eventual

Pode ser mais alterações que pretende efetuar para preparar a sua experimentação preditiva implementar como um serviço Web. Por exemplo, se pretender que o serviço Web para apenas um subconjunto dos resultados de saída, pode adicionar um módulo de filtragem antes da porta de saída.

Este processo de conversão, a experimentação de preparação não é rejeitada. Quando o processo estiver concluído, tem dois separadores no Studio: um para a experimentação de preparação e outro para a experimentação preditiva. Desta forma pode efetuar alterações para a experimentação de preparação antes de implementar o serviço Web e reconstrua a experimentação preditiva. Ou pode guardar uma cópia da experimentação de preparação para iniciar a outra linha de experimentação.

> [!NOTE]
> Ao clicar em **preditiva serviço Web** iniciar um processo automático para converter a experimentação de preparação para uma experimentação preditiva e isto funciona bem na maioria dos casos. Se a sua experimentação de preparação é complexa (por exemplo, tiver vários caminhos para formação que associa o em conjunto), poderá preferir efetuar manualmente esta conversão. Para obter mais informações, consulte [como preparar o modelo de implementação no Azure Machine Learning Studio](convert-training-experiment-to-scoring-experiment.md).
> 
> 

### <a name="the-web-service"></a>O serviço Web
Quando estiver satisfeito que sua experimentação preditiva estiver pronta, pode implementar o serviço como um serviço de Web clássico ou um serviço Web nova com base no Gestor de recursos do Azure. Para operacionalizar o seu modelo implementando-o como um *serviço Web de aprendizagem máquina clássico*, clique em **implementar serviço Web** e selecione **implementar o serviço Web [clássico]**. Para implementar como *serviço Web de aprendizagem máquina novo*, clique em **implementar serviço Web** e selecione **implementar o serviço Web do [New]**. Os utilizadores podem agora enviar dados para o modelo utilizando o REST API do serviço Web e receber os resultados de volta. Para obter mais informações, veja [How to consume an Azure Machine Learning Web service](consume-web-services.md) (Como consumir um serviço Web do Azure Machine Learning).

## <a name="the-non-typical-case-creating-a-non-predictive-web-service"></a>O cenário não típico: criar um serviço Web não preditiva
Se a sua experimentação não dar formação sobre um modelo de Análise Preditiva, em seguida, que não precisa de criar uma experimentação de preparação e de uma experimentação de classificação - existe apenas uma experimentação, e pode implementá-la como um serviço Web. O Machine Learning Studio Deteta se a sua experimentação contém um modelo preditivo ao analisar os módulos que utilizou.

Depois de ter iterated na sua experimentação e estiver satisfeito com o mesmo:

1. Clique em **segurança serviço Web** e selecione **reparametrização serviço da Web** - entrada e saída nós são adicionados automaticamente
2. Clique em **executar**
3. Clique em **implementar serviço Web** e selecione **implementar o serviço Web [clássico]** ou **implementar o serviço Web do [New]** consoante o ambiente para o qual pretende implementar.

O serviço Web é agora implementado e pode aceder e geri-lo tal como um serviço Web preditivo.

## <a name="updating-your-web-service"></a>Atualizar o seu serviço Web
Agora que transferiu a sua experimentação como um serviço Web, e se tem de atualizá-lo?

Que depende o que precisa de atualizar:

**Para alterar a entrada ou saída ou de que pretende modificar a forma como o serviço Web manipula dados**

Se não estiver a alterar o modelo, mas apenas são alterar a forma como o serviço Web processa dados, pode editar a experimentação preditiva e, em seguida, clique em **implementar serviço Web** e selecione **implementar o serviço Web [clássico]** ou **implementar o serviço Web do [New]** novamente. O serviço Web está parado, a experimentação preditiva atualizada é implementada e o serviço Web for reiniciado.

Eis um exemplo: Suponhamos sua experimentação preditiva devolve toda a linha de dados de entrada com o resultado previsto. Pode decidir que pretende que o serviço Web para devolver apenas o resultado. Para poder adicionar um **colunas do projeto** módulo na experimentação preditiva, imediatamente antes da porta de saída, a excluir colunas que não seja o resultado. Ao clicar em **implementar serviço Web** e selecione **implementar o serviço Web [clássico]** ou **implementar o serviço Web do [New]** novamente, o serviço Web é atualizado.

**Pretende reparametrização do modelo com novos dados**

Se pretender manter o seu modelo de machine learning, mas que gostaria de reparametrização dos-lo com novos dados, tem duas opções:

1. **Reparametrização do modelo enquanto o serviço Web está em execução** -se pretender reparametrização do seu modelo, enquanto o serviço Web preditivo está em execução, pode fazer isto efetuar algumas alterações para a experimentação de preparação para torná-lo um ***reparametrização experimentação***, em seguida, pode implementá-lo como um  ***web reparametrização* serviço**. Para obter instruções sobre como efetuar esta ação, consulte [Machine Learning reparametrização dos modelos programáticos](retrain-models-programmatically.md).
2. **Volte para a experimentação de preparação original e utilizar os dados de formação diferentes para desenvolver o seu modelo** - sua experimentação preditiva está ligada ao serviço Web, mas a experimentação de preparação não está diretamente ligada desta forma. Se modificar a experimentação de preparação original e clique em **segurança serviço Web**, irá criar um *novo* preditiva experimentação que, quando implementados, irá criar um *novo* serviço Web. Tal não atualiza o serviço Web original.
   
   Se necessitar de modificar a experimentação de preparação, abra-o e clique em **guardar como** que faça uma cópia. Este procedimento irá deixar a experimentação de preparação original, experimentação preditiva, intactos e serviço Web. Agora, pode criar um novo serviço Web com as suas alterações. Assim que tiver implementado o novo serviço Web, em seguida, pode decidir se pretende parar o serviço Web anterior ou manter a funcionar em conjunto com o novo.

**Pretende preparar um modelo diferente**

Se pretende efetuar alterações à sua experimentação preditiva original, por exemplo, selecionar uma algoritmo de aprendizagem diferentes tentar um método de formação diferentes, etc., em seguida, tem de seguir o segundo procedimento descrito acima para a reparametrização o seu modelo: Abra a experimentação de preparação, clique em **guardar como** para efetuar uma cópia e, em seguida, iniciar para baixo o novo caminho de desenvolver o seu modelo , criar a experimentação preditiva e implementar o serviço web. Esta ação irá criar uma novo Web service não relacionado com um original - pode decidir que um ou ambos, para manter em execução.

## <a name="next-steps"></a>Passos Seguintes
Para obter mais detalhes sobre o processo de desenvolvimento e a experimentação, consulte os artigos seguintes:

* converter a experimentação - [como preparar o modelo de implementação no Azure Machine Learning Studio](convert-training-experiment-to-scoring-experiment.md)
* implementar o serviço Web - [implementar um serviço web do Azure Machine Learning](publish-a-machine-learning-web-service.md)
* reparametrização do modelo - [Machine Learning reparametrização dos modelos programáticos](retrain-models-programmatically.md)

Para obter exemplos de todo o processo, consulte:

* [Tutorial de Machine learning: crie a sua primeira experimentação no Azure Machine Learning Studio](create-experiment.md)
* [Instruções: Desenvolver uma solução de Análise Preditiva para a avaliação de riscos de crédito no Azure Machine Learning](walkthrough-develop-predictive-solution.md)


---
title: "Passo 5: Implementar o serviço web do Machine Learning | Microsoft Docs"
description: "Passo 5 a desenvolver uma solução preditiva explicação passo a passo: implementar uma experimentação preditiva no Machine Learning Studio, como um serviço web."
services: machine-learning
documentationcenter: 
author: garyericson
manager: jhubbard
editor: cgronlun
ms.assetid: 3fca74a3-c44b-4583-a218-c14c46ee5338
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/23/2017
ms.author: garye
ms.openlocfilehash: ba8f1678d87159088c58cf0e05e0fe5a6579b358
ms.sourcegitcommit: 5a6e943718a8d2bc5babea3cd624c0557ab67bd5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/01/2017
---
# <a name="walkthrough-step-5-deploy-the-azure-machine-learning-web-service"></a>Passo 5 das Instruções: Implementar o serviço Web do Azure Machine Learning
Este é o quinto passo instruções, [desenvolver uma solução de Análise Preditiva no Azure Machine Learning](walkthrough-develop-predictive-solution.md)

1. [Criar uma área de trabalho do Machine Learning](walkthrough-1-create-ml-workspace.md)
2. [Carregar os dados existentes](walkthrough-2-upload-data.md)
3. [Criar uma nova experimentação](walkthrough-3-create-new-experiment.md)
4. [Dar formação e avaliar os modelos](walkthrough-4-train-and-evaluate-models.md)
5. **Implementar o serviço web**
6. [Aceder ao serviço web](walkthrough-6-access-web-service.md)

- - -
Para atribuir a outras pessoas a possibilidade de utilizar o modelo preditivo que iremos tiver desenvolvidas esta explicação passo a passo, vamos pode implementá-lo como um serviço web no Azure.

Até este ponto foi foi que tiver conseguirmos uma com o nosso modelo de preparação. Mas o serviço implementado já não vai fazer formação - vai gerar novo predições através da classificação de entrada do utilizador com base no nosso modelo. Para que vamos fazer alguma preparação para converter esta fase experimental de um ***formação*** experimentação para um ***preditiva*** experimentação. 

Este é um processo de três passos:  

1. Remova um dos modelos
2. Converter o *experimentação de preparação* criámos para um *experimentação preditiva*
3. Implementar a experimentação preditiva como um serviço web

## <a name="remove-one-of-the-models"></a>Remova um dos modelos

Em primeiro lugar, temos de corte esta fase experimental para baixo um pouco. Atualmente temos dois modelos diferentes na experimentação, mas apenas queremos utilizar um modelo ao iremos implementá-la como um serviço web.  

Imaginemos que decidido que o modelo de árvore elevada efetuadas melhor do que o modelo SVM. Para a primeira coisa a fazer é a remover o [máquina de Vetor com suporte de duas classes] [ two-class-support-vector-machine] módulo e os módulos que foram utilizados para formação-lo. Poderá pretender fazer uma cópia da experimentação primeiro clicando **guardar como** na parte inferior da tela de experimentação.

É necessário eliminar os seguintes módulos:  

* [Máquina de Vetor com suporte de classe dois][two-class-support-vector-machine]
* [Preparar o modelo] [ train-model] e [Pontuar modelo] [ score-model] módulos que foram ligados ao mesmo
* [Normaliza dados] [ normalize-data] (ambos os parâmetros)
* [Avaliar modelo] [ evaluate-model] (porque está tudo concluídos avaliar os modelos de)

Selecionar cada módulo e prima a tecla de eliminação, ou o módulo com o botão direito e selecione **eliminar**. 

![Remover o modelo SVM][3a]

Nosso modelo deverá agora parecer-algo semelhante ao seguinte:

![Remover o modelo SVM][3]

Agora está tudo prontos implementar este modelo utilizando o [árvore de decisões elevada de duas classes][two-class-boosted-decision-tree].

## <a name="convert-the-training-experiment-to-a-predictive-experiment"></a>Converter a experimentação de preparação para uma experimentação preditiva

Para obter este modelo preparado para a implementação, é necessário converter esta experimentação de preparação para uma experimentação preditiva. Isto envolve três passos:

1. Guarde o modelo que tenha preparado e, em seguida, substitua o nosso módulos de formação
2. Corte a experimentação para remover módulos só eram necessários para formação
3. Definir onde o serviço web irá aceitar entrada e onde gera a saída

Iremos pode fazê-lo manualmente, mas Felizmente todos os três passos podem ser realizados clicando **segurança serviço Web** na parte inferior da tela de experimentação (e selecionando a **preditiva serviço Web** opção).

> [!TIP]
> Se quiser obter mais detalhes sobre o que acontece quando converter uma experimentação de preparação para uma experimentação preditiva, consulte [como preparar o modelo de implementação no Azure Machine Learning Studio](convert-training-experiment-to-scoring-experiment.md).

Ao clicar em **segurança serviço Web**, acontecer vários aspetos:

* O modelo treinado é convertido para um único **modelo treinado** módulo e armazenadas na paleta do módulo para a esquerda da tela de experimentação (pode encontrá-lo na **modelos de formação**)
* Módulos que foram utilizados para formação são removidos; especificamente:
  * [Árvore de decisões elevada de duas classes][two-class-boosted-decision-tree]
  * [Preparar modelo][train-model]
  * [Dividir dados][split]
  * o segundo [executar Script do R] [ execute-r-script] módulo que foi utilizado para dados de teste
* O modelo treinado guardado é adicionado novamente a experimentação
* **Entrada de serviço de Web** e **Web saída de serviço** módulos são adicionados (estes identificam onde os dados do utilizador irão introduzir o modelo e os dados que são devolvidos quando o serviço web é acedido)

> [!NOTE]
> Pode ver que a experimentação é guardada em duas partes dentro de separadores que foram adicionados na parte superior da tela de experimentação. A experimentação de preparação original é no separador **experimentação de preparação**, sendo a experimentação preditiva recém-criado em **experimentação preditiva**. A experimentação preditiva é o que irá implementar como um serviço web.

É necessário efetuar um passo adicional com esta fase experimental específico.
Adicionámos dois [executar Script do R] [ execute-r-script] módulos para fornecer uma função de peso aos dados. Que foi apenas um truque que precisávamos de formação e teste, pelo que iremos pode demorar saída esses módulos no modelo final.
O Machine Learning Studio removido um [executar Script do R] [ execute-r-script] módulo quando este removido o [divisão] [ split] módulo. Agora, pode remover o outro e ligar [metadados Editor] [ metadata-editor] diretamente para [Pontuar modelo][score-model].    

A nossa experiência deve agora ter o seguinte aspeto:  

![O modelo treinado da classificação][4]  

> [!NOTE]
> -Pode estar a pensar por que motivo é restantes o conjunto de dados de dados de cartão de crédito UCI alemão na experimentação preditiva. O serviço vai Pontuar os dados do utilizador, não o conjunto de dados original, por isso deixe por que motivo o conjunto de dados original no modelo?
> 
> É verdadeiro se o serviço não tem dos dados originais do cartão de crédito. Mas é necessário o esquema para os dados, que inclui informações como o número de colunas existem e as colunas são numéricas. Estas informações de esquema são necessárias para interpretar os dados do utilizador. Iremos deixar estes componentes ligados para que o módulo de pontuação tem o esquema de conjunto de dados quando o serviço está em execução. Os dados não estão a ser utilizados, apenas o esquema.  
> 
> 

Execute a experimentação uma última vez (clique **executar**.) Se pretender verificar que o modelo ainda está a funcionar, clique no resultado a [Pontuar modelo] [ score-model] módulo e selecione **ver resultados**. Pode ver que os dados originais são apresentados, juntamente com o valor de risco de crédito ("etiquetas classificadas") e o valor de probabilidade de classificação ("classificada probabilidades".) 

## <a name="deploy-the-web-service"></a>Implementar o serviço web
Pode implementar a experimentação, como a um serviço web clássico ou como um novo serviço web com base no Gestor de recursos do Azure.

### <a name="deploy-as-a-classic-web-service"></a>Implementar como um serviço de web clássico
Para implementar um serviço de web clássico derivado do nosso experimentação, clique em **implementar serviço Web** abaixo a tela e selecione **implementar o serviço Web [clássico]**. O Machine Learning Studio implementa a experimentação como um serviço web e leva-o para o dashboard para que o serviço web. Nesta página pode regressar à experimentação (**instantâneo de vista** ou **ver mais recente**) e executar um teste simple do serviço web (consulte **testar o serviço web** abaixo). Também é informações para criar aplicações que podem aceder ao serviço web (mais informações sobre as que o passo seguinte destas instruções).

![Dashboard de serviço Web][6]

Pode configurar o serviço clicando a **configuração** separador. Aqui pode modificar o nome do serviço (que forneceu o nome da experimentação por predefinição) e atribua-lhe uma descrição. Também pode dar mais etiquetas amigáveis para os dados de entrada e de saída.  

![Configurar o serviço web][5]  

### <a name="deploy-as-a-new-web-service"></a>Implementar como um novo serviço web

> [!NOTE] 
> Para implementar um novo serviço web tem de ter permissões suficientes na subscrição que pretende implementar o serviço web. Para obter mais informações, consulte [gira um serviço web utilizando o portal de serviços Web do Azure Machine Learning](manage-new-webservice.md). 

Para implementar um novo serviço web derivado da nossa experiência:

1. Clique em **implementar serviço Web** abaixo a tela e selecione **implementar o serviço Web do [New]**. O Machine Learning Studio transfere para os serviços web do Azure Machine Learning **implementar experimentação** página.

2. Introduza um nome para o serviço web. 

3. Para **plano de preços**, pode selecionar um plano de preços existente, ou selecione "Novo criar" e atribua um nome de novo plano e selecione a opção plano mensal. A predefinição de camadas de plano para os planos para sua região predefinida e o seu serviço web é implementada nessa região.

4. Clique em **implementar**.

Após alguns minutos, o **início rápido** abre a página para o seu serviço web.

Pode configurar o serviço clicando a **configurar** separador. Aqui pode modificar o serviço title e atribua-lhe uma descrição. 

Para testar o serviço web, clique em de **testar** separador (consulte **testar o serviço web** abaixo). Para obter informações sobre como criar aplicações que podem aceder ao serviço web, clique o **Consume** separador (que o passo seguinte nestas instruções serão enviadas para obter mais detalhes).

> [!TIP]
> Pode atualizar o serviço web após implementá-lo. Por exemplo, se pretender alterar o seu modelo, em seguida, pode editar a experimentação de preparação, otimizar os parâmetros de modelo e clique em **implementar serviço Web**, selecionando **implementar o serviço Web [clássico]** ou **Implementar serviço Web do [New]**. Quando implementar novamente a experimentação, substitui o serviço web, utilizando agora o seu modelo atualizado.  
> 
> 

## <a name="test-the-web-service"></a>Testar o serviço web

Quando o serviço web é acedido, os dados do utilizador introduz através de **Web entrada de serviço** módulo onde é passado para o [Pontuar modelo] [ score-model] módulo e pontuadas. A forma que configurámos a experimentação preditiva, do modelo espera dados no mesmo formato como o conjunto de dados de risco de crédito original.
Os resultados são devolvidos para o utilizador do serviço web através de **Web saída de serviço** módulo.

> [!TIP]
> A forma temos a experimentação preditiva configurada, todo o resulta do [Pontuar modelo] [ score-model] módulo são devolvidos. Isto inclui todos os dados de entrada e o valor de risco de crédito e a probabilidade de classificação. Mas pode devolver algo diferente, se quiser; por exemplo, pode devolver apenas o valor de risco de crédito. Para tal, insira um [colunas do projeto] [ project-columns] módulo entre [Pontuar modelo] [ score-model] e **Web saída de serviço**para eliminar colunas não pretende que o serviço web para devolver. 
> 
> 

Pode testar uma web clássico encontra-se num serviço **Machine Learning Studio** ou no **serviços Web do Azure Machine Learning** portal.
Pode testar uma nova web service apenas no **serviços Web Machine Learning** portal.

> [!TIP]
> Quando o teste no portal de serviços Web do Azure Machine Learning, pode fazer com o portal criar dados de exemplo que pode utilizar para testar o serviço de resposta-pedido. No **configurar** página, selecione "Sim" para **ativada de dados de exemplo?**. Ao abrir o separador de resposta-pedido no **teste** página, o portal preenche nos dados de exemplo obtidos a partir do conjunto de dados de risco de crédito original.

### <a name="test-a-classic-web-service"></a>Testar um serviço de web clássico

Pode testar um serviço de web clássico no Machine Learning Studio ou no portal de serviços Web Machine Learning. 

#### <a name="test-in-machine-learning-studio"></a>Teste no Machine Learning Studio

1. No **DASHBOARD** página para o serviço web, clique em de **teste** botão em **ponto final predefinido**. Uma caixa de diálogo aparece e pede-lhe para os dados de entrada para o serviço. Estas são as mesmas colunas que apareceu no conjunto de dados de risco de crédito original.  

2. Introduza um conjunto de dados e, em seguida, clique em **OK**. 

#### <a name="test-in-the-machine-learning-web-services-portal"></a>Teste no portal de serviços Web Machine Learning

1. No **DASHBOARD** página para o serviço web, clique em de **pré-visualização do teste** ligação em **ponto final predefinido**. A página de teste no portal de serviços Web do Azure Machine Learning para o ponto de final de serviço web abre e pede-lhe para os dados de entrada para o serviço. Estas são as mesmas colunas que apareceu no conjunto de dados de risco de crédito original.

2. Clique em **testar pedido-resposta**. 

### <a name="test-a-new-web-service"></a>Testar um novo serviço web

Pode testar um novo serviço web apenas no portal de serviços Web Machine Learning.

1. No [serviços Web do Azure Machine Learning](https://services.azureml.net/quickstart) portal, clique em **teste** na parte superior da página. O **teste** página abre e pode introduzir dados para o serviço. Os campos de entrada apresentados correspondem às colunas que antes eram no conjunto de dados de risco crédito original. 

2. Introduza um conjunto de dados e, em seguida, clique em **teste de resposta-pedido**.

São apresentados os resultados do teste no lado direito da página da coluna de saída. 


## <a name="manage-the-web-service"></a>Gerir o serviço web

Depois de implementar o serviço web, se clássico ou novo, pode geri-lo do [serviços Web do Microsoft Azure Machine Learning](https://services.azureml.net/quickstart) portal.

Para monitorizar o desempenho do seu serviço web:

1. Iniciar sessão para o [serviços Web do Microsoft Azure Machine Learning](https://services.azureml.net/quickstart) portal
2. Clique em **serviços Web**
3. Clique em seu serviço web
4. Clique em de **Dashboard**

- - -
**Seguinte: [aceder ao serviço web](walkthrough-6-access-web-service.md)**

[3]: ./media/walkthrough-5-publish-web-service/publish3.png
[3a]: ./media/walkthrough-5-publish-web-service/publish3a.png
[4]: ./media/walkthrough-5-publish-web-service/publish4.png
[5]: ./media/walkthrough-5-publish-web-service/publish5.png
[6]: ./media/walkthrough-5-publish-web-service/publish6.png


<!-- Module References -->
[evaluate-model]: https://msdn.microsoft.com/library/azure/927d65ac-3b50-4694-9903-20f6c1672089/
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[metadata-editor]: https://msdn.microsoft.com/library/azure/370b6676-c11c-486f-bf73-35349f842a66/
[normalize-data]: https://msdn.microsoft.com/library/azure/986df333-6748-4b85-923d-871df70d6aaf/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
[two-class-boosted-decision-tree]: https://msdn.microsoft.com/library/azure/e3c522f8-53d9-4829-8ea4-5c6a6b75330c/
[two-class-support-vector-machine]: https://msdn.microsoft.com/library/azure/12d8479b-74b4-4e67-b8de-d32867380e20/
[project-columns]: https://msdn.microsoft.com/en-us/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/

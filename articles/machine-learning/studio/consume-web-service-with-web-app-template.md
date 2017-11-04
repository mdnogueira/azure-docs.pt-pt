---
title: "Consumir um serviço web do Machine Learning com um modelo de aplicação web | Microsoft Docs"
description: "Utilize um modelo de aplicação web no Azure Marketplace para consumir um serviço web preditiva no Azure Machine Learning."
keywords: "serviço Web, operationalization, REST API, machine learning"
services: machine-learning
documentationcenter: 
author: garyericson
manager: jhubbard
editor: cgronlun
ms.assetid: e0d71683-61b9-4675-8df5-09ddc2f0d92d
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2017
ms.author: garye;raymondl
ms.openlocfilehash: 1c182403409966923440f359cb2514af7b7df9f3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="consume-an-azure-machine-learning-web-service-with-a-web-app-template"></a>Consumir um serviço Web do Azure Machine Learning com um modelo de aplicação Web

Uma vez que já desenvolveu o seu modelo preditivo e implementado como um serviço web do Azure utilizando o Machine Learning Studio, ou utilizando ferramentas como R ou Python, pode aceder ao modelo de operacionalizado utilizando uma API REST.

Existem várias formas de utilizar a API REST e aceder ao serviço web. Por exemplo, pode escrever uma aplicação em c#, R ou Python com o código de exemplo gerado quando implementou o serviço web (disponível no [Portal de serviços Web do Machine Learning](https://services.azureml.net/quickstart) ou no dashboard do serviço web no Machine Learning Studio). Ou pode utilizar o livro do Microsoft Excel de exemplo criado para si ao mesmo tempo.

Mas é a forma mais rápida e mais fácil de aceder ao seu serviço web através de modelos de aplicação Web disponíveis no [Azure Marketplace de aplicação Web](https://azure.microsoft.com/marketplace/web-applications/all/).

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="the-azure-machine-learning-web-app-templates"></a>A modelos de aplicação Web de aprendizagem do Azure
Modelos de aplicação web disponíveis no Azure Marketplace, podem criar uma aplicação web personalizada que sabe o seu serviço web dados de entrada e resultados esperados. Tudo o que precisa de fazer é permitir o acesso de aplicação web ao seu serviço web e os dados, e o modelo não o resto.

Estão disponíveis dois modelos:

* [Modelo de aplicação do Azure ML resposta-pedido serviço Web](https://azure.microsoft.com/marketplace/partners/microsoft/azuremlaspnettemplateforrrs/)
* [Modelo de aplicação de Web de serviço de execução de lote do Azure ML](https://azure.microsoft.com/marketplace/partners/microsoft/azuremlbeswebapptemplate/)

Cada modelo cria uma aplicação de ASP.NET de exemplo, utilizando o URI de API e a chave para o seu serviço web e implementa-o como um web site do Azure. O modelo de serviço de resposta-pedido (RRS) cria uma aplicação web que lhe permite enviar uma única linha de dados para o serviço web para obter um resultado único. O modelo de serviço de execução de lote (BES) cria uma aplicação web que lhe permite enviar o número de linhas de dados para obter resultados vários.

Sem codificação é necessária para utilizar estes modelos. Basta fornecer a chave de API e o URI e o modelo baseia-se a aplicação por si.

Para obter a chave de API e o URL do pedido para um serviço web:

1. No [Portal de serviços Web](https://services.azureml.net/quickstart), para um novo serviço web, clique em **serviços Web** na parte superior. Ou de um clique de serviço web clássico **serviços Web clássicos**.
2. Clique no serviço de web que pretende aceder.
3. Para um serviço web clássico, clique no ponto final que pretende aceder.
4. Clique em **Consume** na parte superior.
5. Copiar o **primário** ou **chave secundária** e guardá-lo.
6. Se estiver a criar um modelo de serviço de resposta-pedido (RRS), copie o **pedido-resposta** URI e guardá-lo. Se estiver a criar um modelo de serviço de execução de lote (BES), copie o **pedidos de Batch** URI e guardá-lo.


## <a name="how-to-use-the-request-response-service-rrs-template"></a>Como utilizar o modelo de serviço de resposta-pedido (RRS)
Siga estes passos para utilizar o modelo de aplicação de web RRS, conforme mostrado no diagrama seguinte.

![Processo para utilizar o modelo de web RRS][image1]


<!--    ![API Key][image3] -->

<!-- This value will look like this:
   
        https://ussouthcentral.services.azureml.net/workspaces/<workspace-id>/services/<service-id>/execute?api-version=2.0&details=true
   
    ![Request URI][image4] -->

1. Aceda ao [portal do Azure](https://portal.azure.com), **início de sessão**, clique em **novo**, procure e selecione **do Azure ML resposta-pedido Web do App Service**, em seguida, clique em **criar**. 
   
   * Dê um nome exclusivo da aplicação web. O URL da aplicação web será este nome seguido `.azurewebsites.net.` por exemplo,`http://carprediction.azurewebsites.net.`
   * Selecione a subscrição do Azure e serviços em que o seu serviço web está em execução.
   * Clique em **Criar**.
     
     ![Criar aplicação Web][image5]

4. Quando o Azure foi concluída a implementação da aplicação web, clique em de **URL** nas definições da aplicação web página no Azure, ou introduza o URL num browser. Por exemplo, `http://carprediction.azurewebsites.net.`
5. Quando a aplicação web é executada pela primeira vez, irá pedir-lhe o **API Post URL** e **chave de API**.
   Introduza os valores que guardou anteriormente (**URI pedido** e **chave de API**, respetivamente).
     
     Clique em **submeter**.
     
     ![Introduza o Post URI e chave de API][image6]

6. A aplicação de web apresenta o **configuração de aplicação Web** página com as definições do serviço web atual. Aqui pode efetuar alterações às definições utilizadas pela aplicação web.
   
   > [!NOTE]
   > Alterar as definições aqui apenas altera-los para esta aplicação web. Não altera as definições predefinidas do seu serviço web. Por exemplo, se alterar o **Descrição** aqui não altera a descrição a apresentar no dashboard do serviço web no Machine Learning Studio.
   > 
   > 
   
    Quando tiver terminado, clique em **guardar alterações**e, em seguida, clique em **aceda à Home Page**.

7. Na home page pode introduzir valores para enviar ao seu serviço web. Clique em **submeter** quando tiver terminado, e o resultado será devolvido.

Se pretender regressar ao **configuração** página, vá para o `setting.aspx` página da aplicação web. Por exemplo: `http://carprediction.azurewebsites.net/setting.aspx.` será solicitado para introduzir novamente a chave de API - terá que para aceder à página e atualizar as definições.

Pode parar, reiniciar ou eliminar a aplicação web no portal do Azure como qualquer outra aplicação web. Enquanto estiver em execução pode navegar para o endereço web de raiz e introduza novos valores.

## <a name="how-to-use-the-batch-execution-service-bes-template"></a>Como utilizar o modelo de serviço de execução de lote (BES)
Pode utilizar o modelo de aplicação web BES da mesma forma que o modelo RRS, exceto que a aplicação web que é criada permitirá submeter várias linhas de dados e receber os resultados de múltiplos.

Os valores de entrada para um serviço de web de execução do batch podem provenientes de armazenamento do Azure ou um ficheiro local; os resultados são armazenados no contentor de armazenamento do Azure.
Por isso, terá de um contentor de armazenamento do Azure para armazenar os resultados devolvidos pela aplicação web, e terá de preparar os dados de entrada.

![Processo para utilizar BES web modelo][image2]

1. Siga o mesmo procedimento para criar a aplicação de web BES para o modelo RRS, exceto aceda ao [modelo de aplicação ao Web do serviço de execução do Azure ML Batch](https://azure.microsoft.com/marketplace/partners/microsoft/azuremlbeswebapptemplate/) para abrir o modelo de BES no Azure Marketplace e clique em **Criar aplicação de Web**.

2. Para especificar onde pretende que os resultados armazenados, introduza as informações de contentor de destino na home page da aplicação web. Também pode especificar onde a aplicação web pode obter os valores de entrada, a um ficheiro local ou um contentor de armazenamento do Azure.
   Clique em **submeter**.
   
    ![Informações de armazenamento][image7]

A aplicação web apresentará uma página com o estado da tarefa.
Quando concluir a tarefa irá ser fornecido a localização dos resultados no armazenamento de Blobs do Azure. Também tem a opção de transferir os resultados num ficheiro local.

## <a name="for-more-information"></a>Para obter mais informações
Para saber mais sobre...

* criar uma experimentação de aprendizagem com Machine Learning Studio, consulte [criar a sua primeira experimentação no Azure Machine Learning Studio](create-experiment.md)
* como implementar a sua experimentação de aprendizagem como um serviço web, consulte [implementar um serviço web do Azure Machine Learning](publish-a-machine-learning-web-service.md)
* outras formas de aceder ao seu serviço web, consulte [como consumir um serviço Web do Azure Machine Learning](consume-web-services.md)

[image1]: media/consume-web-service-with-web-app-template/rrs-web-template-flow.png
[image2]: media/consume-web-service-with-web-app-template/bes-web-template-flow.png
[image3]: media/consume-web-service-with-web-app-template/api-key.png
[image4]: media/consume-web-service-with-web-app-template/post-uri.png
[image5]: media/consume-web-service-with-web-app-template/create-web-app.png
[image6]: media/consume-web-service-with-web-app-template/web-service-info.png
[image7]: media/consume-web-service-with-web-app-template/storage.png

---
title: "Implementar um serviço web do Machine Learning | Microsoft Docs"
description: "Como converter uma experimentação de preparação para uma experimentação preditiva, preparar para a implementação, em seguida, implementá-lo como um serviço web Azure Machine Learning."
services: machine-learning
documentationcenter: 
author: garyericson
manager: jhubbard
editor: cgronlun
ms.assetid: 73a3e9c6-00d0-41d4-8cf1-2ec87713867e
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/06/2017
ms.author: garye
ms.openlocfilehash: 75577ad318f2ff23a7b7d10cf551f3bced56fb62
ms.sourcegitcommit: 5a6e943718a8d2bc5babea3cd624c0557ab67bd5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/01/2017
---
# <a name="deploy-an-azure-machine-learning-web-service"></a>Implementar um serviço Web do Azure Machine Learning
O Azure Machine Learning permite-lhe criar, testar e implementar soluções de Análise Preditiva.

A partir de uma ponto de-de-vista de alto nível, este procedimento é efetuado três passos:

* **[Criar uma experimentação de preparação]**  -Azure Machine Learning Studio é um ambiente de desenvolvimento de visual de colaboração que utilizar para formação e testar um modelo de Análise Preditiva utilizando os dados de formação que forneceu.
* **[Convertê-lo para uma experimentação preditiva]**  -depois do seu modelo tem sido preparado com a dados existentes e estará pronto para utilizá-lo para pontuar novos dados, preparar e simplificar a sua experimentação para as predições.
* **[Implementá-lo como um serviço web]**  -pode implementar a sua experimentação preditiva como um [novo] ou [clássico] serviço web do Azure. Os utilizadores podem enviar dados para o seu modelo e receber predições do seu modelo.

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="create-a-training-experiment"></a>Criar uma experimentação de preparação
Para preparar um modelo de Análise Preditiva, pode utilizar Azure Machine Learning Studio para criar uma experimentação de preparação onde inclui vários módulos a carregar dados de formação, preparar os dados conforme necessário, aplicar algoritmos de machine learning e avaliar os resultados. Pode repetir uma experimentação e tentar algoritmos de aprendizagem diferentes para comparar e avaliar os resultados.

O processo de criação e gestão de experimentações de formação é descrito mais exaustivamente noutro local. Para obter mais informações, consulte estes artigos:

* [Criar uma experimentação simple no Azure Machine Learning Studio](create-experiment.md)
* [Desenvolver uma solução Preditiva com o Azure Machine Learning](walkthrough-develop-predictive-solution.md)
* [Importar os dados de formação para o Azure Machine Learning Studio](import-data.md)
* [Gerir iterações de experimentação no Azure Machine Learning Studio](manage-experiment-iterations.md)

## <a name="convert-the-training-experiment-to-a-predictive-experiment"></a>Converter a experimentação de preparação para uma experimentação preditiva
Depois de ter preparado o seu modelo, está pronto para converter a experimentação de preparação para uma experimentação preditiva para pontuar novos dados.

Por converter para uma experimentação preditiva, a obter o modelo treinado pronto para ser implementado como um serviço web de classificação. Os utilizadores do serviço web podem enviar dados de entrada para o seu modelo e o seu modelo irá enviar novamente os resultados de predição. Como converter uma experimentação preditiva, tenha em atenção como espera que o seu modelo a ser utilizada por outras pessoas.

Para converter a experimentação de preparação para uma experimentação preditiva, clique em **executar** na parte inferior da tela de experimentação, clique em **segurança serviço Web**, em seguida, selecione **preditiva serviço Web**.

![Converter a experimentação da classificação](./media/publish-a-machine-learning-web-service/figure-1.png)

Para obter mais informações sobre como efetuar esta conversão, consulte [como preparar o modelo de implementação no Azure Machine Learning Studio](convert-training-experiment-to-scoring-experiment.md).

Os passos seguintes descrevem a implementar uma experimentação preditiva como um novo serviço web. Também pode implementar a experimentação como um serviço de web clássico.

## <a name="deploy-it-as-a-web-service"></a>Implementá-lo como um serviço web

Pode implementar a experimentação preditiva como um novo serviço web ou como um serviço de web clássico.

### <a name="deploy-the-predictive-experiment-as-a-new-web-service"></a>Implementar a experimentação preditiva como um novo serviço web
Agora que tenha sido preparada a experimentação preditiva, pode implementá-lo como um novo serviço web do Azure. Utilizando o serviço web, os utilizadores podem enviar dados para o seu modelo e o modelo irá devolver as predições.

Para implementar a sua experimentação preditiva, clique em **executar** na parte inferior da tela de experimentação. Depois da experimentação ter terminado, clique em **implementar serviço Web** e selecione **implementar serviço Web [novo]**.  Abre a página de implementação do portal do serviço Web do Machine Learning.

> [!NOTE] 
> Para implementar um novo serviço web tem de ter permissões suficientes na subscrição aos quais pode implementar o serviço web. Para obter mais informações, consulte [gira um serviço Web utilizando o portal de serviços Web do Azure Machine Learning](manage-new-webservice.md). 

#### <a name="machine-learning-web-service-portal-deploy-experiment-page"></a>Página de experimentação de implementar do portal de serviço Web de aprendizagem máquina
Na página de experimentação implementar, introduza um nome para o serviço web.
Selecione um plano de preços. Se tiver um plano de preços existente, que pode selecioná-lo, caso contrário, terá de criar um novo plano de preços para o serviço.

1. No **plano de preços** pendente, selecione um plano existente ou selecione o **selecione novo plano** opção.
2. No **nome do plano**, escreva um nome que identifique o plano na sua factura.
3. Selecione uma do **mensal camadas planear**. A predefinição de camadas de plano para os planos para sua região predefinida e o seu serviço web é implementada nessa região.

Clique em **implementar** e **início rápido** abre a página para o seu serviço web.

A página de início rápido do serviço web dá-lhe acesso e as orientações sobre as tarefas mais comuns que irá efetuar depois de criar um serviço web. Aqui, pode aceder facilmente a página de teste e a página de Consume.

<!-- ![Deploy the web service](./media/publish-a-machine-learning-web-service/figure-2.png)-->

#### <a name="test-your-new-web-service"></a>Testar o novo serviço web
Para testar o novo serviço web, clique em **testar o serviço web** em tarefas comuns. Na página de teste, pode testar o seu serviço web como um serviço de resposta-pedido (RRS) ou um serviço de execução de lote (BES).

A página de teste RRS apresenta as entradas, saídas e quaisquer parâmetros globais que foram definidos para a experimentação. Para testar o serviço web, pode manualmente introduzir os valores adequados para as entradas ou forneça um separados por vírgulas (CSV) formatado ficheiro que contém os valores de teste.

Para testar com RRS, do modo de vista de lista, introduza os valores adequados para as entradas e clique em **testar resposta-pedido**. Apresentam os resultados de predição na coluna de saída à esquerda.

![Implementar o serviço web](./media/publish-a-machine-learning-web-service/figure-5-test-request-response.png)

Para testar a sua BES, clique em **Batch**. Na página de teste do Batch, clique em Procurar sob a entrada e selecione um ficheiro CSV que contém valores de exemplo adequado. Se não tiver um ficheiro CSV e que criou a sua experimentação preditiva utilizando o Machine Learning Studio, pode transferir o conjunto de dados para a sua experimentação preditiva e utilizá-lo.

Para transferir o conjunto de dados, abra o Machine Learning Studio. Abra a sua experimentação preditiva e clique com o botão direito do rato na entrada para a sua experimentação. No menu de contexto, selecione **dataset** e, em seguida, selecione **transferir**.

![Implementar o serviço web](./media/publish-a-machine-learning-web-service/figure-7-mls-download.png)

Clique em **teste**. Apresenta o estado da tarefa de execução de lote à direita em **tarefas de lote de teste**.

![Implementar o serviço web](./media/publish-a-machine-learning-web-service/figure-6-test-batch-execution.png)

<!--![Test the web service](./media/publish-a-machine-learning-web-service/figure-3.png)-->

No **configuração** página, pode alterar a descrição, o título, atualizar a chave de conta de armazenamento e ativar os dados de exemplo para o seu serviço web.

![Configurar o serviço web](./media/publish-a-machine-learning-web-service/figure-8-arm-configure.png)

Depois de implementar o serviço web, pode:

* **Acesso** -lo através da API do serviço web.
* **Gerir** -lo através do portal de serviços web do Azure Machine Learning.
* **Atualização** -lo se o seu modelo for alterada.

#### <a name="access-your-new-web-service"></a>Aceder ao seu novo serviço web
Depois de implementar o serviço web do Machine Learning Studio, pode enviar dados para o serviço e receber respostas através de programação.

O **Consume** página fornece todas as informações necessárias para aceder ao seu serviço web. Por exemplo, a chave de API é fornecida para permitir acesso autorizado ao serviço.

Para obter mais informações sobre como aceder a um serviço web do Machine Learning, consulte [como consumir um serviço Web do Azure Machine Learning](consume-web-services.md).

#### <a name="manage-your-new-web-service"></a>Gerir o seu novo serviço web
Pode gerir o novo portal de serviços Web Machine Learning de serviços web. Do [página de portal principal](https://services.azureml-test.net/), clique em **serviços Web**. Página de serviços web, pode eliminar ou copie um serviço. Para monitorizar um serviço específico, clique no serviço e, em seguida, clique em **Dashboard**. Para monitorizar tarefas de lote associadas ao serviço web, clique em **registo de pedido de Batch**.

### <a name="deploy-the-predictive-experiment-as-a-classic-web-service"></a>Implementar a experimentação preditiva como um serviço de web clássico

Agora que tenha sido preparada suficientemente a experimentação preditiva, pode implementá-lo como um serviço web do Azure clássico. Utilizando o serviço web, os utilizadores podem enviar dados para o seu modelo e o modelo irá devolver as predições.

Para implementar a sua experimentação preditiva, clique em **executar** na parte inferior da experimentação da tela e, em seguida, clique em **implementar serviço Web**. O serviço web está configurada e são colocados no dashboard do serviço web.

![Implementar o serviço web](./media/publish-a-machine-learning-web-service/figure-2.png)

#### <a name="test-your-classic-web-service"></a>Testar o seu serviço de web clássico

Pode testar o serviço web no portal de serviços Web Machine Learning ou Machine Learning Studio.

Para testar o serviço web de resposta a um pedido, clique em de **testar** botão no dashboard do serviço web. Uma caixa de diálogo que aparece para pedir-lhe para os dados de entrada para o serviço. Estas são as colunas esperadas pela experimentação de classificação. Introduza um conjunto de dados e, em seguida, clique em **OK**. Os resultados gerados pelo serviço web são apresentados na parte inferior do dashboard.

Pode clicar no **testar** ligação de pré-visualização para testar o serviço no portal de serviços Web do Azure Machine Learning, conforme mostrado anteriormente na secção de serviço web novas.

Para testar o serviço de execução do Batch, clique em **testar** ligação de pré-visualização. Na página de teste do Batch, clique em Procurar sob a entrada e selecione um ficheiro CSV que contém valores de exemplo adequado. Se não tiver um ficheiro CSV e que criou a sua experimentação preditiva utilizando o Machine Learning Studio, pode transferir o conjunto de dados para a sua experimentação preditiva e utilizá-lo.

![Testar o serviço web](./media/publish-a-machine-learning-web-service/figure-3.png)

No **configuração** página, pode alterar o nome a apresentar do serviço e atribua-lhe uma descrição. O nome e descrição é apresentada no [portal do Azure](https://portal.azure.com/) onde irá gerir os seus serviços web.

Pode fornecer uma descrição para os dados de entrada, dados de saída e web parâmetros de serviço ao introduzir uma cadeia para cada coluna em **esquema da entrada**, **esquema de saída**, e **parâmetro de serviço Web**. Estas descrições são utilizadas na documentação do código de exemplo fornecida para o serviço web.

Pode ativar o registo diagnosticar eventuais falhas que está a ver quando o serviço web é acedido. Para obter mais informações, consulte [ativar o registo de serviços web do Machine Learning](web-services-logging.md).

![Configurar o serviço web](./media/publish-a-machine-learning-web-service/figure-4.png)

Também pode configurar os pontos finais para o serviço web no portal de serviços Web do Azure Machine Learning semelhante para o procedimento apresentado anteriormente na secção de serviço web novas. As opções são diferentes, pode adicionar ou alterar a descrição do serviço, ative o registo e ativar os dados de exemplo para fins de teste.

#### <a name="access-your-classic-web-service"></a>Aceder ao seu serviço web clássico
Depois de implementar o serviço web do Machine Learning Studio, pode enviar dados para o serviço e receber respostas através de programação.

O dashboard fornece todas as informações necessárias para aceder ao seu serviço web. Por exemplo, a chave de API é fornecida para permitir acesso autorizado ao serviço e páginas de ajuda de API são fornecidas para ajudar a começar a escrever o seu código.

Para obter mais informações sobre como aceder a um serviço web do Machine Learning, consulte [como consumir um serviço Web do Azure Machine Learning](consume-web-services.md).

#### <a name="manage-your-classic-web-service"></a>Gerir o serviço de web clássico
Existem várias ações que pode efetuar para monitorizar um serviço web. Pode atualizá-lo e eliminá-lo. Também pode adicionar os pontos finais adicionais a um serviço web do clássico para além do ponto final predefinido que é criado quando a implementá-la.

Para obter mais informações, consulte [gerir uma área de trabalho do Azure Machine Learning](manage-workspace.md) e [gira um serviço web utilizando o portal de serviços Web do Azure Machine Learning](manage-new-webservice.md).

<!-- When this article gets published, fix the link and uncomment
For more information on how to manage Azure Machine Learning web service endpoints using the REST API, see **Azure machine learning web service endpoints**.
-->

## <a name="update-the-web-service"></a>Atualizar o serviço web
Pode efetuar alterações ao seu serviço web, tais como a atualização do modelo com dados de formação adicionais e implementá-la novamente, substituindo o serviço web original.

Para atualizar o serviço web, abra a experimentação preditiva original utilizada para implementar o serviço web e fazer uma cópia editável clicando **guardar como**. Efetue as alterações e, em seguida, clique em **implementar serviço Web**.

Porque tiver implementado esta fase experimental antes, é-lhe perguntado se pretende substituir (serviço Web clássico) ou atualizar (novo serviço web) o serviço existente. Ao clicar em **Sim** ou **atualização** interrompe o serviço web existente e implementa a nova experimentação preditiva é implementada no seu lugar.

> [!NOTE]
> Se tiver efetuado alterações de configuração no serviço web original, por exemplo, introduzir um novo nome a apresentar ou a descrição, terá de introduzir novamente esses valores.
> 
> 

Uma opção para atualizar o seu serviço web é reparametrização do modelo de programação. Para obter mais informações, veja [Retrain Machine Learning models programmatically (Reparametrizar modelos do Machine Learning programaticamente)](retrain-models-programmatically.md).

<!-- internal links -->
[Criar uma experimentação de preparação]: #create-a-training-experiment
[Convertê-lo para uma experimentação preditiva]: #convert-the-training-experiment-to-a-predictive-experiment
[Implementá-lo como um serviço web]: #deploy-it-as-a-web-service
[novo]: #deploy-the-predictive-experiment-as-a-new-Web-service
[clássico]: #deploy-the-predictive-experiment-as-a-new-Web-service
[Access]: #access-the-Web-service
[Manage]: #manage-the-Web-service-in-the-azure-management-portal
[Update]: #update-the-Web-service

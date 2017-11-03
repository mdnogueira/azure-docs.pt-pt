---
title: "Reparametrização dos modelos de Machine Learning através de programação | Microsoft Docs"
description: "Saiba como programaticamente reparametrização de um modelo e atualizar o serviço web para utilizar o modelo treinado recentemente no Azure Machine Learning."
services: machine-learning
documentationcenter: 
author: raymondlaghaeian
manager: jhubbard
editor: cgronlun
ms.assetid: 7ae4f977-e6bf-4d04-9dde-28a66ce7b664
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/19/2017
ms.author: raymondl;garye;v-donglo
ms.openlocfilehash: c56ce659766536772d203d0366ef6b53e544a82b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="retrain-machine-learning-models-programmatically"></a>Voltar a preparar modelos do Machine Learning programaticamente
Nestas instruções, irá aprender a reparametrização programática de um serviço Web do Azure Machine Learning utilizar c# e o serviço de execução de lote do Machine Learning.

Depois de ter retrained o modelo, os guias seguintes mostram como atualizar o modelo no seu serviço web preditiva:

* Se tiver implementado um serviço de web clássico no portal de serviços Web Machine Learning, consulte [reparametrização de um serviço de web clássico](retrain-a-classic-web-service.md). 
* Se implementou um novo serviço web, consulte [reparametrização de um novo serviço web utilizando os cmdlets de gestão do Machine Learning](retrain-new-web-service-using-powershell.md).

Para obter uma descrição geral do processo de reparametrização, consulte [reparametrização de um modelo de Machine Learning](retrain-machine-learning-model.md).

Se pretender começar a utilizar o serviço de web novo Azure Resource Manager com base existente, consulte [reparametrização de um serviço web preditiva existente](retrain-existing-resource-manager-based-web-service.md).

## <a name="create-a-training-experiment"></a>Criar uma experimentação de preparação
Neste exemplo, irá utilizar "exemplo 5: Evaluate de comboio, teste, para classificação do binária: conjunto de dados para adultos" de exemplos do Microsoft Azure Machine Learning. 

Para criar a experimentação:

1. Inicie sessão no Microsoft Azure Machine Learning Studio. 
2. No canto inferior direito do dashboard, clique em **novo**.
3. Na Microsoft Samples, selecione exemplo 5.
4. Para mudar o nome a experimentação, na parte superior da tela de experimentação, selecione o nome da experimentação "exemplo 5: Evaluate de comboio, teste, para classificação do binária: conjunto de dados para adultos".
5. Modelo de Census de tipo.
6. Na parte inferior da tela de experimentação, clique em **executar**.
7. Clique em **configurar o serviço de web** e selecione **reparametrização serviço web**. 

O seguinte mostra a experimentação inicial.
   
   ![Experimentação inicial.][2]


## <a name="create-a-predictive-experiment-and-publish-as-a-web-service"></a>Criar uma experimentação preditiva e publicar como um serviço web
Em seguida, criar uma experimentação Predicative.

1. Na parte inferior da tela de experimentação, clique em **segurança serviço Web** e selecione **preditiva serviço Web**. Isto guarda o modelo como um modelo preparado e adiciona módulos de entrada e saída do serviço de web. 
2. Clique em **Executar**. 
3. Depois da experimentação ter terminado, clique em **implementar o serviço Web [clássico]** ou **implementar o serviço Web do [New]**.

> [!NOTE] 
> Para implementar um novo serviço web tem de ter permissões suficientes na subscrição aos quais pode implementar o serviço web. Para obter mais informações, consulte [gira um serviço Web utilizando o portal de serviços Web do Azure Machine Learning](manage-new-webservice.md). 

## <a name="deploy-the-training-experiment-as-a-training-web-service"></a>Implementar a experimentação de preparação como um serviço web de formação
Para a reparametrização do modelo treinado, tem de implementar a experimentação de preparação que criou como um serviço web de Retraining. Este serviço web tem um *saída de serviço Web* módulo ligado para o  *[preparar modelo] [ train-model]*  módulo, para conseguir criar modelos de formação novo.

1. Para voltar para a experimentação de preparação, clique no ícone de experimentações no painel da esquerda e clique em experimentação com o nome de modelo Census.  
2. Na caixa de pesquisa de itens de experiência de pesquisa, escreva o serviço web. 
3. Arrastar um *entrada de serviço Web* tela de módulo para a experimentação e ligue o resultado para o *apagar dados em falta* módulo.  Isto garante que os dados reparametrização são processados da mesma forma que os dados de formação original.
4. Arraste duas *saída do serviço web* módulos para a tela de experimentação. Ligar a saída do *preparar modelo* um e o resultado do módulo a *avaliar modelo* módulo para outro. A saída do serviço web para **preparar modelo** dá-no novo modelo treinado. Resultado do anexado ao **avaliar modelo** devolve esse módulo de saída do, que é que os resultados de desempenho.
5. Clique em **Executar**. 

Em seguida tem de implementar a experimentação de preparação como um serviço web que produz um modelo preparado e resultados de avaliação do modelo. Para tal, o seguinte conjunto de ações dependem se estiver a trabalhar com um serviço de web clássico ou um novo serviço web.  

**Serviço web clássico**

Na parte inferior da tela de experimentação, clique em **segurança serviço Web** e selecione **implementar o serviço Web [clássico]**. O serviço Web **Dashboard** é apresentada com a chave de API e a página de ajuda da API para execução de lote. O método de execução de lote pode ser utilizado para criar modelos de formação.

**Novo serviço web**

Na parte inferior da tela de experimentação, clique em **segurança serviço Web** e selecione **implementar o serviço Web do [New]**. O portal de serviços de Web do Web Service do Azure Machine Learning abre a página de serviço web de implementar. Escreva um nome para o seu serviço web e escolha um plano de pagamento, em seguida, clique em **implementar**. O método de execução de lote pode ser utilizado para criar modelos de formação

Em ambos os casos, após a experimentação estiver concluída, o fluxo de trabalho resultante deve ter o seguinte aspeto:

![Resultante fluxo de trabalho após a execução.][4]



## <a name="retrain-the-model-with-new-data-using-bes"></a>Reparametrização do modelo com novos dados utilizando BES
Neste exemplo, estiver a utilizar c# para criar a aplicação reparametrização. Também pode utilizar o código de exemplo Python ou R para realizar esta tarefa.

Para chamar as APIs reparametrização:

1. Criar uma aplicação de consola c# no Visual Studio: **novo** > **projeto** > **Visual c#** > **ambiente de trabalho clássico do Windows** > **aplicação de consola (.NET Framework)**.
2. Inicie sessão no portal do serviço Web do Machine Learning.
3. Se estiver a trabalhar com um serviço web de clássica, clique em **serviços Web clássicos**.
   1. Clique no serviço de web que estiver a trabalhar com.
   2. Clique no ponto final predefinido.
   3. Clique em **consumir**.
   4. Na parte inferior do **Consume** na página de **código de exemplo** secção, clique em **Batch**.
   5. Continue para o passo 5 deste procedimento.
4. Se estiver a trabalhar com um novo serviço web, clique em **serviços Web**.
   1. Clique no serviço de web que estiver a trabalhar com.
   2. Clique em **consumir**.
   3. Na parte inferior do Consume página, além de **código de exemplo** secção, clique em **Batch**.
5. Copie o c# código de exemplo para a execução de lote e cole-o no ficheiro Program.cs, certificar-se de que o espaço de nomes permanece intacta.

Adicione o pacote Nuget Microsoft.AspNet.WebApi.Client conforme especificado nos comentários. Para adicionar a referência ao Microsoft.WindowsAzure.Storage.dll, poderá terá primeiro de instalar a biblioteca de cliente para serviços de armazenamento do Microsoft Azure. Para obter mais informações, consulte [dos serviços de armazenamento do Windows](https://www.nuget.org/packages/WindowsAzure.Storage).

### <a name="update-the-apikey-declaration"></a>Atualizar a declaração de apikey
Localize o **apikey** declaração.

    const string apiKey = "abc123"; // Replace this with the API key for the web service

No **informações de consumo básico** secção o **Consume** página, localize a chave primária e copie-o para o **apikey** declaração.

### <a name="update-the-azure-storage-information"></a>Atualizar as informações de armazenamento do Azure
O código de exemplo BES carrega um ficheiro a partir de uma unidade local (por exemplo "C:\temp\CensusIpnput.csv") ao Storage do Azure, os processos que e escreve os resultados de volta ao Storage do Azure.  

Para realizar esta tarefa, tem de obter as informações de nome, a chave e o contentor da conta de armazenamento para a sua conta de armazenamento a partir do portal clássico do Azure e os valores correspondentes de atualização no código. 

1. Inicie sessão no portal clássico do Azure.
2. Na coluna de navegação esquerdo, clique em **armazenamento**.
3. Na lista de contas do storage, selecione um para armazenar o modelo retrained.
4. Na parte inferior da página, clique em **gerir chaves de acesso**.
5. Copie e guarde o **chave de acesso primária** e fechar a caixa de diálogo. 
6. Na parte superior da página, clique em **contentores**.
7. Selecione um contentor existente ou crie um novo e guarde o nome.

Localize o *StorageAccountName*, *StorageAccountKey*, e *StorageContainerName* declarações e atualize os valores que guardou no portal do Azure.

    const string StorageAccountName = "mystorageacct"; // Replace this with your Azure Storage Account name
    const string StorageAccountKey = "a_storage_account_key"; // Replace this with your Azure Storage Key
    const string StorageContainerName = "mycontainer"; // Replace this with your Azure Storage Container name

Também tem de garantir que o ficheiro de entrada está disponível na localização que especificar no código. 

### <a name="specify-the-output-location"></a>Especifique a localização de saída
Ao especificar a localização de saída no Payload de pedido, a extensão do ficheiro especificado no *RelativeLocation* tem de ser especificado como ilearner. 

Consulte o exemplo seguinte:

    Outputs = new Dictionary<string, AzureBlobDataReference>() {
        {
            "output1",
            new AzureBlobDataReference()
            {
                ConnectionString = storageConnectionString,
                RelativeLocation = string.Format("{0}/output1results.ilearner", StorageContainerName) /*Replace this with the location you would like to use for your output file, and valid file extension (usually .csv for scoring results, or .ilearner for trained models)*/
            }
        },

> [!NOTE]
> Os nomes das suas localizações de saída podem ser diferentes desta explicação passo a passo com base na ordem na qual adicionou os módulos de saída do serviço web. Uma vez que configurar esta experimentação de preparação com dois saídas, os resultados incluem informações de localização de armazenamento para ambos os parâmetros.  
> 
> 

![Reparametrização saída][6]

Diagrama 4: Reparametrização saída.

## <a name="evaluate-the-retraining-results"></a>Avaliar os resultados reparametrização
Quando executar a aplicação, o resultado inclui o token de URL e SAS necessário para os resultados da avaliação de acesso.

Pode ver os resultados de desempenho do modelo de retrained ao combinar o *BaseLocation*, *RelativeLocation*, e *SasBlobToken* dos resultados de saída para *output2* (conforme mostrado na imagem de saída reparametrização anterior) e colar o URL completo na barra de endereço do browser.  

Examine os resultados para determinar se o modelo treinado recentemente efetua bem suficiente para substituir a existente.

Copiar o *BaseLocation*, *RelativeLocation*, e *SasBlobToken* nos resultados de saída, irá utilizá-los durante o processo de reparametrização.

## <a name="next-steps"></a>Passos seguintes
Se implementou o serviço web preditiva clicando **implementar o serviço Web [clássico]**, consulte [reparametrização de um serviço de web clássico](retrain-a-classic-web-service.md).

Se implementou o serviço web preditiva clicando **implementar o serviço Web do [New]**, consulte [reparametrização de um novo serviço web utilizando os cmdlets de gestão do Machine Learning](retrain-new-web-service-using-powershell.md).

<!-- Retrain a New web service using the Machine Learning Management REST API -->


[1]: ./media/retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE01.png
[2]: ./media/retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE02.png
[3]: ./media/retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE03.png
[4]: ./media/retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE04.png
[5]: ./media/retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE05.png
[6]: ./media/retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE06.png
[7]: ./media/retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE07.png


<!-- Module References -->
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/

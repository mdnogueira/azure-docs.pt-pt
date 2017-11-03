---
title: "Reparametrização de um serviço web preditiva | Microsoft Docs"
description: "Saiba como reparametrização de um modelo e atualizar o serviço web para utilizar o modelo treinado recentemente no Azure Machine Learning."
services: machine-learning
documentationcenter: 
author: vDonGlover
manager: raymondl
editor: 
ms.assetid: cc4c26a2-5672-4255-a767-cfd971e46775
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/11/2017
ms.author: v-donglo
ms.openlocfilehash: e7663f931594c0626a173562b846f3f9324d8ba3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="retrain-an-existing-predictive-web-service"></a>Reparametrização de um serviço web preditiva existente
Este documento descreve o processo reparametrização para o seguinte cenário:

* Tem uma experimentação de preparação e de uma experimentação preditiva que implementou como um serviço operacionalizado web.
* Tiver novos dados que pretende que o seu serviço web preditiva a utilizar para efetuar a respetiva classificação.

> [!NOTE] 
> Para implementar um novo serviço web tem de ter permissões suficientes na subscrição aos quais pode implementar o serviço web. Para obter mais informações, consulte [gira um serviço Web utilizando o portal de serviços Web do Azure Machine Learning](manage-new-webservice.md). 

A partir do seu serviço web existente e experimentações, terá de seguir estes passos:

1. Atualize o modelo.
   1. Modificar a experimentação de preparação para que o web service entradas e saídas.
   2. Implemente a experimentação de preparação como um serviço web reparametrização.
   3. Utilize o serviço de execução de lote (BES) da experimentação de preparação para reparametrização do modelo.
2. Utilize os cmdlets do PowerShell do Azure Machine Learning para atualizar a experimentação preditiva.
   1. Inicie sessão sua conta do Azure Resource Manager.
   2. Obter a definição de serviço web.
   3. Exporte a definição de serviço web como JSON.
   4. Atualize a referência para o blob ilearner no JSON.
   5. Importe o JSON para uma definição de serviço web.
   6. Atualize o serviço web com uma nova definição de serviço web.

## <a name="deploy-the-training-experiment"></a>Implementar a experimentação de preparação
Para implementar a experimentação de preparação como um serviço web reparametrização, tem de adicionar web service entradas e saídas do modelo. Ao ligar-se um *saída de serviço Web* módulo para a experimentação  *[preparar modelo] [ train-model]*  módulo, ativar a experimentação de preparação para criar um novo modelo treinado que pode utilizar na sua experimentação preditiva. Se tiver um *avaliar modelo* módulo, também pode anexar saída de serviço web para obter os resultados da avaliação como saída.

Para atualizar a sua experimentação de preparação:

1. Ligar um *Web Service entrada* módulo para a entrada de dados (por exemplo, um *apagar dados em falta* módulo). Normalmente, quando pretende garantir que os dados de entrada são processados da mesma forma como os dados de formação original.
2. Ligar um *saída de serviço Web* módulo para a saída da sua *preparar modelo* módulo.
3. Se tiver um *avaliar modelo* módulo e pretender enviar os resultados da avaliação, ligar-se um *saída de serviço Web* módulo para a saída da sua *avaliar modelo* módulo.

Execute a experimentação.

Em seguida, tem de implementar a experimentação de preparação como um serviço web que produz um modelo preparado e resultados de avaliação do modelo.  

Na parte inferior da tela de experimentação, clique em **segurança serviço Web**e, em seguida, selecione **implementar o serviço Web do [New]**. O portal de serviços Web do Azure Machine Learning é aberto o **implementar serviço Web** página. Escreva um nome para o seu serviço web, escolha um plano de pagamento e, em seguida, clique em **implementar**. Só pode utilizar o método de execução de lote para a criação de modelos de formação.

## <a name="retrain-the-model-with-new-data-by-using-bes"></a>Reparametrização do modelo com novos dados utilizando BES
Neste exemplo, estamos a utilizar c# para criar a aplicação reparametrização. Também pode utilizar o Python ou R código de exemplo para realizar esta tarefa.

Para chamar as APIs reparametrização:

1. Criar uma aplicação de consola c# no Visual Studio: **novo** > **projeto** > **Visual c#** > **ambiente de trabalho clássico do Windows** > **aplicação de consola (.NET Framework)**.
2. Inicie sessão no portal de serviços Web Machine Learning.
3. Clique no serviço de web que está a trabalhar com.
4. Clique em **consumir**.
5. Na parte inferior do **Consume** na página de **código de exemplo** secção, clique em **Batch**.
6. Copie o c# código de exemplo para a execução de lote e cole-o no ficheiro Program.cs. Certifique-se de que o espaço de nomes permanece intacto.

Adicione o pacote NuGet Microsoft.AspNet.WebApi.Client, conforme especificado nos comentários. Para adicionar a referência ao Microsoft.WindowsAzure.Storage.dll, poderá terá primeiro de instalar o [biblioteca de clientes para serviços de armazenamento do Azure](https://www.nuget.org/packages/WindowsAzure.Storage).

A seguinte captura de ecrã mostra o **Consume** página no portal de serviços Web do Azure Machine Learning.

![Consumir página][1]

### <a name="update-the-apikey-declaration"></a>Atualizar a declaração de apikey
Localize o **apikey** declaração:

    const string apiKey = "abc123"; // Replace this with the API key for the web service

No **informações de consumo básico** secção o **Consume** página, localize a chave primária e copie-o para o **apikey** declaração.

### <a name="update-the-azure-storage-information"></a>Atualizar as informações de armazenamento do Azure
O código de exemplo BES carrega um ficheiro a partir de uma unidade local (por exemplo, "C:\temp\CensusIpnput.csv") ao Storage do Azure, os processos que e escreve os resultados de volta ao Storage do Azure.  

Para atualizar as informações de armazenamento do Azure, terá de obter o nome da conta de armazenamento, chave e informações de contentor para a sua conta de armazenamento a partir do portal clássico do Azure e, em seguida, atualize o correspondi depois de executar a sua experimentação, o fluxo de trabalho resultante deve ser semelhante ao seguinte:

![Fluxo de trabalho resultante após a execução][4]valores de ng no código.

1. Inicie sessão no Portal Clássico do Azure.
2. Na coluna de navegação esquerdo, clique em **armazenamento**.
3. Na lista de contas do storage, selecione um para armazenar o modelo retrained.
4. Na parte inferior da página, clique em **gerir chaves de acesso**.
5. Copie e guarde o **chave de acesso primária** e fechar a caixa de diálogo.
6. Na parte superior da página, clique em **contentores**.
7. Selecione um contentor existente, ou crie um novo e guarde o nome.

Localize o *StorageAccountName*, *StorageAccountKey*, e *StorageContainerName* declarações e atualize os valores que guardou no portal clássico.

    const string StorageAccountName = "mystorageacct"; // Replace this with your Azure storage account name
    const string StorageAccountKey = "a_storage_account_key"; // Replace this with your Azure Storage key
    const string StorageContainerName = "mycontainer"; // Replace this with your Azure Storage container name

Também tem de garantir que o ficheiro de entrada está disponível na localização que especificar no código.

### <a name="specify-the-output-location"></a>Especifique a localização de saída
Quando especificar a localização de saída no Payload de pedido, a extensão de ficheiro especificado no *RelativeLocation* tem de ser especificado como `ilearner`. Consulte o exemplo seguinte:

    Outputs = new Dictionary<string, AzureBlobDataReference>() {
        {
            "output1",
            new AzureBlobDataReference()
            {
                ConnectionString = storageConnectionString,
                RelativeLocation = string.Format("{0}/output1results.ilearner", StorageContainerName) /*Replace this with the location you want to use for your output file and a valid file extension (usually .csv for scoring results or .ilearner for trained models)*/
            }
        },

Segue-se um exemplo de saída reparametrização: ![reparametrização saída][6]

## <a name="evaluate-the-retraining-results"></a>Avaliar os resultados reparametrização
Quando executar a aplicação, o resultado inclui o URL e o token de assinaturas de acesso partilhado são necessários para os resultados da avaliação de acesso.

Pode ver os resultados de desempenho do modelo de retrained ao combinar o *BaseLocation*, *RelativeLocation*, e *SasBlobToken* dos resultados de saída para *output2* (conforme mostrado na imagem de saída reparametrização anterior) e colar o URL completo para a barra de endereço do browser.  

Examine os resultados para determinar se o modelo treinado recentemente efetua bem suficiente para substituir a existente.

Copiar o *BaseLocation*, *RelativeLocation*, e *SasBlobToken* dos resultados de saída.

## <a name="retrain-the-web-service"></a>Reparametrização do serviço web
Quando a reparametrização de um novo serviço web, atualize a definição de serviço web preditiva para referenciar o novo modelo treinado. A definição de serviço web é uma representação interna do modelo treinado do serviço web e não é modificável diretamente. Certifique-se de que estão a obter a definição de serviço web para a sua experimentação preditiva e não a experimentação de preparação.

## <a name="sign-in-to-azure-resource-manager"></a>A iniciar sessão para o Azure Resource Manager
Tem primeiro de iniciar sessão sua conta do Azure do ambiente de PowerShell utilizando o [Add-AzureRmAccount](https://msdn.microsoft.com/library/mt619267.aspx) cmdlet.

## <a name="get-the-web-service-definition-object"></a>Obter o objeto de definição do serviço Web
Em seguida, obter o objeto de definição do serviço Web ao chamar o [Get-AzureRmMlWebService](https://msdn.microsoft.com/library/mt619267.aspx) cmdlet.

    $wsd = Get-AzureRmMlWebService -Name 'RetrainSamplePre.2016.8.17.0.3.51.237' -ResourceGroupName 'Default-MachineLearning-SouthCentralUS'

Para determinar o nome do grupo de recursos de um serviço web existente, execute o cmdlet Get-AzureRmMlWebService sem quaisquer parâmetros para apresentar os serviços web na sua subscrição. Localize o serviço web e, em seguida, observe o respetivo ID de serviço web. O nome do grupo de recursos é o quarto elemento no ID de imediatamente após o *resourceGroups* elemento. No exemplo seguinte, o nome do grupo de recursos é predefinido-MachineLearning-SouthCentralUS.

    Properties : Microsoft.Azure.Management.MachineLearning.WebServices.Models.WebServicePropertiesForGraph
    Id : /subscriptions/<subscription ID>/resourceGroups/Default-MachineLearning-SouthCentralUS/providers/Microsoft.MachineLearning/webServices/RetrainSamplePre.2016.8.17.0.3.51.237
    Name : RetrainSamplePre.2016.8.17.0.3.51.237
    Location : South Central US
    Type : Microsoft.MachineLearning/webServices
    Tags : {}

Em alternativa, para determinar o nome do grupo de recursos de um serviço web existente, inicie sessão no portal de serviços Web do Azure Machine Learning. Selecione o serviço web. O nome do grupo de recursos é o quinto elemento do URL do serviço web, imediatamente após o *resourceGroups* elemento. No exemplo seguinte, o nome do grupo de recursos é predefinido-MachineLearning-SouthCentralUS.

    https://services.azureml.net/subscriptions/<subcription ID>/resourceGroups/Default-MachineLearning-SouthCentralUS/providers/Microsoft.MachineLearning/webServices/RetrainSamplePre.2016.8.17.0.3.51.237


## <a name="export-the-web-service-definition-object-as-json"></a>Exportar o objeto de definição do serviço Web como JSON
Para modificar a definição do modelo treinado para utilizar o modelo treinado recentemente, primeiro tem de utilizar o [exportação AzureRmMlWebService](https://msdn.microsoft.com/library/azure/mt767935.aspx) cmdlet para exportá-lo para um ficheiro de formato JSON.

    Export-AzureRmMlWebService -WebService $wsd -OutputFile "C:\temp\mlservice_export.json"

## <a name="update-the-reference-to-the-ilearner-blob"></a>Atualizar a referência para o blob ilearner
No elementos, localize o [modelo treinado], atualize o *uri* valor o *locationInfo* nó com o URI do ilearner blob. O URI é gerado ao combinar o *BaseLocation* e *RelativeLocation* da saída de BES reparametrização chamada.

     "asset3": {
        "name": "Retrain Sample [trained model]",
        "type": "Resource",
        "locationInfo": {
          "uri": "https://mltestaccount.blob.core.windows.net/azuremlassetscontainer/baca7bca650f46218633552c0bcbba0e.ilearner"
        },
        "outputPorts": {
          "Results dataset": {
            "type": "Dataset"
          }
        }
      },

## <a name="import-the-json-into-a-web-service-definition-object"></a>Importe o JSON para um objeto de definição do serviço Web
Tem de utilizar o [importação AzureRmMlWebService](https://msdn.microsoft.com/library/azure/mt767925.aspx) cmdlet para converter o ficheiro JSON modificado com um objeto de definição do serviço Web que pode utilizar para atualizar a experimentação predicative.

    $wsd = Import-AzureRmMlWebService -InputFile "C:\temp\mlservice_export.json"


## <a name="update-the-web-service"></a>Atualizar o serviço web
Por último, utilize o [atualização AzureRmMlWebService](https://msdn.microsoft.com/library/azure/mt767922.aspx) cmdlet para atualizar a experimentação preditiva.

    Update-AzureRmMlWebService -Name 'RetrainSamplePre.2016.8.17.0.3.51.237' -ResourceGroupName 'Default-MachineLearning-SouthCentralUS'

[1]: ./media/retrain-existing-arm-web-service/machine-learning-retrain-models-consume-page.png
[4]: ./media/retrain-existing-arm-web-service/machine-learning-retrain-models-programmatically-IMAGE04.png
[6]: ./media/retrain-existing-arm-web-service/machine-learning-retrain-models-programmatically-IMAGE06.png

<!-- Module References -->
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/

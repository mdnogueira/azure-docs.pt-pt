---
title: "Reparametrização de um serviço web do novo Azure Machine Learning com o PowerShell | Microsoft Docs"
description: "Saiba como programaticamente reparametrização de um modelo e atualizar o serviço web para utilizar o modelo treinado recentemente no Azure Machine Learning utilizando os cmdlets do PowerShell de gestão do Machine Learning."
services: machine-learning
documentationcenter: 
author: vDonGlover
manager: raymondlaghaeian
editor: 
ms.assetid: 3953a398-6174-4d2d-8bbd-e55cf1639415
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/28/2017
ms.author: v-donglo
ms.openlocfilehash: 77bc78e7ed27f1566e5e5f6a3539c93c9aa73e2d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="retrain-a-new-resource-manager-based-web-service-using-the-machine-learning-management-powershell-cmdlets"></a>Reparametrização de um serviço web de novo Gestor de recursos com base utilizando os cmdlets do PowerShell de gestão do Machine Learning
Quando a reparametrização de um novo serviço web, atualize a definição de serviço web preditiva para referenciar o novo modelo treinado.  

## <a name="prerequisites"></a>Pré-requisitos
Tem de configurar uma experimentação de preparação e de uma experimentação preditiva conforme mostrado no [Machine Learning reparametrização dos modelos programáticos](retrain-models-programmatically.md). 

> [!IMPORTANT]
> A experimentação preditiva tem de ser implementada como um Gestor de recursos do Azure (novo) com base do machine learning serviço web. Para implementar um novo serviço web tem de ter permissões suficientes na subscrição aos quais pode implementar o serviço web. Para obter mais informações, consulte [gira um serviço Web utilizando o portal de serviços Web do Azure Machine Learning](manage-new-webservice.md). 

Para obter informações adicionais sobre implementar os serviços web, consulte [implementar um serviço web do Azure Machine Learning](publish-a-machine-learning-web-service.md).

Este processo requer que tem instalado o Cmdlets do Azure Machine Learning. Para instalar os cmdlets de Machine Learning de informações, consulte o [Cmdlets do Azure Machine Learning](https://msdn.microsoft.com/library/azure/mt767952.aspx) na MSDN.

Copiar as seguintes informações a partir da saída reparametrização:

* BaseLocation
* RelativeLocation

Os passos que efetuar são:

1. Inicie sessão sua conta do Azure Resource Manager.
2. Obter a definição de serviço web
3. Exportar a definição de serviço Web como JSON
4. Atualize a referência para o blob ilearner no JSON.
5. Importar o JSON para uma definição de serviço Web
6. Atualizar o serviço web com a nova definição de serviço Web

## <a name="sign-in-to-your-azure-resource-manager-account"></a>Inicie sessão na sua conta do Azure Resource Manager
Tem primeiro de iniciar sessão sua conta do Azure de dentro do ambiente de PowerShell utilizando o [Add-AzureRmAccount](https://msdn.microsoft.com/library/mt619267.aspx) cmdlet.

## <a name="get-the-web-service-definition"></a>Obter a definição de serviço Web
Em seguida, obter o serviço Web ao chamar o [Get-AzureRmMlWebService](https://msdn.microsoft.com/library/mt619267.aspx) cmdlet. A definição de serviço Web é uma representação interna do modelo treinado do serviço web e não é modificável diretamente. Certifique-se de que estão a obter a definição de serviço Web para a sua experimentação preditiva e não a experimentação de preparação.

    $wsd = Get-AzureRmMlWebService -Name 'RetrainSamplePre.2016.8.17.0.3.51.237' -ResourceGroupName 'Default-MachineLearning-SouthCentralUS'

Para determinar o nome do grupo de recursos de um serviço web existente, execute o cmdlet Get-AzureRmMlWebService sem quaisquer parâmetros para apresentar os serviços web na sua subscrição. Localize o serviço web e, em seguida, observe o respetivo ID de serviço web. O nome do grupo de recursos é o quarto elemento no ID de imediatamente após o *resourceGroups* elemento. No exemplo seguinte, o nome do grupo de recursos é predefinido-MachineLearning-SouthCentralUS.

    Properties : Microsoft.Azure.Management.MachineLearning.WebServices.Models.WebServicePropertiesForGraph
    Id : /subscriptions/<subscription ID>/resourceGroups/Default-MachineLearning-SouthCentralUS/providers/Microsoft.MachineLearning/webServices/RetrainSamplePre.2016.8.17.0.3.51.237
    Name : RetrainSamplePre.2016.8.17.0.3.51.237
    Location : South Central US
    Type : Microsoft.MachineLearning/webServices
    Tags : {}

Em alternativa, para determinar o nome do grupo de recursos de um serviço web existente, inicie sessão no portal de serviços Web do Microsoft Azure Machine Learning. Selecione o serviço web. O nome do grupo de recursos é o quinto elemento do URL do serviço web, imediatamente após o *resourceGroups* elemento. No exemplo seguinte, o nome do grupo de recursos é predefinido-MachineLearning-SouthCentralUS.

    https://services.azureml.net/subscriptions/<subcription ID>/resourceGroups/Default-MachineLearning-SouthCentralUS/providers/Microsoft.MachineLearning/webServices/RetrainSamplePre.2016.8.17.0.3.51.237


## <a name="export-the-web-service-definition-as-json"></a>Exportar a definição de serviço Web como JSON
Para modificar a definição para o modelo treinado para utilizar recentemente treinado modelo, primeiro tem de utilizar o [exportação AzureRmMlWebService](https://msdn.microsoft.com/library/azure/mt767935.aspx) cmdlet para exportá-lo para um ficheiro de formato JSON.

    Export-AzureRmMlWebService -WebService $wsd -OutputFile "C:\temp\mlservice_export.json"

## <a name="update-the-reference-to-the-ilearner-blob-in-the-json"></a>Atualize a referência para o blob ilearner no JSON.
No elementos, localize o [modelo treinado], atualize o *uri* valor o *locationInfo* nó com o URI do ilearner blob. O URI é gerado ao combinar o *BaseLocation* e *RelativeLocation* da saída de BES reparametrização chamada. Isto atualiza o caminho para o novo modelo treinado de referência.

     "asset3": {
        "name": "Retrain Samp.le [trained model]",
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

## <a name="import-the-json-into-a-web-service-definition"></a>Importar o JSON para uma definição de serviço Web
Tem de utilizar o [importação AzureRmMlWebService](https://msdn.microsoft.com/library/azure/mt767925.aspx) cmdlet para converter o ficheiro JSON modificado com uma definição de serviço Web que pode utilizar para atualizar a definição de serviço Web.

    $wsd = Import-AzureRmMlWebService -InputFile "C:\temp\mlservice_export.json"


## <a name="update-the-web-service-with-new-web-service-definition"></a>Atualizar o serviço web com a nova definição de serviço Web
Por fim, utilize [atualização AzureRmMlWebService](https://msdn.microsoft.com/library/azure/mt767922.aspx) cmdlet para atualizar a definição de serviço Web.

    Update-AzureRmMlWebService -Name 'RetrainSamplePre.2016.8.17.0.3.51.237' -ResourceGroupName 'Default-MachineLearning-SouthCentralUS'  -ServiceUpdates $wsd

## <a name="summary"></a>Resumo
Utilizar os cmdlets de gestão do PowerShell do Machine Learning, pode atualizar o modelo treinado de um serviço Web preditiva ativar cenários, tais como:

* Modelo periódico reparametrização com novos dados.
* Distribuição de um modelo para clientes com o objetivo de permitir que os reparametrização do modelo utilizando os seus próprios dados.


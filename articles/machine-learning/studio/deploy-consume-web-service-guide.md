---
title: "Do Azure serviços Web Machine Learning: Implementação e o consumo | Microsoft Docs"
description: "Recursos para implementar e utilizar os serviços web."
services: machine-learning
documentationcenter: 
author: vDonGlover
manager: raymondl
editor: 
ms.assetid: 47635376-d1f4-4ea4-a6af-bd1f99f69a69
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/19/2017
ms.author: v-donglo
ms.openlocfilehash: bc331e1557c3a35ea3faa8e021923764ec748a4f
ms.sourcegitcommit: 5a6e943718a8d2bc5babea3cd624c0557ab67bd5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/01/2017
---
# <a name="azure-machine-learning-web-services-deployment-and-consumption"></a>Serviços Web do Azure Machine Learning: implementação e consumo
Pode utilizar o Azure Machine Learning para implementar modelos como serviços web e fluxos de trabalho de machine learning. Estes serviços web, em seguida, podem ser utilizados para chamar os modelos de machine learning a partir de aplicações através da Internet para fazer predições em tempo real ou no modo de batch. Dado que os serviços web RESTful, pode chamá-los de vários idiomas e plataformas, tal como .NET e Java, programação e de aplicações, como o Excel.

As secções seguintes fornecem ligações para instruções, códigos e documentação para o ajudar a começar.

## <a name="deploy-a-web-service"></a>Implementar serviços Web

### <a name="with-azure-machine-learning-studio"></a>Com o Azure Machine Learning Studio
O Machine Learning Studio e o portal de serviços Web do Microsoft Azure Machine Learning ajudam a implementar e gerir um serviço web sem escrever código.

As hiperligações seguintes fornecem informações gerais sobre como implementar um novo serviço web:

* Para obter uma descrição geral sobre como implementar um novo serviço web com base no Gestor de recursos do Azure, consulte [implementar um novo serviço web](publish-a-machine-learning-web-service.md).
* Para obter instruções sobre como implementar um serviço web, consulte [implementar um serviço web do Azure Machine Learning](publish-a-machine-learning-web-service.md).
* Para obter instruções completas sobre como criar e implementar um serviço web, consulte [instruções passo 1: criar uma área de trabalho do Machine Learning](walkthrough-1-create-ml-workspace.md).
* Para obter exemplos específicos que implementar um serviço web, consulte:

  * [Instruções passo 5: Implementar o serviço web do Azure Machine Learning](walkthrough-5-publish-web-service.md)
  * [Como implementar um serviço web em várias regiões](how-to-deploy-to-multiple-regions.md)

### <a name="with-web-services-resource-provider-apis-azure-resource-manager-apis"></a>Com o fornecedor de recursos de serviços web APIs (APIs do Azure Resource Manager)
O fornecedor de recursos do Azure Machine Learning para serviços web permite a implementação e gestão de serviços web através de chamadas da REST API. Para obter mais detalhes, consulte o [Machine Learning Web Service (REST)](/rest/api/machinelearning/index) referência.

<!-- [Machine Learning Web Service (REST)](https://msdn.microsoft.com/library/azure/mt767538.aspx) reference. -->


### <a name="with-powershell-cmdlets"></a>Com os cmdlets do PowerShell
Fornecedor de recursos do Azure Machine Learning para serviços web permite a implementação e gestão de serviços web utilizando cmdlets do PowerShell.

Para utilizar os cmdlets, tem primeiro de iniciar sessão sua conta do Azure de dentro do ambiente do PowerShell, utilizando o [Add-AzureRmAccount](https://msdn.microsoft.com/library/mt619267.aspx) cmdlet. Se não estiver familiarizado com como chamar comandos do PowerShell que são baseados no Resource Manager, veja [utilizar o Azure PowerShell com o Azure Resource Manager](../../azure-resource-manager/powershell-azure-resource-manager.md#log-in-to-your-azure-account).

Para exportar a sua experimentação preditiva, utilize [este código de exemplo](https://github.com/ritwik20/AzureML-WebServices). Depois de criar o ficheiro .exe do código, pode escrever:

    C:\<folder>\GetWSD <experiment-url> <workspace-auth-token>

Executar a aplicação cria um modelo de JSON do serviço web. Para utilizar o modelo para implementar um serviço web, tem de adicionar as seguintes informações:

* Nome da conta de armazenamento e a chave

    Pode obter o nome de conta de armazenamento e da chave do [portal do Azure](https://portal.azure.com/).
* ID de plano de compromisso

    Pode obter o ID de plano do [serviços Web do Azure Machine Learning](https://services.azureml.net) portal, iniciando sessão e clicar em nome do plano.

Adicioná-los para o modelo JSON como subordinados a *propriedades* nó no mesmo nível, como o *MachineLearningWorkspace* nó.

Eis um exemplo:

    "StorageAccount": {
            "name": "YourStorageAccountName",
            "key": "YourStorageAccountKey"
    },
    "CommitmentPlan": {
        "id": "subscriptions/YouSubscriptionID/resourceGroups/YourResourceGroupID/providers/Microsoft.MachineLearning/commitmentPlans/YourPlanName"
    }

Consulte os seguintes artigos e o código de exemplo para obter detalhes adicionais:

* [Cmdlets do Azure Machine Learning](https://msdn.microsoft.com/library/azure/mt767952.aspx) na MSDN
* Exemplo [instruções](https://github.com/raymondlaghaeian/azureml-webservices-arm-powershell/blob/master/sample-commands.txt) no GitHub

## <a name="consume-the-web-services"></a>Consumir os serviços web
### <a name="from-the-azure-machine-learning-web-services-ui-testing"></a>Partir do Azure Machine Learning Web dos serviços da IU (teste)
Pode testar o seu serviço web do portal de serviços Web do Azure Machine Learning. Isto inclui a testar o serviço de resposta-pedido (RRS) e interfaces de serviço de execução de lote (BES).

* [Implementar um serviço Web novo](publish-a-machine-learning-web-service.md)
* [Implementar um serviço web do Azure Machine Learning](publish-a-machine-learning-web-service.md)
* [Instruções passo 5: Implementar o serviço web do Azure Machine Learning](walkthrough-5-publish-web-service.md)

### <a name="from-excel"></a>A partir do Excel
Pode transferir um modelo do Excel que consome o serviço web:

* [Consumir um serviço web do Azure Machine Learning a partir do Excel](consuming-from-excel.md)
* [Suplemento do Excel para serviços Web do Azure Machine Learning](excel-add-in-for-web-services.md)

### <a name="from-a-rest-based-client"></a>De um cliente baseado em REST
Serviços Web do Azure Machine Learning são RESTful APIs. Pode consumir estas APIs de várias plataformas, tais como .NET, Python, R, Java, etc. O **Consume** página para o seu serviço web de [portal de serviços Web do Microsoft Azure Machine Learning](https://services.azureml.net) tem o código de exemplo que pode ajudar a começar a utilizar. Para obter mais informações, veja [How to consume an Azure Machine Learning Web service](consume-web-services.md) (Como consumir um serviço Web do Azure Machine Learning).

---
title: "Criar a sua primeira função em Linux a partir da CLI do Azure (pré-visualização) | Microsoft Docs"
description: "Saiba como criar a sua primeira função do Azure em execução numa imagem predefinida do Linux utilizando a CLI do Azure."
services: functions
keywords: 
author: ggailey777
ms.author: glenga
ms.date: 11/15/2017
ms.topic: quickstart
ms.service: functions
ms.custom: mvc
ms.devlang: azure-cli
manager: cfowler
ms.openlocfilehash: d04e2000f2043e8bb11e15f6b9d7fd06ef5b9da3
ms.sourcegitcommit: 7d107bb9768b7f32ec5d93ae6ede40899cbaa894
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/16/2017
---
# <a name="create-your-first-function-running-on-linux-using-the-azure-cli-preview"></a>Criar a sua primeira função em execução no Linux utilizando a CLI do Azure (pré-visualização)

As funções do Azure permite-lhe alojar as suas funções no Linux num contentor predefinido App Service do Azure. Esta funcionalidade está atualmente em pré-visualização. Também pode [traga a sua própria contentor personalizado](functions-create-function-linux-custom-image.md). 

Este tópico de início rápido explica como utilizar as funções do Azure com a CLI do Azure para criar a sua primeira aplicação de função no Linux alojado no contentor do serviço de aplicações predefinido. O código de função em si é implementado para a imagem de um repositório de exemplo do GitHub.    

São suportados os seguintes passos num computador Mac, Windows ou Linux. 

## <a name="prerequisites"></a>Pré-requisitos 

Para concluir este guia de início rápido, necessita de:

+ Uma subscrição ativa do Azure.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar a CLI localmente, este tópico requer a CLI do Azure versão 2.0.21 ou posterior. Executar `az --version` para localizar a versão do. Se precisar de instalar ou atualizar, veja [instalar o Azure CLI 2.0]( /cli/azure/install-azure-cli). 

[!INCLUDE [functions-create-resource-group](../../includes/functions-create-resource-group.md)]

[!INCLUDE [functions-create-storage-account](../../includes/functions-create-storage-account.md)]

## <a name="create-a-linux-app-service-plan"></a>Criar um plano de serviço de aplicações do Linux

Linux que alojam funções de está atualmente só é suportado um plano de serviço de aplicações. Ainda, o consumo plano de alojamento não é suportado. Para saber mais acerca de alojamento, consulte o artigo [que alojam as funções do Azure planos de comparação](functions-scale.md). 

[!INCLUDE [app-service-plan-no-h](../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

## <a name="create-a-function-app-on-linux"></a>Criar uma aplicação de função no Linux

Tem de ter uma aplicação de função para alojar a execução das suas funções no Linux. A aplicação de função fornece um ambiente de execução do seu código de função. Permite-lhe agrupar funções como unidades lógicas para uma gestão, implementação e partilha de recursos mais fácil. Criar uma aplicação de função utilizando o [az functionapp criar](/cli/azure/functionapp#create) comando com um plano de serviço de aplicações do Linux. 

O seguinte comando, substitua um nome de aplicação de função exclusivo onde vir o `<app_name>` marcador de posição e a conta de armazenamento de nome para `<storage_name>`. O `<app_name>` vai ser utilizado como o domínio DNS predefinido para a aplicação Function App, daí que o nome tenha de ser exclusivo em todas as aplicações no Azure. O _url de origem de implementação_ parâmetro é um repositório de exemplo no GitHub que contém uma função de "Olá mundo" HTTP acionado.

```azurecli-interactive
az functionapp create --name <app_name> --storage-account  <storage_name>  --resource-group myResourceGroup \
--plan myAppServicePlan --deployment-source-url https://github.com/Azure-Samples/functions-quickstart-linux
```
Depois da função de aplicação foi criada e implementada, a CLI do Azure mostra informações semelhante ao seguinte exemplo:

```json
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 1536,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "quickstart.azurewebsites.net",
  "enabled": true,
  "enabledHostNames": [
    "quickstart.azurewebsites.net",
    "quickstart.scm.azurewebsites.net"
  ],
   ....
    // Remaining output has been truncated for readability.
}
```

Porque `myAppServicePlan` é um plano de Linux, a imagem de docker incorporado é utilizada para criar o contentor que executa a aplicação de função no Linux. 

>[!NOTE]  
>O repositório de exemplo inclui atualmente dois ficheiros de scripts, [deploy.sh](https://github.com/Azure-Samples/functions-quickstart-linux/blob/master/deploy.sh) e [.deployment](https://github.com/Azure-Samples/functions-quickstart-linux/blob/master/.deployment). O ficheiro .deployment informa o processo de implementação a utilizar deploy.sh como o [script de implementação personalizada](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script). Na versão de pré-visualização atual, os scripts são necessários para implementar a aplicação de função numa imagem de Linux.  

[!INCLUDE [functions-test-function-code](../../includes/functions-test-function-code.md)]

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

[!INCLUDE [functions-quickstart-next-steps-cli](../../includes/functions-quickstart-next-steps-cli.md)]

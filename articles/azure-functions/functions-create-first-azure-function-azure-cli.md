---
title: "Criar a sua primeira função a partir da CLI do Azure | Microsoft Docs"
description: "Saiba como criar a sua primeira Função do Azure para execução sem servidor através da CLI do Azure."
services: functions
keywords: 
author: ggailey777
ms.author: glenga
ms.assetid: 674a01a7-fd34-4775-8b69-893182742ae0
ms.date: 08/22/2017
ms.topic: quickstart
ms.service: functions
ms.custom: mvc
ms.devlang: azure-cli
manager: cfowler
ms.openlocfilehash: ab35963dc9d10134799270e6ab3e6593be0e601a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="create-your-first-function-using-the-azure-cli"></a>Criar a sua primeira função com a CLI do Azure

Este tutorial de guia de introdução mostra-lhe como utilizar as Funções do Azure para criar a sua primeira função. Vai utilizar a CLI do Azure para criar uma aplicação Function App, que é a infraestrutura sem servidor que aloja a sua função. O código da função propriamente dito é implementado a partir de um repositório de exemplos do GitHub.    

Pode seguir os passos abaixo num computador Mac, Windows ou Linux. 

## <a name="prerequisites"></a>Pré-requisitos 

Antes de executar este exemplo, tem de ter o seguinte:

+ Uma conta do [GitHub](https://github.com) ativa. 
+ Uma subscrição ativa do Azure.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar a CLI localmente, este tópico requer a CLI do Azure versão 2.0 ou posterior. Executar `az --version` para localizar a versão do. Se precisar de instalar ou atualizar, veja [instalar o Azure CLI 2.0]( /cli/azure/install-azure-cli). 


## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos com o comando [az group create](/cli/azure/group#create). Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure, como aplicações Function App, bases de dados e contas de armazenamento, são implementados e geridos.

O exemplo seguinte cria um grupo de recursos com o nome `myResourceGroup`.  
Se não estiver a utilizar a Shell de nuvem, inicie sessão com a primeira `az login`.

```azurecli-interactive
az group create --name myResourceGroup --location westeurope
```


## <a name="create-an-azure-storage-account"></a>Criar uma conta de Armazenamento do Azure

As Funções utilizam uma conta de Armazenamento do Azure para guardar as informações de estado das funções, entre outras. Utilize o comando [az storage account create](/cli/azure/storage/account#create) para criar uma conta de armazenamento no grupo de recursos que criou.

No comando seguinte, substitua o nome de uma conta de armazenamento globalmente exclusivo onde vir o `<storage_name>` marcador de posição. Os nomes das contas do Storage devem ter entre 3 e 24 carateres de comprimento e apenas podem conter números e letras minúsculas.

```azurecli-interactive
az storage account create --name <storage_name> --location westeurope --resource-group myResourceGroup --sku Standard_LRS
```

Depois de a conta de armazenamento ter sido criada, a CLI do Azure mostra informações semelhantes às do exemplo seguinte:

```json
{
  "creationTime": "2017-04-15T17:14:39.320307+00:00",
  "id": "/subscriptions/bbbef702-e769-477b-9f16-bc4d3aa97387/resourceGroups/myresourcegroup/...",
  "kind": "Storage",
  "location": "westeurope",
  "name": "myfunctionappstorage",
  "primaryEndpoints": {
    "blob": "https://myfunctionappstorage.blob.core.windows.net/",
    "file": "https://myfunctionappstorage.file.core.windows.net/",
    "queue": "https://myfunctionappstorage.queue.core.windows.net/",
    "table": "https://myfunctionappstorage.table.core.windows.net/"
  },
     ....
    // Remaining output has been truncated for readability.
}
```

## <a name="create-a-function-app"></a>Criar uma aplicação de função

Precisa de uma aplicação Function App para alojar a execução das suas funções. A aplicação Function App proporciona um ambiente para a execução sem servidor do código da sua função. Permite-lhe agrupar funções como unidades lógicas para uma gestão, implementação e partilha de recursos mais fácil. Utilize o comando [az functionapp create](/cli/azure/functionapp#create) para criar uma aplicação Function App. 

O seguinte comando, substitua um nome de aplicação de função exclusivo onde vir o `<app_name>` marcador de posição e a conta de armazenamento de nome para `<storage_name>`. O `<app_name>` vai ser utilizado como o domínio DNS predefinido para a aplicação Function App, daí que o nome tenha de ser exclusivo em todas as aplicações no Azure. 

```azurecli-interactive
az functionapp create --name <app_name> --storage-account  <storage_name>  --resource-group myResourceGroup \
--consumption-plan-location westeurope
```
Por predefinição, as aplicações Function App são criadas com o plano de alojamento Consumo, o que significa que os recursos são adicionados dinamicamente conforme necessário por parte das suas funções e que só paga quando estas são executadas. Para obter mais informações, veja [Choose the correct hosting plan](functions-scale.md) (Escolher o plano de alojamento certo). 

Depois de a aplicação Function App ter sido criada, a CLI do Azure mostra informações semelhantes às do exemplo seguinte:

```json
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
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

Agora que já tem uma aplicação Function App, pode implementar o código da função propriamente dito a partir do repositório de exemplos do GitHub.

## <a name="deploy-your-function-code"></a>Implementar o código de função  

Existem várias formas de criar o código de função na sua aplicação Function App nova. Este tópico liga-se a um repositório de exemplos do GitHub. Tal como anteriormente, no código seguinte, substitua o marcador de posição `<app_name>` pelo nome da aplicação Function App que criou. 

```azurecli-interactive
az functionapp deployment source config --name <app_name> --resource-group myResourceGroup --branch master \
--repo-url https://github.com/Azure-Samples/functions-quickstart \
--manual-integration 
```
Depois da origem de implementação tiver sido definida, a CLI do Azure mostra as informações semelhante ao seguinte exemplo (valores nulos removidos para legibilidade):

```json
{
  "branch": "master",
  "deploymentRollbackEnabled": false,
  "id": "/subscriptions/bbbef702-e769-477b-9f16-bc4d3aa97387/resourceGroups/myResourceGroup/...",
  "isManualIntegration": true,
  "isMercurial": false,
  "location": "West Europe",
  "name": "quickstart",
  "repoUrl": "https://github.com/Azure-Samples/functions-quickstart",
  "resourceGroup": "myResourceGroup",
  "type": "Microsoft.Web/sites/sourcecontrols"
}
```

## <a name="test-the-function"></a>Testar a função

Utilize cURL para testar a função implementada num computador Mac ou Linux ou utilize Bash no Windows. Execute o comando cURL seguinte, substituindo o marcador de posição `<app_name>` pelo nome da sua aplicação Function App. Anexe a cadeia de consulta `&name=<yourname>` ao URL.

```bash
curl http://<app_name>.azurewebsites.net/api/HttpTriggerJS1?name=<yourname>
```  

![Resposta da função mostrada num browser.](./media/functions-create-first-azure-function-azure-cli/functions-azure-cli-function-test-curl.png)  

Se não tiver cURL disponível na sua linha de comandos, introduza o mesmo URL na barra de endereço do browser. Mais uma vez, substitua o marcador de posição `<app_name>` pelo nome da aplicação de funções, anexe a cadeia de consulta `&name=<yourname>` ao URL e execute o pedido. 

    http://<app_name>.azurewebsites.net/api/HttpTriggerJS1?name=<yourname>
   
![Resposta da função mostrada num browser.](./media/functions-create-first-azure-function-azure-cli/functions-azure-cli-function-test-browser.png)  

## <a name="clean-up-resources"></a>Limpar recursos

Outros guias de introdução desta coleção têm por base este guia de introdução. Se pretender continuar a trabalhar com guias de introdução subsequentes ou com os tutoriais, não limpe os recursos criados neste guia de introdução. Se não quiser continuar, utilize os comandos seguintes para eliminar todos os recursos criados por este guia de introdução:

```azurecli-interactive
az group delete --name myResourceGroup
```
Escreva `y` quando lhe for pedido.

## <a name="next-steps"></a>Passos seguintes

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]

---
title: "Criar a sua primeira função a partir da CLI do Azure | Microsoft Docs"
description: "Saiba como criar a sua primeira Função do Azure para execução sem servidor através da CLI do Azure."
services: functions
keywords: 
author: ggailey777
ms.author: glenga
ms.assetid: 674a01a7-fd34-4775-8b69-893182742ae0
ms.date: 05/02/2017
ms.topic: hero-article
ms.service: functions
ms.custom: mvc
ms.devlang: azure-cli
manager: erikre
ms.translationtype: Human Translation
ms.sourcegitcommit: 4f68f90c3aea337d7b61b43e637bcfda3c98f3ea
ms.openlocfilehash: 2292b35819c5a98b690041e10f6e6d1a93fa7837
ms.contentlocale: pt-pt
ms.lasthandoff: 06/20/2017

---

<a id="create-your-first-function-using-the-azure-cli" class="xliff"></a>

# Criar a sua primeira função com a CLI do Azure

Este tutorial de guia de introdução mostra-lhe como utilizar as Funções do Azure para criar a sua primeira função. Vai utilizar a CLI do Azure para criar uma aplicação Function App, que é a infraestrutura sem servidor que aloja a sua função. O código da função propriamente dito é implementado a partir de um repositório de exemplos do GitHub.    

Pode seguir os passos abaixo num computador Mac, Windows ou Linux. 

<a id="prerequisites" class="xliff"></a>

## Pré-requisitos 

Antes de executar este exemplo, tem de ter o seguinte:

+ Uma conta do [GitHub](https://github.com) ativa. 
+ Uma subscrição ativa do Azure.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se optar por instalar e usar a CLI localmente, este tópico requer a execução da versão 2.0 ou posterior da CLI do Azure. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [instalar o Azure CLI 2.0]( /cli/azure/install-azure-cli). 


<a id="create-a-resource-group" class="xliff"></a>

## Criar um grupo de recursos

Crie um grupo de recursos com o comando [az group create](/cli/azure/group#create). Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure, como aplicações Function App, bases de dados e contas de armazenamento, são implementados e geridos.

O exemplo seguinte cria um grupo de recursos com o nome `myResourceGroup`:

```azurecli-interactive
az group create --name myResourceGroup --location westeurope
```
<a id="create-an-azure-storage-account" class="xliff"></a>

## Criar uma conta de Armazenamento do Azure

As Funções utilizam uma conta de Armazenamento do Azure para guardar as informações de estado das funções, entre outras. Utilize o comando [az storage account create](/cli/azure/storage/account#create) para criar uma conta de armazenamento no grupo de recursos que criou.

No comando seguinte, substitua o nome da sua conta de armazenamento globalmente único onde vir o marcador de posição `<storage_name>`. Os nomes das contas do Storage devem ter entre 3 e 24 carateres de comprimento e apenas podem conter números e letras minúsculas.

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

<a id="create-a-function-app" class="xliff"></a>

## Criar uma aplicação de função

Precisa de uma aplicação Function App para alojar a execução das suas funções. A aplicação Function App proporciona um ambiente para a execução sem servidor do código da sua função. Permite-lhe agrupar funções como unidades lógicas para uma gestão, implementação e partilha de recursos mais fácil. Utilize o comando [az functionapp create](/cli/azure/functionapp#create) para criar uma aplicação Function App. 

No comando seguinte, substitua o nome da sua aplicação Function App exclusivo onde vir o marcador de posição `<app_name>` e o nome da conta de armazenamento para `<storage_name>`. O `<app_name>` vai ser utilizado como o domínio DNS predefinido para a aplicação Function App, daí que o nome tenha de ser exclusivo em todas as aplicações no Azure. 

```azurecli-interactive
az functionapp create --name <app_name> --storage-account  <storage_name>  --resource-group myResourceGroup --consumption-plan-location westeurope
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

<a id="deploy-your-function-code" class="xliff"></a>

## Implementar o código de função  

Existem várias formas de criar o código de função na sua aplicação Function App nova. Este tópico liga-se a um repositório de exemplos do GitHub. Tal como anteriormente, no código seguinte, substitua o marcador de posição `<app_name>` pelo nome da aplicação Function App que criou. 

```azurecli-interactive
az functionapp deployment source config --name <app_name> --resource-group myResourceGroup --repo-url https://github.com/Azure-Samples/functions-quickstart --branch master --manual-integration
```
Depois de definida a origem da implementação, a CLI do Azure mostra informações semelhantes às do exemplo seguinte (os valores nulos foram removidos, para melhorar a legibilidade):

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

<a id="test-the-function" class="xliff"></a>

## Testar a função

Utilize cURL para testar a função implementada num computador Mac ou Linux ou utilize Bash no Windows. Execute o comando cURL seguinte, substituindo o marcador de posição `<app_name>` pelo nome da sua aplicação Function App. Anexe a cadeia de consulta `&name=<yourname>` ao URL.

```bash
curl http://<app_name>.azurewebsites.net/api/HttpTriggerJS1?name=<yourname>
```  

![Resposta da função mostrada num browser.](./media/functions-create-first-azure-function-azure-cli/functions-azure-cli-function-test-curl.png)  

Se não tiver cURL disponível na sua linha de comandos, introduza simplesmente o mesmo URL na barra de endereço do browser. Mais uma vez, substitua o marcador de posição `<app_name>` pelo nome da função Function App, anexe a cadeia de consulta `&name=<yourname>` ao URL e execute o pedido. 

    http://<app_name>.azurewebsites.net/api/HttpTriggerJS1?name=<yourname>
   
![Resposta da função mostrada num browser.](./media/functions-create-first-azure-function-azure-cli/functions-azure-cli-function-test-browser.png)  

<a id="clean-up-resources" class="xliff"></a>

## Limpar recursos

Outros guias de introdução desta coleção têm por base este guia de introdução. Se pretender continuar a trabalhar com guias de introdução subsequentes ou com os tutoriais, não limpe os recursos criados neste guia de introdução. Se não quiser continuar, utilize os comandos seguintes para eliminar todos os recursos criados por este guia de introdução:

```azurecli-interactive
az group delete --name myResourceGroup
```
Escreva `y` quando lhe for pedido.

<a id="next-steps" class="xliff"></a>

## Passos seguintes

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]


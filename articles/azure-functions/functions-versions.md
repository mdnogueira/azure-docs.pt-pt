---
title: "Como destino a versões de tempo de execução das funções do Azure | Microsoft Docs"
description: "As funções suporta várias versões do tempo de execução. Saiba como especificar a versão de tempo de execução de um Azure alojado aplicação de função."
services: functions
documentationcenter: 
author: ggailey777
manager: cfowler
editor: 
ms.service: functions
ms.workload: na
ms.devlang: na
ms.topic: article
ms.date: 10/04/2017
ms.author: glenga
ms.openlocfilehash: 26d4276a0a550d78a9c7657c464bd3320c956fb0
ms.sourcegitcommit: 54fd091c82a71fbc663b2220b27bc0b691a39b5b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/12/2017
---
# <a name="how-to-target-azure-functions-runtime-versions"></a>Como destino a versões de tempo de execução das funções do Azure

O tempo de execução das funções do Azure implementa a execução do seu código sem servidor no Azure. Localizar este tempo de execução em vários ambientes diferente de alojado no Azure. O [ferramentas de núcleos de funções do Azure](functions-run-local.md) implementa este tempo de execução no computador de desenvolvimento. [Tempo de execução de funções do Azure](functions-runtime-overview.md) permite-lhe utilizar as funções em ambientes no local. 

As funções suporta várias versões principais do tempo de execução. Uma atualização de versão principal pode introduzir alterações. Este tópico descreve como pode visar as aplicações de função para uma versão de runtime específico quando está alojada no Azure. 

As funções permite-lhe uma versão específica de principais do tempo de execução de destino utilizando o `FUNCTIONS_EXTENSION_VERSION` definição da aplicação na sua aplicação de função. Isto aplica-se para ambos os público e versões de pré-visualização. A aplicação de função é mantida na versão de tempo de execução principal especificado, até decidir explicitamente mover para uma nova versão. A aplicação de função é atualizada para novas versões de secundárias do tempo de execução que ficarem disponíveis. Atualizações de versões de secundárias introduz alterações.  

Quando uma nova versão principal é publicamente disponível, é-lhe dada a oportunidade para mover para cima para essa versão quando o utilizador vê a sua aplicação de função no portal. Depois de mover para uma nova versão, é sempre possível utilizar o `FUNCTIONS_EXTENSION_VERSION` definição da aplicação para mover novamente para uma versão anterior do tempo de execução.

Cada alteração para a versão de tempo de execução faz com que a sua aplicação de função reiniciar. Notas de versão para todas as versões de tempo de execução (principais e secundárias) são publicadas no [repositório do GitHub](https://github.com/Azure/azure-webjobs-sdk-script/releases).   
## <a name="view-the-current-runtime-version"></a>Ver a versão atual do tempo de execução

Utilize o procedimento seguinte para ver a versão de runtime específico atualmente a ser utilizada pela sua aplicação de função. 

1. No [portal do Azure](https://portal.azure.com), navegue para a sua aplicação de função e, em **configurado funcionalidades**, escolha **as definições de aplicação de função**. 

    ![Selecione as definições de aplicação de função](./media/functions-versions/add-update-app-setting.png)

2. No **as definições de aplicação de função** separador, localize o **versão Runtime**. Tenha em atenção que a versão de runtime específico e a versão principal pedida. No exemplo abaixo, a versão principal está definida como `~1.0`.
 
   ![Selecione as definições de aplicação de função](./media/functions-versions/function-app-view-version.png)

## <a name="target-the-functions-version-20-runtime"></a>O tempo de execução de versão 2.0 de funções de destino

>[!IMPORTANT]   
> Tempo de execução funções do Azure 2.0 está em pré-visualização e, atualmente nem todas as funcionalidades das funções do Azure são suportadas. Para obter mais informações, consulte [problemas de tempo de execução 2.0 conhecido das funções do Azure](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Azure-Functions-runtime-2.0-known-issues)  

<!-- Add a table comparing the 1.x and 2.x runtime features-->

[!INCLUDE [functions-set-runtime-version](../../includes/functions-set-runtime-version.md)]

Pode mover a sua aplicação de função para a pré-visualização de versão 2.0 do tempo de execução no portal do Azure. No **as definições de aplicação de função** separador, escolha **beta** em **versão Runtime**.  

   ![Selecione as definições de aplicação de função](./media/functions-versions/function-app-view-version.png)

Esta definição é equivalente à definição de `FUNCTIONS_EXTENSION_VERSION` definição da aplicação para `beta`. Escolha o **~ 1** botão para mover de volta para o atual publicamente suportado versão principal. Também pode utilizar a CLI do Azure para atualizar esta definição de aplicação. 

## <a name="target-a-specific-runtime-version-from-the-portal"></a>Uma versão específica do tempo de execução do portal de destino

Quando tiver de destino de uma versão principal que não seja o principal atual versão ou a versão 2.0, tem de definir o `FUNCTIONS_EXTENSION_VERSION` definição da aplicação.

1. No [portal do Azure](https://portal.azure.com), navegue para a sua aplicação de função e, em **configurado funcionalidades**, escolha **definições da aplicação**.

    ![Selecione as definições de aplicação de função](./media/functions-versions/add-update-app-setting1a.png)

2. No **definições da aplicação** separador, localizar o `FUNCTIONS_EXTENSION_VERSION` definição e altere o valor para uma versão principal válida do tempo de execução de 1. x ou `beta` para a versão 2.0. 

    ![Configure a definição de aplicação de função](./media/functions-versions/add-update-app-setting2.png)

3. Clique em **guardar** para guardar a atualização da definição de aplicação. 

## <a name="target-a-specific-version-using-azure-cli"></a>Destino de uma versão específica a utilizar a CLI do Azure

 Também pode definir o `FUNCTIONS_EXTENSION_VERSION` partir da CLI do Azure. Utilizar a CLI do Azure, atualizar a definição da aplicação na aplicação de função com o [az functionapp configuração appsettings conjunto](/cli/azure/functionapp/config/appsettings#set) comando.

```azurecli-interactive
az functionapp config appsettings set --name <function_app> \
--resource-group <my_resource_group> \
--settings FUNCTIONS_EXTENSION_VERSION=<version>
```
Neste código, substitua `<function_app>` com o nome da sua aplicação de função. Substitua, também `<my_resource_group>` com o nome do grupo de recursos para a sua aplicação de função. Substitua `<version>` com uma versão principal válida do tempo de execução de 1. x ou `beta` para a versão 2.0. 

Pode executar este comando a partir de [Shell de nuvem do Azure](../cloud-shell/overview.md) escolhendo **experimente** no exemplo de código anterior. Também pode utilizar o [localmente a CLI do Azure](/cli/azure/install-azure-cli) para executar este comando depois de executar [início de sessão az](/cli/azure#az_login) para iniciar sessão.

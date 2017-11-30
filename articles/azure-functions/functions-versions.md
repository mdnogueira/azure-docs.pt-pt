---
title: "Como destino a versões de tempo de execução das funções do Azure"
description: "As funções do Azure suporta várias versões do tempo de execução. Saiba como especificar a versão de tempo de execução de uma aplicação de função alojada no Azure."
services: functions
documentationcenter: 
author: ggailey777
manager: cfowler
editor: 
ms.service: functions
ms.workload: na
ms.devlang: na
ms.topic: article
ms.date: 11/21/2017
ms.author: glenga
ms.openlocfilehash: 588437af80ecf60b7c4b24dbf6bccc67fc33da7a
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/29/2017
---
# <a name="how-to-target-azure-functions-runtime-versions"></a>Como destino a versões de tempo de execução das funções do Azure

Uma aplicação de função é executada numa versão específica do tempo de execução das funções do Azure. Existem duas versões principais: 1. x e 2. x. Este artigo explica como escolher qual versão principal para utilizar e como configurar uma aplicação de função no Azure com a versão escolher. Para obter informações sobre como configurar um ambiente de desenvolvimento local para uma versão específica, consulte [código e teste das funções do Azure localmente](functions-run-local.md).

## <a name="differences-between-runtime-1x-and-2x"></a>Diferenças entre o tempo de execução 1. x e 2. x

> [!IMPORTANT] 
> Tempo de execução 1. x é a única versão aprovada para utilização em produção.

| Tempo de execução | Estado |
|---------|---------|
|1. x|Geralmente disponível (GA)|
|2. x|Pré-visualização|

As secções seguintes explicam as diferenças no suporte de desenvolvimento de plataformas cruzadas, enlaces e idiomas.

### <a name="languages"></a>Linguagens

A tabela seguinte indica as linguagens de programação são suportadas em cada versão do tempo de execução.

[!INCLUDE [functions-supported-languages](../../includes/functions-supported-languages.md)]

Para obter mais informações, consulte [idiomas suportados](supported-languages.md).

### <a name="bindings"></a>Enlaces 

Tempo de execução 2 permite criar personalizado [enlace extensões](https://github.com/Azure/azure-webjobs-sdk-extensions/wiki/Binding-Extensions-Overview). Enlaces incorporados que utilizam este modelo de extensibilidade só estão disponíveis nos 2; entre o primeiro conjunto é o [enlaces Microsoft Graph](functions-bindings-microsoft-graph.md).

[!INCLUDE [Full bindings table](../../includes/functions-bindings.md)]

Para obter mais informações sobre suporte Enlaces e outras funcionais lacunas na 2. x, consulte [2.0 Runtime problemas conhecidos](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Azure-Functions-runtime-2.0-known-issues).

### <a name="cross-platform-development"></a>Desenvolvimento de plataforma

Tempo de execução 1. x suporta função desenvolvimento apenas no portal ou no Windows; com 2 pode desenvolver e executar as funções do Azure no Linux ou macOS.

## <a name="automatic-and-manual-version-updates"></a>Versão automática e manual de atualizações

As funções permite-lhe uma versão específica do tempo de execução de destino utilizando o `FUNCTIONS_EXTENSION_VERSION` definição da aplicação na aplicação de função. A aplicação de função é mantida na versão principal especificado, até decidir explicitamente mover para uma nova versão.

Se especificar apenas a versão principal ("~ 1" para 1. x) ou "beta", para 2. x, a aplicação de função é atualizada automaticamente para novas versões de secundárias do tempo de execução que ficarem disponíveis. Novas versões de secundárias introduz alterações. Se especificar uma versão secundária (por exemplo, "1.0.11360"), a aplicação de função é mantida nessa versão até que altere explicitamente. 

Quando uma nova versão é publicamente disponível, numa linha no portal do dá-lhe a oportunidade para mover para cima para essa versão. Depois de mover para uma nova versão, é sempre possível utilizar o `FUNCTIONS_EXTENSION_VERSION` definição da aplicação para mover novamente para uma versão anterior.

Uma alteração para a versão de tempo de execução faz com que uma aplicação de função reiniciar.

Os valores que pode definir no `FUNCTIONS_EXTENSION_VERSION` aplicação definição para ativar as atualizações automáticas está atualmente "~ 1" para o tempo de execução de 1. x e "beta", para 2. x.

## <a name="view-the-current-runtime-version"></a>Ver a versão atual do tempo de execução

Utilize o procedimento seguinte para ver a versão de runtime atualmente utilizada por uma aplicação de função. 

1. No [portal do Azure](https://portal.azure.com), navegue para a aplicação de função e, em **configurado funcionalidades**, escolha **as definições de aplicação de função**. 

    ![Selecione as definições de aplicação de função](./media/functions-versions/add-update-app-setting.png)

2. No **as definições de aplicação de função** separador, localize o **versão Runtime**. Tenha em atenção que a versão de runtime específico e a versão principal pedida. No exemplo abaixo, as funções\_extensão\_definição de aplicação de versão é definida como `~1`.
 
   ![Selecione as definições de aplicação de função](./media/functions-versions/function-app-view-version.png)

## <a name="target-the-version-20-runtime"></a>O tempo de execução da versão 2.0 do destino

>[!IMPORTANT]   
> Tempo de execução funções do Azure 2.0 está em pré-visualização e são suportadas atualmente nem todas as funcionalidades das funções do Azure. Para obter mais informações, consulte [diferenças entre o tempo de execução 1. x e 2](#differences-between-runtime-1x-and-2x) anteriormente neste artigo.

Pode mover uma aplicação de função para a pré-visualização de versão 2.0 do tempo de execução no portal do Azure. No **as definições de aplicação de função** separador, escolha **beta** em **versão Runtime**.  

![Selecione as definições de aplicação de função](./media/functions-versions/function-app-view-version.png)

Esta definição é equivalente à definição de `FUNCTIONS_EXTENSION_VERSION` definição da aplicação para `beta`. Escolha o **~ 1** botão para mover de volta para o atual publicamente suportado versão principal. Também pode utilizar a CLI do Azure para atualizar esta definição de aplicação. 

## <a name="target-a-version-using-the-portal"></a>Uma versão através do portal de destino

Quando precisar de uma versão diferente da versão principal atual ou a versão 2.0 de destino, tem de definir o `FUNCTIONS_EXTENSION_VERSION` definição da aplicação.

1. No [portal do Azure](https://portal.azure.com), navegue para a sua aplicação de função e, em **configurado funcionalidades**, escolha **definições da aplicação**.

    ![Selecione as definições de aplicação de função](./media/functions-versions/add-update-app-setting1a.png)

2. No **definições da aplicação** separador, localizar o `FUNCTIONS_EXTENSION_VERSION` definição e altere o valor para uma versão válida do tempo de execução de 1. x ou `beta` para a versão 2.0. 

    ![Configure a definição de aplicação de função](./media/functions-versions/add-update-app-setting2.png)

3. Clique em **guardar** para guardar a atualização da definição de aplicação. 

## <a name="target-a-version-using-azure-cli"></a>Destino de uma versão utilizando a CLI do Azure

 Também pode definir o `FUNCTIONS_EXTENSION_VERSION` partir da CLI do Azure. Utilizar a CLI do Azure, atualizar a definição da aplicação na aplicação de função com o [az functionapp configuração appsettings conjunto](/cli/azure/functionapp/config/appsettings#set) comando.

```azurecli-interactive
az functionapp config appsettings set --name <function_app> \
--resource-group <my_resource_group> \
--settings FUNCTIONS_EXTENSION_VERSION=<version>
```
Neste código, substitua `<function_app>` com o nome da sua aplicação de função. Substitua, também `<my_resource_group>` com o nome do grupo de recursos para a sua aplicação de função. Substitua `<version>` com uma versão válida do tempo de execução de 1. x ou `beta` para a versão 2.0. 

Pode executar este comando a partir de [Shell de nuvem do Azure](../cloud-shell/overview.md) escolhendo **experimente** no exemplo de código anterior. Também pode utilizar o [localmente a CLI do Azure](/cli/azure/install-azure-cli) para executar este comando depois de executar [início de sessão az](/cli/azure#az_login) para iniciar sessão.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [O tempo de execução 2.0 no seu ambiente de desenvolvimento local de destino](functions-run-local.md)

> [!div class="nextstepaction"]
> [Consulte as notas de versão para versões de tempo de execução](https://github.com/Azure/azure-webjobs-sdk-script/releases)
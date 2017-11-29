---
title: "Instale a extensão de funções durável e amostras - Azure"
description: "Saiba como instalar a extensão de funções durável para as funções do Azure, para desenvolvimento portal ou de desenvolvimento de Visual Studio."
services: functions
author: cgillum
manager: cfowler
editor: 
tags: 
keywords: 
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/29/2017
ms.author: azfuncdf
ms.openlocfilehash: 03326304e438f3b716c43d268f07d8e22cd15ea3
ms.sourcegitcommit: cf42a5fc01e19c46d24b3206c09ba3b01348966f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/29/2017
---
# <a name="install-the-durable-functions-extension-and-samples-azure-functions"></a>Instale a extensão de funções durável e amostras (funções do Azure)

O [funções durável](durable-functions-overview.md) extensão para as funções do Azure é fornecido no pacote NuGet [Microsoft.Azure.WebJobs.Extensions.DurableTask](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask). Este artigo mostra como instalar o pacote e um conjunto de exemplos para os seguintes ambientes de desenvolvimento:

* Visual Studio 2017, (recomendado) 
* Portal do Azure

## <a name="visual-studio-2017"></a>Visual Studio 2017

O Visual Studio atualmente fornece a melhor experiência para desenvolver aplicações que utilizam funções durável.  As suas funções podem ser executadas localmente e também podem ser publicadas para o Azure. Pode começar com um projeto vazio ou com um conjunto de funções de exemplo.

### <a name="prerequisites"></a>Pré-requisitos

* Instalar o [versão mais recente do Visual Studio](https://www.visualstudio.com/downloads/) (versão 15.3 ou superior). Incluir o **programação do Azure** as opções de configuração de carga de trabalho.

### <a name="start-with-sample-functions"></a>Começar a utilizar funções de exemplo

1. Transferir o [ficheiro. zip de aplicação de exemplo para o Visual Studio](https://azure.github.io/azure-functions-durable-extension/files/VSDFSampleApp.zip). Não terá de adicionar a referência do NuGet porque já tem o projeto de exemplo-lo.
2. Instalar e executar [emulador do Storage do Azure](https://docs.microsoft.com/azure/storage/storage-use-emulator) versão 5.2 ou posterior. Em alternativa, pode atualizar o *local.appsettings.json* ficheiro com cadeias de ligação reais do armazenamento do Azure.
3. Abra o projeto no Visual Studio 2017. 
4. Para obter instruções sobre como executar o exemplo, começar a utilizar [funcionar encadeamento - exemplo de sequência de Olá](durable-functions-sequence.md). O exemplo pode ser executado localmente ou publicado para o Azure.

### <a name="start-with-an-empty-project"></a>Começar com um projeto vazio
 
Siga as instruções para começar com o exemplo mesmas, mas efetuar os passos seguintes em vez de transferirem o *. zip* ficheiro:

1. Crie um projeto de aplicação de função.
2. Adicione a seguinte referência de pacote NuGet ao seu *. csproj* ficheiro:

   ```xml
   <PackageReference Include="Microsoft.Azure.WebJobs.Extensions.DurableTask" Version="1.0.0-beta" />
   ```

## <a name="azure-portal"></a>Portal do Azure

Se preferir, pode utilizar o portal do Azure para desenvolvimento de funções durável.

### <a name="create-an-orchestrator-function"></a>Criar uma função do orchestrator

1. Criar uma nova aplicação de função no [functions.azure.com](https://functions.azure.com/signin).
2. Configurar a aplicação de função para [utilizar a versão de 2.0 runtime](functions-versions.md).
3. Crie uma nova função, escolhendo "C#" para o idioma e "All" para o cenário. Em seguida, selecione o **durável funções Orchestrator - C #** modelo.
4. Em **extensões instaladas não**, clique em **instalar** para transferir a extensão de NuGet.org.

### <a name="copy-sample-code-to-the-function-app"></a>Copie o código de exemplo para a aplicação de função

1. Transferir o [DFSampleApp.zip](https://github.com/Azure/azure-functions-durable-extension/raw/master/docfx/files/DFSampleApp.zip) ficheiro.
2. Deszipe o ficheiro de exemplo para `D:\home\site\wwwroot` na aplicação de função, utilizando o Kudu ou FTP.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Executar a função de encadeamento de exemplo](durable-functions-sequence.md)

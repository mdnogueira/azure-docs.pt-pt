---
title: "Desenvolver as funções do Azure com o Visual Studio | Microsoft Docs"
description: "Saiba como desenvolver e testar as funções do Azure, utilizando ferramentas de funções do Azure para Visual Studio 2017."
services: functions
documentationcenter: .net
author: ggailey777
manager: cfowler
editor: 
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: dotnet
ms.devlang: na
ms.topic: article
ms.date: 09/06/2017
ms.author: glenga
ms.openlocfilehash: 099ff4c9748244ddaf9c12f5eb39657bf59c063b
ms.sourcegitcommit: 5a6e943718a8d2bc5babea3cd624c0557ab67bd5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/01/2017
---
# <a name="azure-functions-tools-for-visual-studio"></a>Ferramentas de funções do Azure para Visual Studio  

Ferramentas de funções do Azure para Visual Studio 2017 é uma extensão para o Visual Studio permite-lhe desenvolver, testar e implementar funções de c# no Azure. Se esta for a primeira experiência com as funções do Azure, pode saber mais em [uma introdução para as funções do Azure](functions-overview.md).

As ferramentas de funções do Azure fornece as seguintes vantagens: 

* Editar, criar e executar funções no seu computador de desenvolvimento local. 
* Publica o projeto de funções do Azure diretamente no Azure. 
* Utilize os atributos de WebJobs para declarar enlaces de funções diretamente no código c# em vez de manter um function.json separados para definições de enlace.
* Desenvolver e implementar as funções c# previamente compiladas. Funções previamente complied proporcionar um desempenho de frio início melhor que funções de baseados em script do c#. 
* O código das suas funções em c# ao ter todas as vantagens de desenvolvimento de Visual Studio. 

Este tópico mostra como utilizar as ferramentas de funções do Azure para Visual Studio 2017 para desenvolver as suas funções em c#. Também irá aprender a publicar o projeto no Azure como uma assemblagem .NET.

> [!IMPORTANT]
> Não misture desenvolvimento local com o desenvolvimento portal na mesma aplicação de função. Quando publicar a partir de um projeto local para uma aplicação de função, o processo de implementação irá substituir quaisquer funções que desenvolvidos no portal.

## <a name="prerequisites"></a>Pré-requisitos

Ferramentas de funções do Azure está incluída na carga de trabalho de desenvolvimento do Azure de [Visual Studio 2017 versão 15.4](https://www.visualstudio.com/vs/), ou uma versão posterior. Certifique-se de que inclui o **programação do Azure** carga de trabalho na instalação do Visual Studio 2017:

![Instalar o Visual Studio 2017 com a carga de trabalho de desenvolvimento do Azure](./media/functions-create-your-first-function-visual-studio/functions-vs-workloads.png)

Para criar e implementar funções, é também necessário:

* Uma subscrição ativa do Azure. Se não tiver uma subscrição do Azure, [livre contas](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) estão disponíveis.

* Uma conta de armazenamento do Azure. Para criar uma conta de armazenamento, consulte [criar uma conta de armazenamento](../storage/common/storage-create-storage-account.md#create-a-storage-account).  
## <a name="create-an-azure-functions-project"></a>Criar um projeto de funções do Azure 

[!INCLUDE [Create a project using the Azure Functions](../../includes/functions-vstools-create.md)]


## <a name="configure-the-project-for-local-development"></a>Configurar o projeto de desenvolvimento local

Quando cria um novo projeto utilizando o modelo de funções do Azure, obtenha um vazio projeto c# que contém os seguintes ficheiros:

* **Host.JSON**: permite-lhe configurar o anfitrião de funções. Estas definições aplicam-se tanto ao executar localmente e no Azure. Para obter mais informações, consulte [host.json referência](functions-host-json.md).
    
* **local.Settings.JSON**: mantém as definições utilizadas ao executar localmente a funções. Estas definições não são utilizadas pelo Azure, são utilizados pelo [ferramentas de núcleos de funções do Azure](functions-run-local.md). Utilize este ficheiro para especificar definições, tais como cadeias de ligação para outros serviços do Azure. Adicionar uma nova chave para o **valores** matriz para cada ligação de funções no seu projeto. Para obter mais informações, consulte [ficheiro de definições locais](functions-run-local.md#local-settings-file) o tópico de ferramentas de núcleos de funções do Azure.

O tempo de execução de funções utiliza uma conta de armazenamento do Azure internamente. Para acionam todos os tipos diferentes de HTTP e webhooks, tem de definir o **Values.AzureWebJobsStorage** chave para uma cadeia de ligação de conta do Storage do Azure válida.

[!INCLUDE [Note to not use local storage](../../includes/functions-local-settings-note.md)]

 Para definir a cadeia de ligação da conta de armazenamento:

1. No Visual Studio, abra **Cloud Explorer**, expanda **conta de armazenamento** > **da conta de armazenamento**, em seguida, selecione **propriedades**e copie o **cadeia de ligação principal** valor.   

2. No seu projeto, abra o ficheiro de projeto local.settings.json e defina o valor da **AzureWebJobsStorage** chave para a cadeia de ligação que copiou.

3. Repita o passo anterior para adicionar as chaves exclusivas para o **valores** matriz para ligações de necessária para as suas funções.  

## <a name="create-a-function"></a>Criar uma função

Nas funções de pré-compiladas, os enlaces utilizados pela função são definidos por aplicar atributos no código. Quando utilizar as ferramentas de funções do Azure para criar as suas funções entre os modelos fornecidos, estes atributos são aplicados por si. 

1. No **Explorador de Soluções**, clique com o botão direito do rato no nó do projeto e selecione **Adicionar** > **Novo item**. Selecione **função do Azure**, escreva um **nome** para a classe e clique em **adicionar**.

2. Escolha o acionador, definir as propriedades de enlace e clique em **criar**. O exemplo seguinte mostra as definições, quando criar um armazenamento de filas activado função. 

    ![](./media/functions-develop-vs/functions-vstools-create-queuetrigger.png)
    
    Uma chave de cadeia de ligação denominada **QueueStorage** for fornecido, que está definido no ficheiro local.settings.json. 
 
3. Examine a classe adicionada recentemente. Consulte static **executar** método, o que tem o atributo com o **FunctionName** atributo. Este atributo indica que o método é o ponto de entrada para a função. 

    Por exemplo, a classe do c# seguinte representa uma função de armazenamento acionada fila básica:

    ````csharp
    using System;
    using Microsoft.Azure.WebJobs;
    using Microsoft.Azure.WebJobs.Host;
    
    namespace FunctionApp1
    {
        public static class Function1
        {
            [FunctionName("QueueTriggerCSharp")]        
            public static void Run([QueueTrigger("myqueue-items", Connection = "QueueStorage")]string myQueueItem, TraceWriter log)
            {
                log.Info($"C# Queue trigger function processed: {myQueueItem}");
            }
        }
    } 
    ````
 
    Um atributo específico do enlace é aplicado a cada parâmetro de enlace fornecido para o método de ponto de entrada. O atributo demora as informações de enlace como parâmetros. No exemplo anterior, o primeiro parâmetro tem um **QueueTrigger** atributo aplicado, que indica que a função de acionada de fila. O nome da fila e o nome de definição da cadeia de ligação são transmitidos como parâmetros.  

## <a name="testing-functions"></a>Testar funções

As Ferramentas de Núcleo das Funções do Azure permitem-lhe executar o projeto de funções do Azure no seu computador de programação local. Deverá instalar essas ferramentas da primeira vez que iniciar uma função do Visual Studio.  

Para testar a sua função, prima F5. Se solicitado, aceite o pedido do Visual Studio para transferir e instalar as ferramentas de Núcleo das Funções do Azure (CLI).  Também poderá ativar a exceção da firewall para que todas as ferramentas possam aceitar os pedidos de HTTP.

Com o projeto em execução, pode testar o seu código como seria testar a função implementada. Para obter mais informações, consulte [estratégias para testar o seu código das funções do Azure](functions-test-a-function.md). Quando em execução no modo de depuração, pontos de interrupção são atingidos no Visual Studio, conforme esperado. 

Para obter um exemplo de como uma função de acionada de fila de teste, consulte o [tutorial de início rápido de função de fila acionada](functions-create-storage-queue-triggered-function.md#test-the-function).  

Para saber mais sobre como utilizar as ferramentas de núcleos de funções do Azure, consulte [código e testar as funções do Azure localmente](functions-run-local.md).

## <a name="publish-to-azure"></a>Publicar no Azure

[!INCLUDE [Publish the project to Azure](../../includes/functions-vstools-publish.md)]

>[!NOTE]  
>As definições que adicionou no local.settings.json tem de ser adicionadas também para a aplicação de função no Azure. Estas definições não são automaticamente adicionadas. Pode adicionar as definições necessárias para a sua aplicação de função de uma das seguintes formas:
>
>* [No portal do Azure](functions-how-to-use-azure-function-app-settings.md#settings).
>* [Utilizar o `--publish-local-settings` publicar opção nas ferramentas de núcleos de funções do Azure](functions-run-local.md#publish).
>* [Utilizar a CLI do Azure](/cli/azure/functionapp/config/appsettings#set). 

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre as ferramentas de funções do Azure, consulte a secção perguntas comuns a [as ferramentas de 2017 do Visual Studio para as funções do Azure](https://blogs.msdn.microsoft.com/webdev/2017/05/10/azure-function-tools-for-visual-studio-2017/) blogue.

Para saber mais sobre as ferramentas de núcleos de funções do Azure, consulte [código e testar as funções do Azure localmente](functions-run-local.md).  
Para obter mais informações sobre o desenvolvimento de funções como bibliotecas de classes do .NET, veja [Utilizar bibliotecas de classes do .NET com as Funções do Azure](functions-dotnet-class-library.md). Este tópico também fornece exemplos de como utilizar atributos para declarar vários tipos de enlaces suportadas funções do Azure.    

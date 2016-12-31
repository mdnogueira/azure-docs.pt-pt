---
title: "Introdução ao Armazenamento do Azure em cinco minutos| Microsoft Docs"
description: "De forma rápida, conhecer melhor os Blobs do Microsoft Azure, as Tabelas e as Filas através dos Inícios Rápidos do Storage do Azure, do Visual Studio e do emulador do Storage do Azure. Execute a sua primeira aplicação do Storage do Azure em cinco minutos."
services: storage
documentationcenter: .net
author: mmacy
manager: timlt
editor: tysonn
ms.assetid: 582f76f8-c814-4a69-8a5c-1fd0e0d5d8f2
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: get-started-article
ms.date: 12/08/2016
ms.author: marsma
translationtype: Human Translation
ms.sourcegitcommit: 12ce6b6bccf3ea2aa2945ddd775716f29cf01e1f
ms.openlocfilehash: 47b2623eb3b83220ef8e3cfafde06dab3ac3d22e


---
# <a name="get-started-with-azure-storage-in-five-minutes"></a>Introdução ao Storage do Azure em cinco minutos
## <a name="overview"></a>Descrição geral
É fácil começar a programar com o Storage do Azure. Este tutorial demonstra como colocar uma aplicação do Storage do Azure em funcionamento rapidamente. Irá utilizar os modelos de Início Rápido incluídos com o Azure SDK para o .NET. Estes Inícios Rápidos contêm código pronto para execução que demonstra alguns cenários de programação básicos com o Storage do Azure.

Para saber mais sobre o Storage do Azure antes de explorar o código, consulte o artigo [Passos Seguintes](#next-steps).

## <a name="prerequisites"></a>Pré-requisitos
Terá de seguir os seguintes pré-requisitos seguintes antes de começar:

1. Para compilar e criar a aplicação, precisará de uma versão do [Visual Studio](https://www.visualstudio.com/) instalado no seu computador.
2. Instale a versão mais recente do [Azure SDK para .NET](https://azure.microsoft.com/downloads/). O SDK inclui os projetos de exemplo de Início Rápido do Azure, o emulador do Storage do Azure e a [Biblioteca de Clientes do Storage do Azure para .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx).
3. Certifique-se de que tem o [.NET Framework 4.5](http://www.microsoft.com/download/details.aspx?id=30653) instalado no seu computador, uma vez que é necessário pelos projetos de exemplo de Início Rápido do Azure que iremos utilizar neste tutorial.

    Se não tem a certeza de qual é a versão do .NET Framework que está instalada no seu computador, consulte o artigo [Como: Determinar Que Versões do .NET Framework Estão Instaladas](https://msdn.microsoft.com/vstudio/hh925568.aspx). Em alternativa, prima o botão **Iniciar** ou a tecla Windows e escreva **Painel de Controlo**. Em seguida, clique em **Programas** > **Programas e Funcionalidades** e verifique se o .NET Framework 4.5 está listado entre os programas instalados.
4. Precisa de uma subscrição do Azure e uma conta do Storage do Azure.

   * Para obter uma subscrição do Azure, consulte [Versão de Avaliação Gratuita](https://azure.microsoft.com/pricing/free-trial/), [Opções de Compra](https://azure.microsoft.com/pricing/purchase-options/) e [Ofertas de Membros](https://azure.microsoft.com/pricing/member-offers/) (para os membros da MSDN, Microsoft Partner Network, BizSpark e outros programas Microsoft).
   * Para criar uma conta do Storage no Azure, consulte o artigo [Como criar uma conta do Storage](storage-create-storage-account.md#create-a-storage-account).

## <a name="run-your-first-azure-storage-application-against-azure-storage-in-the-cloud"></a>Executar a sua primeira aplicação do Storage do Azure relativamente ao Storage do Azure na nuvem
Assim que tiver uma conta, pode criar uma aplicação do Storage do Azure simples utilizando um dos projetos de exemplo de Inícios Rápidos do Azure no Visual Studio. Este tutorial centra-se nos projetos de exemplo para o Storage do Azure: **Storage do Azure: Blobs**, **Storage do Azure: Ficheiros**, **Storage do Azure: Filas** e **Storage do Azure: Tabelas**:

1. Inicie o Visual Studio.
2. No menu **Ficheiro**, clique em **Novo Projeto**.
3. Na caixa de diálogo **Novo Projeto**, clique em **Instalado** > **Modelos** > **Visual C#** > **Nuvem** > **Inícios Rápidos** > **Serviços de Dados**.
    a. Escolha um dos seguintes modelos: **Storage do Azure: Blobs**, **Storage do Azure: Ficheiros**, **Storage do Azure: Filas** ou **Storage do Azure: Tabelas**.
    b. Certifique-se de que **.NET Framework 4.5** é selecionada como o framework de destino.
    c. Especifique um nome para o seu projeto e crie a nova solução do Visual Studio, conforme mostrado:

    ![Inícios Rápidos do Azure][Image1]

Poderá pretender rever o código fonte antes de executar a aplicação. Para rever o código, selecione **Explorador de Soluções** no menu **Vista** no Visual Studio. Em seguida, faça duplo clique no ficheiro Program.cs.

Em seguida, execute a aplicação de exemplo:

1. No Visual Studio, selecione **Explorador de Soluções** no menu **Vista**. Abra o ficheiro App.config e comente a cadeia de ligação para o emulador do Storage do Azure:

   `<!--<add key="StorageConnectionString" value = "UseDevelopmentStorage=true;"/>-->`

2. Anule os comentários da cadeia de ligação para o Serviço do Storage do Azure e forneça a chave de acesso e o nome da conta de armazenamento no ficheiro App.config:

   `<add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=[AccountName];AccountKey=[AccountKey]"`

   Para obter a sua chave de acesso da conta do Storage, consulte o artigo [Gerir as chaves de acesso de armazenamento](storage-create-storage-account.md#manage-your-storage-access-keys).
3. Depois de fornecer a chave de acesso e o nome da conta do Storage no ficheiro App.config, no menu **Ficheiro**, clique em **Guardar Tudo** para guardar todos os ficheiros do projeto.
4. No menu **Construir**, clique em **Construir Solução**.
5. No menu **Depuração**, prima **F11** para executar a solução passo-a-passo ou prima **F5** para executar a solução.

## <a name="run-your-first-azure-storage-application-locally-against-the-azure-storage-emulator"></a>Executar a sua primeira aplicação do Storage do Azure localmente relativamente ao Emulador do Storage do Azure
O [Emulador do Storage do Azure](storage-use-emulator.md) fornece um ambiente local que emula os serviços Tabela, Fila e Blob do Azure para fins de desenvolvimento. Pode utilizar o emulador de armazenamento para testar a sua aplicação de armazenamento localmente, sem criar uma subscrição do Azure ou uma conta do Storage e sem incorrer em qualquer custo.

Para o tentar, vamos criar uma aplicação do Storage do Azure simples utilizando um dos projetos de exemplo de Inícios Rápidos do Azure no Visual Studio. Este tutorial centra-se nos projetos de exemplo **Blob Storage do Azure**, **Table Storage do Azure** e **Armazenamento de Filas do Azure**:

1. Inicie o Visual Studio.
2. No menu **Ficheiro**, clique em **Novo Projeto**.
3. Na caixa de diálogo **Novo Projeto**, clique em **Instalado** > **Modelos** > **Visual C#** > **Nuvem** > **Inícios Rápidos** > **Serviços de Dados**.
    a. Escolha um dos seguintes modelos: **Storage do Azure: Blobs**, **Storage do Azure: Ficheiros**, **Storage do Azure: Filas** ou **Storage do Azure: Tabelas**.
    b. Certifique-se de que **.NET Framework 4.5** é selecionada como o framework de destino.
    c. Especifique um nome para o seu projeto e crie a nova solução do Visual Studio, conforme mostrado:

    ![Inícios Rápidos do Azure][Image1]

4. No Visual Studio, selecione **Explorador de Soluções** no menu **Vista**. Abra o ficheiro App.config e comente a cadeia de ligação para a conta de armazenamento do Azure, se já tiver adicionado uma. Em seguida, anule os comentários da cadeia de ligação para o emulador do Storage do Azure:

   `<add key="StorageConnectionString" value = "UseDevelopmentStorage=true;"/>`

Poderá pretender rever o código fonte antes de executar a aplicação. Para rever o código, selecione **Explorador de Soluções** no menu **Vista** no Visual Studio. Em seguida, faça duplo clique no ficheiro Program.cs.

Em seguida, execute o exemplo de aplicação no Emulador do Storage do Azure:

1. Prima o botão **Iniciar** ou a tecla Windows, procure *Emulador de Armazenamento do Microsoft Azure* e inicie a aplicação. Quando o emulador inicia, verá um ícone e uma notificação na área de Vista de Tarefas do Windows.
2. No Visual Studio, clique em **Construir Solução** no menu **Construir**.
3. No menu **Depuração**, prima **F11** para executar a solução passo-a-passo ou prima **F5** para executar a solução do início ao fim.

## <a name="next-steps"></a>Passos Seguintes
Consulte estes recursos para saber mais sobre o Storage do Azure:

* [Introdução ao Armazenamento do Microsoft Azure](storage-introduction.md)
* [Introdução ao Explorador do Armazenamento do Azure](../vs-azure-tools-storage-manage-with-storage-explorer.md)
* [Introdução ao armazenamento de Blobs do Azure através do .NET](storage-dotnet-how-to-use-blobs.md)
* [Introdução ao armazenamento de Tabelas do Azure através do .NET](storage-dotnet-how-to-use-tables.md)
* [Introdução ao Armazenamento de Filas do Azure através do .NET](storage-dotnet-how-to-use-queues.md)
* [Introdução ao Armazenamento de Ficheiros do Azure no Windows](storage-dotnet-how-to-use-files.md)
* [Transferir dados com o Utilitário de Linha de Comandos AzCopy](storage-use-azcopy.md)
* [Documentação do Armazenamento do Azure](https://azure.microsoft.com/documentation/services/storage/)
* [Biblioteca de Clientes do Armazenamento do Microsoft Azure para o .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx)
* [API REST dos Serviços do Armazenamento do Azure](https://msdn.microsoft.com/library/azure/dd179355.aspx)

[Image1]: ./media/storage-getting-started-guide/QuickStart.png



<!--HONumber=Dec16_HO2-->



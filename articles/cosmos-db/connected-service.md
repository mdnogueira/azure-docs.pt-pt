---
title: "Serviço Ligado do Visual Studio para o Azure Cosmos DB"
description: "Permite aos programadores ligar facilmente a respetiva conta do Azure Cosmos DB e gerir recursos através dos Serviços Ligados do Visual Studio"
services: cosmos-db
documentationcenter: 
author: jejiang
manager: DJ
+tags: cosmos-db
editor: Jenny Jiang
ms.assetid: 
ms.service: cosmos-db
ms.custom: Azure Cosmos DB
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 09/19/2017
ms.author: jejiang
ms.openlocfilehash: de0c83e4c99894f98de18eb089ce11cdf5c70f2e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-cosmos-db-visual-studio-connected-service-preview"></a>Azure Cosmos DB: Serviço Ligado do Visual Studio (Pré-visualização)

Os Serviços Ligados do Visual Studio permitem aos programadores ligar facilmente a respetiva conta do Azure Cosmos DB e gerir os seus recursos.

Também pode utilizar o Data Explorer no Serviço Ligado para criar procedimentos armazenados, UDFs e acionadores, para realizar lógica de negócio do lado do servidor. O Data Explorer expõe todos os acessos a dados programáticos incorporados que estão disponíveis nas APIs, mas proporciona acesso fácil aos seus dados.

## <a name="prerequisites"></a>Pré-requisitos

Certifique-se de que tem os itens seguintes:

* Uma conta ativa do Azure. Se não tiver uma, pode inscrever-se numa [conta gratuita](https://azure.microsoft.com/free/). 
* Uma conta do Azure Cosmos DB. Se ainda não tiver uma, siga os passos indicados em [criar uma conta do Azure Cosmos DB](create-documentdb-dotnet.md) para criar uma conta no portal do Azure ou veja [Criar uma conta do Azure Cosmos DB na ferramenta Serviço Ligado](#Create-an-Azure-Cosmo-DB-account-in-Connected-Service-tool). 
* Se pretender utilizar um ambiente local para fins de desenvolvimento, pode utilizar o [Emulador do Azure Cosmos DB](local-emulator.md). O ambiente emula o serviço Azure Cosmos DB.
* [Visual Studio](http://www.visualstudio.com/).
* O bits mais recente do Serviço Ligado do Azure Cosmos DB. Pode transferir o Serviço Ligado do Azure Cosmos DB a partir do marketplace do Visual Studio, tal como mostra a captura de ecrã seguinte. Abra o **Visual Studio 2017** no seu computador. No menu **Ferramentas**, selecione **Extensões e Atualizações...** e escolha **Online** / **Visual Studio Marketplace**. Introduza **cosmosdb** para procurar o bits.

    Também pode instalar o Serviço Ligado do Azure Cosmos DB a partir do [Visual Studio Marketplace](https://go.microsoft.com/fwlink/?linkid=858709).

    ![Captura de ecrã do bits.png de transferência do Serviço Ligado](./media/connected-service/connected-service-downloadbits.png) 

## <a id="SetupVS"></a>Configurar a sua solução Visual Studio
1. Abra o **Visual Studio** no seu computador.
2. No menu **Ficheiro**, selecione **Novo**, e, em seguida, escolha **Projeto**.
3. Na caixa de diálogo **Novo Projeto**, selecione **Visual C#** / **Console App (.NET Framework)** ou **WPF App (.NET Framework)**, atribua um nome ao projeto e clique em **OK**.

    ![Captura de ecrã da janela Novo Projeto](./media/connected-service/connected-service-new-project.png)
    
## <a name="add-connected-service-and-add-account"></a>Adicionar o Serviço Ligado e adicionar uma conta
1. No Explorador de Soluções, clique com o botão direito do rato no nó do projeto e selecione **Adicionar** / **Serviço Ligado**. Em alternativa, clique no menu **Projeto** e selecione **Adicionar Serviço Ligado**.

    ![Captura de ecrã da janela Adicionar Serviço Ligado](./media/connected-service/connected-service-add-connectedservice-rightclick.png)
2. Na página do serviço ligado, clique em **Serviços Ligados** / **Azure Cosmos DB** para abrir a página **Azure Cosmos DB**.

    ![Captura de ecrã da janela Azure Cosmos DB](./media/connected-service/connected-service-choose-azure-cosmosdb.png)
3. Clique na seta para baixo para iniciar sessão pela primeira vez ou adicionar uma conta. Após o início de sessão, todas as contas do Azure Cosmos DB são apresentadas na área em branco. Escolha uma conta do Azure Cosmos DB para adicionar ao seu projeto.

    ![Captura de ecrã da janela de início de sessão e da conta de db listada](./media/connected-service/connected-service-add-db-account.png)
4. Depois de adicionar uma conta do Azure Cosmos DB, uma pasta do serviço ligado da conta do Azure Cosmos DB é adicionada ao projeto. Pode adicionar mais do que uma conta do Azure Cosmos DB, repetindo o passo 1 até ao passo 3.

    ![Captura de ecrã da janela da pasta do serviço ligado](./media/connected-service/connected-service-add-connectedservice-folder.png)

5. Depois de adicionar um serviço ligado do Azure Cosmos DB, modifique o projeto para permitir o acesso ao Azure Cosmos DB de uma das seguintes formas:

    * Alguns pacotes nuget que são necessários ao cliente Azure Cosmos DB são instalados. Pode vê-los a partir do ficheiro de configuração de pacotes. 

        ![O novo ficheiro de configuração de pacotes do Azure Cosmos DB](./media/connected-service/connected-service-packages-config.png)   
    
    * A chave e o URI de ligação do Azure Cosmos DB são adicionados ao ficheiro de configuração do projeto, neste caso, App.config. 

        ![O novo ficheiro App.config do Azure Cosmos DB](./media/connected-service/connected-service-app-config.png) 

## <a name="open-azure-cosmos-db-explorer"></a>Abrir o Explorador do Azure Cosmos DB
1. Clique com o botão direito do rato no nó do projeto e selecione **Abrir o Explorador do Cosmos DB...**.

    ![Captura de ecrã da janela Abrir o Data Explorer](./media/connected-service/connected-service-right-click-open-data-exporer.png)
2. Na página **Escolher uma conta do Cosmos DB**, clique na lista pendente para selecionar uma conta do Azure Cosmos DB.

    ![Captura de ecrã da janela Serviço Ligado da Conta Adicionada](./media/connected-service/connected-service-open-explorer.png)
3. Clique em **Abrir** e, em seguida, é apresentada a janela do explorador de dados.

## <a id="Create-an-Azure-Cosmo-DB-account-in-Connected-Service-tool"></a>Criar uma conta do Azure Cosmos DB na ferramenta Serviço Ligado
1. Na página do serviço ligado, na parte inferior esquerda do painel, clique em **Criar uma Nova Conta do Cosmos DB** para abrir a página **Criar Conta do Cosmos DB**.

    ![Captura de ecrã da janela de ponto de entrada Criar Conta do Azure Cosmos DB aberta](./media/connected-service/connected-service-click-new-db-account.png)
2. Na página **Criar Conta do Cosmos DB**, especifique a configuração que pretende para esta conta do Azure Cosmos DB.

    Preencha os campos na página **Criar Conta do Cosmos DB**, tendo como referência as informações indicadas na captura de ecrã seguinte. 
 
    ![A nova página do Azure Cosmos DB](./media/connected-service/connected-service-create-new-account.png)        
3. Clique em **Criar** para criar a conta.

## <a name="use-data-explorer"></a>Utilizar o Data Explorer

Depois de abrir o Data Explorer, pode efetuar o seguinte:
* Criar e eliminar bases de dados
* Criar e eliminar coleções
* Criar, eliminar e filtrar documentos
* Criar e eliminar procedimentos armazenados
* Criar e eliminar acionadores e funções definidas pelo utilizador para realizar a lógica de negócio do lado do servidor. 

![A nova página do Azure Cosmos DB](./media/connected-service/connected-service-dataexplorerui.png)

## <a name="demo"></a>Demonstração

Veja o vídeo seguinte para ver como utilizar o Serviço Ligado do Azure Cosmos DB no Visual Studio: [Utilizar o Serviço Ligado do Azure Cosmos DB no Visual Studio](https://go.microsoft.com/fwlink/?linkid=858711)

## <a name="next-steps"></a>Passos seguintes
Neste documento, aprendeu os seguintes itens:

> [!div class="checklist"]
> * Criar uma conta do Azure Cosmos DB
> * Adicionar o Serviço Ligado e adicionar uma conta
> * Abrir o Explorador do Azure Cosmos DB
> * Utilizar o Data Explorer

Agora que já tem os Serviços Ligados operacionais com a sua conta do Azure Cosmos DB, avance para um dos tutoriais para começar a desenvolver a sua solução:

* [Desenvolver com a API de DocumentDB no .NET](tutorial-develop-documentdb-dotnet.md).
* [Azure Cosmos DB: Tutorial de introdução à API de DocumentDB](documentdb-get-started.md).
* Pretende testar o dimensionamento e desempenho com o Azure Cosmos DB? Veja o artigo [Performance and Scale Testing with Azure Cosmos DB (Testar o Desempenho e o Dimensionamento com o Azure Cosmos DB)](performance-testing.md).
* Saiba como [Monitorizar uma conta do Azure Cosmos DB](monitor-accounts.md).


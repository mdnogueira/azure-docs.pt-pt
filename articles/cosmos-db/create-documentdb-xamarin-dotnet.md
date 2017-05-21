---
title: "Azure Cosmos DB: criar uma aplicação Web com Xamarin e a autenticação do Facebook | Microsoft Docs"
description: "Apresenta um exemplo de código .NET que pode utilizar para ligar e consultar o Azure Cosmos DB"
services: cosmosdb
documentationcenter: 
author: mimig1
manager: jhubbard
editor: 
ms.assetid: 
ms.service: cosmosdb
ms.custom: quick start connect
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 05/10/2017
ms.author: mimig
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 80aa96973799584118125829f9479fbd14431ac0
ms.contentlocale: pt-pt
ms.lasthandoff: 05/10/2017


---
# <a name="azure-cosmos-db-build-a-web-app-with-net-xamarin-and-facebook-authentication"></a>Azure Cosmos DB: criar uma aplicação Web com .NET, Xamarin e a autenticação do Facebook

O Azure Cosmos DB é um serviço de bases de dados com vários modelos e distribuído globalmente. Pode criar e consultar rapidamente o documento, a chave/valor e as bases de dados de gráficos, que beneficiam de capacidades de escalamento horizontal e distribuição global no centro do Azure Cosmos DB. 

Este guia de introdução demonstra como criar uma conta do Azure Cosmos DB, bases de dados de documentos e coleções com o portal do Azure. Depois, vai criar e implementar uma aplicação Web de Lista A Fazer criada na [DocumentDB .NET API](../documentdb/documentdb-sdk-dotnet.md), em [Xamarin](https://www.xamarin.com/) e no motor de autorização do Azure Cosmos DB. A aplicação Web de Lista A Fazer implementa um padrão de dados por utilizador que permite aos utilizadores iniciar sessão com a Autorização do Facebook e gerir os itens A Fazer deles.

## <a name="prerequisites"></a>Pré-requisitos

Se ainda não tiver o Visual Studio 2017 instalado, pode transferir e utilizar a [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/) **gratuita**. Confirme que ativa o **desenvolvimento do Azure** durante a configuração do Visual Studio.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-database-account"></a>Criar uma conta de base de dados

[!INCLUDE [documentdb-create-dbaccount](../../includes/documentdb-create-dbaccount.md)]

## <a name="add-a-collection"></a>Adicionar uma coleção

[!INCLUDE [cosmosdb-create-collection](../../includes/cosmosdb-create-collection.md)]

## <a name="clone-the-sample-application"></a>Clonar a aplicação de exemplo

Agora, vamos clonar uma aplicação de DocumentDB a partir do GitHub, definir a cadeia de ligação e executá-la. Vai ver como é fácil trabalhar com dados programaticamente. 

1. Abra uma janela de terminal do git, tal como git bash, e `cd` para um diretório de trabalho.  

2. Execute o seguinte comando para clonar o repositório de exemplo. 

    ```bash
    git clone https://github.com/Azure/azure-documentdb-dotnet.git
    ```

3. Em seguida, abra o ficheiro DocumentDBTodo.sln na pasta samples/xamarin/UserItems/xamarin.forms no Visual Studio. 

## <a name="review-the-code"></a>Rever o código

O código na pasta do Xamarin contém:

* A aplicação Xamarin. A aplicação armazena os itens a fazer do utilizador numa coleção particionada, chamada UserItems.
* API do mediador de tokens de recurso. Uma API WEB ASP.NET simples para mediar tokens de recursos do Azure Cosmos DB para os utilizadores da aplicação com sessão iniciada. Os tokens de recursos são tokens de acesso de curta duração que dão à aplicação acesso aos dados do utilizador com sessão iniciada.

O fluxo da autenticação e dos dados está ilustrado no diagrama abaixo.

* A coleção UserItems é criada com a chave de partição “'/userid”. Indicar uma chave de partição para uma coleção permite ao Azure Cosmos DB dimensionar-se indefinidamente, à medida que o número de utilizadores e itens aumenta.
* A aplicação Xamarin permite aos utilizadores iniciar sessão com credenciais do Facebook.
* A aplicação Xamarin utiliza o token de acesso do Facebook para se autenticar com a ResourceTokenBroker API
* A API do mediador do token de recursos autentica o pedido com a funcionalidade Autenticação do Serviço de Aplicações e pede um token de recursos do Azure Cosmos DB com acesso de leitura/escrita a todos os documentos que partilhem a chave de partição do utilizador autenticado.
* O mediador do token de recursos devolve o token de recursos à aplicação cliente.
* A aplicação acede aos itens a fazer to utilizador com o token de recursos.

![Aplicação de Lista A Fazer com dados de exemplo](./media/create-documentdb-xamarin-dotnet/tokenbroker.png)
    
## <a name="update-your-connection-string"></a>Atualizar a cadeia de ligação

Agora, regresse ao portal do Azure para obter as informações da cadeia de ligação e copie-as para a aplicação.

1. No [portal do Azure](http://portal.azure.com/), na sua conta do Azure Cosmos DB, na navegação da esquerda, clique em **Chaves** e em **Chaves de leitura/escrita**. Vai utilizar os botões de copiar no lado direito do ecrã para copiar o URI e a Chave Primária para o ficheiro web.config no próximo passo.

    ![Ver e copiar uma chave de acesso no portal do Azure, painel Chaves](./media/create-documentdb-xamarin-dotnet/keys.png)

2. No Visual Studio 2017, abra o ficheiro web.config na pasta azure-documentdb-dotnet/samples/xamarin/UserItems/ResourceTokenBroker/ResourceTokenBroker. 

3. Copie o valor do URI a partir do portal (com o botão Copiar) e faça deste o valor da chave do accountUrl em web.config. 

    `<add key="accountUrl" value="{Azure Cosmos DB account URL}"/>`

4. Em seguida, copie o valor de CHAVE PRIMÁRIA do portal e faça do mesmo o valor de accountKey em web.config. 

    `<add key="accountKey" value="{Azure Cosmos DB secret}"/>`

Atualizou agora a sua aplicação com todas as informações necessárias para comunicar com o Azure Cosmos DB. 

## <a name="build-and-deploy-the-web-app"></a>Criar e implementar a aplicação Web

1. No portal do Azure, crie um Web site do Serviço de Aplicações para alojar a API do mediador de token de recursos.
2. No portal do Azure, abra o painel Definições da Aplicação do Web site da API do mediador de token de recursos. Preencha as definições da aplicação seguintes:

    * accountUrl - o URL da conta do Azure Cosmos DB no separador Chaves da sua conta do Azure Cosmos DB.
    * accountKey - a chave mestra da conta do Azure Cosmos DB do separador Chaves da sua conta do Azure Cosmos DB.
    * databaseId e collectionId da base de dados e da coleção que criou.

3. Publique a solução ResourceTokenBroker no Web site que criou.

4. Abra o projeto Xamarin e navegue até TodoItemManager.cs. Preencha os valores para accountURL, collectionId, databaseId e resourceTokenBrokerURL como o url https base do Web site do mediador de token de recursos.

5. Conclua o tutorial [How to configure your App Service application to use Facebook login](../app-service-mobile/app-service-mobile-how-to-configure-facebook-authentication.md) (Como configurar a sua aplicação do Serviço de Aplicações para utilizar o início de sessão do Facebook) para configurar a autenticação do Facebook e o Web site ResourceTokenBroker.

    Execute a aplicação Xamarin.

## <a name="review-slas-in-the-azure-portal"></a>Rever os SLAs no portal do Azure

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmosdb-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Limpar recursos

Se não pretender continuar a utilizar esta aplicação, elimine todos os recursos criados com este guia de introdução no portal do Azure com os seguintes passos: 

1. No menu do lado esquerdo no portal do Azure, clique em **Grupos de recursos** e, em seguida, clique no nome do recurso que acabou de criar. 
2. Na página do grupo de recursos, clique em **Eliminar**, escreva o nome do recurso a eliminar na caixa de texto e, em seguida, clique em **Eliminar**.

## <a name="next-steps"></a>Passos seguintes

Neste guia rápido, aprendeu a criar uma conta do Azure Cosmos DB, a criar uma coleção com o Data Explorer e criar e implementar uma aplicação Xamarin. Agora, pode importar dados adicionais à sua conta do Cosmos DB. 

> [!div class="nextstepaction"]
> [Import data into Azure Cosmos DB](../documentdb/documentdb-import-data.md) (Importar dados para o Azure Cosmos DB).


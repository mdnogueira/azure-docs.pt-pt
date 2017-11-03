---
title: "Ativar a sincronização offline da aplicação móvel de Azure (Cordova) | Microsoft Docs"
description: "Saiba como utilizar a aplicação do serviço de aplicações móveis para sincronização e a cache de dados offline na sua aplicação Cordova"
documentationcenter: cordova
author: ggailey777
manager: syntaxc4
editor: 
services: app-service\mobile
ms.assetid: 1a3f685d-f79d-4f8b-ae11-ff96e79e9de9
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-cordova-ios
ms.devlang: dotnet
ms.topic: article
ms.date: 10/30/2016
ms.author: glenga
ms.openlocfilehash: 45e80ca672dfdb6defc6e5c1aac3d29f5479125c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="enable-offline-sync-for-your-cordova-mobile-app"></a>Ativar a sincronização offline para a sua aplicação móvel Cordova
[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

Este tutorial apresenta a funcionalidade de sincronização offline de Mobile Apps do Azure para Cordova. Sincronização offline permite que os utilizadores finais interajam com uma aplicação móvel&mdash;visualizar, adicionar ou modificar dados&mdash;, mesmo quando não existe nenhuma ligação de rede. As alterações são armazenadas na base de dados local.  Assim que o dispositivo estiver novamente online, estas alterações são sincronizadas com o serviço remoto.

Este tutorial baseia-se na solução de início rápido de Cordova para Mobile Apps que criar quando concluir o tutorial [início rápido do Apache Cordova]. Neste tutorial, atualize a solução de início rápido para adicionar funcionalidades offline de Mobile Apps do Azure.  Podemos também realçar o código de offline específicos na aplicação.

Para saber mais sobre a funcionalidade de sincronização offline, consulte o tópico [sincronização de dados Offline no Mobile Apps do Azure]. Para detalhes de utilização da API, consulte o [documentação da API](https://azure.github.io/azure-mobile-apps-js-client).

## <a name="add-offline-sync-to-the-quickstart-solution"></a>Adicionar a sincronização offline para a solução de início rápido
O código de sincronização offline tem de ser adicionado à aplicação. Sincronização offline requer o plug-in cordova-sqlite-storage, que obtém adicionado automaticamente à sua aplicação quando o plug-in de Mobile Apps do Azure está incluído no projeto. O projeto de início rápido inclui ambas estas plug-ins.

1. No Explorador de soluções do Visual Studio, abra index.js e substitua o código seguinte

        var client,            // Connection to the Azure Mobile App backend
           todoItemTable;      // Reference to a table endpoint on backend

    com este código:

        var client,            // Connection to the Azure Mobile App backend
           todoItemTable,      // Reference to a table endpoint on backend
           syncContext;        // Reference to offline data sync context

2. Em seguida, substitua o código seguinte:

        client = new WindowsAzure.MobileServiceClient('http://yourmobileapp.azurewebsites.net');

    com este código:

        client = new WindowsAzure.MobileServiceClient('http://yourmobileapp.azurewebsites.net');
        var store = new WindowsAzure.MobileServiceSqliteStore('store.db');

        store.defineTable({
          name: 'todoitem',
          columnDefinitions: {
              id: 'string',
              text: 'string',
              complete: 'boolean',
              version: 'string'
          }
        });

        // Get the sync context from the client
        syncContext = client.getSyncContext();

    As adições de código anterior inicializar o arquivo local e definir uma tabela local que corresponda os valores da coluna utilizados no seu Azure back-end. (Não precisa de incluir todos os valores de coluna neste código.)  O `version` campo é mantido através do back-end móvel e é utilizado para resolução de conflitos.

    Obter uma referência para o contexto de sincronização ao chamar **getSyncContext**. O contexto de sincronização ajuda a manter relações de tabela de controlo e enviar as alterações em todas as tabelas modificou uma aplicação de cliente quando `.push()` é chamado.

3. Atualize o URL da aplicação para o URL da aplicação de aplicação móvel.

4. Em seguida, substitua este código:

        todoItemTable = client.getTable('todoitem'); // todoitem is the table name

    com este código:

        // Initialize the sync context with the store
        syncContext.initialize(store).then(function () {

        // Get the local table reference.
        todoItemTable = client.getSyncTable('todoitem');

        syncContext.pushHandler = {
            onConflict: function (pushError) {
                // Handle the conflict.
                console.log("Sync conflict! " + pushError.getError().message);
                // Update failed, revert to server's copy.
                pushError.cancelAndDiscard();
              },
              onError: function (pushError) {
                  // Handle the error
                  // In the simulated offline state, you get "Sync error! Unexpected connection failure."
                  console.log("Sync error! " + pushError.getError().message);
              }
        };

        // Call a function to perform the actual sync
        syncBackend();

        // Refresh the todoItems
        refreshDisplay();

        // Wire up the UI Event Handler for the Add Item
        $('#add-item').submit(addItemHandler);
        $('#refresh').on('click', refreshDisplay);

    O código anterior inicializa o contexto de sincronização e, em seguida, chama getSyncTable (em vez de getTable) para obter uma referência à tabela local.

    Utiliza este código base de dados local para todos os criar, ler, atualiza e eliminar as operações de tabela (CRUD).

    Este exemplo executa no conflitos de sincronização de processamento de erros simples. Uma aplicação real seria processar vários erros, tais como condições de rede, conflitos de servidor e outras pessoas. Para obter exemplos de código, consulte o [exemplo sincronização offline].

5. Em seguida, adicione esta função para efetuar a sincronização real.

        function syncBackend() {

          // Sync local store to Azure table when app loads, or when login complete.
          syncContext.push().then(function () {
              // Push completed

          });

          // Pull items from the Azure table after syncing to Azure.
          syncContext.pull(new WindowsAzure.Query('todoitem'));
        }

    Decidir quando emitir alterações para o back-end de aplicação móvel chamando **syncContext.push()**. Por exemplo, pode chamar **syncBackend** um processador de eventos do botão associado a um botão de sincronização.

## <a name="offline-sync-considerations"></a>Considerações de sincronização offline

No exemplo, o **push** método **syncContext** denomina-se apenas durante o arranque aplicação na função de chamada de retorno para início de sessão.  Uma aplicação do mundo real, também pode fazer esta funcionalidade de sincronização acionada manualmente ou quando o estado de rede é alterado.

Quando uma solicitação é executada em relação a uma tabela que tem pendente atualizações locais controlados pelo contexto, que a solicitação de operação automaticamente acionadores um push. Durante a atualização, a adição e concluir itens neste exemplo, pode omitir o explícita **push** chamar, uma vez que poderá ser redundante.

No código fornecido, todos os registos na tabela todoItem remoto são consultados, mas também é possível filtrar os registos através da transmissão de um id de consulta e consultar para **push**. Para obter mais informações, consulte a secção *Sincronização Incremental* no [sincronização de dados Offline no Mobile Apps do Azure].

## <a name="optional-disable-authentication"></a>(Opcional) Desative a autenticação

Se não pretender configurar a autenticação antes de teste sincronização offline, comente a função de chamada de retorno para início de sessão, mas deixe o código dentro uncommented a função de chamada de retorno.  Após comentar terminar as linhas de início de sessão, o código seguinte:

      // Login to the service.
      // client.login('twitter')
      //    .then(function () {
        syncContext.initialize(store).then(function () {
          // Leave the rest of the code in this callback function  uncommented.
                ...
        });
      // }, handleError);

Agora, a aplicação sincroniza-se com o Azure back-end ao executar a aplicação.

## <a name="run-the-client-app"></a>Executar a aplicação de cliente
Com a sincronização offline ativada, é possível executar a aplicação de cliente, pelo menos, uma vez em cada plataforma para povoar a base de dados do arquivo local. Mais tarde, simular um cenário offline e modificar os dados no arquivo local enquanto a aplicação está offline.

## <a name="optional-test-the-sync-behavior"></a>(Opcional) Testar o comportamento de sincronização
Nesta secção, modifique o projeto de cliente para simular um cenário offline utilizando um URL de aplicação inválido para o seu back-end. Quando adicionar ou alterar os itens de dados, estas alterações são guardadas no arquivo local, mas não são sincronizadas para o arquivo de dados de back-end até que a ligação é restabelecida.

1. No Explorador de soluções, abra o ficheiro de projeto index.js e altere o URL de aplicação para apontar para um URL inválido, como o seguinte código:

        client = new WindowsAzure.MobileServiceClient('http://yourmobileapp.azurewebsites.net-fail');

2. No index.html, atualize o CSP `<meta>` elemento com o mesmo URL inválido.

        <meta http-equiv="Content-Security-Policy" content="default-src 'self' data: gap: http://yourmobileapp.azurewebsites.net-fail; style-src 'self'; media-src *">

3. Compilar e executar a aplicação de cliente e tenha em atenção que uma excepção está registada na consola quando a aplicação tenta sincronizar com o back-end após o início de sessão. Quaisquer novos itens que adicionar existem apenas no arquivo local até serem enviadas por push para o back-end móvel. A aplicação cliente comporta-se como se está ligada ao back-end.

4. Feche a aplicação e reiniciá-lo para verificar que os novos itens que criou estão continuados no armazenamento local.

5. (Opcional) Utilize o Visual Studio para ver a tabela de SQL Database do Azure para ver se os dados na base de dados de back-end não foi alterado.

    No Visual Studio, abra **Explorador de servidores**. Navegue para a base de dados no **Azure**->**bases de dados SQL**. A base de dados com o botão direito e selecione **abrir no SQL Server Object Explorer**. Agora pode navegar para a tabela de base de dados do SQL Server e o respetivo conteúdo.

## <a name="optional-test-the-reconnection-to-your-mobile-backend"></a>(Opcional) Testar o restabelecimento de ligação ao seu back-end móvel

Nesta secção, voltar a ligar a aplicação de back-end móvel, o que simula a aplicação regressam ao estado online. Quando iniciar sessão, os dados estão sincronizados com o back-end móvel.

1. Reabra index.js e restaurar o URL da aplicação.
2. Reabra index.html e corrija o URL da aplicação no CSP `<meta>` elemento.
3. Reconstruir e executar a aplicação de cliente. A aplicação tenta sincronizar com o back-end de aplicação móvel depois de início de sessão. Certifique-se de que não existem exceções são registadas na consola de depuração.
4. (Opcional) Ver os dados atualizados através do SQL Server Object Explorer ou uma ferramenta REST como o Fiddler. Tenha em atenção de que os dados foram sincronizados entre a base de dados de back-end e o arquivo local.

    Tenha em atenção que os dados foram sincronizados entre a base de dados e o arquivo local e contém os itens adicionados enquanto a aplicação foi desligada.

## <a name="additional-resources"></a>Recursos adicionais
* [sincronização de dados Offline no Mobile Apps do Azure]
* [Visual Studio Tools para Apache Cordova]

## <a name="next-steps"></a>Passos seguintes
* Revisão mais sincronização offline funcionalidades avançadas como a resolução de conflito no [exemplo sincronização offline]
* Reveja a referência da API de sincronização offline no [documentação da API](https://azure.github.io/azure-mobile-apps-js-client).

<!-- ##Summary -->

<!-- Images -->

<!-- URLs. -->
[início rápido do Apache Cordova]: app-service-mobile-cordova-get-started.md
[exemplo sincronização offline]: https://github.com/Azure-Samples/app-service-mobile-cordova-client-conflict-handling
[sincronização de dados Offline no Mobile Apps do Azure]: app-service-mobile-offline-data-sync.md
[Cloud Cover: Offline Sync in Azure Mobile Services]: http://channel9.msdn.com/Shows/Cloud+Cover/Episode-155-Offline-Storage-with-Donna-Malayeri
[Adding Authentication]: app-service-mobile-cordova-get-started-users.md
[authentication]: app-service-mobile-cordova-get-started-users.md
[Work with the .NET backend server SDK for Azure Mobile Apps]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Visual Studio Community 2015]: http://www.visualstudio.com/
[Visual Studio Tools para Apache Cordova]: https://www.visualstudio.com/en-us/features/cordova-vs.aspx
[Apache Cordova SDK]: app-service-mobile-cordova-how-to-use-client-library.md
[ASP.NET Server SDK]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Node.js Server SDK]: app-service-mobile-node-backend-how-to-use-server-sdk.md

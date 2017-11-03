---
title: "Ativar a sincronização offline da aplicação móvel de Azure (Xamarin iOS)"
description: "Saiba como utilizar a aplicação do serviço de aplicações móveis para sincronização e a cache de dados offline na aplicação Xamarin iOS"
documentationcenter: xamarin
author: ggailey777
manager: cfowler
editor: 
services: app-service\mobile
ms.assetid: 828a287c-5d58-4540-9527-1309ebb0f32b
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-ios
ms.devlang: dotnet
ms.topic: article
ms.date: 10/01/2016
ms.author: glenga
ms.openlocfilehash: b5878d8a2e18cf08b6e9074ecf40cd732624f0c0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="enable-offline-sync-for-your-xamarinios-mobile-app"></a>Permitir a sincronização offline para a sua aplicação móvel Xamarin.iOS
[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

## <a name="overview"></a>Descrição geral
Este tutorial apresenta a funcionalidade de sincronização offline de Mobile Apps do Azure para xamarin. IOS. Sincronização offline permite que os utilizadores finais interajam com uma aplicação móvel – visualizar, adicionar ou modificar dados –, mesmo quando não existe nenhuma ligação de rede. As alterações são armazenadas na base de dados local. Assim que o dispositivo estiver novamente online, estas alterações são sincronizadas com o serviço remoto.

Neste tutorial, atualize o projeto de aplicação xamarin. IOS de [criar uma aplicação Xamarin iOS] para suportar as funcionalidades offline de Mobile Apps do Azure. Se utilizar o projeto de início rápido transferido do servidor, tem de adicionar os pacotes de extensão de acesso de dados ao seu projeto. Para obter mais informações sobre pacotes de extensão de servidor, consulte [trabalhar com o servidor de back-end .NET SDK de Mobile Apps do Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

Para saber mais sobre a funcionalidade de sincronização offline, consulte o tópico [sincronização de dados Offline no Mobile Apps do Azure].

## <a name="update-the-client-app-to-support-offline-features"></a>Atualizar a aplicação de cliente para suportar funcionalidades offline
Funcionalidades de offline de aplicação móvel do Azure permitem-lhe interagir com uma base de dados local quando estão num cenário offline. Para utilizar estas funcionalidades na sua aplicação, inicializar um [SyncContext] para um arquivo local. Referencia a tabela através da interface [IMobileServiceSyncTable]. SQLite é utilizado como o arquivo local no dispositivo.

1. Abra o Gestor de pacotes NuGet no projeto que concluídas no [criar uma aplicação Xamarin iOS] tutorial, em seguida, procure e instale o **Microsoft.Azure.Mobile.Client.SQLiteStore** pacote NuGet.
2. Abra o ficheiro QSTodoService.cs e anule os comentários do `#define OFFLINE_SYNC_ENABLED` definição.
3. Reconstruir e executar a aplicação de cliente. A aplicação funciona da mesma forma que antes de ativar a sincronização offline. No entanto, a base de dados local agora é preenchido com os dados que podem ser utilizados num cenário offline.

## <a name="update-sync"></a>Atualizar a aplicação para desligar do back-end
Nesta secção, quebrar a ligação ao seu back-end de aplicação móvel para simular uma situação offline. Ao adicionar itens de dados, o processador de excepções indica que a aplicação está no modo offline. Neste estado, os novos itens adicionados no arquivo local em serão sincronizados com o back-end de aplicação móvel quando push seguinte é executado num estado ligado.

1. Edite QSToDoService.cs no projeto partilhado. Alterar o **applicationURL** para apontar para um URL inválido:

         const string applicationURL = @"https://your-service.azurewebsites.fail";

    Também pode demonstram comportamento offline desativar Wi-Fi e redes celular no dispositivo ou utilizando o modo de avião.
2. Crie e execute a aplicação. Tenha em atenção a sincronização falha na atualização, quando a aplicação iniciada.
3. Introduza novos itens e repare que push falha com um Estado [CancelledByNetworkError] cada vez que clicar **guardar**. No entanto, os novos itens todo existem no arquivo local, até que estas podem ser enviados para o back-end de aplicação móvel.  Numa aplicação de produção, se suprimir estas exceções a aplicação cliente comporta-se como se ainda está ligado ao back-end da aplicação móvel.
4. Feche a aplicação e reiniciá-lo para verificar que os novos itens que criou estão continuados no armazenamento local.
5. (Opcional) Se tiver o Visual Studio instalada num PC, abra **Explorador de servidores**. Navegue para a base de dados no **Azure**-> **bases de dados SQL**. A base de dados com o botão direito e selecione **abrir no SQL Server Object Explorer**. Agora pode navegar para a tabela de base de dados do SQL Server e o respetivo conteúdo. Certifique-se de que os dados na base de dados de back-end não foi alterado.
6. (Opcional) Utilize uma ferramenta REST, tais como o Fiddler ou Postman para consultar o seu back-end móvel, utilizando uma consulta GET no formato `https://<your-mobile-app-backend-name>.azurewebsites.net/tables/TodoItem`.

## <a name="update-online-app"></a>Atualizar a aplicação para restabelecer a ligação back-end da aplicação móvel
Nesta secção, voltar a ligar a aplicação ao back-end da aplicação móvel. Isto simula a aplicação mover de um estado offline para um estado online com o back-end de aplicação móvel.   Se simulated breakage a rede por desativar a conectividade de rede, sem alterações de código são necessários.
Ative a rede novamente.  Quando executar primeiro a aplicação, o `RefreshDataAsync` método é chamado. Isto chama por sua vez `SyncAsync` para sincronizar o seu arquivo local com a base de dados de back-end.

1. Abra QSToDoService.cs no projeto partilhado e reverter a sua alteração do **applicationURL** propriedade.
2. Reconstruir e executar a aplicação. A aplicação sincroniza-se as suas alterações locais com o back-end de aplicação móvel do Azure utilizando push e pull operações quando o `OnRefreshItemsSelected` método executa.
3. (Opcional) Ver os dados atualizados através do SQL Server Object Explorer ou uma ferramenta REST como o Fiddler. Tenha em atenção os dados foram sincronizada entre a base de dados de back-end de aplicação móvel do Azure e o arquivo local.
4. Na aplicação, clique na caixa de verificação ao lado de alguns itens para conclui-los no arquivo local.

   `CompleteItemAsync`chamadas `SyncAsync` ao item de sincronização cada foi concluída com o back-end de aplicação móvel. `SyncAsync`chama o push e pull.
   **Sempre que executar uma solicitação em relação a uma tabela que o cliente efetuou alterações para, um push no contexto de sincronização do cliente é sempre executado primeiro automaticamente**. O push implícita assegura que todas as tabelas no arquivo local, juntamente com relações permanecem consistentes. Para obter mais informações sobre este comportamento, consulte [sincronização de dados Offline no Mobile Apps do Azure].

## <a name="review-the-client-sync-code"></a>Rever o código de sincronização do cliente
O projeto de cliente do Xamarin que transferiu quando concluir o tutorial [criar uma aplicação Xamarin iOS] já contém o código que suporta a sincronização offline utilizando uma base de dados do SQLite local. Eis uma breve descrição geral dos que já está incluído com o código do tutorial. Para uma descrição geral conceptual da funcionalidade, consulte [sincronização de dados Offline no Mobile Apps do Azure].

* Antes de quaisquer operações de tabela podem ser efetuadas, o arquivo local tem de ser inicializado. A base de dados do arquivo local é iniciado quando `QSTodoListViewController.ViewDidLoad()` executa `QSTodoService.InitializeStoreAsync()`. Este método cria um novo local SQLite da base de dados utilizando o `MobileServiceSQLiteStore` classe fornecido pelo cliente de aplicação móvel do Azure SDK.

    O `DefineTable` método cria uma tabela no arquivo local que satisfaça os campos do tipo fornecido, `ToDoItem` neste caso. O tipo não tem de incluir todas as colunas que estão na base de dados remota. É possível armazenar a apenas um subconjunto de colunas.

        // QSTodoService.cs

        public async Task InitializeStoreAsync()
        {
            var store = new MobileServiceSQLiteStore(localDbPath);
            store.DefineTable<ToDoItem>();

            // Uses the default conflict handler, which fails on conflict
            await client.SyncContext.InitializeAsync(store);
        }
* O `todoTable` membro de `QSTodoService` tem o `IMobileServiceSyncTable` escreva em vez de `IMobileServiceTable`. O IMobileServiceSyncTable direciona todos os criarem, ler, atualizarem e eliminar as operações de tabela (CRUD) na base de dados do arquivo local.

    Decidir quando essas alterações são enviadas por push para o back-end de aplicação móvel do Azure ao chamar `IMobileServiceSyncContext.PushAsync()`. O contexto de sincronização ajuda a manter relações de tabela de controlo e enviar as alterações em todas as tabelas modificou uma aplicação de cliente quando `PushAsync` é chamado.

    As chamadas de código fornecidos `QSTodoService.SyncAsync()` para sincronização sempre que a lista de todoitem seja atualizada ou um todoitem está adicionado ou foi concluído. As sincronizações de aplicação após cada alteração local. Se uma solicitação for executada em relação a uma tabela que tem atualizações locais pendentes controlados pelo contexto, essa operação de solicitação automaticamente acionará um push contexto primeiro.

    No código fornecido, todos os registos no remoto `TodoItem` tabela são consultados, mas também é possível filtrar os registos através da transmissão de um id de consulta e consultar para `PushAsync`. Para obter mais informações, consulte a secção *Sincronização Incremental* no [sincronização de dados Offline no Mobile Apps do Azure].

        // QSTodoService.cs
        public async Task SyncAsync()
        {
            try
            {
                await client.SyncContext.PushAsync();
                await todoTable.PullAsync("allTodoItems", todoTable.CreateQuery()); // query ID is used for incremental sync
            }

            catch (MobileServiceInvalidOperationException e)
            {
                Console.Error.WriteLine(@"Sync Failed: {0}", e.Message);
            }
        }

## <a name="additional-resources"></a>Recursos Adicionais
* [sincronização de dados Offline no Mobile Apps do Azure]
* [HOWTO de SDK do .NET de Mobile Apps do Azure][8]

<!-- Images -->

<!-- URLs. -->
[criar uma aplicação Xamarin iOS]: app-service-mobile-xamarin-ios-get-started.md
[sincronização de dados Offline no Mobile Apps do Azure]: app-service-mobile-offline-data-sync.md
[SyncContext]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.synccontext(v=azure.10).aspx
[8]: app-service-mobile-dotnet-how-to-use-client-library.md

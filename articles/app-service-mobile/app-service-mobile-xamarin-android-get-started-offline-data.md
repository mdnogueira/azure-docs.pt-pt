---
title: "Ativar a sincronização offline da aplicação móvel de Azure (Xamarin Android)"
description: "Saiba como utilizar a aplicação do serviço de aplicações móveis para sincronização e a cache de dados offline na sua aplicação Xamarin Android"
documentationcenter: xamarin
author: ggailey777
manager: syntaxc4
editor: 
services: app-service\mobile
ms.assetid: 91d59e4b-abaa-41f4-80cf-ee7933b32568
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-android
ms.devlang: dotnet
ms.topic: article
ms.date: 10/01/2016
ms.author: glenga
ms.openlocfilehash: 471433c7ef2f6f128210ed145f685b42b44eea92
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="enable-offline-sync-for-your-xamarinandroid-mobile-app"></a>Ativar a sincronização offline para a sua aplicação móvel xamarin. Android
[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

## <a name="overview"></a>Descrição geral
Este tutorial apresenta a funcionalidade de sincronização offline de Mobile Apps do Azure para xamarin. Android. Sincronização offline permite que os utilizadores finais interajam com uma aplicação móvel – visualizar, adicionar ou modificar dados –, mesmo quando não existe nenhuma ligação de rede. As alterações são armazenadas na base de dados local.
Assim que o dispositivo estiver novamente online, estas alterações são sincronizadas com o serviço remoto.

Neste tutorial, atualizar o projeto de cliente do tutorial [criar uma aplicação Xamarin Android] para suportar as funcionalidades offline de Mobile Apps do Azure. Se utilizar o projeto de início rápido transferido do servidor, tem de adicionar os pacotes de extensão de acesso de dados ao seu projeto. Para obter mais informações sobre pacotes de extensão de servidor, consulte [trabalhar com o servidor de back-end .NET SDK de Mobile Apps do Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

Para saber mais sobre a funcionalidade de sincronização offline, consulte o tópico [sincronização de dados Offline no Mobile Apps do Azure].

## <a name="update-the-client-app-to-support-offline-features"></a>Atualizar a aplicação de cliente para suportar funcionalidades offline
Funcionalidades de offline de aplicação móvel do Azure permitem-lhe interagir com uma base de dados local quando estão num cenário offline. Para utilizar estas funcionalidades na sua aplicação, de inicializar um [SyncContext] para um arquivo local. Em seguida, referencia a tabela através da interface [IMobileServiceSyncTable] [IMobileServiceSyncTable]. SQLite é utilizado como o arquivo local no dispositivo.

1. No Visual Studio, abra o Gestor de pacotes NuGet no projeto que concluídas no [criar uma aplicação Xamarin Android] tutorial.  Procurar e instalar o **Microsoft.Azure.Mobile.Client.SQLiteStore** pacote NuGet.
2. Abra o ficheiro ToDoActivity.cs e anule os comentários do `#define OFFLINE_SYNC_ENABLED` definição.
3. No Visual Studio, prima a **F5** chave para reconstruir e executar a aplicação de cliente. A aplicação funciona da mesma forma que antes de ativar a sincronização offline. No entanto, a base de dados local agora é preenchido com os dados que podem ser utilizados num cenário offline.

## <a name="update-sync"></a>Atualizar a aplicação para desligar do back-end
Nesta secção, quebrar a ligação ao seu back-end de aplicação móvel para simular uma situação offline. Ao adicionar itens de dados, o processador de excepções indica que a aplicação está no modo offline. Neste estado, os novos itens adicionados no arquivo local em são sincronizados para o back-end da aplicação móvel quando é executado um push num estado ligado.

1. Edite ToDoActivity.cs no projeto partilhado. Alterar o **applicationURL** para apontar para um URL inválido:

         const string applicationURL = @"https://your-service.azurewebsites.fail";

    Também pode demonstram comportamento offline desativar Wi-Fi e redes celular no dispositivo ou utilizando o modo de avião.
2. Prima **F5** para compilar e executar a aplicação. Tenha em atenção a sincronização falha na atualização, quando a aplicação iniciada.
3. Introduza novos itens e repare que push falha com um Estado [CancelledByNetworkError] cada vez que clicar **guardar**. No entanto, os novos itens todo existem no arquivo local, até que estas podem ser enviados para o back-end de aplicação móvel.  Numa aplicação de produção, se suprimir estas exceções a aplicação cliente comporta-se como se ainda está ligado ao back-end da aplicação móvel.
4. Feche a aplicação e reiniciá-lo para verificar que os novos itens que criou estão continuados no armazenamento local.
5. (Opcional) No Visual Studio, abra **Explorador de servidores**. Navegue para a base de dados no **Azure**->**bases de dados SQL**. A base de dados com o botão direito e selecione **abrir no SQL Server Object Explorer**. Agora pode navegar para a tabela de base de dados do SQL Server e o respetivo conteúdo. Certifique-se de que os dados na base de dados de back-end não foi alterado.
6. (Opcional) Utilize uma ferramenta REST, tais como o Fiddler ou Postman para consultar o seu back-end móvel, utilizando uma consulta GET no formato `https://<your-mobile-app-backend-name>.azurewebsites.net/tables/TodoItem`.

## <a name="update-online-app"></a>Atualizar a aplicação para restabelecer a ligação back-end da aplicação móvel
Nesta secção, voltar a ligar a aplicação ao back-end da aplicação móvel. Quando executar primeiro a aplicação, o `OnCreate` chamadas do processador de eventos `OnRefreshItemsSelected`. Este método chama `SyncAsync` para sincronizar o seu arquivo local com a base de dados de back-end.

1. Abra ToDoActivity.cs no projeto partilhado e reverter a sua alteração do **applicationURL** propriedade.
2. Prima a **F5** chave para reconstruir e executar a aplicação. A aplicação sincroniza-se as suas alterações locais com o back-end de aplicação móvel do Azure utilizando push e pull operações quando o `OnRefreshItemsSelected` método executa.
3. (Opcional) Ver os dados atualizados através do SQL Server Object Explorer ou uma ferramenta REST como o Fiddler. Tenha em atenção os dados foram sincronizada entre a base de dados de back-end de aplicação móvel do Azure e o arquivo local.
4. Na aplicação, clique na caixa de verificação ao lado de alguns itens para conclui-los no arquivo local.

   `CheckItem`chamadas `SyncAsync` ao item de sincronização cada foi concluída com o back-end de aplicação móvel. `SyncAsync`chama o push e pull. **Sempre que executar uma solicitação em relação a uma tabela que o cliente efetuou alterações para, um push é sempre executado automaticamente**. Isto garante que todas as tabelas no arquivo local, juntamente com relações permanecem consistentes. Este comportamento poderá resultar num push inesperado. Para obter mais informações sobre este comportamento, consulte [sincronização de dados Offline no Mobile Apps do Azure].

## <a name="review-the-client-sync-code"></a>Rever o código de sincronização do cliente
O projeto de cliente do Xamarin que transferiu quando concluir o tutorial [criar uma aplicação Xamarin Android] já contém o código que suporta a sincronização offline utilizando uma base de dados do SQLite local. Eis uma breve descrição geral dos que já está incluído com o código do tutorial. Para uma descrição geral conceptual da funcionalidade, consulte [sincronização de dados Offline no Mobile Apps do Azure].

* Antes de quaisquer operações de tabela podem ser efetuadas, o arquivo local tem de ser inicializado. A base de dados do arquivo local é iniciado quando `ToDoActivity.OnCreate()` executa `ToDoActivity.InitLocalStoreAsync()`. Este método cria um local SQLite da base de dados, utilizando o `MobileServiceSQLiteStore` classe fornecido pelo cliente de Mobile Apps do Azure SDK.

    O `DefineTable` método cria uma tabela no arquivo local que satisfaça os campos do tipo fornecido, `ToDoItem` neste caso. O tipo não tem de incluir todas as colunas que estão na base de dados remota. É possível armazenar a apenas um subconjunto de colunas.

        // ToDoActivity.cs
        private async Task InitLocalStoreAsync()
        {
            // new code to initialize the SQLite store
            string path = Path.Combine(System.Environment
                .GetFolderPath(System.Environment.SpecialFolder.Personal), localDbFilename);

            if (!File.Exists(path))
            {
                File.Create(path).Dispose();
            }

            var store = new MobileServiceSQLiteStore(path);
            store.DefineTable<ToDoItem>();

            // Uses the default conflict handler, which fails on conflict
            // To use a different conflict handler, pass a parameter to InitializeAsync.
            // For more details, see http://go.microsoft.com/fwlink/?LinkId=521416.
            await client.SyncContext.InitializeAsync(store);
        }
* O `toDoTable` membro de `ToDoActivity` tem o `IMobileServiceSyncTable` escreva em vez de `IMobileServiceTable`. O IMobileServiceSyncTable direciona todos os criarem, ler, atualizarem e eliminar as operações de tabela (CRUD) na base de dados do arquivo local.

    Decidir quando as alterações são enviadas por push para o back-end de aplicação móvel do Azure ao chamar `IMobileServiceSyncContext.PushAsync()`. O contexto de sincronização ajuda a manter relações de tabela de controlo e enviar as alterações em todas as tabelas modificou uma aplicação de cliente quando `PushAsync` é chamado.

    As chamadas de código fornecidos `ToDoActivity.SyncAsync()` para sincronização sempre que a lista de todoitem seja atualizada ou um todoitem está adicionado ou foi concluído. As sincronizações de código após cada alteração local.

    No código fornecido, todos os registos no remoto `TodoItem` tabela são consultados, mas também é possível filtrar os registos através da transmissão de um id de consulta e consultar para `PushAsync`. Para obter mais informações, consulte a secção *Sincronização Incremental* no [sincronização de dados Offline no Mobile Apps do Azure].

        // ToDoActivity.cs
        private async Task SyncAsync()
        {
            try {
                await client.SyncContext.PushAsync();
                await toDoTable.PullAsync("allTodoItems", toDoTable.CreateQuery()); // query ID is used for incremental sync
            } catch (Java.Net.MalformedURLException) {
                CreateAndShowDialog (new Exception ("There was an error creating the Mobile Service. Verify the URL"), "Error");
            } catch (Exception e) {
                CreateAndShowDialog (e, "Error");
            }
        }

## <a name="additional-resources"></a>Recursos Adicionais
* [sincronização de dados Offline no Mobile Apps do Azure]
* [HOWTO de SDK do .NET de Mobile Apps do Azure][8]

<!-- URLs. -->
[criar uma aplicação Xamarin Android]: ../app-service-mobile-xamarin-android-get-started.md
[sincronização de dados Offline no Mobile Apps do Azure]: ../app-service-mobile-offline-data-sync.md

<!-- Images -->

<!-- URLs. -->
[Criar uma aplicação Xamarin Android]: app-service-mobile-xamarin-android-get-started.md
[Sincronização de Dados Offline em Aplicações Móveis do Azure]: app-service-mobile-offline-data-sync.md
[Xamarin Studio]: http://xamarin.com/download
[Xamarin extension]: http://xamarin.com/visual-studio
[SyncContext]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.synccontext(v=azure.10).aspx
[8]: app-service-mobile-dotnet-how-to-use-client-library.md

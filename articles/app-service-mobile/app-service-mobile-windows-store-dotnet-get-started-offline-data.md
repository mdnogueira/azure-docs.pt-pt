---
title: "Ativar a sincronização offline para a sua aplicação plataforma Universal do Windows (UWP) com aplicações móveis | Microsoft Docs"
description: "Saiba como utilizar uma aplicação móvel do Azure para a cache e sincronização de dados offline na sua aplicação plataforma Universal do Windows (UWP)."
documentationcenter: windows
author: ggailey777
manager: syntaxc4
editor: 
services: app-service\mobile
ms.assetid: 8fe51773-90de-4014-8a38-41544446d9b5
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: article
ms.date: 10/01/2016
ms.author: glenga
ms.openlocfilehash: 4b0a57c3bab688487eb9a50461b406e1a6e477c6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="enable-offline-sync-for-your-windows-app"></a>Ativar a sincronização offline da aplicação do Windows
[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

## <a name="overview"></a>Descrição geral
Este tutorial mostra como adicionar suporte offline para uma aplicação plataforma Universal do Windows (UWP) utilizando um back-end de aplicação móvel do Azure. Sincronização offline permite que os utilizadores finais interajam com uma aplicação móvel – visualizar, adicionar ou modificar dados -, mesmo quando não existe nenhuma ligação de rede. As alterações são armazenadas na base de dados local. Assim que o dispositivo estiver novamente online, estas alterações são sincronizadas com o back-end remoto.

Neste tutorial, atualizar o projeto de aplicação UWP o tutorial [criar uma aplicação Windows] para suportar as funcionalidades offline de Mobile Apps do Azure. Se utilizar o projeto de início rápido transferido do servidor, tem de adicionar os pacotes de extensão de acesso de dados ao seu projeto. Para obter mais informações sobre pacotes de extensão de servidor, consulte [trabalhar com o servidor de back-end .NET SDK de Mobile Apps do Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

Para saber mais sobre a funcionalidade de sincronização offline, consulte o tópico [sincronização de dados Offline no Mobile Apps do Azure].

## <a name="requirements"></a>Requisitos
Este tutorial requer os seguintes pré-requisitos:

* Visual Studio 2013 com Windows 8.1 ou posterior.
* Conclusão da [criar uma aplicação Windows][criar uma aplicação do windows].
* [O Azure Mobile Services SQLite Store][sqlite store nuget]
* [SQLite para o desenvolvimento de plataforma Universal do Windows](http://www.sqlite.org/downloads)

## <a name="update-the-client-app-to-support-offline-features"></a>Atualizar a aplicação de cliente para suportar funcionalidades offline
Funcionalidades de offline de aplicação móvel do Azure permitem-lhe interagir com uma base de dados local quando estão num cenário offline. Para utilizar estas funcionalidades na sua aplicação, de inicializar um [SyncContext] [ synccontext] para um arquivo local. Em seguida, referenciar a tabela através de [IMobileServiceSyncTable][IMobileServiceSyncTable] interface. SQLite é utilizado como o arquivo local no dispositivo.

1. Instalar o [SQLite tempo de execução para a plataforma Universal do Windows](http://sqlite.org/2016/sqlite-uwp-3120200.vsix).
2. No Visual Studio, abra o Gestor de pacotes NuGet para o projeto de aplicação UWP concluída no [criar uma aplicação Windows] tutorial.
    Procurar e instalar o **Microsoft.Azure.Mobile.Client.SQLiteStore** pacote NuGet.
3. No Explorador de soluções, faça duplo clique **referências** > **Adicionar referência...** >**Universal Windows** > **extensões**, em seguida, ative ambos **SQLite para a plataforma Universal do Windows** e **Visual C++ 2015 em tempo de execução para aplicações da plataforma Universal do Windows**.

    ![Adicionar referência SQLite UWP][1]
4. Abra o ficheiro MainPage.xaml.cs e anule os comentários do `#define OFFLINE_SYNC_ENABLED` definição.
5. No Visual Studio, prima a **F5** chave para reconstruir e executar a aplicação de cliente. A aplicação funciona da mesma forma que antes de ativar a sincronização offline. No entanto, a base de dados local agora é preenchido com os dados que podem ser utilizados num cenário offline.

## <a name="update-sync"></a>Atualizar a aplicação para desligar do back-end
Nesta secção, quebrar a ligação ao seu back-end de aplicação móvel para simular uma situação offline. Ao adicionar itens de dados, o processador de excepções indica que a aplicação está no modo offline. Neste estado, os novos itens adicionados no arquivo local em serão sincronizados com o back-end de aplicação móvel quando push seguinte é executado num estado ligado.

1. Edite App.xaml.cs no projeto partilhado. Comente a inicialização do **MobileServiceClient** e adicione a seguinte linha, que utiliza um URL de aplicação móvel inválido:

         public static MobileServiceClient MobileService = new MobileServiceClient("https://your-service.azurewebsites.fail");

    Também pode demonstrar comportamento offline através da desativação de Wi-Fi e redes celular no dispositivo ou utilizar o modo de avião.
2. Prima **F5** para compilar e executar a aplicação. Tenha em atenção a sincronização falha na atualização, quando a aplicação iniciada.
3. Introduza novos itens e tenha em atenção que o push falha com um [CancelledByNetworkError] estado de cada vez que clicar **guardar**. No entanto, os novos itens todo existem no arquivo local, até que estas podem ser enviados para o back-end de aplicação móvel.  Numa aplicação de produção, se suprimir estas exceções a aplicação cliente comporta-se como se ainda está ligado ao back-end da aplicação móvel.
4. Feche a aplicação e reiniciá-lo para verificar que os novos itens que criou estão continuados no armazenamento local.
5. (Opcional) No Visual Studio, abra **Explorador de servidores**. Navegue para a base de dados no **Azure**->**bases de dados SQL**. A base de dados com o botão direito e selecione **abrir no SQL Server Object Explorer**. Agora pode navegar para a tabela de base de dados do SQL Server e o respetivo conteúdo. Certifique-se de que os dados na base de dados de back-end não foi alterado.
6. (Opcional) Utilize uma ferramenta REST, tais como o Fiddler ou Postman para consultar o seu back-end móvel, utilizando uma consulta GET no formato `https://<your-mobile-app-backend-name>.azurewebsites.net/tables/TodoItem`.

## <a name="update-online-app"></a>Atualizar a aplicação para restabelecer a ligação back-end da aplicação móvel
Nesta secção, voltar a ligar a aplicação ao back-end da aplicação móvel. Estas alterações simulam um restabelecimento de ligação de rede na aplicação.

Quando executar primeiro a aplicação, o `OnNavigatedTo` chamadas do processador de eventos `InitLocalStoreAsync`. Por sua vez chama este método `SyncAsync` para sincronizar o seu arquivo local com a base de dados de back-end. A aplicação tenta sincronizar no arranque.

1. Abra App.xaml.cs no projeto partilhado e anule os comentários da sua inicialização anterior do `MobileServiceClient` para utilizar o correto o URL de aplicação móvel.
2. Prima a **F5** chave para reconstruir e executar a aplicação. A aplicação sincroniza-se as suas alterações locais com o back-end de aplicação móvel do Azure utilizando push e pull operações quando o `OnNavigatedTo` executa de processador de eventos.
3. (Opcional) Ver os dados atualizados através do SQL Server Object Explorer ou uma ferramenta REST como o Fiddler. Tenha em atenção os dados foram sincronizada entre a base de dados de back-end de aplicação móvel do Azure e o arquivo local.
4. Na aplicação, clique na caixa de verificação ao lado de alguns itens para conclui-los no arquivo local.

   `UpdateCheckedTodoItem`chamadas `SyncAsync` ao item de sincronização cada foi concluída com o back-end de aplicação móvel. `SyncAsync`chama o push e pull. No entanto, **sempre que executar uma solicitação em relação a uma tabela que o cliente efetuou alterações para, um push é sempre executado automaticamente**. Este comportamento assegura que todas as tabelas no arquivo local, juntamente com relações permanecem consistentes. Este comportamento poderá resultar num push inesperado.  Para obter mais informações sobre este comportamento, consulte [sincronização de dados Offline no Mobile Apps do Azure].

## <a name="api-summary"></a>Resumo de API
Para suportar as funcionalidades dos serviços móveis offline, utilizámos o [IMobileServiceSyncTable] interface e inicializado [MobileServiceClient.SyncContext] [ synccontext] com uma base de dados do SQLite local. Quando offline, as operações CRUD normais para Mobile Apps funcionam como se a aplicação ainda está ligada enquanto as operações ocorrem contra o arquivo local. Os métodos seguintes são utilizados para sincronizar o arquivo local com o servidor:

* **[PushAsync]**  porque este método é um membro de [IMobileServicesSyncContext], as alterações em todas as tabelas são enviadas por push para o back-end. Apenas os registos com alterações locais são enviados para o servidor.
* **[PullAsync]**  recebe é iniciada a partir de um [IMobileServiceSyncTable]. Quando existirem alterações registadas na tabela, é executado um push implícito para se certificar de que todas as tabelas no arquivo local, juntamente com relações permanecem consistentes. O *pushOtherTables* parâmetro controla se outras tabelas no contexto são enviadas por push num push implícita. O *consulta* parâmetro assume um [IMobileServiceTableQuery<T> ] [ IMobileServiceTableQuery] ou cadeia de consulta de OData para filtrar os dados devolvidos. O *queryId* parâmetro é utilizado para definir a sincronização incremental. Para obter mais informações, consulte [Offline sincronização de dados no Azure Mobile Apps](app-service-mobile-offline-data-sync.md#how-sync-works).
* **[PurgeAsync]**  a aplicação periodicamente deve chamar este método para remover dados desatualizados do arquivo local. Utilize o *forçar* parâmetro quando tiver de remover quaisquer alterações que ainda não foram sincronizadas.

Para obter mais informações sobre estes conceitos, consulte [Offline sincronização de dados no Azure Mobile Apps](app-service-mobile-offline-data-sync.md#how-sync-works).

## <a name="more-info"></a>Mais informações
Os tópicos seguintes fornecem mais informações gerais sobre a funcionalidade de sincronização offline de Mobile Apps:

* [sincronização de dados Offline no Mobile Apps do Azure]
* [HOWTO de SDK do .NET de Mobile Apps do Azure][8]

<!-- Anchors. -->
[Update the app to support offline features]: #enable-offline-app
[Update the sync behavior of the app]: #update-sync
[Update the app to reconnect your Mobile Apps backend]: #update-online-app
[Next Steps]:#next-steps

<!-- Images -->
[1]: ./media/app-service-mobile-windows-store-dotnet-get-started-offline-data/app-service-mobile-add-reference-sqlite-dialog.png
[11]: ./media/app-service-mobile-windows-store-dotnet-get-started-offline-data/app-service-mobile-add-wp81-reference-sqlite-dialog.png
[13]: ./media/app-service-mobile-windows-store-dotnet-get-started-offline-data/cpu-architecture.png


<!-- URLs. -->
[sincronização de dados Offline no Mobile Apps do Azure]: app-service-mobile-offline-data-sync.md
[criar uma aplicação do windows]: app-service-mobile-windows-store-dotnet-get-started.md
[SQLite for Windows 8.1]: http://go.microsoft.com/fwlink/?LinkID=716919
[SQLite for Windows Phone 8.1]: http://go.microsoft.com/fwlink/?LinkID=716920
[SQLite for Windows 10]: http://go.microsoft.com/fwlink/?LinkID=716921
[synccontext]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.synccontext(v=azure.10).aspx
[sqlite store nuget]: https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client.SQLiteStore/
[IMobileServiceSyncTable]: https://msdn.microsoft.com/library/azure/mt691742(v=azure.10).aspx
[IMobileServiceTableQuery]: https://msdn.microsoft.com/library/azure/dn250631(v=azure.10).aspx
[IMobileServicesSyncContext]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.sync.imobileservicesynccontext(v=azure.10).aspx
[MobileServicePushFailedException]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.sync.mobileservicepushfailedexception(v=azure.10).aspx
[Status]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.sync.mobileservicepushcompletionresult.status(v=azure.10).aspx
[CancelledByNetworkError]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.sync.mobileservicepushstatus(v=azure.10).aspx
[PullAsync]: https://msdn.microsoft.com/library/azure/mt667558(v=azure.10).aspx
[PushAsync]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileservicesynccontextextensions.pushasync(v=azure.10).aspx
[PurgeAsync]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.sync.imobileservicesynctable.purgeasync(v=azure.10).aspx
[8]: app-service-mobile-dotnet-how-to-use-client-library.md

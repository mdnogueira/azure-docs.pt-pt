---
title: "Ativar a sincronização offline da aplicação móvel de Azure (xamarin. Forms) | Microsoft Docs"
description: "Saiba como utilizar a aplicação do serviço de aplicações móveis para sincronização e a cache de dados offline na sua aplicação xamarin. Forms"
documentationcenter: xamarin
author: ggailey777
manager: yochayk
editor: 
services: app-service\mobile
ms.assetid: acf0f874-3ea5-4410-bd22-b0e72140f3b5
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-ios
ms.devlang: dotnet
ms.topic: article
ms.date: 10/04/2016
ms.author: glenga
ms.openlocfilehash: f2bed0a7124517319cc82405c4ab6b4d79aacfe1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="enable-offline-sync-for-your-xamarinforms-mobile-app"></a>Permitir sincronização offline para a sua aplicação móvel Xamarin.Forms
[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

## <a name="overview"></a>Descrição geral
Este tutorial apresenta a funcionalidade de sincronização offline de Mobile Apps do Azure para xamarin. Forms. Sincronização offline permite que os utilizadores finais interajam com uma aplicação móvel – visualizar, adicionar ou modificar dados –, mesmo quando não existe nenhuma ligação de rede. As alterações são armazenadas na base de dados local. Assim que o dispositivo estiver novamente online, estas alterações são sincronizadas com o serviço remoto.

Este tutorial baseia-se na solução de início rápido de xamarin. Forms para Mobile Apps que criar quando concluir o tutorial [criar uma aplicação Xamarin iOS]. A solução de início rápido para xamarin. Forms contém o código para suportar a sincronização offline, que apenas tem de ser ativado. Neste tutorial, atualize a solução de início rápido para ativar as funcionalidades offline de Mobile Apps do Azure. Podemos também realçar o código de offline específicos na aplicação. Se não utilizar a solução de início rápido transferido, tem de adicionar os pacotes de extensão de acesso de dados ao seu projeto. Para obter mais informações sobre pacotes de extensão de servidor, consulte [trabalhar com o servidor de back-end .NET SDK de Mobile Apps do Azure][1].

Para saber mais sobre a funcionalidade de sincronização offline, consulte o tópico [sincronização de dados Offline no Mobile Apps do Azure][2].

## <a name="enable-offline-sync-functionality-in-the-quickstart-solution"></a>Ativar a funcionalidade de sincronização offline na solução de início rápido
O código de sincronização offline está incluído no projeto através da utilização de c# diretivas no pré-processador. Quando o **OFFLINE\_SINCRONIZAÇÃO\_ATIVADO** símbolo está definido, estes caminhos de código são incluídos na compilação. Para aplicações do Windows, tem de instalar também a plataforma do SQLite.

1. No Visual Studio, clique com botão direito na solução > **gerir pacotes NuGet para solução...** , em seguida, procure e instale o **Microsoft.Azure.Mobile.Client.SQLiteStore** pacote NuGet para todos os projetos na solução.
2. No Explorador de soluções, abra o ficheiro todoitemmanager.cs em projeto com **portátil** no nome, o que é o projeto da biblioteca de classe portátil, em seguida, anule os comentários a diretiva de pré-processador seguinte:

        #define OFFLINE_SYNC_ENABLED
3. (Opcional) Para suportar dispositivos Windows, instale um dos seguintes pacotes de tempo de execução do SQLite:

   * **Tempo de execução do Windows 8.1:** instalar [SQLite para Windows 8.1][3].
   * **Windows Phone 8.1:** instalar [SQLite para Windows Phone 8.1][4].
   * **Plataforma universal do Windows** instalar [SQLite para Universal Universal Windows][5].

     Embora o guia de introdução não contém um projeto Universal do Windows, a plataforma Universal do Windows é suportada com formulários Xamarin.
4. (Opcional) Em cada projeto de aplicação do Windows, clique com botão direito **referências** > **Adicionar referência...** , expanda o **Windows** pasta > **extensões**.
    Ativar o adequado **SQLite para Windows** SDK juntamente com o **Visual C++ 2013 Runtime para o Windows** SDK.
    Os nomes de SQLite SDK variam ligeiramente com cada plataforma do Windows.

## <a name="review-the-client-sync-code"></a>Rever o código de sincronização do cliente
Eis uma breve descrição geral dos que já está incluído com o código do tutorial dentro de `#if OFFLINE_SYNC_ENABLED` diretivas. A funcionalidade de sincronização offline é no ficheiro todoitemmanager.cs em projeto no projeto da biblioteca de classes portátil. Para uma descrição geral conceptual da funcionalidade, consulte [Offline sincronização de dados no Azure Mobile Apps][2].

* Antes de quaisquer operações de tabela podem ser efetuadas, o arquivo local tem de ser inicializado. A base de dados do arquivo local é inicializado no **TodoItemManager** construtor de classe utilizando o seguinte código:

        var store = new MobileServiceSQLiteStore(OfflineDbPath);
        store.DefineTable<TodoItem>();

        //Initializes the SyncContext using the default IMobileServiceSyncHandler.
        this.client.SyncContext.InitializeAsync(store);

        this.todoTable = client.GetSyncTable<TodoItem>();

    Este código cria um novo local SQLite da base de dados utilizando o **MobileServiceSQLiteStore** classe.

    O **DefineTable** método cria uma tabela no arquivo local que satisfaça os campos do tipo fornecido.  O tipo não tem de incluir todas as colunas que estão na base de dados remota. É possível armazenar um subconjunto de colunas.
* O **todoTable** campo no **TodoItemManager** é um **IMobileServiceSyncTable** escreva em vez de **IMobileServiceTable**. Esta utiliza de classe base de dados local para todos os criar, ler, atualiza e eliminar as operações de tabela (CRUD). Decidir quando essas alterações são enviadas por push para o back-end de aplicação móvel chamando **PushAsync** no **IMobileServiceSyncContext**. O contexto de sincronização ajuda a manter relações de tabela de controlo e enviar as alterações em todas as tabelas modificou uma aplicação de cliente quando **PushAsync** é chamado.

    O seguinte **SyncAsync** método é denominado a sincronizar com o back-end de aplicação móvel:

        public async Task SyncAsync()
        {
            ReadOnlyCollection<MobileServiceTableOperationError> syncErrors = null;

            try
            {
                await this.client.SyncContext.PushAsync();

                await this.todoTable.PullAsync(
                    "allTodoItems",
                    this.todoTable.CreateQuery());
            }
            catch (MobileServicePushFailedException exc)
            {
                if (exc.PushResult != null)
                {
                    syncErrors = exc.PushResult.Errors;
                }
            }

            // Simple error/conflict handling.
            if (syncErrors != null)
            {
                foreach (var error in syncErrors)
                {
                    if (error.OperationKind == MobileServiceTableOperationKind.Update && error.Result != null)
                    {
                        //Update failed, reverting to server's copy.
                        await error.CancelAndUpdateItemAsync(error.Result);
                    }
                    else
                    {
                        // Discard local change.
                        await error.CancelAndDiscardItemAsync();
                    }

                    Debug.WriteLine(@"Error executing sync operation. Item: {0} ({1}). Operation discarded.",
                        error.TableName, error.Item["id"]);
                }
            }
        }

    Este exemplo utiliza com o processador de sincronização predefinido de processamento de erros simples. Uma aplicação real seria processar vários erros, tais como condições de rede e conflitos de servidor utilizando um personalizado **IMobileServiceSyncHandler** implementação.

## <a name="offline-sync-considerations"></a>Considerações de sincronização offline
No exemplo, o **SyncAsync** método só é chamado no arranque e quando é pedida uma sincronização.  Para iniciar uma sincronização numa aplicação Android ou iOS, obtenha a lista de itens; para o Windows, utilize o **sincronização** botão. Uma aplicação do mundo real, também foi possível efetuar o acionador de sincronização quando o estado de rede é alterado.

Quando uma solicitação é executada em relação a uma tabela que tem pendente atualizações locais controlados pelo contexto, que a solicitação de operação automaticamente acionadores um push contexto anterior. Durante a atualização, a adição e concluir itens neste exemplo, pode omitir o explícita **PushAsync** chamada.

No código fornecido, todos os registos na tabela TodoItem remoto são consultados, mas também é possível filtrar os registos através da transmissão de um id de consulta e consultar para **PushAsync**. Para obter mais informações, consulte a secção *Sincronização Incremental* no [Offline sincronização de dados no Azure Mobile Apps][2].

## <a name="run-the-client-app"></a>Executar a aplicação de cliente
Com a sincronização offline ativada, execute a aplicação de cliente, pelo menos, uma vez em cada plataforma para povoar a base de dados do arquivo local. Mais tarde, simular um cenário offline e modificar os dados no arquivo local enquanto a aplicação está offline.

## <a name="update-the-sync-behavior-of-the-client-app"></a>Atualizar o comportamento de sincronização da aplicação cliente
Nesta secção, modifique o projeto de cliente para simular um cenário offline utilizando um URL de aplicação inválido para o seu back-end. Em alternativa, pode desativar ligações de rede, movendo o seu dispositivo para "Modo de avião".  Quando adicionar ou alterar os itens de dados, estas alterações são guardadas no arquivo local, mas não sincronizadas para o arquivo de dados de back-end, até que a ligação é restabelecida.

1. No Explorador de soluções, abra o ficheiro de projeto Constants do **portátil** projeto e altere o valor de `ApplicationURL` para apontar para um URL inválido:

        public static string ApplicationURL = @"https://your-service.azurewebsites.net/";
2. Abra o ficheiro todoitemmanager.CS no **portátil** do projeto, em seguida, adicione um **catch** para a base de **exceção** de classe para o **tente... catch**bloquear **SyncAsync**. Isto **catch** bloco escreve na mensagem de exceção na consola, da seguinte forma:

            catch (Exception ex)
            {
                Console.Error.WriteLine(@"Exception: {0}", ex.Message);
            }
3. Crie e execute a aplicação de cliente.  Adicione alguns itens de novo. Tenha em atenção que uma excepção está registada na consola para cada tentativa de sincronização com o back-end. Estes novos itens existem apenas no arquivo local até que estas podem ser enviados para o back-end móvel. A aplicação cliente comporta-se como se está ligada ao back-end, suportar que todos os criar, ler, atualizar, eliminar (CRUD) operações.
4. Feche a aplicação e reiniciá-lo para verificar que os novos itens que criou estão continuados no armazenamento local.
5. (Opcional) Utilize o Visual Studio para ver a tabela de SQL Database do Azure para ver se os dados na base de dados de back-end não foi alterado.

    No Visual Studio, abra **Explorador de servidores**. Navegue para a base de dados no **Azure**->**bases de dados SQL**. A base de dados com o botão direito e selecione **abrir no SQL Server Object Explorer**. Agora pode navegar para a tabela de base de dados do SQL Server e o respetivo conteúdo.

## <a name="update-the-client-app-to-reconnect-your-mobile-backend"></a>Atualizar a aplicação de cliente para restabelecer a ligação back-end móvel
Nesta secção, voltar a ligar a aplicação de back-end móvel, o que simula a aplicação regressam ao estado online. Quando efetua o gesto de atualização, dados estão sincronizados com o back-end móvel.

1. Reabra Constants. cs. Corrija o `applicationURL` para apontar para o URL correto.
2. Reconstruir e executar a aplicação de cliente. A aplicação tenta sincronizar com o back-end de aplicação móvel depois de iniciar. Certifique-se de que não existem exceções são registadas na consola de depuração.
3. (Opcional) Ver os dados atualizados através do SQL Server Object Explorer ou uma ferramenta REST como o Fiddler ou [Postman][6]. Tenha em atenção de que os dados foram sincronizados entre a base de dados de back-end e o arquivo local.

    Tenha em atenção que os dados foram sincronizados entre a base de dados e o arquivo local e contém os itens adicionados enquanto a aplicação foi desligada.

## <a name="additional-resources"></a>Recursos Adicionais
* [Sincronização de dados offline no Mobile Apps do Azure][2]
* [HOWTO de SDK do .NET de Mobile Apps do Azure][8]

<!-- URLs. -->
[1]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[2]: app-service-mobile-offline-data-sync.md
[3]: http://go.microsoft.com/fwlink/p/?LinkID=716919
[4]: http://go.microsoft.com/fwlink/p/?LinkID=716920
[5]: http://sqlite.org/2016/sqlite-uwp-3120200.vsix
[6]: https://www.getpostman.com/
[7]: http://www.telerik.com/fiddler
[8]: app-service-mobile-dotnet-how-to-use-client-library.md

---
title: "Ativar a sincronização offline da aplicação móvel de Azure (Android)"
description: "Saiba como utilizar Mobile Apps do App Service para sincronização e a cache de dados offline na sua aplicação Android"
documentationcenter: android
author: ggailey777
manager: syntaxc4
services: app-service\mobile
ms.assetid: 32a8a079-9b3c-4faf-8588-ccff02097224
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: article
ms.date: 10/01/2016
ms.author: glenga
ms.openlocfilehash: 304323c1816302e8c1f68f36a029aee55e02c54e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="enable-offline-sync-for-your-android-mobile-app"></a>Permitir sincronização offline para a sua aplicação móvel Android
[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

## <a name="overview"></a>Descrição geral
Este tutorial abrange a funcionalidade de sincronização offline de Mobile Apps do Azure para Android. Sincronização offline permite que os utilizadores finais interajam com uma aplicação móvel&mdash;visualizar, adicionar ou modificar dados&mdash;, mesmo quando não existe nenhuma ligação de rede. As alterações são armazenadas na base de dados local. Assim que o dispositivo estiver novamente online, estas alterações são sincronizadas com o back-end remoto.

Se esta for a primeira experiência com Mobile Apps do Azure, primeiro deve concluir o tutorial [criar uma aplicação Android]. Se utilizar o projeto de início rápido transferido do servidor, tem de adicionar os pacotes de extensão de acesso de dados ao seu projeto. Para obter mais informações sobre pacotes de extensão de servidor, consulte [trabalhar com o servidor de back-end .NET SDK de Mobile Apps do Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

Para saber mais sobre a funcionalidade de sincronização offline, consulte o tópico [sincronização de dados Offline no Mobile Apps do Azure].

## <a name="update-the-app-to-support-offline-sync"></a>Atualizar a aplicação para suportar a sincronização offline
Com a sincronização offline, leia a e escrever a partir de um *tabela de sincronização* (utilizando o *IMobileServiceSyncTable* interface), que faz parte de um **SQLite** base de dados no seu dispositivo.

Para push e pull alterações entre o dispositivo e Mobile Services do Azure, pode utilizar um *contexto de sincronização* (*MobileServiceClient.SyncContext*), qual inicializar com a base de dados local para armazenar dados localmente.

1. No `TodoActivity.java`, comente a definição existente de `mToDoTable` e anule os comentários a versão da tabela de sincronização:
   
        private MobileServiceSyncTable<ToDoItem> mToDoTable;
2. No `onCreate` método, comente a inicialização existente do `mToDoTable` e anule os comentários esta definição:
   
        mToDoTable = mClient.getSyncTable("ToDoItem", ToDoItem.class);
3. No `refreshItemsFromTable` comente a definição de `results` e anule os comentários esta definição:
   
        // Offline Sync
        final List<ToDoItem> results = refreshItemsFromMobileServiceTableSyncTable();
4. Comente a definição de `refreshItemsFromMobileServiceTable`.
5. Anule os comentários a definição de `refreshItemsFromMobileServiceTableSyncTable`:
   
        private List<ToDoItem> refreshItemsFromMobileServiceTableSyncTable() throws ExecutionException, InterruptedException {
            //sync the data
            sync().get();
            Query query = QueryOperations.field("complete").
                    eq(val(false));
            return mToDoTable.read(query).get();
        }
6. Anule os comentários a definição de `sync`:
   
        private AsyncTask<Void, Void, Void> sync() {
            AsyncTask<Void, Void, Void> task = new AsyncTask<Void, Void, Void>(){
                @Override
                protected Void doInBackground(Void... params) {
                    try {
                        MobileServiceSyncContext syncContext = mClient.getSyncContext();
                        syncContext.push().get();
                        mToDoTable.pull(null).get();
                    } catch (final Exception e) {
                        createAndShowDialogFromTask(e, "Error");
                    }
                    return null;
                }
            };
            return runAsyncTask(task);
        }

## <a name="test-the-app"></a>Testar a aplicação
Nesta secção, testar o comportamento com Wi-Fi em e, em seguida, desative Wi-Fi para criar um cenário offline.

Ao adicionar itens de dados, são guardados no arquivo local do SQLite, mas não sincronizados com o serviço móvel até, prima a **atualizar** botão. Outras aplicações podem ter diferentes requisitos relativos ao dados devem ser sincronizadas, mas para fins de demonstração, este tutorial tem do utilizador explicitamente solicitá-la.

Quando prima este botão, inicia uma nova tarefa em segundo plano. Primeiro, este empurra todas as alterações efetuadas para o arquivo local utilizando o contexto de sincronização, em seguida, obtém todos os alterado dados do Azure para a tabela local.

### <a name="offline-testing"></a>Testar offline
1. Colocar o dispositivo ou simulador no *modo de avião*. Esta ação cria um cenário offline.
2. Adicione alguns *ToDo* itens ou marca alguns itens como concluídos. Sair, o dispositivo ou simulador (ou forçado a fechar a aplicação) e reinicie. Certifique-se de que as alterações foram continuadas no dispositivo porque estes são guardados no arquivo local do SQLite.
3. Ver o conteúdo do Azure *TodoItem* tabela com uma ferramenta SQL, tais como *SQL Server Management Studio*, ou um cliente REST, tais como *Fiddler* ou  *Postman*. Certifique-se de que os novos itens têm *não* foi sincronizada com êxito para o servidor
   
       + Para um back-end Node.js, vá para o [portal do Azure](https://portal.azure.com/), e na sua aplicação móvel de back-end clique **tabelas fácil** > **TodoItem** para ver os conteúdos do `TodoItem`tabela.
       + Para um back-end do .NET, ver os conteúdos de tabela com um SQL Server, tais como ferramenta *SQL Server Management Studio*, ou um cliente REST, tais como *Fiddler* ou *Postman*.
4. Ative Wi-Fi no dispositivo ou simulador. Em seguida, prima a **atualizar** botão.
5. Ver os dados de TodoItem novamente no portal do Azure. O novo e alterado TodoItems deverá agora aparecer.

## <a name="additional-resources"></a>Recursos Adicionais
* [sincronização de dados Offline no Mobile Apps do Azure]
* [Nuvem abrangem: Sincronização Offline nos Mobile Services do Azure] \(Nota: o vídeo está no Mobile Services, mas a sincronização offline funciona de forma semelhante em Mobile Apps do Azure\)

<!-- URLs. -->

[sincronização de dados Offline no Mobile Apps do Azure]: app-service-mobile-offline-data-sync.md

[criar uma aplicação Android]: app-service-mobile-android-get-started.md

[Nuvem abrangem: Sincronização Offline nos Mobile Services do Azure]: http://channel9.msdn.com/Shows/Cloud+Cover/Episode-155-Offline-Storage-with-Donna-Malayeri
[Azure Friday: Offline-enabled apps in Azure Mobile Services]: http://azure.microsoft.com/documentation/videos/azure-mobile-services-offline-enabled-apps-with-donna-malayeri/


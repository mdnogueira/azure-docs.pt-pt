---
title: "Sincronização de dados offline no Mobile Apps do Azure | Microsoft Docs"
description: "Referência conceptual e descrição geral sobre a funcionalidade de sincronização de dados offline para Mobile Apps do Azure"
documentationcenter: windows
author: ggailey777
manager: syntaxc4
editor: 
services: app-service\mobile
ms.assetid: 982fb683-8884-40da-96e6-77eeca2500e3
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 10/30/2016
ms.author: glenga
ms.openlocfilehash: 8e2bd755d14319f8c66f7ae7ec64fbd10801b39d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="offline-data-sync-in-azure-mobile-apps"></a>Sincronização de Dados Offline nas Aplicações Móveis do Azure
## <a name="what-is-offline-data-sync"></a>O que é a sincronização de dados offline?
Sincronização de dados offline é um cliente e servidor funcionalidade SDK do Azure Mobile Apps que torna mais fácil para os programadores criem aplicações que estão funcionais sem uma ligação de rede.

Quando a aplicação está no modo offline, pode ainda criar e modificar dados, que são guardados para um arquivo local. Quando a aplicação estiver novamente online, este possa sincronizar alterações locais com o back-end da aplicação móvel do Azure. A funcionalidade também inclui suporte para detetar conflitos quando o registo do mesmo é alterado no cliente e o back-end. Em seguida, podem ser processados conflitos no servidor ou o cliente.

Sincronização offline tem várias vantagens:

* Melhorar a capacidade de resposta da aplicação ao colocar em cache os dados de servidor localmente no dispositivo
* Criar aplicações robustas que permanecem útil quando existem problemas de rede
* Permitir aos utilizadores finais criar e modificar dados, mesmo quando há sem acesso de rede, o suporte para cenários com pouca ou nenhuma conectividade
* Sincronizar os dados em vários dispositivos e detetar conflitos quando o registo do mesmo é modificado pelo dois dispositivos
* Limitar a utilização de rede em redes de alta latência ou com tráfego limitado

Os tutoriais seguintes mostram como adicionar sincronização offline para os seus clientes móveis utilizar Mobile Apps do Azure:

* [Android: Ativar a sincronização offline]
* [Apache Cordova: Ativar a sincronização offline](app-service-mobile-cordova-get-started-offline-data.md)
* [iOS: Ativar a sincronização offline]
* [Xamarin iOS: Ativar a sincronização offline]
* [Xamarin Android: Ativar a sincronização offline]
* [Xamarin. Forms: Sincronização offline ativar](app-service-mobile-xamarin-forms-get-started-offline-data.md)
* [plataforma Universal do Windows: Ativar a sincronização offline]

## <a name="what-is-a-sync-table"></a>O que é uma tabela de sincronização?
Para aceder ao ponto final "/ tabelas", o cliente do Azure Mobile SDKs fornecer interfaces como `IMobileServiceTable` (cliente SDK do .NET) ou `MSTable` (iOS cliente). Estas APIs ligam diretamente ao back-end da aplicação móvel do Azure e falharem se o dispositivo de cliente não tem uma ligação de rede.

Para suportar a utilização offline, a sua aplicação em vez disso, deve utilizar o *tabela sincronização* APIs, tais como `IMobileServiceSyncTable` (cliente SDK do .NET) ou `MSSyncTable` (iOS cliente). Todas as mesmas operações CRUD (criar, ler, atualizar, eliminar) funcionam com sincronização de APIs de tabela, exceto agora ler a partir de ou escrever um *arquivo local*. Antes de quaisquer operações de tabela de sincronização podem ser efetuadas, o arquivo local tem de ser inicializado.

## <a name="what-is-a-local-store"></a>O que é um arquivo local?
Um arquivo local é a camada de persistência de dados no dispositivo cliente. Os SDKs do cliente de Mobile Apps do Azure fornecem uma implementação de arquivo local predefinido. No Windows, Xamarin e Android, se baseia no SQLite. No iOS, se baseia nos dados de núcleos.

Para utilizar a implementação baseada no SQLite no Windows Phone ou loja Windows 8.1, tem de instalar uma extensão do SQLite. Para obter mais informações, consulte [plataforma Universal do Windows: Ativar a sincronização offline]. Android e iOS, são enviados juntamente com uma versão do SQLite no sistema operativo do dispositivo, pelo que não é necessário fazer referência a sua própria versão do SQLite.

Os programadores também podem implementar os seus próprios arquivo local. Por exemplo, se pretender armazenar dados num formato encriptado no cliente móvel, pode definir um arquivo local que utiliza SQLCipher para encriptação.

## <a name="what-is-a-sync-context"></a>O que é um contexto de sincronização?
A *contexto de sincronização* está associado um objeto de cliente móvel (tais como `IMobileServiceClient` ou `MSClient`) e regista as alterações efetuadas com tabelas de sincronização. O contexto de sincronização mantém um *fila operação*, que mantém uma lista ordenada de operações de CUD (criação, atualização, eliminação) que seja posterior enviados para o servidor.

Um arquivo local está associado com o contexto de sincronização através de um método de inicialização, como `IMobileServicesSyncContext.InitializeAsync(localstore)` no [SDK de cliente .NET].

## <a name="how-sync-works"></a>Funciona como offline de sincronização
Quando utilizar tabelas de sincronização, o código de cliente controla quando alterações locais sejam sincronizadas com um back-end de aplicação móvel do Azure. Nada é enviado para o back-end até uma chamada para *push* alterações locais. Da mesma forma, o arquivo local é preenchido com novos dados apenas quando existe uma chamada para *solicitação* dados.

* **Push**: Push é uma operação no contexto de sincronização e envia todas CUD as alterações desde a última push. Tenha em atenção que não é possível enviar apenas uma tabela individuais as alterações, porque o caso contrário operações foi enviadas fora de ordem. Push executa uma série de chamadas REST ao seu back-end aplicação móvel do Azure, que por sua vez modifica a base de dados do servidor.
* **Solicitar**: solicitação é executada numa base por tabela e podem ser personalizada com uma consulta para obter apenas um subconjunto dos dados do servidor. Os SDKs de cliente do Azure Mobile inserir, em seguida, os dados resultantes para o arquivo local.
* **Pushes implícitas**: se uma solicitação for executada em relação a uma tabela que tem atualizações locais pendentes, a solicitação primeiro executa um `push()` no contexto de sincronização. Este push ajuda a minimizar conflitos entre as alterações que já estão a ser colocados em fila e novos dados a partir do servidor.
* **Sincronização incremental**: o primeiro parâmetro para a operação de extração é um *nome da consulta* que é utilizado apenas no cliente. Se utilizar um nome de consulta não nulo, o Azure Mobile SDK efetua uma *sincronização incremental*. Sempre que uma operação de solicitação devolve um conjunto de resultados, o mais recente `updatedAt` timestamp desse conjunto de resultados é armazenado nas tabelas de sistema local do SDK. Operações de extração subsequentes obter registos apenas após esse timestamp.

  Utilizar a sincronização incremental, o servidor tem de devolver significativo `updatedAt` os valores e também tem de suportar ordenar por este campo. No entanto, uma vez que o SDK adiciona a suas próprias ordenar o campo de updatedAt, não é possível utilizar uma consulta de extração que tenha a sua própria `orderBy` cláusula.

  O nome da consulta pode ser qualquer cadeia que escolher, mas tem de ser exclusivo para cada consulta na sua aplicação lógica.
  Caso contrário, as operações de extração diferente podem substituir o mesmo timestamp de sincronização incremental e as suas consultas podem devolver resultados incorretos.

  Se a consulta tem um parâmetro, é uma forma de criar um nome exclusivo de consulta incorporar o valor do parâmetro.
  Por exemplo, se está a filtrar no ID de utilizador, o nome da consulta foi constar da seguinte forma (c#):

        await todoTable.PullAsync("todoItems" + userid,
            syncTable.Where(u => u.UserId == userid));

  Se pretender ativamente sincronização incremental, transmitir `null` como o ID de consulta. Neste caso, todos os registos são obtidos em cada chamada para `PullAsync`, que é potencialmente ineficaz.
* **Remover**: pode apagar o conteúdo de utilizar o arquivo local `IMobileServiceSyncTable.PurgeAsync`.
  Remover poderá ser necessário se tiver dados obsoletos na base de dados de cliente ou se pretender eliminar todas as alterações pendentes.

  Uma remoção limpa uma tabela a partir do arquivo local. Se existirem operações a aguardar sincronização com a base de dados do servidor, a remoção emite uma exceção a menos que o *Forçar remoção* parâmetro está definido.

  Como um exemplo de dados obsoletos no cliente, suponha que o exemplo de "lista de tarefas", Device1 obtém apenas os itens que não estão concluídas. Um todoitem "Comprar milk" está marcado como concluído no servidor de por outro dispositivo. No entanto, Device1 ainda tem o todoitem "Comprar milk" no arquivo local porque-lo é extrair apenas itens que não estão marcadas como concluídas. Uma remoção limpa este item obsoleto.

## <a name="next-steps"></a>Passos seguintes
* [iOS: Ativar a sincronização offline]
* [Xamarin iOS: Ativar a sincronização offline]
* [Xamarin Android: Ativar a sincronização offline]
* [plataforma Universal do Windows: Ativar a sincronização offline]

<!-- Links -->
[SDK de cliente .NET]: app-service-mobile-dotnet-how-to-use-client-library.md
[Android: Ativar a sincronização offline]: app-service-mobile-android-get-started-offline-data.md
[iOS: Ativar a sincronização offline]: app-service-mobile-ios-get-started-offline-data.md
[Xamarin iOS: Ativar a sincronização offline]: app-service-mobile-xamarin-ios-get-started-offline-data.md
[Xamarin Android: Ativar a sincronização offline]: app-service-mobile-xamarin-android-get-started-offline-data.md
[plataforma Universal do Windows: Ativar a sincronização offline]: app-service-mobile-windows-store-dotnet-get-started-offline-data.md

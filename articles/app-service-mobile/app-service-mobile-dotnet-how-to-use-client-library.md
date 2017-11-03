---
title: Trabalhar com a biblioteca de cliente geridos Mobile Apps do App Service (Windows | Microsoft Docs
description: "Saiba como utilizar um cliente do .NET para aplicações móveis de serviço de aplicações do Azure com aplicações do Windows e Xamarin."
services: app-service\mobile
documentationcenter: 
author: ggailey777
manager: syntaxc4
editor: 
ms.assetid: 0280785c-e027-4e0d-aaf2-6f155e5a6197
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 01/04/2017
ms.author: glenga
ms.openlocfilehash: 2b83dfe2f3e2645713ff7e1acfb4d7f26fe0d0cd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-use-the-managed-client-for-azure-mobile-apps"></a>Como utilizar o cliente gerido para Mobile Apps do Azure
[!INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

## <a name="overview"></a>Descrição geral
Este guia mostra como efetuar cenários comuns utilizando a biblioteca de cliente gerido para a aplicação do Azure Mobile de serviço de aplicações para Windows e aplicações Xamarin. Se estiver familiarizado com as Mobile Apps, deve considerar primeiro a concluir o [início rápido do Azure Mobile Apps] [ 1] tutorial. Neste guia, iremos focar-se sobre o SDK gerido do lado do cliente. Para saber mais sobre os SDKs do lado do servidor para Mobile Apps, consulte a documentação para o [SDK do .NET Server] [ 2] ou [SDK Node.js do servidor][3].

## <a name="reference-documentation"></a>Documentação de referência
A documentação de referência para o cliente SDK está localizada aqui: [referência de cliente .NET do Azure Mobile Apps][4].
Também pode encontrar exemplos cliente várias o [repositório do GitHub do Azure-Samples][5].

## <a name="supported-platforms"></a>Plataformas suportadas
A plataforma de .NET suporta as seguintes plataformas:

* Versões de Xamarin Android para a API 19 através de 24 (KitKat através de Nougat)
* Versões de Xamarin iOS para iOS versões 8.0 e posteriores
* Plataforma universal do Windows
* Windows Phone 8.1
* Windows Phone 8.0, exceto as aplicações Silverlight

A autenticação de "servidor fluxo" utiliza um WebView para a IU apresentada.  Se o dispositivo não for capaz de apresentar uma IU de WebView, outros métodos de autenticação são necessárias.  Este SDK não, por conseguinte, é adequado para o tipo veja ou do mesmo modo restritos dispositivos.

## <a name="setup"></a>A configuração e pré-requisitos
Presumimos que já criou e publicou o projeto de back-end da aplicação móvel, que inclui pelo menos uma tabela.  A tabela com o nome no código utilizado neste tópico, `TodoItem` e tem as seguintes colunas: `Id`, `Text`, e `Complete`. Esta tabela é a mesma tabela criada quando concluir o [início rápido do Azure Mobile Apps][1].

O tipo de lado do cliente correspondente escrito em c# é a seguinte classe:

```
public class TodoItem
{
    public string Id { get; set; }

    [JsonProperty(PropertyName = "text")]
    public string Text { get; set; }

    [JsonProperty(PropertyName = "complete")]
    public bool Complete { get; set; }
}
```

O [JsonPropertyAttribute] [ 6] é utilizado para definir o *PropertyName* mapeamento entre o campo de cliente e o campo da tabela.

Para saber como criar tabelas no seu back-end do Mobile Apps, consulte o [tópico do SDK do .NET Server] [ 7] ou [tópico do SDK do servidor Node.js][8]. Se tiver criado o seu back-end de aplicação móvel no portal do Azure utilizando o guia de introdução, também pode utilizar o **tabelas fácil** definição o [portal do Azure].

### <a name="how-to-install-the-managed-client-sdk-package"></a>Como: instalar o pacote do SDK cliente gerido
Utilize um dos seguintes métodos para instalar o pacote do SDK cliente gerido para Mobile Apps do [NuGet][9]:

* **Visual Studio** clique no seu projeto, clique em **gerir pacotes NuGet**, procure o `Microsoft.Azure.Mobile.Client` do pacote, em seguida, clique em **instalar**.
* **Xamarin Studio** clique no seu projeto, clique em **adicionar** > **adicionar pacotes de NuGet**, procure o `Microsoft.Azure.Mobile.Client `do pacote e, em seguida, clique em **Adicionar pacote**.

No seu ficheiro de atividade principal, lembre-se de adicionar o seguinte **utilizando** instrução:

```
using Microsoft.WindowsAzure.MobileServices;
```

### <a name="symbolsource"></a>Como: trabalhar com símbolos de depuração no Visual Studio
Os símbolos para o espaço de nomes Microsoft.Azure.Mobile estão disponíveis no [SymbolSource][10].  Consulte o [SymbolSource instruções] [ 11] integrar SymbolSource com o Visual Studio.

## <a name="create-client"></a>Criar o cliente de Mobile Apps
O código seguinte cria o [MobileServiceClient] [ 12] objeto que é utilizado para aceder ao seu back-end de aplicação móvel.

```
var client = new MobileServiceClient("MOBILE_APP_URL");
```

No código anterior, substitua `MOBILE_APP_URL` com o URL de back-end da aplicação móvel, que se encontra no painel de back-end da aplicação móvel no [portal do Azure]. O objeto de MobileServiceClient deve ser um singleton.

## <a name="work-with-tables"></a>Trabalhar com tabelas
A secção seguinte fornece detalhes sobre como pesquisar e obter registos e modificar os dados na tabela.  São abrangidos os seguintes tópicos:

* [Criar uma referência de tabela](#instantiating)
* [Dados de consulta](#querying)
* [Filtrar os dados devolvidos](#filtering)
* [Ordenação devolvida dados](#sorting)
* [Devolver dados nas páginas](#paging)
* [Selecionar colunas específicas](#selecting)
* [Procurar um registo por Id](#lookingup)
* [Processamento de consultas sem tipo](#untypedqueries)
* [A inserir dados](#inserting)
* [Atualização dos dados](#updating)
* [A eliminação de dados](#deleting)
* [Resolução de conflitos e simultaneidade otimista](#optimisticconcurrency)
* [Enlace a uma Interface de utilizador do Windows](#binding)
* [Alterar o tamanho da página](#pagesize)

### <a name="instantiating"></a>Como: criar uma referência de tabela
O código que acede ao ou modifica dados numa tabela back-end chama funções no `MobileServiceTable` objeto. Obter uma referência à tabela ao chamar o [GetTable] método, da seguinte forma:

```
IMobileServiceTable<TodoItem> todoTable = client.GetTable<TodoItem>();
```

O objeto devolvido utiliza o modelo de serialização escrito. Um modelo de serialização sem tipo, também é suportado. O exemplo seguinte [cria uma referência a uma tabela sem tipo]:

```
// Get an untyped table reference
IMobileServiceTable untypedTodoTable = client.GetTable("TodoItem");
```

Nas consultas sem tipo, tem de especificar a cadeia de consulta de OData subjacente.

### <a name="querying"></a>Como: consultar dados a partir da aplicação móvel
Esta secção descreve como emitir consultas para o back-end aplicação móvel, que inclui as seguintes funcionalidades:

* [Filtrar os dados devolvidos](#filtering)
* [Ordenação devolvida dados](#sorting)
* [Devolver dados nas páginas](#paging)
* [Selecionar colunas específicas](#selecting)
* [Procurar dados por ID](#lookingup)

> [!NOTE]
> Um tamanho de página condicionada por servidor é imposto para impedir que todas as linhas a ser devolvido.  Paginação mantém os pedidos de predefinido para grandes conjuntos de dados de afetar negativamente o serviço.  Para devolver mais de 50 linhas, utilize o `Skip` e `Take` método, conforme descrito em [devolver dados nas páginas](#paging).

### <a name="filtering"></a>Como: filtro devolveu dados
O seguinte código ilustra como filtrar dados, incluindo um `Where` cláusula FROM na consulta. Devolve todos os itens de `todoTable` cujo `Complete` propriedade é igual a `false`. O [onde] função aplica-se de uma linha filtragem predicado para a consulta de tabela.

```
// This query filters out completed TodoItems and items without a timestamp.
List<TodoItem> items = await todoTable
    .Where(todoItem => todoItem.Complete == false)
    .ToListAsync();
```

Pode ver o URI do pedido enviado para o back-end utilizando software de inspeção de mensagem, tal como ferramentas de programador do browser ou [Fiddler]. Se observar o URI do pedido, repare que a cadeia de consulta está modificada:

```
GET /tables/todoitem?$filter=(complete+eq+false) HTTP/1.1
```

Este pedido de OData é convertido uma consulta SQL pelo SDK do servidor:

```
SELECT *
    FROM TodoItem
    WHERE ISNULL(complete, 0) = 0
```

A função que é transferida para o `Where` método pode ter um número aleatório de condições.

```
// This query filters out completed TodoItems where Text isn't null
List<TodoItem> items = await todoTable
    .Where(todoItem => todoItem.Complete == false && todoItem.Text != null)
    .ToListAsync();
```

Neste exemplo seria convertido de uma consulta SQL pelo SDK do servidor:

```
SELECT *
    FROM TodoItem
    WHERE ISNULL(complete, 0) = 0
          AND ISNULL(text, 0) = 0
```

Esta consulta também pode ser dividida em múltiplas cláusulas:

```
List<TodoItem> items = await todoTable
    .Where(todoItem => todoItem.Complete == false)
    .Where(todoItem => todoItem.Text != null)
    .ToListAsync();
```

Os dois métodos são equivalentes e podem ser utilizados-no alternadamente.  A opção anteriores&mdash;de concatenar múltiplos predicados numa consulta&mdash;é mais compacto e recomendada.

O `Where` cláusula suporta operações ser convertido o subconjunto de OData. Operações incluem:

* Os operadores relacionais (= =,! =, <, < =, >, > =),
* Operadores aritméticas (+, -, /, *, %),
* Número de precisão (Math.Floor, Math.Ceiling)
* Funções de cadeia (comprimento subcadeia, substituir, IndexOf, StartsWith, EndsWith),
* Propriedades de data (ano, mês, dia, hora, minuto, segundo),
* Aceder às propriedades de um objeto, e
* Expressões combinar qualquer uma destas operações.

Ao considerar que suporta o SDK do servidor, pode considerar o [OData v3 documentação].

### <a name="sorting"></a>Como: ordenação devolveu dados
O seguinte código ilustra como ordenar dados, incluindo um [OrderBy] ou [OrderByDescending] função na consulta. Devolve os itens de `todoTable` ordenados ascendente pelo `Text` campo.

```
// Sort items in ascending order by Text field
MobileServiceTableQuery<TodoItem> query = todoTable
                .OrderBy(todoItem => todoItem.Text)
List<TodoItem> items = await query.ToListAsync();

// Sort items in descending order by Text field
MobileServiceTableQuery<TodoItem> query = todoTable
                .OrderByDescending(todoItem => todoItem.Text)
List<TodoItem> items = await query.ToListAsync();
```

### <a name="paging"></a>Como: devolver dados nas páginas
Por predefinição, o back-end devolve apenas as primeiro 50 linhas. Pode aumentar o número de linhas devolvidos ao chamar o [demorar] método. Utilize `Take` juntamente com o [ignorar] método para pedir um específico "página" do conjunto de dados total devolvido pela consulta. A consulta seguinte, quando executada, devolve os itens superiores três na tabela.

```
// Define a filtered query that returns the top 3 items.
MobileServiceTableQuery<TodoItem> query = todoTable.Take(3);
List<TodoItem> items = await query.ToListAsync();
```

A seguinte consulta revista ignora os três primeiros resultados e devolve os resultados de três. Esta consulta produz a segunda "página" de dados, onde o tamanho de página é três itens.

```
// Define a filtered query that skips the top 3 items and returns the next 3 items.
MobileServiceTableQuery<TodoItem> query = todoTable.Skip(3).Take(3);
List<TodoItem> items = await query.ToListAsync();
```

O [IncludeTotalCount] a contagem total de pedidos de método *todos os* os registos que teriam sido devolvidos, ignorando quaisquer cláusula de paginação/limite especificada:

```
query = query.IncludeTotalCount();
```

Numa aplicação mundo real, pode utilizar consultas semelhantes ao anterior exemplo com um controlo de pager ou IU comparável para navegar entre as páginas.

> [!NOTE]
> Para substituir o limite de 50-linha num back-end de aplicação móvel, tem de aplicar também o [EnableQueryAttribute] para público obtenha método e especificar o comportamento de paginação. Quando aplicada para o método, o seguinte define o máximo devolvido linhas para 1000:
>
> `[EnableQuery(MaxTop=1000)]`


### <a name="selecting"></a>Como: selecionar colunas específicas
Pode especificar que conjunto de propriedades para incluir nos resultados da adicionando um [selecione] cláusula à sua consulta. Por exemplo, o código seguinte mostra como selecionar apenas um campo bem como selecionar e formatar vários campos:

```
// Select one field -- just the Text
MobileServiceTableQuery<TodoItem> query = todoTable
                .Select(todoItem => todoItem.Text);
List<string> items = await query.ToListAsync();

// Select multiple fields -- both Complete and Text info
MobileServiceTableQuery<TodoItem> query = todoTable
                .Select(todoItem => string.Format("{0} -- {1}",
                    todoItem.Text.PadRight(30), todoItem.Complete ?
                    "Now complete!" : "Incomplete!"));
List<string> items = await query.ToListAsync();
```

Todas as funções descritas até ao momento são cumulativas, pelo que iremos pode manter encadeamento-los. Cada chamada em cadeia afeta mais da consulta. Um exemplo mais:

```
MobileServiceTableQuery<TodoItem> query = todoTable
                .Where(todoItem => todoItem.Complete == false)
                .Select(todoItem => todoItem.Text)
                .Skip(3).
                .Take(3);
List<string> items = await query.ToListAsync();
```

### <a name="lookingup"></a>Como: procurar dados por ID
O [LookupAsync] função pode ser utilizada para procurar objetos da base de dados com um ID específico.

```
// This query filters out the item with the ID of 37BBF396-11F0-4B39-85C8-B319C729AF6D
TodoItem item = await todoTable.LookupAsync("37BBF396-11F0-4B39-85C8-B319C729AF6D");
```

### <a name="untypedqueries"></a>Como: executar consultas sem tipo
Quando executar uma consulta com um objeto de tabela sem tipo, tem de especificar explicitamente a cadeia de consulta de OData chamando [ReadAsync], como no exemplo seguinte:

```
// Lookup untyped data using OData
JToken untypedItems = await untypedTodoTable.ReadAsync("$filter=complete eq 0&$orderby=text");
```

Voltar valores JSON que podem ser utilizados como uma matriz de propriedades. Para obter mais informações sobre JToken e Newtonsoft Json.NET, consulte o [Json.NET] site.

### <a name="inserting"></a>Como: Inserir dados de um back-end de aplicação móvel
Todos os tipos de cliente tem de conter um membro com o nome **Id**, que está por predefinição uma cadeia. Isto **Id** é necessária para efetuar as operações CRUD de e para a sincronização offline. O seguinte código ilustra como utilizar o [InsertAsync] método para inserir novas linhas numa tabela. O parâmetro contém os dados para ser inserido como objeto .NET.

```
await todoTable.InsertAsync(todoItem);
```

Se um valor de ID exclusivo personalizado não está incluído no `todoItem` durante insert, um GUID é gerado pelo servidor.
Pode obter o Id de gerado através da inspeção o objeto após a chamada.

Para introduzir dados sem tipo, pode tirar partido de Json.NET:

```
JObject jo = new JObject();
jo.Add("Text", "Hello World");
jo.Add("Complete", false);
var inserted = await table.InsertAsync(jo);
```

Eis um exemplo utilizando um endereço de e-mail como um id exclusivo de cadeia:

```
JObject jo = new JObject();
jo.Add("id", "myemail@emaildomain.com");
jo.Add("Text", "Hello World");
jo.Add("Complete", false);
var inserted = await table.InsertAsync(jo);
```

### <a name="working-with-id-values"></a>Trabalhar com valores de ID
As Mobile Apps suporta valores de cadeia personalizado exclusivo para a tabela **id** coluna. Um valor de cadeia permite às aplicações a utilizar valores personalizados, tais como endereços de e-mail ou nomes de utilizador para o ID.  Os IDs de cadeia lhe fornecem as seguintes vantagens:

* Os iDs gerados sem o tornar uma ida e volta para a base de dados.
* Os registos são mais fáceis de intercalação de tabelas diferentes ou bases de dados.
* Valores de iDs podem integrar melhor com a lógica de uma aplicação.

Quando um valor de ID de cadeia não está definido num registo inserido, o back-end de aplicação móvel gera um valor exclusivo para o ID. Pode utilizar o [Guid.NewGuid] método para gerar os seus próprios valores de ID, no cliente ou no back-end.

```
JObject jo = new JObject();
jo.Add("id", Guid.NewGuid().ToString("N"));
```

### <a name="modifying"></a>Como: modificar dados um back-end de aplicação móvel
O seguinte código ilustra como utilizar o [UpdateAsync] método para atualizar um registo existente com o mesmo ID com novas informações. O parâmetro contém os dados sejam atualizados como objeto .NET.

```
await todoTable.UpdateAsync(todoItem);
```

Para atualizar os dados sem tipo, pode tirar partido das [Json.NET] da seguinte forma:

```
JObject jo = new JObject();
jo.Add("id", "37BBF396-11F0-4B39-85C8-B319C729AF6D");
jo.Add("Text", "Hello World");
jo.Add("Complete", false);
var inserted = await table.UpdateAsync(jo);
```

Um `id` campo tem de ser especificado quando efetuar uma atualização. O back-end utiliza o `id` campo para identificar qual linha para atualizar. O `id` campo pode obter o resultado do `InsertAsync` chamada. Um `ArgumentException` é gerado se tentar atualizar um item sem fornecer o `id` valor.

### <a name="deleting"></a>Como: eliminar dados de um back-end de aplicação móvel
O seguinte código ilustra como utilizar o [DeleteAsync] método para eliminar uma instância existente. A instância é identificada pelo `id` campo conjunto no `todoItem`.

```
await todoTable.DeleteAsync(todoItem);
```

Para eliminar dados sem tipo, pode tirar partido das Json.NET da seguinte forma:

```
JObject jo = new JObject();
jo.Add("id", "37BBF396-11F0-4B39-85C8-B319C729AF6D");
await table.DeleteAsync(jo);
```

Quando efetuar um pedido de eliminação, tem de ser especificado um ID. Outras propriedades não são transmitidas para o serviço ou são ignoradas no serviço. O resultado de uma `DeleteAsync` chamada é normalmente `null`. O resultado da pode obter o ID de passar o `InsertAsync` chamada. A `MobileServiceInvalidOperationException` é acionada ao tentar eliminar um item sem especificar o `id` campo.

### <a name="optimisticconcurrency"></a>Como: utilizar simultaneidade otimista para a resolução de conflito
Dois ou mais clientes podem escrever alterações para o mesmo item ao mesmo tempo. Sem deteção de conflitos, a última operação de escrita substituiria quaisquer atualizações anteriores. **Controlo de simultaneidade otimista** parte do princípio de que cada transação pode confirmar e, por conseguinte, não utilize qualquer bloqueio de recurso.  Antes de consolidar uma transação, o controlo de simultaneidade otimista verifica que nenhuma outra transação alterou os dados. Se os dados foram modificados, a committing transação é revertida.

As Mobile Apps suporta o controlo de simultaneidade otimista, controlar as alterações a cada item a utilizar o `version` coluna de propriedade de sistema que está definida para cada tabela no back-end da aplicação móvel. Sempre que um registo for atualizado, as Mobile Apps define o `version` propriedade para esse registo para um novo valor. Durante a cada pedido de atualização, o `version` propriedade de registo incluído com o pedido é comparado com a mesma propriedade para o registo no servidor. Se a versão passado com o pedido não coincide com o back-end, em seguida, a biblioteca de cliente gera um `MobileServicePreconditionFailedException<T>` exceção. O tipo incluído com a exceção é o registo de back-end que contém a versão de servidores do registo. A aplicação, em seguida, pode utilizar estas informações para decidir se pretende executar o pedido de atualização com o correto `version` valor de back-end ao consolidar as alterações.

Definir uma coluna na classe de tabela para o `version` propriedade de sistema para ativar a simultaneidade otimista. Por exemplo:

```
public class TodoItem
{
    public string Id { get; set; }

    [JsonProperty(PropertyName = "text")]
    public string Text { get; set; }

    [JsonProperty(PropertyName = "complete")]
    public bool Complete { get; set; }

    // *** Enable Optimistic Concurrency *** //
    [JsonProperty(PropertyName = "version")]
    public string Version { set; get; }
}
```

As aplicações que utilizam a tabelas sem tipo ativam simultaneidade otimista definindo a `Version` sinalizador no `SystemProperties` da tabela da seguinte forma.

```
//Enable optimistic concurrency by retrieving version
todoTable.SystemProperties |= MobileServiceSystemProperties.Version;
```

Para além de ativarem a simultaneidade otimista, também tem catch o `MobileServicePreconditionFailedException<T>` exceção no seu código ao chamar [UpdateAsync].  Resolva o conflito ao aplicar o correto `version` para o registo atualizado e a chamada [UpdateAsync] com o registo resolvido. O código seguinte mostra como resolver um conflito de escrita detetou uma vez:

```
private async void UpdateToDoItem(TodoItem item)
{
    MobileServicePreconditionFailedException<TodoItem> exception = null;

    try
    {
        //update at the remote table
        await todoTable.UpdateAsync(item);
    }
    catch (MobileServicePreconditionFailedException<TodoItem> writeException)
    {
        exception = writeException;
    }

    if (exception != null)
    {
        // Conflict detected, the item has changed since the last query
        // Resolve the conflict between the local and server item
        await ResolveConflict(item, exception.Item);
    }
}


private async Task ResolveConflict(TodoItem localItem, TodoItem serverItem)
{
    //Ask user to choose the resoltion between versions
    MessageDialog msgDialog = new MessageDialog(
        String.Format("Server Text: \"{0}\" \nLocal Text: \"{1}\"\n",
        serverItem.Text, localItem.Text),
        "CONFLICT DETECTED - Select a resolution:");

    UICommand localBtn = new UICommand("Commit Local Text");
    UICommand ServerBtn = new UICommand("Leave Server Text");
    msgDialog.Commands.Add(localBtn);
    msgDialog.Commands.Add(ServerBtn);

    localBtn.Invoked = async (IUICommand command) =>
    {
        // To resolve the conflict, update the version of the item being committed. Otherwise, you will keep
        // catching a MobileServicePreConditionFailedException.
        localItem.Version = serverItem.Version;

        // Updating recursively here just in case another change happened while the user was making a decision
        UpdateToDoItem(localItem);
    };

    ServerBtn.Invoked = async (IUICommand command) =>
    {
        RefreshTodoItems();
    };

    await msgDialog.ShowAsync();
}
```

Para obter mais informações, consulte o [Offline sincronização de dados no Azure Mobile Apps] tópico.

### <a name="binding"></a>Como: dados de enlace Mobile Apps para uma interface de utilizador do Windows
Esta secção mostra como apresentar objetos de dados devolvidos, utilizando elementos da IU numa aplicação do Windows.  O seguinte código de exemplo vincula a origem da lista com uma consulta para itens incompletos. O [MobileServiceCollection] cria uma coleção de enlace móveis aplicações com suporte para.

```
// This query filters out completed TodoItems.
MobileServiceCollection<TodoItem, TodoItem> items = await todoTable
    .Where(todoItem => todoItem.Complete == false)
    .ToCollectionAsync();

// itemsControl is an IEnumerable that could be bound to a UI list control
IEnumerable itemsControl  = items;

// Bind this to a ListBox
ListBox lb = new ListBox();
lb.ItemsSource = items;
```

Alguns controlos em tempo de execução gerido suportam uma interface chamada [ISupportIncrementalLoading]. Esta interface permite que os controlos solicitar dados adicionais quando o utilizador desloca. Não há suporte incorporado para esta interface para aplicações universais do Windows através de [MobileServiceIncrementalLoadingCollection], que processa automaticamente as chamadas provenientes de controlos. Utilize `MobileServiceIncrementalLoadingCollection` nas aplicações do Windows da seguinte forma:

```
MobileServiceIncrementalLoadingCollection<TodoItem,TodoItem> items;
items = todoTable.Where(todoItem => todoItem.Complete == false).ToIncrementalLoadingCollection();

ListBox lb = new ListBox();
lb.ItemsSource = items;
```

Para utilizar a nova coleção nas aplicações Windows Phone 8 e "Silverlight", utilize o `ToCollection` os métodos de extensão no `IMobileServiceTableQuery<T>` e `IMobileServiceTable<T>`. Para carregar dados, chamar `LoadMoreItemsAsync()`.

```
MobileServiceCollection<TodoItem, TodoItem> items = todoTable.Where(todoItem => todoItem.Complete==false).ToCollection();
await items.LoadMoreItemsAsync();
```

Ao utilizar a coleção criada chamando `ToCollectionAsync` ou `ToCollection`, obtenha uma coleção que pode ser vinculada a controlos de IU.  Esta coleção está ciente de paginação.  Uma vez que a coleção está a carregar os dados da rede, por vezes, a carregar falha. Para processar esses falhas, substitua o `OnException` método no `MobileServiceIncrementalLoadingCollection` para lidar com exceções resultantes de chamadas para `LoadMoreItemsAsync`.

Considere se a tabela tem muitos dos campos, mas pretende apresentar algumas no controlo. Pode utilizar as instruções na secção anterior "[selecionar colunas específicas](#selecting)" para selecionar colunas específicas para apresentar na IU.

### <a name="pagesize"></a>Alterar o tamanho da página
Mobile Apps do Azure devolve um máximo de 50 itens por pedido, por predefinição.  Pode alterar o tamanho de paginação, aumentar o tamanho máximo de página no cliente e servidor.  Para aumentar o tamanho da página pedida, especifique `PullOptions` quando utilizar `PullAsync()`:

```
PullOptions pullOptions = new PullOptions
    {
        MaxPageSize = 100
    };
```

Partindo do princípio de que efetuou a `PageSize` igual ou maior que 100 no servidor, um pedido devolve até 100 itens.

## <a name="#offlinesync"></a>Trabalhar com tabelas Offline
Tabelas offline utilizam um arquivo para armazenar dados locais do SQLite para utilização quando offline.  Tabela de todas as operações são efetuadas contra local SQLite armazenar em vez do arquivo de servidor remoto.  Para criar uma tabela offline, primeiro a preparar o seu projeto:

1. No Visual Studio, clique com botão direito na solução > **gerir pacotes NuGet para solução...** , em seguida, procure e instale o **Microsoft.Azure.Mobile.Client.SQLiteStore** pacote NuGet para todos os projetos na solução.
2. (Opcional) Para suportar dispositivos Windows, instale um dos seguintes pacotes de tempo de execução do SQLite:

   * **Tempo de execução do Windows 8.1:** instalar [SQLite para Windows 8.1][3].
   * **Windows Phone 8.1:** instalar [SQLite para Windows Phone 8.1][4].
   * **Plataforma universal do Windows** instalar [SQLite para o Windows Universal][5].
3. (Opcional). Para dispositivos Windows, clique em **referências** > **Adicionar referência...** , expanda o **Windows** pasta > **extensões**, em seguida, ative o adequado **SQLite para Windows** SDK juntamente com o **Visual C++ 2013 Runtime para o Windows** SDK.
    Os nomes de SQLite SDK variam ligeiramente com cada plataforma do Windows.

Antes de pode criar uma referência de tabela, o arquivo local tem de ser preparado:

```
var store = new MobileServiceSQLiteStore(Constants.OfflineDbPath);
store.DefineTable<TodoItem>();

//Initializes the SyncContext using the default IMobileServiceSyncHandler.
await this.client.SyncContext.InitializeAsync(store);
```

Inicialização do arquivo é feita normalmente imediatamente após o cliente é criado.  O **OfflineDbPath** deve ser um nome de ficheiro adequado para utilização em todas as plataformas suportadas.  Se o caminho é um caminho completamente qualificado (ou seja, começa com uma barra), que o caminho será utilizado.  Se o caminho não está totalmente qualificado, o ficheiro é colocado numa localização específica da plataforma.

* Para dispositivos iOS e Android, o caminho predefinido é a pasta "Ficheiros pessoal".
* Para dispositivos Windows, o caminho predefinido é a pasta de "AppData" específico da aplicação.

Uma referência de tabela pode ser obtida utilizando o `GetSyncTable<>` método:

```
var table = client.GetSyncTable<TodoItem>();
```

Não é necessário autenticar a utilizar uma tabela offline.  Só tem de autenticar quando estão a comunicar com o serviço de back-end.

### <a name="syncoffline"></a>A sincronizar a uma tabela Offline
Tabelas offline não estão sincronizadas com o back-end por predefinição.  Sincronização é dividida em duas partes.  Pode emitir alterações separadamente de transferir novos itens.  Segue-se um método de sincronização típica:

```
public async Task SyncAsync()
{
    ReadOnlyCollection<MobileServiceTableOperationError> syncErrors = null;

    try
    {
        await this.client.SyncContext.PushAsync();

        await this.todoTable.PullAsync(
            //The first parameter is a query name that is used internally by the client SDK to implement incremental sync.
            //Use a different query name for each unique query in your program
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

    // Simple error/conflict handling. A real application would handle the various errors like network conditions,
    // server conflicts and others via the IMobileServiceSyncHandler.
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

            Debug.WriteLine(@"Error executing sync operation. Item: {0} ({1}). Operation discarded.", error.TableName, error.Item["id"]);
        }
    }
}
```

Se o primeiro argumento para `PullAsync` é nulo, em seguida, a sincronização incremental não é utilizada.  Cada operação de sincronização obtém todos os registos.

O SDK efetua uma implícita `PushAsync()` antes de extrair registos.

Processamento de conflito acontece num `PullAsync()` método.  Pode lidar com conflitos da mesma forma que tabelas online.  O conflito é produzido quando `PullAsync()` denomina-se em vez de durante a inserção, atualização ou eliminação. Se ocorrem conflitos vários, estão incluídas para um único MobileServicePushFailedException.  Processe cada falha separadamente.

## <a name="#customapi"></a>Trabalhar com uma API personalizada
Uma API personalizada permite-lhe definir os pontos finais personalizados que expõem funcionalidades do servidor que não mapeiam para uma insert, update, eliminar ou operação de leitura. Ao utilizar uma API personalizada, pode ter mais controlo sobre mensagens, incluindo o ler, definir cabeçalhos de mensagens HTTP e definir um formato de corpo de mensagem que não sejam JSON.

Chamar uma API personalizada chamando um do [InvokeApiAsync] métodos no cliente. Por exemplo, a seguinte linha de código envia um pedido POST para o **completeAll** API no back-end:

```
var result = await client.InvokeApiAsync<MarkAllResult>("completeAll", System.Net.Http.HttpMethod.Post, null);
```

Este formulário é uma chamada de método escrito e requer que o **MarkAllResult** devolver o tipo está definido. São suportados métodos escritos e sem tipo.

O método InvokeApiAsync() prepends '/ api /' para a API que pretende chamar, a menos que a API começa com um '/'.
Por exemplo:

* `InvokeApiAsync("completeAll",...)`chama o /api/completeAll no back-end
* `InvokeApiAsync("/.auth/me",...)`chama o /.auth/me no back-end

Pode utilizar InvokeApiAsync chamar qualquer end WebAPI, incluindo esses WebAPIs que não estão definidos com Mobile Apps do Azure.  Quando utilizar InvokeApiAsync(), são enviados os cabeçalhos adequados, incluindo os cabeçalhos de autenticação, com o pedido.

## <a name="authentication"></a>Autenticar os utilizadores
As Mobile Apps suporta autenticar e autorizar utilizadores de aplicações através de vários fornecedores de identidade externas: Facebook, Google, Account Microsoft, Twitter e o Azure Active Directory. Pode definir as permissões em tabelas para restringir o acesso de operações específicas para apenas os utilizadores autenticados. Também pode utilizar a identidade de utilizadores autenticados para implementar as regras de autorização em scripts de servidor. Para obter mais informações, consulte o tutorial [Add authentication to your app (Adicionar autenticação à sua aplicação)].

Dois fluxos de autenticação são suportados: *geridos pelo cliente* e *servidor gerido* fluxo. O fluxo de servidor gerido fornece a experiência de autenticação mais simples, como baseia-se na interface de autenticação de web do fornecedor. Permite que o fluxo de cliente gerido para uma integração mais profunda com as capacidades específicas do dispositivo como depende específica do fornecedor específicos do dispositivo SDKs.

> [!NOTE]
> Recomendamos que utilize um fluxo de cliente gerido nas suas aplicações de produção.

Para configurar a autenticação, tem de registar a sua aplicação com um ou mais fornecedores de identidade.  O fornecedor de identidade gera um ID de cliente e um segredo do cliente para a sua aplicação.  Estes valores estarem definidos, em seguida, no seu back-end para ativar a autenticação/autorização do App Service do Azure.  Para obter mais informações, siga as instruções detalhadas no tutorial [Add authentication to your app (Adicionar autenticação à sua aplicação)].

Os tópicos seguintes estão incluídos nesta secção:

* [Autenticação de cliente gerido](#clientflow)
* [Autenticação de servidor gerido](#serverflow)
* [Colocação em cache o token de autenticação](#caching)

### <a name="clientflow"></a>Autenticação de cliente gerido
A aplicação pode independentemente, contacte o fornecedor de identidade e, em seguida, forneça o token devolvido durante o início de sessão com o back-end. Este fluxo de cliente permite-lhe fornecer uma experiência de início de sessão único para os utilizadores ou obter dados de utilizador adicionais a partir do fornecedor de identidade. Autenticação de fluxo de cliente é preferida para a utilização de um fluxo de servidor como o fornecedor de identidade SDK fornece uma funcionalidade do UX mais nativa e permite a personalização adicional.

Os exemplos são fornecidos para os seguintes padrões de autenticação de fluxo de cliente:

* [Biblioteca de autenticação do Active Directory](#adal)
* [Facebook ou da Google](#client-facebook)
* [Live SDK](#client-livesdk)

#### <a name="adal"></a>Autenticar os utilizadores com a biblioteca de autenticação do Active Directory
Pode utilizar o Active Directory Authentication Library (ADAL) para autenticação de utilizador de iniciação do cliente utilizando a autenticação do Azure Active Directory.

1. Configurar o back-end da aplicação móvel para início de sessão no AAD, seguindo o [como configurar o serviço de aplicações para início de sessão do Active Directory] tutorial. Certifique-se concluir o passo opcional de registar uma aplicação cliente nativa.
2. No Visual Studio ou no Xamarin Studio, abra o projeto e adicione uma referência para o `Microsoft.IdentityModel.CLients.ActiveDirectory` pacote NuGet. Quando pesquisar, inclua as versões de pré-lançamento.
3. Adicione o seguinte código à sua aplicação, de acordo com a plataforma que está a utilizar. Em cada um, efetuar a substituição de seguintes:

   * Substitua **aqui de autoridade de inserção** com o nome do inquilino no qual que aprovisionou a sua aplicação. O formato deve ser https://login.microsoftonline.com/contoso.onmicrosoft.com. Este valor pode ser copiado a partir do separador no Azure Active Directory no domínio a [portal clássico do Azure].
   * Substitua **INSERT-RESOURCE-ID-aqui** com o ID de cliente para o back-end da aplicação móvel. Pode obter o ID de cliente do **avançadas** separador em **definições do Azure Active Directory** no portal.
   * Substitua **INSERT-cliente ID aqui** com o ID de cliente que copiou da aplicação cliente nativa.
   * Substitua **INSERT-REDIRECIONAMENTO-URI-aqui** com o seu site */.auth/login/done* ponto final, utilizando o esquema de HTTPS. Este valor deve ser semelhante *https://contoso.azurewebsites.net/.auth/login/done*.

     O código necessário para cada plataforma segue:

     **Windows:**

    ```
    private MobileServiceUser user;
    private async Task AuthenticateAsync()
    {

        string authority = "INSERT-AUTHORITY-HERE";
        string resourceId = "INSERT-RESOURCE-ID-HERE";
        string clientId = "INSERT-CLIENT-ID-HERE";
        string redirectUri = "INSERT-REDIRECT-URI-HERE";
        while (user == null)
        {
            string message;
            try
            {
                AuthenticationContext ac = new AuthenticationContext(authority);
                AuthenticationResult ar = await ac.AcquireTokenAsync(resourceId, clientId,
                    new Uri(redirectUri), new PlatformParameters(PromptBehavior.Auto, false) );
                JObject payload = new JObject();
                payload["access_token"] = ar.AccessToken;
                user = await App.MobileService.LoginAsync(
                    MobileServiceAuthenticationProvider.WindowsAzureActiveDirectory, payload);
                message = string.Format("You are now logged in - {0}", user.UserId);
            }
            catch (InvalidOperationException)
            {
                message = "You must log in. Login Required";
            }
            var dialog = new MessageDialog(message);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
        }
    }
    ```

     **Xamarin.iOS**

    ```
    private MobileServiceUser user;
    private async Task AuthenticateAsync(UIViewController view)
    {

        string authority = "INSERT-AUTHORITY-HERE";
        string resourceId = "INSERT-RESOURCE-ID-HERE";
        string clientId = "INSERT-CLIENT-ID-HERE";
        string redirectUri = "INSERT-REDIRECT-URI-HERE";
        try
        {
            AuthenticationContext ac = new AuthenticationContext(authority);
            AuthenticationResult ar = await ac.AcquireTokenAsync(resourceId, clientId,
                new Uri(redirectUri), new PlatformParameters(view));
            JObject payload = new JObject();
            payload["access_token"] = ar.AccessToken;
            user = await client.LoginAsync(
                MobileServiceAuthenticationProvider.WindowsAzureActiveDirectory, payload);
        }
        catch (Exception ex)
        {
            Console.Error.WriteLine(@"ERROR - AUTHENTICATION FAILED {0}", ex.Message);
        }
    }
    ```

     **Xamarin.Android**

    ```
    private MobileServiceUser user;
    private async Task AuthenticateAsync()
    {

        string authority = "INSERT-AUTHORITY-HERE";
        string resourceId = "INSERT-RESOURCE-ID-HERE";
        string clientId = "INSERT-CLIENT-ID-HERE";
        string redirectUri = "INSERT-REDIRECT-URI-HERE";
        try
        {
            AuthenticationContext ac = new AuthenticationContext(authority);
            AuthenticationResult ar = await ac.AcquireTokenAsync(resourceId, clientId,
                new Uri(redirectUri), new PlatformParameters(this));
            JObject payload = new JObject();
            payload["access_token"] = ar.AccessToken;
            user = await client.LoginAsync(
                MobileServiceAuthenticationProvider.WindowsAzureActiveDirectory, payload);
        }
        catch (Exception ex)
        {
            AlertDialog.Builder builder = new AlertDialog.Builder(this);
            builder.SetMessage(ex.Message);
            builder.SetTitle("You must log in. Login Required");
            builder.Create().Show();
        }
    }
    protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
    {

        base.OnActivityResult(requestCode, resultCode, data);
        AuthenticationAgentContinuationHelper.SetAuthenticationAgentContinuationEventArgs(requestCode, resultCode, data);
    }
    ```

#### <a name="client-facebook"></a>Início de sessão utilizando um token do Facebook ou da Google
Pode utilizar o fluxo de cliente conforme mostrado neste fragmento para Facebook ou da Google.

```
var token = new JObject();
// Replace access_token_value with actual value of your access token obtained
// using the Facebook or Google SDK.
token.Add("access_token", "access_token_value");

private MobileServiceUser user;
private async Task AuthenticateAsync()
{
    while (user == null)
    {
        string message;
        try
        {
            // Change MobileServiceAuthenticationProvider.Facebook
            // to MobileServiceAuthenticationProvider.Google if using Google auth.
            user = await client.LoginAsync(MobileServiceAuthenticationProvider.Facebook, token);
            message = string.Format("You are now logged in - {0}", user.UserId);
        }
        catch (InvalidOperationException)
        {
            message = "You must log in. Login Required";
        }

        var dialog = new MessageDialog(message);
        dialog.Commands.Add(new UICommand("OK"));
        await dialog.ShowAsync();
    }
}
```

#### <a name="client-livesdk"></a>Início de sessão único com Account Microsoft com o SDK em direto
Para autenticar os utilizadores, tem de registar a aplicação na conta Microsoft Developer Center. Configure os detalhes de registo no back-end da aplicação móvel. Para criar um registo de conta Microsoft e ligá-lo ao back-end da aplicação móvel, concluir os passos [registar a sua aplicação para utilizar um início de sessão de conta Microsoft]. Se tiver loja Windows e Windows Phone 8/Silverlight versões da sua aplicação, registe primeiro a versão de loja do Windows.

O seguinte código autentica utilizando o SDK em direto e utiliza o token devolvido para iniciar sessão no seu back-end de aplicação móvel.

```
private LiveConnectSession session;
    //private static string clientId = "<microsoft-account-client-id>";
private async System.Threading.Tasks.Task AuthenticateAsync()
{

    // Get the URL the Mobile App backend.
    var serviceUrl = App.MobileService.ApplicationUri.AbsoluteUri;

    // Create the authentication client for Windows Store using the service URL.
    LiveAuthClient liveIdClient = new LiveAuthClient(serviceUrl);
    //// Create the authentication client for Windows Phone using the client ID of the registration.
    //LiveAuthClient liveIdClient = new LiveAuthClient(clientId);

    while (session == null)
    {
        // Request the authentication token from the Live authentication service.
        // The wl.basic scope should always be requested.  Other scopes can be added
        LiveLoginResult result = await liveIdClient.LoginAsync(new string[] { "wl.basic" });
        if (result.Status == LiveConnectSessionStatus.Connected)
        {
            session = result.Session;

            // Get information about the logged-in user.
            LiveConnectClient client = new LiveConnectClient(session);
            LiveOperationResult meResult = await client.GetAsync("me");

            // Use the Microsoft account auth token to sign in to App Service.
            MobileServiceUser loginResult = await App.MobileService
                .LoginWithMicrosoftAccountAsync(result.Session.AuthenticationToken);

            // Display a personalized sign-in greeting.
            string title = string.Format("Welcome {0}!", meResult.Result["first_name"]);
            var message = string.Format("You are now logged in - {0}", loginResult.UserId);
            var dialog = new MessageDialog(message, title);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
        }
        else
        {
            session = null;
            var dialog = new MessageDialog("You must log in.", "Login Required");
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
        }
    }
}
```

Para obter mais informações, consulte o [Windows Live SDK] documentação.

### <a name="serverflow"></a>Autenticação de servidor gerido
Depois de ter registado o fornecedor de identidade, chame o [LoginAsync] método no [MobileServiceClient] com o [MobileServiceAuthenticationProvider] valor do seu fornecedor. Por exemplo, o código seguinte inicia um servidor fluxo início de sessão utilizando o Facebook.

```
private MobileServiceUser user;
private async System.Threading.Tasks.Task Authenticate()
{
    while (user == null)
    {
        string message;
        try
        {
            user = await client
                .LoginAsync(MobileServiceAuthenticationProvider.Facebook);
            message =
                string.Format("You are now logged in - {0}", user.UserId);
        }
        catch (InvalidOperationException)
        {
            message = "You must log in. Login Required";
        }

        var dialog = new MessageDialog(message);
        dialog.Commands.Add(new UICommand("OK"));
        await dialog.ShowAsync();
    }
}
```

Se estiver a utilizar um fornecedor de identidade que não seja o Facebook, altere o valor de [MobileServiceAuthenticationProvider] para o valor para o fornecedor.

Um fluxo de servidor, o App Service do Azure gere o fluxo de autenticação OAuth ao apresentar a página de início de sessão do fornecedor selecionado.  Depois do devolve de fornecedor de identidade, App Service do Azure gera um token de autenticação do serviço de aplicações. O [LoginAsync] método devolve um [MobileServiceUser], que fornece ambos o [UserId] do utilizador autenticado e a [MobileServiceAuthenticationToken], como um token de web JSON (JWT). Este token pode ser colocado em cache e reutilizado até expirar. Para obter mais informações, consulte [colocação em cache o token de autenticação](#caching).

### <a name="caching"></a>Colocação em cache o token de autenticação
Em alguns casos, a chamada do método de início de sessão pode ser evitada após a primeira autenticação com êxito ao armazenar o token de autenticação do fornecedor.  Podem utilizar aplicações da loja Windows e UWP [PasswordVault] para colocar em cache o token de autenticação atual depois de um bem-sucedida início de sessão, da seguinte forma:

```
await client.LoginAsync(MobileServiceAuthenticationProvider.Facebook);

PasswordVault vault = new PasswordVault();
vault.Add(new PasswordCredential("Facebook", client.currentUser.UserId,
    client.currentUser.MobileServiceAuthenticationToken));
```

O valor de ID de utilizador é armazenado como o nome da credencial de utilizador e o token é armazenado como a palavra-passe. No start-ups subsequentes, pode verificar o **PasswordVault** credenciais em cache. O exemplo seguinte utiliza credenciais em cache quando estes forem detetados e, caso contrário, se tentar autenticar novamente com o back-end:

```
// Try to retrieve stored credentials.
var creds = vault.FindAllByResource("Facebook").FirstOrDefault();
if (creds != null)
{
    // Create the current user from the stored credentials.
    client.currentUser = new MobileServiceUser(creds.UserName);
    client.currentUser.MobileServiceAuthenticationToken =
        vault.Retrieve("Facebook", creds.UserName).Password;
}
else
{
    // Regular login flow and cache the token as shown above.
}
```

Quando terminar um utilizador, tem também de remover a credencial armazenada, da seguinte forma:

```
client.Logout();
vault.Remove(vault.Retrieve("Facebook", client.currentUser.UserId));
```

Utilização de aplicações Xamarin o [Xamarin.Auth] APIs para armazenar com segurança as credenciais num **conta** objeto. Para obter um exemplo de utilização estas APIs, consulte o [AuthStore.cs] ficheiro de código no [fotografia ContosoMoments partilha exemplo](https://github.com/azure-appservice-samples/ContosoMoments).

Quando utiliza a autenticação de cliente gerido, pode também colocar em cache o token de acesso obtido a partir do seu fornecedor como o Facebook e Twitter. Este token pode ser fornecida para pedir um novo token de autenticação de back-end, da seguinte forma:

```
var token = new JObject();
// Replace <your_access_token_value> with actual value of your access token
token.Add("access_token", "<your_access_token_value>");

// Authenticate using the access token.
await client.LoginAsync(MobileServiceAuthenticationProvider.Facebook, token);
```

## <a name="pushnotifications"></a>Notificações Push
Os tópicos seguintes incluem as notificações Push:

* [Registar-se as notificações Push](#register-for-push)
* [Obter um SID do pacote da loja Windows](#package-sid)
* [Registar modelos de plataforma](#register-xplat)

### <a name="register-for-push"></a>Como: registar-se as notificações Push
O cliente de Mobile Apps permite-lhe para se registar para notificações push com Notification Hubs do Azure. Ao registar, obtenha um identificador que obtém do específicos da plataforma Push Notification Service (PNS). Em seguida, fornecer este valor juntamente com quaisquer etiquetas ao criar o registo. O seguinte código regista a aplicação do Windows para notificações push com o serviço de notificação do Windows (WNS):

```
private async void InitNotificationsAsync()
{
    // Request a push notification channel.
    var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

    // Register for notifications using the new channel.
    await MobileService.GetPush().RegisterNativeAsync(channel.Uri, null);
}
```

Se estiver a enviar para WNS, em seguida, tem de [obter um SID do pacote da loja Windows](#package-sid).  Para obter mais informações sobre as aplicações do Windows, incluindo como registar a registos de modelo, consulte [adicionar notificações push à sua aplicação].

Pedir etiquetas do cliente não é suportada.  Pedidos de tag silenciosamente são ignorados do registo.
Se pretender registar o seu dispositivo com etiquetas, crie uma API personalizada que utiliza a API dos Notification Hubs para efetuar o registo em seu nome.  [Chamar a API personalizada](#customapi) em vez do `RegisterNativeAsync()` método.

### <a name="package-sid"></a>Como: obter um SID do pacote da loja Windows
É necessário um SID do pacote para ativar notificações de push em aplicações da loja Windows.  Para receber um SID do pacote, registe a aplicação à loja Windows.

Para obter este valor:

1. No Explorador de soluções do Visual Studio, clique no projeto de aplicação loja Windows, clique em **arquivo** > **associar aplicação à loja...** .
2. No assistente, clique em **seguinte**, inicie sessão com a sua conta Microsoft, escreva um nome para a sua aplicação no **reservar um novo nome de aplicação**, em seguida, clique em **reserva**.
3. Após o registo de aplicação é criado com êxito, selecione o nome da aplicação, clique em **seguinte**e, em seguida, clique em **associar**.
4. Inicie sessão no [Windows Dev Center] com a Account Microsoft. Em **as minhas aplicações**, clique o registo de aplicação que criou.
5. Clique em **gestão de aplicações** > **identidade da aplicação**e, em seguida, desloque para baixo para localizar o **SID do pacote**.

Muitas utilizações do SID do pacote processá-la como um URI, caso em que tem de utilizar *ms-app: / /* como o esquema. Tome nota da versão do seu formado por concatenar este valor como um prefixo de SID do pacote.

Aplicações Xamarin requerem alguns códigos adicionais para poder registar uma aplicação em execução nas plataformas Android ou iOS. Para obter mais informações, consulte o tópico para a sua plataforma:

* [Xamarin.Android](app-service-mobile-xamarin-android-get-started-push.md#add-push)
* [Xamarin.iOS](app-service-mobile-xamarin-ios-get-started-push.md#add-push-notifications-to-your-app)

### <a name="register-xplat"></a>Como: registar modelos de push para enviar notificações de plataforma
Para registar modelos, utilize o `RegisterAsync()` método com os modelos, da seguinte forma:

```
JObject templates = myTemplates();
MobileService.GetPush().RegisterAsync(channel.Uri, templates);
```

Os modelos devem ser `JObject` tipos e pode conter vários modelos no seguinte formato JSON:

```
public JObject myTemplates()
{
    // single template for Windows Notification Service toast
    var template = "<toast><visual><binding template=\"ToastText01\"><text id=\"1\">$(message)</text></binding></visual></toast>";

    var templates = new JObject
    {
        ["generic-message"] = new JObject
        {
            ["body"] = template,
            ["headers"] = new JObject
            {
                ["X-WNS-Type"] = "wns/toast"
            },
            ["tags"] = new JArray()
        },
        ["more-templates"] = new JObject {...}
    };
    return templates;
}
```

O método **RegisterAsync()** também aceita mosaicos secundário:

```
MobileService.GetPush().RegisterAsync(string channelUri, JObject templates, JObject secondaryTiles);
```

Todas as etiquetas estão repartidas ausente durante o registo de segurança. Para adicionar etiquetas ao instalações ou modelos dentro de instalações, consulte [funcionam com o servidor de back-end .NET SDK de Mobile Apps do Azure].

Para enviar notificações utilizando estes modelos registados, consulte o [APIs de Hubs de notificação].

## <a name="misc"></a>Tópicos de diversas
### <a name="errors"></a>Como: processar erros
Quando ocorre um erro no back-end, o cliente SDK desencadeia um `MobileServiceInvalidOperationException`.  O exemplo seguinte mostra como processar uma exceção que é devolvida pelo back-end:

```
private async void InsertTodoItem(TodoItem todoItem)
{
    // This code inserts a new TodoItem into the database. When the operation completes
    // and App Service has assigned an Id, the item is added to the CollectionView
    try
    {
        await todoTable.InsertAsync(todoItem);
        items.Add(todoItem);
    }
    catch (MobileServiceInvalidOperationException e)
    {
        // Handle error
    }
}
```

Outro exemplo de lidar com condições de erro pode ser encontrado no [exemplo de ficheiros de aplicações móveis]. O [LoggingHandler] exemplo fornece um processador de delegado de registo para registar pedidos efetuados para o back-end.

### <a name="headers"></a>Como: personalizar cabeçalhos de pedido
Para suportar o cenário de aplicação específica, poderá ter de personalizar a comunicação com o back-end de aplicação móvel. Por exemplo, poderá pretender adicionar um cabeçalho personalizado a cada pedido de saída ou mesmo alterar códigos de estado de respostas. Pode utilizar um personalizado [DelegatingHandler], como no exemplo seguinte:

```
public async Task CallClientWithHandler()
{
    MobileServiceClient client = new MobileServiceClient("AppUrl", new MyHandler());
    IMobileServiceTable<TodoItem> todoTable = client.GetTable<TodoItem>();
    var newItem = new TodoItem { Text = "Hello world", Complete = false };
    await todoTable.InsertAsync(newItem);
}

public class MyHandler : DelegatingHandler
{
    protected override async Task<HttpResponseMessage>
        SendAsync(HttpRequestMessage request, CancellationToken cancellationToken)
    {
        // Change the request-side here based on the HttpRequestMessage
        request.Headers.Add("x-my-header", "my value");

        // Do the request
        var response = await base.SendAsync(request, cancellationToken);

        // Change the response-side here based on the HttpResponseMessage

        // Return the modified response
        return response;
    }
}
```


<!-- Anchors. -->


<!-- Images. -->

<!-- URLs. -->
[1]: app-service-mobile-windows-store-dotnet-get-started.md
[2]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[3]: app-service-mobile-node-backend-how-to-use-server-sdk.md
[4]: https://msdn.microsoft.com/en-us/library/azure/mt419521(v=azure.10).aspx
[5]: https://github.com/Azure-Samples
[6]: http://www.newtonsoft.com/json/help/html/Properties_T_Newtonsoft_Json_JsonPropertyAttribute.htm
[7]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#define-table-controller
[8]: app-service-mobile-node-backend-how-to-use-server-sdk.md#TableOperations
[9]: https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/
[10]: http://www.symbolsource.org/
[11]: http://www.symbolsource.org/Public/Wiki/Using
[12]: https://msdn.microsoft.com/en-us/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient(v=azure.10).aspx

[Add authentication to your app (Adicionar autenticação à sua aplicação)]: app-service-mobile-windows-store-dotnet-get-started-users.md
[Offline sincronização de dados no Azure Mobile Apps]: app-service-mobile-offline-data-sync.md
[adicionar notificações push à sua aplicação]: app-service-mobile-windows-store-dotnet-get-started-push.md
[registar a sua aplicação para utilizar um início de sessão de conta Microsoft]: ../app-service/app-service-mobile-how-to-configure-microsoft-authentication.md
[como configurar o serviço de aplicações para início de sessão do Active Directory]: ../app-service/app-service-mobile-how-to-configure-active-directory-authentication.md

<!-- Microsoft URLs. -->
[MobileServiceCollection]: https://msdn.microsoft.com/en-us/library/azure/dn250636(v=azure.10).aspx
[MobileServiceIncrementalLoadingCollection]: https://msdn.microsoft.com/en-us/library/azure/dn268408(v=azure.10).aspx
[MobileServiceAuthenticationProvider]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceauthenticationprovider(v=azure.10).aspx
[MobileServiceUser]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser(v=azure.10).aspx
[MobileServiceAuthenticationToken]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.mobileserviceauthenticationtoken(v=azure.10).aspx
[GetTable]: https://msdn.microsoft.com/en-us/library/azure/jj554275(v=azure.10).aspx
[cria uma referência a uma tabela sem tipo]: https://msdn.microsoft.com/en-us/library/azure/jj554278(v=azure.10).aspx
[DeleteAsync]: https://msdn.microsoft.com/en-us/library/azure/dn296407(v=azure.10).aspx
[IncludeTotalCount]: https://msdn.microsoft.com/en-us/library/azure/dn250560(v=azure.10).aspx
[InsertAsync]: https://msdn.microsoft.com/en-us/library/azure/dn296400(v=azure.10).aspx
[InvokeApiAsync]: https://msdn.microsoft.com/en-us/library/azure/dn268343(v=azure.10).aspx
[LoginAsync]: https://msdn.microsoft.com/en-us/library/azure/dn296411(v=azure.10).aspx
[LookupAsync]: https://msdn.microsoft.com/en-us/library/azure/jj871654(v=azure.10).aspx
[OrderBy]: https://msdn.microsoft.com/en-us/library/azure/dn250572(v=azure.10).aspx
[OrderByDescending]: https://msdn.microsoft.com/en-us/library/azure/dn250568(v=azure.10).aspx
[ReadAsync]: https://msdn.microsoft.com/en-us/library/azure/mt691741(v=azure.10).aspx
[demorar]: https://msdn.microsoft.com/en-us/library/azure/dn250574(v=azure.10).aspx
[selecione]: https://msdn.microsoft.com/en-us/library/azure/dn250569(v=azure.10).aspx
[ignorar]: https://msdn.microsoft.com/en-us/library/azure/dn250573(v=azure.10).aspx
[UpdateAsync]: https://msdn.microsoft.com/en-us/library/azure/dn250536.(v=azure.10)aspx
[ID de utilizador]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.userid(v=azure.10).aspx
[onde]: https://msdn.microsoft.com/en-us/library/azure/dn250579(v=azure.10).aspx
[portal do Azure]: https://portal.azure.com/
[portal clássico do Azure]: https://manage.windowsazure.com/
[EnableQueryAttribute]: https://msdn.microsoft.com/library/system.web.http.odata.enablequeryattribute.aspx
[Guid.NewGuid]: https://msdn.microsoft.com/en-us/library/system.guid.newguid(v=vs.110).aspx
[ISupportIncrementalLoading]: http://msdn.microsoft.com/library/windows/apps/Hh701916.aspx
[Windows Dev Center]: https://dev.windows.com/en-us/overview
[DelegatingHandler]: https://msdn.microsoft.com/library/system.net.http.delegatinghandler(v=vs.110).aspx
[Windows Live SDK]: https://msdn.microsoft.com/en-us/library/bb404787.aspx
[PasswordVault]: http://msdn.microsoft.com/library/windows/apps/windows.security.credentials.passwordvault.aspx
[ProtectedData]: http://msdn.microsoft.com/library/system.security.cryptography.protecteddata%28VS.95%29.aspx
[APIs de Hubs de notificação]: https://msdn.microsoft.com/library/azure/dn495101.aspx
[exemplo de ficheiros de aplicações móveis]: https://github.com/Azure-Samples/app-service-mobile-dotnet-todo-list-files
[LoggingHandler]: https://github.com/Azure-Samples/app-service-mobile-dotnet-todo-list-files/blob/master/src/client/MobileAppsFilesSample/Helpers/LoggingHandler.cs#L63

<!-- External URLs -->
[OData v3 documentação]: http://www.odata.org/documentation/odata-version-3-0/
[Fiddler]: http://www.telerik.com/fiddler
[Json.NET]: http://www.newtonsoft.com/json
[Xamarin.Auth]: https://components.xamarin.com/view/xamarin.auth/
[AuthStore.cs]: https://github.com/azure-appservice-samples/ContosoMoments
[ContosoMoments photo sharing sample]: https://github.com/azure-appservice-samples/ContosoMoments

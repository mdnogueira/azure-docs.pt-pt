---
title: Criar um acionador de HTTP com um enlace de entrada de base de dados do Azure Cosmos | Microsoft Docs
description: "Saiba como utilizar as funções do Azure com os Acionadores de HTTP para consulta de base de dados do Azure Cosmos."
services: cosmos-db
documentationcenter: 
author: mimig1
manager: jhubbard
ms.assetid: 
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: mvc
ms.date: 09/25/2017
ms.author: mimig
ms.openlocfilehash: 86a660309fd3fd80f10f706ff460af2309c12174
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="create-an-azure-functions-http-trigger-with-an-azure-cosmos-db-input-binding"></a>Criar um acionador de HTTP de funções do Azure com um enlace de entrada de base de dados do Azure Cosmos

BD do Cosmos do Azure é uma globalmente distribuído, com vários modelo base de dados sem esquemas e sem servidor. A função do Azure é um serviço de computação sem servidor que permite a execução de código a pedido. Emparelhe a cópia de segurança destes dois serviços do Azure e ter a base para uma arquitetura sem servidor que permite-lhe focar-se na criação de aplicações de grande e não se preocupe sobre o aprovisionamento e a manutenção dos servidores da sua computação e tem de base de dados.

Este tutorial baseia-se o código que criou no [início rápido da API do gráfico para .NET](create-graph-dotnet.md). Este tutorial adiciona uma função do Azure que contém um [acionador HTTP](https://github.com/MicrosoftDocs/azure-docs-pr/azure-functions/functions-bindings-http-webhook.md#http-trigger). O acionador HTTP utiliza uma base de dados do Azure Cosmos [enlace de entrada](https://github.com/MicrosoftDocs/azure-docs-pr/azure-functions/functions-triggers-bindings.md) para obter dados da base de dados do gráfico criado no início rápido. Esta consulta de Acionador HTTP de determinado BD do Cosmos do Azure para dados, mas a entrada de enlaces da base de dados do Azure Cosmos pode ser utilizado para obter valores de entrada de dados para que a função requer.

Este tutorial abrange as seguintes tarefas:

> [!div class="checklist"]
> * Criar um projeto de função do Azure 
> * Criar um acionador HTTP
> * Publicar a função do Azure
> * Ligar a função do Azure para a base de dados de base de dados do Azure Cosmos

## <a name="prerequisites"></a>Pré-requisitos

- [Visual Studio 2017 versão 15.3](https://www.visualstudio.com/vs/preview/), incluindo a carga de trabalho de **desenvolvimento do Azure**.

    ![Instalar o Visual Studio 2017 com a carga de trabalho de desenvolvimento do Azure](./media/tutorial-functions-http-trigger/functions-vs-workloads.png)
    
- Depois de instalar ou atualizar para o Visual Studio 2017 versão 15.3, tem de atualizar manualmente as ferramentas do Visual Studio 2017 para as funções do Azure. Pode atualizar as ferramentas do **ferramentas** menu em **extensões e atualizações...**   >  **Atualizações** > **Marketplace do Visual Studio** > **as funções do Azure e Web tarefas ferramentas**  >  **Atualização**.

- Concluir o [compilar uma aplicação .NET através da API de gráfico](tutorial-develop-graph-dotnet.md) tutorial ou get o exemplo de código do [azure-cosmos-db-graph-dotnet-getting-started](https://github.com/Azure-Samples/azure-cosmos-db-graph-dotnet-getting-started) repositório do GitHub e compilar o projeto.
 
## <a name="build-a-function-in-visual-studio"></a>Criar uma função no Visual Studio

1. Adicionar um **das funções do Azure** projeto para a sua solução ao clicar no nó de solução na **Explorador de soluções**, a escolha **adicionar**  >   **Novo projeto**. Escolha **das funções do Azure** da caixa de diálogo caixa e dê-lhe nome **PeopleDataFunctions**.

   ![Adicionar um projeto de função do Azure para a solução](./media/tutorial-functions-http-trigger/01-add-function-project.png)

2. Depois de criar o projeto de funções do Azure, existem alguns NuGet relacionadas com atualizações e é instalado para executar. 

    a. Para se certificar de que tem o SDK de funções mais recente, utilize o Gestor de NuGet para atualizar o **Microsoft.NET.Sdk.Functions** pacote. No **Explorador de soluções**, clique com o botão direito no projeto e selecione **gerir pacotes NuGet**. No **instalada** separador, selecione Microsoft.NET.Sdk.Functions, em seguida, clique em **atualização**.

   ![Atualizar pacotes NuGet](./media/tutorial-functions-http-trigger/02-update-functions-sdk.png)

    b. No **procurar** separador, introduza **azure.graphs** para localizar o **Microsoft.Azure.Graphs** do pacote e, em seguida, clique em **instalar**. Este pacote contém o SDK de cliente .NET do Graph API.

   ![Instalar o gráfico de API](./media/tutorial-functions-http-trigger/03-add-azure-graphs.png)

    c. No **procurar** separador, introduza **mono.csharp** para localizar o **Mono.CSharp** do pacote e, em seguida, clique em **instalar**.

   ![Instalar Mono.CSharp](./media/tutorial-functions-http-trigger/04-add-mono.png)

3. O Explorador de soluções agora deve incluir os pacotes instalados, conforme mostrado aqui. 
   
   Em seguida, temos de escrever alguns códigos, pelo que iremos adicionar um novo **função do Azure** item ao projeto. 

    a. Clique com o botão direito do rato no nó do projeto em **Explorador de Soluções** e escolha **Adicionar** > **Novo Item**.   
    b. No **Adicionar Novo Item** caixa de diálogo, selecione **Visual c# itens**, selecione **função do Azure**, tipo **pesquisa** como o nome do seu projeto e, em seguida, Clique em **adicionar**.  
 
   ![Criar uma nova função com o nome da pesquisa](./media/tutorial-functions-http-trigger/05-add-function.png)

4. A função do Azure irá responder a pedidos de HTTP pelo que o modelo de Acionador de Http é adequado aqui.
   
   No **nova função de Azure** caixa, selecione **acionador de Http**. Queremos esta função do Azure para ser "ao nível aberto,", pelo que iremos definir o **direitos de acesso** para **anónimo**, todas as pessoas que permite. Clique em **OK**.

   ![Conjunto de direitos de acesso anónimo](./media/tutorial-functions-http-trigger/06-http-trigger.png)

5. Depois de adicionar Search.cs para o projeto de função do Azure, copie estas **utilizando** instruções através de instruções de utilização existente:

   ```csharp
   using Microsoft.Azure.Documents;
   using Microsoft.Azure.Documents.Client;
   using Microsoft.Azure.Documents.Linq;
   using Microsoft.Azure.Graphs;
   using Microsoft.Azure.WebJobs;
   using Microsoft.Azure.WebJobs.Extensions.Http;
   using Microsoft.Azure.WebJobs.Host;
   using System;
   using System.Collections.Generic;
   using System.Configuration;
   using System.Linq;
   using System.Net;
   using System.Net.Http;
   using System.Threading.Tasks;
   ```

6. Em seguida, substitua o código de classe da função do Azure com o código abaixo. O código de procura na base de dados de base de dados do Azure Cosmos a Graph API a utilizar para a todas as pessoas ou para a pessoa específica identificado pelo `name` parâmetro de cadeia de consulta.

   ```csharp
   public static class Search
   {
       static string endpoint = ConfigurationManager.AppSettings["Endpoint"];
       static string authKey = ConfigurationManager.AppSettings["AuthKey"];

       [FunctionName("Search")]
       public static async Task<HttpResponseMessage> Run(
           [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)]HttpRequestMessage req,
           TraceWriter log)
       {
           log.Info("C# HTTP trigger function processed a request.");

           // the person objects are free-form in structure
           List<dynamic> results = new List<dynamic>();

           // open the client's connection
           using (DocumentClient client = new DocumentClient(
               new Uri(endpoint),
               authKey,
               new ConnectionPolicy
               {
                   ConnectionMode = ConnectionMode.Direct,
                   ConnectionProtocol = Protocol.Tcp
               }))
           {
               // get a reference to the database the console app created
               Database database = await client.CreateDatabaseIfNotExistsAsync(
                   new Database
                   {
                       Id = "graphdb"
                   });

               // get an instance of the database's graph
               DocumentCollection graph = await client.CreateDocumentCollectionIfNotExistsAsync(
                   UriFactory.CreateDatabaseUri("graphdb"),
                   new DocumentCollection { Id = "graphcollz" },
                   new RequestOptions { OfferThroughput = 1000 });

               // build a gremlin query based on the existence of a name parameter
               string name = req.GetQueryNameValuePairs()
                   .FirstOrDefault(q => string.Compare(q.Key, "name", true) == 0)
                   .Value;

               IDocumentQuery<dynamic> query = (!String.IsNullOrEmpty(name))
                   ? client.CreateGremlinQuery<dynamic>(graph, string.Format("g.V('{0}')", name))
                   : client.CreateGremlinQuery<dynamic>(graph, "g.V()");

               // iterate over all the results and add them to the list
               while (query.HasMoreResults)
                   foreach (dynamic result in await query.ExecuteNextAsync())
                       results.Add(result);
           }

           // return the list with an OK response
           return req.CreateResponse<List<dynamic>>(HttpStatusCode.OK, results);
       }
   }
   ```

   O código é basicamente a mesma lógica de ligação como a aplicação de consola original pré-propagadas a base de dados, com uma consulta simple para obter os registos correspondentes.

## <a name="debug-the-azure-function-locally"></a>Depurar a função do Azure localmente

Agora que o código for concluído, pode utilizar as ferramentas de depuração locais da função do Azure e emulador para executar código localmente para testá-lo.

1. Antes do código é executado corretamente, tem de configurá-la para execução local com as informações de ligação de base de dados do Azure Cosmos. Pode utilizar o ficheiro de local.settings.json para configurar a função do Azure para a execução local muito da mesma forma que pretende utilizar o ficheiro App. config para configurar a aplicação de consola original para execução.

    Para tal, adicione as seguintes linhas de código para local.settings.json e, em seguida, copie no ponto final e AuthKey do ficheiro App. config no projeto GraphGetStarted conforme mostrado na imagem seguinte.

   ```json
    "Endpoint": "",
    "AuthKey": ""
    ```

   ![Defina a chave de autorização e de ponto final no ficheiro local.settings.json](./media/tutorial-functions-http-trigger/07-local-functions-settings.png)

2. Altere o projeto de arranque para a nova aplicação de funções. No **Explorador de soluções**, faça duplo clique **PeopleDataFunctions**e selecione **definir como projeto de arranque**.

3. No **Explorador de soluções**, clique com o botão direito **dependências** no **PeopleDataFunctions** projeto e, em seguida, clique em **Adicionar referência**. Na lista, selecione System e, em seguida, clique em **OK**.

3. Agora vamos executar a aplicação. Prima F5 para iniciar a ferramenta de depuração local func.exe, com o código de função do Azure alojado e pronto a utilizar.

   No final do resultado inicial do func.exe, Vemos que a função do Azure está a ser alojada no localhost:7071. Esta ação é útil para testá-lo num cliente.

   ![Testar o cliente](./media/tutorial-functions-http-trigger/08-functions-emulator.png)

4. Para testar a função do Azure, utilize [Visual Studio Code](http://code.visualstudio.com/) com a extensão de Huachao Mao, [cliente REST](https://marketplace.visualstudio.com/items?itemName=humao.rest-client). Cliente de REST oferece local ou remota a capacidade do pedido HTTP num rato único. 

    Para tal, crie um novo ficheiro designado locally.http de função de teste e adicione o seguinte código:

    ```http
    get http://localhost:7071/api/Search

    get http://localhost:7071/api/Search?name=ben
   ```

    Agora a primeira linha de código com o botão direito, em seguida selecione **enviar pedido** conforme mostrado na imagem seguinte.

   ![Enviar um pedido REST do Visual Studio code](./media/tutorial-functions-http-trigger/09-rest-client-in-vs-code.png)

   É-lhe apresentada a resposta HTTP não processada de cabeçalhos de função do Azure localmente em execução, o conteúdo do corpo JSON, tudo.

   ![Resposta REST](./media/tutorial-functions-http-trigger/10-general-results.png)

5. Agora, selecione a segunda linha de código e, em seguida, selecione **enviar pedido**. Ao adicionar o `name` parâmetro de cadeia de consulta com um valor conhecido estar na base de dados, pode filtrar os resultados devolve a função do Azure.

   ![Filtrar os resultados da função do Azure](./media/tutorial-functions-http-trigger/11-search-for-ben.png)

Depois da função do Azure é validada e parecer estar a funcionar corretamente, o último passo consiste em publicá-lo no App Service do Azure e configurá-lo para ser executada na nuvem.

## <a name="publish-the-azure-function"></a>Publicar a função do Azure

1. No **Explorador de soluções**, clique com o botão direito no projeto, em seguida, selecione **publicar**.

   ![Publicar o novo projeto](./media/tutorial-functions-http-trigger/12-publish-function.png)

2. Está tudo prontos publicar esta para a nuvem para testá-lo num cenário publicamente disponível. No **publicar** separador, selecione **aplicação de função do Azure**, selecione **criar novo** para criar uma função do Azure na sua subscrição do Azure, em seguida, clique em **publicar** .

   ![Criar uma nova aplicação de função do Azure](./media/tutorial-functions-http-trigger/13-publish-panel.png)

3. No **publicar** caixa de diálogo, efetue o seguinte procedimento:
   
    a. No **nome da aplicação**, atribua a função de um nome exclusivo.

    b. No **subscrição**, selecione a subscrição do Azure a utilizar.
   
    c. No **grupo de recursos**, crie um novo grupo de recursos e utilizar o mesmo nome como o nome da aplicação.
   
    d. Para **plano do App Service**, clique em **novo** para criar uma nova com base no consumo plano do App Service porque pretendemos utilizar o método de faturação de pagamento por utilização para a função do Azure sem servidor. Utilize as predefinições no **configurar plano do App Service** página e, em seguida, clique em **OK**.
   
    e. Para **conta de armazenamento**, também clicar em **novo** para criar uma nova conta de armazenamento para utilizar com a função do Azure caso estamos alguma vez precisar de suporte para Blobs, tabelas ou filas acionar a execução de outras funcionalidades. Utilize as predefinições no **conta de armazenamento** página e, em seguida, clique em **OK**.

    f. Em seguida, clique o **criar** botão na caixa de diálogo Criar todos os recursos na sua subscrição do Azure. Visual Studio transfere um perfil de publicação (um ficheiro XML simple) que utiliza na próxima vez que publicar o seu código de função do Azure.

   ![Criar a conta de armazenamento](./media/tutorial-functions-http-trigger/14-new-function-app.png)

    Visual Studio, em seguida, apresenta uma página de publicação que pode utilizar se efetuar alterações para a função e tem de publicá-la novamente. Não tem de efetuar qualquer ação nessa página agora.

4. Depois da função do Azure é publicada, pode ir para o [portal do Azure](https://portal.azure.com/) página para a função do Azure. Aqui, pode ver uma ligação para a função de Azure **definições da aplicação**. Abra esta ligação para configurar a função do Azure em direto para conetividade à base de dados do Azure Cosmos DB com os dados da pessoa.

   ![Reveja as definições da aplicação](./media/tutorial-functions-http-trigger/15-function-in-portal.png)

5. Tal como fez anteriormente no ficheiro de App. config da aplicação de consola e no ficheiro de local.settings.json a aplicação de função do Azure, terá de adicionar o ponto final e AuthKey na base de dados de base de dados do Azure Cosmos para a função publicada. Desta forma, nunca terá der entrada do código de configuração que contenha as chaves - pode configurá-las no portal e certifique-se de que não está a armazenadas no controlo de origem. Para adicionar cada valor, clique o **Adicionar nova definição** botão, adicione **Endpoint** e o valor a partir da App. config, em seguida, clique em **Adicionar nova definição** novamente e adicione **AuthKey**  e o valor personalizado. Depois de ter adicionado e guardar os valores, as definições devem o seguinte aspeto.

   ![Configurar o ponto final e AuthKey](./media/tutorial-functions-http-trigger/16-app-settings.png)

6. Depois da função do Azure está configurada corretamente na sua subscrição do Azure, pode utilizar novamente a extensão de cliente de REST de código do Visual Studio para consultar o URL de função publicamente disponíveis do Azure. Adicione estas duas linhas de código para o locally.http de função de teste e, em seguida, execute cada linha para testar esta função. Substitua o nome da função no URL com o nome da sua função.

    ```json
    get https://peoplesearchfunction.azurewebsites.net/api/Search

    get https://peoplesearchfunction.azurewebsites.net/api/Search?name=thomas
    ```

    A função responde com os dados obtidos na base de dados do Azure Cosmos.

    ![Utilizar o cliente REST para consultar a função do Azure](./media/tutorial-functions-http-trigger/17-calling-function-from-code.png)


## <a name="next-steps"></a>Passos seguintes

Neste tutorial, fez o seguinte:

> [!div class="checklist"]
> * Criar um projeto de função do Azure 
> * Criar um acionador HTTP
> * Publicar a função do Azure
> * Ligado a função para a base de dados de base de dados do Azure Cosmos

Agora pode avançar para a secção de conceitos para obter mais informações sobre a base de dados do Cosmos.

> [!div class="nextstepaction"]
> [Distribuição global](distribute-data-globally.md) 

Este artigo baseia-se num blogue do [do Brady Gaster Schemaless & Serverless](http://www.bradygaster.com/category/%20Serverless%20&%20Schemaless) série de blogues. Visite o blogue para publicações adicionais da série.

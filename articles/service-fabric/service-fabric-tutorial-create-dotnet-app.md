---
title: "Criar uma aplicação .NET para o Service Fabric | Microsoft Docs"
description: "Saiba como criar uma aplicação com um front-end do ASP.NET Core e um serviço fiável com monitorização de estado back-end da e implementar a aplicação para um cluster."
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/08/2017
ms.author: ryanwi
ms.custom: mvc
ms.openlocfilehash: 341d275fbf9f80ac9e3363757d880b9546bdee13
ms.sourcegitcommit: adf6a4c89364394931c1d29e4057a50799c90fc0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/09/2017
---
# <a name="create-and-deploy-an-application-with-an-aspnet-core-web-api-front-end-service-and-a-stateful-back-end-service"></a>Criar e implementar uma aplicação com um serviço de front-end de API Web do ASP.NET Core e o serviço de back-end com monitorização de estado
Este tutorial faz parte de um de uma série.  Ficará a saber como criar uma aplicação de Service Fabric do Azure com um front-end de API Web do ASP.NET Core e o serviço de back-end com monitorização de estado para armazenar os dados. Quando tiver terminado, terá uma aplicação de voto com um web de ASP.NET Core front-end que guarda os resultados de votos no serviço de back-end com monitorização de estado no cluster. Se não pretender criar manualmente a aplicação de voto, pode [transferir o código de origem](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/) para a aplicação concluída e avançar diretamente para [guiá-a aplicação de exemplo voto](#walkthrough_anchor).

![Diagrama de aplicação](./media/service-fabric-tutorial-create-dotnet-app/application-diagram.png)

Na parte de uma série, saiba como:

> [!div class="checklist"]
> * Criar um serviço de API Web do ASP.NET Core como um serviço fiável com monitorização de estado
> * Criar um serviço de aplicação Web do ASP.NET Core como um serviço web sem monitorização de estado
> * Utilizar o proxy inverso para comunicar com o serviço com estado

Este tutorial série, a saber como:
> [!div class="checklist"]
> * Criar uma aplicação .NET Service Fabric
> * [Implementar a aplicação para um cluster remoto](service-fabric-tutorial-deploy-app-to-party-cluster.md)
> * [Configurar CI/CD utilizando o Visual Studio Team Services](service-fabric-tutorial-deploy-app-with-cicd-vsts.md)
> * [Configurar a monitorização e diagnóstico para a aplicação](service-fabric-tutorial-monitoring-aspnet.md)

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar este tutorial:
- Se não tiver uma subscrição do Azure, crie um [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- [Instalar Visual Studio 2017](https://www.visualstudio.com/) e instalar o **programação do Azure** e **desenvolvimento ASP.NET e web** cargas de trabalho.
- [Instalar o SDK do Service Fabric](service-fabric-get-started.md)

## <a name="create-an-aspnet-web-api-service-as-a-reliable-service"></a>Criar um serviço de API Web do ASP.NET como um serviço fiável
Em primeiro lugar, crie web front-end da aplicação de voto com o ASP.NET Core. ASP.NET Core é uma estrutura de desenvolvimento simples, de plataforma web que pode utilizar para criar a IU da web do moderna e web APIs. Para obter uma compreensão concluída de forma ASP.NET Core é se integra com o Service Fabric, recomendamos vivamente que ler de [ASP.NET Core no Service Fabric Reliable Services](service-fabric-reliable-services-communication-aspnetcore.md) artigo. Por agora, pode seguir este tutorial para começar a trabalhar rapidamente. Para saber mais sobre o ASP.NET Core, consulte o [ASP.NET Core documentação](https://docs.microsoft.com/aspnet/core/).

> [!NOTE]
> Este tutorial baseia-se no [ASP.NET Core tools para Visual Studio 2017](https://docs.microsoft.com/aspnet/core/tutorials/first-mvc-app/start-mvc). As ferramentas do .NET Core para o Visual Studio 2015 já não estão a ser atualizadas.

1. Inicie o Visual Studio como **administrador**.

2. Criar um projeto com **ficheiro**->**novo**->**projeto**

3. Na caixa de diálogo **Novo Projeto**, escolha **Cloud > Aplicação do Service Fabric**.

4. Atribua um nome de aplicação **voto** e prima **OK**.

   ![Caixa de diálogo de novo projeto no Visual Studio](./media/service-fabric-tutorial-create-dotnet-app/new-project-dialog.png)

5. No **novo serviço de recursos de infraestrutura de serviço** página, escolha **sem monitorização de estado ASP.NET Core**e o nome do seu serviço **VotingWeb**.
   
   ![Escolher o serviço web ASP.NET na caixa de diálogo novo serviço](./media/service-fabric-tutorial-create-dotnet-app/new-project-dialog-2.png) 

6. A página seguinte fornece um conjunto de ASP.NET Core modelos de projeto. Para este tutorial, escolha **aplicação Web**. 
   
   ![Escolha o tipo de projeto ASP.NET](./media/service-fabric-tutorial-create-dotnet-app/vs-new-aspnet-project-dialog.png)

   Visual Studio cria uma aplicação e um projeto de serviço e apresenta-os no Explorador de soluções.

   ![Explorador de soluções a seguir a criação da aplicação com o serviço de Web API do ASP.NET core]( ./media/service-fabric-tutorial-create-dotnet-app/solution-explorer-aspnetcore-service.png)

### <a name="add-angularjs-to-the-votingweb-service"></a>Adicionar AngularJS ao serviço VotingWeb
Adicionar [AngularJS](http://angularjs.org/) ao seu serviço utilizando o incorporado [Bower suporte](/aspnet/core/client-side/bower). Abra *bower.json* e adicionar entradas para angular para e de arranque de angular para, em seguida, guarde as alterações.

```json
{
  "name": "asp.net",
  "private": true,
  "dependencies": {
    "bootstrap": "3.3.7",
    "jquery": "2.2.0",
    "jquery-validation": "1.14.0",
    "jquery-validation-unobtrusive": "3.2.6",
    "angular": "v1.6.5",
    "angular-bootstrap": "v1.1.0"
  }
}
```
Após guardar o *bower.json* ficheiro, Angular está instalado no seu projeto *wwwroot/lib* pasta. Além disso, este está listado no *dependências/Bower* pasta.

### <a name="update-the-sitejs-file"></a>Atualizar o ficheiro site.js
Abra o *wwwroot/js/site.js* ficheiro.  Substitua o respetivo conteúdo com o JavaScript utilizado pelas vistas de Home:

```javascript
var app = angular.module('VotingApp', ['ui.bootstrap']);
app.run(function () { });

app.controller('VotingAppController', ['$rootScope', '$scope', '$http', '$timeout', function ($rootScope, $scope, $http, $timeout) {

    $scope.refresh = function () {
        $http.get('api/Votes?c=' + new Date().getTime())
            .then(function (data, status) {
                $scope.votes = data;
            }, function (data, status) {
                $scope.votes = undefined;
            });
    };

    $scope.remove = function (item) {
        $http.delete('api/Votes/' + item)
            .then(function (data, status) {
                $scope.refresh();
            })
    };

    $scope.add = function (item) {
        var fd = new FormData();
        fd.append('item', item);
        $http.put('api/Votes/' + item, fd, {
            transformRequest: angular.identity,
            headers: { 'Content-Type': undefined }
        })
            .then(function (data, status) {
                $scope.refresh();
                $scope.item = undefined;
            })
    };
}]);
```

### <a name="update-the-indexcshtml-file"></a>Atualizar o ficheiro cshtml
Abra o *Views/Home/Index.cshtml* ficheiro, a vista específica para o controlador de casa.  Substitua o conteúdo com o seguinte, em seguida, guarde as alterações.

```html
@{
    ViewData["Title"] = "Service Fabric Voting Sample";
}

<div ng-controller="VotingAppController" ng-init="refresh()">
    <div class="container-fluid">
        <div class="row">
            <div class="col-xs-8 col-xs-offset-2 text-center">
                <h2>Service Fabric Voting Sample</h2>
            </div>
        </div>

        <div class="row">
            <div class="col-xs-8 col-xs-offset-2">
                <form class="col-xs-12 center-block">
                    <div class="col-xs-6 form-group">
                        <input id="txtAdd" type="text" class="form-control" placeholder="Add voting option" ng-model="item" />
                    </div>
                    <button id="btnAdd" class="btn btn-default" ng-click="add(item)">
                        <span class="glyphicon glyphicon-plus" aria-hidden="true"></span>
                        Add
                    </button>
                </form>
            </div>
        </div>

        <hr />

        <div class="row">
            <div class="col-xs-8 col-xs-offset-2">
                <div class="row">
                    <div class="col-xs-4">
                        Click to vote
                    </div>
                </div>
                <div class="row top-buffer" ng-repeat="vote in votes.data">
                    <div class="col-xs-8">
                        <button class="btn btn-success text-left btn-block" ng-click="add(vote.key)">
                            <span class="pull-left">
                                {{vote.key}}
                            </span>
                            <span class="badge pull-right">
                                {{vote.value}} Votes
                            </span>
                        </button>
                    </div>
                    <div class="col-xs-4">
                        <button class="btn btn-danger pull-right btn-block" ng-click="remove(vote.key)">
                            <span class="glyphicon glyphicon-remove" aria-hidden="true"></span>
                            Remove
                        </button>
                    </div>
                </div>
            </div>
        </div>
    </div>
</div>
```

### <a name="update-the-layoutcshtml-file"></a>Atualizar o ficheiro Layout
Abra o *Views/Shared/_Layout.cshtml* ficheiro, o esquema predefinido para a aplicação ASP.NET.  Substitua o conteúdo com o seguinte, em seguida, guarde as alterações.

```html
<!DOCTYPE html>
<html ng-app="VotingApp" xmlns:ng="http://angularjs.org">
<head>
    <meta charset="utf-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>@ViewData["Title"]</title>

    <link href="~/lib/bootstrap/dist/css/bootstrap.min.css" rel="stylesheet" />
    <link href="~/css/site.css" rel="stylesheet" />

</head>
<body>
    <div class="container body-content">
        @RenderBody()
    </div>

    <script src="~/lib/jquery/dist/jquery.js"></script>
    <script src="~/lib/bootstrap/dist/js/bootstrap.js"></script>
    <script src="~/lib/angular/angular.js"></script>
    <script src="~/lib/angular-bootstrap/ui-bootstrap-tpls.js"></script>
    <script src="~/js/site.js"></script>

    @RenderSection("Scripts", required: false)
</body>
</html>
```

### <a name="update-the-votingwebcs-file"></a>Atualizar o ficheiro VotingWeb.cs
Abra o *VotingWeb.cs* ficheiro, que cria o WebHost de núcleo de ASP.NET dentro do serviço sem monitorização de estado com o servidor de web WebListener.  

Adicionar o `using System.Net.Http;` directiva na parte superior do ficheiro.  

Substitua o `CreateServiceInstanceListeners()` funcionar com o seguinte, em seguida, guarde as alterações.

```csharp
protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
{
    return new ServiceInstanceListener[]
    {
        new ServiceInstanceListener(serviceContext =>
            new WebListenerCommunicationListener(serviceContext, "ServiceEndpoint", (url, listener) =>
            {
                ServiceEventSource.Current.ServiceMessage(serviceContext, $"Starting WebListener on {url}");

                return new WebHostBuilder().UseWebListener()
                            .ConfigureServices(
                                services => services
                                    .AddSingleton<StatelessServiceContext>(serviceContext)
                                    .AddSingleton<HttpClient>())
                            .UseContentRoot(Directory.GetCurrentDirectory())
                            .UseStartup<Startup>()
                            .UseApplicationInsights()
                            .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.None)
                            .UseUrls(url)
                            .Build();
            }))
    };
}
```

### <a name="add-the-votescontrollercs-file"></a>Adicione o ficheiro VotesController.cs
Adicione um controlador que define as ações de votos. Clique com o botão direito no **controladores** pasta, em seguida, selecione **adicionar -> novo item -> classe**.  Atribua o nome "VotesController.cs" e clique em **adicionar**.  

Substitua o conteúdo do ficheiro com o seguinte, em seguida, guarde as alterações.  Mais adiante, [atualizar o ficheiro VotesController.cs](#updatevotecontroller_anchor), este ficheiro será modificado para ler e escrever dados de votos do serviço de back-end.  Por agora, o controlador devolve os dados de cadeia estática para a vista.

```csharp
using System;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using System.Collections.Generic;
using Newtonsoft.Json;
using System.Text;
using System.Net.Http;
using System.Net.Http.Headers;

namespace VotingWeb.Controllers
{
    [Produces("application/json")]
    [Route("api/Votes")]
    public class VotesController : Controller
    {
        private readonly HttpClient httpClient;

        public VotesController(HttpClient httpClient)
        {
            this.httpClient = httpClient;
        }

        // GET: api/Votes
        [HttpGet]
        public async Task<IActionResult> Get()
        {
            List<KeyValuePair<string, int>> votes= new List<KeyValuePair<string, int>>();
            votes.Add(new KeyValuePair<string, int>("Pizza", 3));
            votes.Add(new KeyValuePair<string, int>("Ice cream", 4));

            return Json(votes);
        }
     }
}
```

### <a name="configure-the-listening-port"></a>Configurar a porta de escuta
Quando o serviço de front-end VotingWeb é criado, o Visual Studio seleciona aleatoriamente uma porta para o serviço de escuta no.  O serviço de VotingWeb atua como o front-end para esta aplicação e aceita o tráfego externo, por isso, vamos vincular que o serviço para fixa e conhecer bem porta. No Explorador de soluções, abra *VotingWeb/PackageRoot/ServiceManifest.xml*.  Localizar o **Endpoint** recurso no **recursos** secção e altere o **porta** valor para 80, ou para outra porta. Para implementar e executar a aplicação localmente, a porta de escuta de aplicação tem de ser aberta e disponível no seu computador.

```xml
<Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port on which to 
           listen. Please note that if your service is partitioned, this port is shared with 
           replicas of different partitions that are placed in your code. -->
      <Endpoint Protocol="http" Name="ServiceEndpoint" Type="Input" Port="80" />
    </Endpoints>
  </Resources>
```

Também de atualizar o valor da propriedade URL da aplicação no projeto de voto, para que um web browser abre-se para a porta correta quando depurar a utilizar 'F5'.  No Explorador de soluções, selecione o **voto** projeto e atualize o **URL da aplicação** propriedade.

![URL da aplicação](./media/service-fabric-tutorial-deploy-app-to-party-cluster/application-url.png)

### <a name="deploy-and-run-the-application-locally"></a>Implementar e executar a aplicação localmente
Agora, pode avançar e executar a aplicação. No Visual Studio, prima `F5` para implementar a aplicação, com o fim de depurá-la. `F5`falha se anteriormente não abra o Visual Studio como **administrador**.

> [!NOTE]
> Da primeira vez que executar e implementar a aplicação localmente, o Visual Studio cria um cluster local para depuração.  Criação do cluster pode demorar algum tempo. O estado da criação do cluster aparece na janela de saída do Visual Studio.

Neste momento, a aplicação web deve ter o seguinte aspeto:

![ASP.NET Core front-end](./media/service-fabric-tutorial-create-dotnet-app/debug-front-end.png)

Para parar a depuração da aplicação, volte atrás para Visual Studio e prima **Shift + F5**.

## <a name="add-a-stateful-back-end-service-to-your-application"></a>Adicionar um serviço de back-end com monitorização de estado à sua aplicação
Agora que temos um serviço de API Web do ASP.NET em execução na nossa aplicação, vamos avançar e adicionar um serviço fiável com monitorização de estado para armazenar alguns dados na nossa aplicação.

Recursos de infraestrutura de serviço permite-lhe consistentemente e fiável armazenar os seus diretamente dados dentro do seu serviço através de coleções fiáveis. Coleções fiáveis são um conjunto de classes de elevada disponibilidade e fiável coleção que estão familiarizados qualquer pessoa que tenha utilizado c# coleções.

Neste tutorial, vai criar um serviço que armazena um valor de contador numa coleção fiável.

1. No Explorador de soluções, faça duplo clique **serviços** na aplicação do projeto e escolha **adicionar > novo serviço de recursos de infraestrutura de serviço**.
   
    ![Adicionar um novo serviço para uma aplicação existente](./media/service-fabric-tutorial-create-dotnet-app/vs-add-new-service.png)

2. No **novo serviço de recursos de infraestrutura de serviço** caixa de diálogo, escolha **com monitorização de estado ASP.NET Core**e o serviço de nomes **VotingData** e prima **OK**.

    ![Caixa de diálogo novo serviço no Visual Studio](./media/service-fabric-tutorial-create-dotnet-app/add-stateful-service.png)

    Depois do projeto de serviço é criado, tem dois serviços na sua aplicação. À medida que continue a criar a sua aplicação, pode adicionar mais serviços da mesma forma. Cada pode ser independente com versão e atualizado.

3. A página seguinte fornece um conjunto de ASP.NET Core modelos de projeto. Para este tutorial, escolha **Web API**.

    ![Escolha o tipo de projeto ASP.NET](./media/service-fabric-tutorial-create-dotnet-app/vs-new-aspnet-project-dialog2.png)

    Visual Studio cria um projeto de serviço e apresenta-os no Explorador de soluções.

    ![Explorador de Soluções](./media/service-fabric-tutorial-create-dotnet-app/solution-explorer-aspnetcore-webapi-service.png)

### <a name="add-the-votedatacontrollercs-file"></a>Adicione o ficheiro VoteDataController.cs

No **VotingData** contexto do projeto no **controladores** pasta, em seguida, selecione **adicionar -> novo item -> classe**. Atribua o nome "VoteDataController.cs" e clique em **adicionar**. Substitua o conteúdo do ficheiro com o seguinte, em seguida, guarde as alterações.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.ServiceFabric.Data;
using System.Threading;
using Microsoft.ServiceFabric.Data.Collections;

namespace VotingData.Controllers
{
    [Route("api/[controller]")]
    public class VoteDataController : Controller
    {
        private readonly IReliableStateManager stateManager;

        public VoteDataController(IReliableStateManager stateManager)
        {
            this.stateManager = stateManager;
        }

        // GET api/VoteData
        [HttpGet]
        public async Task<IActionResult> Get()
        {
            var ct = new CancellationToken();

            var votesDictionary = await this.stateManager.GetOrAddAsync<IReliableDictionary<string, int>>("counts");

            using (ITransaction tx = this.stateManager.CreateTransaction())
            {
                var list = await votesDictionary.CreateEnumerableAsync(tx);

                var enumerator = list.GetAsyncEnumerator();

                var result = new List<KeyValuePair<string, int>>();

                while (await enumerator.MoveNextAsync(ct))
                {
                    result.Add(enumerator.Current);
                }

                return Json(result);
            }
        }

        // PUT api/VoteData/name
        [HttpPut("{name}")]
        public async Task<IActionResult> Put(string name)
        {
            var votesDictionary = await this.stateManager.GetOrAddAsync<IReliableDictionary<string, int>>("counts");

            using (ITransaction tx = this.stateManager.CreateTransaction())
            {
                await votesDictionary.AddOrUpdateAsync(tx, name, 1, (key, oldvalue) => oldvalue + 1);
                await tx.CommitAsync();
            }

            return new OkResult();
        }

        // DELETE api/VoteData/name
        [HttpDelete("{name}")]
        public async Task<IActionResult> Delete(string name)
        {
            var votesDictionary = await this.stateManager.GetOrAddAsync<IReliableDictionary<string, int>>("counts");

            using (ITransaction tx = this.stateManager.CreateTransaction())
            {
                if (await votesDictionary.ContainsKeyAsync(tx, name))
                {
                    await votesDictionary.TryRemoveAsync(tx, name);
                    await tx.CommitAsync();
                    return new OkResult();
                }
                else
                {
                    return new NotFoundResult();
                }
            }
        }
    }
}
```


## <a name="connect-the-services"></a>Ligar os serviços
Neste passo seguinte, iremos ligar os dois serviços e tornar a Web front-end de aplicação get e set votar informações do serviço de back-end.

O Service Fabric fornece flexibilidade concluída na como comunicar com serviços fiáveis. Dentro de uma única aplicação, poderá ter serviços que estão acessíveis através de TCP. Outros serviços que podem estar acessíveis através de uma API de REST de HTTP e ainda outros serviços poderia ficar acessíveis através de web sockets. Para fundo sobre as opções disponíveis e fala envolvidas, consulte [comunicar com serviços](service-fabric-connect-and-communicate-with-services.md).

Neste tutorial, estamos a utilizar [Web API do ASP.NET Core](service-fabric-reliable-services-communication-aspnetcore.md).

<a id="updatevotecontroller" name="updatevotecontroller_anchor"></a>

### <a name="update-the-votescontrollercs-file"></a>Atualizar o ficheiro VotesController.cs
No **VotingWeb** projeto, abra o *Controllers/VotesController.cs* ficheiro.  Substitua o `VotesController` classe conteúdo de definição com o seguinte, em seguida, guarde as alterações.

```csharp
    public class VotesController : Controller
    {
        private readonly HttpClient httpClient;
        string serviceProxyUrl = "http://localhost:19081/Voting/VotingData/api/VoteData";
        string partitionKind = "Int64Range";
        string partitionKey = "0";

        public VotesController(HttpClient httpClient)
        {
            this.httpClient = httpClient;
        }

        // GET: api/Votes
        [HttpGet]
        public async Task<IActionResult> Get()
        {
            IEnumerable<KeyValuePair<string, int>> votes;

            HttpResponseMessage response = await this.httpClient.GetAsync($"{serviceProxyUrl}?PartitionKind={partitionKind}&PartitionKey={partitionKey}");

            if (response.StatusCode != System.Net.HttpStatusCode.OK)
            {
                return this.StatusCode((int)response.StatusCode);
            }

            votes = JsonConvert.DeserializeObject<List<KeyValuePair<string, int>>>(await response.Content.ReadAsStringAsync());

            return Json(votes);
        }

        // PUT: api/Votes/name
        [HttpPut("{name}")]
        public async Task<IActionResult> Put(string name)
        {
            string payload = $"{{ 'name' : '{name}' }}";
            StringContent putContent = new StringContent(payload, Encoding.UTF8, "application/json");
            putContent.Headers.ContentType = new MediaTypeHeaderValue("application/json");

            string proxyUrl = $"{serviceProxyUrl}/{name}?PartitionKind={partitionKind}&PartitionKey={partitionKey}";

            HttpResponseMessage response = await this.httpClient.PutAsync(proxyUrl, putContent);

            return new ContentResult()
            {
                StatusCode = (int)response.StatusCode,
                Content = await response.Content.ReadAsStringAsync()
            };
        }

        // DELETE: api/Votes/name
        [HttpDelete("{name}")]
        public async Task<IActionResult> Delete(string name)
        {
            HttpResponseMessage response = await this.httpClient.DeleteAsync($"{serviceProxyUrl}/{name}?PartitionKind={partitionKind}&PartitionKey={partitionKey}");

            if (response.StatusCode != System.Net.HttpStatusCode.OK)
            {
                return this.StatusCode((int)response.StatusCode);
            }

            return new OkResult();

        }
    }
```
<a id="walkthrough" name="walkthrough_anchor"></a>

## <a name="walk-through-the-voting-sample-application"></a>Percorrer a aplicação de exemplo voto
A aplicação de voto é composta por dois serviços:
- Serviço de front-end (VotingWeb) Web - web de ASP.NET Core um serviço front-end, o que funciona a página web e expõe web APIs para comunicar com o serviço de back-end.
- O serviço de back-end (VotingData)-serviço web de uma ASP.NET Core, que expõe uma API para armazenar os resultados de voto num dictionary fiável persistentes no disco.

![Diagrama de aplicação](./media/service-fabric-tutorial-create-dotnet-app/application-diagram.png)

Quando votar na aplicação ocorrem os seguintes eventos:
1. Um JavaScript envia o pedido de voto para a API web no serviço web de front-end como um pedido HTTP PUT.

2. O serviço web de front-end utiliza um proxy para localizar e reencaminhar um pedido de HTTP PUT de mensagens em fila para o serviço de back-end.

3. O serviço de back-end demora o pedido de entrada e armazena o resultado atualizado num dictionary fiável, que obtém replicado para múltiplos nós dentro do cluster e persistentes no disco. Dados da aplicação são armazenados no cluster, pelo que não é necessária nenhuma base de dados.

## <a name="debug-in-visual-studio"></a>Depuração no Visual Studio
Quando a depuração de aplicações no Visual Studio, está a utilizar um cluster de desenvolvimento do Service Fabric local. Tem a opção para ajustar a sua experiência de depuração para o seu cenário. Nesta aplicação, podemos armazenar dados no nosso serviço de back-end, utilizando um dicionário fiável. Visual Studio remove a aplicação por predefinição, quando parar o depurador. Remover a aplicação faz com que os dados no serviço de back-end para também ser removido. Para manter os dados entre sessões de depuração, pode alterar o **modo de depuração da aplicação** como uma propriedade no **voto** projeto no Visual Studio.

Para ver o que acontece no código, conclua os seguintes passos:
1. Abra o **VotesController.cs** de ficheiros e defina um ponto de interrupção na web da API **colocar** método (linha 47) - pode procurar o ficheiro no Explorador de soluções no Visual Studio.

2. Abra o **VoteDataController.cs** de ficheiros e defina um ponto de interrupção da API web **colocar** método (50 de linha).

3. Voltar para o browser e clique numa opção de voto ou adicionar uma nova opção de voto. Atingiu o primeiro ponto de interrupção no controlador de api do web frente-fim.
    
    1. Este é onde o JavaScript no browser envia um pedido para o controlador da API web no serviço de front-end.
    
    ![Adicionar serviço de front-end de voto](./media/service-fabric-tutorial-create-dotnet-app/addvote-frontend.png)

    2. Primeiro vamos construir o URL para o ReverseProxy para o nosso serviço de back-end **(1)**.
    3. Em seguida, enviar-lhe o colocar pedido de HTTP para o ReverseProxy **(2)**.
    4. Por fim, o que enviamos a resposta do serviço de back-end para o cliente **(3)**.

4. Prima **F5** para continuar
    1. Está agora no ponto de interrupção no serviço de back-end.
    
    ![Adicionar serviço de Back-End de voto](./media/service-fabric-tutorial-create-dotnet-app/addvote-backend.png)

    2. Na primeira linha no método **(1)** estamos a utilizar o `StateManager` obter ou adicionar um dicionário fiável chamado `counts`.
    3. Todas as interações com valores num dictionary fiável necessitam de uma transação, esta utilizando instrução **(2)** cria esse transação.
    4. A transação, vamos, em seguida, atualize o valor da chave relevante para a opção de voto e consolida a operação **(3)**. Depois da consolidação método devolve, os dados é atualizado no dicionário e replicados para outros nós do cluster. Os dados estão agora armazenados em segurança no cluster e o serviço de back-end pode falhar relativamente aos outros nós, ainda que os dados disponíveis.
5. Prima **F5** para continuar

Para parar a sessão de depuração, prima **Shift + F5**.


## <a name="next-steps"></a>Passos seguintes
Nesta parte do tutorial, aprendeu como:

> [!div class="checklist"]
> * Criar um serviço de API Web do ASP.NET Core como um serviço fiável com monitorização de estado
> * Criar um serviço de aplicação Web do ASP.NET Core como um serviço web sem monitorização de estado
> * Utilizar o proxy inverso para comunicar com o serviço com estado

Avançar para o próximo tutorial:
> [!div class="nextstepaction"]
> [Implementar a aplicação no Azure](service-fabric-tutorial-deploy-app-to-party-cluster.md)

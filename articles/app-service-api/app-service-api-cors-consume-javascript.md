---
title: O suporte de CORS no App Service | Microsoft Docs
description: Saiba com utilizar o suporte de CORS no App Service do Azure.
services: app-service\api
documentationcenter: .net
author: tdykstra
manager: wpickett
editor: 
ms.assetid: 4f980a97-b9f5-4d1d-87ab-82b60bb96e1c
ms.service: app-service-api
ms.workload: na
ms.tgt_pltfrm: dotnet
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/27/2016
ms.author: rachelap
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 14de66f6035d8cd0579351d64b85bd7e1c8f2ab2


---
# <a name="consume-an-api-app-from-javascript-using-cors"></a>Consumir uma aplicação API da JavaScript utilizando CORS
O App Service oferece suporte incorporado para [Cross Origin Resource Sharing (CORS)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing), que permite aos clientes JavaScript efetuar chamadas entre domínios para APIs alojadas em API Apps. O App Service permite-lhe configurar o acesso CORS à sua API sem escrever qualquer código na sua API.

Este artigo contém duas secções:

* A secção [Como configurar a CORS](#corsconfig) explica, de um modo geral, como configurar a CORS para qualquer aplicação API, aplicação Web ou aplicação móvel. Aplica-se igualmente a todas as arquiteturas que são suportadas pelo App Service, incluindo .NET, Node.js e Java. 
* Começando pela secção [Continuar os tutoriais de introdução ao .NET](#tutorialstart) , o artigo é um tutorial que demonstra o suporte de CORS, dando continuidade ao que já tinha feito no [primeiro tutorial de introdução das API Apps](app-service-api-dotnet-get-started.md). 

## <a name="a-idcorsconfiga-how-to-configure-cors-in-azure-app-service"></a><a id="corsconfig"></a> Como configurar o CORS no Serviço de Aplicações do Azure
Pode configurar a CORS no Portal do Azure ou através das ferramentas do [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md).

#### <a name="configure-cors-in-the-azure-portal"></a>Configurar a CORS no Portal do Azure
1. Num browser, aceda ao [Portal do Azure](https://portal.azure.com/).
2. Clique em **Serviços de Aplicações**, e, em seguida, clique no nome da sua aplicação API.
   
    ![Selecionar a aplicação API no portal](./media/app-service-api-cors-consume-javascript/browseapiapps.png)
3. No painel **Definições** que abre à direita do painel **Aplicação API**, localize a secção **API** e, em seguida, clique em **CORS**.
   
   ![Selecionar a CORS no painel Definições](./media/app-service-api-cors-consume-javascript/clicksettings.png)
4. Na caixa de texto introduza o URL ou URLs a partir dos quais pretende receber chamadas JavaScript.

    Por exemplo, se tiver implementado a aplicação JavaScript para uma aplicação Web com o nome todolistangular, introduza "https://todolistangular.azurewebsites.net". Como alternativa, pode introduzir um asterisco (*) para especificar que todos os domínios de origem são aceites.


1. Clique em **Guardar**.
   
   ![Clicar em Guardar](./media/app-service-api-cors-consume-javascript/corsinportal.png)
   
   Depois de clicar em **Guardar**, a aplicação API aceitará chamadas JavaScript a partir dos URLs especificados.

#### <a name="configure-cors-by-using-azure-resource-manager-tools"></a>Configurar a CORS utilizando ferramentas do Azure Resource Manager
Também pode configurar a CORS para uma aplicação API utilizando [modelos do Azure Resource Manager](../resource-group-authoring-templates.md) nas ferramentas da linha de comandos como [Azure PowerShell](../powershell-install-configure.md) e [CLI do Azure](../xplat-cli-install.md). 

Para obter um exemplo de um modelo do Azure Resource Manager que define a propriedade CORS, abra o [ficheiro azuredeploy.json no repositório para a aplicação de exemplo no tutorial](https://github.com/azure-samples/app-service-api-dotnet-todo-list/blob/master/azuredeploy.json). Localize a secção do modelo que se pareça com o exemplo seguinte:

        "cors": {
            "allowedOrigins": [
                "todolistangular.azurewebsites.net"
            ]
        }

## <a name="a-idtutorialstarta-continuing-the-net-gettingstarted-tutorial"></a><a id="tutorialstart"></a>Continuar o tutorial de introdução ao .NET
Se estiver a seguir a série de introdução ao Node.js ou Java para API Apps, concluiu a série de introdução. Avance para a secção [Passos seguintes](#next-steps) para encontrar sugestões para aprender mais sobre API Apps.

O resto deste artigo é uma continuação da série de introdução ao .NET e pressupõe que concluiu com êxito o [primeiro tutorial](app-service-api-dotnet-get-started.md).

## <a name="deploy-the-todolistangular-project-to-a-new-web-app"></a>Implementar o projeto ToDoListAngular numa nova aplicação Web
No [primeiro tutorial](app-service-api-dotnet-get-started.md), criou uma aplicação API de camada média e uma aplicação API de camada de dados. Neste tutorial, vai criar uma aplicação Web de aplicação de página única (SPA) que chama a aplicação API de camada média. Para que a SPA funcione, tem de ativar a CORS na aplicação API de camada média. 

Na [aplicação de exemplo ToDoList](https://github.com/Azure-Samples/app-service-api-dotnet-todo-list), o projeto ToDoListAngular é um cliente AngularJS simples que chama o projeto API Web ToDoListAPI de camada média. O código JavaScript no ficheiro *app/scripts/todoListSvc.js* chama a API, utilizando o fornecedor AngularJS HTTP. 

        angular.module('todoApp')
        .factory('todoListSvc', ['$http', function ($http) {

            $http.defaults.useXDomain = true;
            delete $http.defaults.headers.common['X-Requested-With']; 

            return {
                getItems : function(){
                    return $http.get(apiEndpoint + '/api/TodoList');
                },

                /* Get by ID, Put, and Delete methods not shown */

                postItem : function(item){
                    return $http.post(apiEndpoint + '/api/TodoList', item);
                }
            };
        }]);

### <a name="create-a-new-web-app-for-the-todolistangular-project"></a>Criar uma nova aplicação Web para o projeto ToDoListAngular
O procedimento para criar uma nova aplicação Web do App Service e implementar um projeto no mesmo é semelhante ao que viu para [criar e implementar uma aplicação API no primeiro tutorial nesta série](app-service-api-dotnet-get-started.md#createapiapp). A única diferença é que o tipo de aplicação é **Aplicação Web** em vez de **Aplicação API**.  Para capturas de ecrã das caixas de diálogo, consulte 

1. No **Explorador de Soluções**, clique com o botão direito do rato no projeto ToDoListAngular e, em seguida, clique em **Publicar**.
2. No separador **Perfil** do assistente **Publicar Web**, clique em **App Service do Microsoft Azure**.
3. Na caixa de diálogo **App Service**, clique em **Novo**.
4. No separador **Alojamento** da caixa de diálogo **Criar App Service**, introduza um **Nome da Aplicação Web** que seja exclusivo no domínio *azurewebsites.net*. 
5. Escolha a **Subscrição** do Azure com a qual pretende trabalhar.
6. Na lista pendente **Grupo de recursos**, selecione o mesmo grupo de recursos que criou anteriormente.
7. Na lista pendente **Plano do App Service**, selecione o mesmo plano que criou anteriormente. 
8. Clique em **Criar**.
   
    O Visual Studio cria a aplicação Web, cria um perfil de publicação para o mesmo e apresenta o passo **Ligação** do assistente **Publicar Web**.
   
    Não clique ainda em **Publicar**. Na secção seguinte, configure a nova aplicação Web para chamar a aplicação API de camada média que está em execução no App Service. 

### <a name="set-the-middle-tier-url-in-web-app-settings"></a>Definir o URL de camada média nas definições da aplicação Web
1. Aceda ao [Portal do Azure](https://portal.azure.com/), e, em seguida, navegue para o painel **Aplicação Web** da aplicação Web que criou para alojar o projeto TodoListAngular (front-end).
2. Clique em **Definições > Definições da Aplicação**.
3. Na secção **Definições da aplicação**, adicione a seguinte chave e valor:
   
   | Chave | Valor | Exemplo |
   | --- | --- | --- |
   | toDoListAPIURL |https://{o nome da aplicação API de camada média}.azurewebsites.net |https://todolistapi0121.azurewebsites.net |
4. Clique em **Guardar**.
   
    Quando o código é executado no Azure, este valor substitui o URL de localhost que está a ser o *Web. config* ficheiro. 
   
    O código que obtém o valor de definição está em *index.cshtml*:
   
        <script type="text/javascript">
            var apiEndpoint = "@System.Configuration.ConfigurationManager.AppSettings["toDoListAPIURL"]";
        </script>
        <script src="app/scripts/todoListSvc.js"></script>
   
    O código em *todoListSvc.js* utiliza a definição:
   
        return {
            getItems : function(){
                return $http.get(apiEndpoint + '/api/TodoList');
            },
            getItem : function(id){
                return $http.get(apiEndpoint + '/api/TodoList/' + id);
            },
            postItem : function(item){
                return $http.post(apiEndpoint + '/api/TodoList', item);
            },
            putItem : function(item){
                return $http.put(apiEndpoint + '/api/TodoList/', item);
            },
            deleteItem : function(id){
                return $http({
                    method: 'DELETE',
                    url: apiEndpoint + '/api/TodoList/' + id
                });
            }
        };

### <a name="deploy-the-todolistangular-web-project-to-the-new-web-app"></a>Implementar o projeto Web ToDoListAngular na nova aplicação Web
* No Visual Studio, no passo **Ligação** do assistente **Publicar Web**, clique em **Publicar**.
  
   O Visual Studio implementa o projeto ToDoListAngular na nova aplicação Web e abre um browser para o URL da aplicação Web. 

### <a name="test-the-application-without-cors-enabled"></a>Testar a aplicação sem a CORS ativada
1. Nas Ferramentas de Programação do seu browser, abra a janela Consola.
2. Na janela do browser que apresenta a IU do AngularJS, clique na ligação **Lista de Tarefas**.
   
    O código JavaScript tenta chamar a aplicação API de camada média, mas a chamada falha porque o front-end está em execução num domínio diferente do back-end. A janela do browser Ferramentas de Programação mostra uma mensagem de erro transversal à origem.
   
    ![Mensagem de erro transversal à origem](./media/app-service-api-cors-consume-javascript/consoleaccessdenied.png)

## <a name="configure-cors-for-the-middle-tier-api-app"></a>Configurar a CORS para a aplicação API de camada média
Nesta secção, configure a definição CORS no Azure para a aplicação API ToDoListAPI de camada média. Esta definição permite à aplicação API de camada média receber chamadas JavaScript da aplicação Web que criou para o projeto ToDoListAngular.

1. Num browser, aceda ao [Portal do Azure](https://portal.azure.com/).
2. Clique em **Serviços de Aplicações**, e, em seguida, clique na aplicação API ToDoListAPI (camada média).
   
    ![Selecionar a aplicação API no portal](./media/app-service-api-cors-consume-javascript/browseapiapps.png)
3. No painel **Definições** que abre à direita do painel **Aplicação API**, localize a secção **API** e, em seguida, clique em **CORS**.
   
   ![Selecionar a CORS no portal](./media/app-service-api-cors-consume-javascript/clicksettings.png)
4. Na caixa de texto, introduza o URL para a aplicação Web ToDoListAngular (front-end). Por exemplo, se tiver implementado o projeto ToDoListAngular numa aplicação Web com o nome todolistangular0121, permita chamadas a partir do URL `https://todolistangular0121.azurewebsites.net`.
   
   Como alternativa, pode introduzir um asterisco (*) para especificar que todos os domínios de origem são aceites.
5. Clique em **Guardar**.
   
   ![Clicar em Guardar](./media/app-service-api-cors-consume-javascript/corsinportal.png)
   
   Depois de clicar em **Guardar**, a aplicação API aceitará chamadas JavaScript a partir do URL especificado. Nesta captura de ecrã, a aplicação API ToDoListAPI0223 aceitará chamadas de cliente JavaScript da aplicação Web ToDoListAngular.

### <a name="test-the-application-with-cors-enabled"></a>Testar a aplicação com a CORS ativada
* Abra um browser para o URL HTTPS da aplicação Web. 
  
    Desta vez, a aplicação permite-lhe ver, adicionar, editar e eliminar itens de tarefas. 
  
    ![Página Lista de tarefas da aplicação de exemplo](./media/app-service-api-cors-consume-javascript/corssuccess.png)

## <a name="app-service-cors-versus-web-api-cors"></a>CORS do App Service versus CORS da API Web
Num projeto API Web, pode instalar o pacote NuGet [Microsoft.AspNet.WebApi.Cors](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Cors/) para especificar no código os domínios a partir dos quais a API aceitará chamadas JavaScript.

O suporte de CORS da API Web é mais flexível do que o suporte de CORS do Serviço de Aplicação. Por exemplo, no código pode especificar diferentes origens aceites para métodos de ação diferentes, enquanto que para a CORS do App Service especifica um conjunto de origens aceites para todos os métodos de uma aplicação API.

> [!NOTE]
> Não tente utilizar as duas CORS (da API Web e do App Service) numa aplicação API. A CORS do App Service irá ter precedência e a CORS de API Web não tem qualquer efeito. Por exemplo, se ativar um domínio de origem no App Service e ativar todos os domínios de origem no seu código da API Web, a aplicação API do Azure API apenas aceitará chamadas do domínio que especificou no Azure.
> 
> 

### <a name="how-to-enable-cors-in-web-api-code"></a>Como ativar a CORS no código da API Web
Os passos seguintes resumem o processo de ativação do suporte de CORS da API Web. Para obter mais informações, consulte o artigo [Ativar pedidos de origem na API Web 2 do ASP.NET](http://www.asp.net/web-api/overview/security/enabling-cross-origin-requests-in-web-api).

1. Num projeto API Web, instale o pacote NuGet do [Microsoft.AspNet.WebApi.Cors](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Cors/).
2. Inclua uma `config.EnableCors()` linha de código no método **Registar** da classe **WebApiConfig**, como no exemplo seguinte. 
   
        public static class WebApiConfig
        {
            public static void Register(HttpConfiguration config)
            {
                // Web API configuration and services
   
                // The following line enables you to control CORS by using Web API code
                config.EnableCors();
   
                // Web API routes
                config.MapHttpAttributeRoutes();
   
                config.Routes.MapHttpRoute(
                    name: "DefaultApi",
                    routeTemplate: "api/{controller}/{id}",
                    defaults: new { id = RouteParameter.Optional }
                );
            }
        }
3. No controlador da API Web, adicione uma declaração `using` para o espaço de nomes `System.Web.Http.Cors` e adicione o atributo `EnableCors` para a classe do controlador ou métodos de ação individuais. No exemplo seguinte, o suporte de CORS aplica-se ao controlador na totalidade.
   
        namespace ToDoListAPI.Controllers 
        {
            [HttpOperationExceptionFilterAttribute]
            [EnableCors(origins:"https://todolistangular0121.azurewebsites.net", headers:"accept,content-type,origin,x-my-header", methods: "get,post")]
            public class ToDoListController : ApiController

## <a name="using-azure-api-management-with-api-apps"></a>Utilizar a API Management do Azure com as API Apps
Se utilizar a API Management do Azure com uma aplicação API, configure a CORS na API Management em vez de na aplicação API. Para obter mais informações, consulte os seguintes recursos:

* [Azure API Management Overview (Descrição Geral da Gestão de API do Azure) (vídeo: CORS começa às 12:10)](https://azure.microsoft.com/documentation/videos/azure-api-management-overview/)
* [API Management cross domain policies (Políticas entre domínios da Gestão de API)](https://msdn.microsoft.com/library/azure/dn894084.aspx#CORS)

## <a name="troubleshooting"></a>Resolução de problemas
Caso se depare com um problema ao percorrer neste tutorial, aqui estão algumas ideias de resolução de problemas.

* Certifique-se de que está a utilizar a versão mais recente do [Azure SDK para .NET para Visual Studio 2015](http://go.microsoft.com/fwlink/?linkid=518003).
* Certifique-se de que introduziu `https` na definição de CORS e certifique-se de que está a utilizar `https` para executar a aplicação Web front-end.
* Certifique-se de que introduziu a definição de CORS na aplicação API de camada média e não na aplicação Web de front-end.
* Se estiver a configurar a CORS no código da aplicação e no App Service do Azure, tenha em atenção que irá substituir a definição de CORS do Serviço de Aplicação irá substituir tudo o que estiver a fazer no código da aplicação. 

Para saber mais sobre as funcionalidades do Visual Studio que simplificam a resolução de problemas, consulte o artigo [Resolver problemas de aplicações do App Service do Azure no Visual Studio](../app-service-web/web-sites-dotnet-troubleshoot-visual-studio.md).

## <a name="next-steps"></a>Passos seguintes
Neste artigo, vimos como ativar o suporte de CORS do App Service para que o código JavaScript de cliente possa chamar uma API num domínio diferente. Para mais informações sobre API Apps, leia a [Introdução à autenticação no App Service](../app-service/app-service-authentication-overview.md), e, em seguida, aceda ao tutorial [autenticação de utilizador para API Apps](app-service-api-dotnet-user-principal-auth.md).




<!--HONumber=Nov16_HO2-->



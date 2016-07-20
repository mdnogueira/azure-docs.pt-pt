<properties
    pageTitle="Introdução às API Apps e ASP.NET no App Service | Microsoft Azure"
    description="Saiba como criar, implementar e consumir uma aplicação API do ASP.NET no App Service do Azure, utilizando o Visual Studio 2015."
    services="app-service\api"
    documentationCenter=".net"
    authors="tdykstra"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="app-service-api"
    ms.workload="na"
    ms.tgt_pltfrm="dotnet"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="05/27/2016"
    ms.author="tdykstra"/>

# Introdução às API Apps, ASP.NET e Swagger no App Service do Azure

[AZURE.INCLUDE [selector](../../includes/app-service-api-get-started-selector.md)]

Este é o primeiro de uma série de tutoriais que mostram como utilizar as funcionalidades do App Service do Azure que são úteis para desenvolver e o alojar as APIs RESTful.  Este tutorial abrange o suporte para os metadados de API no formato Swagger.

Irá aprender:

* Como criar e implementar [API Apps](app-service-api-apps-why-best-platform.md) no App Service do Azure, utilizando ferramentas incorporadas no Visual Studio 2015.
* Como automatizar a deteção de API utilizando o pacote Swashbuckle NuGet para gerar dinamicamente metadados de API do Swagger.
* Como utilizar os metadados de API do Swagger para gerar automaticamente o código de cliente para uma aplicação API.

## Descrição geral da aplicação de exemplo

Neste tutorial, vai trabalhar com uma aplicação de exemplo da lista de tarefas simples. A aplicação tem um front-end da aplicação de página única (SPA), uma camada de média de API Web do ASP.NET e uma camada de dados da API Web do ASP.NET.

![Diagrama de aplicações de exemplo de API Apps](./media/app-service-api-dotnet-get-started/noauthdiagram.png)

Eis uma captura de ecrã do front-end [AngularJS](https://angularjs.org/).

![Lista de tarefas da aplicação de exemplo das API Apps](./media/app-service-api-dotnet-get-started/todospa.png)

A solução Visual Studio inclui três projetos:

![](./media/app-service-api-dotnet-get-started/projectsinse.png)

* **ToDoListAngular** – o front-end: uma SPA AngularJS que chama a camada média. 

* **ToDoListAPI** – a camada média: um projeto API Web do ASP.NET que chama a camada de dados para efetuar operações CRUD em itens de tarefas.

* **ToDoListDataAPI** – a camada de dados: um projeto API Web do ASP.NET que efetua operações CRUD nos itens de tarefas. 

A arquitetura de três camadas é uma das muitas arquiteturas que pode implementar através de API Apps e é aqui utilizada apenas para efeitos de demonstração. O código em cada camada é tão simples quanto possível para demonstrar funcionalidades de API Apps; por exemplo, a camada de dados utiliza a memória do servidor em vez de uma base de dados como o mecanismo de persistência.

Ao concluir neste tutorial, terá os dois projetos API Web em total execução na nuvem em API Apps do App Service.

O próximo tutorial da série implementa o front-end SPA na nuvem.

## Pré-requisitos

* Web API do ASP.NET – as instruções do tutorial partem do princípio de que tem conhecimentos básicos sobre como trabalhar com a [API Web 2](http://www.asp.net/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api) do ASP.NET no Visual Studio.

* Conta do Azure – Pode [Abrir uma conta do Azure gratuitamente](/pricing/free-trial/?WT.mc_id=A261C142F) ou [Ativar os benefícios de subscritor do Visual Studio](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F).

    Se pretender começar com o App Service do Azure antes de se inscrever numa conta do Azure, aceda a [Experimentar o App Service](http://go.microsoft.com/fwlink/?LinkId=523751). Aqui, pode criar imediatamente uma aplicação móvel de arranque de curta duração no App Service — sem cartões de crédito, sem compromissos.

* Visual Studio 2015 com o [Azure SDK para .NET](http://go.microsoft.com/fwlink/?linkid=518003) – o SDK instala o Visual Studio 2015 automaticamente se ainda não o tiver.

    >[AZURE.NOTE] Dependendo do número de dependências SDK que já possui no seu computador, instalar o SDK pode demorar muito tempo, de alguns minutos a meia hora ou mais.

## Transferir a aplicação de exemplo 

1. Transfira o repositório [Azure-Samples/app-service-api-dotnet-to-do-list](https://github.com/Azure-Samples/app-service-api-dotnet-todo-list).

    Pode clicar no botão **Transferir ZIP** ou clonar o repositório no seu computador local. 

2. Abra a solução ToDoList no Visual Studio 2015 ou 2013.

2. Crie a solução para restaurar os pacotes NuGet.

    Se pretender ver a aplicação a funcionar antes de implementá-la, pode executá-la localmente. Certifique-se de que os três projetos são projetos de arranque. Terá de utilizar o Internet Explorer ou o Microsoft Edge, uma vez que esses browsers permitem chamadas JavaScript transversáveis à origem para`http://localhost` URLs. 

## Utilizar metadados e IU da API do Swagger

O suporte para metadados da API do [Swagger](http://swagger.io/) 2.0 está incorporado no App Service do Azure. Cada aplicação API pode especificar um ponto final do URL que devolve metadados para a API no formato Swagger JSON. Os metadados devolvidos por esse ponto final podem ser utilizados para gerar código de cliente. 

Um projeto API Web do ASP.NET pode gerar dinamicamente metadados do Swagger utilizando o pacote [Swashbuckle](https://www.nuget.org/packages/Swashbuckle) NuGet. O pacote Swashbuckle NuGet já está instalado nos projetos ToDoListDataAPI e ToDoListAPI que transferiu.

Nesta secção do tutorial, observe os metadados do Swagger 2.0 gerado e, em seguida, experimente uma IU baseada nos metadados do Swagger.

2. Definir o projeto ToDoListDataAPI (**não** projeto ToDoListAPI) como o projeto de arranque. 
 
4. Prima F5 ou clique em **Depurar > Iniciar depuração** para executar o projeto no modo de depuração.

    O browser abre-se e mostra a página de erro HTTP 403.

12. Na sua barra de endereço do browser, adicione `swagger/docs/v1` ao fim da linha e, em seguida, prima Regressar. (O URL é `http://localhost:45914/swagger/docs/v1`.)

    Este é o URL predefinido utilizado pelo Swashbuckle para devolver metadados do Swagger 2.0 JSON para a API. 

    Se estiver a utilizar o Internet Explorer, o browser pede-lhe para transferir um ficheiro *v1.json*.

    ![Transferir metadados JSON no IE](./media/app-service-api-dotnet-get-started/iev1json.png)

    Se estiver a utilizar o Chrome, o Firefox ou o Microsoft Edge, o browser apresenta o JSON na janela do browser. Diferentes browsers processam o JSON de forma diferente e a janela do browser poderá não ser exatamente igual ao exemplo.

    ![Metadados JSON no Chrome](./media/app-service-api-dotnet-get-started/chromev1json.png)

    O exemplo seguinte mostra a primeira secção dos metadados do Swagger para a API, com a definição para o método Get. Estes metadados são o que orienta a IU do Swagger que utiliza nos passos seguintes, e que utiliza numa secção posterior do tutorial para gerar automaticamente o código de cliente.

        {
          "swagger": "2.0",
          "info": {
            "version": "v1",
            "title": "ToDoListDataAPI"
          },
          "host": "localhost:45914",
          "schemes": [ "http" ],
          "paths": {
            "/api/ToDoList": {
              "get": {
                "tags": [ "ToDoList" ],
                "operationId": "ToDoList_GetByOwner",
                "consumes": [ ],
                "produces": [ "application/json", "text/json", "application/xml", "text/xml" ],
                "parameters": [
                  {
                    "name": "owner",
                    "in": "query",
                    "required": true,
                    "type": "string"
                  }
                ],
                "responses": {
                  "200": {
                    "description": "OK",
                    "schema": {
                      "type": "array",
                      "items": { "$ref": "#/definitions/ToDoItem" }
                    }
                  }
                },
                "deprecated": false
              },

1. Feche o browser e pare a depuração do Visual Studio.

3. No projeto ToDoListDataAPI no **Explorador de Soluções**, abra o ficheiro *App_Start\SwaggerConfig.cs* e, em seguida, desloque-se para baixo para o código seguinte e anule os comentários do mesmo.

        /*
            })
        .EnableSwaggerUi(c =>
            {
        */

    O ficheiro *SwaggerConfig.cs* é criado quando instala o pacote Swashbuckle num projeto. O ficheiro fornece várias formas de configurar o Swashbuckle.

    O código cujos comentários anulou ativar a IU do Swagger que utiliza nos passos seguintes. Quando cria um projeto API Web utilizando o modelo de projeto de aplicação, este código é comentado por predefinição como medida de segurança.

5. Execute novamente o projeto.

3. Na sua barra de endereço do browser, adicione `swagger` ao fim da linha e, em seguida, prima Regressar. (O URL é `http://localhost:45914/swagger`.)

4. Quando for apresentada a página de IU do Swagger, clique em **ToDoList** para ver os métodos disponíveis.

    ![Métodos disponíveis da IU do Swagger](./media/app-service-api-dotnet-get-started/methods.png)

5. Clique no primeiro botão **Obter** na lista.

6. Na secção **Parâmetros**, introduza um asterisco como o valor do parâmetro `owner` e, em seguida, clique em **Experimentar**.

    Quando adiciona autenticação nos tutoriais posteriores, a camada média irá fornecer o ID de utilizador real à camada de dados. Por agora, todas as tarefas terão asterisco como os respetivos ID de proprietário enquanto a aplicação é executada sem a autenticação ativada.

    ![Experimentação da IU do Swagger](./media/app-service-api-dotnet-get-started/gettryitout1.png)

    A IU do Swagger chama o método ToDoList e apresenta o código de resposta e os resultados JSON.

    ![Resultados da experimentação da IU do Swagger](./media/app-service-api-dotnet-get-started/gettryitout.png)

6. Clique em **Publicar** e, em seguida, clique na caixa em **Esquema do Modelo**.

    Ao clicar no esquema do modelo preenche a caixa de entrada onde pode especificar o valor do parâmetro para o método Publicar. (Se isto não funcionar no Internet Explorer, utilize um browser diferente ou introduza o valor do parâmetro manualmente no próximo passo.)  

    ![Publicar a experimentação da IU do Swagger](./media/app-service-api-dotnet-get-started/post.png)

7. Altere o JSON na caixa de entrada do parâmetro `todo`, de modo a que tenha um aspeto semelhante ao seguinte exemplo, ou substitua o seu próprio texto de descrição:

        {
          "ID": 2,
          "Description": "buy the dog a toy",
          "Owner": "*"
        }

10. Clique em **Experimentar**.

    A API ToDoList devolve um código de resposta HTTP 204 que indica êxito.

11. Clique no primeiro botão **Obter** e, em seguida, na secção da página clique no botão **Experimentar**.

    A resposta de método Get inclui agora o novo item de tarefa. 

12. Opcional: experimente também os métodos Put, Delete e Get by ID.

14. Feche o browser e pare a depuração do Visual Studio.

O Swashbuckle funciona com qualquer projeto API Web do ASP.NET. Se pretender adicionar metadados do Swagger a um projeto existente, basta instalar o pacote Swashbuckle. 

**Nota:** os metadados Swagger incluem um ID exclusivo para cada operação de API. Por predefinição, o Swashbuckle poderá gerar IDs de operação do Swagger duplicadas para métodos de controlador da API Web. Isto acontece se o controlador sobrecarregou os métodos HTTP, como `Get()` e `Get(id)`. Para obter informações sobre como lidar com as sobrecargas, consulte o artigo [Personalizar definições da API geradas pelo Swashbuckle](app-service-api-dotnet-swashbuckle-customize.md). Se criar um projeto API Web no Visual Studio utilizando o modelo Aplicação API do Azure, o código que gera IDs de operação exclusivos é adicionado automaticamente ao ficheiro *SwaggerConfig.cs*.  

## <a id="createapiapp"></a> Criar uma aplicação API no Azure e implementar código na mesma

Nesta secção, irá utilizar as ferramentas do Azure que estão integradas no assistente **Publicar Web** do Visual Studio para criar uma nova aplicação API no Azure. Em seguida, implemente o projeto ToDoListDataAPI na nova aplicação API e chame a API executando a IU do Swagger.

1. No **Explorador de Soluções**, clique com o botão direito do rato no projeto ToDoListDataAPI e, em seguida, clique em **Publicar**.

    ![Clicar em Publicar no Visual Studio](./media/app-service-api-dotnet-get-started/pubinmenu.png)

3.  No separador **Perfil** do assistente **Publicar Web**, clique em **App Service do Microsoft Azure**.

    ![Clicar no App Service do Azure em Publicar Web](./media/app-service-api-dotnet-get-started/selectappservice.png)

4. Inicie sessão na sua conta do Azure se que ainda não o tiver feito, ou atualize as suas credenciais, se tiverem expirado.

4. Na caixa de diálogo App Service, escolha a **subscrição** do Azure que pretende utilizar e, em seguida, clique em **Novo**.

    ![Clicar em Novo na caixa de diálogo App Service](./media/app-service-api-dotnet-get-started/clicknew.png)

    O separador **Alojamento** da caixa de diálogo **Criar App Service** é apresentada.

    Uma vez que está a implementar um projeto API Web que tem o Swashbuckle instalado, o Visual Studio pressupõe que pretende criar uma aplicação API. Esta situação é indicada pelo título **Nome da aplicação API** e pelo facto de a lista pendente **Alterar Tipo** estar definida como **Aplicação API**.

    ![Tipo de aplicação na caixa de diálogo App Service](./media/app-service-api-dotnet-get-started/apptype.png)

4. Introduza um **Nome da aplicação API** que seja exclusivo no domínio *azurewebsites.net*. Pode aceitar o nome predefinido proposto pelo Visual Studio.

    Se introduzir um nome que outra pessoa já tenha utilizado, verá um ponto de exclamação vermelho à direita.

    O URL da aplicação API será `{APi app name}.azurewebsites.net`.

6. No menu pendente **Grupo de Recursos**, clique em **Novo**, e, em seguida, introduza “ToDoListGroup” ou outro nome, se preferir. 

    Um grupo de recursos é uma coleção de recursos do Azure, tais como API Apps, bases de dados, VMs e por aí adiante. Para este tutorial, é melhor criar um novo grupo de recursos porque torna mais fácil eliminar num único passo todos os recursos do Azure que criar para o tutorial.

    Esta caixa permite-lhe selecionar um [grupo de recursos](../azure-portal/resource-group-portal.md) existente ou criar um novo ao escrever um nome diferente a partir de qualquer grupo de recursos existente na sua subscrição.

4. Clique no botão **Novo** junto ao menu pendente **Plano do App Service**.

    A captura de ecrã mostra os valores de exemplo para **Nome da aplicação API**, **Subscrição**, e **Grupo de Recursos** – os seus valores serão diferentes.

    ![Caixa de diálogo Criar App Service](./media/app-service-api-dotnet-get-started/createas.png)

    Nos passos seguintes, pode criar um plano do App Service para o novo grupo de recursos. Um plano do App Service especifica os recursos de computação em que é executada a aplicação API. Por exemplo, se escolher o escalão gratuito, a aplicação API é executada em VMs partilhadas, enquanto para alguns escalões pagos, é executada em VMs dedicadas. Para obter informações sobre os planos do App Service, consulte [Descrição geral dos planos do App Service](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md).

5. Na caixa de diálogo **Configurar Plano do App Service**, introduza “ToDoListPlan” ou outro nome, se preferir.

5. Na lista pendente **Localização**, escolha a localização que esteja mais perto de si.

    Esta definição especifica em que datacenter do Azure a sua aplicação será executada. Escolha uma localização perto de si para minimizar a [latência](http://www.bing.com/search?q=web%20latency%20introduction&qs=n&form=QBRE&pq=web%20latency%20introduction&sc=1-24&sp=-1&sk=&cvid=eefff99dfc864d25a75a83740f1e0090).

5. No menu pendente **Tamanho** pendente, clique **Gratuito**.

    Para este tutorial, o escalão de preço gratuito irá fornecer o desempenho suficiente.

6. Na caixa de diálogo **Configurar Plano do App Service**, clique em **OK**.

    ![Clique em OK em Configurar Plano do App Service](./media/app-service-api-dotnet-get-started/configasp.png)

7. Na caixa de diálogo **Criar App Service**, clique em **Criar**.

    ![Clicar em Criar na caixa de diálogo Criar App Service](./media/app-service-api-dotnet-get-started/clickcreate.png)

    O Visual Studio cria a aplicação API e um perfil de publicação que tem todas as definições necessárias para a aplicação API. Em seguida, é aberto assistente **Publicar Web**, que irá utilizar para implementar o projeto.

    O assistente **Publicar Web** é aberto n separador **Ligação** (mostrado abaixo). 

    No separador **Ligação**, as definições **Servidor** e **Nome do site** apontam para a aplicação API. O **Nome de utilizador** e a **Palavra-passe** são as credenciais de implementação que o Azure cria por si. Após a implementação, o Visual Studio abre um browser para o **URL de destino** (que é o objetivo apenas para o **URL de destino**).  

8. Clique em **Seguinte**. 

    ![Clicar em Seguinte no separador Ligação de Publicar Web](./media/app-service-api-dotnet-get-started/connnext.png)

    O separador que se segue é o separador **Definições** (mostrado abaixo). Aqui pode alterar o separador de configuração de compilação para implementar uma compilação de depuração para [depuração remota](../app-service-web/web-sites-dotnet-troubleshoot-visual-studio.md#remotedebug). O separador também oferece várias **Opções de Publicação do Ficheiro**:

    * Remover ficheiros adicionais no destino
    * Pré-compilar durante a publicação
    * Excluir ficheiros da pasta App_Data

    Para este tutorial, não precisa de nenhuma. Para obter explicações detalhadas sobre a funcionalidade dos mesmos, consulte o artigo [Como: implementar um Projeto Web utilizando a Publicação com um clique no Visual Studio](https://msdn.microsoft.com/library/dd465337.aspx).

14. Clique em **Seguinte**.

    ![Clicar em Seguinte no separador Definições do Publicar Web](./media/app-service-api-dotnet-get-started/settingsnext.png)

    Em seguida, é a vez do separador **Pré-visualização** (mostrado abaixo), que lhe dá a oportunidade de ver os ficheiros que vão ser copiados do seu projeto para a aplicação API. Quando estiver a implementar um projeto para uma aplicação API que já tenha implementado anteriormente, apenas os ficheiros alterados são copiados. Se pretender ver uma lista daquilo que será copiado, pode clicar no botão **Iniciar Pré-visualização**.

15. Clique em **Publicar**.

    ![Clicar em Publicar no separador Pré-visualização do Publicar Web](./media/app-service-api-dotnet-get-started/clickpublish.png)

    O Visual Studio implementa o projeto ToDoListDataAPI para a nova aplicação API. O janela **Saída** regista a implementação bem sucedida e é apresentada uma página “criada com êxito” numa janela do browser aberta para o URL da aplicação API.

    ![Implementação efetuada com êxito da janela Saída](./media/app-service-api-dotnet-get-started/deploymentoutput.png)

    ![Página de criação com êxito da nova aplicação API](./media/app-service-api-dotnet-get-started/appcreated.png)

11. Adicione “swagger” ao URL na barra de endereço do browser e, em seguida, prima Enter. (O URL é `http://{apiappname}.azurewebsites.net/swagger`.)

    O browser apresenta a mesma IU do Swagger que vimos anteriormente, mas agora está em execução na nuvem. Experimente o método Get e vai reparar que está de volta aos itens de tarefas 2 predefinidos. As alterações que efetuou anteriormente foram guardadas na memória no computador local.

12. Abra o [Portal do Azure](https://portal.azure.com/).

    O Portal do Azure é uma interface Web para a gestão de recursos do Azure como é o caso das API Apps.
 
14. Clique em **Procurar > Serviços de Aplicações**.

    ![Procurar Serviços de Aplicações](./media/app-service-api-dotnet-get-started/browseas.png)

15. No painel **Serviços de Aplicações**, localize e clique na sua nova aplicação API. (No Portal do Azure, as janelas que abrem à direita são denominadas *painéis*.)

    ![Painel Serviços de Aplicações](./media/app-service-api-dotnet-get-started/choosenewapiappinportal.png)

    São abertos dois painéis. Um painel tem uma descrição geral da aplicação API e um tem uma longa lista de definições que pode ver e alterar.

16. No painel **Definições**, localize a secção **API** e clique em **Definição da API**. 

    ![Definição da API no painel Definições](./media/app-service-api-dotnet-get-started/apidefinsettings.png)

    O painel **Definição da API** permite-lhe especificar o URL que devolve os metadados do Swagger 2.0 no formato JSON. Quando o Visual Studio cria a aplicação API, define o URL de definição da API para o valor predefinido para metadados gerados pelo Swashbuckle que vimos anteriormente, que são o URL de base da aplicação API mais `/swagger/docs/v1`. 

    ![URL de definição da API](./media/app-service-api-dotnet-get-started/apidefurl.png)

    Quando seleciona uma aplicação API para gerar o código de cliente para a mesma, o Visual Studio obtém os metadados a partir deste URL. 

## <a id="codegen"></a> Gerar código de cliente para a camada de dados

Uma das vantagens de integrar o Swagger em API Apps do Azure é a geração automática de código. As classes de cliente geradas tornam mais fácil escrever código que chama uma aplicação API.

O projeto ToDoListAPI já tem o código de cliente gerado, mas nos passos seguintes irá eliminá-lo e voltar a gerá-lo para ver como efetuar a geração de código.

1. No **Explorador de Soluções** do Visual Studio, no projeto ToDoListAPI, elimine a pasta *ToDoListDataAPI*. **Atenção: Elimine apenas a pasta, não o projeto ToDoListDataAPI.**

    ![Eliminar o código de cliente gerado](./media/app-service-api-dotnet-get-started/deletecodegen.png)

    Esta pasta foi criada utilizando o processo de geração de código que está prestes a seguir.

2. Clique com o botão direito do rato no projeto ToDoListAPI e, em seguida, clique em **Adicionar > Cliente de API REST**.

    ![Adicionar cliente de API REST no Visual Studio](./media/app-service-api-dotnet-get-started/codegenmenu.png)

3. Na caixa de diálogo **Adicionar Cliente de API REST**, clique em **URL do Swagger**, e, em seguida, clique em **Selecionar Recurso do Azure**.

    ![Selecionar Recurso do Azure](./media/app-service-api-dotnet-get-started/codegenbrowse.png)

8. Na caixa de diálogo **App Service**, expanda o grupo de recursos que está a utilizar para este tutorial e selecione a aplicação API e, em seguida, clique em **OK**.

    ![Selecionar a aplicação API para a geração de código](./media/app-service-api-dotnet-get-started/codegenselect.png)

    Repare que, ao regressar à caixa de diálogo **Adicionar Cliente de API REST**, a caixa de texto foi preenchida com o valor de URL de definição da API que vimos anteriormente no portal. 

    ![URL de Definição da API](./media/app-service-api-dotnet-get-started/codegenurlplugged.png)

    >[AZURE.TIP] Uma maneira de obter metadados para a geração de código é introduzir o URL diretamente em vez de percorrer a caixa de diálogo de procura. Ou, se pretender gerar o código de cliente antes de implementar no Azure, poderia executar o projeto de API Web localmente, aceder ao URL que fornece o ficheiro de JSON do Swagger, guardar o ficheiro e utilizar a opção **Selecionar um ficheiro de metadados do Swagger existente**.

9. Na caixa de diálogo **Adicionar cliente de API REST**, clique em **OK**.

    O Visual Studio cria uma pasta denominada após a aplicação de API e gera classes de cliente.

    ![Ficheiros de código para cliente gerado](./media/app-service-api-dotnet-get-started/codegenfiles.png)

5. No projeto ToDoListAPI, abra *Controllers\ToDoListController.cs* para ver o código que chama a API utilizando o cliente gerado. 

    O fragmento seguinte mostra como o código instancia o objeto de cliente e chama o método Get.

        private static ToDoListDataAPI NewDataAPIClient()
        {
            var client = new ToDoListDataAPI(new Uri(ConfigurationManager.AppSettings["toDoListDataAPIURL"]));
            return client;
        }
        
        public async Task<IEnumerable<ToDoItem>> Get()
        {
            using (var client = NewDataAPIClient())
            {
                var results = await client.ToDoList.GetByOwnerAsync(owner);
                return results.Select(m => new ToDoItem
                {
                    Description = m.Description,
                    ID = (int)m.ID,
                    Owner = m.Owner
                });
            }
        }

    O parâmetro construtor obtém o URL de ponto final a partir da definição de aplicação `toDoListDataAPIURL`. No ficheiro Web.config, esse valor é definido para o URL de Express de IIS local do projeto API, de modo a que possa executar a aplicação localmente. Se omitir o parâmetro construtor, o ponto final predefinido é o URL a partir do qual gerou o código.

6. A classe de cliente será gerado com um nome diferente com base no nome da aplicação API; altere o código em *Controllers\ToDoListController.cs* para que o nome do tipo corresponda ao que foi gerado no seu projeto. Por exemplo, se denominar a aplicação API ToDoListDataAPI0121, alteraria este código:

        private static ToDoListDataAPI NewDataAPIClient()
        {
            var client = new ToDoListDataAPI(new Uri(ConfigurationManager.AppSettings["toDoListDataAPIURL"]));

para este:

        private static ToDoListDataAPI0121 NewDataAPIClient()
        {
            var client = new ToDoListDataAPI0121(new Uri(ConfigurationManager.AppSettings["toDoListDataAPIURL"]));


## Criar uma aplicação API para alojar a camada média

Anteriormente, [criou a aplicação API de camada de dados e implementou código na mesma](#createapiapp).  Agora, vai seguir o mesmo procedimento para a aplicação API de camada média. 

1. No **Explorador de Soluções**, clique com o botão direito do rato no projeto ToDoListAPI de camada média (e não na ToDoListDataAPI de camada de dados) e, em seguida, clique com o botão direito em **Publicar**.

    ![Clicar em Publicar no Visual Studio](./media/app-service-api-dotnet-get-started/pubinmenu2.png)

3.  No separador **Perfil** do assistente **Publicar Web**, clique em **App Service do Microsoft Azure**.

5. Na caixa de diálogo **App Service**, clique em **Novo**.

3. No separador **Alojamento** da caixa de diálogo **Criar App Service**, aceite o **Nome da Aplicação API** predefinido ou introduza um nome que seja exclusivo no domínio *azurewebsites.net*. 

5. Escolha a **Subscrição** do Azure que estiver a utilizar.

6. No menu pendente **Grupo de recursos**, selecione o mesmo grupo de recursos que criou anteriormente.

4. No menu pendente **Plano do App Service**, selecione o mesmo plano que criou anteriormente. Será predefinido para esse valor. 

7. Clique em **Criar**.

    O Visual Studio cria a aplicação API, cria um perfil de publicação para a mesma e apresenta o passo **Ligação** do assistente **Publicar Web**.

3.  No passo **Ligação** do assistente **Publicar Web**, clique em **Publicar**.

    O Visual Studio implementa o projeto ToDoListAPI na nova aplicação API e abre um browser para o URL da aplicação API. É apresentada a página “criada com êxito”.

## Configurar a camada média para chamar a camada de dados

Se chamar a aplicação API de camada média agora, tentaria chamar a camada de dados utilizando o URL de localhost que ainda se encontra no ficheiro Web.config. Nesta secção, vai introduzir o URL da aplicação API de camada de dados numa definição do ambiente na aplicação API de camada média. Como o código na aplicação API de camada média recupera a definição do URL da camada de dados, a definição do ambiente irá substituir o que estiver no ficheiro Web.config. 
 
1. Aceda ao [Portal do Azure](https://portal.azure.com/), e, em seguida, navegue para o painel **Aplicação API** da aplicação API que criou para alojar o projeto TodoListAPI (camada média).

2. No painel **Definições** da Aplicação API, clique em **Definições da aplicação**.
 
4. No painel **Definições da Aplicação** da Aplicação API, desloque-se para baixo até á secção **Definições da aplicação** e adicione a seguinte chave e valor:

  	| **Chave** | toDoListDataAPIURL |
  	|---|---|
  	| **Valor** | https://{o nome da aplicação API de camada de dados}.azurewebsites.net |
  	| **Exemplo** | https://todolistdataapi0121.azurewebsites.net |

4. Clique em **Guardar**.

    ![Clicar em Guardar para as Definições da Aplicação](./media/app-service-api-dotnet-get-started/asinportal.png)

    Quando o código é executado no Azure, este valor irá substituir o URL de localhost que se encontra no ficheiro Web.config. 

## Teste

11. Numa janela do browser, navegue para o URL da nova aplicação API de camada média que acabou de criar para ToDoListAPI. Pode aceder à mesma clicando no URL no painel principal da aplicação API no portal.

13. Adicione “swagger” ao URL na barra de endereço do browser e, em seguida, prima Enter. (O URL é `http://{apiappname}.azurewebsites.net/swagger`.)

    O browser apresenta a IU do Swagger que vimos anteriormente para ToDoListDataAPI, mas agora `owner` não é um campo obrigatório para a operação Get, porque a aplicação API de camada média está a enviar esse valor para a aplicação API de camada de dados por si. (Quando fizer os tutoriais de autenticação, a camada média irá enviar os IDs de utilizador reais para o parâmetro `owner`; por agora, está a pré-programar um asterisco.)

12. Experimente o método Get e os outros métodos para validar que a aplicação API camada média está a chamar com êxito a aplicação API de camada de dados.

    ![Método Get da IU do Swagger](./media/app-service-api-dotnet-get-started/midtierget.png)

## Resolução de problemas

Caso se depare com um problema à medida que percorre este tutorial, aqui estão algumas ideias de resolução de problemas.

* Certifique-se de que está a utilizar a versão mais recente do [Azure SDK para .NET](http://go.microsoft.com/fwlink/?linkid=518003).

* Dois dos nomes de projeto são semelhantes (ToDoListAPI, ToDoListDataAPI). Se as coisas não aparecerem como descrito nas instruções quando estiver a trabalhar com um projeto, certifique-se de que abriu o projeto correto.

* Caso se encontre numa rede empresarial e esteja a tentar implementar no App Service do Azure através de uma firewall, certifique-se de que as portas 443 e 8172 estão abertas para Web Deploy. Se não for possível abrir essas portas, pode utilizar outros métodos de implementação.  Consulte o artigo [Implementar a aplicação no App Service do Azure](../app-service-web/web-sites-deploy.md).

* Erros “Nomes de rota tem de ser exclusivos” – pode obter estes erros se acidentalmente implementar o projeto errado para uma aplicação API e posteriormente implementar o correto para a mesma. Para corrigir isto, volte a implementar o projeto correto para a aplicação API e no separador **Definições** do assistente **Publicar Web**, selecione **Remover ficheiros adicionais no destino**.

Depois de ter a aplicação API do ASP.NET em execução no App Service do Azure, pode querer saber mais sobre as funcionalidades do Visual Studio que simplificam a resolução de problemas. Para obter informações sobre o registo, depuração remota e muito mais, consulte o artigo [Resolver problemas de aplicações do App Service do Azure no Visual Studio](../app-service-web/web-sites-dotnet-troubleshoot-visual-studio.md).

## Passos seguintes

Vimos como implementar projetos de API Web existentes em API Apps, gerar código de cliente para API Apps e consumir API Apps de clientes .NET. O próximo tutorial nesta série mostra como [utilizar a CORS para consumir API Apps a partir de clientes JavaScript](app-service-api-cors-consume-javascript.md).
 
Para mais informações sobre a geração de código de cliente, consulte o repositório [Azure/AutoRest](https://github.com/azure/autorest) no GitHub.com. Para obter ajuda com problemas com o cliente gerado, abra um [problema no repositório AutoRest](https://github.com/azure/autorest/issues).

Se pretender criar novos projetos de aplicação API do início, utilize o modelo **Aplicação API do Azure**.

![Modelo Aplicação API no Visual Studio](./media/app-service-api-dotnet-get-started/apiapptemplate.png)

O modelo do projeto **Aplicação API do Azure** é equivalente a escolher o modelo ASP.NET 4.5.2 **Empty**, clicando na caixa de verificação para adicionar suporte da API Web e instalar o pacote Swashbuckle NuGet. Além disso, o modelo adiciona algum código de configuração do Swashbuckle concebido para impedir a criação de IDs de operação do Swagger duplicadas. Depois de criar um projeto Aplicação API, pode implementá-lo numa aplicação API da mesma forma que vimos neste tutorial.



<!--HONumber=Jun16_HO2-->



---
title: "Introdução com um exemplo"
description: "Neste artigo, iremos irá apresenta-o exemplo de introdução de get de coleções de área de trabalho do Power BI."
services: power-bi-embedded
documentationcenter: 
author: guyinacube
manager: erikre
editor: 
tags: 
ROBOTS: NOINDEX
ms.assetid: d8a9ef78-ad4e-4bc7-9711-89172dc5c548
ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 09/25/2017
ms.author: asaxton
ms.openlocfilehash: 9049f95c9f81c0217c96469a45561b6cd0b33ae9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-power-bi-workspace-collections-sample"></a>Introdução ao exemplo de coleções de área de trabalho do Power BI

Com **coleções de área de trabalho do Microsoft Power BI**, pode integrar os relatórios do Power BI diretamente no seu web ou de aplicações móveis. Neste artigo, iremos introduzir ao **coleções de área de trabalho do Power BI** get de exemplo foi iniciada.

> [!IMPORTANT]
> As Coleções de Áreas de Trabalho do Power BI foram preteridas e estão disponíveis até junho de 2018 ou até quando indicar o contrato. Recomendamos que planeie a migração para o Power BI Embedded para evitar interrupções na sua aplicação. Para obter informações sobre como migrar os dados para o Power BI Embedded, veja [How to migrate Power BI Workspace Collections content to Power BI Embedded (Como migrar o conteúdo das Coleções de Áreas de Trabalho do Power BI para o Power BI Embedded)](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/).

Antes que vá qualquer adicional, pretende guardar os seguintes recursos: poderem ajudá-lo ao integrar demasiado relatórios do Power BI para a aplicação de exemplo e as suas aplicações.

* [Aplicação de web de área de trabalho de exemplo](http://go.microsoft.com/fwlink/?LinkId=761493)
* [Referência da API de coleções de área de trabalho de BI de energia](https://msdn.microsoft.com/library/azure/mt711507.aspx)
* [Power BI .NET SDK ](http://go.microsoft.com/fwlink/?LinkId=746472) (disponível através do NuGet)
* [Exemplo de incorporação de relatório de JavaScript](https://microsoft.github.io/PowerBI-JavaScript/demo)

> [!NOTE]
> Antes de pode configurar e executar as coleções de área de trabalho do Power BI obter iniciado exemplo, tem de criar pelo menos um **coleção de área de trabalho** na sua subscrição do Azure. Para saber como criar um **coleção de área de trabalho** no portal do Azure, consulte [introdução às coleções de área de trabalho do Power BI](get-started.md).

## <a name="configure-the-sample-app"></a>Configurar a aplicação de exemplo

Vamos guiá-lo através da configuração de ambiente de desenvolvimento de Visual Studio para aceder a componentes necessários para executar a aplicação de exemplo.

1. Transfira e deszipe o [coleções de área de trabalho do Power BI - integrar um relatório numa aplicação web](http://go.microsoft.com/fwlink/?LinkId=761493) em GitHub.
2. Abra **PowerBI embedded.sln** no Visual Studio. Poderá ter de executar o **pacote de atualização** comando na consola do Gestor de pacotes NuGet para atualizar os pacotes utilizados nesta solução.
3. Compilar a solução.
4. Execute o **ProvisionSample** aplicação de consola. Na aplicação de consola exemplo, Aprovisiona uma área de trabalho e importar um ficheiro PBIX.
5. Para aprovisionar um novo **área de trabalho**, selecione a opção 1, **gestão de recolha**e, em seguida, selecione a opção 6, **aprovisionar uma nova área de trabalho**
6. Para importar uma nova **relatório**, selecione a opção 2, **reportar gestão**e, em seguida, selecione a opção 3, **ficheiro importar ambiente de trabalho de PBIX para uma área de trabalho**.

7. Introduza o **coleção de área de trabalho** nome, e **chave de acesso**. Pode obter estes **portal do Azure**. Para saber mais sobre como obter o **chave de acesso**, consulte [chaves de acesso de API do Vista Power BI](get-started.md#view-power-bi-api-access-keys) na introdução ao Microsoft Power BI Embedded.

    ![Chaves de acesso no portal do Azure](media/get-started-sample/azure-portal.png)
8. Copie e guarde o recém-criado **ID da área de trabalho** para utilizar posteriormente neste artigo. Depois do **ID da área de trabalho** é criado, pode encontrá-lo a **portal do Azure**.

    ![ID da área de trabalho no portal do Azure](media/get-started-sample/workspace-id.png)
9. Para importar um ficheiro PBIX para sua **área de trabalho**, selecione a opção **6. Ficheiro de importação de ambiente de trabalho PBIX para uma área de trabalho existente**. Se não tiver um PBIX à mão de ficheiros, pode transferir o [PBIX de exemplo de análise de revenda](http://go.microsoft.com/fwlink/?LinkID=780547).
10. Se lhe for solicitado, introduza um nome amigável para a sua **Dataset**.

Deverá ver uma resposta como:

```
Checking import state... Publishing
Checking import state... Succeeded
```

> [!NOTE]
> Se o ficheiro PBIX contém todas as ligações de consulta direta, execute a opção 7 para atualizar as cadeias de ligação.

Neste momento, tem um relatório do Power BI PBIX importado para o seu **área de trabalho**. Agora, vamos ver como executar o **coleções de área de trabalho do Power BI** obter a aplicação web de exemplo foi iniciada.

## <a name="run-the-sample-web-app"></a>Executar a aplicação web de exemplo

O exemplo de aplicação web é uma aplicação de exemplo que apresenta-os relatórios importados para o **área de trabalho**. Eis como configurar o exemplo de aplicação web.

1. No **incorporado de PowerBI** solução Visual Studio, clique com botão direito a **EmbedSample** aplicação web e escolha **definir como projeto de arranque**.
2. No **Web. config**, no **EmbedSample** aplicação web, edite o **appSettings**: **AccessKey**, **WorkspaceCollection** nome, e **WorkspaceId**.

    ```
    <appSettings>
        <add key="powerbi:AccessKey" value="" />
        <add key="powerbi:ApiUrl" value="https://api.powerbi.com" />
        <add key="powerbi:WorkspaceCollection" value="" />
        <add key="powerbi:WorkspaceId" value="" />
    </appSettings>
    ```
3. Execute o **EmbedSample** aplicação web.

Depois de executar o **EmbedSample** aplicação web, o painel de navegação esquerdo deve conter um **relatórios** menu. Para ver o relatório que importou, expanda **relatórios**e clique num relatório. Se importou o [PBIX de exemplo de análise de revenda](http://go.microsoft.com/fwlink/?LinkID=780547), a aplicação web de exemplo seria ter este aspeto:

![Exemplo de navegação esquerda na aplicação de exemplo](media/get-started-sample/sample-left-nav.png)

Depois de clicar num relatório, o **EmbedSample** aplicação web deve ter um aspeto isto:

![Apresentar relatório na aplicação de exemplo](media/get-started-sample/sample-web-app.png)

## <a name="explore-the-sample-code"></a>Explorar o código de exemplo

O **coleções de área de trabalho do Microsoft Power BI** exemplo é uma aplicação de web de exemplo que mostra como integrar **Power BI** relatórios na sua aplicação. Utiliza um padrão de design do Model-View-Controller (MVC) para demonstrar as melhores práticas. Nesta secção realça partes do código de exemplo que pode explorar dentro de **incorporado de PowerBI** web solução de aplicação. O padrão do Model-View-Controller (MVC) separa a modelação de domínio, a apresentação e as ações com base na entrada de utilizador em três classes separadas: modelo, ver e controlo. Para saber mais sobre MVC, consulte o artigo [Saiba mais sobre o ASP.NET](http://www.asp.net/mvc).

O **coleções de área de trabalho do Microsoft Power BI** código de exemplo está separado da seguinte forma. Cada secção inclui o nome de ficheiro na solução de PowerBI embedded.sln para que pode encontrar facilmente o código no exemplo.

> [!NOTE]
> Esta secção é um resumo do código de exemplo que mostra como o código de escrita. Para ver o exemplo completo, carregue a solução de PowerBI embedded.sln no Visual Studio.

### <a name="model"></a>Modelo

O exemplo tem um **ReportsViewModel** e **ReportViewModel**.

**ReportsViewModel.cs**: representa os relatórios do Power BI.

    public class ReportsViewModel
    {
        public List<Report> Reports { get; set; }
    }

**ReportViewModel.cs**: representa um relatório do Power BI.

    public classReportViewModel
    {
        public IReport Report { get; set; }

        public string AccessToken { get; set; }
    }

### <a name="connection-string"></a>Cadeia de ligação

A cadeia de ligação tem de estar no seguinte formato:

```
Data Source=tcp:MyServer.database.windows.net,1433;Initial Catalog=MyDatabase
```

Utilizar o servidor e base de dados comuns de atributos falhar. Por exemplo: Server=tcp:MyServer.database.windows.net,1433;Database=MyDatabase,

### <a name="view"></a>Vista

O **vista** gere a apresentação do Power BI **relatórios** e um Power BI **relatório**.

**Reports.cshtml**: iterar **Model.Reports** para criar um **ActionLink**. O **ActionLink** é composto da seguinte forma:

| Parte | Descrição |
| --- | --- |
| Título |Nome do relatório. |
| Cadeia de consulta |Uma ligação para o ID de relatório. |

    <div id="reports-nav" class="panel-collapse collapse">
        <div class="panel-body">
            <ul class="nav navbar-nav">
                @foreach (var report in Model.Reports)
                {
                    var reportClass = Request.QueryString["reportId"] == report.Id ? "active" : "";
                    <li class="@reportClass">
                        @Html.ActionLink(report.Name, "Report", new { reportId = report.Id })
                    </li>
                }
            </ul>
        </div>
    </div>

Report.cshtml: Definir o **Model.AccessToken**e a expressão Lambda para **PowerBIReportFor**.

    @model ReportViewModel

    ...

    <div class="side-body padding-top">
        @Html.PowerBIAccessToken(Model.AccessToken)
        @Html.PowerBIReportFor(m => m.Report, new { style = "height:85vh" })
    </div>

### <a name="controller"></a>Controlador

**DashboardController.cs**: cria um transmissão PowerBIClient um **token de aplicação**. Um Token Web JSON (JWT) é gerado a partir de **chave de assinatura** para obter o **credenciais**. O **credenciais** são utilizados para criar uma instância de **PowerBIClient**. Assim que tiver uma instância de **PowerBIClient**, pode chamar GetReports() e GetReportsAsync().

CreatePowerBIClient()

    private IPowerBIClient CreatePowerBIClient()
    {
        var credentials = new TokenCredentials(accessKey, "AppKey");
        var client = new PowerBIClient(credentials)
        {
            BaseUri = new Uri(apiUrl)
        };

        return client;
    }

ActionResult Reports()

    public ActionResult Reports()
    {
        using (var client = this.CreatePowerBIClient())
        {
            var reportsResponse = client.Reports.GetReports(this.workspaceCollection, this.workspaceId);

            var viewModel = new ReportsViewModel
            {
                Reports = reportsResponse.Value.ToList()
            };

            return PartialView(viewModel);
        }
    }


Tarefa<ActionResult> relatório (cadeia reportId)

    public async Task<ActionResult> Report(string reportId)
    {
        using (var client = this.CreatePowerBIClient())
        {
            var reportsResponse = await client.Reports.GetReportsAsync(this.workspaceCollection, this.workspaceId);
            var report = reportsResponse.Value.FirstOrDefault(r => r.Id == reportId);
            var embedToken = PowerBIToken.CreateReportEmbedToken(this.workspaceCollection, this.workspaceId, report.Id);

            var viewModel = new ReportViewModel
            {
                Report = report,
                AccessToken = embedToken.Generate(this.accessKey)
            };

            return View(viewModel);
        }
    }

### <a name="integrate-a-report-into-your-app"></a>Integrar um relatório na aplicação

Depois de ter um **relatório**, utiliza um **IFrame** para incorporar o Power BI **relatório**. Eis um fragmento de código do powerbi.js no **coleções de área de trabalho do Microsoft Power BI** exemplo.

```
init: function() {
    var embedUrl = this.getEmbedUrl();
    var iframeHtml = '<igrame style="width:100%;height:100%;" src="' + embedUrl + 
        '" scrolling="no" allowfullscreen="true"></iframe>';
    this.element.innerHTML = iframeHtml;
    this.iframe = this.element.childNodes[0];
    this.iframe.addEventListener('load', this.load.bind(this), false);
}
```

## <a name="filter-reports-embedded-in-your-application"></a>Relatórios de filtro incorporados na aplicação

Pode filtrar um relatório incorporado utilizando uma sintaxe de URL. Para tal, adicione um **$filter** consultar o parâmetro de cadeia com um **eq** operador para o seu url de src do iFrame com o filtro especificado. Segue-se a sintaxe de filtro de consulta:

```
https://app.powerbi.com/reportEmbed
?reportId=d2a0ea38-...-9673-ee9655d54a4a&
$filter={tableName/fieldName}%20eq%20'{fieldValue}'
```

> [!NOTE]
> {tableName/fieldName} não pode incluir espaços nem carateres especiais. {fieldValue} aceita um valor único de categoria.  

## <a name="see-also"></a>Consultar também

[Cenários comuns de coleção de área de trabalho do Microsoft Power BI](scenarios.md)  
[Autenticação e autorização nas Coleções de Áreas de Trabalho do Power BI](app-token-flow.md)  
[Incorporar um relatório](embed-report.md)  
[Criar um novo relatório a partir de um conjunto de dados](create-report-from-dataset.md)  
[Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/)  
[Exemplo de Incorporação de JavaScript](https://microsoft.github.io/PowerBI-JavaScript/demo/)  

Mais perguntas? [Tente a Comunidade do Power BI](http://community.powerbi.com/)

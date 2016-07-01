<properties
   pageTitle="Pré-visualização do Microsoft Power BI Embedded – Incorporar um relatório do Power BI com um IFrame"
   description="Pré-visualização do Microsoft Power BI Embedded – O código essencial para integrar um relatório na aplicação, como autenticar-se com o token de aplicação do Power BI Embedded, como obter relatórios"
   services="power-bi-embedded"
   documentationCenter=""
   authors="dvana"
   manager="NA"
   editor=""
   tags=""/>
<tags
   ms.service="power-bi-embedded"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="powerbi"
   ms.date="05/16/2016"
   ms.author="derrickv"/>

# Incorporar um relatório do Power BI com um IFrame
Este artigo mostra-lhe o código essencial para utilizar a API REST do **Power BI Embedded**, tokens de aplicação, um IFrame e JavaScript para integrar ou incorporar um relatório na aplicação.

Na [Introdução à Pré-visualização do Microsoft Power BI Embedded](power-bi-embedded-get-started.md), aprenderá a configurar uma **coleção de áreas de trabalho** para conter uma ou várias **áreas de trabalho** para o conteúdo do relatório. Em seguida, na [Introdução ao exemplo do Microsoft Power BI Embedded ](power-bi-embedded-get-started-sample.md) importa-se um relatório para uma **Área de trabalho**.

Este artigo mostra-lhe os passos para incorporar um relatório na aplicação. Para seguir este artigo, deverá transferir o exemplo [Integrar um relatório com um IFrame](https://github.com/Azure-Samples/power-bi-embedded-iframe) em GitHub. Este exemplo é uma aplicação simples de formulário Web ASP.NET cujo objetivo é descrever os códigos C# e JavaScript essenciais necessários para integrar um relatório. Para um exemplo mais avançado que utiliza o padrão de design do Model-View-Controller (MVC) para integrar um relatório, consulte a [Aplicação Web do dashboard de exemplo](http://go.microsoft.com/fwlink/?LinkId=761493) no GitHub.

Vamos começar por descrever como integrar um relatório do **Power BI Embedded** na aplicação.

Aqui estão os passos para integrar um relatório.

- Passo 1: [Obter um relatório numa área de trabalho](#GetReport). Neste passo, utiliza-se um fluxo de token de aplicação para obter um token de acesso para chamar a operação REST [Obter Relatórios](https://msdn.microsoft.com/library/mt711510.aspx). Depois de obter um relatório na lista **Obter relatórios**, pode incorporá-lo numa aplicação com um elemento **IFrame**.
- Passo 2: [Incorporar um relatório numa aplicação](#EmbedReport). Neste passo, utiliza-se um token de incorporação para um relatório, JavaScript e um IFrame para integrar ou incorporar um relatório numa aplicação Web.

Se pretender executar o exemplo para ver como integrar um relatório, transfira o exemplo [Integrar um relatório com um IFrame](https://github.com/Azure-Samples/power-bi-embedded-iframe) em GitHub e configure três definições de Web.Config:

- **AccessKey**: utiliza-se um **AccessKey** para gerar um Token Web JSON (JWT) que é utilizado para obter relatórios e incorporar um relatório. Para saber como obter um **AccessKey**, consulte o artigo [Introdução à Pré-visualização do Microsoft Power BI Embedded](power-bi-embedded-get-started.md).
- **WorkspaceName**: Para saber como obter um **WorkspaceName**, consulte o artigo [Introdução à Pré-visualização do Microsoft Power BI Embedded](power-bi-embedded-get-started.md).
- **WorkspaceId**: Para saber como obter um **WorkspaceId**, consulte o artigo [Introdução à Pré-visualização do Microsoft Power BI Embedded](power-bi-embedded-get-started.md).

As secções seguintes mostram o código que precisa para integrar um relatório.

<a name="GetReport"/>
## Obter um relatório numa área de trabalho

Para integrar um relatório numa aplicação, precisa da **ID** e do **embedUrl** do relatório. Para obter a **ID** e o **embedUrl** do relatório, tem de chamar a operação REST de [Obter Relatórios](https://msdn.microsoft.com/library/mt711510.aspx) e escolher um relatório na lista JSON. Em [Incorporar um relatório numa aplicação](#EmbedReport), utilize a **ID** e o **embedUrl** do relatório para incorporar o relatório na aplicação.

### Resposta JSON de Obter relatórios 
```
{
  "@odata.context":"https://api.powerbi.com/beta/collections/{WorkspaceName}/workspaces/{WorkspaceId}/$metadata#reports","value":[
    {
      "id":"804d3664-…-e71882055dba","name":"Import report sample","webUrl":"https://embedded.powerbi.com/reports/804d3664-...-e71882055dba","embedUrl":"https://embedded.powerbi.com/appTokenReportEmbed?reportId=804d3664-...-e71882055dba"
    },{
      "id":"1d7cff58-…-380610e263a9","name":"Sample Report 2","webUrl":"https://embedded.powerbi.com/reports/1d7cff58-...-380610e263a9","embedUrl":"https://embedded.powerbi.com/appTokenReportEmbed?reportId=1d7cff58-...-380610e263a9"
    }
  ]
}

```

Para chamar a operação REST de [Obter Relatórios](https://msdn.microsoft.com/library/mt711510.aspx), utilize um token da aplicação. Para saber mais sobre o fluxo do token da aplicação, consulte o artigo [Sobre o fluxo do token da aplicação no Power BI Embedded](power-bi-embedded-app-token-flow.md). O código seguinte descreve como obter uma lista JSON de relatórios. Para incorporar um relatório, consulte o artigo [Incorporar um relatório numa aplicação](#EmbedReport).

```
protected void getReportsButton_Click(object sender, EventArgs e)
{
    //Get an app token to generate a JSON Web Token (JWT). An app token flow is a claims-based design pattern.
    //To learn how you can code an app token flow to generate a JWT, see the PowerBIToken class.
    var appToken = PowerBIToken.CreateDevToken(workspaceName, workspaceId);

    //After you get a PowerBIToken which has Claims including your WorkspaceName and WorkspaceID,
    //you generate JSON Web Token (JWT) . The Generate() method uses classes from System.IdentityModel.Tokens: SigningCredentials,
    //JwtSecurityToken, and JwtSecurityTokenHandler.
    string jwt = appToken.Generate(accessKey);

    //Set app token textbox to JWT string to show that the JWT was generated
    appTokenTextbox.Text = jwt;

    //Construct reports uri resource string
    var uri = String.Format("https://api.powerbi.com/beta/collections/{0}/workspaces/{1}/reports", workspaceName, workspaceId);

    //Configure reports request
    System.Net.WebRequest request = System.Net.WebRequest.Create(uri) as System.Net.HttpWebRequest;
    request.Method = "GET";
    request.ContentLength = 0;

    //Set the WebRequest header to AppToken, and jwt
    //Note the use of AppToken instead of Bearer
    request.Headers.Add("Authorization", String.Format("AppToken {0}", jwt));

    //Get reports response from request.GetResponse()
    using (var response = request.GetResponse() as System.Net.HttpWebResponse)
    {
        //Get reader from response stream
        using (var reader = new System.IO.StreamReader(response.GetResponseStream()))
        {
            //Deserialize JSON string into PBIReports
            PBIReports PBIReports = JsonConvert.DeserializeObject<PBIReports>(reader.ReadToEnd());

            //Clear Textbox
            tb_reportsResult.Text = string.Empty;

            //Get each report
            foreach (PBIReport rpt in PBIReports.value)
            {
                tb_reportsResult.Text += String.Format("{0}\t{1}\t{2}\n", rpt.id, rpt.name, rpt.embedUrl);
            }
        }
    }
}
```

<a name="EmbedReport"/>
## Incorporar um relatório numa aplicação

Antes de poder incorporar um relatório na aplicação, precisa de um token de incorporação para um relatório. Este token é semelhante a um token de aplicação que se utiliza para chamar as operações REST do **Power BI Embedded**, mas é gerado para um recurso de relatório em vez de para um recurso REST. Segue-se o código para obter um token de aplicação para um relatório. Para utilizar o token de aplicação de relatórios, consulte o artigo [Incorporar relatório na aplicação](#EmbedReportJS).

<a name="EmbedReportToken"/>
### Obter um token de aplicação para um relatório

```
protected void getReportAppTokenButton_Click(object sender, EventArgs e)
{
    //Get an embed token for a report.
    var token = PowerBIToken.CreateReportEmbedToken(workspaceName, workspaceId, getReportAppTokenTextBox.Text);

    //After you get a PowerBIToken which has Claims including your WorkspaceName and WorkspaceID,
    //you generate JSON Web Token (JWT) . The Generate() method uses classes from System.IdentityModel.Tokens: SigningCredentials,
    //JwtSecurityToken, and JwtSecurityTokenHandler.
    string jwt = token.Generate(accessKey);

    //Set textbox to JWT string. The JavaScript embed code uses the embed jwt for a report.
    reportAppTokenTextbox.Text = jwt;
}
```

<a name="EmbedReportJS"/>
### Incorporar relatório na aplicação

Para incorporar um relatório do **Power BI** para a aplicação, utilize um IFrame e código JavaScript. Segue-se um exemplo de IFrame e código JavaScript para incorporar um relatório. Para ver todo o código de exemplo para incorporar um relatório, consulte o artigo de exemplo [Integrar um relatório com um IFrame](https://github.com/Azure-Samples/power-bi-embedded-iframe) em GitHub.

![Iframe](media\power-bi-embedded-integrate-report\Iframe.png)


```
window.onload = function () {
    // Client side click to embed a selected report.
    var el = document.getElementById("bEmbedReportAction");
    if (el.addEventListener) {
        el.addEventListener("click", updateEmbedReport, false);
    } else {
        el.attachEvent('onclick', updateEmbedReport);
    }

};

// Update embed report
function updateEmbedReport() {
    // Check if the embed url was selected
    var embedUrl = document.getElementById('tb_EmbedURL').value;

    // To load a report do the following:
    // 1: Set the url
    // 2: Add a onload handler to submit the auth token
    var iframe = document.getElementById('iFrameEmbedReport');
    iframe.src = embedUrl;
    iframe.onload = postActionLoadReport;
}

// Post the auth token to the iFrame.
function postActionLoadReport() {

    // Get the app token.
    accessToken = document.getElementById('MainContent_reportAppTokenTextbox').value;

    // Construct the push message structure
    var m = { action: "loadReport", accessToken: accessToken };
    message = JSON.stringify(m);

    // Push the message.
    iframe = document.getElementById('iFrameEmbedReport');
    iframe.contentWindow.postMessage(message, "*");
}
```
Quando tiver um relatório incorporado na aplicação, pode filtrar o relatório. A secção seguinte mostra como filtrar um relatório com uma sintaxe de URL.

## Filtrar um relatório

Pode filtrar um relatório incorporado utilizando uma sintaxe de URL. Para fazê-lo, adicione um parâmetro de cadeia de consulta ao url de src do iFrame com o filtro especificado. Pode **Filtrar por um valor** e **Ocultar o painel de filtro**.


**Filtrar por um valor**

Para filtrar por um valor, utilize a sintaxe de consulta de **$filter** com um operador **eq** da seguinte forma:

```
https://app.powerbi.com/reportEmbed
?reportId=d2a0ea38-0694-...-ee9655d54a4a&
$filter={tableName/fieldName}%20eq%20'{fieldValue}'
```

Por exemplo, poderia filtrar por uma Cadeia de Armazéns “Lindseys”. A parte do filtro do URL teria o seguinte aspeto:

```
$filter=Store/Chain%20eq%20'Lindseys'
```

> [AZURE.NOTE] {tableName/fieldName} não pode incluir espaços nem carateres especiais. {fieldValue} aceita um valor único de categoria.

**Ocultar o Painel de Filtro**

Para ocultar o **painel de filtro**, adicionar **filterPaneEnabled** à cadeia de consulta de relatório da seguinte forma:

```
&filterPaneEnabled=false
```

## Conclusão

Neste artigo, foi apresentado o código para integrar um relatório do **Power BI** na aplicação. Para começar rapidamente a integrar um relatório para uma aplicação, transfira estes exemplos em GitHub:

- [Exemplo Integrar um relatório com um IFrame](https://github.com/Azure-Samples/power-bi-embedded-iframe)
- [Aplicação Web do dashboard de exemplo](http://go.microsoft.com/fwlink/?LinkId=761493)

## Consultar Também
- [Introdução à Pré-visualização do Microsoft Power BI Embedded](power-bi-embedded-get-started.md)
- [Introdução com exemplo](power-bi-embedded-get-started-sample.md)
- [System.IdentityModel.Tokens.SigningCredentials](https://msdn.microsoft.com/library/system.identitymodel.tokens.signingcredentials.aspx)
- [System.IdentityModel.Tokens.JwtSecurityToken](https://msdn.microsoft.com/library/system.identitymodel.tokens.jwtsecuritytoken.aspx)
- [System.IdentityModel.Tokens.JwtSecurityTokenHandler](https://msdn.microsoft.com/library/system.identitymodel.tokens.signingcredentials.aspx)
- [Obter Relatórios](https://msdn.microsoft.com/library/mt711510.aspx)



<!--HONumber=Jun16_HO2-->



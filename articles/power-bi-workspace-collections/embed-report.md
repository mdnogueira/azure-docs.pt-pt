---
title: "Incorporar um relatório em coleções de área de trabalho do Azure Power BI | Microsoft Docs"
description: "Saiba como incorporar um relatório que está a ser coleções de área de trabalho do Power BI na sua aplicação."
services: power-bi-embedded
documentationcenter: 
author: guyinacube
manager: erikre
editor: 
tags: 
ROBOTS: NOINDEX
ms.assetid: 
ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 09/20/2017
ms.author: asaxton
ms.openlocfilehash: 56e7ca90132527c0ef9d4bd478e99b75ca055272
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="embed-a-report-in-power-bi-workspace-collections"></a>Incorporar um relatório em coleções de área de trabalho do Power BI

Saiba como incorporar um relatório que está a ser coleções de área de trabalho do Power BI na sua aplicação.

> [!IMPORTANT]
> As Coleções de Áreas de Trabalho do Power BI foram preteridas e estão disponíveis até junho de 2018 ou até quando indicar o contrato. Recomendamos que planeie a migração para o Power BI Embedded para evitar interrupções na sua aplicação. Para obter informações sobre como migrar os dados para o Power BI Embedded, veja [How to migrate Power BI Workspace Collections content to Power BI Embedded (Como migrar o conteúdo das Coleções de Áreas de Trabalho do Power BI para o Power BI Embedded)](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/).

Veremos como realmente incorporar um relatório na sua aplicação. Esta é a partindo do princípio de que já tem um relatório que existe dentro de uma área de trabalho na sua coleção de área de trabalho. Se ainda não o fez ainda esse passo, consulte [começar com coleções de área de trabalho do Power BI](get-started.md).

Pode utilizar o .NET (c#) ou o SDK Node.js, juntamente com JavaScript, para criar facilmente a sua aplicação com coleções de área de trabalho do Power BI.

## <a name="using-the-access-keys-to-use-rest-apis"></a>Utilizar as chaves de acesso para utilizar REST APIs

Para chamar a API REST, pode passar a chave de acesso que pode obter a partir do portal do Azure para uma coleção de área de trabalho especificado. Para obter mais informações, consulte [começar com coleções de área de trabalho do Power BI](get-started.md).

## <a name="get-a-report-id"></a>Obter um ID de relatório

Cada token de acesso é baseado num relatório. Terá de obter o id de relatório especificado para o relatório que pretende incorporar. Isto pode ser feito com base em chamadas para o [obter relatórios](https://msdn.microsoft.com/library/azure/mt711510.aspx) REST API. Esta ação irá devolver o id de relatório e o url de incorporação. Isto pode ser feito utilizando o SDK .NET do Power BI ou chamar a API REST diretamente.

### <a name="using-the-power-bi-net-sdk"></a>Utilizando o SDK .NET do Power BI

Quando utilizar o SDK .NET, terá de criar uma credencial de token com base na chave de acesso a que obter do portal do Azure. Isto requer que instale o [pacote NuGet de API do Power BI](https://www.nuget.org/profiles/powerbi).

**Instalação de pacotes NuGet**

```
Install-Package Microsoft.PowerBI.Api
```

**Código c#**

```
using Microsoft.PowerBI.Api.V1;
using Microsoft.Rest;

var credentials = new TokenCredentials("{access key}", "AppKey");
var client = new PowerBIClient(credentials);
client.BaseUri = new Uri(https://api.powerbi.com);

var reports = (IList<Report>)client.Reports.GetReports(workspaceCollectionName, workspaceId).Value;

// Select the report that you want to work with from the collection of reports.
```

### <a name="calling-the-rest-api-directly"></a>Chamada da API REST diretamente

```
System.Net.WebRequest request = System.Net.WebRequest.Create("https://api.powerbi.com/v1.0/collections/{collectionName}/workspaces/{workspaceId}/Reports") as System.Net.HttpWebRequest;

request.Method = "GET";
request.ContentLength = 0;
request.Headers.Add("Authorization", String.Format("AppKey {0}", accessToken.Value));

using (var response = request.GetResponse() as System.Net.HttpWebResponse)
{
    using (var reader = new System.IO.StreamReader(response.GetResponseStream()))
    {
        // Work with JSON response to get the report you want to work with.
    }

}
```

## <a name="create-an-access-token"></a>Criar um token de acesso

Utilização do Power BI área de trabalho coleções incorpore tokens, que HMAC iniciada JSON Web Tokens. Os tokens são assinados com a chave de acesso da sua coleção de área de trabalho do Power BI. Incorpore tokens, por predefinição, são utilizados para fornecer acesso só de leitura para um relatório para incorporar numa aplicação. Incorpore tokens emitidos para um relatório específico e deve estar associados um URL de incorporação.

Tokens de acesso devem ser criados no servidor, como as chaves de acesso são utilizadas para os tokens de início de sessão/encriptar. Para obter informações sobre como criar um token de acesso, consulte [Authenticating e autorização com Power BI área de trabalho coleções](app-token-flow.md). Também pode rever o [CreateReportEmbedToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken?redirectedfrom=MSDN#methods_) método. Eis um exemplo de como isto iria aspeto utilizando o SDK .NET para o Power BI.

Utilize o ID de relatório que obteve anteriormente. Quando o token de incorporação for criado, em seguida, irá utilizar a chave de acesso para gerar o token que pode utilizar a partir da perspetiva de javascript. O *PowerBIToken classe* requer que instale o [pacote do Power BI Core NuGut](https://www.nuget.org/packages/Microsoft.PowerBI.Core/).

**Instalação de pacotes NuGet**

```
Install-Package Microsoft.PowerBI.Core
```

**Código c#**

```
using Microsoft.PowerBI.Security;

// rlsUsername, roles and scopes are optional.
embedToken = PowerBIToken.CreateReportEmbedToken(workspaceCollectionName, workspaceId, reportId, rlsUsername, roles, scopes);

var token = embedToken.Generate("{access key}");
```

### <a name="adding-permission-scopes-to-embed-tokens"></a>Adicionar âmbitos de permissões para incorporar tokens

Ao utilizar incorporação tokens, poderá querer restringir a utilização dos recursos de que conceder acesso. Por este motivo, pode gerar um token com permissões com âmbito definido. Para obter mais informações, consulte [âmbitos](app-token-flow.md#scopes)

## <a name="embed-using-javascript"></a>Incorporar através de JavaScript

Depois de ter o token de acesso e o ID de relatório, iremos pode incorporar o relatório utilizando o JavaScript. Isto requer a instalação do NuGet [pacote do Power BI JavaScript](https://www.nuget.org/packages/Microsoft.PowerBI.JavaScript/). O embedUrl apenas será https://embedded.powerbi.com/appTokenReportEmbed.

> [!NOTE]
> Pode utilizar o [exemplo de incorporar o relatório de JavaScript](https://microsoft.github.io/PowerBI-JavaScript/demo/) para testar a funcionalidade. Também fornece exemplos de código para as operações diferentes que estão disponíveis.

**Instalação de pacotes NuGet**

```
Install-Package Microsoft.PowerBI.JavaScript
```

**Código JavaScript**

```
<script src="/scripts/powerbi.js"></script>
<div id="reportContainer"></div>

var embedConfiguration = {
    type: 'report',
    accessToken: 'eyJ0eXAiO...Qron7qYpY9MI',
    id: '5dac7a4a-4452-46b3-99f6-a25915e0fe55',
    embedUrl: 'https://embedded.powerbi.com/appTokenReportEmbed'
};

var $reportContainer = $('#reportContainer');
var report = powerbi.embed($reportContainer.get(0), embedConfiguration);
```

### <a name="set-the-size-of-embedded-elements"></a>Definir o tamanho dos elementos incorporados

O relatório será automaticamente incorporado com base no tamanho do respectivo contentor. Para substituir o tamanho predefinido do item incorporado, adicione simplesmente um estilos de atributo ou inline classe CSS para a largura e altura.

## <a name="see-also"></a>Consultar também

[Introdução com exemplo](get-started-sample.md)  
[Autenticação e autorização nas Coleções de Áreas de Trabalho do Power BI](app-token-flow.md)  
[CreateReportEmbedToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken?redirectedfrom=MSDN#methods_)  
[Exemplo de Incorporação de JavaScript](https://microsoft.github.io/PowerBI-JavaScript/demo/)  
[Pacote do Power BI JavaScript](https://www.nuget.org/packages/Microsoft.PowerBI.JavaScript/)  
[Pacote do Power BI API NuGet](https://www.nuget.org/profiles/powerbi)
[pacote NuGut do Power BI Core](https://www.nuget.org/packages/Microsoft.PowerBI.Core/)  
[Repositório de Git do PowerBI CSharp](https://github.com/Microsoft/PowerBI-CSharp)  
[Repositório de Git do nó de PowerBI](https://github.com/Microsoft/PowerBI-Node)  

Mais perguntas? [Tente a Comunidade do Power BI](http://community.powerbi.com/)

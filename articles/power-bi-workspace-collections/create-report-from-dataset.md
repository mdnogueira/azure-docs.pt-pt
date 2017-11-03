---
title: "Criar um novo relatório a partir de um conjunto de dados em coleções de área de trabalho do Power BI | Microsoft Docs"
description: "Relatórios do Power BI coleção de área de trabalho agora podem ser criados a partir de um conjunto de dados na sua própria aplicação."
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
ms.openlocfilehash: aa902cbc4992292420948b36d85e52fafc7224de
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-new-report-from-a-dataset-in-power-bi-workspace-collections"></a>Criar um novo relatório a partir de um conjunto de dados em coleções de área de trabalho do Power BI

Relatórios do Power BI coleção de área de trabalho agora podem ser criados a partir de um conjunto de dados na sua própria aplicação.

> [!IMPORTANT]
> As Coleções de Áreas de Trabalho do Power BI foram preteridas e estão disponíveis até junho de 2018 ou até quando indicar o contrato. Recomendamos que planeie a migração para o Power BI Embedded para evitar interrupções na sua aplicação. Para obter informações sobre como migrar os dados para o Power BI Embedded, veja [How to migrate Power BI Workspace Collections content to Power BI Embedded (Como migrar o conteúdo das Coleções de Áreas de Trabalho do Power BI para o Power BI Embedded)](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/).

O método de autenticação é semelhante de incorporar um relatório. Se basear em tokens de acesso que são específicos para um conjunto de dados. Tokens utilizados para PowerBI.com são emitidos pelo Azure Active Directory (AAD). Tokens do Power BI coleção de área de trabalho são emitidos pela sua própria aplicação.

Ao criar um relatório incorporados, são os tokens emitidos para um conjunto de dados específico. Tokens devem ser associados com o URL de incorporação no mesmo elemento para assegurar que cada um token exclusivo. Para criar um relatório incorporados, *Dataset.Read e Workspace.Report.Create* âmbitos tem de ser fornecidos no token de acesso.

## <a name="create-access-token-needed-to-create-new-report"></a>Criar o token de acesso necessário para criar novo relatório

Coleções de área de trabalho do Power BI utilizar uma incorporação token, que é HMAC assinado JSON Web Tokens. Os tokens são assinados com a chave de acesso da sua coleção de área de trabalho do Power BI. Incorpore tokens, por predefinição, são utilizados para fornecer acesso só de leitura para um relatório para incorporar numa aplicação. Incorpore tokens emitidos para um relatório específico e deve estar associados um URL de incorporação.

Tokens de acesso devem ser criados no servidor, como as chaves de acesso são utilizadas para os tokens de início de sessão/encriptar. Para obter informações sobre como criar um token de acesso, consulte [Authenticating e autorização com Power BI área de trabalho coleções](app-token-flow.md). Também pode rever o [CreateReportEmbedToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken?redirectedfrom=MSDN#methods_) método. Eis um exemplo de como isto iria aspeto utilizando o SDK .NET para o Power BI.

Neste exemplo, temos de ID nosso conjunto de dados que pretendemos criar um novo relatório no. Também é necessário adicionar os âmbitos para *Dataset.Read e Workspace.Report.Create*.

O *PowerBIToken classe* requer que instale o [pacote do Power BI Core NuGut](https://www.nuget.org/packages/Microsoft.PowerBI.Core/).

**Instalação de pacotes NuGet**

```
Install-Package Microsoft.PowerBI.Core
```

**Código c#**

```
using Microsoft.PowerBI.Security;

// rlsUsername and roles are optional
string scopes = "Dataset.Read Workspace.Report.Create";
PowerBIToken embedToken = PowerBIToken.CreateReportEmbedTokenForCreation(workspaceCollectionName, workspaceId, datasetId, null, null, scopes);

var token = embedToken.Generate("{access key}");
```

## <a name="create-a-new-blank-report"></a>Criar um novo relatório em branco

Para criar um novo relatório, a configuração de criação deve ser fornecida. Isto deve incluir o token de acesso, o embedURL e datasetID que queremos para criar o relatório em relação a. Isto requer a instalação do nuget [pacote do Power BI JavaScript](https://www.nuget.org/packages/Microsoft.PowerBI.JavaScript/). O embedUrl apenas será https://embedded.powerbi.com/appTokenReportEmbed.

> [!NOTE]
> Pode utilizar o [exemplo de incorporar o relatório de JavaScript](https://microsoft.github.io/PowerBI-JavaScript/demo/) para testar a funcionalidade. Também fornece exemplos de código para as operações diferentes que estão disponíveis.

**Instalação de pacotes NuGet**

```
Install-Package Microsoft.PowerBI.JavaScript
```

**Código JavaScript**

```
<div id="reportContainer"></div>
  
var embedCreateConfiguration = {
        accessToken: 'eyJ0eXAiO...Qron7qYpY9MI',
        embedUrl: 'https://embedded.powerbi.com/appTokenReportEmbed',
        datasetId: '5dac7a4a-4452-46b3-99f6-a25915e0fe55',
    };
    
    // Grab the reference to the div HTML element that will host the report
    var reportContainer = $('#reportContainer')[0];

    // Create report
    var report = powerbi.createReport(reportContainer, embedCreateConfiguration);
```

Chamar *powerbi.createReport()* faz uma tela em branco no modo de edição aparecer dentro de *div* elemento.

![Novo relatório em branco](media/create-report-from-dataset/create-new-report.png)

## <a name="save-new-reports"></a>Guardar os novos relatórios

O relatório não é criado até tem de chamar o **guardar como** operação. Isto pode ser feito a partir do menu de ficheiro ou de JavaScript.

```
 // Get a reference to the embedded report.
    report = powerbi.get(reportContainer);
    
    var saveAsParameters = {
        name: "newReport"
    };

    // SaveAs report
    report.saveAs(saveAsParameters);
```

> [!IMPORTANT]
> É criado um novo relatório apenas após **guardar como** é chamado. Depois de guardar, a tela de ainda aparecer o conjunto de dados no modo de edição e não o relatório. Precisa de recarregar o novo relatório, tal como faria com qualquer outro tipo de relatório.

![Ficheiro Menu - Guardar como](media/create-report-from-dataset/save-new-report.png)

## <a name="load-the-new-report"></a>Carregar o novo relatório

Para interagir com o novo relatório tem de incorporá-la da mesma forma que a aplicação incorpora um relatório regular, ou seja, um novo token tem de ser emitido especificamente para o novo relatório e, em seguida, chame o método de incorporação.

```
<div id="reportContainer"></div>
  
var embedConfiguration = {
        accessToken: 'eyJ0eXAiO...Qron7qYpY9MJ',
        embedUrl: 'https://embedded.powerbi.com/appTokenReportEmbed',
        reportId: '5dac7a4a-4452-46b3-99f6-a25915e0fe54',
    };
    
    // Grab the reference to the div HTML element that will host the report
    var reportContainer = $('#reportContainer')[0];

    // Embed report
    var report = powerbi.embed(reportContainer, embedConfiguration);
```

## <a name="automate-save-and-load-of-a-new-report-using-the-saved-event"></a>Automatizar guardar e carregar de um novo relatório utilizando o evento de "guardado"

Para automatizar o processo de "Guardar como" e, em seguida, carregar o novo relatório, pode efetuar a utilizar o evento de "guardado". Este evento é desencadeado quando o guardar operação foi concluída e devolve um objeto Json que contém o reportId novo, o nome do relatório, o reportId antigo (se tiver ocorrido um) e se a operação foi saveAs ou guardar.

```
{
  "reportObjectId": "5dac7a4a-4452-46b3-99f6-a25915e0fe54",
  "reportName": "newReport",
  "saveAs": true,
  "originalReportObjectId": null
}
```

Para automatizar o processo que pode escutar do evento "guardado", colocar o novo reportId, criar o token do novo e incorporar o novo relatório com o mesmo.

```
<div id="reportContainer"></div>
  
var embedCreateConfiguration = {
        accessToken: 'eyJ0eXAiO...Qron7qYpY9MI',
        embedUrl: 'https://embedded.powerbi.com/appTokenReportEmbed',
        datasetId: '5dac7a4a-4452-46b3-99f6-a25915e0fe55',
    };
    
    // Grab the reference to the div HTML element that will host the report
    var reportContainer = $('#reportContainer')[0];

    // Create report
    var report = powerbi.createReport(reportContainer, embedCreateConfiguration);


   var saveAsParameters = {
        name: "newReport"
    };

    // SaveAs report
    report.saveAs(saveAsParameters);

    // report.on will add an event handler which prints to Log window.
    report.on("saved", function(event) {
        
         // get new Token
         var newReportId =  event.detail.reportObjectId;

        // create new Token. This is a function that the application should provide
        var newToken = createAccessToken(newReportId,scopes /*provide the wanted scopes*/);
        
        
    var embedConfiguration = {
        accessToken: newToken ,
        embedUrl: 'https://embedded.powerbi.com/appTokenReportEmbed',
        reportId: newReportId,
    };

    // Embed report
    var report = powerbi.embed(reportContainer, embedConfiguration);
       
   // report.off removes a given event handler if it exists.
   report.off("saved");
    });
```

## <a name="see-also"></a>Consultar também

[Introdução com exemplo](get-started-sample.md)  
[Guardar relatórios](save-reports.md)  
[Incorporar um relatório](embed-report.md)  
[Autenticação e autorização nas Coleções de Áreas de Trabalho do Power BI](app-token-flow.md)  
[Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/)  
[Exemplo de Incorporação de JavaScript](https://microsoft.github.io/PowerBI-JavaScript/demo/)  
[Pacote de NuGut do Power BI Core](https://www.nuget.org/packages/Microsoft.PowerBI.Core/)  
[Pacote do Power BI JavaScript](https://www.nuget.org/packages/Microsoft.PowerBI.JavaScript/)  

Mais perguntas? [Tente a Comunidade do Power BI](http://community.powerbi.com/)
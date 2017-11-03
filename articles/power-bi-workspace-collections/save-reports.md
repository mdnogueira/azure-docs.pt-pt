---
title: "Guardar relatórios em coleções de área de trabalho do Power BI | Microsoft Docs"
description: "Saiba como guardar relatórios em coleções de área de trabalho do Power BI. Isto requer permissões adequadas para que funcionem com êxito."
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
ms.openlocfilehash: 94a72ba4478aa317b4a1930b2894c1346d0590c1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="save-reports-in-power-bi-workspace-collections"></a>Guardar relatórios em coleções de área de trabalho do Power BI

Saiba como guardar relatórios em coleções de área de trabalho do Power BI. Guardar relatórios requer permissões adequadas para que funcionem com êxito.

> [!IMPORTANT]
> As Coleções de Áreas de Trabalho do Power BI foram preteridas e estão disponíveis até junho de 2018 ou até quando indicar o contrato. Recomendamos que planeie a migração para o Power BI Embedded para evitar interrupções na sua aplicação. Para obter informações sobre como migrar os dados para o Power BI Embedded, veja [How to migrate Power BI Workspace Collections content to Power BI Embedded (Como migrar o conteúdo das Coleções de Áreas de Trabalho do Power BI para o Power BI Embedded)](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/).

Dentro de coleções de área de trabalho do Power BI, pode editar os relatórios existentes e guardá-las. Também pode criar um novo relatório e guardar como um relatório novo para criar um.

Para poder guardar um relatório, terá primeiro de criar um token para o relatório específico com os âmbitos direita:

* Para ativar a guardar Report.ReadWrite âmbito é necessário
* Para ativar o guardar como, Report.Read e Workspace.Report.Copy âmbitos são necessários
* Para ativar a guardar e guardar como, são necessários Report.ReadWrite e Workspace.Report.Copy

Respetivamente para ativar o direito save/guardar como botões no menu ficheiro tem de fornecer a permissão adequada na configuração de incorporação ao incorporar o relatório:

* modelos. Permissions.ReadWrite
* modelos. Permissions.Copy
* modelos. Permissions.All

> [!NOTE]
> O token de acesso também tem dos âmbitos adequados. Para obter mais informações, consulte [âmbitos](app-token-flow.md#scopes).

## <a name="embed-report-in-edit-mode"></a>Incorporar relatório em modo de edição

Digamos que pretende incorporar um relatório em modo de edição no interior à aplicação, por isso, basta passar as propriedades de direitos numa configuração de incorporação e chamar powerbi.embed(). Forneça permissões e uma viewMode para ver o guardar e guardar como botões no modo de edição. Para obter mais informações, consulte [incorporar os detalhes de configuração](https://github.com/Microsoft/PowerBI-JavaScript/wiki/Embed-Configuration-Details).

Por exemplo, no JavaScript:

```
   <div id="reportContainer"></div>

    // Get models. Models, it contains enums that can be used.
    var models = window['powerbi-client'].models;

    // Embed configuration used to describe the what and how to embed.
    // This object is used when calling powerbi.embed.
    // This also includes settings and options such as filters.
    // You can find more information at https://github.com/Microsoft/PowerBI-JavaScript/wiki/Embed-Configuration-Details.
    var config= {
        type: 'report',
        accessToken: 'eyJ0eXAiO...Qron7qYpY9MI',
        embedUrl: 'https://embedded.powerbi.com/appTokenReportEmbed',
        id:  '5dac7a4a-4452-46b3-99f6-a25915e0fe55',
        permissions: models.Permissions.All /*both save & save as buttons will be visible*/,
        viewMode: models.ViewMode.Edit,
        settings: {
            filterPaneEnabled: true,
            navContentPaneEnabled: true
        }
    };

    // Get a reference to the embedded report HTML element
    var reportContainer = $('#reportContainer')[0];

    // Embed the report and display it within the div container.
    var report = powerbi.embed(reportContainer, config);
```

Agora um relatório é incorporado na sua aplicação no modo de edição.

## <a name="save-report"></a>Guardar o relatório

Depois de incorporar o relatório no modo de edição com o token à direita e permissões, pode guardar o relatório no menu de ficheiro ou de javascript:

```
 // Get a reference to the embedded report.
    report = powerbi.get(reportContainer);

 // Save report
    report.save();
```

## <a name="save-as"></a>Guardar como

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
> Só depois *guardar como* é um novo relatório criado. Depois do guardar, a tela é ainda que mostra o relatório antigo no modo de edição e não o novo relatório. Incorpore o novo relatório, que foi criado. Ao incorporar o novo relatório requer um novo token de acesso que são criados por cada relatório.

Em seguida, terá de carregar o novo relatório após um *guardar como*. Carregar o novo relatório é semelhante ao incorporar qualquer relatório.

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

## <a name="see-also"></a>Consultar também

[Introdução com exemplo](get-started-sample.md)  
[Incorporar um relatório](embed-report.md)  
[Criar um novo relatório a partir de um conjunto de dados](create-report-from-dataset.md)  
[Autenticação e autorização nas Coleções de Áreas de Trabalho do Power BI](app-token-flow.md)  
[Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/)  
[Exemplo de Incorporação de JavaScript](https://microsoft.github.io/PowerBI-JavaScript/demo/)  

Mais perguntas? [Tente a Comunidade do Power BI](http://community.powerbi.com/)


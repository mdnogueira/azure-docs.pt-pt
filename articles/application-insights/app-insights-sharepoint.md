---
title: Monitorize um site do SharePoint com o Application Insights
description: "Comece a monitorizar uma nova aplicação com uma nova chave de instrumentação"
services: application-insights
documentationcenter: 
author: mrbullwinkle
manager: carmonm
ms.assetid: 2bfe5910-d673-4cf6-a5c1-4c115eae1be0
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/24/2016
ms.author: mbullwin
ms.openlocfilehash: 9c07ba125e0f9eae2b8f94661abf6dc1efc0cdad
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/01/2017
---
# <a name="monitor-a-sharepoint-site-with-application-insights"></a>Monitorize um site do SharePoint com o Application Insights
O Azure Application Insights monitoriza a disponibilidade, o desempenho e a utilização das suas aplicações. Aqui, irá aprender a configurá-lo para um site do SharePoint.

## <a name="create-an-application-insights-resource"></a>Criar um recurso do Application Insights
No [Portal do Azure](https://portal.azure.com), crie um novo recurso do Application Insights. Escolha ASP.NET como o tipo de aplicação.

![Clicar em Propriedades, selecionar a chave e premir Ctrl+C](./media/app-insights-sharepoint/01-new.png)

O painel que se abre é o local onde verá os dados de utilização e de desempenho da sua aplicação. Para voltar à mesma da próxima vez em que iniciar sessão no Azure, deve encontrar um mosaico para o mesmo no ecrã Início. Em alternativa clique em Procurar para encontrá-lo.

## <a name="add-our-script-to-your-web-pages"></a>Adicione os nossos scripts às suas páginas Web
Em Início Rápido, obtenha o script para páginas Web:

![](./media/app-insights-sharepoint/02-monitor-web-page.png)

Insira o script imediatamente antes da etiqueta &lt;/cabeçalho&gt; de todas as páginas que quer controlar. Se o seu site tiver uma página mestra, pode colocar aí o script. Por exemplo, num projeto de MVC do ASP.NET, colocaria em View\Shared\_Layout.cshtml

O script contém a chave de instrumentação que direciona a telemetria para o recurso do Application Insights.

### <a name="add-the-code-to-your-site-pages"></a>Adicione o código às páginas do seu site
#### <a name="on-the-master-page"></a>Na página mestra
Se conseguir editar a página mestra no site, irá fornecer a monitorização para todas as páginas no site.

Confira a página mestra e edite-a através do SharePoint Designer ou qualquer outro editor.

![](./media/app-insights-sharepoint/03-master.png)

Adicione o código imediatamente antes da etiqueta </head>. 

![](./media/app-insights-sharepoint/04-code.png)

#### <a name="or-on-individual-pages"></a>Ou em páginas individuais
Para monitorizar um conjunto limitado de páginas, adicione o script em separado para cada página. 

Insira uma peça Web e incorpore o fragmento de código na mesma.

![](./media/app-insights-sharepoint/05-page.png)

## <a name="view-data-about-your-app"></a>Veja dados sobre a sua aplicação
Volte a implementar a aplicação.

Regresse ao seu painel da aplicação no [portal do Azure](https://portal.azure.com).

Os primeiros eventos irão aparecer na Pesquisa. 

![](./media/app-insights-sharepoint/09-search.png)

Clique em Atualizar após alguns segundos, se estiver à espera de mais dados.

No painel da descrição geral, clique em **Análise de utilização** para ver os gráficos de utilizadores, as sessões e as vistas da página:

![](./media/app-insights-sharepoint/06-usage.png)

Clique em qualquer gráfico para ver mais detalhes - por exemplo as Vistas de Página:

![](./media/app-insights-sharepoint/07-pages.png)

Ou Utilizadores:

![](./media/app-insights-sharepoint/08-users.png)

## <a name="capturing-user-id"></a>Capturar Id do Utilizador
O fragmento de código da página Web standard não captura o ID do utilizador do SharePoint, mas pode fazê-lo com uma pequena modificação.

1. Copie a chave de instrumentação a partir da lista pendente do Essentials no Application Insights. 

    ![](./media/app-insights-sharepoint/02-props.png)

1. Substitua a chave de instrumentação de "XXXX" no fragmento abaixo. 
2. Incorpore o script na sua aplicação do SharePoint, em vez do fragmento que obtém a partir do portal.

```


<SharePoint:ScriptLink ID="ScriptLink1" name="SP.js" runat="server" localizable="false" loadafterui="true" /> 
<SharePoint:ScriptLink ID="ScriptLink2" name="SP.UserProfiles.js" runat="server" localizable="false" loadafterui="true" /> 

<script type="text/javascript"> 
var personProperties; 

// Ensure that the SP.UserProfiles.js file is loaded before the custom code runs. 
SP.SOD.executeOrDelayUntilScriptLoaded(getUserProperties, 'SP.UserProfiles.js'); 

function getUserProperties() { 
    // Get the current client context and PeopleManager instance. 
    var clientContext = new SP.ClientContext.get_current(); 
    var peopleManager = new SP.UserProfiles.PeopleManager(clientContext); 

    // Get user properties for the target user. 
    // To get the PersonProperties object for the current user, use the 
    // getMyProperties method. 

    personProperties = peopleManager.getMyProperties(); 

    // Load the PersonProperties object and send the request. 
    clientContext.load(personProperties); 
    clientContext.executeQueryAsync(onRequestSuccess, onRequestFail); 
} 

// This function runs if the executeQueryAsync call succeeds. 
function onRequestSuccess() { 
var appInsights=window.appInsights||function(config){
function s(config){t[config]=function(){var i=arguments;t.queue.push(function(){t[config].apply(t,i)})}}var t={config:config},r=document,f=window,e="script",o=r.createElement(e),i,u;for(o.src=config.url||"//az416426.vo.msecnd.net/scripts/a/ai.0.js",r.getElementsByTagName(e)[0].parentNode.appendChild(o),t.cookie=r.cookie,t.queue=[],i=["Event","Exception","Metric","PageView","Trace"];i.length;)s("track"+i.pop());return config.disableExceptionTracking||(i="onerror",s("_"+i),u=f[i],f[i]=function(config,r,f,e,o){var s=u&&u(config,r,f,e,o);return s!==!0&&t["_"+i](config,r,f,e,o),s}),t
    }({
        instrumentationKey:"XXXX"
    });
    window.appInsights=appInsights;
    appInsights.trackPageView(document.title,window.location.href, {User: personProperties.get_displayName()});
} 

// This function runs if the executeQueryAsync call fails. 
function onRequestFail(sender, args) { 
} 
</script> 


```



## <a name="next-steps"></a>Passos Seguintes
* [Testes Web](app-insights-monitor-web-app-availability.md) para monitorizar a disponibilidade do seu site.
* [Application Insights](app-insights-overview.md) para outros tipos de aplicação.

<!--Link references-->



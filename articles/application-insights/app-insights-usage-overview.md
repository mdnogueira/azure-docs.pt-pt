---
title: "Análise de utilização com o Azure Application Insights | Documentos da Microsoft"
description: "Compreenda os seus utilizadores e o que fazer com a sua aplicação."
services: application-insights
documentationcenter: 
author: botatoes
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 10/10/2017
ms.author: mbullwin
ms.openlocfilehash: 4a6647e30657a6d2d076cd254069d96f99a0aa60
ms.sourcegitcommit: c25cf136aab5f082caaf93d598df78dc23e327b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/15/2017
---
# <a name="usage-analysis-with-application-insights"></a>Análise de utilização com o Application Insights

As funcionalidades da web ou aplicação móvel são mais populares? Os seus utilizadores alcançar os seus objetivos com a sua aplicação? De remover estes em determinado pontos e volte mais tarde?  [Azure Application Insights](app-insights-overview.md) ajuda-o a obter informações acerca poderosas como as pessoas utilizam a sua aplicação. Sempre que atualizar a sua aplicação, pode avaliar o quão bem funciona para os utilizadores. Com este conhecimento, pode efetuar dados orientada por tomar decisões sobre a sua próximas ciclos de desenvolvimento.

## <a name="send-telemetry-from-your-app"></a>Enviar a telemetria da sua aplicação

A melhor experiência é obtida pela instalação do Application Insights no seu código de servidor de aplicação tanto nas suas páginas web. Os componentes de cliente e o servidor da sua aplicação enviam telemetria ao portal do Azure para análise.

1. **Código do servidor:** instalar o módulo adequado para o [ASP.NET](app-insights-asp-net.md), [Azure](app-insights-azure.md), [Java](app-insights-java-get-started.md), [Node.js](app-insights-nodejs.md), ou [outros](app-insights-platforms.md) aplicação.

    * *Não pretende instalar o código do servidor? Apenas [crie um recurso do Azure Application Insights](app-insights-create-new-resource.md).*

2. **Código de página Web:** abrir o [portal do Azure](https://portal.azure.com), abra o recurso do Application Insights para a sua aplicação e, em seguida, abra **introdução > monitorizar e diagnosticar do lado do cliente**. 

    ![Copie o script para o cabeçalho da sua página web mestra.](./media/app-insights-usage-overview/02-monitor-web-page.png)

3. **Código de aplicação móvel:** utilizar o SDK da aplicação do System Center para recolher eventos a partir da sua aplicação, em seguida, envie cópias destes eventos para o Application Insights para análise pelo [seguir este guia](app-insights-mobile-center-quickstart.md).

4. **Obter telemetria:** executar o projeto no modo de depuração para alguns minutos e, em seguida, procure os resultados no painel de descrição geral no Application Insights.

    Publica a aplicação para monitorizar o desempenho da aplicação e saber que os utilizadores estão a fazer com a sua aplicação.

## <a name="include-user-and-session-id-in-your-telemetry"></a>Inclui o ID de utilizador e de sessão na sua telemetria
Para monitorizar os utilizadores ao longo do tempo, o Application Insights requer uma forma para identificá-las. A ferramenta de eventos é a única ferramenta de utilização que não requer um ID de utilizador ou um ID de sessão.

Começar a enviar de utilizador e os IDs de sessão utilizando [este processo](https://docs.microsoft.com/azure/application-insights/app-insights-usage-send-user-context).

## <a name="explore-usage-demographics-and-statistics"></a>Explorar demografia de utilização e estatísticas
Determinar quando as pessoas utilizam a sua aplicação, as páginas que está a mais interessados, onde estão localizados os seus utilizadores, que sistemas operativos e browsers utilizam. 

Os relatórios de utilizadores e sessões filtre os dados por páginas ou eventos personalizados e segmentá-los por propriedades, tais como a localização e o ambiente e página. Também pode adicionar os seus próprios filtros.

![Utilizadores](./media/app-insights-usage-overview/users.png)  

Insights à direita do ponto de saída padrões interessantes no conjunto de dados.  

* O **utilizadores** relatório conta os números de utilizadores exclusivos que aceder às suas páginas na sua períodos de tempo escolhido. Para aplicações web, os utilizadores são contados através da utilização de cookies. Se alguém acede ao seu site com as máquinas de cliente ou browsers diferentes ou limpa os cookies, em seguida, estes serão contados tendo mais do que uma vez.
* O **sessões** relatório conta o número de sessões de utilizador aceder ao site. Uma sessão é um período de atividade por um utilizador, terminada por um período de inatividade de mais de metade uma hora.

[Mais informações sobre as ferramentas de utilizadores, sessões e os eventos](app-insights-usage-segmentation.md)  

## <a name="page-views"></a>Vistas de página

No painel de utilização, clique no mosaico de vistas de página para obter uma análise detalhada das suas páginas mais populares:

![No painel de descrição geral, clique o gráfico de vistas de página](./media/app-insights-usage-overview/05-games.png)

O exemplo acima é de um web site de jogos. De gráficos, pode de forma instantânea, vemos:

* Utilização não melhorado da semana passada. Talvez é necessário pensar sobre a otimização de motor de pesquisa?
* Tennis é a página de jogo mais popular. Vamos concentrar-se em mais melhorias para esta página.
* Em média, os utilizadores visitam a página de Tennis a cerca de três vezes por semana. (Existem mais de cerca de três vezes sessões que os utilizadores.)
* A maioria dos utilizadores visitam o site durante a semana do trabalho E.U.A. e, em horas de expediente. Talvez deve fornecemos um botão "Ocultar rápido" na página web.
* O [anotações](app-insights-annotations.md) no gráfico mostram quando foram implementadas novas versões do Web site. Nenhuma das implementações recentes tinha um efeito considerável na utilização.

E se pretende investigar o tráfego do site em mais detalhe, como dividir por uma propriedade personalizada, que o site envia na sua telemetria de visualizações de página?

1. Abra o **eventos** ferramenta no menu de recurso do Application Insights. Esta ferramenta permite-lhe analisar quantos vistas de página e eventos personalizados foram enviados da sua aplicação, com base numa variedade de opções de filtragem, cohorting e segmentação.
2. Na lista pendente "Quem utilizada", selecione "Qualquer vista de página".
3. Na lista pendente "Dividir por", selecione uma propriedade pelo qual dividir a sua telemetria de visualizações de página.

## <a name="retention---how-many-users-come-back"></a>Retenção - quantos utilizadores voltar?

Retenção ajuda-o a compreender com que frequência os seus utilizadores regressar ao utilizar a sua aplicação, com base no cohorts de utilizadores que efetuar qualquer ação do negócio durante um determinado registo de tempo. 

- Compreender as funcionalidades específicas fazer com que os utilizadores fique voltar mais do que outras pessoas 
- Hypotheses de formulário com base nos dados de utilizador reais 
- Determinar se o período de retenção é um problema no seu produto 

![Retenção](./media/app-insights-usage-overview/retention.png) 

Os controlos de retenção na parte superior permitem-lhe definir o intervalo de tempo para calcular a retenção e eventos específicos. O gráfico no meio proporciona uma representação visual de percentagem de retenção global, o intervalo de tempo especificado. O gráfico na parte inferior representa retenção individuais num determinado período de tempo. Este nível de detalhe permite-lhe compreender de que os utilizadores estão a fazer e o que poderá afetar devolvidos utilizadores uma granularidade mais detalhada.  

[Mais informações sobre a ferramenta de retenção](app-insights-usage-retention.md)

## <a name="custom-business-events"></a>Eventos de negócio personalizado

Para obter uma compreensão clara dos que utilizadores fazem com a sua aplicação, é útil inserir linhas de código para registar eventos personalizados. Estes eventos podem controlar tudo de ações de utilizador de detalhado como clicar botões específicos, a eventos empresariais mais significativos, tais como efetuar uma compra ou prevalecente um jogo. 

Embora em alguns casos, as vistas de página podem representar eventos úteis, não se encontra verdadeiro em geral. Um utilizador pode abrir uma página de produto, sem comprar o produto. 

Com eventos empresariais específicos, pode gráfico progresso dos seus utilizadores através do seu site. Pode saber as respetivas preferências para diferentes opções e em que o se remover out ou tem dificuldades. Com este conhecimento, pode efetuar decisões informadas sobre as prioridades no seu registo de segurança de desenvolvimento.

Os eventos podem ser registados no lado do cliente da aplicação:

```JavaScript

    appInsights.trackEvent("ExpandDetailTab", {DetailTab: tabName});
```

Ou a partir do lado do servidor:

```C#
    var tc = new Microsoft.ApplicationInsights.TelemetryClient();
    tc.TrackEvent("CreatedAccount", new Dictionary<string,string> {"AccountType":account.Type}, null);
    ...
    tc.TrackEvent("AddedItemToCart", new Dictionary<string,string> {"Item":item.Name}, null);
    ...
    tc.TrackEvent("CompletedPurchase");
```

Pode anexar os valores de propriedade para estes eventos, para que possa filtrar ou dividir os eventos quando inspecioná-las no portal. Além disso, um conjunto de propriedades padrão está anexado a cada evento, tal como ID de utilizador anónimo, que permite-lhe rastrear a sequência de atividades de um utilizador individual.

Saiba mais sobre [eventos personalizados](app-insights-api-custom-events-metrics.md#trackevent) e [propriedades](app-insights-api-custom-events-metrics.md#properties).

### <a name="slice-and-dice-events"></a>Eventos dividem e organizam

As ferramentas de utilizadores, sessões e os eventos, pode segmentação e decomposição de eventos personalizados por utilizador, o nome de evento e propriedades.
![Utilizadores](./media/app-insights-usage-overview/users.png)  
  
## <a name="design-the-telemetry-with-the-app"></a>Estruturar a telemetria com a aplicação

Ao conceber a cada funcionalidade da sua aplicação, considere como vai para medir o sucesso com os seus utilizadores. Decida que eventos de negócio é necessário registar bem como código de controlo de chamadas para esses eventos na sua aplicação desde o início.

## <a name="a--b-testing"></a>A | Testar B
Se não souber que a variante de uma funcionalidade que será mais bem sucedida, versão ambas, efetuar cada acessível para diferentes utilizadores. Medir o sucesso de cada e, em seguida, mova para uma versão unificada.

Para esta técnica, anexe os valores de propriedade de diferentes para a telemetria enviada por cada versão da sua aplicação. Pode fazê-lo ao definir propriedades na TelemetryContext Active Directory. Estas propriedades predefinidas são adicionadas a cada mensagem de telemetria que a aplicação envia - não apenas as mensagens personalizadas, mas a telemetria de padrão.

No portal do Application Insights, filtrar e dividir os dados nos valores de propriedade, de modo a comparar as diversas versões.

Para tal, [configurar um inicializador de telemetria](app-insights-api-filtering-sampling.md##add-properties-itelemetryinitializer):

```C#


    // Telemetry initializer class
    public class MyTelemetryInitializer : ITelemetryInitializer
    {
        public void Initialize (ITelemetry telemetry)
        {
            telemetry.Properties["AppVersion"] = "v2.1";
        }
    }
```

No inicializador de aplicação web, tais como Global.asax.cs:

```C#

    protected void Application_Start()
    {
        // ...
        TelemetryConfiguration.Active.TelemetryInitializers
        .Add(new MyTelemetryInitializer());
    }
```

Todos os TelemetryClients novo adicionam automaticamente o valor da propriedade que especificar. Eventos de telemetria individuais podem substituir os valores predefinidos.

## <a name="next-steps"></a>Passos seguintes
   - [Utilizadores, Sessões, Eventos](app-insights-usage-segmentation.md)
   - [Funis](usage-funnels.md)
   - [Retenção](app-insights-usage-retention.md)
   - [Fluxos do Utilizador](app-insights-usage-flows.md)
   - [Livros](app-insights-usage-workbooks.md)
   - [Adicionar o contexto de utilizador](app-insights-usage-send-user-context.md)

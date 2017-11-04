---
title: Definir alertas no Azure Application Insights | Microsoft Docs
description: "Seja notificado sobre tempos de resposta lento, exceções e outros desempenho ou alterações de utilização na sua aplicação web."
services: application-insights
documentationcenter: 
author: mrbullwinkle
manager: carmonm
ms.assetid: f8ebde72-f819-4ba5-afa2-31dbd49509a5
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 03/14/2017
ms.author: mbullwin
ms.openlocfilehash: b0e4828b2cb6bbcb4329381e77db3b187d659706
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/01/2017
---
# <a name="set-alerts-in-application-insights"></a>Definir alertas no Application Insights
[Azure Application Insights] [ start] podem alertá-lo para as alterações nas métricas de desempenho ou a utilização na sua aplicação web. 

Application Insights monitoriza a sua aplicação em direto num [ampla variedade de plataformas] [ platforms] para ajudar a diagnosticar problemas de desempenho e compreender os padrões de utilização.

Existem três tipos de alertas:

* **Alertas métricas** indicam quando uma métrica atravesse um valor de limiar para um determinado período - como tempos de resposta, contagens de exceção, utilização da CPU ou vistas de página. 
* [**Testes Web** ] [ availability] indicam quando o site estiver disponível na internet, ou está a responder lentamente. [Saiba mais][availability].
* [**O diagnóstico** ](app-insights-proactive-diagnostics.md) são automaticamente configurados para notificá-lo sobre os padrões de desempenho de atividade invulgar.

Iremos focar-se de alertas métricas neste artigo.

## <a name="set-a-metric-alert"></a>Definir um alerta de métrico
Abra o painel de regras de alertas e, em seguida, utilize o botão de adição. 

![No painel regras de alertas, escolha Adicionar alerta. Defina a sua aplicação como o recurso para medir, forneça um nome para o alerta e escolha uma métrica.](./media/app-insights-alerts/01-set-metric.png)

* Defina o recurso antes das outras propriedades. **Escolha o recurso "(componentes)"** se pretender definir alertas nas métricas de desempenho ou utilização.
* O nome que irá fornecer o alerta tem de ser exclusivo dentro do grupo de recursos (não apenas a aplicação).
* Tenha o cuidado de tenha em atenção as unidades nas quais está a pedido para introduzir o valor de limiar.
* Se selecionar a caixa "… proprietários de E-Mail", os alertas são enviados por e-mail para todas as pessoas que tem acesso a este grupo de recursos. Para expandir este conjunto de pessoas, adicioná-los para o [grupo de recursos ou subscrição](app-insights-resources-roles-access-control.md) (não o recurso).
* Se especificar "Adicionais mensagens de correio eletrónico", os alertas são enviados para essas pessoas ou grupos (ou não tiver selecionado a caixa "… proprietários de e-mail"). 
* Definir um [webhook endereço](../monitoring-and-diagnostics/insights-webhooks-alerts.md) se tiver configurado uma aplicação web que responde a alertas. É denominado quando o alerta é ativado e quando for resolvida. (Mas tenha em atenção que neste momento, os parâmetros de consulta não são submetidos como propriedades de webhook).
* Pode desativar ou ativar o alerta: consulte os botões na parte superior do painel.

*Não consigo ver o botão Adicionar alerta.* 

* Está a utilizar uma conta institucional? Pode definir alertas se tiver o proprietário ou contribuinte aceder a este recurso de aplicação. Observe o painel de controlo de acesso. [Saiba mais sobre o controlo de acesso][roles].

> [!NOTE]
> No painel de alertas, verá que já há um conjunto de alerta cópias de segurança: [diagnóstico](app-insights-proactive-failure-diagnostics.md). O alerta automático monitoriza um determinado métrica, pedido taxa de falhas. Exceto se optar por desativar o alerta proativo, não precisa de definir o seus próprios alerta de taxa de falhas de pedido. 
> 
> 

## <a name="see-your-alerts"></a>Consulte os alertas
Obter um e-mail quando um alerta muda de estado entre inativos e Active Directory. 

O estado atual de cada alerta é apresentado no painel de regras de alertas.

Há um resumo da atividade recente dos alertas de lista pendente:

![Lista de alertas pendente](./media/app-insights-alerts/010-alert-drop.png)

Se o histórico de alterações de estado no registo de atividade:

![No painel de descrição geral, clique em definições, os registos de auditoria](./media/app-insights-alerts/09-alerts.png)

## <a name="how-alerts-work"></a>Como funcionam os alertas
* Um alerta tem três Estados: "Nunca ativado", "Ativado" e "Resolvido". Significa ativada a condição que especificou foi for verdadeira, quando foi avaliado pela última vez.
* Uma notificação é gerada quando um alerta muda de estado. (Se a condição de alerta já foi verdadeira quando criou o alerta, poderá não receberá uma notificação até que a condição for false.)
* Cada notificação gera uma mensagem de e-mail, se selecionado a caixa de correio eletrónico ou fornecidos endereços de correio eletrónico. Também pode ver a notificações na lista pendente.
* Um alerta é avaliado sempre que uma métrica de chega, mas não, caso contrário.
* A avaliação agrega a métrica durante o período anterior e, em seguida, compara-o limiar para determinar o estado de novo.
* O período que escolher Especifica o intervalo durante o qual são agregadas métricas. Não afeta a frequência de avaliação do alerta: que depende da frequência de chegada de métricas.
* Se não existem dados chegam para uma determinada métrica durante algum tempo, o intervalo tem efeitos diferentes avaliação de alerta e os gráficos no Explorador de métrica. No Explorador de métrica, se não existem dados são utilizados durante um período superior intervalo de amostragem do gráfico, o gráfico mostra um valor de 0. Mas um alerta com base na mesma métrica não é possível reavaliadas, e o estado deste alerta permanece inalterado. 
  
    Quando os dados são recebidos eventualmente, o gráfico salta volta para um valor diferente de zero. Avalia o alerta com base nos dados disponíveis para o período especificado. Se o novo ponto de dados é a única disponível no período, a agregação baseia-se apenas no ponto de dados.
* Um alerta pode flicker frequentemente entre os Estados de bom estado de funcionamento e alertas, mesmo se definir um período de tempo. Isto pode acontecer se o valor métrico passa em torno do limiar. Não há nenhum hysteresis com o limiar: a transição para o alerta ocorre ao mesmo valor que a transição para o bom estado de funcionamento.

## <a name="what-are-good-alerts-to-set"></a>O que são alertas boas definir?
Depende da aplicação. Para começar, é melhor não definir métricas demasiados. Demora algum tempo a observar os métricos gráficos enquanto a aplicação está em execução, para obter uma funcionalidade de como esta se comporta normalmente. Esta prática ajuda-o a encontrar formas de melhorar o desempenho dele. Em seguida, configure alertas para lhe indicar quando as métricas enviados para fora da zona normal. 

Alertas populares incluem:

* [Métricas de browser][client], especialmente Browser **tempos de carregamento de página**, está pronto para aplicações web. Se a sua página tiver muitos scripts, deve procurar **exceções de browser**. Para obter estas métricas e alertas, tem de configurar [monitorização da página web][client].
* **Tempo de resposta do servidor** no lado do servidor de aplicações web. Bem como configurar alertas, atento esta métrica para ver se disproportionately varia com taxas de pedidos elevada: variação pode indicar que a aplicação está a ficar sem recursos. 
* **Exceções de servidor** - para vê-los, tem de fazer alguns [configuração adicional](app-insights-asp-net-exceptions.md).

Não se esqueça de que [diagnósticos de taxa de falhas proativa](app-insights-proactive-failure-diagnostics.md) monitorizar automaticamente a velocidade a que a aplicação responde a pedidos com códigos de falha. 

## <a name="automation"></a>Automatização
* [Utilize o PowerShell para automatizar a configuração de alertas](app-insights-powershell-alerts.md)
* [Utilizar webhooks para automatizar a responder a alertas](../monitoring-and-diagnostics/insights-webhooks-alerts.md)

## <a name="video"></a>Vídeo

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player]

## <a name="see-also"></a>Consultar também
* [Testes web de disponibilidade](app-insights-monitor-web-app-availability.md)
* [Automatizar a configuração de alertas](app-insights-powershell-alerts.md)
* [Diagnóstico](app-insights-proactive-diagnostics.md) 

<!--Link references-->

[availability]: app-insights-monitor-web-app-availability.md
[client]: app-insights-javascript.md
[platforms]: app-insights-platforms.md
[roles]: app-insights-resources-roles-access-control.md
[start]: app-insights-overview.md


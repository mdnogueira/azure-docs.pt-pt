---
title: "Integração do SCOM com o Application Insights | Microsoft Docs"
description: "Se a um utilizador do SCOM, monitorizar o desempenho e diagnosticar problemas com o Application Insights. Dashboards abrangentes, alertas inteligentes, ferramentas de diagnóstico de elevado desempenho e consultas de análise."
services: application-insights
documentationcenter: 
author: mrbullwinkle
manager: carmonm
ms.assetid: 606e9d03-c0e6-4a77-80e8-61b75efacde0
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 08/12/2016
ms.author: mbullwin
ms.openlocfilehash: 35ea37b751909e14e616a965462b832e4e51bae0
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/01/2017
---
# <a name="application-performance-monitoring-using-application-insights-for-scom"></a>Monitorização de Desempenho de Aplicações com o Application Insights para SCOM
Se utilizar o System Center Operations Manager (SCOM) para gerir os servidores, pode monitorizar o desempenho e diagnosticar problemas de desempenho com a ajuda do [Azure Application Insights](app-insights-asp-net.md). Application Insights monitoriza os pedidos recebidos da sua aplicação web, REST e chamadas do SQL Server, exceções e rastreios de registo de saída. Fornece dashboards com gráficos métricos e inteligentes alertas, bem como poderosa pesquisa de diagnóstico e consultas analíticas sobre esta telemetria. 

Pode mudar na monitorização do Application Insights, utilizando um pacote de gestão do SCOM.

## <a name="before-you-start"></a>Antes de começar
Presumimos:

* Servidores web estiver familiarizado com o SCOM e de que utiliza o SCOM 2012 R2 ou 2016 para gerir o IIS.
* Já instalou nos seus servidores de uma aplicação web que pretende monitorizar com o Application Insights.
* Versão de estrutura de aplicação é .NET 4.5 ou posterior.
* Tem acesso a uma subscrição no [Microsoft Azure](https://azure.com) e pode iniciar sessão para o [portal do Azure](https://portal.azure.com). A organização pode ter uma subscrição e pode adicionar a sua conta Microsoft ao mesmo.

(A equipa de desenvolvimento pode compilar o [Application Insights SDK](app-insights-asp-net.md) para a aplicação web. Este instrumentação de tempo de compilação fornece-lhes uma maior flexibilidade na escrever telemetria personalizada. No entanto, é irrelevante: pode seguir os passos descritos aqui com ou sem o SDK incorporada.)

## <a name="one-time-install-application-insights-management-pack"></a>(Uma vez) Instalar o pacote de gestão do Application Insights
Na máquina em que executar o Operations Manager:

1. Desinstale qualquer versão antiga do pacote de gestão:
   1. No Operations Manager, abra a administração, pacotes de gestão. 
   2. Elimine a versão antiga.
2. Transfira e instale o pacote de gestão a partir do catálogo.
3. Reinicie o Operations Manager.

## <a name="create-a-management-pack"></a>Criar um pacote de gestão
1. No Operations Manager, abra **criação**, **.NET... com o Application Insights**, **Adicionar Assistente de monitorização**e selecione novamente **.NET... com o Application Insights**.
   
    ![](./media/app-insights-scom/020.png)
2. Nome da configuração após a sua aplicação. (Tem de instrumentar uma aplicação numa altura.)
   
    ![](./media/app-insights-scom/030.png)
3. Na mesma página do assistente, crie um novo pacote de gestão ou selecione um pacote que criou anteriormente para o Application Insights.
   
     (O Application Insights [pacote de gestão](https://technet.microsoft.com/library/cc974491.aspx) é um modelo, a partir da qual criar uma instância. Pode reutilizar a mesma instância mais tarde.)

    ![No separador de propriedades gerais, escreva o nome da aplicação. Clique em novo e escreva um nome para um pacote de gestão. Clique em OK e, em seguida, clique em seguinte.](./media/app-insights-scom/040.png)

1. Escolha uma aplicação que pretende monitorizar. A funcionalidade de pesquisa procura entre as aplicações instaladas nos seus servidores.
   
    ![Que separador Monitor, clique em Adicionar, escreva a parte do nome da aplicação, clique em Procurar, escolha a aplicação e, em seguida, adicionar, OK.](./media/app-insights-scom/050.png)
   
    O campo de âmbito de monitorização opcional pode ser utilizado para especificar um subconjunto de servidores, se não pretender monitorizar a aplicação em todos os servidores.
2. Na página seguinte do assistente, primeiro tem de fornecer as suas credenciais para iniciar sessão no Microsoft Azure.
   
    Nesta página, escolha o recurso do Application Insights onde pretende que os dados de telemetria a ser analisada e apresentada. 
   
   * Se a aplicação foi configurada para o Application Insights durante o desenvolvimento, selecione o recurso existente.
   * Caso contrário, crie um novo recurso com o nome da aplicação. Se existirem outras aplicações que são componentes do mesmo sistema, colocá-los no mesmo grupo de recursos, para que o acesso à telemetria fáceis de gerir.
     
     Pode alterar estas definições mais tarde.
     
     ![No separador de definições do Application Insights, clique em 'Iniciar sessão' e forneça as credenciais da conta Microsoft para o Azure. Em seguida, escolha uma subscrição, o grupo de recursos e recursos.](./media/app-insights-scom/060.png)
3. Conclua o assistente.
   
    ![Clique em Criar](./media/app-insights-scom/070.png)

Repita este procedimento para cada aplicação que pretende monitorizar.

Se precisar de alterar as definições mais tarde, volte a abrir as propriedades do monitor na janela de criação.

![Na criação de conteúdos, selecione monitorização de desempenho de aplicações do .NET com o Application Insights, selecione o seu monitor e clique em propriedades.](./media/app-insights-scom/080.png)

## <a name="verify-monitoring"></a>Certifique-se de monitorização
O monitor que tem instalado procura para a sua aplicação em cada servidor. Onde encontrar a aplicação, este configura o Monitor de estado do Application Insights para monitorizar a aplicação. Se necessário, primeiro instala o Monitor de estado no servidor.

Pode verificar que instâncias da aplicação tem possível encontrá-lo:

![Na monitorização, abrir o Application Insights](./media/app-insights-scom/100.png)

## <a name="view-telemetry-in-application-insights"></a>Ver a telemetria no Application Insights
No [portal do Azure](https://portal.azure.com), navegue para o recurso para a sua aplicação. [Ver gráficos que mostra a telemetria](app-insights-dashboards.md) da sua aplicação. (Se ele não apresentado cópias de segurança na página principal ainda, clique em fluxo em direto de métricas.)

## <a name="next-steps"></a>Passos seguintes
* [Configurar um dashboard](app-insights-dashboards.md) para reunir os gráficos mais importantes monitorização esta e outras aplicações.
* [Saiba mais sobre as métricas](app-insights-metrics-explorer.md)
* [Configurar alertas](app-insights-alerts.md)
* [Diagnosticar problemas de desempenho](app-insights-detect-triage-diagnose.md)
* [Consultas de análises poderosas](app-insights-analytics.md)
* [Testes web de disponibilidade](app-insights-monitor-web-app-availability.md)


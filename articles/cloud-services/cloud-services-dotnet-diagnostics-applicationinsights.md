---
title: "Resolver problemas de serviços em nuvem com o Application Insights | Microsoft Docs"
description: "Saiba como resolver problemas de serviço de nuvem, utilizando o Application Insights para processar dados de diagnóstico do Azure."
services: cloud-services
documentationcenter: .net
author: sbtron
manager: timlt
editor: tysonn
ms.assetid: e93f387b-ef29-4731-ae41-0676722accb6
ms.service: cloud-services
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/23/2017
ms.author: saurabh
ms.openlocfilehash: 4001ca908ff00b1a40829d687589080e9b07b18a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshoot-cloud-services-using-application-insights"></a>Resolver problemas de serviços em nuvem com o Application Insights
Com [Azure SDK 2.8](https://azure.microsoft.com/downloads/) e 1.5 de extensão de diagnóstico do Azure, pode enviar dados de diagnóstico do Azure para o seu serviço de nuvem diretamente para o Application Insights. Os registos recolhidos pelo Azure Diagnostics&mdash;, incluindo os registos de aplicações, os registos de eventos do Windows, os registos de ETW e contadores de desempenho&mdash;podem ser enviadas para o Application Insights. Em seguida, pode visualizar estas informações no portal do Application Insights da IU. Em seguida, pode utilizar o Application Insights SDK para obter informações sobre as métricas e registos que vêm da sua aplicação, bem como o sistema e ao nível da infraestrutura de dados de diagnóstico do Azure.

## <a name="configure-azure-diagnostics-to-send-data-to-application-insights"></a>Configurar o diagnóstico do Azure para enviar dados para o Application Insights
Siga estes passos para configurar o projeto de serviço em nuvem para enviar dados de diagnóstico do Azure ao Application Insights.

1. No Explorador de soluções do Visual Studio, uma função com o botão direito e selecione **propriedades** para abrir o estruturador de função.

    ![Propriedades de função do Explorador de soluções][1]

2. No **diagnóstico** secção do designer de função, selecione o **enviar dados de diagnóstico para o Application Insights** opção.

    ![Designer de função enviar dados de diagnóstico para o application insights][2]

3. Na caixa de diálogo que aparece, selecione o recurso do Application Insights que pretende enviar os dados de diagnóstico do Azure. A caixa de diálogo permite-lhe para selecionar um recurso do Application Insights existente da sua subscrição ou especifique manualmente uma chave de instrumentação para um recurso do Application Insights. Para obter mais informações sobre como criar um recurso do Application Insights, consulte [criar um novo recurso do Application Insights](../application-insights/app-insights-create-new-resource.md).

    ![Selecione o recurso do application insights][3]

    Depois de adicionar o recurso do Application Insights, a chave de instrumentação para esse recurso é armazenada como uma definição de configuração de serviço com o nome **APPINSIGHTS_INSTRUMENTATIONKEY**. Pode alterar esta definição de configuração para cada ambiente ou configuração de serviço. Para tal, selecione uma configuração diferente do **a configuração do serviço** lista e especifique uma nova chave de instrumentação para que a configuração.

    ![Seleccione a configuração do serviço][4]

    O **APPINSIGHTS_INSTRUMENTATIONKEY** definição de configuração é utilizada pelo Visual Studio para configurar a extensão de diagnóstico com as informações de recurso do Application Insights adequadas durante a publicação. A definição de configuração é uma forma conveniente de definir as chaves de instrumentação diferentes para configurações de serviço diferente. Visual Studio irá traduzir essa definição e insira-o para a configuração pública de extensão de diagnóstico durante o processo de publicação. Para simplificar o processo de configuração a extensão de diagnóstico com o PowerShell, o resultado de pacote do Visual Studio também contém a configuração pública de XML com a chave de instrumentação do Application Insights adequada. Os ficheiros de configuração pública são criados na pasta de extensões e seguir o padrão *PaaSDiagnostics.&lt; RoleName&gt;. PubConfig.xml*. Todas as implementações baseadas no PowerShell podem utilizar este padrão para mapear cada configuração para uma função.

4) Para configurar o diagnóstico do Azure para enviar todos os registos de nível de erro recolhidos pelo agente de diagnóstico do Azure ao Application Insights e contadores de desempenho, ative o **enviar dados de diagnóstico para o Application Insights** opção. 

    Se quiser configurar ainda mais os dados que é enviada para o Application Insights, tem de editar manualmente o *diagnostics.wadcfgx* ficheiros para cada função. Consulte [configurar diagnósticos do Azure para enviar dados para o Application Insights](#configure-azure-diagnostics-to-send-data-to-application-insights) para obter mais informações sobre como atualizar manualmente a configuração.

Quando o serviço em nuvem está configurado para enviar dados de diagnóstico do Azure ao application insights, pode implementá-lo para o Azure normalmente, certificar-se de que a extensão de diagnóstico do Azure está ativa. Para obter mais informações, consulte [publicação de um serviço em nuvem com o Visual Studio](../vs-azure-tools-publishing-a-cloud-service.md).  

## <a name="viewing-azure-diagnostics-data-in-application-insights"></a>Visualizar os dados de diagnóstico do Azure no Application Insights
A telemetria de diagnóstico do Azure que aparece no recurso do Application Insights configurado para o seu serviço de nuvem.

Mapa de tipos de registo de diagnóstico do Azure para conceitos do Application Insights nas seguintes formas:

* Contadores de desempenho são apresentados como métricas personalizadas no Application Insights.
* Registos de eventos do Windows são apresentadas como rastreios e eventos personalizados no Application Insights.
* Registos de aplicação, registos ETW e quaisquer registos de diagnóstico de infraestrutura são apresentados como os rastreios no Application Insights.

Para ver os dados de diagnóstico do Azure no Application Insights, efetue um dos seguintes:

* Utilize [Explorador de métricas](../application-insights/app-insights-metrics-explorer.md) para visualizar todos os contadores de desempenho personalizado ou contagens de diferentes tipos de eventos de registo de eventos do Windows.

    ![Métricas personalizadas no Explorador de métricas][5]

* Utilize [pesquisa](../application-insights/app-insights-diagnostic-search.md) para procurar em todos os registos de rastreio enviados pelo diagnósticos do Azure. Por exemplo, se uma exceção não processada provocou a função de falhas e recicle, informações sobre a exceção aparecem no *aplicação* canal do *registo de eventos do Windows*. Pode utilizar a procura para ver o erro do registo de eventos do Windows e obter o rastreio da pilha completa para a exceção para o ajudar a descobrir a causa do problema.

    ![Rastreios de pesquisa][6]

## <a name="next-steps"></a>Passos Seguintes
* [Adicionar o Application Insights SDK ao seu serviço de nuvem](../application-insights/app-insights-cloudservices.md) para enviar dados sobre pedidos, exceções, dependências e qualquer telemetria personalizada da sua aplicação. Quando combinada com os dados de diagnóstico do Azure, esta informação pode obter uma perspetiva completa da sua aplicação e o sistema, todos na mesmo recurso do Application Insight.  

<!--Image references-->
[1]: ./media/cloud-services-dotnet-diagnostics-applicationinsights/solution-explorer-properties.png
[2]: ./media/cloud-services-dotnet-diagnostics-applicationinsights/role-designer-sendtoappinsights.png
[3]: ./media/cloud-services-dotnet-diagnostics-applicationinsights/select-appinsights-resource.png
[4]: ./media/cloud-services-dotnet-diagnostics-applicationinsights/role-designer-appinsights-serviceconfig.png
[5]: ./media/cloud-services-dotnet-diagnostics-applicationinsights/metrics-explorer-custom-metrics.png
[6]: ./media/cloud-services-dotnet-diagnostics-applicationinsights/search-windowseventlog-error.png

---
title: Enviar alertas do Azure Application Insights | Microsoft Docs
description: "Tutorial para enviar alertas em resposta a erros na sua aplicação com o Azure Application Insights."
keywords: 
author: mrbullwinkle
ms.author: mbullwin
ms.date: 09/20/2017
ms.service: application-insights
ms.custom: mvc
ms.topic: tutorial
manager: carmonm
ms.openlocfilehash: 39e2f136e30ebb6dcfc003c435382f3384af1052
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/01/2017
---
# <a name="monitor-and-alert-on-application-health-with-azure-application-insights"></a>Monitor e alerta no estado de funcionamento de aplicação com o Azure Application Insights

Azure Application Insights permite-lhe monitorizar a sua aplicação e enviar-lhe alertas quando está indisponível, estão a ocorrer falhas ou sofrer de problemas de desempenho.  Este tutorial orienta-o processo de criação de testes para verificar continuamente a disponibilidade da aplicação e enviar diferentes tipos de alertas em resposta a detetou problemas.  Saiba como:

> [!div class="checklist"]
> * Criar o teste de disponibilidade para verificar continuamente a resposta da aplicação
> * Enviar correio eletrónico para os administradores quando ocorre um problema
> * Criar alertas com base nas métricas de desempenho 
> * Utilize uma aplicação lógica para enviar telemetria resumida com base numa agenda.


## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial:

- Instale o [2017 do Visual Studio](https://www.visualstudio.com/downloads/) com as seguintes cargas de trabalho:
    - Desenvolvimento ASP.NET e Web
    - Desenvolvimento do Azure
    - Implementar uma aplicação .NET do Azure e [ativar o Application Insights SDK](app-insights-asp-net.md). 


## <a name="log-in-to-azure"></a>Iniciar sessão no Azure
Inicie sessão no portal do Azure em [https://portal.azure.com](https://portal.azure.com).

## <a name="create-availability-test"></a>Criar o teste de disponibilidade
Testes de disponibilidade no Application Insights permitem-lhe automaticamente testar a aplicação a partir de várias localizações em todo o mundo.   Neste tutorial, irá executar um teste simple para garantir que a aplicação está disponível.  Pode também criar instruções completas para testar a sua operação detalhada. 

1. Selecione **Application Insights** e, em seguida, selecione a sua subscrição.  
1. Selecione **disponibilidade** sob o **investigar** menu e, em seguida, clique em **adicionar teste**.
 
    ![Adicionar o teste de disponibilidade](media/app-insights-tutorial-alert/add-test.png)

2. Escreva um nome para o teste e manter as outras predefinições.  Isto solicita a home page da aplicação a cada 5 minutos 5 localizações geográficas diferentes. 
3. Selecione **alertas** para abrir o **alertas** painel onde pode definir os detalhes sobre como responder se o teste falha. Tipo de um endereço de e-mail para enviar quando os critérios de alerta for cumprida.  Poderá, opcionalmente, escrever o endereço de um webhook para chamar quando os critérios de alerta for cumprida.

    ![Criar o teste](media/app-insights-tutorial-alert/create-test.png)
 
4. Regresse ao painel de teste e após alguns minutos deve começar a ver resultados de teste de disponibilidade.  Clique no nome do teste para ver os detalhes de cada localização.  O gráfico de gráfico de dispersão mostra o êxito e a duração de cada teste.

    ![Detalhes de teste](media/app-insights-tutorial-alert/test-details.png)

5.  Pode desagregar para baixo detalhes de qualquer teste específico ao clicar no respetivo ponto no gráfico de gráfico de dispersão.  O exemplo abaixo mostra os detalhes de um pedido falhado.

    ![Resultado do teste](media/app-insights-tutorial-alert/test-result.png)
  
6. Se os critérios de alerta for cumprida, é enviada uma mensagem semelhante ao abaixo para o endereço que especificou.

    ![Alerta correio](media/app-insights-tutorial-alert/alert-mail.png)


## <a name="create-an-alert-from-metrics"></a>Criar um alerta de métricas
Para além de enviar alertas a partir de um teste de disponibilidade, pode criar um alerta a partir de qualquer métricas de desempenho que estão a ser recolhidas para a sua aplicação.

2. Selecione **alertas** do **configurar** menu.  Esta ação abre o painel de alertas do Azure.  Poderão existir outras regras de alerta configuradas aqui para outros serviços.
3. Clique em **Adicionar alerta métrica**.  Esta ação abre o painel para criar uma nova regra de alerta.

    ![Adicionar o alerta métrica](media/app-insights-tutorial-alert/add-metric-alert.png)

4. Escreva um **nome** para o alerta de regra e selecione a aplicação na lista pendente para **recursos**.
5. Selecione um **métrica** a amostra.  Um gráfico é apresentado para indicar o valor deste pedido ao longo das últimas 24 horas.  Isto ajuda a definir a condição para a métrica.

    ![Adicionar regra de alerta](media/app-insights-tutorial-alert/add-alert-01.png)

6. Especifique um **condição** e **limiar** para o alerta. Este é o número de vezes que a métrica tem de ser excedida para um alerta a ser criado. 
6. Em **notificar através de** verificar o **proprietários, contribuintes e leitores de E-Mail** caixa a enviar um e-mail aos utilizadores quando a condição de alerta for cumprida e adiciona o endereço de correio eletrónico dos destinatários qualquer adicionais.  Também pode especificar um webhook ou uma aplicação de lógica aqui que é executado quando a condição for satisfeita.  Estes podem ser utilizadas para tentar mitigar o problema detetado ou 

    ![Adicionar regra de alerta](media/app-insights-tutorial-alert/add-alert-02.png)


## <a name="proactively-send-information"></a>Proativamente enviar informações
Os alertas são criados no reação a um conjunto específico de problemas identificados na sua aplicação e, normalmente a reservar alertas críticos condições que necessite de atenção imediata.  Proativamente pode receber informações sobre a sua aplicação com uma aplicação lógica que é executada automaticamente com base numa agenda.  Por exemplo, pode ter um correio enviado para os administradores de com informações de resumo que requer mais avaliação.

Para obter mais informações sobre como criar uma aplicação lógica com o Application Insights, consulte [automatizar o Application Insights processa utilizando as Logic Apps](automate-with-logic-apps.md)

## <a name="next-steps"></a>Passos seguintes
Agora que aprendeu como alerta sobre problemas, avançar para o próximo tutorial para saber como analisar a forma como os utilizadores são interagir com a sua aplicação.

> [!div class="nextstepaction"]
> [Compreender os utilizadores](app-insights-tutorial-users.md)
---
title: "Receber uma notificação quando um valor métrico cumpre uma condição | Microsoft Docs"
description: "Um guia de início rápido para ajudar os utilizadores criem uma métrica para uma aplicação lógica"
author: anirudhcavale
manager: orenr
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.service: monitoring-and-diagnostics
ms.topic: quickstart
ms.date: 09/25/2017
ms.author: ancav
ms.custom: mvc
ms.openlocfilehash: 08d63d47a99bdf9480299a74634bc0e9a09e691e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="receive-a-notification-when-a-metric-value-meets-a-condition"></a>Receber uma notificação quando um valor métrico cumpre uma condição

Monitor do Azure torna as métricas disponíveis para vários recursos do Azure. Estas métricas de transmitir o desempenho e o funcionamento desses recursos. Em muitos métrica de casos valores podem apontar para algo que está a ser errado com um recurso. Pode criar alertas métricas para monitorizar comportamento anormal e notificado se ocorrer. Este passos de início rápido através da criação de uma aplicação lógica, criação de uma tarefa e visualizar as métricas para a aplicação lógica. Em seguida, passa através da criação de um alerta e receber uma notificação para uma métrica para o recurso de aplicação lógica.

Para obter mais informações sobre as métricas e métricas alertas, consulte [descrição geral de métricas de Monitor de Azure](./monitoring-overview-metrics.md) e [descrição geral dos alertas do Monitor de Azure](./monitoring-overview-alerts.md). 

Se não tiver uma subscrição do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no [Portal do Azure](https://portal.azure.com/).

## <a name="create-a-logic-app"></a>Criar uma Aplicação Lógica

1. Clique no botão **Novo** localizado no canto superior esquerdo do portal do Azure.

2. Procure e selecione **aplicação lógica**. Criar um novo grupo de recursos com o nome **myResourceGroup** utilizar a localização predefinida. Clique no botão **Criar**.

3. Introduza as informações da aplicação lógica e verifique o **afixar ao Dashboard** opção. Quando terminar, clique em **Criar**.

    ![Introduza as informações básicas sobre a sua aplicação lógica no portal](./media/monitoring-quick-resource-metric-alert-portal/create-logic-app-portal.png)  


4. A aplicação lógica deve ser afixada ao dashboard. Navegue para a aplicação lógica clicando no mesmo.

5. No painel aplicação lógica, selecione o **Designer de aplicação lógica**

     ![Criar um acionador de recorrência no designer de aplicação lógica, no painel do portal](./media/monitoring-quick-resource-metric-alert-portal/logic-app-designer.png)  

6. Configure que os valores, como mostrado no diagrama seguinte.

    ![Configurar o acionador de aplicação lógica no painel do portal](./media/monitoring-quick-resource-metric-alert-portal/create-logic-app-triggers.png). 

7. No designer, selecione o **periodicidade** acionador.

8. Defina um intervalo de 20 e uma frequência de segundo para garantir a que sua aplicação lógica é acionada a cada 20 segundos.

9. Clique em de **novo passo** botão e selecione **adicionar uma ação**.

10. Escolha o **HTTP** opção e selecione **HTTP HTTP**.

11. Definir o **método** como POST e o **Uri** para um endereço de web à sua escolha.

12. Clique em **Guardar**.

## <a name="view-metrics-for-your-logic-app"></a>Veja as métricas para a sua aplicação lógica

1. Clique em de **Monitor** opção no painel de navegação esquerdo.

2. Selecione o **métricas** separador, preencha o **subscrição**, **grupo de recursos**, **tipo de recurso** e **recursos** informações para a sua aplicação lógica.

3. Na lista de métricas, escolha **executa iniciado**.

4. Modificar o **intervalo de tempo** do gráfico a apresentar dados para a hora anterior.

5. Deverá ver um gráfico de representar o número total de execuções que começou a sua aplicação lógica através da hora anterior.

    ![Desenhar um gráfico de métrico para o recurso de aplicação lógica](./media/monitoring-quick-resource-metric-alert-portal/logic-app-metric-chart.png)

## <a name="create-a-metric-alert-for-your-logic-app"></a>Criar um alerta de métrico para a sua aplicação lógica

1.  Na parte superior direita do painel métricas clique o **Adicionar alerta métrica** botão.

2. Nome do alerta métrico 'myLogicAppAlert' e forneça uma breve descrição do alerta.

3. Definir o **condição** para o alerta métrico como 'Maior que', defina o **limiar** como "10" e defina o **período** como 'nos últimos 5 minutos'.

4. Por fim, em **administrador adicionais email(s)** introduza o seu endereço de correio eletrónico. Este alerta garante que recebe uma mensagem de e-mail no caso da aplicação de lógica tiver mais de 10 executa falhadas durante um período de 5 minutos.

    ![Configurar o alerta de aplicação lógica no painel do portal](./media/monitoring-quick-resource-metric-alert-portal/logic-app-metrics-alert-portal.png)

## <a name="receive-metric-alert-notifications-for-your-logic-app"></a>Receber notificações de alerta métricas para a sua aplicação lógica
1. Dentro de alguns instantes, deverá receber uma mensagem de e-mail a partir de "Microsoft Azure alertas' para o informar sobre o alerta foi 'ativado'.

2. Navegue de volta para a sua aplicação lógica e modifique o acionador de recorrência para um intervalo de 1 e a frequência de hora.

3. Dentro de alguns minutos, deverá receber um e-mail de 'Microsoft alertas do Azure' informando-o alerta foi 'resolveu'.

## <a name="clean-up-resources"></a>Limpar recursos

Outros inícios rápidos nesta coleção baseiam-se este guia de introdução. Se pretender continuar com a trabalhar com subsequentes inícios rápidos ou com os tutoriais, não limpeza até os recursos criados neste guia de introdução. Se não pretender continuar, utilize os passos seguintes para eliminar todos os recursos criados por este guia de introdução no portal do Azure.

1. No menu da esquerda no portal do Azure, clique na **Monitor**.

2. Selecione o **alertas** separador, localize o alerta que criou neste guia de início rápido e clique no mesmo.

3. No painel de métrico alerta, clique em **eliminar**.

4. No menu da esquerda no portal do Azure, procure **aplicação lógica** e, em seguida, clique em **as Logic apps**.

5. No painel, clique na aplicação lógica que criou neste guia de introdução na caixa de texto e, em seguida, clique em **eliminar**.

## <a name="next-steps"></a>Passos seguintes

Este guia de introdução, aprendeu como criar um alerta de métrico para os seus recursos. Para mais informações sobre alertas de métricas, clique em através do nosso descrição geral de alertas.

> [!div class="nextstepaction"]
> [Alertas de ação de subscrição de Monitor do Azure](./monitor-quick-audit-notify-action-in-subscription.md )

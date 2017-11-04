---
title: "Introdução ao dimensionamento automático no Azure | Microsoft Docs"
description: Saiba como dimensionar o seu recurso no Azure.
author: rajram
manager: rboucher
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: d37d3fda-8ef1-477c-a360-a855b418de84
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/07/2017
ms.author: rajram
ms.openlocfilehash: 84ce3af2bef1e3204ad91567d155d7cbf90e9c7e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-autoscale-in-azure"></a>Introdução ao dimensionamento automático no Azure
Este artigo descreve como configurar as definições de dimensionamento automático para o seu recurso no portal do Microsoft Azure.

Azure de Monitor de dimensionamento automático só se aplica a conjuntos de dimensionamento de máquina virtual, serviços em nuvem, os planos do App Service do Azure e ambientes de serviço de aplicações. 

## <a name="discover-the-autoscale-settings-in-your-subscription"></a>Detetar as definições de dimensionamento automático na sua subscrição
Pode detetar todos os recursos aos quais se aplica no Monitor de Azure dimensionamento automático. Utilize os seguintes passos para obter instruções passo a passo:

1. Abra o [portal do Azure.][1]
2. Clique no ícone de Monitor do Azure no painel esquerdo.
  ![Abra Monitor do Azure][2]
3. Clique em **dimensionamento automático** para ver todos os recursos aos quais se aplica, juntamente com o respetivo estado atual do dimensionamento automático dimensionamento automático.
  ![Detetar o dimensionamento automático no Monitor do Azure][3]

Pode utilizar o painel de filtro na parte superior para o âmbito da lista pendente para selecionar recursos um grupo de recurso específico, tipos de recurso específico ou um recurso específico.

Para cada recurso, encontrará a contagem de instâncias atual e o estado de dimensionamento automático. O estado de dimensionamento automático pode ser:

- **Não configurado**: não tiver ativado dimensionamento automático ainda para este recurso.
- **Ativado**: tiver ativado o dimensionamento automático para este recurso.
- **Desativado**: ter desativado o dimensionamento automático para este recurso.

## <a name="create-your-first-autoscale-setting"></a>Criar a primeira definição de dimensionamento automático

Vamos agora percorrer simples instruções passo a passo para criar a primeira definição de dimensionamento automático.

1. Abra o **dimensionamento automático** painel no Monitor do Azure e selecione um recurso que pretende dimensionar. (Os passos seguintes utilizam um plano de serviço de aplicações associado a uma aplicação web. Pode [criar a sua primeira aplicação web ASP.NET no Azure em 5 minutos.] [4])
2. Tenha em atenção que a contagem atual de instâncias é 1. Clique em **ativar o dimensionamento automático**.
  ![Definição de dimensionamento para a nova aplicação web][5]
3. Forneça um nome para a definição de dimensionamento e, em seguida, clique em **adicionar uma regra**. Tenha em atenção as opções de regra de escala que abra como um painel de contexto no lado direito. Por predefinição, isto define a opção para dimensionar a contagem de instâncias por 1 se a percentagem de CPU do recurso exceder 70 por cento. Deixe-os valores predefinidos e clique em **adicionar**.
  ![Criar a definição de dimensionamento para uma aplicação web][6]
4. Agora que criou na primeira regra de escala. Tenha em atenção que o UX recomenda melhores práticas e Estados de que "é recomendado ter pelo menos uma escala na regra." Para tal:
  
    a. Clique em **adicionar uma regra**. 

    b. Definir **operador** para **menor**.

    c. Definir **limiar** para **20**.

    d. Definir **operação** para **diminuir a contagem por**.

   Agora, deve ter uma definição de dimensionamento que escalas out/escalas em com base na utilização de CPU.
   ![Escala com base na CPU][8]
5. Clique em **Guardar**.

Parabéns! Agora criou com êxito a primeira definição de dimensionamento para dimensionar automaticamente a sua aplicação web com base na utilização de CPU.

> [!NOTE] 
> Os mesmos passos são aplicáveis a começar com um conjunto de dimensionamento de máquina virtual ou função do serviço de nuvem.

## <a name="other-considerations"></a>Outras considerações
### <a name="scale-based-on-a-schedule"></a>Escala com base numa agenda
Para além de escala com base na CPU, pode definir a escala diferente para dias específicos da semana.

1. Clique em **adicionar uma condição de escala**.
2. Definir o modo de escala e as regras de é igual a condição de predefinição.
3. Selecione **repetir dias específicos** para a agenda.
4. Selecione os dias e a hora de início/fim para quando a condição de escala deve ser aplicada.

![Condição de escala com base numa agenda][9]
### <a name="scale-differently-on-specific-dates"></a>Dimensionar de forma diferente em datas específicas
Para além de escala com base na CPU, pode definir a escala diferente para datas específicas.

1. Clique em **adicionar uma condição de escala**.
2. Definir o modo de escala e as regras de é igual a condição de predefinição.
3. Selecione **datas de início/fim de especificar** para a agenda.
4. Selecione as datas de início/fim e a hora de início/fim para quando a condição de escala deve ser aplicada.

![Condição de escala com base nas datas][10]

### <a name="view-the-scale-history-of-your-resource"></a>Ver o histórico de escala do seu recurso
Sempre que o recurso do é escalado para cima ou para baixo, fica registado um evento no registo de atividade. Pode ver o histórico de escala dos seus recursos para as últimas 24 horas, mudar para o **histórico de execução** separador.

![histórico de execução][11]

Se pretender ver o histórico de escala completa (para até 90 dias), selecione **clique aqui para ver mais detalhes**. É aberto o registo de atividade, com dimensionamento automático previamente selecionado para o recurso e categoria.

### <a name="view-the-scale-definition-of-your-resource"></a>Ver a definição de dimensionamento do seu recurso
Dimensionamento automático é um recurso do Azure Resource Manager. Pode ver a definição de dimensionamento no JSON, mudar para o **JSON** separador.

![Definição de dimensionamento][12]

Pode efetuar alterações no JSON diretamente, se necessário. Estas alterações serão refletidas depois de guardá-los.

### <a name="disable-autoscale-and-manually-scale-your-instances"></a>Desativar o dimensionamento automático e dimensionar manualmente as instâncias
Poderão existir horas em que pretende desativar a definição de dimensionamento atual e dimensionar manualmente o recurso.

Clique em de **desativar o dimensionamento automático** botão na parte superior.
![Desativar o dimensionamento automático][13]

> [!NOTE] 
> Esta opção desativa a configuração. No entanto, pode regressar ao mesmo depois de ativar o dimensionamento automático novamente. 

Agora pode definir o número de instâncias que pretende dimensionar manualmente.

![Conjunto de dimensionamento manual][14]

Pode sempre regressar ao dimensionamento automático ao clicar em **ativar o dimensionamento automático** e, em seguida, **guardar**.

## <a name="next-steps"></a>Passos seguintes
- [Criar um alerta de registo de atividade para monitorizar todas as operações de motor de dimensionamento automático na sua subscrição](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-alert)
- [Criar um alerta de registo de atividade para monitorizar as operações de escala-na/escalável de dimensionamento automático falhas todas na sua subscrição](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-failed-alert)

<!--Reference-->
[1]:https://portal.azure.com
[2]: ./media/monitoring-autoscale-get-started/azure-monitor-launch.png
[3]: ./media/monitoring-autoscale-get-started/discover-autoscale-azure-monitor.png
[4]: https://docs.microsoft.com/azure/app-service/app-service-web-get-started-dotnet
[5]: ./media/monitoring-autoscale-get-started/scale-setting-new-web-app.png
[6]: ./media/monitoring-autoscale-get-started/create-scale-setting-web-app.png
[7]: ./media/monitoring-autoscale-get-started/scale-in-recommendation.png
[8]: ./media/monitoring-autoscale-get-started/scale-based-on-cpu.png
[9]: ./media/monitoring-autoscale-get-started/scale-condition-schedule.png
[10]: ./media/monitoring-autoscale-get-started/scale-condition-dates.png
[11]: ./media/monitoring-autoscale-get-started/scale-history.png
[12]: ./media/monitoring-autoscale-get-started/scale-definition-json.png
[13]: ./media/monitoring-autoscale-get-started/disable-autoscale.png
[14]: ./media/monitoring-autoscale-get-started/set-manualscale.png


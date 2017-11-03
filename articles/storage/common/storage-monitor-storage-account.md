---
title: Como monitorizar uma conta de armazenamento do Azure | Microsoft Docs
description: Saiba como monitorizar uma conta de armazenamento no Azure utilizando o portal do Azure.
services: storage
documentationcenter: 
author: tamram
manager: timlt
editor: tysonn
ms.assetid: b83cba7b-4627-4ba7-b5d0-f1039fe30e78
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2017
ms.author: tamram
ms.openlocfilehash: 8c566978531ceb9db9aa7c9e2ba3ab83b2dc67cb
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="monitor-a-storage-account-in-the-azure-portal"></a>Monitorizar uma conta de armazenamento no portal do Azure

[Análise de armazenamento do Azure](../storage-analytics.md) fornece métricas para todos os serviços de armazenamento e os registos para blobs, filas e tabelas. Pode utilizar o [portal do Azure](https://portal.azure.com) para configurar os registos e as métricas são registados para a sua conta e configure gráficos que fornecem representações visuais dos seus dados de métricas.

> [!NOTE]
> Não existem custos associados a examinar os dados de monitorização no portal do Azure. Para obter mais informações, consulte [faturação e de análise de armazenamento](/rest/api/storageservices/Storage-Analytics-and-Billing).
>
> Ficheiros do Azure suporta métricas da análise de armazenamento atualmente, mas ainda não suporta o registo.
>
> Contas de armazenamento com um tipo de replicação de com redundância de zona de armazenamento (ZRS) atualmente não dispõe de métricas ou a capacidade do registo ativada.
> 
> Para obter um guia aprofundado sobre a utilização de análise de armazenamento e outras ferramentas para identificar, diagnosticar e resolver problemas relacionados com o Storage do Azure, consulte [monitorizar, diagnosticar e resolver problemas de armazenamento do Microsoft Azure](../storage-monitoring-diagnosing-troubleshooting.md).
>

## <a name="configure-monitoring-for-a-storage-account"></a>Configurar a monitorização de uma conta de armazenamento

1. No [portal do Azure](https://portal.azure.com), selecione **contas do Storage**, em seguida, o nome de conta de armazenamento para abrir o dashboard de conta.
1. Selecione **diagnóstico** no **monitorização** secção do painel de menu.

    ![MonitoringOptions](./media/storage-monitor-storage-account/stg-enable-metrics-00.png)

1. Selecione o **tipo** dos dados de métricas para cada **serviço** que pretende monitorizar e o **política de retenção** para os dados. Também pode desativar a monitorização, definindo **estado** para **desativar**.

    ![MonitoringOptions](./media/storage-monitor-storage-account/stg-enable-metrics-01.png)

   Existem dois tipos de métricas, pode ativar para cada serviço, que estão ativadas por predefinição para novas contas de armazenamento:

   * **Agregação**: recolhe métricas como percentagens de entrada/saída, disponibilidade, latência e êxito. Estas métricas são agregadas para o blob, fila, tabela e serviços de ficheiros.
   * **Por API**: para além das métricas de agregação, recolhe o mesmo conjunto de métricas para cada operação de armazenamento na API do serviço de armazenamento do Azure.

   Para definir a política de retenção de dados, mover o **retenção (dias)** controlo de deslize ou introduza o número de dias de dados para a retenção de 1 a 365. A predefinição para novas contas de armazenamento é sete dias. Se não pretender definir uma política de retenção, introduza zero. Se não houver nenhuma política de retenção, é até eliminar os dados de monitorização.

   > [!WARNING]
   > São-lhe cobrados quando eliminar manualmente os dados de métricas. Dados de análise obsoletos (mais antigos do que a política de retenção de dados) foi eliminados pelo sistema, sem qualquer custo. É recomendável definir uma política de retenção com base no quanto pretende manter os dados de análise de armazenamento para a sua conta. Consulte [que cobra pode implicar ao ativar as métricas do storage?](../common/storage-enable-and-view-metrics.md#what-charges-do-you-incur-when-you-enable-storage-metrics) para obter mais informações.
   >

1. Quando concluir a configuração de monitorização, selecione **guardar**.

Um conjunto predefinido de métricas é apresentado nos gráficos no painel de conta de armazenamento, bem como os painéis do serviço individual (blob, fila, tabela e ficheiro). Assim que tiver ativado as métricas para um serviço, pode demorar uma hora para os dados são apresentados no respetivos gráficos. Pode selecionar **editar** em qualquer gráfico métrico para [configurar as métricas](#how-to-customize-metrics-charts) são apresentadas no gráfico.

Pode desativar a recolha de métricas e registo definindo **estado** para **desativar**.

> [!NOTE]
> Storage do Azure utiliza [tabela armazenamento](../common/storage-introduction.md#table-storage) para armazenar as métricas para a sua conta de armazenamento e armazena as métricas em tabelas na sua conta. Para obter mais informações, consulte. [Como são armazenadas as métricas](../common/storage-analytics.md#how-metrics-are-stored).
>

## <a name="customize-metrics-charts"></a>Personalizar gráficos de métricas

Utilize o procedimento seguinte para escolher quais as métricas do storage para ver um gráfico de métricas. 

1. Comece por apresentar um gráfico de métrica de armazenamento no portal do Azure. Pode encontrar gráficos no **painel de conta de armazenamento** e no **métricas** painel para um serviço individual (blob, fila, tabela, o ficheiro).

   Neste exemplo, iremos trabalhar com o gráfico seguinte que aparece no **painel de conta de armazenamento**:

   ![Seleção de gráfico no portal do Azure](./media/storage-monitor-storage-account/stg-customize-chart-00.png)

1. Em seguida, clique em qualquer lugar dentro do gráfico para abrir o **métrica** painel. Selecione **editar gráfico** para abrir o **editar gráfico** painel.

   ![Botão de gráfico no painel de gráfico editar](./media/storage-monitor-storage-account/stg-customize-chart-01.png)

1. No **editar gráfico** painel, selecione o **intervalo de tempo** das métricas para apresentar no gráfico e o **serviço** (blob, fila, tabela, de ficheiros) cujas métricas que pretende apresentar. Aqui, selecionamos apresentação das métricas da semana passada para o serviço blob:

   ![Seleção de intervalo e o serviço de hora no painel de editar gráfico](./media/storage-monitor-storage-account/stg-customize-chart-02.png)

1. Selecione o indivíduo **métricas** como tinha apresentadas no gráfico, em seguida, clique em **OK**. Por exemplo, aqui escolheu apresentar o *ContainerCount* e *ObjectCount* métricas:

   ![Seleção de métrica individuais no painel de editar gráfico](./media/storage-monitor-storage-account/stg-customize-chart-03.png)

As definições de gráfico não afetam a coleção, a agregação ou o armazenamento dos dados na conta de armazenamento, apenas a visualização de dados de métricas de monitorização.

### <a name="metrics-availability-in-charts"></a>Disponibilidade de métricas nos gráficos

A lista de alterações de métricas disponíveis com base nos que escolheu no menu pendente e o tipo de unidade do gráfico de serviço que está a editar. Por exemplo, pode selecionar métricas de percentagem, como *PercentNetworkError* e *PercentThrottlingError* apenas se estiver a editar um gráfico que mostra as unidades de medida como percentagem:

![Gráfico de percentagem de erro de pedido no portal do Azure](./media/storage-monitor-storage-account/stg-customize-chart-04.png)

### <a name="metrics-resolution"></a>Resolução de métricas

As métricas que selecionou no diagnóstico determina a resolução das métricas que estão disponíveis para a sua conta:

* **Agregação** monitorização fornece métricas como percentagens de entrada/saída, disponibilidade, latência e êxito. Estas métricas são agregadas a partir do blob, tabela, ficheiros e serviços da fila.
* **Por API** disponibiliza melhorar resolução, métricas disponíveis para operações de armazenamento individuais, além para as agregações de nível de serviço.

## <a name="configure-metrics-alerts"></a>Configurar alertas de métricas

Pode criar alertas para notificá-lo quando limiares tem sido atingidos de métricas de recurso de armazenamento.

1. Para abrir o **painel regras de alertas**, desloque para baixo até o **monitorização** secção o **painel de Menu** e selecione **regras de alerta**.
1. Selecione **Adicionar alerta** para abrir o **adicionar uma regra de alerta** painel
1. Selecione um **recursos** (blob, ficheiro, fila, tabela) da lista pendente e introduza um **nome** e **Descrição** para a nova regra de alerta.
1. Selecione o **métrica** para o qual pretende adicionar um alerta, um alerta **condição**e um **limiar**. A unidade de limiar escreva muda, consoante a métrica que escolheu. Por exemplo, "contagem" é o tipo de unidade para *ContainerCount*, enquanto a unidade para o *PercentNetworkError* métrica é uma percentagem.
1. Selecione o **período**. As métricas que atingirem ou excedem o limiar dentro do período de acionam um alerta.
1. (Opcional) Configurar **E-Mail** e **Webhook** notificações. Para obter mais informações sobre webhooks, consulte [configurar um webhook num alerta métrico Azure](../../monitoring-and-diagnostics/insights-webhooks-alerts.md). Se não configurar notificações de e-mail ou webhook, alertas irão aparecer apenas no portal do Azure.

![Painel 'Adicionar uma regra de alerta' no portal do Azure](./media/storage-monitor-storage-account/stg-alert-rules-01.png)

## <a name="add-metrics-charts-to-the-portal-dashboard"></a>Adicione gráficos de métricas ao dashboard do portal

Pode adicionar gráficos de métricas do Storage do Azure para qualquer uma das suas contas do storage ao seu dashboard do portal.

1. Clique em selecionar **editar dashboard** ao visualizar o dashboard na [portal do Azure](https://portal.azure.com).
1. No **mosaico galeria**, selecione **localizar os mosaicos por** > **tipo**.
1. Selecione **tipo** > **contas do Storage**.
1. No **recursos**, selecione a conta de armazenamento cujas métricas que pretende adicionar ao dashboard.
1. Selecione **categorias** > **monitorização**.
1. Arrastar e largar o gráfico mosaico no dashboard para a métrica que pretende apresentar. Repita para todas as métricas que gostaria de apresentadas no dashboard. Na imagem seguinte, o gráfico "Blobs - Total de pedidos do" é realçado, por exemplo, mas todos os gráficos estão disponíveis para colocação no dashboard.

   ![Galeria de mosaico no portal do Azure](./media/storage-monitor-storage-account/stg-customize-dashboard-01.png)
1. Selecione **feito personalizar** perto da parte superior do dashboard quando tiver terminado a adição de gráficos.

Depois de adicionar gráficos ao seu dashboard, pode personalizar ainda mais-las conforme descrito em [personalizar gráficos de métricas](#how-to-customize-metrics-charts).

## <a name="configure-logging"></a>Configurar o registo

Pode instruir o Storage do Azure para guardar os registos de diagnóstico para leitura, escrita e eliminação pedidos para o blob, tabela e serviços da fila. A política de retenção de dados, definir também se aplica a estes registos.

> [!NOTE]
> Ficheiros do Azure suporta métricas da análise de armazenamento atualmente, mas ainda não suporta o registo.
>

1. No [portal do Azure](https://portal.azure.com), selecione **contas do Storage**, em seguida, o nome da conta do storage para abrir o painel de conta de armazenamento.
1. Selecione **diagnóstico** no **monitorização** secção do painel de menu.

    ![Diagnóstico item de menu sob monitorização no portal do Azure.](./media/storage-monitor-storage-account/stg-enable-metrics-00.png)
    
1. Certifique-se **estado** está definido como **no**e selecione o **serviços** para o qual gostaria de ativar o registo.

    ![Configure o registo no portal do Azure.](./media/storage-monitor-storage-account/stg-enable-logging-01.png)
1. Clique em **Guardar**.

Os registos de diagnóstico são guardados no contentor do blob denominado $logs na sua conta de armazenamento. Pode ver os dados de registo utilizando o Explorador de armazenamento, como o [Explorador de armazenamento do Microsoft](http://storageexplorer.com), ou através de programação utilizando a biblioteca de clientes de armazenamento ou o PowerShell.

Para obter informações sobre como aceder ao contentor $logs, consulte [aceder aos dados de registo e ativar o registo de armazenamento](/rest/api/storageservices/enabling-storage-logging-and-accessing-log-data).

## <a name="next-steps"></a>Passos seguintes

* Obter mais informações sobre [métricas, registo e faturação](../storage-analytics.md) para análise de armazenamento.
* [Ativar dados de métricas de métricas e vista de armazenamento do Azure](../storage-enable-and-view-metrics.md) utilizando o PowerShell e através de programação com c#.

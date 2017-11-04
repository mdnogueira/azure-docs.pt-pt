---
title: "Arquivar dados de monitorização do Azure | Microsoft Docs"
description: "Arquivar dados de registo e a métrica produzidos no Azure para uma conta de armazenamento."
author: johnkemnetz
manager: orenr
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.service: monitoring-and-diagnostics
ms.topic: tutorial
ms.date: 09/25/2017
ms.author: johnkem
ms.custom: mvc
ms.openlocfilehash: 445901a740920a74f259aaa9c6b862680c1c807e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="archive-azure-monitoring-data"></a>Azure de arquivar dados de monitorização

Várias camadas do seu ambiente do Azure produzem registo e os dados métricos que podem ser arquivados para uma conta de armazenamento do Azure. Poderá fazê-lo para manter um histórico dos dados de monitorização ao longo do tempo de um arquivo de económico, não pesquisável depois dos dados passou o respetivo período de retenção de Monitor de Azure ou de análise de registos. Este tutorial passos durante o processo de configuração do seu ambiente do Azure para arquivar dados para uma conta de armazenamento.

Se não tiver uma subscrição do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no [Portal do Azure](https://portal.azure.com/).

## <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

Primeiro terá de configurar uma conta de armazenamento ao qual os dados de monitorização serão arquivados. Para tal, [siga os passos aqui descritos](../storage/common/storage-create-storage-account.md).

## <a name="route-subscription-logs-to-the-storage-account"></a>Registos de subscrição de rota para a conta de armazenamento

Agora está pronto para começar a configurar o ambiente do Azure para encaminhar os dados de monitorização para uma conta de armazenamento. Primeiro vamos configurar dados de nível de subscrição (contidos no registo de atividade do Azure) para ser encaminhados para a conta de armazenamento. O [ **registo de atividade do Azure** ](monitoring-overview-activity-logs.md) disponibiliza um histórico de eventos de nível de subscrição no Azure. Pode procurá-la no portal do Azure para determinar *quem* criados, atualizadas ou eliminadas *que* recursos e *quando* fizeram-lo.

1. Clique em de **Monitor** encontrado botão na lista de navegação esquerdo, em seguida, no **registo de atividade**.

   ![Secção de registo de atividade](media/monitor-tutorial-archive-monitoring-data/activity-log-home.png)

2. Na secção de registo de atividade que é apresentada, clique em de **exportar** botão.

3. No **exportar registo de atividade** secção que aparece, selecione a caixa para **exportar para uma conta de armazenamento** e clique em **Selecione uma conta de armazenamento.**

   ![Exportação de registo de atividade](media/monitor-tutorial-archive-monitoring-data/activity-log-export.png)

4. Na secção que aparece, utilize o **conta de armazenamento** pendente para selecionar o nome da conta de armazenamento que criou no precedente **criar uma conta de armazenamento** passo, em seguida, clique em **OK**.

   ![Escolha uma conta de armazenamento](media/monitor-tutorial-archive-monitoring-data/activity-log-storage.png)

5. Definir o **retenção (dias)** controlo de deslize para 30. Este controlo de deslize define um número de dias a manter os dados de monitorização na conta de armazenamento. Monitor do Azure elimina automaticamente dados mais antigos do que o número de dias especificado. Uma retenção de zero dias armazena os dados indefinidamente.

6. Clique em **guardar** e fechar esta secção.

Agora que fluem a dados da sua subscrição de monitorização para a conta de armazenamento.

## <a name="route-resource-data-to-the-storage-account"></a>Rota de dados de recursos para a conta de armazenamento

Agora vamos configurar dados de nível de recursos (métricas de recurso e os registos de diagnóstico) para ser encaminhados para a conta de armazenamento ao configurar **definições de diagnóstico de recurso**.

1. Clique em de **Monitor** encontrado botão na lista de navegação esquerdo, em seguida, no **definições de diagnóstico**. Aqui pode ver uma lista de todos os recursos na sua subscrição produzir dados de monitorização por meio do Monitor do Azure. Se não tiver quaisquer recursos nesta lista, pode [criar uma aplicação lógica](../logic-apps/logic-apps-create-a-logic-app.md) antes de prosseguir para que tenham um recurso que pode configurar uma definição de diagnóstico.

2. Clique num recurso na lista e, em seguida, clique em **ative os diagnósticos**.
   
   ![Ative os diagnósticos](media/monitor-tutorial-archive-monitoring-data/diagnostic-settings-turn-on.png)

   Se já existir uma definição configurada, consulte em vez disso, as definições existentes e um botão para **Adicionar definição de diagnóstico**. Clique neste botão.

   Uma definição de diagnóstico de recursos é uma definição de *que* dados de monitorização devem ser encaminhado de um recurso específico e *onde* que dados de monitorização devem passar.

3. Na secção que aparece, atribua a definição de um **nome** e marque a caixa de **arquivo para uma conta de armazenamento**.

   ![Secção de definições de diagnóstico](media/monitor-tutorial-archive-monitoring-data/diagnostic-settings-home.png)

4. Clique em de **configurar** botão em **arquivo para uma conta de armazenamento** e selecione a conta de armazenamento que criou na secção anterior. Clique em **OK**.

   ![Conta de armazenamento de definições de diagnóstico](media/monitor-tutorial-archive-monitoring-data/diagnostic-settings-storage.png)

5. Verifique todas as caixas em **registo** e **métrica**. Dependendo do tipo de recurso, só pode ter uma destas opções. Estas caixas de verificação controlam quais as categorias de dados de registo e a métrica disponíveis para que tipo de recurso são enviadas para o destino que selecionou, neste caso, uma conta de armazenamento.

   ![Categorias de definições de diagnóstico](media/monitor-tutorial-archive-monitoring-data/diagnostic-settings-categories.png)
   
6. Definir o **retenção (dias)** controlo de deslize para 30. Este controlo de deslize define um número de dias a manter os dados de monitorização na conta de armazenamento. Monitor do Azure elimina automaticamente dados mais antigos do que o número de dias especificado. Uma retenção de zero dias armazena os dados indefinidamente.

7. Clique em **Guardar**.

Agora que fluem a dados de monitorização do seu recurso para a conta de armazenamento.

## <a name="route-virtual-machine-guest-os-data-to-the-storage-account"></a>Encaminhar os dados da máquina virtual (SO convidado) para a conta de armazenamento

1. Se tiver já uma máquina virtual na sua subscrição, [criar uma máquina virtual](../virtual-machines/windows/quick-create-portal.md).

2. Na lista de navegação esquerdo no portal, clique em **máquinas virtuais**.

3. Na lista de máquinas virtuais que é apresentada, clique na máquina virtual que criou.

4. Na secção que aparece, clique em **definições de diagnóstico** no painel de navegação esquerdo. Esta secção permite-lhe configurar a extensão de monitorização de out-of-box monitor do Azure na sua máquina virtual e que está a ser produzidos pelo Windows ou Linux para uma conta de armazenamento de dados de rota.

   ![Navegue para definições de diagnóstico](media/monitor-tutorial-archive-monitoring-data/guest-navigation.png)

5. Clique em **ativar a monitorização de ao nível do convidado** na secção que aparece.

   ![Ative as definições de diagnóstico](media/monitor-tutorial-archive-monitoring-data/guest-enable.png)

6. Assim que a definição de diagnóstico foi guardado corretamente, o **descrição geral** separador mostra uma lista dos dados que está a ser recolhidos e onde está a ser armazenada. Clique em de **contadores de desempenho** contadores de secção para rever o conjunto de desempenho do Windows que está a ser recolhidos.

   ![Definições de contadores de desempenho](media/monitor-tutorial-archive-monitoring-data/guest-perf-counters.png)
   
7. Clique em de **registos** separador e marque as caixas de verificação para **informações** nível os registos de aplicações e os registos de sistema.

   ![As definições de registos](media/monitor-tutorial-archive-monitoring-data/guest-logs.png)

8. Clique em de **agente** separador e, em **conta de armazenamento** clique no nome da conta do storage mostrado.

   ![Atualizar a conta de armazenamento](media/monitor-tutorial-archive-monitoring-data/guest-storage-home.png)

9. Na secção que aparece, escolha a conta de armazenamento que criou no precedente **criar uma conta de armazenamento** passo.

10. Clique em **Guardar**.

Agora que fluem a dados das suas máquinas virtuais de monitorização para a conta de armazenamento.

## <a name="view-the-monitoring-data-in-the-storage-account"></a>Ver os dados de monitorização na conta de armazenamento

Se seguiu os passos anteriores, dados começou a fluir à sua conta de armazenamento.

1. Para alguns tipos de dados, por exemplo, o registo de atividade, tem de haver uma atividade que gera um evento na conta de armazenamento. Para gerar a atividade no registo de atividade, siga [estas instruções](./monitor-quick-audit-notify-action-in-subscription.md). Poderá ter de aguardar até cinco minutos antes do evento é apresentado na conta de armazenamento.

2. No portal, navegue para o **contas do Storage** secção por encontrá-las na barra de navegação esquerdo.

3. Identificar a conta de armazenamento que criou na secção anterior e clique no mesmo.

4. Clique em **Blobs**, em seguida, no contentor de etiqueta **insights registos operacionais** e, finalmente, no contentor com a etiqueta **name = predefinido**. Este é o contentor que tenha o registo de atividade no mesmo. Dados de monitorização é dividido em contentores por ID de recurso (apenas o ID de subscrição para o registo de atividade), em seguida, pela data e hora. O formato completo para estes blobs é:

   informações operacionais-registos/nome = predefinido/resourceId = / subscrições / {ID de subscrição} / y = {com quatro dígitos numérico year} / m = {dois dígitos numérico month} / d = {dois dígitos numérico day} / h = {relógio de 24 horas dois dígitos hour}/m=00/PT1H.json

5. Navegue para o ficheiro de PT1H.json ao clicar nos contentores de ID de recurso, data e hora. Clique no ficheiro PT1H.json e clique em **transferir**. Cada blob PT1H.json contém um blob JSON de eventos que ocorreram dentro da hora especificada no URL do blob (por exemplo, h = 12). Durante a hora presente, os eventos são acrescentados para o ficheiro PT1H.json à medida que ocorrem. O valor de minuto (m = 00) é sempre 00, uma vez que o registo de eventos é divididos em blobs individuais por hora.

   Agora, pode ver o evento JSON que foram armazenado na conta de armazenamento. Para registos de diagnóstico de recursos, o formato para os blobs é:

   insights - registos-{nome da categoria de registo} / resourceId = / {ID do recurso} / y = {com quatro dígitos numérico year} / m = {dois dígitos numérico month} / d = {dois dígitos numérico day} / h = {: hour}/m=00/PT1H.json dois dígitos relógio de 24 horas

6. Dados de monitorização de SO de convidado é armazenado nas tabelas. Navegue de volta para a conta de armazenamento inicial e clique em **tabelas**. Não existem tabelas de métricas, contadores de desempenho e registos de eventos.

Pode agora definiu com êxito dados de monitorização para ser arquivados para uma conta de armazenamento.

## <a name="clean-up-resources"></a>Limpar recursos

1. Navegue de volta para o **exportar registo de atividade** secção de precedente **encaminhar registos de subscrição para a conta de armazenamento** passo e clique em **repor**.

2. Navegue para o **definições de diagnóstico** secção, clique no recurso no qual criou uma definição de diagnóstico no precedente **encaminhar os dados de recursos para a conta de armazenamento** passo, em seguida, localize a definição criou, clique em de **Editar definição** botão e clique em **eliminar**.

3. Navegue para o **definições de diagnóstico** secção na máquina virtual configurada no precedente **encaminhar os dados da máquina virtual (SO convidado) para a conta de armazenamento** passo e, no  **Agente** separador clique **remover** (sob a **agente de diagnóstico do Azure Remova** secção).

4. Navegue para a conta de armazenamento que criou no precedente **criar uma conta de armazenamento** passo e clique em **eliminar conta do storage**. Escreva o nome da conta de armazenamento e, em seguida, clique em **eliminar**.

5. Se tiver criado uma máquina virtual ou uma aplicação lógica para os passos anteriores, elimine-os também.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu a configurar dados de monitorização do seu ambiente do Azure (subscrição, recursos e SO convidado) sejam arquivadas, para uma conta de armazenamento. Para tornar mais significado fora os dados e derivar insights, tente enviar os dados para análise de registos, bem como.

> [!div class="nextstepaction"]
> [Introdução à análise de registos](../log-analytics/log-analytics-get-started.md)

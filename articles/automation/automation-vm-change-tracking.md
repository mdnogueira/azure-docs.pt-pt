---
title: "Controlar as alterações nas suas máquinas virtuais do Azure | Microsoft Docs"
description: "Utilização de registo de alterações para controlar as alterações em ficheiros e registo em máquinas virtuais."
services: automation
documentationcenter: automation
author: eslesar
manager: carmonm
editor: tysonn
tags: azure-service-management
ms.assetid: 
ms.service: automation
ms.devlang: 
ms.topic: article
ms.tgt_pltfrm: 
ms.workload: infrastructure
ms.date: 09/25/2017
ms.author: eslesar
ms.custom: 
ms.openlocfilehash: 5c6e8390ec8533fc7ab281c212e47a6982b30f1a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="track-changes-in-your-azure-virtual-machines"></a>Controlar as alterações nas suas máquinas virtuais do Azure

Ao ativar o registo de alterações, pode controlar as alterações a ficheiros e chaves de registo do Windows em máquinas virtuais. Identificar as alterações de configuração pode ajudar a identificar problemas operacionais.

Pode ativar a alterações diretamente a partir da sua máquina virtual do Azure.

Se não tiver uma máquina virtual do Azure, pode criar um ao seguir as instruções no [início rápido do Windows](../virtual-machines/windows/quick-create-portal.md) ou [início rápido do Linux](../virtual-machines/linux/quick-create-portal.md) artigo.

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure
Inicie sessão no [Portal do Azure](https://portal.azure.com/).

## <a name="enable-change-tracking-for-an-azure-virtual-machine"></a>Ativar o controlo de alterações de uma máquina virtual do Azure

1. No painel esquerdo do portal do Azure, selecione **Máquinas virtuais**.
2. Na lista, selecione uma máquina virtual.
3. Da máquina virtual janela, em **operações**, selecione **de registo de alterações**. 

   ![Vm carregar o registo de alterações](./media/automation-vm-change-tracking/change-onboard-vm-blade.png)  
    O **ativar a gestão de atualização** é aberta a janela.

    A validação é efetuada para determinar se o registo de alterações está ativada para esta máquina virtual. Se o registo de alterações não estiver ativada, é apresentada uma faixa, dando-lhe a opção para ativar a solução.

   ![Faixa de carregar configuração de controlo de alterações](./media/automation-vm-change-tracking/change-onboard-banner.png)

4. Para ativar a solução, selecione a faixa. Se não tem os seguintes itens, estes serão adicionados automaticamente:

   * [Análise de registo](../log-analytics/log-analytics-overview.md) área de trabalho
   * [Automatização](../automation/automation-offering-get-started.md) conta

5. Selecione um espaço de trabalho de análise de registos para armazenar os registos de dados do registo de alterações, selecione uma conta de automatização para controlar as alterações e, em seguida, selecione **ativar**.  
    Uma barra de estado notifica-o de que a solução está a ser ativada. Este processo pode demorar até 15 minutos a concluir.

## <a name="configure-change-tracking"></a>Configurar registo de alterações

Depois de controlo de alterações estão ativado, o **alterações** surge a janela. 

Para escolher quais os ficheiros e chaves de registo para acompanhar, selecione **editar definições**.

   ![Definições de editar o registo de alterações](./media/automation-vm-change-tracking/change-edit-settings.png)

   O **configuração de área de trabalho** é aberta a janela. 

No **configuração de área de trabalho** janela, adicionar as chaves de registo do Windows, ficheiros do Windows ou Linux ficheiros ser controlados, conforme descrito nas três secções seguintes.

### <a name="add-a-windows-registry-key"></a>Adicionar uma chave de registo do Windows

1. No **registo do Windows** separador, selecione **adicionar**.  
    O **adicionar registo do Windows para controlo de alterações** é aberta a janela.

   ![Registo de adicionar o registo de alterações](./media/automation-vm-change-tracking/change-add-registry.png)

2. Em **ativado**, selecione **verdadeiro**.
3. No **nome do Item** box, introduza um nome amigável.
4. (Opcional) No **grupo** box, introduza um nome de grupo.
5. No **chave de registo do Windows** caixa, introduza o nome da chave de registo que pretende controlar.
6. Selecione **Guardar**.

### <a name="add-a-windows-file"></a>Adicionar um ficheiro do Windows

1. No **ficheiros do Windows** separador, selecione **adicionar**.  
    O **adicionar ficheiros do Windows para controlo de alterações** é aberta a janela.

   ![Registo de alterações adicionar ficheiros do Windows](./media/automation-vm-change-tracking/change-add-win-file.png)

2. Em **ativado**, selecione **verdadeiro**.
3. No **nome do Item** box, introduza um nome amigável.
4. (Opcional) No **grupo** box, introduza um nome de grupo.
5. No **introduza caminho** box, introduza o nome de ficheiro e caminho completo do ficheiro que pretende controlar.
6. Selecione **Guardar**.

### <a name="add-a-linux-file"></a>Adicione um ficheiro do Linux

1. No **Linux ficheiros** separador, selecione **adicionar**.  
    O **adicionar ficheiros de Linux para o controlo de alterações** é aberta a janela.

   ![Registo de alterações adicionar ficheiros do Linux](./media/automation-vm-change-tracking/change-add-linux-file.png)

2. Em **ativado**, selecione **verdadeiro**.
3. No **nome do Item** box, introduza um nome amigável.
4. (Opcional) No **grupo** box, introduza um nome de grupo.
5. No **introduza caminho** box, introduza o nome de ficheiro e caminho completo do ficheiro que pretende controlar.
6. No **tipo de caminho** caixa, selecione **ficheiro** ou **diretório**.
7. Em **recursão**para controlar as alterações para o caminho especificado e todos os ficheiros e caminhos sob a mesma, selecione **no**. Para monitorizar apenas o caminho selecionado ou o ficheiro, selecione **desativar**.
8. Em **utilize Sudo**, para controlar os ficheiros que necessitam de `sudo` comando para o acesso, selecione **no**. Caso contrário, selecione **desativar**.
9. Selecione **Guardar**.

## <a name="view-changes"></a>Alterações de vista

No **alterações** janela, pode ver as alterações em cada uma das várias categorias para a máquina virtual ao longo do tempo.

   ![Registo de alterações Adicionar vista alterações](./media/automation-vm-change-tracking/change-view-changes.png)

Pode selecionar as categorias e o intervalo de tempo de alterações para ver. Na parte superior da janela, pode ver uma representação gráfica de alterações ao longo do tempo. Na parte inferior da janela, pode ver uma lista de alterações recentes.

## <a name="view-change-tracking-log-data"></a>Ver dados de registo de controlo de alterações

Registo de alterações gera dados de registo que são enviados para análise de registos. Para procurar os registos através da execução de consultas, selecione **análise de registos** na parte superior do **alterações** janela.

   ![Análise de registos de controlo de alterações](./media/automation-vm-change-tracking/change-log-analytics.png)

Para saber mais sobre a ser executado e a procurar ficheiros de registo na análise de registos, consulte o artigo [Log Analytics](../log-analytics/log-analytics-overview.md).

## <a name="next-steps"></a>Passos seguintes

* Para saber mais sobre o controlo de alterações, consulte o artigo [controlar as alterações de software no seu ambiente com a solução Alterar controlo](../log-analytics/log-analytics-change-tracking.md).
* Para saber mais sobre a gestão de atualizações para as máquinas virtuais, consulte [solução de gestão de atualizações no OMS](../operations-management-suite/oms-solution-update-management.md).

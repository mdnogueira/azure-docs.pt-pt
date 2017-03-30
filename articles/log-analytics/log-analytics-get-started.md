---
title: "Começar a utilizar uma área de trabalho do Azure Log Analytics | Documentos da Microsoft"
description: "Pode começar a trabalhar com uma área de trabalho no Log Analytics numa questão de minutos."
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
ms.assetid: 508716de-72d3-4c06-9218-1ede631f23a6
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/08/2017
ms.author: banders
translationtype: Human Translation
ms.sourcegitcommit: 6e0ad6b5bec11c5197dd7bded64168a1b8cc2fdd
ms.openlocfilehash: 634b4c99b746ad0b8b3238e11b3c305d2cf7082f
ms.lasthandoff: 03/28/2017


---
# <a name="get-started-with-a-log-analytics-workspace"></a>Começar a utilizar uma área de trabalho do Log Analytics
Pode começar a trabalhar rapidamente com o Azure Log Analytics, uma ferramenta que o ajuda a avaliar as informações operacionais recolhidas a partir da sua infraestrutura de TI. Com este artigo, é fácil começar a explorar, analisar e tomar medidas relacionadas com os dados recolhidos *gratuitamente*.

Este artigo funciona como uma introdução ao Log Analytics, no qual é utilizado um breve tutorial que visa guiá-lo ao longo de uma implementação básica no Azure para que possa começar a utilizar o serviço. Uma área de trabalho é um contentor lógico onde os seus dados de gestão no Azure são armazenados. Depois de analisar estas informações e concluir a sua própria avaliação, pode remover a área de trabalho de avaliação. Este artigo é um tutorial, pelo que não abrange os requisitos de negócio, o planeamento nem as orientações sobre a arquitetura.

>[!NOTE]
>Se utiliza a Cloud Microsoft Azure Government, utilize a [documentação Monitorização + Gestão do Azure Government](https://docs.microsoft.com/azure/azure-government/documentation-government-services-monitoringandmanagement#log-analytics) em alternativa.

Segue-se uma breve perspetiva do processo utilizado para começar a trabalhar:

![diagrama do processo](./media/log-analytics-get-started/onboard-oms.png)

## <a name="1-create-an-azure-account-and-sign-in"></a>1 Criar uma conta do Azure e iniciar sessão

Se ainda não tem uma conta do Azure, precisa de criar uma para utilizar o Log Analytics. Pode criar uma [conta gratuita](https://azure.microsoft.com/free/), válida durante 30 dias, que lhe permite aceder a qualquer serviço do Azure.

### <a name="to-create-a-free-account-and-sign-in"></a>Para criar uma conta gratuita e iniciar sessão
1. Siga as instruções apresentadas em [Criar a sua conta do Azure gratuita](https://azure.microsoft.com/free/).
2. Aceda ao [Portal do Azure](https://portal.azure.com) e inicie sessão.

## <a name="2-create-a-workspace"></a>2 Criar uma área de trabalho

O passo seguinte é criar uma área de trabalho.

1. No portal do Azure, procure o *Log Analytics* na lista de serviços em Marketplace e, em seguida, selecione **Log Analytics**.  
    ![Portal do Azure](./media/log-analytics-get-started/log-analytics-portal.png)
2. Clique em **Criar** e, em seguida, selecione opções para os seguintes itens:
   * **Área de Trabalho do OMS** - Escreva um nome para a área de trabalho.
   * **Subscrição** - Se tiver várias subscrições, selecione a que quer associar à nova área de trabalho.
   * **Grupo de recursos**
   * **Localização**
   * **Escalão de preço**  
       ![criação rápida](./media/log-analytics-get-started/oms-onboard-quick-create.png)
3. Clique em **OK** para ver uma lista das suas áreas de trabalho.
4. Selecione uma área de trabalho para ver os detalhes da mesma no portal do Azure.       
    ![detalhes da área de trabalho](./media/log-analytics-get-started/oms-onboard-workspace-details.png)         

## <a name="3-add-solutions-and-solution-offerings"></a>3 Adicionar soluções e ofertas de solução

Em seguida, adicione soluções de gestão e ofertas de solução. As soluções de gestão são uma coleção de regras de lógica, visualização e aquisição de dados que fornecem métricas que giram em torno de uma área de problemas específica. Uma oferta de solução é um pacote de soluções de gestão.

Ao adicionar soluções à sua área de trabalho, permite que o Log Analytics recolha vários tipos de dados a partir dos computadores que estão ligados à sua área de trabalho através de agentes. O carregamento de agentes será abordado mais adiante.

### <a name="to-add-solutions-and-solution-offerings"></a>Para adicionar soluções e ofertas de solução

1. No portal do Azure, clique em **Novo** e, na caixa **Pesquisar no marketplace**, escreva **Log Analytics da Atividade** e, em seguida, prima ENTER.
2. No painel Tudo, selecione **Log Analytics da Atividade** e, em seguida, clique em **Criar**.  
    ![Log Analytics da Atividade](./media/log-analytics-get-started/activity-log-analytics.png)  
3. No painel *nome da solução de gestão*, selecione uma área de trabalho que queira associar à solução de gestão.
4. Clique em **Criar**.  
    ![área de trabalho da solução](./media/log-analytics-get-started/solution-workspace.png)  
5. Repita os passos 1 a 4 para adicionar:
    - A oferta de serviço **Segurança e Compatibilidade** com as soluções Avaliação do Antimalware e Segurança e Auditoria.
    - A oferta de serviço **Automatização e Controlo** com as soluções Função de Trabalho Híbrida de Automatização, Controlo de Alterações e Avaliação da Atualização do Sistema (também designada por Gestão de Atualizações). Tem de criar uma Conta de automatização quando adicionar a oferta de solução.  
        ![Conta de automatização](./media/log-analytics-get-started/automation-account.png)  
6. Para ver as soluções de gestão que adicionou à sua área de trabalho, navegue para **Log Analytics** > **Subscrições** > ***nome da área de trabalho*** > **Descrição Geral**. São apresentados mosaicos para as soluções de gestão adicionadas.  
    >[!NOTE]
    >Como ainda não foram ligados quaisquer agentes à área de trabalho, não verá quaisquer dados para as soluções que adicionou.  

    ![mosaicos de solução sem dados](./media/log-analytics-get-started/solutions-no-data.png)

## <a name="4-create-a-vm-and-onboard-an-agent"></a>4 Criar uma VM e carregar um agente

Em seguida, crie uma máquina virtual simples no Azure. Depois de criar uma VM, carregue o agente do OMS para a ativar. A ativação do agente inicia a recolha de dados da VM e envia os dados para o Log Analytics.

### <a name="to-create-a-virtual-machine"></a>Para criar uma máquina virtual

- Siga as instruções apresentadas em [Criar a sua primeira máquina virtual do Windows no portal do Azure](../virtual-machines/virtual-machines-windows-hero-tutorial.md) e inicie a máquina virtual nova.

### <a name="connect-the-virtual-machine-to-log-analytics"></a>Ligar a máquina virtual ao Log Analytics

- Siga as instruções apresentadas em [Ligar máquinas virtuais do Azure ao Log Analytics](log-analytics-azure-vm-extension.md) para ligar a VM ao Log Analytics com o portal do Azure.

## <a name="5-view-and-act-on-data"></a>5 Ver e tomar medidas relacionadas com os dados

Há pouco, ativou a solução Log Analytics da Atividade e as ofertas de serviço Segurança e Compatibilidade e Automatização e Controlo. A seguir, vamos dar início à análise dos dados recolhidos pelas soluções e dos resultados apresentados nas pesquisas de registos.

Para começar, olhe para os dados apresentados dentro das próprias soluções. A seguir, olhe para algumas das pesquisas de registos que são acedidas a partir das pesquisas de registos. As pesquisas de registos permitem combinar e relacionar quaisquer dados do computador provenientes de várias origens no seu ambiente. Para obter mais informações, consulte [Pesquisas de registos no Log Analytics](log-analytics-log-searches.md). Por último, tome medidas relacionadas com os dados encontrados através do portal do OMS, que é externo ao portal do Azure.

### <a name="to-view-antimalware-data"></a>Para ver os dados Antimalware

1. No portal do Azure, navegue para **Log Analytics** > ***a sua área de trabalho***.
2. No painel da sua área de trabalho, em **Geral**, clique em **Descrição Geral**.  
    ![Descrição geral](./media/log-analytics-get-started/overview.png)
3. Clique no mosaico **Avaliação do Antimalware**. Neste exemplo, pode constatar que o Windows Defender está instalado num computador, mas a assinatura deste está desatualizada.  
    ![Antimalware](./media/log-analytics-get-started/solution-antimalware.png)
4. Para este exemplo, em **Estado de proteção**, clique em **Assinatura desatualizada** para abrir a Pesquisa de Registos e ver detalhes sobre os computadores com assinaturas desatualizadas. Neste exemplo, repare que o computador tem o nome *getstarted*. Se fossem encontrados vários computadores com assinaturas desatualizadas, todos eles seriam apresentados nos resultados da Pesquisa de Registos.  
    ![Antimalware desatualizado](./media/log-analytics-get-started/antimalware-search.png)

### <a name="to-view-security-and-audit-data"></a>Para ver dados de Segurança e Auditoria

1. No painel da sua área de trabalho, em **Geral**, clique em **Descrição Geral**.  
2. Clique no mosaico **Segurança e Auditoria**. Neste exemplo, pode constatar que existem dois problemas evidentes: há um computador que tem atualizações críticas em falta e há um outro que não tem proteção suficiente.  
    ![Auditoria e segurança](./media/log-analytics-get-started/security-audit.png)
3. Para este exemplo, em **Problemas Evidentes**, clique em **Computadores com atualizações críticas em falta** para abrir a Pesquisa de Registos e ver detalhes sobre os computadores que têm atualizações críticas em falta. Neste exemplo, há uma atualização crítica em falta e 63 outras atualizações em falta.  
    ![Pesquisa de Registos de Segurança e Auditoria](./media/log-analytics-get-started/security-audit-log-search.png)

### <a name="to-view-and-act-on-system-update-data"></a>Para ver e tomar medidas relacionadas com os dados de Atualização do Sistema

1. No painel da sua área de trabalho, em **Geral**, clique em **Descrição Geral**.  
2. Clique no mosaico **Avaliação da Atualização do Sistema**. Neste exemplo, pode constatar que há um computador Windows com o nome *getstarted* que precisa de atualizações críticas e um outro que precisa de atualizações de definições.  
    ![Atualizações do Sistema](./media/log-analytics-get-started/system-updates.png)
3. Para este exemplo, em **Atualizações em Falta**, clique em **Atualizações Críticas** para abrir a Pesquisa de Registos e ver detalhes sobre os computadores com atualizações críticas em falta. Neste exemplo, há uma atualização em falta e uma atualização necessária.  
    ![Pesquisa de Registos de Atualizações do Sistema](./media/log-analytics-get-started/system-updates-log-search.png)
4. Aceda ao site do [Operations Management Suite](http://microsoft.com/oms) e inicie sessão com a sua conta do Azure. Depois de iniciar sessão, repare na semelhança entre as informações da solução e o que viu no portal do Azure.  
    ![Portal do OMS](./media/log-analytics-get-started/oms-portal.png)
5. Clique no mosaico **Gestão de Atualizações**.
6. No dashboard Gestão de Atualizações, repare na semelhança entre as informações de atualização do sistema e as informações da Atualização do Sistema que viu no portal do Azure. No entanto, o mosaico **Gerir Implementações de Atualizações** é novo. Clique no mosaico **Gerir Implementações de Atualizações**.  
    ![Mosaico Gestão de Atualizações](./media/log-analytics-get-started/update-management.png)
7. Na página **Implementações de Atualizações**, clique em **Adicionar** para criar uma *execução da atualização*.  
    ![Implementações de Atualizações](./media/log-analytics-get-started/update-management-update-deployments.png)
8.  Na página **Nova Implementação da Atualização**, escreva um nome para a implementação da atualização, selecione os computadores a atualizar (neste exemplo, *getstarted*), selecione uma agenda e, em seguida, clique em **Guardar**.  
    ![Nova Implementação](./media/log-analytics-get-started/new-deployment.png)  
    Depois de guardar a implementação da atualização, verá a atualização agendada.  
    ![atualização agendada](./media/log-analytics-get-started/scheduled-update.png)  
    Depois de concluída a execução da atualização, o estado mostrado é **Concluído**.
    ![atualização concluída](./media/log-analytics-get-started/completed-update.png)
9. Após a execução da atualização estar concluída, pode ver se a execução foi ou não bem-sucedida e ver detalhes sobre as atualizações aplicadas.

## <a name="after-evaluation"></a>Após a avaliação

Neste tutorial, instalou um agente numa máquina virtual e começou logo a trabalhar. Os passos que seguiu foram rápidos e simples. No entanto, a maior parte das organizações e empresas de grande dimensão têm infraestruturas de TI no local complexas. Por isso, a recolha de dados nesses ambientes complexos envolve um planeamento e esforço mais aprofundados do que o demonstrado neste tutorial. Reveja as informações na secção Passos seguintes a seguir para obter ligações para artigos úteis.

Opcionalmente, pode remover a área de trabalho que criou com este tutorial.

## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre como ligar [Agentes Windows](log-analytics-windows-agents.md) ao Log Analytics.
* Saiba mais sobre como ligar [Agentes do Operations Manager](log-analytics-om-agents.md) ao Log Analytics.
* [Adicionar soluções do Log Analytics a partir da Galeria de Soluções](log-analytics-add-solutions.md) para adicionar funcionalidade e recolher dados.
* Familiarize-se com as [pesquisas de registos](log-analytics-log-searches.md) para ver informações detalhadas recolhidas pelas soluções.


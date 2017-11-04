---
title: "Recolher dados sobre máquinas virtuais do Azure | Microsoft Docs"
description: "Saiba como ativar a extensão de VM de agente do OMS e ativar a recolha de dados das suas VMs do Azure com a análise de registos."
services: log-analytics
documentationcenter: log-analytics
author: MGoedtel
manager: carmonm
editor: 
ms.assetid: 
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 09/20/2017
ms.author: magoedte
ms.custom: mvc
ms.openlocfilehash: 2dec744b512a86a30cec1f334e265572fa7acc3e
ms.sourcegitcommit: e6029b2994fa5ba82d0ac72b264879c3484e3dd0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2017
---
# <a name="collect-data-about-azure-virtual-machines"></a>Recolher dados sobre máquinas virtuais do Azure
[Análise de registos do Azure](log-analytics-overview.md) pode recolher dados diretamente a partir de máquinas virtuais do Azure e outros recursos no seu ambiente para um único repositório para uma análise detalhada e da correlação.  Este guia de introdução mostra como configurar e recolher dados a partir do Linux do Azure ou VMs do Windows com alguns passos simples.  
 
Este guia de introdução pressupõe que tem uma máquina virtual do Azure existente. Se não puder [criar uma VM do Windows](../virtual-machines/windows/quick-create-portal.md) ou [criar uma VM com Linux](../virtual-machines/linux/quick-create-cli.md) seguir nosso inícios rápidos VM.

## <a name="log-in-to-azure-portal"></a>Inicie sessão no portal do Azure
Inicie sessão no portal do Azure em [https://portal.azure.com](https://portal.azure.com). 

## <a name="create-a-workspace"></a>Criar áreas de trabalho
1. No portal do Azure, clique em **mais serviços** encontrado no canto inferior esquerdo. Na lista de recursos, escreva **Log Analytics**. À medida que começa a escrever, a lista filtra com base na sua entrada. Selecione **Iniciar análise**.<br> ![Portal do Azure](media/log-analytics-quick-collect-azurevm/azure-portal-01.png)<br>  
2. Clique em **criar**e, em seguida, selecione as opções para os seguintes itens:

  * Forneça um nome para o novo **área de trabalho OMS**, tais como *DefaultLAWorkspace*. 
  * Selecione uma **Subscrição** à qual ligar ao escolher na lista pendente se a predefinição selecionada não é adequada.
  * Para **grupo de recursos**, selecione um grupo de recursos existente que contém um ou mais máquinas virtuais do Azure.  
  * Selecione o **localização** as suas VMs são implementadas.  Para obter mais informações, consulte o artigo que [regiões análise de registos está disponível no](https://azure.microsoft.com/regions/services/).
  * Pode escolher entre três diferentes **escalões de preço** na análise de registos, mas para este guia de introdução que vai selecionar o **livre** camada.  Para obter informações adicionais sobre as camadas específicas, consulte [detalhes de preços de análise do registo](https://azure.microsoft.com/pricing/details/log-analytics/).

        ![Create Log Analytics resource blade](./media/log-analytics-quick-collect-azurevm/create-loganalytics-workspace-01.png)<br>  
3. Depois de fornecer as informações necessárias sobre o **área de trabalho OMS** painel, clique em **OK**.  

Enquanto as informações são confirmadas e a área de trabalho criada, pode acompanhar o progresso em **Notificações**, no menu. 

## <a name="enable-the-log-analytics-vm-extension"></a>Ativar a extensão VM de análise do registo
Para Windows e Linux máquinas virtuais já implementadas no Azure, instale o agente de análise de registos com a extensão de VM de análise do registo.  Com a extensão simplifica o processo de instalação e configura automaticamente o agente para enviar dados para a área de trabalho de análise de registos que especificar. O agente é também atualizado automaticamente, assegurando que tem as funcionalidades e correções mais recentes.

Poderá reparar a faixa na parte superior da sua página de recursos de análise de registos no portal do inviting a atualizar.  A atualização não é necessária para efeitos deste guia de introdução.<br>

![Análise de registos atualizar aviso no portal do Azure](media/log-analytics-quick-collect-azurevm/log-analytics-portal-upgradebanner.png).    
1. No portal do Azure, clique em **mais serviços** encontrado no canto inferior esquerdo. Na lista de recursos, escreva **Log Analytics**. À medida que começa a escrever, a lista filtra com base na sua entrada. Selecione **Iniciar análise**.
2. Na lista de áreas de trabalho de análise de registos, selecione *DefaultLAWorkspace* criada anteriormente.
3. No menu da esquerda, em origens de dados da área de trabalho, clique em **máquinas virtuais**.  
4. Na lista de **máquinas virtuais**, selecione uma máquina virtual que pretende instalar o agente. Tenha em atenção que o **estado de ligação do OMS** para a VM indica que é **não ligado**.
5. Nos detalhes para a máquina virtual, selecione **Connect**. O agente é automaticamente instalado e configurado para a área de trabalho de análise de registos. Este processo demora alguns minutos, durante o período de tempo a **estado** é **ligação**.
6. Depois de instalar e ligar o agente, o **estado de ligação do OMS** será atualizada com **esta área de trabalho**.

## <a name="collect-event-and-performance-data"></a>Recolher dados de eventos e desempenho
Análise de registos pode recolher eventos dos registos de eventos do Windows ou Linux Syslog e contadores de desempenho que especificar para mais análises de termo e relatórios e tomar medidas, quando é detetada uma condição específica.  Siga estes passos para configurar a recolha de eventos de registo do sistema Windows e Linux Syslog e vários contadores de desempenho comuns para começar.  

### <a name="data-collection-from-windows-vm"></a>Recolha de dados da VM do Windows
1. Selecione **definições avançadas**.<br> ![Definições avançadas da análise de registo](media/log-analytics-quick-collect-azurevm/log-analytics-advanced-settings-01.png)<br> 
3. Selecione **dados**e, em seguida, selecione **registos de eventos do Windows**.  
4. Adicione um registo de eventos, escrevendo o nome do registo.  Tipo **sistema** e, em seguida, clique no sinal  **+** .  
5. Na tabela, verifique as gravidades **erro** e **aviso**.   
6. Clique em **guardar** na parte superior da página para guardar a configuração.
7. Selecione **dados de desempenho do Windows** para ativar a recolha de contadores de desempenho num computador Windows. 
8. Quando configurar primeiro os contadores de desempenho do Windows para uma nova área de trabalho de análise de registos, é-lhe dada a opção para criar rapidamente vários contadores comuns. São listados com uma caixa de verificação junto a cada.<br> ![Contadores de desempenho do Windows predefinido selecionados](media/log-analytics-quick-collect-azurevm/windows-perfcounters-default.png).<br> Clique em **adicionar os contadores de desempenho selecionados**.  Estes são adicionados e com um intervalo de amostra de recolha de dez segundo da configuração predefinidas.  
9. Clique em **guardar** na parte superior da página para guardar a configuração.

### <a name="data-collection-from-linux-vm"></a>Recolha de dados da VM com Linux

1. Selecione **Syslog**.  
2. Adicione um registo de eventos, escrevendo o nome do registo.  Tipo **Syslog** e, em seguida, clique no sinal  **+** .  
3. Na tabela, desmarque as gravidades **informações**, **aviso** e **depurar**. 
4. Clique em **guardar** na parte superior da página para guardar a configuração.
5. Selecione **dados de desempenho do Linux** para ativar a recolha de contadores de desempenho num computador Windows. 
6. Quando configurar primeiro os contadores de desempenho do Linux para uma nova área de trabalho de análise de registos, é-lhe dada a opção para criar rapidamente vários contadores comuns. São listados com uma caixa de verificação junto a cada.<br> ![Contadores de desempenho do Windows predefinido selecionados](media/log-analytics-quick-collect-azurevm/linux-perfcounters-default.png).<br> Clique em **adicionar os contadores de desempenho selecionados**.  Estes são adicionados e com um intervalo de amostra de recolha de dez segundo da configuração predefinidas.  
7. Clique em **guardar** na parte superior da página para guardar a configuração.

## <a name="view-data-collected"></a>Visualizar os dados recolhidos
Agora que ativou a recolha de dados, permite executar um exemplo de pesquisa de registo simples para ver alguns dados a partir de VMs de destino.  

1. No portal do Azure, navegue até à análise de registos e selecione a área de trabalho criada anteriormente.
2. Clique em de **pesquisa registo** mosaico e no painel de pesquisa de registo, no tipo de campo de consulta `Type=Perf` e, em seguida, prima introduza ou clique no botão de procura para a direita do campo de consulta.<br> ![Exemplo de consulta de pesquisa de registo de análise de registos](./media/log-analytics-quick-collect-azurevm/log-analytics-portal-queryexample.png)<br> Por exemplo, a consulta na imagem seguinte devolveu 78,000 registos de desempenho.  Os resultados será significativamente inferior.<br> ![Resultado da pesquisa de registo de análise de registos](media/log-analytics-quick-collect-azurevm/log-analytics-search-perf.png)

## <a name="clean-up-resources"></a>Limpar recursos
Quando já não é necessário elimine a área de trabalho de análise de registos. Para tal, selecione a área de trabalho de análise de registos que criou anteriormente e clique de página recursos **eliminar**.<br> ![Eliminar recurso de análise de registos](media/log-analytics-quick-collect-azurevm/log-analytics-portal-delete-resource.png)

## <a name="next-steps"></a>Passos seguintes
Agora que está a recolher operacional e dados de desempenho das suas máquinas de virtuais do Windows ou Linux, pode facilmente começar a explorar, analisar e ação de colocar em dados que recolhe para *livre*.  

Para saber como ver e analisar os dados, avance para o tutorial.   

> [!div class="nextstepaction"]
> [Ver ou analisar dados de análise de registos](log-analytics-tutorial-viewdata.md)

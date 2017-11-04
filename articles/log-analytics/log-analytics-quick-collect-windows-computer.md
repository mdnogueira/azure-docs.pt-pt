---
title: Recolher dados de computadores com Windows no local com o Log Analytics do Azure | Microsoft Docs
description: "Saiba como implementar o agente de análise de registos para o Windows em execução nos computadores fora do Azure e ativar a recolha de dados com a análise de registos."
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
ms.date: 10/16/2017
ms.author: magoedte
ms.custom: mvc
ms.openlocfilehash: 6da36184baee921c828b037e1337df2d14c79462
ms.sourcegitcommit: e6029b2994fa5ba82d0ac72b264879c3484e3dd0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2017
---
# <a name="collect-data-from-windows-computers-hosted-in-your-environment"></a>Recolher dados de computadores com o Windows alojados no seu ambiente
[Análise de registos do Azure](log-analytics-overview.md) pode recolher dados diretamente a partir de computadores físicos ou virtuais Windows e outros recursos no seu ambiente para um único repositório para uma análise detalhada e da correlação.  Este guia de introdução mostra como configurar e recolher dados a partir do seu computador Windows com alguns passos simples.  Para VMs do Windows Azure, consulte o tópico seguinte [recolher dados sobre máquinas virtuais do Azure](log-analytics-quick-collect-azurevm.md).  
 
Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="log-in-to-azure-portal"></a>Inicie sessão no portal do Azure
Inicie sessão no portal do Azure em [https://portal.azure.com](https://portal.azure.com). 

## <a name="create-a-workspace"></a>Criar áreas de trabalho
1. No portal do Azure, clique em **mais serviços** encontrado no canto inferior esquerdo. Na lista de recursos, escreva **Log Analytics**. À medida que começa a escrever, a lista filtra com base na sua entrada. Selecione **Iniciar análise**.<br><br> ![Portal do Azure](media/log-analytics-quick-collect-azurevm/azure-portal-01.png)<br><br>  
2. Clique em **criar**e, em seguida, selecione as opções para os seguintes itens:

  * Forneça um nome para o novo **área de trabalho OMS**, tais como *DefaultLAWorkspace*. 
  * Selecione uma **Subscrição** à qual ligar ao escolher na lista pendente se a predefinição selecionada não é adequada.
  * Para **grupo de recursos**, selecione um grupo de recursos existente que contém um ou mais máquinas virtuais do Azure.  
  * Selecione o **localização** as suas VMs são implementadas.  Para obter mais informações, consulte o artigo que [regiões análise de registos está disponível no](https://azure.microsoft.com/regions/services/).
  * Pode escolher entre três diferentes **escalões de preço** na análise de registos, mas para este guia de introdução que vai selecionar o **livre** camada.  Para obter informações adicionais sobre as camadas específicas, consulte [detalhes de preços de análise do registo](https://azure.microsoft.com/pricing/details/log-analytics/).

        ![Create Log Analytics resource blade](media/log-analytics-quick-collect-azurevm/create-loganalytics-workspace-01.png)<br>  
3. Depois de fornecer as informações necessárias sobre o **área de trabalho OMS** painel, clique em **OK**.  

Enquanto as informações são confirmadas e a área de trabalho criada, pode acompanhar o progresso em **Notificações**, no menu. 

## <a name="obtain-workspace-id-and-key"></a>Obter ID da área de trabalho e a chave
Antes de instalar o Microsoft monitorização do agente do Windows, terá do ID da área de trabalho e a chave para a sua área de trabalho de análise de registos.  Esta informação é necessária para o Assistente de configuração para configurar o agente e certifique-se de que consegue comunicar com êxito com a análise de registos de corretamente.  

1. No portal do Azure, clique em **mais serviços** encontrado no canto inferior esquerdo. Na lista de recursos, escreva **Log Analytics**. À medida que começa a escrever, a lista filtra com base na sua entrada. Selecione **Iniciar análise**.
2. Na lista de áreas de trabalho de análise de registos, selecione *DefaultLAWorkspace* criada anteriormente.
3. Selecione **definições avançadas**.<br><br> ![Definições avançadas da análise de registo](media/log-analytics-quick-collect-azurevm/log-analytics-advanced-settings-01.png)<br><br>  
4. Selecione **origens ligadas**e, em seguida, selecione **servidores Windows**.   
5. O valor para a direita da **ID da área de trabalho** e **chave primária**. Copie e cole-o no seu editor favorito.   

## <a name="install-the-agent-for-windows"></a>Instalar o agente para Windows
Os seguintes passos: instalar e configurar o agente de análise de registos na nuvem do Azure e Azure Government utilizando a configuração do Microsoft Monitoring Agent no seu computador.  

1. No **servidores Windows** página, selecione as adequadas **transferir o agente do Windows** versão transferir consoante a arquitetura de processador do sistema operativo Windows.
2. Execute a configuração para instalar o agente no seu computador.
2. No **boas-vindas** página, clique em **seguinte**.
3. No **termos de licenciamento** página, leia a licença e, em seguida, clique em **concordo**.
4. No **pasta de destino** página, altere ou mantenha a pasta de instalação predefinida e, em seguida, clique em **seguinte**.
5. No **opções de configuração do agente** página, escolha ligar o agente para análise de registos do Azure (OMS) e, em seguida, clique em **seguinte**.   
6. No **Log Analytics do Azure** página, efetue o seguinte:
   1. Colar o **ID da área de trabalho** e **chave da área de trabalho (chave primária)** que copiou anteriormente.  Se o computador deve reportar uma área de trabalho de análise de registos na nuvem do Azure Government, selecione **Azure US Government** do **nuvem do Azure** na lista pendente.  
   2. Se o computador tem de comunicar através de um servidor proxy para o serviço de análise de registos, clique em **avançadas** e forneça o URL e o número de porta do servidor proxy.  Se o servidor proxy requer autenticação, escreva o nome de utilizador e palavra-passe para autenticar com o servidor proxy e, em seguida, clique em **seguinte**.  
7. Clique em **seguinte** depois de concluir a fornecer as definições de configuração necessárias.<br><br> ![Cole o ID da área de trabalho e a chave primária](media/log-analytics-quick-collect-windows-computer/log-analytics-mma-setup-laworkspace.png)<br><br>
8. No **pronto para instalar** página, reveja as suas opções e, em seguida, clique em **instalar**.
9. No **configuração concluída com sucesso** página, clique em **concluir**.

Quando terminar, o **Microsoft Monitoring Agent** aparece no **painel de controlo**. Pode rever a configuração e certifique-se de que o agente está ligado à análise de registos. Quando estiver ligado, no **análise de registos do Azure (OMS)** separador, o agente apresenta uma mensagem a indicar: **o Microsoft Monitoring Agent foi ligado com êxito para o serviço do Microsoft Operations Management Suite.**<br><br> ![Estado da ligação MMA ao Log Analytics](media/log-analytics-quick-collect-windows-computer/log-analytics-mma-laworkspace-status.png)

## <a name="collect-event-and-performance-data"></a>Recolher dados de eventos e desempenho
Análise de registos pode recolher eventos do registo de eventos do Windows e os contadores de desempenho que especificar para mais análises de termo e relatórios e tomar medidas, quando é detetada uma condição específica.  Siga estes passos para configurar a recolha de eventos de registo de eventos do Windows e vários contadores de desempenho comuns para começar.  

1. No portal do Azure, clique em **mais serviços** encontrado no canto inferior esquerdo. Na lista de recursos, escreva **Log Analytics**. À medida que começa a escrever, a lista filtra com base na sua entrada. Selecione **Iniciar análise**.
2. Selecione **definições avançadas**.<br><br> ![Definições avançadas da análise de registo](media/log-analytics-quick-collect-azurevm/log-analytics-advanced-settings-01.png)<br><br> 
3. Selecione **dados**e, em seguida, selecione **registos de eventos do Windows**.  
4. Adicione um registo de eventos, escrevendo o nome do registo.  Tipo **sistema** e, em seguida, clique no sinal  **+** .  
5. Na tabela, verifique as gravidades **erro** e **aviso**.   
6. Clique em **guardar** na parte superior da página para guardar a configuração.
7. Selecione **dados de desempenho do Windows** para ativar a recolha de contadores de desempenho num computador Windows. 
8. Quando configurar primeiro os contadores de desempenho do Windows para uma nova área de trabalho de análise de registos, é-lhe dada a opção para criar rapidamente vários contadores comuns. São listados com uma caixa de verificação junto a cada.<br> ![Contadores de desempenho do Windows predefinido selecionados](media/log-analytics-quick-collect-azurevm/windows-perfcounters-default.png).<br> Clique em **adicionar os contadores de desempenho selecionados**.  Estes são adicionados e com um intervalo de amostra de recolha de dez segundo da configuração predefinidas.  
9. Clique em **guardar** na parte superior da página para guardar a configuração.

## <a name="view-data-collected"></a>Visualizar os dados recolhidos
Agora que ativou a recolha de dados, permite executar um exemplo de pesquisa de registo simples para ver alguns dados a partir do computador de destino.  

1. No portal do Azure, na área de trabalho selecionado, clique em de **pesquisa registo** mosaico.  
2. No painel de pesquisa de registo, no tipo de campo de consulta `Perf` e, em seguida, prima introduza ou clique no botão de procura para a direita do campo de consulta.<br><br> ![Exemplo de consulta de pesquisa de registo de análise de registos](media/log-analytics-quick-collect-linux-computer/log-analytics-portal-queryexample.png)<br><br> Por exemplo, a consulta na imagem seguinte devolveu 735 registos de desempenho.<br><br> ![Resultado da pesquisa de registo de análise de registos](media/log-analytics-quick-collect-windows-computer/log-analytics-search-perf.png)

## <a name="clean-up-resources"></a>Limpar recursos
Quando já não é necessário, pode remover o agente do computador Windows e eliminar a área de trabalho de análise de registos.  

Para remover o agente, execute os seguintes passos.

1. Abra o **Painel de Controlo**.
2. Abra **programas e funcionalidades**.
3. No **programas e funcionalidades**, selecione **Microsoft Monitoring Agent** e clique em **desinstalação**.

Para eliminar a área de trabalho, selecione a área de trabalho de análise de registos que criou anteriormente e clique de página recursos **eliminar**.<br><br> ![Eliminar recurso de análise de registos](media/log-analytics-quick-collect-azurevm/log-analytics-portal-delete-resource.png)

## <a name="next-steps"></a>Passos seguintes
Agora que está a recolher operacional e dados de desempenho do computador Linux no local, pode facilmente começar a explorar, analisar e ação de colocar em dados que recolhe para *livre*.  

Para saber como ver e analisar os dados, avance para o tutorial.   

> [!div class="nextstepaction"]
> [Ver ou analisar dados de análise de registos](log-analytics-tutorial-viewdata.md)

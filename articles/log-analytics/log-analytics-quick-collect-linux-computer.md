---
title: Recolher dados de computadores com Linux no local com o Log Analytics do Azure | Microsoft Docs
description: "Saiba como implementar o agente de análise de registos para Linux e ativar a recolha de dados a partir desse SO com a análise de registos."
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
ms.date: 10/13/2017
ms.author: magoedte
ms.custom: mvc
ms.openlocfilehash: d22fe6456c3bd886f8f8863d362c0084fbe03da3
ms.sourcegitcommit: e6029b2994fa5ba82d0ac72b264879c3484e3dd0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2017
---
# <a name="collect-data-from-linux-computers-hosted-in-your-environment"></a>Recolher dados de computadores Linux alojados no seu ambiente
[Análise de registos do Azure](log-analytics-overview.md) pode recolher dados diretamente a partir de computadores físicos ou virtuais Linux e outros recursos no seu ambiente para um único repositório para uma análise detalhada e da correlação.  Este guia de introdução mostra como configurar e recolher dados a partir do seu computador Linux com alguns passos simples.  Para as VMs Linux do Azure, consulte o tópico seguinte [recolher dados sobre máquinas virtuais do Azure](log-analytics-quick-collect-azurevm.md).  
 
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

        ![Create Log Analytics resource blade](./media/log-analytics-quick-collect-azurevm/create-loganalytics-workspace-01.png)<br>  
3. Depois de fornecer as informações necessárias sobre o **área de trabalho OMS** painel, clique em **OK**.  

Enquanto as informações são confirmadas e a área de trabalho criada, pode acompanhar o progresso em **Notificações**, no menu. 

## <a name="obtain-workspace-id-and-key"></a>Obter ID da área de trabalho e a chave
Antes de instalar o agente do OMS para Linux, tem do ID da área de trabalho e a chave para a sua área de trabalho de análise de registos.  Esta informação é necessária pelo agente wrapper script corretamente configurar o agente e certifique-se de que consegue comunicar com êxito com a análise de registos.  

1. No portal do Azure, clique em **mais serviços** encontrado no canto inferior esquerdo. Na lista de recursos, escreva **Log Analytics**. À medida que começa a escrever, a lista filtra com base na sua entrada. Selecione **Iniciar análise**.
2. Na lista de áreas de trabalho de análise de registos, selecione *DefaultLAWorkspace* criada anteriormente.
3. Selecione **definições avançadas**.<br><br> ![Definições avançadas da análise de registo](media/log-analytics-quick-collect-azurevm/log-analytics-advanced-settings-01.png)<br><br>  
4. Selecione **origens ligadas**e, em seguida, selecione **servidores Linux**.   
5. O valor para a direita da **ID da área de trabalho** e **chave primária**. Copie e cole-o no seu editor favorito.   

## <a name="install-the-agent-for-linux"></a>Instalar o agente para Linux
Os seguintes passos configurar a configuração do agente para análise de registos na nuvem do Azure e Azure Government.  

1. Para configurar o computador com Linux para ligar ao Log Analytics, execute o seguinte comando fornecer o ID da área de trabalho e a chave primária que copiou anteriormente.  Este comando transfere o agente, valida a soma de verificação e instala-o. 
    
    ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY>
    ```

2. Para configurar o computador com Linux para ligar ao Log Analytics na nuvem do Azure Government, execute o seguinte comando fornecer o ID da área de trabalho e a chave primária que copiou anteriormente.  Este comando transfere o agente, valida a soma de verificação e instala-o. 

    ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY> -d opinsights.azure.us
    ``` 

## <a name="configure-agent-to-communicate-with-a-proxy-server"></a>Configurar o agente para comunicar com um servidor proxy

Execute os passos seguintes se os computadores Linux têm de comunicar através de um servidor proxy para análise de registos.  O valor de configuração de proxy tem a seguinte sintaxe `[protocol://][user:password@]proxyhost[:port]`.

1. Edite o ficheiro `/etc/opt/microsoft/omsagent/proxy.conf` executando os comandos seguintes e alterar os valores para as definições específicas.

    ```
    proxyconf="https://proxyuser:proxypassword@proxyserver01:30443"
    sudo echo $proxyconf >>/etc/opt/microsoft/omsagent/proxy.conf
    sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/proxy.conf 
    ```

2. Reinicie o agente executando o seguinte comando: 

    ```
    sudo /opt/microsoft/omsagent/bin/service_control restart [<workspace id>]
    ``` 

## <a name="collect-event-and-performance-data"></a>Recolher dados de eventos e desempenho
Análise de registos pode recolher eventos do Linux Syslog e contadores de desempenho que especificar para mais análises de termo e relatórios e tomar medidas, quando é detetada uma condição específica.  Siga estes passos para configurar a recolha de eventos de Linux Syslog e vários contadores de desempenho comuns para começar.  

1. Selecione **Syslog**.  
2. Adicione um registo de eventos, escrevendo o nome do registo.  Tipo **Syslog** e, em seguida, clique no sinal  **+** .  
3. Na tabela, desmarque as gravidades **informações**, **aviso** e **depurar**. 
4. Clique em **guardar** na parte superior da página para guardar a configuração.
5. Selecione **dados de desempenho do Linux** para ativar a recolha de contadores de desempenho num computador Windows. 
6. Quando configurar primeiro os contadores de desempenho do Linux para uma nova área de trabalho de análise de registos, é-lhe dada a opção para criar rapidamente vários contadores comuns. São listados com uma caixa de verificação junto a cada.<br><br> ![Contadores de desempenho do Windows predefinido selecionados](media/log-analytics-quick-collect-azurevm/linux-perfcounters-default.png).<br><br> Clique em **adicionar os contadores de desempenho selecionados**.  Estes são adicionados e com um intervalo de amostra de recolha de dez segundo da configuração predefinidas.  
7. Clique em **guardar** na parte superior da página para guardar a configuração.

## <a name="view-data-collected"></a>Visualizar os dados recolhidos
Agora que ativou a recolha de dados, permite executar um exemplo de pesquisa de registo simples para ver alguns dados a partir do computador de destino.  

1. No portal do Azure, navegue até à análise de registos e selecione a área de trabalho criada anteriormente.
2. Clique em de **pesquisa registo** mosaico e no painel de pesquisa de registo, no tipo de campo de consulta `Perf` e, em seguida, prima introduza ou clique no botão de procura para a direita do campo de consulta.<br><br> ![Exemplo de consulta de pesquisa de registo de análise de registos](media/log-analytics-quick-collect-linux-computer/log-analytics-portal-queryexample.png)<br><br> Por exemplo, a consulta na imagem seguinte devolveu 735 registos de desempenho.<br><br> ![Resultado da pesquisa de registo de análise de registos](media/log-analytics-quick-collect-linux-computer/log-analytics-search-perf.png)

## <a name="clean-up-resources"></a>Limpar recursos
Quando já não é necessário, pode remover o agente do computador Linux e eliminar a área de trabalho de análise de registos.  

Para remover o agente, execute os seguintes passos.

1. Transferir o agente Linux [universal script](https://github.com/Microsoft/OMS-Agent-for-Linux/releases) ao computador.
2. Execute o w de ficheiro do pacote .sh o *– remover* argumento no computador, o que remove completamente a respetiva configuração e o agente.

    `sudo sh ./omsagent-<version>.universal.x64.sh --purge`

Para eliminar a área de trabalho, selecione a área de trabalho de análise de registos que criou anteriormente e clique de página recursos **eliminar**.<br><br> ![Eliminar recurso de análise de registos](media/log-analytics-quick-collect-azurevm/log-analytics-portal-delete-resource.png)

## <a name="next-steps"></a>Passos seguintes
Agora que está a recolher operacional e dados de desempenho do computador Linux no local, pode facilmente começar a explorar, analisar e ação de colocar em dados que recolhe para *livre*.  

Para saber como ver e analisar os dados, avance para o tutorial.   

> [!div class="nextstepaction"]
> [Ver ou analisar dados de análise de registos](log-analytics-tutorial-viewdata.md)

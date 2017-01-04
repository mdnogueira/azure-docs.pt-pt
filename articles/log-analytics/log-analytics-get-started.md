---
title: "Introdução ao Log Analytics | Microsoft Docs"
description: "Pode começar a trabalhar com o Log Analytics em minutos."
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
ms.date: 01/02/2017
ms.author: banders
translationtype: Human Translation
ms.sourcegitcommit: 49e624dd9bfc534fdbae25fd0c8646be36851635
ms.openlocfilehash: 4ab71b6ee09883abd4d095f2b1788cf69d44a219


---
# <a name="get-started-with-log-analytics"></a>Introdução ao Log Analytics
Pode ficar operacional com o Log Analytics no Microsoft Operations Management Suite (OMS) em apenas alguns minutos. Tem duas opções ao escolher como criar uma área de trabalho do OMS, que é semelhante a uma conta:

* Site do Microsoft Operations Management Suite
* Subscrição do Microsoft Azure

Pode criar uma área de trabalho do OMS gratuita através do Web site do OMS. Em alternativa, pode utilizar uma subscrição do Microsoft Azure para criar uma área de trabalho gratuita do Log Analytics. As áreas de trabalho criadas de qualquer forma são funcionalmente equivalentes. A áreas de trabalho gratuitas podem enviar apenas 500 MB de dados diários para o serviço OMS. Todas as áreas de trabalho necessitam de uma subscrição do Azure, para que possa utilizar a mesma para aceder a outros serviços do Azure. Independentemente do método que utilizar para criar a área de trabalho, terá de criar a mesma com uma conta Microsoft ou uma conta organizacional.

Eis o processo:

![diagrama de integração](./media/log-analytics-get-started/oms-onboard-diagram.png)

## <a name="log-analytics-prerequisites-and-deployment-considerations"></a>Pré-requisitos e considerações de implementação do Log Analytics
* Precisa de uma subscrição paga do Microsoft Azure para utilizar integralmente o Log Analytics. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) que funciona durante 30 dias e lhe permite aceder a qualquer serviço do Azure. Também pode criar uma conta gratuita do OMS no Web site do [Operations Management Suite ](http://microsoft.com/oms).
* Tem de criar uma área de trabalho
* Cada computador Windows de que queira recolher dados tem de executar o Windows Server 2008 SP1 ou versão superior
* Acesso de [firewall](log-analytics-proxy-firewall.md) aos endereços do serviço Web do OMS
* Determine se os computadores têm acesso direto à Internet. Se não tiverem, precisarão de um servidor de gateway para aceder aos sites do serviço Web do OMS. Todo o acesso é através de HTTPS. Pode configurar um servidor [Gateway de OMS](log-analytics-oms-gateway.md) para reencaminhar tráfego dos servidores para o OMS, caso o acesso à Internet não esteja disponível a partir dos computadores.
* Se utilizar o Operations Manager, o Log Analytics suporta o Operations Manager 2012 SP1 UR6 e versões superiores e o Operations Manager 2012 R2 UR2 e versões superiores. Foi adicionado suporte de proxy ao Operations Manager 2012 SP1 UR7 e ao Operations Manager 2012 R2 UR3. Determine a forma como o Operations Manager será integrado com o OMS.
* Determine que tecnologias e servidores enviarão dados para o OMS. Por exemplo, controladores de domínio, SQL Server, etc.
* Conceda aos utilizadores permissão no OMS e no Azure.
* Se está preocupado com a utilização de dados, implemente cada solução individualmente e teste o impacto no desempenho antes de adicionar mais soluções.
* Reveja o desempenho e a utilização dos dados à medida que adiciona funcionalidades e soluções ao Log Analytics. Isto inclui a recolha de eventos, recolha de registos, recolha de dados de desempenho, etc. É melhor começar com uma recolha mínima até que a utilização de dados ou o impacto no desempenho terem sido identificados.
* Certifique-se de que os agentes do Windows não são também geridos com o Operations Manager, pois, se forem, serão criados dados duplicados. Isto também se aplica aos agentes baseados no Azure que tenham o Diagnóstico do Azure ativado.
* Depois de instalar agentes, certifique-se de que o agente está a funcionar corretamente. Se não estiver, certifique-se de que o Isolamento da Chave da API Cryptography Next Generation (CNG) não está desativado através da Política de Grupo.
* Algumas soluções do Log Analytics têm requisitos adicionais

## <a name="sign-up-in-3-steps-using-oms"></a>Inscrever-se em três passos com o OMS
1. Ir para o Web site do [Operations Management Suite](http://microsoft.com/oms). Inicie sessão com a sua conta Microsoft, como o Outlook.com, ou com uma conta organizacional fornecida pela sua empresa ou instituição de ensino para utilizar com o Office 365 ou outros serviços Microsoft.
2. Forneça um nome de área de trabalho exclusivo. Uma área de trabalho é um contentor lógico em que são armazenados os seus dados de gestão. Esta permite-lhe organizar os seus dados em partições entre diferentes equipas na sua organização, pois os dados são exclusivos à área de trabalho. Especifique um endereço de e-mail e a região onde quer os seus dados armazenados.  
    ![criar a área de trabalho e uma ligação de subscrição](./media/log-analytics-get-started/oms-onboard-create-workspace-link01.png)
3. Em seguida, crie uma nova subscrição gratuita do Azure ou ligar a uma subscrição do Azure existente.  
   ![criar a área de trabalho e uma ligação de subscrição](./media/log-analytics-get-started/oms-onboard-create-workspace-link02.png)

Está pronto para começar a utilizar o portal do Operations Management Suite.

Para saber mais sobre como configurar a sua área de trabalho e ligar contas do Azure existentes a áreas de trabalho criadas com o Operations Management Suite, consulte [Manage workspaces (Gerir áreas de trabalho)](log-analytics-manage-access.md).

## <a name="sign-up-quickly-using-microsoft-azure"></a>Inscrição rápida através do Microsoft Azure
1. Aceda ao [portal do Azure](https://portal.azure.com) e inicie sessão, navegue na lista de serviços e, em seguida, selecione **Log Analytics**.  
    ![Portal do Azure](./media/log-analytics-get-started/oms-onboard-azure-portal.png)
2. Clique em **Adicionar** e selecione opções para os seguintes itens:
   * Nome da **Área de Trabalho do OMS**
   * **Subscrição** - Se tiver várias subscrições, selecione a que quer associar à nova área de trabalho.
   * **Grupo de recursos**
   * **Localização**
   * **Escalão de preço**  
       ![criação rápida](./media/log-analytics-get-started/oms-onboard-quick-create.png)
3. Clique em **OK** e verá uma lista das suas áreas de trabalho.
4. Selecione uma área de trabalho para ver os respetivos detalhes no portal do Azure.       
    ![detalhes da área de trabalho](./media/log-analytics-get-started/oms-onboard-workspace-details.png)         
5. Clique na ligação do **Portal do OMS** para abrir o site do Operations Management Suite com a nova área de trabalho.

Está pronto para começar a utilizar o portal do Operations Management Suite.

Para saber mais sobre como configurar a sua área de trabalho e ligar áreas de trabalho existentes criadas com o Operations Management Suite a subscrições do Azure, veja o artigo [Gerir o acesso ao Log Analytics](log-analytics-manage-access.md).

## <a name="get-started-with-the-operations-management-suite-portal"></a>Introdução ao portal do Operations Management Suite
Para escolher soluções e ligar os servidores que quer gerir, clique no mosaico **Definições** e siga os passos nesta secção.  

![introdução](./media/log-analytics-get-started/oms-onboard-get-started.png)  

1. **Adicionar Soluções** - Ver as soluções instaladas.  
    ![soluções](./media/log-analytics-get-started/oms-onboard-solutions.png)  
    Clique em **Visitar a Galeria** para adicionar mais soluções.  
    ![soluções](./media/log-analytics-get-started/oms-onboard-solutions02.png)  
    Selecione uma solução e clique em **Adicionar**.
2. **Ligar uma origem** - Escolha como quer ligar ao seu ambiente de servidor para recolher dados:

   * Ligue a qualquer cliente ou Windows Server diretamente ao instalar um agente.
   * Ligar servidores Linux com o Agente OMS para Linux.
   * Utilize uma conta de armazenamento do Azure configurada com a extensão VM de diagnóstico do Azure para Windows ou Linux.
   * Utilize o System Center Operations Manager para anexar os seus grupos de gestão ou toda a sua implementação do Operations Manager.
   * Ative a Telemetria do Windows para utilizar o Upgrade Analytics.
       ![origens ligadas](./media/log-analytics-get-started/oms-onboard-data-sources.png)    
3. **Recolher dados** Configure pelo menos uma origem de dados para preencher dados na sua área de trabalho. Quando terminar, clique em **Guardar**.    

    ![recolher dados](./media/log-analytics-get-started/oms-onboard-logs.png)    

## <a name="optionally-connect-windows-computers-by-installing-an-agent"></a>Opcionalmente, ligue os computadores com o Windows ao instalar um agente
O exemplo seguinte mostra como instalar um agente do Windows.

1. Clique no mosaico **Definições**, clique no separador **Origens Ligadas**, clique num separador para o tipo de origem que pretende adicionar e transfira um agente ou saiba mais sobre como ativar um agente. Por exemplo, clique em **Transferir o Agente do Windows (64 bits)**. Para agentes do Windows, só pode instalar o agente no Windows Server 2008 SP 1 ou versão posterior ou no Windows 7 SP1 ou versão posterior.
2. Instale o agente num ou mais servidores. Pode instalar agentes um de cada vez ou através de um método mais automatizado com um [script personalizado](log-analytics-windows-agents.md), ou pode utilizar uma solução de distribuição de software existente que possa ter.
3. Depois de aceitar o contrato de licença e de escolher a pasta de instalação, selecione **Ligar o agente ao Azure Log Analytics (OMS)**.   
    ![configuração do agente](./media/log-analytics-get-started/oms-onboard-agent.png)
4. Na página seguinte, são-lhe pedidos o ID da Área de Trabalho e a Chave da Área de Trabalho. Os seus ID e chave da Área de Trabalho são apresentados no ecrã onde transferiu o ficheiro do agente.  
    ![chaves do agente](./media/log-analytics-get-started/oms-onboard-mma-keys.png)  

    ![ligar servidores](./media/log-analytics-get-started/oms-onboard-key.png)
5. Durante a instalação, pode clicar em **Avançadas** para, em alternativa, configurar o seu servidor proxy e fornecer informações de autenticação. Clique no botão **Seguinte** para voltar ao ecrã de informações da área de trabalho.
6. Clique em **Seguinte** para validar os seus ID e Chave da Área de Trabalho. Se ocorrerem erros, pode clicar em **Anterior** para fazer correções. Quando os seus ID e Chave da Área de Trabalho são validados, clique em **Instalar** para concluir a instalação do agente.
7. No Painel de Controlo, clique em Microsoft Monitoring Agent > separador Azure Log Analytics (OMS). É apresentado um ícone de marca de verificação verde quando os agentes comunicarem com o serviço do Operations Management Suite. Inicialmente, esta ação demora cerca de 5 a 10 minutos.

> [!NOTE]
> De momento, as soluções de gestão da capacidade e de avaliação da configuração não são suportadas pelos servidores ligados diretamente ao Operations Management Suite.


Também pode ligar o agente ao System Center Operations Manager 2012 SP1 e versões posteriores. Para tal, selecione **Ligar o agente ao System Center Operations Manager**. Se escolher esta opção, envia dados para o serviço sem necessidade de hardware ou carga adicionais nos seus grupos de gestão.

Para saber mais sobre como ligar agentes ao Operations Management Suite, veja o artigo [Ligar computadores Windows ao Log Analytics](log-analytics-windows-agents.md).

## <a name="optionally-connect-servers-using-system-center-operations-manager"></a>Em alternativa, ligar servidores com o System Center Operations Manager
1. Na consola do Operations Manager, selecione **Administração**.
2. Expanda o nó das **Informações Operacionais** e selecione **Ligação das Informações Operacionais**.

   > [!NOTE]
   > Consoante o Update Rollup do SCOM que estiver a utilizar, poderá ver um nó para o *System Center Advisor*, as * Informações Operacionais* ou o *Operations Management Suite*.
   >
   >
3. Clique na ligação **Registar nas Informações Operacionais** no canto superior direito e siga as instruções.
4. Depois de concluir o assistente de registo, clique na ligação **Adicionar um Computador/Grupo**.
5. Na caixa de diálogo **Pesquisa no Computador**, pode procurar computadores ou grupos monitorizados pelo Operations Manager. Selecione computadores ou grupos para carregá-los para o Log Analytics, clique em **Adicionar** e, em seguida, clique em **OK**. Para verificar se o serviço OMS está a receber dados, aceda ao mosaico **Utilização** no portal do Operations Management Suite. Os dados deverão aparecer em cerca de 5 a 10 minutos.

Para saber mais sobre como ligar o Operations Manager ao Operations Management Suite, veja o artigo [Ligar o Operations Manager ao Log Analytics](log-analytics-om-agents.md).

## <a name="optionally-analyze-data-from-cloud-services-in-microsoft-azure"></a>Em alternativa, analisar dados dos serviços cloud no Microsoft Azure
Com o Operations Management Suite, pode procurar rapidamente registos de eventos e de IIS de serviços cloud e máquinas virtuais ao ativar os diagnósticos dos Serviços em Nuvem do Azure. Também pode receber informações adicionais sobre as suas máquinas virtuais do Azure ao instalar o Agente de Monitorização da Microsoft. Para saber mais sobre como configurar o seu ambiente do Azure para utilizar o Operations Management Suite, veja o artigo [Ligar o armazenamento do Azure ao Log Analytics](log-analytics-azure-storage.md).

## <a name="next-steps"></a>Passos seguintes
* [Adicionar soluções do Log Analytics a partir da Galeria de Soluções](log-analytics-add-solutions.md) para adicionar funcionalidade e recolher dados.
* Familiarize-se com as [pesquisas de registos](log-analytics-log-searches.md) para ver informações detalhadas recolhidas pelas soluções.
* Utilize [dashboards](log-analytics-dashboards.md) para guardar e apresentar as suas próprias pesquisas personalizadas.



<!--HONumber=Dec16_HO5-->



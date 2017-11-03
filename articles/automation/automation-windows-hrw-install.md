---
title: "Trabalho de Runbook híbrida da automatização do Azure Windows | Microsoft Docs"
description: "Este artigo fornece informações sobre como instalar um Azure Automation Runbook Worker híbrido que permite a execução de runbooks em computadores baseados em Windows no seu local datacenter ou o ambiente de nuvem."
services: automation
documentationcenter: 
author: eslesar
manager: carmonm
editor: tysonn
ms.assetid: 
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/07/2017
ms.author: magoedte
ms.openlocfilehash: d889fda72f54af3a5f6db24f7ee6632f72eacb88
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-deploy-a-windows-hybrid-runbook-worker"></a>Como implementar um Runbook Worker híbrido do Windows

Os Runbooks na automatização do Azure não é possível aceder a recursos em outras nuvens ou no seu ambiente no local, uma vez que são executados na nuvem do Azure.  A funcionalidade de trabalho de Runbook híbrida da automatização do Azure permite-lhe executar runbooks diretamente no computador que aloja a função e relativamente aos recursos no ambiente para gerir os recursos locais. Os Runbooks são armazenados e geridos na automatização do Azure e, em seguida, entregar uma ou mais computadores designados.  

Esta funcionalidade é ilustrada na imagem seguinte:<br>

![Descrição geral de trabalho de Runbook híbrida](media/automation-offering-get-started/automation-infradiagram-networkcomms.png)

Para uma descrição geral técnica as considerações de função e a implementação do Runbook Worker híbrido, consulte [descrição geral da arquitetura de automatização](automation-offering-get-started.md#automation-architecture-overview).

## <a name="hybrid-runbook-worker-groups"></a>Grupos de trabalho de Runbook híbrida

Cada Runbook Worker híbrido é um membro de um grupo de trabalho de Runbook híbrida que especificou quando instalar o agente.  Um grupo pode incluir um único agente, mas pode instalar múltiplos agentes num grupo de disponibilidade elevada.

Quando inicia um runbook num Runbook Worker híbrido, especifique o grupo que é executada no.  Os membros do grupo de determinam qual trabalho processa o pedido.  Não é possível especificar uma função de trabalho específica.

## <a name="installing-the-windows-hybrid-runbook-worker"></a>Instalar o Runbook Worker híbrido do Windows 

Para instalar e configurar um Runbook Worker híbrido do Windows, existem dois métodos disponíveis.  O método recomendado é utilizar um runbook da automatização para automatizar completamente o processo necessário para configurar um computador Windows.  O segundo método é a seguir um procedimento passo a passo para instalar e configurar a função manualmente.  

> [!NOTE]
> Para gerir a configuração dos seus servidores que suportam a função de trabalho de Runbook híbrida com configuração de estado pretendido (DSC), terá de adicioná-los como nós de DSC.  Para obter mais informações sobre a integração-los para a gestão com o DSC, consulte [máquinas de integração para gestão pelo Azure Automation DSC](automation-dsc-onboarding.md).        
Se ativar o [solução de gestão de atualizações](../operations-management-suite/oms-solution-update-management.md), qualquer computador com o Windows à sua área de trabalho do OMS é automaticamente configurado como um Runbook Worker híbrido para suportar runbooks incluídos nesta solução.  No entanto, não está registado em todos os grupos de trabalho híbrida já definidos na sua conta de automatização.  O computador pode ser adicionado a um grupo de trabalho de Runbook híbrida na sua conta de automatização para suportar os runbooks de automatização, desde que estiver a utilizar a mesma conta para a solução e a associação ao grupo de trabalho de Runbook híbrida.  Esta funcionalidade foi adicionada à versão 7.2.12024.0 da Função de Trabalho de Runbook Híbrida.  

Reveja as seguintes informações sobre o [requisitos de hardware e software](automation-offering-get-started.md#hybrid-runbook-worker) e [informações para preparar a sua rede](automation-offering-get-started.md#network-planning) antes de começar a implementar um Runbook Worker híbrido.  Depois de ter implementado com êxito um runbook worker, reveja [executar runbooks num Runbook Worker híbrido](automation-hrw-run-runbooks.md) para saber como configurar os runbooks para automatizar processos no seu centro de dados no local ou outro ambiente de nuvem.  
 
### <a name="automated-deployment"></a>Implementação automatizada

Execute os seguintes passos para automatizar a instalação e configuração da função de trabalho do Windows híbrida.  

1. Transferir o *New-OnPremiseHybridWorker.ps1* script do [galeria do PowerShell](https://www.powershellgallery.com/packages/New-OnPremiseHybridWorker/1.0/DisplayScript) diretamente a partir do computador que executa a função Runbook Worker híbrido ou de outro computador no seu ambiente e Copie-a de trabalho.  

    O *New-OnPremiseHybridWorker.ps1* script requer os seguintes parâmetros durante a execução:

  * *AutomationAccountName* (obrigatório) - o nome da sua conta de automatização.  
  * *ResourceGroupName* (obrigatório) - o nome do grupo de recursos associados a sua conta de automatização.  
  * *HybridGroupName* (obrigatório) - o nome de um grupo de trabalho de Runbook híbrida que especificar como um destino para os runbooks que suportam este cenário. 
  *  *SubscriptionID* (obrigatório) - o Id de subscrição do Azure que consta a sua conta de automatização.
  *  *WorkspaceName* (opcional) - o nome de área de trabalho do OMS.  Se não tiver uma área de trabalho do OMS, o script cria e configura um.  

     > [!NOTE]
     > Atualmente as regiões de automatização apenas suportadas para integração com o OMS são - **Sudeste da Austrália**, **EUA Leste 2**, **Sudeste asiático**, e **Europa Ocidental** .  Se a sua conta de automatização não está dessas regiões, o script cria uma área de trabalho do OMS, mas avisa-o que este não pode associá-las em conjunto.
     >
2. No seu computador, inicie **do Windows PowerShell** do **iniciar** ecrã no modo de administrador.  
3. A shell de linha de comandos do PowerShell, navegue para a pasta que contém o script transferido e executá-lo alterando os valores para parâmetros *- AutomationAccountName*, *- ResourceGroupName*, *- HybridGroupName*, *- SubscriptionId*, e *- WorkspaceName*.

     > [!NOTE] 
     > Lhe for pedido para autenticar com o Azure, depois de executar o script.  **Tem** iniciar sessão com uma conta que seja um membro da função de administradores da subscrição e o coadministrador da subscrição.  
     >  
    
        .\New-OnPremiseHybridWorker.ps1 -AutomationAccountName <NameofAutomationAccount> `
        -ResourceGroupName <NameofOResourceGroup> -HybridGroupName <NameofHRWGroup> `
        -SubscriptionId <AzureSubscriptionId> -WorkspaceName <NameOfOMSWorkspace>

4. Lhe for pedido para aceitar instalar **NuGet** e lhe for pedido para autenticar com as suas credenciais do Azure.<br><br> ![Execução do script New-OnPremiseHybridWorker](media/automation-hybrid-runbook-worker/new-onpremisehybridworker-scriptoutput.png)

5. Depois do script estiver concluído, o painel de grupos de trabalho híbrida irá mostrar o novo grupo e o número de membros ou se um grupo existente, o número de membros é incrementado.  Pode selecionar o grupo da lista no **grupos de trabalho híbrida** painel e selecione o **híbridos** mosaico.  No **híbridos** painel, verá cada membro do grupo listado.  

### <a name="manual-deployment"></a>Implementação manual 

Execute os primeiros dois passos uma vez para o seu ambiente de automatização e, em seguida, repita os passos restantes para cada computador de trabalho.

#### <a name="1-create-operations-management-suite-workspace"></a>1. Criar a área de trabalho do Operations Management Suite

Se ainda não tiver uma área de trabalho do Operations Management Suite, em seguida, crie uma utilizando as instruções apresentadas em [gerir a sua área de trabalho](../log-analytics/log-analytics-manage-access.md). Pode utilizar uma área de trabalho existente se já tiver uma.

#### <a name="2-add-automation-solution-to-operations-management-suite-workspace"></a>2. Adicionar a solução de automatização à área de trabalho do Operations Management Suite

Soluções de adicionar funcionalidades ao Operations Management Suite.  A solução de automatização adiciona funcionalidades de automatização do Azure, incluindo suporte para o Runbook Worker híbrido.  Quando adiciona a solução para a sua área de trabalho, este automaticamente empurra para baixo componentes de trabalho para o computador de agente que pretende instalar no próximo passo.

Siga as instruções apresentadas em [para adicionar uma solução utilizando a Galeria soluções](../log-analytics/log-analytics-add-solutions.md) para adicionar o **automatização** solução para a sua área de trabalho do Operations Management Suite.

#### <a name="3-install-the-microsoft-monitoring-agent"></a>3. Instalar o Microsoft Monitoring Agent

O Microsoft Monitoring Agent liga-se os computadores no Operations Management Suite.  Quando instalar o agente no computador local e ligá-lo à sua área de trabalho, irá transferir automaticamente os componentes necessários para o Runbook Worker híbrido.

Siga as instruções apresentadas em [computadores Windows ligar ao Log Analytics](../log-analytics/log-analytics-windows-agents.md) para instalar o agente no computador local.  Pode repetir este processo para vários computadores adicionar vários workers ao seu ambiente.

Quando o agente foi ligado com êxito no Operations Management Suite, estas serão listadas no **origens ligadas** separador do Operations Management Suite **definições** painel.  Pode verificar se o agente corretamente transferiu a solução de automatização quando tem uma pasta denominada **AzureAutomationFiles** em C:\Program Files\Microsoft monitorização Agent\Agent.  Para confirmar a versão do Runbook Worker híbrido, pode navegar para C:\Program Files\Microsoft monitorização Agent\Agent\AzureAutomation\ e tenha em atenção o \\ *versão* subpasta.   

#### <a name="4-install-the-runbook-environment-and-connect-to-azure-automation"></a>4. Instalar o ambiente de runbook e ligue à automatização do Azure

Quando adiciona um agente no Operations Management Suite, a solução de automatização empurra para baixo a **HybridRegistration** módulo do PowerShell, que contém o **Add-HybridRunbookWorker** cmdlet.  Utilize este cmdlet para instalar o ambiente de runbook no computador e registá-lo com a automatização do Azure.

Abra uma sessão do PowerShell no modo de administrador e execute os seguintes comandos para importar o módulo.

    cd "C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\<version>\HybridRegistration"
    Import-Module HybridRegistration.psd1

Em seguida, execute o **Add-HybridRunbookWorker** cmdlet utilizando a seguinte sintaxe:

    Add-HybridRunbookWorker –GroupName <String> -EndPoint <Url> -Token <String>

Pode obter as informações necessárias para este cmdlet a partir de **gerir chaves** painel no portal do Azure.  Abrir este painel, selecionando o **chaves** opção do **definições** painel na sua conta de automatização.

![Descrição geral de trabalho de Runbook híbrida](media/automation-hybrid-runbook-worker/elements-panel-keys.png)

* **GroupName** é o nome do grupo de trabalho de Runbook híbrida. Se este grupo já existe na conta de automatização, o computador atual é adicionado ao mesmo.  Se ainda não existir, em seguida, é adicionado.
* **Ponto final** é o **URL** campo no **gerir chaves** painel.
* **Token** é o **chave de acesso primária** no **gerir chaves** painel.  

Utilize o **-Verbose** mudar com **Add-HybridRunbookWorker** receber informações detalhadas sobre a instalação.

#### <a name="5-install-powershell-modules"></a>5. Instalar módulos do PowerShell

Os Runbooks, pode utilizar qualquer um dos cmdlets definidos em módulos instalados no seu ambiente de automatização do Azure e atividades.  Estes módulos não são implementados automaticamente em computadores no local, como tal, deve instalar manualmente.  A exceção é o módulo do Azure, o que é instalado por predefinição, fornecer acesso a cmdlets para todos os serviços do Azure e actividades de automatização do Azure.

Uma vez que o objetivo principal da funcionalidade do Runbook Worker híbrido gerir recursos locais, provavelmente terá de instalar os módulos que suportam estes recursos.  Pode fazer referência a [instalar módulos](http://msdn.microsoft.com/library/dd878350.aspx) para obter informações sobre como instalar os módulos do Windows PowerShell.  Tem de ser módulos instalados numa localização referenciada pela variável de ambiente de PSModulePath, de modo a que são automaticamente importados pelo worker híbrido.  Para obter mais informações, consulte [modificar o caminho de instalação PSModulePath](https://msdn.microsoft.com/library/dd878326%28v=vs.85%29.aspx). 

## <a name="troubleshooting"></a>Resolução de problemas 

O Runbook Worker híbrido depende do Microsoft Monitoring Agent para comunicar com a sua conta de automatização para registar o trabalho, receber tarefas de runbook e comunicar estado. Se o registo do worker falhar, aqui estão algumas causas possíveis para o erro:  

1. A função de trabalho híbrida estiver atrás de um proxy ou firewall.  
    Certifique-se de que o computador tem acesso de saída para *.azure automation.net na porta 443.  

2. A função de trabalho híbrida está em execução no computador tiver menos do que o hardware mínimo [requisitos](automation-offering-get-started.md#hybrid-runbook-worker).  
    Computadores com o Runbook Worker híbrido devem cumprir os requisitos mínimos de hardware antes de indicar que esta funcionalidade do anfitrião. Caso contrário, consoante a utilização de recursos de outros processos em segundo plano e a contenção causado por runbooks durante a execução, o computador irá ficar sobrecarregado e causar atrasos de tarefa de runbook ou tempos limite.
   Confirme que o computador designado para executar a funcionalidade do Runbook Worker híbrido cumpre os requisitos mínimos de hardware.  Se existir, monitorize a utilização de CPU e memória para determinar a qualquer correlação entre o desempenho dos processos de trabalho de Runbook híbrida e o Windows.  Se existir memória ou pressão de CPU, isto pode indicar a necessidade de atualizar ou adicione processadores adicionais ou aumente a memória para resolver o congestionamento do recurso e resolva o erro. Em alternativa, selecione um recurso de computação diferentes que pode suportar os requisitos mínimos e escala, quando a carga de trabalho exigências indicam que um aumento é necessário.
    
3. O serviço Microsoft Monitoring Agent não está em execução.  
    Se o serviço Windows do Microsoft Monitoring Agent não está em execução, isto impede o Runbook Worker híbrido comunicar com a automatização do Azure.  Certifique-se de que o agente está em execução, introduzindo o seguinte comando do PowerShell: `get-service healthservice`.  Se o serviço for parado, introduza o seguinte comando do PowerShell para iniciar o serviço: `start-service healthservice`.  

4. No **aplicação e o Gestor de serviços de Logs\Operations** registo de eventos, para ver eventos 4502 e EventMessage contendo **Microsoft.EnterpriseManagement.HealthService.AzureAutomation.HybridAgent**com a descrição seguinte: *o certificado apresentado pelo serviço <wsid>. oms.opinsights.azure.com não foi emitido por uma autoridade de certificação utilizada para os serviços Microsoft. Contacte o administrador de rede para ver se estiverem a executar um proxy que intercepte a comunicação TLS/SSL. O artigo KB3126513 tem informações adicionais de resolução de problemas para problemas de conectividade.*
    Isto pode dever-se a comunicação de blockking de firewall de proxy ou de rede ao Microsoft Azure.  Certifique-se de que o computador tem acesso de saída para *.azure automation.net nas portas 443.

Os registos são armazenados localmente em cada função de trabalho híbrida no C:\ProgramData\Microsoft\System Center\Orchestrator\7.2\SMA\Sandboxes.  Pode verificar se existem quaisquer avisos ou eventos de erro escritos para o **aplicações e serviços Logs\Microsoft-SMA\Operations** e **aplicação e o Gestor de serviços de Logs\Operations** registo de eventos deverá indicar uma conectividade ou outro problema que afetam a integração de função da automatização do Azure ou o problema ao efetuar operações normais.  

## <a name="next-steps"></a>Passos Seguintes

* Reveja [executar runbooks num Runbook Worker híbrido](automation-hrw-run-runbooks.md) para saber como configurar os runbooks para automatizar processos no seu centro de dados no local ou outro ambiente de nuvem.
* Para obter instruções sobre como remover os Runbook Workers híbridos, consulte [remover Azure automatização de Runbook híbridos](automation-remove-hrw.md)
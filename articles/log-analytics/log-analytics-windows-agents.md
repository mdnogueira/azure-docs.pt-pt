---
title: Ligar a computadores com o Windows ao Log Analytics do Azure | Microsoft Docs
description: "Este artigo mostra os passos para ligar os computadores com o Windows na sua infraestrutura no local para o serviço de análise de registos, utilizando uma versão personalizada da monitorização do agente Microsoft (MMA)."
services: log-analytics
documentationcenter: 
author: MGoedtel
manager: carmonm
editor: 
ms.assetid: 932f7b8c-485c-40c1-98e3-7d4c560876d2
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/03/2017
ms.author: magoedte
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e5f04f3b9135167c0f339c58323ebd931b260109
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="connect-windows-computers-to-the-log-analytics-service-in-azure"></a>Ligar computadores Windows para o serviço de análise de registos do Azure

Este artigo mostra os passos para ligar computadores com o Windows na sua infraestrutura no local a áreas de trabalho do OMS, utilizando uma versão personalizada da monitorização do agente Microsoft (MMA). Terá de instalar e ligar agentes para todos os computadores que pretende para carregar por ordem para os mesmos para enviar dados para o serviço de análise de registos e para ver e atuar sobre dados. Cada agente pode reportar a vários áreas de trabalho.

Pode instalar agentes utilizando a configuração, a linha de comandos, ou com pretendido Estado Configuration (DSC) na automatização do Azure.  

>[!NOTE]
Para máquinas virtuais em execução no Azure, pode simplificar a instalação utilizando a [extensão da máquina virtual](log-analytics-azure-vm-extension.md).

Em computadores com a conectividade à Internet, o agente utiliza a ligação à Internet para enviar dados para OMS. Para computadores que não têm acesso à Internet, pode utilizar um proxy ou o [OMS Gateway](log-analytics-oms-gateway.md).

Ligar os computadores Windows OMS é simples utilizar três passos simples:

1. Transferir o ficheiro de configuração do agente a partir do portal do OMS
2. Instalar o agente utilizando o método que escolher
3. Configure o agente ou adicione áreas de trabalho adicionais, se necessário

O diagrama seguinte mostra a relação entre os computadores com o Windows e o OMS, depois de ter instalado e configurado os agentes.

![OMS-direta-agente-diagrama](./media/log-analytics-windows-agents/oms-direct-agent-diagram.png)

Se as políticas de segurança de TI não permitir que os computadores na sua rede para ligar à Internet, pode configurar os computadores para ligar ao OMS Gateway. Para obter mais informações e os passos sobre como configurar os servidores para comunicar através de um Gateway do OMS para o serviço do OMS, consulte [ligar computadores ao OMS utilizando o Gateway do OMS](log-analytics-oms-gateway.md).

## <a name="system-requirements-and-required-configuration"></a>Requisitos de sistema e a configuração necessária
Antes de instalar ou implementar agentes, consulte os detalhes seguintes para se certificar de que cumpre os requisitos.

- Só pode instalar o MMA OMS em computadores com o Windows Server 2008 SP 1 ou posterior ou Windows 7 SP1 ou posterior.
- Necessita de uma subscrição do Azure.  Para obter mais informações, consulte [introdução à análise de registos](log-analytics-get-started.md).
- Cada computador com o Windows tem de ser possível estabelecer ligação à Internet através de HTTPS ou para o Gateway do OMS. Esta ligação pode ser direta, através de um proxy ou através do Gateway do OMS.
- Pode instalar o MMA OMS em computadores autónomos, servidores e máquinas virtuais. Se pretender ligar máquinas de virtuais alojado no Azure ao OMS, consulte [máquinas de virtuais do Azure de ligar ao Log Analytics](log-analytics-azure-vm-extension.md).
- O agente tem de utilizar a porta TCP 443 para vários recursos.

### <a name="network"></a>Rede

Para agentes do Windows ligar a e registar com o serviço do OMS, têm de ter acesso a recursos de rede, incluindo os números de porta e os URLs de domínio.

- Para servidores proxy, tem de assegurar que os recursos do servidor proxy apropriados estão configurados nas definições do agente.
- Para firewalls que restringem o acesso à Internet, ou os engenheiros de rede tem de configurar a firewall para permitir o acesso ao OMS. Não é necessária nenhuma ação nas definições do agente.

A tabela seguinte mostra os recursos necessários para a comunicação.

>[!NOTE]
>Alguns dos recursos seguintes mencionar das informações operacionais, que era um nome anterior para análise de registos.

| Recursos do Agente | Portas | Inspeção de HTTPS direto |
|---|---|---|
| *.ods.opinsights.azure.com | 443 | Sim |
| *.oms.opinsights.azure.com | 443 | Sim |
| *.blob.core.windows.net | 443 | Sim |
| *.azure-automation.net | 443 | Sim |



## <a name="download-the-agent-setup-file-from-oms"></a>Transfira o ficheiro de configuração do agente do OMS
1. No [portal do OMS](https://www.mms.microsoft.com), no **descrição geral** página, clique em de **definições** mosaico.  Clique em de **origens ligadas** separador no topo.  
    ![Separador de origens ligada](./media/log-analytics-windows-agents/oms-direct-agent-connected-sources.png)
2. Clique em **servidores Windows** e, em seguida, clique em **transferir o agente do Windows** aplicável ao seu tipo de processador do computador para transferir o ficheiro de configuração.
3. À direita do **ID da área de trabalho**, clique no ícone de copiar e colar o ID no bloco de notas.
4. À direita do **chave primária**, clique no ícone de copiar e colar a chave no bloco de notas.     

## <a name="install-the-agent-using-setup"></a>Instalar o agente utilizando a configuração
1. Execute a configuração para instalar o agente num computador que pretende gerir.
2. Na página de boas-vindas, clique em **seguinte**.
3. Na página de termos de licença, leia a licença e, em seguida, clique em **concordo**.
4. Na página pasta de destino, altere ou mantenha a pasta de instalação predefinida e, em seguida, clique em **seguinte**.
5. Na página Opções de configuração do agente, pode optar por ligar o agente à análise de registos do Azure (OMS), Operations Manager, ou pode deixar as escolhas em branco se pretender configurar o agente mais tarde. Clique em **Seguinte**.   
    - Se tiver optado por ligar para análise de registos do Azure (OMS), cole o **ID da área de trabalho** e **chave da área de trabalho (chave primária)** copiado no bloco de notas no procedimento anterior e, em seguida, clique em **seguinte**.  
        ![Cole o ID da área de trabalho e a chave primária](./media/log-analytics-windows-agents/connect-workspace.png)
    - Se tiver optado por ligar ao Operations Manager, escreva o **Management Group Name**, **servidor de gestão** nome, e **porta do servidor de gestão**e, em seguida, clique em **seguinte**. Na página de conta de ação do agente, escolher a conta de sistema Local ou uma conta de domínio local e, em seguida, clique em **seguinte**.  
        ![configuração do grupo de gestão](./media/log-analytics-windows-agents/oms-mma-om-setup01.png)![conta de ação do agente](./media/log-analytics-windows-agents/oms-mma-om-setup02.png)

6. Preparado para a página de instalação, reveja as suas opções e, em seguida, clique em **instalar**.
7. A configuração foi concluída com êxito página, clique em **concluir**.
8. Quando terminar, o **Microsoft Monitoring Agent** aparece no **painel de controlo**. Pode rever a configuração não existe e certifique-se de que o agente está ligado ao Operational Insights (OMS). Quando estiver ligado à OMS, o agente apresenta uma mensagem a indicar: **o Microsoft Monitoring Agent foi ligado com êxito para o serviço do Microsoft Operations Management Suite.**

## <a name="configure-proxy-settings"></a>Configurar definições de proxy

Pode utilizar o procedimento seguinte para configurar as definições de proxy para o Agente de Monitorização da Microsoft utilizando o Painel de Controlo. Terá de utilizar este procedimento em cada servidor. Se tiver vários servidores que necessita configurar, poderá considerar mais fácil utilizar um script para automatizar este processo. Se este é o caso, consulte o seguinte procedimento [Configurar as definições de proxy para o Agente de Monitorização da Microsoft utilizando um script](#to-configure-proxy-settings-for-the-microsoft-monitoring-agent-using-a-script).

### <a name="to-configure-proxy-settings-for-the-microsoft-monitoring-agent-using-control-panel"></a>Configurar as definições de proxy para o Agente de Monitorização da Microsoft utilizando o Painel de Controlo
1. Abra o **Painel de Controlo**.
2. Abra o **Agente de Monitorização da Microsoft**.
3. Clique no separador **Definições de Proxy**.  
    ![separador de definições de proxy](./media/log-analytics-windows-agents/proxy-direct-agent-proxy.png)
4. Selecione **Utilizar um servidor proxy** e escreva o URL e o número de porta, se necessário, semelhante ao exemplo apresentado. Se o servidor proxy requer autenticação, escreva o nome de utilizador e a palavra-passe para aceder ao servidor proxy.


### <a name="verify-agent-connectivity-to-oms"></a>Verificar a conectividade de agente OMS

Pode facilmente verificar se os agentes estão a comunicar com o OMS, utilizando o seguinte procedimento:

1.  No computador com o agente do Windows, abra o painel de controlo.
2.  Abra o Microsoft Monitoring Agent.
3.  Clique no separador de análise de registos do Azure (OMS).
4.  Na coluna Estado, deverá ver que o agente ligado com êxito para o serviço do Operations Management Suite.

![agente ligado](./media/log-analytics-windows-agents/mma-connected.png)


### <a name="to-configure-proxy-settings-for-the-microsoft-monitoring-agent-using-a-script"></a>Configurar as definições de proxy para o Agente de Monitorização da Microsoft utilizando um script
Copie o exemplo seguinte, atualize-o com informações específicas do ambiente, guarde-o com uma extensão de nome de ficheiro PS1 e, em seguida, execute o script em cada computador que estabelece ligação ao serviço OMS diretamente.

    param($ProxyDomainName="http://proxy.contoso.com:80", $cred=(Get-Credential))

    # First we get the Health Service configuration object.  We need to determine if we
    #have the right update rollup with the API we need.  If not, no need to run the rest of the script.
    $healthServiceSettings = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'

    $proxyMethod = $healthServiceSettings | Get-Member -Name 'SetProxyInfo'

    if (!$proxyMethod)
    {
         Write-Output 'Health Service proxy API not present, will not update settings.'
         return
    }

    Write-Output "Clearing proxy settings."
    $healthServiceSettings.SetProxyInfo('', '', '')

    $ProxyUserName = $cred.username

    Write-Output "Setting proxy to $ProxyDomainName with proxy username $ProxyUserName."
    $healthServiceSettings.SetProxyInfo($ProxyDomainName, $ProxyUserName, $cred.GetNetworkCredential().password)



## <a name="install-the-agent-using-the-command-line"></a>Instalar o agente utilizando a linha de comandos
- Modificar e, em seguida, utilize o exemplo seguinte para instalar o agente utilizando a linha de comandos. O exemplo executa uma instalação totalmente automática.

    >[!NOTE]
    Se pretender atualizar um agente, terá de utilizar a API de scripting de análise de registos. Consulte a secção seguinte para atualizar um agente.

    ```dos
    MMASetup-AMD64.exe /Q:A /R:N /C:"setup.exe /qn ADD_OPINSIGHTS_WORKSPACE=1 OPINSIGHTS_WORKSPACE_ID=<your workspace id> OPINSIGHTS_WORKSPACE_KEY=<your workspace key> AcceptEndUserLicenseAgreement=1"
    ```

O agente utiliza IExpress como respetivo Self-extractor utilizando o `/c` comando. Pode ver os parâmetros da linha de comandos em [comutadores da linha de comandos para IExpress](https://support.microsoft.com/help/197147/command-line-switches-for-iexpress-software-update-packages) e, em seguida, atualize o exemplo de acordo com as suas necessidades.

|Opções de MMA específico                   |Notas         |
|---------------------------------------|--------------|
|ADD_OPINSIGHTS_WORKSPACE               | 1 = configurar o agente para reportar a uma área de trabalho                |
|OPINSIGHTS_WORKSPACE_ID                | Id da área de trabalho (guid) para a área de trabalho para adicionar                    |
|OPINSIGHTS_WORKSPACE_KEY               | Chave de área de trabalho utilizada para autenticar inicialmente com área de trabalho |
|OPINSIGHTS_WORKSPACE_AZURE_CLOUD_TYPE  | Especificar o ambiente de nuvem onde está localizada a área de trabalho <br> 0 = em nuvem comerciais do azure (predefinição) <br> 1 = azure Government |
|OPINSIGHTS_PROXY_URL               | URI de proxy a utilizar |
|OPINSIGHTS_PROXY_USERNAME               | Nome de utilizador para aceder a um proxy autenticado |
|OPINSIGHTS_PROXY_PASSWORD               | Palavra-passe para aceder a um proxy autenticado |

>[!NOTE]
Para evitar atingir o limite de comprimento de linha de comandos do IExpress, instale o agente com nenhuma área de trabalho configurada e, em seguida, utilizar um script para definir a configuração para a área de trabalho.

>[!NOTE]
Se obtiver um `Command line option syntax error.` ao utilizar o `OPINSIGHTS_WORKSPACE_AZURE_CLOUD_TYPE` parâmetro, pode utilizar a seguinte solução:
```dos
MMASetup-AMD64.exe /C /T:.\MMAExtract
cd .\MMAExtract
setup.exe /qn ADD_OPINSIGHTS_WORKSPACE=1 OPINSIGHTS_WORKSPACE_AZURE_CLOUD_TYPE=1 OPINSIGHTS_WORKSPACE_ID=<your workspace id> OPINSIGHTS_WORKSPACE_KEY=<your workspace key> AcceptEndUserLicenseAgreement=1
```

## <a name="add-a-workspace-using-a-script"></a>Adicionar uma área de trabalho através de um script
Adicione uma área de trabalho utilizando a API de scripting de agente de análise de registos com o exemplo seguinte:

```PowerShell
$mma = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'
$mma.AddCloudWorkspace($workspaceId, $workspaceKey)
$mma.ReloadConfiguration()
```

Para adicionar uma área de trabalho no Azure para US Government, utilize o seguinte script de exemplo:
```PowerShell
$mma = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'
$mma.AddCloudWorkspace($workspaceId, $workspaceKey, 1)
$mma.ReloadConfiguration()
```

>[!NOTE]
Se utilizou a linha de comandos ou script anteriormente para instalar ou configurar o agente, `EnableAzureOperationalInsights` foi substituído por `AddCloudWorkspace`.

## <a name="install-the-agent-using-dsc-in-azure-automation"></a>Instalar o agente utilizando DSC na automatização do Azure

Pode utilizar o seguinte exemplo de script para instalar o agente utilizando DSC na automatização do Azure. O exemplo instala o agente de 64 bits, identificado pelo `URI` valor. Também pode utilizar a versão de 32 bits, substituindo o valor URI. O URI para ambas as versões são:

- O agente de 64 bits do Windows - https://go.microsoft.com/fwlink/?LinkId=828603
- O agente de 32 bits do Windows - https://go.microsoft.com/fwlink/?LinkId=828604


>[!NOTE]
Este procedimento e o script de exemplo não irá atualizar um agente existente.

1. Módulo de importação do DSC xPSDesiredStateConfiguration da [http://www.powershellgallery.com/packages/xPSDesiredStateConfiguration](http://www.powershellgallery.com/packages/xPSDesiredStateConfiguration) na automatização do Azure.  
2.  Criar recursos de variável de automatização do Azure para *OPSINSIGHTS_WS_ID* e *OPSINSIGHTS_WS_KEY*. Definir *OPSINSIGHTS_WS_ID* para o seu ID de área de trabalho de análise de registos do OMS e conjunto *OPSINSIGHTS_WS_KEY* para a chave primária da sua área de trabalho.
3.  Utilize o seguinte script e guarde-o como MMAgent.ps1
4.  Modificar e, em seguida, utilize o exemplo seguinte para instalar o agente utilizando DSC na automatização do Azure. Importe MMAgent.ps1 na automatização do Azure, utilizando a interface de automatização do Azure ou o cmdlet.
5.  Atribua um nó para a configuração. Em 15 minutos, o nó verifica a respetiva configuração e o MMA é enviado para o nó.

```PowerShell
Configuration MMAgent
{
    $OIPackageLocalPath = "C:\MMASetup-AMD64.exe"
    $OPSINSIGHTS_WS_ID = Get-AutomationVariable -Name "OPSINSIGHTS_WS_ID"
    $OPSINSIGHTS_WS_KEY = Get-AutomationVariable -Name "OPSINSIGHTS_WS_KEY"


    Import-DscResource -ModuleName xPSDesiredStateConfiguration

    Node OMSnode {
        Service OIService
        {
            Name = "HealthService"
            State = "Running"
            DependsOn = "[Package]OI"
        }

        xRemoteFile OIPackage {
            Uri = "https://go.microsoft.com/fwlink/?LinkId=828603"
            DestinationPath = $OIPackageLocalPath
        }

        Package OI {
            Ensure = "Present"
            Path  = $OIPackageLocalPath
            Name = "Microsoft Monitoring Agent"
            ProductId = "8A7F2C51-4C7D-4BFD-9014-91D11F24AAE2"
            Arguments = '/C:"setup.exe /qn ADD_OPINSIGHTS_WORKSPACE=1 OPINSIGHTS_WORKSPACE_ID=' + $OPSINSIGHTS_WS_ID + ' OPINSIGHTS_WORKSPACE_KEY=' + $OPSINSIGHTS_WS_KEY + ' AcceptEndUserLicenseAgreement=1"'
            DependsOn = "[xRemoteFile]OIPackage"
        }
    }
}


```

### <a name="get-the-latest-productid-value"></a>Obter o valor mais recente do ProductId

O `ProductId value` o MMAgent.ps1 script é exclusivo para cada versão do agente. Quando uma versão atualizada de cada agente for publicada, o valor de ProductId é alterado. Por isso, quando o ProductId for alterada no futuro, pode encontrar a versão do agente utilizando um script simple. Depois de ter a versão mais recente do agente instalada no servidor de teste, pode utilizar o script seguinte para obter o valor de ProductId instalado. Utilizar o valor mais recente do ProductId, pode atualizar o valor no MMAgent.ps1 script.

```PowerShell
$InstalledApplications  = Get-ChildItem hklm:\SOFTWARE\Microsoft\Windows\CurrentVersion\Uninstall


foreach ($Application in $InstalledApplications)

{

     $Key = Get-ItemProperty $Application.PSPath

     if ($Key.DisplayName -eq "Microsoft Monitoring Agent")

     {

        $Key.DisplayName

        Write-Output ("Product ID is: " + $Key.PSChildName.Substring(1,$Key.PSChildName.Length -2))

     }

}  
```

## <a name="configure-an-agent-manually-or-add-additional-workspaces"></a>Configure manualmente um agente ou adicione áreas de trabalho adicionais
Se instalou agentes, mas não foi possível configurá-las ou se pretender que o agente para comunicar para várias áreas de trabalho, pode utilizar as seguintes informações para ativar um agente ou reconfigurá-lo. Depois de configurar o agente, vai registar com o serviço de agente e irá obter as informações de configuração necessário e pacotes de gestão que contêm informações de soluções.

1. Depois de instalar o Microsoft Monitoring Agent, abra **painel de controlo**.
2. Abra **Microsoft Monitoring Agent** e, em seguida, clique em de **análise de registos do Azure (OMS)** separador.   
3. Clique em **adicionar** para abrir o **adicionar uma área de trabalho de análise do registo** caixa.
4. Colar o **ID da área de trabalho** e **chave da área de trabalho (chave primária)** que copiou no bloco de notas num procedimento anterior para a área de trabalho que pretende adicionar e, em seguida, clique em **OK**.  
    ![configurar as informações operacionais](./media/log-analytics-windows-agents/add-workspace.png)

Depois dos dados são recolhidos a partir de computadores monitorizados por agente, o número de computadores monitorizados pelo OMS será apresentado no portal do OMS no **origens ligadas** separador **definições** como **servidores ligados**.


## <a name="to-disable-an-agent"></a>Para desativar um agente
1. Depois de instalar o agente, abra **painel de controlo**.
2. Abra o Microsoft Monitoring Agent e, em seguida, clique em de **análise de registos do Azure (OMS)** separador.
3. Selecione uma área de trabalho e, em seguida, clique em **remover**. Repita este passo para todas as outras áreas de trabalho.


## <a name="optionally-configure-agents-to-report-to-an-operations-manager-management-group"></a>Opcionalmente, configurar os agentes para reportar a um grupo de gestão do Operations Manager

Se utilizar o Operations Manager na sua infraestrutura de TI, também pode utilizar o agente MMA como um agente do Operations Manager.

### <a name="to-configure-mma-agents-to-report-to-an-operations-manager-management-group"></a>Para configurar os agentes MMA para que reportem a um grupo de gestão do Operations Manager
1.  No computador onde o agente está instalado, abra **painel de controlo**.  
2.  Abra **Microsoft Monitoring Agent** e, em seguida, clique em de **do Operations Manager** separador.  
    ![Separador do Microsoft Monitoring Agent do Operations Manager](./media/log-analytics-windows-agents/om-mg01.png)
3.  Se os servidores do Operations Manager tem integração com o Active Directory, clique em **atualizar automaticamente atribuições de grupo de gestão do AD DS**.
4.  Clique em **adicionar** para abrir o **adicionar um grupo de gestão** caixa de diálogo.  
    ![O Microsoft Monitoring Agent adicionar um grupo de gestão](./media/log-analytics-windows-agents/oms-mma-om02.png)
5.  No **nome do grupo de gestão** caixa, escreva o nome do seu grupo de gestão.
6.  No **servidor de gestão principal** caixa, escreva o nome de computador do servidor de gestão principal.
7.  No **porta do servidor de gestão** caixa, escreva o número de porta TCP.
8.  Em **conta de ação do agente**, escolha a conta de sistema Local ou uma conta de domínio local.
9.  Clique em **OK** para fechar o **adicionar um grupo de gestão** caixa de diálogo e, em seguida, clique em **OK** para fechar o **propriedades do agente de monitorização Microsoft** caixa de diálogo.


## <a name="next-steps"></a>Passos seguintes

- [Adicionar soluções do Log Analytics a partir da Galeria de Soluções](log-analytics-add-solutions.md) para adicionar funcionalidade e recolher dados.

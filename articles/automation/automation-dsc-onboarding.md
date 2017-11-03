---
title: "Máquinas de integração de gestão do Automation DSC do Azure | Microsoft Docs"
description: "Como máquinas de configuração para gestão com o Automation DSC do Azure"
services: automation
documentationcenter: dev-center-name
author: eslesar
manager: carmonm
ms.assetid: da13e1f5-2a1c-443b-8e3b-9f0d6f9e4810
ms.service: automation
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: powershell
ms.workload: TBD
ms.date: 12/13/2016
ms.author: eslesar
ms.openlocfilehash: 1a6355c18aed8a4040121e5af482328b70294b83
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="onboarding-machines-for-management-by-azure-automation-dsc"></a>Máquinas de integração de gestão do Automation DSC do Azure

## <a name="why-manage-machines-with-azure-automation-dsc"></a>Por que motivo gerir máquinas com o Automation DSC do Azure?

Como [configuração de estado pretendido do PowerShell](https://technet.microsoft.com/library/dn249912.aspx), a configuração de estado pretendido do Azure Automation é um serviço de gestão de configuração de simples, mas poderosa, para nós de DSC (máquinas virtuais e físicas) no Centro de dados qualquer nuvem ou no local. Permite que escalabilidade entre milhares de máquinas forma rápida e fácil de uma localização central e segura. Pode carregar facilmente máquinas, atribua-as configurações declarativas e ver relatórios, que mostra cada computador da compatibilidade para o estado pretendido que especificou. A camada de gestão do Automation DSC do Azure é DSC Novidades camada de gestão de automatização do Azure para processamento de scripts do PowerShell. Por outras palavras, da mesma forma que a automatização do Azure ajuda-o a gerir scripts do PowerShell, também o ajuda a gerir configurações de DSC. Para saber mais sobre as vantagens de utilizar o DSC de automatização do Azure, consulte o artigo [descrição geral do Azure Automation DSC](automation-dsc-overview.md).

Automation DSC do Azure podem ser utilizado para gerir uma variedade de máquinas:

* Máquinas virtuais do Azure (clássica)
* Máquinas virtuais do Azure
* Máquinas virtuais Amazon Web Services (AWS)
* Windows físico virtual máquinas no local, ou numa nuvem diferente do Azure/AWS
* Linux físico virtual máquinas no local, no Azure ou numa nuvem diferente do Azure

Além disso, se não estiver pronto para gerir a configuração da máquina da nuvem, DSC de automatização do Azure também pode ser utilizado como um ponto final só de relatório. Isto permite-lhe definir a configuração pretendida do (emitir) através do DSC no local e visualizar detalhes de relatórios avançados conformidade de nó com o estado pretendido na automatização do Azure.

> [!NOTE]
> Gerir as VMs do Azure com o DSC está incluído, sem encargos adicionais se a extensão de máquina virtual DSC instalada é superior ao 2.7.  Consulte o [ **automatização página de preços** ](https://azure.microsoft.com/en-us/pricing/details/automation/) para obter mais detalhes.


As secções seguintes descrevem como pode carregar cada tipo de máquina Automation DSC do Azure.

## <a name="azure-virtual-machines-classic"></a>Máquinas virtuais do Azure (clássica)

Com o DSC de automatização do Azure, pode facilmente carregar virtual machines do Azure (clássica) para gestão de configuração utilizando o portal do Azure, ou o PowerShell. Os bastidores e sem um administrador ter de remoto para a VM, a extensão de configuração de estado pretendido do Azure VM regista a VM no Automation DSC do Azure. Uma vez que a extensão de configuração de estado pretendido do Azure VM é executado no modo assíncrono, os passos para controlar o progresso da mesma ou a resolução de problemas são fornecidos no [ **integração de máquina virtual do Azure de resolução de problemas** ](#troubleshooting-azure-virtual-machine-onboarding) secção abaixo.

### <a name="azure-portal"></a>Portal do Azure

No [portal do Azure](http://portal.azure.com/), clique em **procurar** -> **máquinas virtuais (clássicas)**. Selecione a VM do Windows que pretende integrar. No painel de dashboard da máquina virtual, clique em **todas as definições** -> **extensões** -> **adicionar** -> **Automation DSC do Azure** -> **criar**. Introduza o [valores de Gestor de configuração Local do PowerShell DSC](https://msdn.microsoft.com/powershell/dsc/metaconfig4) necessários para o caso de utilização, chave de registo da sua conta de automatização e URL de registo e, opcionalmente, uma configuração de nó para atribuir à VM.

![](./media/automation-dsc-onboarding/DSC_Onboarding_1.png)

Para localizar o URL de registo e da chave da conta de automatização para carregar a máquina para ver o [ **Secure registo** ](#secure-registration) secção abaixo.

### <a name="powershell"></a>PowerShell

```powershell
# log in to both Azure Service Management and Azure Resource Manager
Add-AzureAccount
Add-AzureRmAccount

# fill in correct values for your VM/Automation account here
$VMName = ""
$ServiceName = ""
$AutomationAccountName = ""
$AutomationAccountResourceGroup = ""

# fill in the name of a Node Configuration in Azure Automation DSC, for this VM to conform to
$NodeConfigName = ""

# get Azure Automation DSC registration info
$Account = Get-AzureRmAutomationAccount -ResourceGroupName $AutomationAccountResourceGroup -Name $AutomationAccountName
$RegistrationInfo = $Account | Get-AzureRmAutomationRegistrationInfo

# use the DSC extension to onboard the VM for management with Azure Automation DSC
$VM = Get-AzureVM -Name $VMName -ServiceName $ServiceName

$PublicConfiguration = ConvertTo-Json -Depth 8 @{
    SasToken = ""
    ModulesUrl = "https://eus2oaasibizamarketprod1.blob.core.windows.net/automationdscpreview/RegistrationMetaConfigV2.zip"
    ConfigurationFunction = "RegistrationMetaConfigV2.ps1\RegistrationMetaConfigV2"

# update these PowerShell DSC Local Configuration Manager defaults if they do not match your use case.
# See https://technet.microsoft.com/library/dn249922.aspx?f=255&MSPPError=-2147217396 for more details
    Properties = @{
    RegistrationKey = @{
        UserName = 'notused'
        Password = 'PrivateSettingsRef:RegistrationKey'
    }
    RegistrationUrl = $RegistrationInfo.Endpoint
    NodeConfigurationName = $NodeConfigName
    ConfigurationMode = "ApplyAndMonitor"
    ConfigurationModeFrequencyMins = 15
    RefreshFrequencyMins = 30
    RebootNodeIfNeeded = $False
    ActionAfterReboot = "ContinueConfiguration"
    AllowModuleOverwrite = $False
    }
}

$PrivateConfiguration = ConvertTo-Json -Depth 8 @{
    Items = @{
        RegistrationKey = $RegistrationInfo.PrimaryKey
    }
}

$VM = Set-AzureVMExtension `
    -VM $vm `
    -Publisher Microsoft.Powershell `
    -ExtensionName DSC `
    -Version 2.19 `
    -PublicConfiguration $PublicConfiguration `
    -PrivateConfiguration $PrivateConfiguration `
    -ForceUpdate

$VM | Update-AzureVM
```

## <a name="azure-virtual-machines"></a>Máquinas virtuais do Azure

Automation DSC do Azure permite carregar facilmente máquinas virtuais do Azure para a gestão de configuração, utilizando o portal do Azure, modelos Azure Resource Manager ou o PowerShell. Os bastidores e sem um administrador ter de remoto para a VM, a extensão de configuração de estado pretendido do Azure VM regista a VM no Automation DSC do Azure. Uma vez que a extensão de configuração de estado pretendido do Azure VM é executado no modo assíncrono, os passos para controlar o progresso da mesma ou a resolução de problemas são fornecidos no [ **integração de máquina virtual do Azure de resolução de problemas** ](#troubleshooting-azure-virtual-machine-onboarding) secção abaixo.

### <a name="azure-portal"></a>Portal do Azure

No [portal do Azure](https://portal.azure.com/), navegue para a conta de automatização do Azure onde pretende carregar máquinas virtuais. No painel de conta de automatização, clique em **nós de DSC** -> **adicionar a VM do Azure**.

Selecione uma máquina virtual do Azure para carregar.

Se a máquina não tem o PowerShell pretendido extensão de estado instalado e o estado de energia está em execução, clique em **Connect**.

Em **registo**, introduza o [valores de Gestor de configuração Local do PowerShell DSC](https://msdn.microsoft.com/powershell/dsc/metaconfig4) necessários para o caso de utilização e, opcionalmente, uma configuração de nó para atribuir à VM.

![](./media/automation-dsc-onboarding/DSC_Onboarding_6.png)

### <a name="azure-resource-manager-templates"></a>Modelos do Azure Resource Manager

Máquinas virtuais do Azure pode ser implementadas e integrado no DSC de automatização do Azure através de modelos Azure Resource Manager. Consulte [configurar uma VM através de extensão de DSC e Automation DSC do Azure](https://azure.microsoft.com/documentation/templates/dsc-extension-azure-automation-pullserver/) para um modelo de exemplo que onboards uma VM existente no DSC de automatização do Azure. Para localizar a chave de registo e o URL de registo tomada como entrada neste modelo, consulte o [ **Secure registo** ](#secure-registration) secção abaixo.

### <a name="powershell"></a>PowerShell

O [Register-AzureRmAutomationDscNode](/powershell/module/azurerm.automation/register-azurermautomationdscnode) cmdlet pode ser utilizado para carregar as máquinas virtuais no portal do Azure através do PowerShell.

## <a name="amazon-web-services-aws-virtual-machines"></a>Máquinas virtuais Amazon Web Services (AWS)

Pode carregar facilmente máquinas de virtuais Amazon Web Services para a gestão de configuração por DSC de automatização do Azure através do Toolkit de DSC AWS. Pode saber mais sobre o toolkit [aqui](https://blogs.msdn.microsoft.com/powershell/2016/04/20/aws-dsc-toolkit/).

## <a name="physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azureaws"></a>Windows físico virtual máquinas no local, ou numa nuvem diferente do Azure/AWS

Máquinas do Windows no local e as máquinas do Windows em nuvens não do Azure (por exemplo, Amazon Web Services) também podem ser integrado no DSC da automatização do Azure, desde que tenham acesso externo à internet, através de alguns passos simples:

1. Certifique-se a versão mais recente do [WMF 5](http://aka.ms/wmf5latest) está instalado nas máquinas que pretende integrar no DSC de automatização do Azure.
2. Siga as indicações na secção [ **gerar DSC metaconfigurations** ](#generating-dsc-metaconfigurations) abaixo para gerar uma pasta que contém o metaconfigurations DSC necessários.
3. Aplicam-se remotamente a configuração meta do DSC do PowerShell para as máquinas que pretende integrar. **A máquina este comando é executado a partir tem de ter a versão mais recente do [WMF 5](http://aka.ms/wmf5latest) instalado**:

    ```powershell
    Set-DscLocalConfigurationManager -Path C:\Users\joe\Desktop\DscMetaConfigs -ComputerName MyServer1, MyServer2
    ```

4. Se não é possível aplicar o metaconfigurations DSC do PowerShell remotamente, copie a pasta de metaconfigurations do passo 2 para cada máquina para carregar. Em seguida, chame **conjunto DscLocalConfigurationManager** localmente em cada máquina para carregar.
5. Utilizando os cmdlets, verifique se as máquinas para carregar são agora apresentadas como nós de DSC registados na sua conta de automatização do Azure ou do portal do Azure.

## <a name="physicalvirtual-linux-machines-on-premises-in-azure-or-in-a-cloud-other-than-azure"></a>Linux físico virtual máquinas no local, no Azure ou numa nuvem diferente do Azure

Máquinas do Linux no local, computadores Linux no Azure e os computadores Linux no Azure não nuvens também podem ser integradas no DSC da automatização do Azure, desde que tenham acesso externo à internet, através de alguns passos simples:

1. Certifique-se a versão mais recente do [PowerShell configuração de estado pretendido para Linux](https://github.com/Microsoft/PowerShell-DSC-for-Linux) está instalado nas máquinas que pretende integrar no DSC de automatização do Azure.
2. Se o [predefinições do Gestor de configuração Local do PowerShell DSC](https://msdn.microsoft.com/powershell/dsc/metaconfig4) corresponder ao seu caso de utilização e de que pretende carregar máquinas, tais que eles **ambos** solicitar a partir e comunicar no DSC de automatização do Azure:

   + Em cada computador Linux para ser integrado no Automation DSC do Azure, utilize Register.py para integrar com as predefinições do Gestor de configuração Local do PowerShell DSC:

     `/opt/microsoft/dsc/Scripts/Register.py <Automation account registration key> <Automation account registration URL>`

   + Para localizar a chave de registo e o URL de registo para a sua conta de automatização, consulte o [ **Secure registo** ](#secure-registration) secção abaixo.

     Se o Gestor de configuração Local do PowerShell DSC predefinições **fazer** **não** correspondência o caso de utilização ou pretende integrar máquinas de forma a que estes relatórios apenas no DSC da automatização do Azure, mas não não configuração de extração ou os módulos do PowerShell a partir do mesmo, siga os passos 3 a 6. Caso contrário, a prosseguir diretamente para o passo 6.

3. Siga as indicações o [ **gerar DSC metaconfigurations** ](#generating-dsc-metaconfigurations) secção abaixo para gerar uma pasta que contém o metaconfigurations DSC necessários.
4. Aplicam-se remotamente a configuração meta do DSC do PowerShell para as máquinas que pretende carregar:

    ```powershell
    $SecurePass = ConvertTo-SecureString -String "<root password>" -AsPlainText -Force
    $Cred = New-Object System.Management.Automation.PSCredential "root", $SecurePass
    $Opt = New-CimSessionOption -UseSsl -SkipCACheck -SkipCNCheck -SkipRevocationCheck

    # need a CimSession for each Linux machine to onboard

    $Session = New-CimSession -Credential $Cred -ComputerName <your Linux machine> -Port 5986 -Authentication basic -SessionOption $Opt

    Set-DscLocalConfigurationManager -CimSession $Session -Path C:\Users\joe\Desktop\DscMetaConfigs
    ```

A máquina este comando é executado a partir tem de ter a versão mais recente do [WMF 5](http://aka.ms/wmf5latest) instalado.

1. Se não é possível aplicar o metaconfigurations DSC do PowerShell remotamente, para cada computador Linux para carregar, copie a configuração meta do correspondente a essa máquina a partir da pasta no passo 5 para a máquina do Linux. Em seguida, chame `SetDscLocalConfigurationManager.py` localmente em cada computador Linux que pretende integrar no DSC de automatização do Azure:

   `/opt/microsoft/dsc/Scripts/SetDscLocalConfigurationManager.py -configurationmof <path to metaconfiguration file>`

2. Utilizando os cmdlets, verifique se as máquinas para carregar são agora apresentadas como nós de DSC registados na sua conta de automatização do Azure ou do portal do Azure.

## <a name="generating-dsc-metaconfigurations"></a>Gerar DSC metaconfigurations

Para carregar genericamente qualquer computador no DSC da automatização do Azure, uma [configuração meta do DSC](https://msdn.microsoft.com/en-us/powershell/dsc/metaconfig) pode ser gerada que, quando aplicada, indica o agente de DSC na máquina para solicitar a partir de e/ou reportar a Automation DSC do Azure. DSC metaconfigurations para Automation DSC do Azure pode ser gerada através de uma configuração de DSC do PowerShell ou os cmdlets do PowerShell de automatização do Azure.

> [!NOTE]
> DSC metaconfigurations contêm os segredos necessários para carregar uma máquina para uma automatização conta para a gestão. Certifique-se proteger corretamente qualquer metaconfigurations DSC que criar ou eliminá-los após a utilização.

### <a name="using-a-dsc-configuration"></a>Utilizar uma configuração de DSC

1. Abra o ISE do PowerShell como administrador num computador do ambiente local. A máquina tem de ter a versão mais recente do [WMF 5](http://aka.ms/wmf5latest) instalado.
2. Copie o seguinte script localmente. Este script contém uma configuração de DSC do PowerShell para criar metaconfigurations e um comando para iniciar a criação de configuração meta.

    ```powershell
    # The DSC configuration that will generate metaconfigurations
    [DscLocalConfigurationManager()]
    Configuration DscMetaConfigs
    {

        param
        (
            [Parameter(Mandatory=$True)]
            [String]$RegistrationUrl,

            [Parameter(Mandatory=$True)]
            [String]$RegistrationKey,

            [Parameter(Mandatory=$True)]
            [String[]]$ComputerName,

            [Int]$RefreshFrequencyMins = 30,

            [Int]$ConfigurationModeFrequencyMins = 15,

            [String]$ConfigurationMode = "ApplyAndMonitor",

            [String]$NodeConfigurationName,

            [Boolean]$RebootNodeIfNeeded= $False,

            [String]$ActionAfterReboot = "ContinueConfiguration",

            [Boolean]$AllowModuleOverwrite = $False,

            [Boolean]$ReportOnly
        )

        if(!$NodeConfigurationName -or $NodeConfigurationName -eq "")
        {
            $ConfigurationNames = $null
        }
        else
        {
            $ConfigurationNames = @($NodeConfigurationName)
        }

        if($ReportOnly)
        {
        $RefreshMode = "PUSH"
        }
        else
        {
        $RefreshMode = "PULL"
        }

        Node $ComputerName
        {

            Settings
            {
                RefreshFrequencyMins = $RefreshFrequencyMins
                RefreshMode = $RefreshMode
                ConfigurationMode = $ConfigurationMode
                AllowModuleOverwrite = $AllowModuleOverwrite
                RebootNodeIfNeeded = $RebootNodeIfNeeded
                ActionAfterReboot = $ActionAfterReboot
                ConfigurationModeFrequencyMins = $ConfigurationModeFrequencyMins
            }

            if(!$ReportOnly)
            {
            ConfigurationRepositoryWeb AzureAutomationDSC
                {
                    ServerUrl = $RegistrationUrl
                    RegistrationKey = $RegistrationKey
                    ConfigurationNames = $ConfigurationNames
                }

                ResourceRepositoryWeb AzureAutomationDSC
                {
                ServerUrl = $RegistrationUrl
                RegistrationKey = $RegistrationKey
                }
            }

            ReportServerWeb AzureAutomationDSC
            {
                ServerUrl = $RegistrationUrl
                RegistrationKey = $RegistrationKey
            }
        }
    }

    # Create the metaconfigurations
    # TODO: edit the below as needed for your use case
    $Params = @{
        RegistrationUrl = '<fill me in>';
        RegistrationKey = '<fill me in>';
        ComputerName = @('<some VM to onboard>', '<some other VM to onboard>');
        NodeConfigurationName = 'SimpleConfig.webserver';
        RefreshFrequencyMins = 30;
        ConfigurationModeFrequencyMins = 15;
        RebootNodeIfNeeded = $False;
        AllowModuleOverwrite = $False;
        ConfigurationMode = 'ApplyAndMonitor';
        ActionAfterReboot = 'ContinueConfiguration';
        ReportOnly = $False;  # Set to $True to have machines only report to AA DSC but not pull from it
    }

    # Use PowerShell splatting to pass parameters to the DSC configuration being invoked
    # For more info about splatting, run: Get-Help -Name about_Splatting
    DscMetaConfigs @Params
    ```

3. Preencha o URL e a chave de registo para a sua conta de automatização, bem como os nomes das máquinas para carregar. Todos os outros parâmetros são opcionais. Para localizar a chave de registo e o URL de registo para a sua conta de automatização, consulte o [ **Secure registo** ](#secure-registration) secção abaixo.
4. Se pretender que as máquinas para relatar informações de estado de DSC Automation DSC do Azure, mas não a configuração de extração ou módulos do PowerShell, defina o **ReportOnly** parâmetro como true.
5. Execute o script. Agora, deve ter uma pasta denominada **DscMetaConfigs** no seu diretório de trabalho, que contém o metaconfigurations DSC do PowerShell para as máquinas integrar (como administrador):

    ```powershell
    Set-DscLocalConfigurationManager -Path ./DscMetaConfigs
    ```

### <a name="using-the-azure-automation-cmdlets"></a>Utilizar os cmdlets de automatização do Azure

Se as predefinições do Gestor de configuração Local do PowerShell DSC corresponder ao seu caso de utilização e que pretende carregar máquinas para que possam tanto solicitar a partir e reportam a Automation DSC do Azure, os cmdlets de automatização do Azure fornecem um método simplificado para gerar o metaconfigurations DSC necessário:

1. Abra a consola do PowerShell ou o ISE do PowerShell como administrador num computador do ambiente local.
2. Ligar ao utilizar o Azure Resource Manager **Add-AzureRmAccount**
3. Transfira o metaconfigurations DSC do PowerShell para as máquinas que pretende para carregar da conta de automatização à qual pretende integrar nós:

    ```powershell
    # Define the parameters for Get-AzureRmAutomationDscOnboardingMetaconfig using PowerShell Splatting
    $Params = @{

        ResourceGroupName = 'ContosoResources'; # The name of the ARM Resource Group that contains your Azure Automation Account
        AutomationAccountName = 'ContosoAutomation'; # The name of the Azure Automation Account where you want a node on-boarded to
        ComputerName = @('web01', 'web02', 'sql01'); # The names of the computers that the meta configuration will be generated for
        OutputFolder = "$env:UserProfile\Desktop\";
    }
    # Use PowerShell splatting to pass parameters to the Azure Automation cmdlet being invoked
    # For more info about splatting, run: Get-Help -Name about_Splatting
    Get-AzureRmAutomationDscOnboardingMetaconfig @Params
    ```
    
4. Agora, deve ter uma pasta denominada ***DscMetaConfigs***, que contém o metaconfigurations DSC do PowerShell para as máquinas integrar (como administrador):
    
    ```powershell
    Set-DscLocalConfigurationManager -Path $env:UserProfile\Desktop\DscMetaConfigs
    ```

## <a name="secure-registration"></a>Registo seguro

As máquinas podem em segurança para uma conta de automatização do Azure através do protocolo de registo DSC do WMF 5, que permite que um nó de DSC autenticar para um servidor de solicitação do PowerShell DSC V2 ou relatórios (incluindo o Automation DSC do Azure). Regista o nó para o servidor de cada um **URL de registo**, autenticação utilizando um **chave de registo**. Durante o registo, o nó de DSC e o servidor de solicitação do DSC/relatórios negociar um certificado exclusivo para este nó utilizar para autenticação para o pós-registo de do servidor. Este processo impede que nós de integrado de representar um que outro, por exemplo, se um nó for comprometido e comportam de forma maliciosa. Após o registo, a chave de registo não é utilizada para autenticação novamente e é eliminada do nó.

Pode obter as informações necessárias para o protocolo de registo DSC do **gerir chaves** painel no portal de pré-visualização do Azure. Abrir este painel ao clicar no ícone de chave no **Essentials** painel da conta de automatização.

![](./media/automation-dsc-onboarding/DSC_Onboarding_4.png)

* URL de registo é o campo de URL no painel de gerir chaves.
* Chave de registo é a chave de acesso primária ou secundária de chave de acesso no painel de gerir chaves. A chave pode ser utilizada.

Para segurança adicional, as chaves de acesso primária e secundária de uma conta de automatização podem ser novamente geradas em qualquer altura (no **gerir chaves** painel) para impedir que os registos de nó futuras utilizando chaves anteriores.

## <a name="troubleshooting-azure-virtual-machine-onboarding"></a>Resolução de problemas de integração da máquina virtual do Azure

Automation DSC do Azure permite-lhe facilmente Windows VMs do Azure para gestão de configuração. Os bastidores, a extensão de configuração de estado pretendido do Azure VM é utilizada para registar a VM no Automation DSC do Azure. Uma vez que a extensão de configuração de estado pretendido do Azure VM é executado no modo assíncrono, ao controlar o progresso e a execução de resolução de problemas podem ser importantes.

> [!NOTE]
> Qualquer método de integração uma VM do Windows Azure no DSC da automatização do Azure que utiliza a extensão de configuração de estado pretendido do Azure VM pode demorar até uma hora para o nó Mostrar conforme registado na automatização do Azure. Isto acontece porque a instalação do Windows Management Framework 5.0 na VM pela extensão do DSC da VM do Azure, o que é necessária para carregar a VM do Azure Automation DSC.

Para resolver problemas ou ver o estado da extensão da configuração de estado pretendido do Azure VM, no portal do Azure, navegue para a VM que está a ser integrado, em seguida, clique em -> **todas as definições** -> **extensões** -> **DSC**. Para obter mais detalhes, pode clicar em **ver o estado detalhado**.

[![](./media/automation-dsc-onboarding/DSC_Onboarding_5.png)](https://technet.microsoft.com/library/dn249912.aspx)

## <a name="certificate-expiration-and-reregistration"></a>Expiração do certificado e o novo registo

Depois de registar uma máquina como um nó de DSC no Automation DSC do Azure, existem vários motivos por que razão poderá ter de voltar a registar esse nó no futuro:

* Depois de registar, cada nó negoceia automaticamente um certificado exclusivo para a autenticação expira após um ano. Atualmente, o protocolo de registo do PowerShell DSC não é possível renovar automaticamente certificados quando estes estão prestes a expirar, por isso terá de voltar a registar os nós após o tempo de um ano. Antes de ao registar novamente, certifique-se de que cada nó está a executar Windows Management Framework 5.0 RTM. Se expira do certificado de autenticação de um nó e o nó não é reregistered, o nó não é possível comunicar com a automatização do Azure e serão marcadas como 'Unresponsive'. O novo registo efetuada 90 dias ou menor do que a hora de expiração do certificado ou em qualquer momento depois da hora de expiração do certificado, irá resultar num novo certificado a ser gerado e utilizado.
* Para alterar as [valores de Gestor de configuração Local do PowerShell DSC](https://msdn.microsoft.com/powershell/dsc/metaconfig4) que foram definidas durante o registo inicial do nó, tais como ConfigurationMode. Atualmente, estes valores de agente do DSC só podem ser alterados através do novo registo. A única exceção é a configuração do nó atribuído para o nó – Isto pode ser alterado no Automation DSC do Azure diretamente.

O novo registo pode ser executado da mesma forma que registou o nó inicialmente, utilizando qualquer um dos métodos de integração descritos neste documento. Não é necessário anular o registo de um nó do DSC da automatização do Azure antes de ao registar novamente-lo.

## <a name="related-articles"></a>Artigos relacionados

* [Descrição geral do DSC da automatização do Azure](automation-dsc-overview.md)
* [Cmdlets do DSC da automatização do Azure](/powershell/module/azurerm.automation/#automation)
* [Preços do DSC da automatização do Azure](https://azure.microsoft.com/pricing/details/automation/)

---
title: "Implementar o serviço de mobilidade de recuperação de Site com o Automation DSC do Azure | Microsoft Docs"
description: "Descreve como utilizar o DSC de automatização do Azure para implementar automaticamente o serviço de mobilidade de recuperação de Site do Azure e o agente do Azure para a VM de VMware e replicação do servidor físico para o Azure"
services: site-recovery
documentationcenter: 
author: krnese
manager: lorenr
editor: 
ms.assetid: 1f8cd3ac-0522-48eb-a5f0-679ee9192ddb
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/22/2017
ms.author: krnese
ms.openlocfilehash: 118a2e775ae3d036f58989d9778104e372e8c701
ms.sourcegitcommit: 310748b6d66dc0445e682c8c904ae4c71352fef2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/28/2017
---
# <a name="deploy-the-mobility-service-with-azure-automation-dsc-for-replication-of-vm"></a>Implementar o serviço de mobilidade com Automation DSC do Azure para a replicação de VM
No Operations Management Suite, fornecemos-lhe com uma cópia de segurança completa e a solução de recuperação de desastre pode utilizar como parte do seu plano de continuidade do negócio.

Iniciamos este journey juntamente com o Hyper-V, utilizando a réplica do Hyper-V. Mas podemos ter alargada para suportar uma configuração heterogénea porque os clientes têm vários hipervisores e plataformas no respetivas nuvens.

Se estiver a executar cargas de trabalho do VMware e/ou servidores físicos hoje em dia, um servidor de gestão executa todos os componentes do Azure Site Recovery no seu ambiente para processar a replicação de dados e de comunicação com o Azure, quando o Azure é o destino.

## <a name="deploy-the-site-recovery-mobility-service-by-using-automation-dsc"></a>Implementar o serviço de mobilidade de recuperação de Site utilizando o DSC de automatização
Vamos começar por uma divisão rápida do que este servidor de gestão faz a fazer.

O servidor de gestão é executada várias funções de servidor. Uma dessas funções é *configuração*, que coordena a comunicação e gere os processos de replicação e recuperação de dados.

Além disso, o *processo* função atua como um gateway de replicação. Esta função recebe dados de replicação de máquinas de origem protegida, otimiza-as com colocação em cache, compressão e encriptação e, em seguida, envia-a para uma conta de armazenamento do Azure. Uma das funções para a função de processo também é a instalação push do serviço de mobilidade para máquinas protegidas e efetua a descoberta automática de VMs de VMware.

Se ocorrer uma reativação pós-falha a partir do Azure, o *destino mestre* função irá processar os dados de replicação como parte desta operação.

Para máquinas protegidas, precisamos do *serviço de mobilidade*. Este componente é implementado para cada máquina (VM de VMware ou servidor físico) que pretende replicar para o Azure. Este obtém dados, escreve na máquina e reencaminha-os para o servidor de gestão (função de processo).

Quando estiver a lidar com a continuidade do negócio, é importante compreender as cargas de trabalho, a infraestrutura e os componentes envolvidos. Em seguida, pode preencher os requisitos para o seu objetivo de tempo de recuperação (RTO) e o objetivo de ponto de recuperação (RPO). Neste contexto, o serviço de mobilidade é importante para se certificar de que as cargas de trabalho estão protegidas como seria de esperar.

Como, por isso, pode, de uma forma otimizada, certifique-se de que tem uma configuração fiável protegida com a ajuda de alguns componentes do Operations Management Suite?

Este artigo fornece um exemplo de como pode utilizar o Automation DSC do Azure pretendido Estado Configuration (), juntamente com a recuperação de Site, para se certificar de que:

* O serviço de mobilidade e o agente da VM do Azure são implementadas para as máquinas do Windows que pretende proteger.
* O serviço de mobilidade e o agente da VM do Azure estão sempre em execução quando o Azure é o destino de replicação.

## <a name="prerequisites"></a>Pré-requisitos
* Um repositório para armazenar o programa de configuração necessário
* Um repositório para armazenar o frase de acesso necessária para registar o servidor de gestão

  > [!NOTE]
  > Uma frase de acesso exclusivo é gerado para cada servidor de gestão. Se pretender implementar vários servidores de gestão, tem de garantir que a frase de acesso correta é armazenado no ficheiro passphrase.txt.
  >
  >
* Windows Management Framework (WMF) 5.0 instalado nas máquinas que pretende ativar a proteção (um requisito de DSC de automatização)

  > [!NOTE]
  > Se pretender utilizar máquinas de DSC para Windows que tenham o WMF 4.0 instalado, consulte a secção [DSC utilizar em ambientes desligados](## Use DSC in disconnected environments).
  

O serviço de mobilidade pode ser instalado através da linha de comandos e aceita vários argumentos. É por esse motivo tem de ter os binários (depois de as extrair do seu programa de configuração) e armazená-las num local onde pode obtê-los utilizando uma configuração de DSC.

## <a name="step-1-extract-binaries"></a>Passo 1: Os binários extrair
1. Para extrair os ficheiros que sejam necessárias para esta configuração, navegue para o diretório no servidor de gestão seguinte:

    **\Microsoft Recovery\home\svsystems\pushinstallsvc\repository de sites do azure**

    Nesta pasta, deverá ver um ficheiro MSI designado:

    **Microsoft-ASR_UA_version_Windows_GA_date_Release.exe**

    Utilize o seguinte comando para extrair o instalador:

    **.\Microsoft-ASR_UA_9.1.0.0_Windows_GA_02May2016_release.exe /q /x:C:\Users\Administrator\Desktop\Mobility_Service\Extract**
2. Selecione todos os ficheiros e enviá-los para uma pasta comprimida (zipped).

Tem agora os binários que precisa para automatizar a configuração do serviço de mobilidade utilizando o DSC de automatização.

### <a name="passphrase"></a>Frase de acesso
Em seguida, precisa de determinar onde pretende colocar esta pasta zipped. Pode utilizar uma conta de armazenamento do Azure, conforme mostrado posteriormente, para armazenar o frase de acesso que terá para o programa de configuração. O agente, em seguida, vai registar com o servidor de gestão como parte do processo.

A frase de acesso que recebeu quando implementou o servidor de gestão pode ser guardado num ficheiro de texto como passphrase.txt.

Coloque a pasta zipped e o frase de acesso num contentor dedicado na conta do storage do Azure.

![Localização da pasta](./media/site-recovery-automate-mobilitysevice-install/folder-and-passphrase-location.png)

Se preferir manter estes ficheiros numa partilha na sua rede, pode fazê. Terá de garantir que os recursos de DSC que irá utilizar mais tarde tem acesso e podem obter a configuração e o frase de acesso.

## <a name="step-2-create-the-dsc-configuration"></a>Passo 2: Criar a configuração DSC
O programa de configuração depende WMF 5.0. Para a máquina aplicar com êxito a configuração através do DSC da automatização, WMF 5.0 tem de estar presente.

O ambiente utiliza a configuração de DSC de exemplo seguinte:

```powershell
configuration ASRMobilityService {

    $RemoteFile = 'https://knrecstor01.blob.core.windows.net/asr/ASR.zip'
    $RemotePassphrase = 'https://knrecstor01.blob.core.windows.net/asr/passphrase.txt'
    $RemoteAzureAgent = 'http://go.microsoft.com/fwlink/p/?LinkId=394789'
    $LocalAzureAgent = 'C:\Temp\AzureVmAgent.msi'
    $TempDestination = 'C:\Temp\asr.zip'
    $LocalPassphrase = 'C:\Temp\Mobility_service\passphrase.txt'
    $Role = 'Agent'
    $Install = 'C:\Program Files (x86)\Microsoft Azure Site Recovery'
    $CSEndpoint = '10.0.0.115'
    $Arguments = '/Role "{0}" /InstallLocation "{1}" /CSEndpoint "{2}" /PassphraseFilePath "{3}"' -f $Role,$Install,$CSEndpoint,$LocalPassphrase

    Import-DscResource -ModuleName xPSDesiredStateConfiguration

    node localhost {

        File Directory {
            DestinationPath = 'C:\Temp\ASRSetup\'
            Type = 'Directory'            
        }

        xRemoteFile Setup {
            URI = $RemoteFile
            DestinationPath = $TempDestination
            DependsOn = '[File]Directory'
        }

        xRemoteFile Passphrase {
            URI = $RemotePassphrase
            DestinationPath = $LocalPassphrase
            DependsOn = '[File]Directory'
        }

        xRemoteFile AzureAgent {
            URI = $RemoteAzureAgent
            DestinationPath = $LocalAzureAgent
            DependsOn = '[File]Directory'
        }

        Archive ASRzip {
            Path = $TempDestination
            Destination = 'C:\Temp\ASRSetup'
            DependsOn = '[xRemotefile]Setup'
        }

        Package Install {
            Path = 'C:\temp\ASRSetup\ASR\UNIFIEDAGENT.EXE'
            Ensure = 'Present'
            Name = 'Microsoft Azure Site Recovery mobility Service/Master Target Server'
            ProductId = '275197FC-14FD-4560-A5EB-38217F80CBD1'
            Arguments = $Arguments
            DependsOn = '[Archive]ASRzip'
        }

        Package AzureAgent {
            Path = 'C:\Temp\AzureVmAgent.msi'
            Ensure = 'Present'
            Name = 'Windows Azure VM Agent - 2.7.1198.735'
            ProductId = '5CF4D04A-F16C-4892-9196-6025EA61F964'
            Arguments = '/q /l "c:\temp\agentlog.txt'
            DependsOn = '[Package]Install'
        }

        Service ASRvx {
            Name = 'svagents'
            Ensure = 'Present'
            State = 'Running'
            DependsOn = '[Package]Install'
        }

        Service ASR {
            Name = 'InMage Scout Application Service'
            Ensure = 'Present'
            State = 'Running'
            DependsOn = '[Package]Install'
        }

        Service AzureAgentService {
            Name = 'WindowsAzureGuestAgent'
            Ensure = 'Present'
            State = 'Running'
            DependsOn = '[Package]AzureAgent'
        }

        Service AzureTelemetry {
            Name = 'WindowsAzureTelemetryService'
            Ensure = 'Present'
            State = 'Running'
            DependsOn = '[Package]AzureAgent'
        }
    }
}
```
A configuração será efetuado o seguinte:

* As variáveis informará a configuração onde obter os binários para o serviço de mobilidade e o agente de VM do Azure, onde obter o frase de acesso e onde pretende guardar a saída.
* A configuração irá importar o recurso de xPSDesiredStateConfiguration DSC para que possa utilizar `xRemoteFile` para transferir os ficheiros do repositório.
* A configuração irá criar um diretório onde pretende armazenar os binários.
* O recurso de arquivo será extraia os ficheiros da pasta zipped.
* O pacote de recursos de instalação irá instalar o serviço de mobilidade do UNIFIEDAGENT. Instalador. EXE com os argumentos específicos. (As variáveis que construir os argumentos tem de ser alterada para refletir o seu ambiente.)
* O pacote de recursos de AzureAgent irá instalar o agente de VM do Azure, recomenda-se em cada VM que é executada no Azure. O agente da VM do Azure também possibilita adicionar extensões para a VM após a ativação pós-falha.
* Os recursos ou recurso de serviço irão garantir que os serviços de mobilidade relacionados e os serviços do Azure estão sempre em execução.

Guardar a configuração como **ASRMobilityService**.

> [!NOTE]
> Não se esqueça de substituir o CSIP na configuração para refletir o servidor de gestão real, para que o agente será ligado corretamente e utilizará o frase de acesso correto.
>
>

## <a name="step-3-upload-to-automation-dsc"></a>Passo 3: Carregar o DSC de automatização
Porque a configuração de DSC que efetuou irá importar um módulo de recursos de DSC necessário (xPSDesiredStateConfiguration), terá de importar este módulo de automatização antes de carregar a configuração de DSC.

Inicie sessão na sua conta de automatização, navegue até à **ativos** > **módulos**e clique em **procurar galeria**.

Aqui pode pesquisar para o módulo e importe-o à sua conta.

![Módulo de importação](./media/site-recovery-automate-mobilitysevice-install/search-and-import-module.png)

Quando concluir isto, aceda ao seu computador em que tenha os módulos do Azure Resource Manager instalados e continue para importar a configuração de DSC recentemente criada.

### <a name="import-cmdlets"></a>Cmdlets de importação
No PowerShell, inicie sessão sua subscrição do Azure. Modificar os cmdlets para refletir o seu ambiente e capturar as informações da conta de automatização numa variável:

```powershell
$AAAccount = Get-AzureRmAutomationAccount -ResourceGroupName 'KNOMS' -Name 'KNOMSAA'
```

Carregar a configuração para o DSC de automatização utilizando o cmdlet seguinte:

```powershell
$ImportArgs = @{
    SourcePath = 'C:\ASR\ASRMobilityService.ps1'
    Published = $true
    Description = 'DSC Config for Mobility Service'
}
$AAAccount | Import-AzureRmAutomationDscConfiguration @ImportArgs
```

### <a name="compile-the-configuration-in-automation-dsc"></a>Compilar a configuração no Automation DSC
Em seguida, tem de compilar a configuração no DSC da automatização, para que possa começar registar nós à mesma. Alcançar que executando o seguinte cmdlet:

```powershell
$AAAccount | Start-AzureRmAutomationDscCompilationJob -ConfigurationName ASRMobilityService
```

Isto pode demorar alguns minutos, porque basicamente estiver a implementar a configuração para o serviço de solicitação do DSC alojado.

Depois de compilar a configuração, pode obter as informações de tarefa com o PowerShell (Get-AzureRmAutomationDscCompilationJob) ou utilizando o [portal do Azure](https://portal.azure.com/).

![Obter a tarefa](./media/site-recovery-automate-mobilitysevice-install/retrieve-job.png)

Tem agora com êxito publicado e carregar a configuração de DSC no DSC da automatização.

## <a name="step-4-onboard-machines-to-automation-dsc"></a>Passo 4: Máquinas carregar no Automation DSC
> [!NOTE]
> Um dos pré-requisitos para concluir este cenário é que as máquinas do Windows são atualizadas com a versão mais recente do WMF. Pode transferir e instalar a versão correta para a sua plataforma a partir de [Centro de transferências](https://www.microsoft.com/download/details.aspx?id=50395).
>
>

Agora, irá criar um metaconfig para DSC que será aplicada aos seus nós. Para ter êxito com esta opção, terá de obter o URL de ponto final e a chave primária para a sua conta de automatização selecionada no Azure. Pode encontrar estes valores em **chaves** no **todas as definições** painel da conta de automatização.

![Valores da chave](./media/site-recovery-automate-mobilitysevice-install/key-values.png)

Neste exemplo, é necessário um servidor físico do Windows Server 2012 R2 que pretende proteger os utilizando a recuperação de Site.

### <a name="check-for-any-pending-file-rename-operations-in-the-registry"></a>Verifique a existência de quaisquer operações de mudança de nome de ficheiro no registo pendentes
Antes de começar a associar o servidor com o ponto final de DSC de automatização, recomendamos que verifique se existem quaisquer operações de mudança de nome de ficheiro no registo pendentes. O programa de configuração poderá proíbem a partir de concluir devido a um reinício pendente.

Execute o cmdlet seguinte para verificar que não há nenhum reinício pendente no servidor:

```powershell
Get-ItemProperty 'HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\' | Select-Object -Property PendingFileRenameOperations
```
Se esta ação mostra vazia, está OK para continuar. Caso contrário, deve resolver isto pelo servidor a ser reiniciado durante uma janela de manutenção.

Para aplicar a configuração no servidor, inicie o PowerShell Integrated Scripting Environment (ISE) e execute o seguinte script. Isto é, essencialmente, uma configuração local do DSC que instruirá o motor de Gestor de configuração locais para registar com o serviço de DSC de automatização e obter a configuração específica (ASRMobilityService.localhost).

```powershell
[DSCLocalConfigurationManager()]
configuration metaconfig {
    param (
        $URL,
        $Key
    )
    node localhost {
        Settings {
            RefreshFrequencyMins = '30'
            RebootNodeIfNeeded = $true
            RefreshMode = 'PULL'
            ActionAfterReboot = 'ContinueConfiguration'
            ConfigurationMode = 'ApplyAndMonitor'
            AllowModuleOverwrite = $true
        }

        ResourceRepositoryWeb AzureAutomationDSC {
            ServerURL = $URL
            RegistrationKey = $Key
        }

        ConfigurationRepositoryWeb AzureAutomationDSC {
            ServerURL = $URL
            RegistrationKey = $Key
            ConfigurationNames = 'ASRMobilityService.localhost'
        }

        ReportServerWeb AzureAutomationDSC {
            ServerURL = $URL
            RegistrationKey = $Key
        }
    }
}
metaconfig -URL 'https://we-agentservice-prod-1.azure-automation.net/accounts/<YOURAAAccountID>' -Key '<YOURAAAccountKey>'

Set-DscLocalConfigurationManager .\metaconfig -Force -Verbose
```

Esta configuração irá fazer com que o motor de Gestor de configuração locais para se registar no Automation DSC. Este também determina como o motor deve funcionar, o deve fazer se não houver um que se desviam configuration (ApplyAndAutoCorrect) e como deve continuar com a configuração se é necessário um reinício.

Depois de executar este script, o nó deve começar a registar o DSC de automatização.

![Registo de nó em curso](./media/site-recovery-automate-mobilitysevice-install/register-node.png)

Se regressar ao portal do Azure, pode ver que o nó recém-registado tem agora apareceu no portal.

![Nó registado no portal](./media/site-recovery-automate-mobilitysevice-install/registered-node.png)

No servidor, pode executar o seguinte cmdlet do PowerShell para verificar se o nó foi registado corretamente:

```powershell
Get-DscLocalConfigurationManager
```

Depois de serem solicitada e aplicada ao servidor de configuração, pode verificar isto, executando o seguinte cmdlet:

```powershell
Get-DscConfigurationStatus
```

O resultado mostra que o servidor com êxito tem solicitados a respetiva configuração:

![Saída](./media/site-recovery-automate-mobilitysevice-install/successful-config.png)

Além disso, o programa de configuração do serviço de mobilidade tem o seu próprio registo que pode ser encontrado em *SystemDrive*\ProgramData\ASRSetupLogs.

Já está. Agora com êxito implementou e registado o serviço de mobilidade na máquina que pretende proteger os utilizando a recuperação de Site. DSC irá garantir que os serviços necessários estão sempre em execução.

![Implementação efetuada com êxito](./media/site-recovery-automate-mobilitysevice-install/successful-install.png)

Depois do servidor de gestão Deteta a implementação com êxito, pode configurar a proteção e ativar a replicação na máquina utilizando a recuperação de Site.

## <a name="use-dsc-in-disconnected-environments"></a>Utilizar o DSC em ambientes desligados
Se as máquinas não estão ligadas à Internet, pode confiar no DSC para implementar e configurar o serviço de mobilidade nas cargas de trabalho que pretende proteger.

Pode instanciar o seu próprio servidor de solicitação do DSC no seu ambiente para fornecer essencialmente as mesmas capacidades que obtém a partir do Automation DSC. Ou seja, os clientes irão solicitar a configuração (após estar registado) para o ponto final de DSC. No entanto, outra opção é emitir manualmente a configuração de DSC para as máquinas, localmente ou remotamente.

Tenha em atenção que neste exemplo, existe um parâmetro adicionado para o nome do computador. Os ficheiros remotos estão agora localizados numa partilha remota que deve estar acessível pelas máquinas que pretende proteger. O fim do script enacts a configuração e, em seguida, começa a aplicar a configuração de DSC ao computador de destino.

### <a name="prerequisites"></a>Pré-requisitos
Certifique-se de que o módulo do PowerShell xPSDesiredStateConfiguration está instalado. Para máquinas de Windows em que o WMF 5.0 é instalado, pode instalar o módulo de xPSDesiredStateConfiguration executando o seguinte cmdlet nos computadores de destino:

```powershell
Find-Module -Name xPSDesiredStateConfiguration | Install-Module
```

Também pode transferir e guardar o módulo no caso de necessitar distribui-lo para as máquinas do Windows que tem o WMF 4.0. Execute este cmdlet num computador onde se encontra presente PowerShellGet (WMF 5.0):

```powershell
Save-Module -Name xPSDesiredStateConfiguration -Path <location>
```

Também para o WMF 4.0, certifique-se de que o [Windows 8.1 update KB2883200](https://www.microsoft.com/download/details.aspx?id=40749) está instalado nos computadores.

A seguinte configuração pode ser enviada a máquinas do Windows que tenham WMF 5.0 e o WMF 4.0:

```powershell
configuration ASRMobilityService {
    param (
        [Parameter(Mandatory=$true)]
        [ValidateNotNullOrEmpty()]
        [System.String] $ComputerName
    )

    $RemoteFile = '\\myfileserver\share\asr.zip'
    $RemotePassphrase = '\\myfileserver\share\passphrase.txt'
    $RemoteAzureAgent = '\\myfileserver\share\AzureVmAgent.msi'
    $LocalAzureAgent = 'C:\Temp\AzureVmAgent.msi'
    $TempDestination = 'C:\Temp\asr.zip'
    $LocalPassphrase = 'C:\Temp\Mobility_service\passphrase.txt'
    $Role = 'Agent'
    $Install = 'C:\Program Files (x86)\Microsoft Azure Site Recovery'
    $CSEndpoint = '10.0.0.115'
    $Arguments = '/Role "{0}" /InstallLocation "{1}" /CSEndpoint "{2}" /PassphraseFilePath "{3}"' -f $Role,$Install,$CSEndpoint,$LocalPassphrase

    Import-DscResource -ModuleName xPSDesiredStateConfiguration

    node $ComputerName {      
        File Directory {
            DestinationPath = 'C:\Temp\ASRSetup\'
            Type = 'Directory'            
        }

        xRemoteFile Setup {
            URI = $RemoteFile
            DestinationPath = $TempDestination
            DependsOn = '[File]Directory'
        }

        xRemoteFile Passphrase {
            URI = $RemotePassphrase
            DestinationPath = $LocalPassphrase
            DependsOn = '[File]Directory'
        }

        xRemoteFile AzureAgent {
            URI = $RemoteAzureAgent
            DestinationPath = $LocalAzureAgent
            DependsOn = '[File]Directory'
        }

        Archive ASRzip {
            Path = $TempDestination
            Destination = 'C:\Temp\ASRSetup'
            DependsOn = '[xRemotefile]Setup'
        }

        Package Install {
            Path = 'C:\temp\ASRSetup\ASR\UNIFIEDAGENT.EXE'
            Ensure = 'Present'
            Name = 'Microsoft Azure Site Recovery mobility Service/Master Target Server'
            ProductId = '275197FC-14FD-4560-A5EB-38217F80CBD1'
            Arguments = $Arguments
            DependsOn = '[Archive]ASRzip'
        }

        Package AzureAgent {
            Path = 'C:\Temp\AzureVmAgent.msi'
            Ensure = 'Present'
            Name = 'Windows Azure VM Agent - 2.7.1198.735'
            ProductId = '5CF4D04A-F16C-4892-9196-6025EA61F964'
            Arguments = '/q /l "c:\temp\agentlog.txt'
            DependsOn = '[Package]Install'
        }

        Service ASRvx {
            Name = 'svagents'
            State = 'Running'
            DependsOn = '[Package]Install'
        }

        Service ASR {
            Name = 'InMage Scout Application Service'
            State = 'Running'
            DependsOn = '[Package]Install'
        }

        Service AzureAgentService {
            Name = 'WindowsAzureGuestAgent'
            State = 'Running'
            DependsOn = '[Package]AzureAgent'
        }

        Service AzureTelemetry {
            Name = 'WindowsAzureTelemetryService'
            State = 'Running'
            DependsOn = '[Package]AzureAgent'
        }
    }
}
ASRMobilityService -ComputerName 'MyTargetComputerName'

Start-DscConfiguration .\ASRMobilityService -Wait -Force -Verbose
```

Se pretender instanciar o seu próprio servidor de solicitação do DSC na sua rede empresarial para imitar as capacidades que pode obter a partir do Automation DSC, consulte [configurar um servidor de solicitação do DSC web](https://msdn.microsoft.com/powershell/dsc/pullserver?f=255&MSPPError=-2147217396).

## <a name="optional-deploy-a-dsc-configuration-by-using-an-azure-resource-manager-template"></a>Opcional: Implementar uma configuração de DSC utilizando um modelo Azure Resource Manager
Este artigo tem centra-se na forma como pode criar sua própria configuração de DSC para implementar o serviço de mobilidade e o agente da VM do Azure – e certifique-se automaticamente que estejam a ser executados nas máquinas que pretende proteger. Além disso, temos um modelo Azure Resource Manager que irá implementar esta configuração de DSC para uma conta de automatização do Azure nova ou existente. O modelo irá utilizar os parâmetros de entrada para criar recursos de automatização que irão conter as variáveis para o seu ambiente.

Depois de implementar o modelo, pode simplesmente Consultar passo 4 neste guia para carregar as máquinas.

O modelo será efetuado o seguinte:

1. Utilizar uma conta de automatização existente ou crie um novo
2. Obtenha os parâmetros de entrada para:
   * ASRRemoteFile – a localização em que tem armazenados o programa de configuração do serviço de mobilidade
   * ASRPassphrase – a localização onde tem armazenou o ficheiro de passphrase.txt
   * ASRCSEndpoint – o endereço IP do seu servidor de gestão
3. Importe o módulo do PowerShell xPSDesiredStateConfiguration
4. Criar e compilar a configuração DSC

Todos os passos anteriores irão acontecer na ordem correta, para que possa começar a integração do seu máquinas para proteção.

O modelo, com instruções para a implementação, está localizado no [GitHub](https://github.com/krnese/AzureDeploy/tree/master/OMS/MSOMS/DSC).

Implemente o modelo utilizando o PowerShell:

```powershell
$RGDeployArgs = @{
    Name = 'DSC3'
    ResourceGroupName = 'KNOMS'
    TemplateFile = 'https://raw.githubusercontent.com/krnese/AzureDeploy/master/OMS/MSOMS/DSC/azuredeploy.json'
    OMSAutomationAccountName = 'KNOMSAA'
    ASRRemoteFile = 'https://knrecstor01.blob.core.windows.net/asr/ASR.zip'
    ASRRemotePassphrase = 'https://knrecstor01.blob.core.windows.net/asr/passphrase.txt'
    ASRCSEndpoint = '10.0.0.115'
    DSCJobGuid = [System.Guid]::NewGuid().ToString()
}
New-AzureRmResourceGroupDeployment @RGDeployArgs -Verbose
```

## <a name="next-steps"></a>Passos seguintes
Depois de implementar os agentes do serviço de mobilidade, pode [ativar a replicação](site-recovery-vmware-to-azure.md) para as máquinas virtuais.

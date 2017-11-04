---
title: "Replicar VMs de Hyper-V no VMM para um site secundário com o PowerShell (Azure Resource Manager) | Microsoft Docs"
description: "Descreve como implementar o Azure Site Recovery para orquestrar a replicação, ativação pós-falha e recuperação de VMs de Hyper-V em nuvens VMM para um site VMM secundário com o PowerShell (Resource Manager)"
services: site-recovery
documentationcenter: 
author: sujaytalasila
manager: rochakm
editor: raynew
ms.assetid: 9d38e9c3-217c-4e44-830c-575e9a4141f2
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/19/2017
ms.author: sutalasi
ms.openlocfilehash: c978c2e31e775f56824d765491f6d7b73648b8ae
ms.sourcegitcommit: 76a3cbac40337ce88f41f9c21a388e21bbd9c13f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/25/2017
---
# <a name="replicate-hyper-v-virtual-machines-in-vmm-clouds-to-a-secondary-vmm-site-using-powershell-resource-manager"></a>Replicar máquinas virtuais de Hyper-V em nuvens VMM para um site VMM secundário com o PowerShell (Resource Manager)

Este artigo mostra como utilizar o PowerShell para automatizar tarefas comuns quando configurou o Azure Site Recovery para replicar máquinas virtuais de Hyper-V em nuvens VMM do System Center em nuvens VMM do System Center num site secundário.



## <a name="on-premises-prerequisites"></a>Pré-requisitos no local
Eis o que necessita nos sites primários e secundários no local para implementar este cenário:

| **Pré-requisitos** | **Detalhes** |
| --- | --- |
| **VMM** |Recomendamos que implemente um servidor VMM no site primário e um servidor do VMM no site secundário.<br/><br/> Também pode replicar entre nuvens num único servidor VMM. Para efetuar este procedimento precisará, pelo menos, dois nuvens configuradas no servidor do VMM.<br/><br/> Servidores do VMM devem estar em execução, pelo menos, System Center 2012 SP1 com as atualizações mais recentes.<br/><br/> Cada servidor VMM tem de ter em um ou mais nuvens configuradas e todas as nuvens têm de ter o conjunto de perfis de capacidade Hyper-V. <br/><br/>Nuvens tem de conter um ou mais grupos de anfitriões do VMM. Servidores do VMM devem ter acesso à internet. |
| **Hyper-V** |Servidores Hyper-V tem de estar em execução, pelo menos, Windows Server 2012 com a função Hyper-V e as atualizações mais recentes instaladas.<br/><br/> Um servidor Hyper-V deve conter uma ou mais VMs.<br/><br/>  Servidores de anfitrião Hyper-V devem estar localizados em grupos de anfitriões em nuvens VMM primárias e secundárias.<br/><br/> Se estiver a executar o Hyper-V num cluster no Windows Server 2012 R2, deverá instalar [atualizar 2961977](https://support.microsoft.com/kb/2961977)<br/><br/> Se estiver a executar o Hyper-V num cluster no Windows Server 2012 nota que Mediador de clusters não é criado automaticamente se tiver um cluster de baseadas no endereço IP estático. Precisará de configurar manualmente o mediador de clusters. [Leia mais](http://social.technet.microsoft.com/wiki/contents/articles/18792.configure-replica-broker-role-cluster-to-cluster-replication.aspx). |
| **Fornecedor** |Durante a implementação da recuperação de Site instale o fornecedor do Azure Site Recovery nos servidores VMM. O fornecedor comunica com a recuperação de Site através de HTTPS 443 para orquestrar a replicação. Ocorre a replicação de dados entre os servidores de Hyper-V principais e secundários através da rede local ou uma ligação VPN.<br/><br/> O fornecedor em execução no servidor do VMM precisa de aceder a estes URLs: *. hypervrecoverymanager.windowsazure.com; *. accesscontrol.windows.net; *. backup.windowsazure.com; *. w; *. store.core.windows.net.<br/><br/> Além disso, permitir comunicação de firewall entre os servidores do VMM para o [intervalos IP do datacenter do Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653) e permitir que o protocolo HTTPS (443). |

### <a name="network-mapping-prerequisites"></a>Pré-requisitos de mapeamento da rede
Mapas de mapeamento de rede entre redes de VM do VMM nos servidores VMM primários e secundários fazer o seguinte:

* Ideal colocar as VMs da réplica em anfitriões de Hyper-V secundários após a ativação pós-falha.
* Ligar as VMs da réplica para redes VM adequadas.
* Se não configurar a réplica de mapeamento de rede que VMS não serão ligadas a nenhuma rede após a ativação pós-falha.
* Se pretender configurar rede mapeamento durante a recuperação de Site implementação aqui é o que necessita:

  * Certifique-se de que as VMs no servidor de anfitrião Hyper-V de origem estão ligadas a uma rede VM do VMM. Essa rede deve ser ligada a uma rede lógica que está associada à nuvem.
  * Certifique-se de que a nuvem secundária que irá utilizar para recuperação tem uma rede VM correspondente configurada. Essa rede VM deve ser ligada a uma rede lógica que tiver associado à nuvem secundária.

Saiba mais sobre como configurar redes VMM no abaixo artigos

* [Como configurar redes lógicas no VMM](http://go.microsoft.com/fwlink/p/?LinkId=386307)
* [Como configurar gateways e redes VM no VMM](http://go.microsoft.com/fwlink/p/?LinkId=386308)


### <a name="powershell-prerequisites"></a>Pré-requisitos do PowerShell
Certifique-se de que tem o Azure PowerShell pronto para começar. Se já estiver a utilizar o PowerShell, terá de atualizar para a versão 0.8.10 ou posterior. Para obter informações sobre como configurar o PowerShell, consulte o [guia para instalar e configurar o Azure PowerShell](/powershell/azureps-cmdlets-docs). Assim que tiver definido e configurado o PowerShell, pode ver todos os cmdlets disponíveis para o serviço [aqui](/powershell/azure/overview).

Para saber mais sobre as sugestões que podem ajudar a utilizar os cmdlets, como a forma como os valores de parâmetros, entradas e saídas normalmente são processadas no Azure PowerShell, consulte o [guia de introdução aos Cmdlets do Azure](/powershell/azure/get-started-azureps).

## <a name="step-1-set-the-subscription"></a>Passo 1: Definir a subscrição
1. A partir do powershell do Azure, o início de sessão para a sua conta do Azure: utilizar os seguintes cmdlets

        $UserName = "<user@live.com>"
        $Password = "<password>"
        $SecurePassword = ConvertTo-SecureString -AsPlainText $Password -Force
        $Cred = New-Object System.Management.Automation.PSCredential -ArgumentList $UserName, $SecurePassword
        Login-AzureRmAccount #-Credential $Cred
2. Obter uma lista das suas subscrições. Isto também irá listar o subscriptionIDs para cada uma das subscrições. Anotar o ID de subscrição da subscrição na qual pretende criar o Cofre de serviços de recuperação    

        Get-AzureRmSubscription
3. Definir a subscrição em que o Cofre de serviços de recuperação está a ser criado pela mentioning o ID de subscrição

        Set-AzureRmContext –SubscriptionID <subscriptionId>

## <a name="step-2-create-a-recovery-services-vault"></a>Passo 2: crie um cofre dos Serviços de Recuperação
1. Se ainda não tiver uma, crie um grupo de recursos do Azure Resource Manager

        New-AzureRmResourceGroup -Name #ResourceGroupName -Location #location
2. Crie um novo cofre de serviços de recuperação e guarde o objeto de cofre dos serviços de recuperação numa variável (serão utilizados mais tarde). Também pode obter a criação do cofre objeto post utilizando o cmdlet Get-AzureRMRecoveryServicesVault:-

        $vault = New-AzureRmRecoveryServicesVault -Name #vaultname -ResouceGroupName #ResourceGroupName -Location #location

## <a name="step-3-set-the-recovery-services-vault-context"></a>Passo 3: Definir o contexto do Cofre de serviços de recuperação
1. Se tiver um cofre já criado, execute o comando para obter o Cofre abaixo.

       $vault = Get-AzureRmRecoveryServicesVault -Name #vaultname
2. Definir o contexto do cofre, executando o comando abaixo.

       Set-AzureRmSiteRecoveryVaultSettings -ARSVault $vault

## <a name="step-4-install-the-azure-site-recovery-provider"></a>Passo 4: Instalar o fornecedor do Azure Site Recovery
1. Na máquina do VMM, crie um diretório, executando o seguinte comando:

       New-Item c:\ASR -type directory
2. Extraia os ficheiros utilizando o fornecedor de transferido executando o seguinte comando

       pushd C:\ASR\
       .\AzureSiteRecoveryProvider.exe /x:. /q
3. Instale o fornecedor a utilizar os seguintes comandos:

       .\SetupDr.exe /i
       $installationRegPath = "hklm:\software\Microsoft\Microsoft System Center Virtual Machine Manager Server\DRAdapter"
       do
       {
         $isNotInstalled = $true;
         if(Test-Path $installationRegPath)
         {
           $isNotInstalled = $false;
         }
       }While($isNotInstalled)

   Aguarde a conclusão da instalação.
4. Registe o servidor no cofre utilizando o seguinte comando:

       $BinPath = $env:SystemDrive+"\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin"
       pushd $BinPath
       $encryptionFilePath = "C:\temp\".\DRConfigurator.exe /r /Credentials $VaultSettingFilePath /vmmfriendlyname $env:COMPUTERNAME /dataencryptionenabled $encryptionFilePath /startvmmservice

## <a name="step-5-create-and-associate-a-replication-policy"></a>Passo 5: Criar e associar uma política de replicação
1. Crie uma política de replicação de Hyper-V R2 de 2012, executando o seguinte comando:

        $ReplicationFrequencyInSeconds = "300";        #options are 30,300,900
        $PolicyName = “replicapolicy”
        $RepProvider = HyperVReplica2012R2
        $Recoverypoints = 24                    #specify the number of hours to retain recovery pints
        $AppConsistentSnapshotFrequency = 4 #specify the frequency (in hours) at which app consistent snapshots are taken
        $AuthMode = "Kerberos"  #options are "Kerberos" or "Certificate"
        $AuthPort = "8083"  #specify the port number that will be used for replication traffic on Hyper-V hosts
        $InitialRepMethod = "Online" #options are "Online" or "Offline"

        $policyresult = New-AzureRmSiteRecoveryPolicy -Name $policyname -ReplicationProvider $RepProvider -ReplicationFrequencyInSeconds $Replicationfrequencyinseconds -RecoveryPoints $recoverypoints -ApplicationConsistentSnapshotFrequencyInHours $AppConsistentSnapshotFrequency -Authentication $AuthMode -ReplicationPort $AuthPort -ReplicationMethod $InitialRepMethod

    > [!NOTE]
    > A nuvem do VMM pode conter os anfitriões de Hyper-V com diferentes versões do Windows Server (tal como mencionado nos pré-requisitos do Hyper-V), mas a política de replicação é uma versão de SO específica. Se tiver de anfitriões diferentes em execução em versões diferentes do sistema operativo, em seguida, crie políticas de replicação separados para cada tipo de versão do SO. Por exemplo, se tiver cinco anfitriões, em execução nos servidores do Windows 2012 e três no Windows Server 2012 R2, crie duas políticas de replicação – um para cada tipo de versões do sistema operativo.

1. Obter o contentor de proteção principal (nuvem VMM principal) e o contentor de proteção de recuperação (nuvem VMM de recuperação) executando os seguintes comandos:

       $PrimaryCloud = "testprimarycloud"
       $primaryprotectionContainer = Get-AzureRmSiteRecoveryProtectionContainer -friendlyName $PrimaryCloud;  

       $RecoveryCloud = "testrecoverycloud"
       $recoveryprotectionContainer = Get-AzureRmSiteRecoveryProtectionContainer -friendlyName $RecoveryCloud;  
2. Obter a política que criou no passo 1 com o nome amigável da política

       $policy = Get-AzureRmSiteRecoveryPolicy -FriendlyName $policyname
3. Começar a associação do contentor de proteção (nuvem VMM) com a política de replicação:

       $associationJob  = Start-AzureRmSiteRecoveryPolicyAssociationJob -Policy     $Policy -PrimaryProtectionContainer $primaryprotectionContainer -RecoveryProtectionContainer $recoveryprotectionContainer
4. Aguarde a conclusão da tarefa de associação da política. Pode verificar se a tarefa foi concluída através do seguinte fragmento de PowerShell.

       $job = Get-AzureRmSiteRecoveryJob -Job $associationJob

       if($job -eq $null -or $job.StateDescription -ne "Completed")
       {
         $isJobLeftForProcessing = $true;
       }

   Depois de concluída a tarefa de processamento, execute o seguinte comando:

       if($isJobLeftForProcessing)
       {
         Start-Sleep -Seconds 60
       }
       }While($isJobLeftForProcessing)

Para verificar a conclusão da operação, siga os passos no [monitorizar a atividade](#monitor).

## <a name="step-6-configure-network-mapping"></a>Passo 6: Configurar o mapeamento da rede
1. O primeiro comando obtém os servidores do Cofre de recuperação de sites do Azure atual. O comando armazena os servidores do Microsoft Azure Site Recovery na variável de matriz $Servers.

        $Servers = Get-AzureRmSiteRecoveryServer
2. O seguinte comando obter a rede de recuperação de Site para o servidor do VMM de origem e o servidor do VMM de destino.

        $PrimaryNetworks = Get-AzureRmSiteRecoveryNetwork -Server $Servers[0]        

        $RecoveryNetworks = Get-AzureRmSiteRecoveryNetwork -Server $Servers[1]

    > [!NOTE]
    > O servidor do VMM de origem pode ser a primeira ou a segunda na matriz de servidores. Verifique os nomes dos servidores do VMM e obter adequadamente as redes


1. O cmdlet final cria um mapeamento entre a rede principal e a rede de recuperação. O cmdlet Especifica a rede principal como o primeiro elemento da $PrimaryNetworks e a rede de recuperação como o primeiro elemento da $RecoveryNetworks.

        New-AzureRmSiteRecoveryNetworkMapping -PrimaryNetwork $PrimaryNetworks[0] -RecoveryNetwork $RecoveryNetworks[0]

## <a name="step-7-configure-storage-mapping"></a>Passo 7: Configurar o mapeamento de armazenamento
1. O comando abaixo obtém a lista de classificações de armazenamento para $storageclassifications variável.

        $storageclassifications = Get-AzureRmSiteRecoveryStorageClassification
2. Obter os seguintes comandos a classificação de origem para $SourceClassificaion variável e a classificação de destino para $TargetClassification variável.

        $SourceClassificaion = $storageclassifications[0]

        $TargetClassification = $storageclassifications[1]

    > [!NOTE]
    > As classificações de origem e de destino podem ser qualquer elemento na matriz. Consulte a saída do comando para identificar o índice de classificações de origem e de destino na matriz $storageclassifications abaixo.

    > Get-AzureRmSiteRecoveryStorageClassification | Select-Object - FriendlyName de propriedade, o Id de | Formato de tabela


1. O cmdlet abaixo cria um mapeamento entre a classificação de origem e a classificação de destino.

        New-AzureRmSiteRecoveryStorageClassificationMapping -PrimaryStorageClassification $SourceClassificaion -RecoveryStorageClassification $TargetClassification

## <a name="step-8-enable-protection-for-virtual-machines"></a>Passo 8: Ativar a proteção para máquinas virtuais
Depois de servidores, nuvens e redes estão configuradas corretamente, pode ativar a proteção para máquinas virtuais na nuvem.

1. Para ativar a proteção, execute o seguinte comando para obter o contentor de proteção:

          $PrimaryProtectionContainer = Get-AzureRmSiteRecoveryProtectionContainer -friendlyName $PrimaryCloudName
2. Obter a entidade de proteção (VM) executando o seguinte comando:

           $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -friendlyName $VMName -ProtectionContainer $PrimaryProtectionContainer
3. Ative a replicação para a VM, executando o seguinte comando:

          $jobResult = Set-AzureRmSiteRecoveryProtectionEntity -ProtectionEntity $protectionentity -Protection Enable -Policy $policy

## <a name="test-your-deployment"></a>Testar a implementação
Para testar a implementação pode executar uma ativação pós-falha de teste para uma única máquina virtual, ou criar um plano de recuperação consiste em várias máquinas virtuais e executar uma ativação pós-falha de teste para o plano. A ativação pós-falha de teste simula o mecanismo de ativação pós-falha e recuperação numa rede isolada.

> [!NOTE]
> Pode criar um plano de recuperação para a sua aplicação no portal do Azure.
>
>

Para verificar a conclusão da operação, siga os passos no [monitorizar a atividade](#monitor).

### <a name="run-a-test-failover"></a>Executar uma ativação pós-falha de teste
1. Execute o abaixo cmdlets para obter as suas VMs para a rede VM à qual pretende testar a ativação pós-falha.

       $Servers = Get-AzureRmSiteRecoveryServer
       $RecoveryNetworks = Get-AzureRmSiteRecoveryNetwork -Server $Servers[1]
2. Execute uma ativação pós-falha de teste de uma VM efetuando o seguinte procedimento:

       $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -FriendlyName $VMName -ProtectionContainer $PrimaryprotectionContainer

       $jobIDResult =  Start-AzureRmSiteRecoveryTestFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity -VMNetwork $RecoveryNetworks[1]
3. Execute uma ativação pós-falha de teste de um plano de recuperação da seguinte forma:

       $recoveryplanname = "test-recovery-plan"

       $recoveryplan = Get-AzureRmSiteRecoveryRecoveryPlan -FriendlyName $recoveryplanname

       $jobIDResult =  Start-AzureRmSiteRecoveryTestFailoverJob -Direction PrimaryToRecovery -Recoveryplan $recoveryplan -VMNetwork $RecoveryNetworks[1]

### <a name="run-a-planned-failover"></a>Executar uma ativação pós-falha planeada
1. Execute uma ativação pós-falha de uma VM efetuando o seguinte procedimento:

        $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -Name $VMName -ProtectionContainer $PrimaryprotectionContainer

        $jobIDResult =  Start-AzureRmSiteRecoveryPlannedFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity
2. Execute uma ativação pós-falha de um plano de recuperação da seguinte forma:

        $recoveryplanname = "test-recovery-plan"

        $recoveryplan = Get-AzureRmSiteRecoveryRecoveryPlan -FriendlyName $recoveryplanname

        $jobIDResult =  Start-AzureRmSiteRecoveryPlannedFailoverJob -Direction PrimaryToRecovery -Recoveryplan $recoveryplan

### <a name="run-an-unplanned-failover"></a>Executar uma ativação pós-falha não planeada
1. Execute uma ativação pós-falha não planeada de uma VM efetuando o seguinte procedimento:

        $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -Name $VMName -ProtectionContainer $PrimaryprotectionContainer

        $jobIDResult =  Start-AzureRmSiteRecoveryUnPlannedFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity

2 Efetue uma ativação pós-falha não planeada de um plano de recuperação da seguinte forma:

        $recoveryplanname = "test-recovery-plan"

        $recoveryplan = Get-AzureRmSiteRecoveryRecoveryPlan -FriendlyName $recoveryplanname

        $jobIDResult =  Start-AzureRmSiteRecoveryUnPlannedFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity

## <a name=monitor></a>Monitorizar a atividade
Utilize os seguintes comandos para monitorizar a atividade. Tenha em atenção que poderá ter de aguardar entre tarefas de processamento concluir.

    Do
    {
        $job = Get-AzureSiteRecoveryJob -Id $associationJob.JobId;
        Write-Host "Job State:{0}, StateDescription:{1}" -f Job.State, $job.StateDescription;
        if($job -eq $null -or $job.StateDescription -ne "Completed")
        {
            $isJobLeftForProcessing = $true;
        }

    if($isJobLeftForProcessing)
        {
            Start-Sleep -Seconds 60
        }
    }While($isJobLeftForProcessing)



## <a name="next-steps"></a>Passos seguintes
[Leia mais](/powershell/module/azurerm.recoveryservices.backup/#recovery) sobre o Azure Site Recovery com cmdlets do PowerShell do Azure Resource Manager.

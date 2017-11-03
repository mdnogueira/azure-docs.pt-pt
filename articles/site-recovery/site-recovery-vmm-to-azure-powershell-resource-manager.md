---
title: "Replicar máquinas virtuais de Hyper-V em nuvens VMM utilizando o Azure Site Recovery e o PowerShell (Resource Manager) | Microsoft Docs"
description: "Replicar máquinas virtuais de Hyper-V em nuvens VMM utilizando o Azure Site Recovery e o PowerShell"
services: site-recovery
documentationcenter: 
author: Rajani-Janaki-Ram
manager: rochakm
editor: raynew
ms.assetid: 6ac509ad-5024-43d8-b621-d8fec019b9a9
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/05/2017
ms.author: rajanaki
ms.openlocfilehash: 34086044db752f09f1282517b59856091e85c2fc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="replicate-hyper-v-virtual-machines-in-vmm-clouds-to-azure-using-powershell-and-azure-resource-manager"></a>Replicar máquinas virtuais de Hyper-V em nuvens VMM para o Azure com o PowerShell e do Azure Resource Manager
> [!div class="op_single_selector"]
> * [Portal do Azure](site-recovery-vmm-to-azure.md)
> * [PowerShell – Resource Manager](site-recovery-vmm-to-azure-powershell-resource-manager.md)
> * [Portal Clássico](site-recovery-vmm-to-azure-classic.md)
> * [PowerShell – Clássica](site-recovery-deploy-with-powershell.md)
>
>

## <a name="overview"></a>Descrição geral
O Azure Site Recovery contribui para a sua estratégia de recuperação (BCDR) de continuidade e desastre negócio através da orquestração da replicação, ativação pós-falha e recuperação de máquinas virtuais num número de cenários de implementação. Para obter uma lista completa de implementação de cenários de ver o [descrição geral do Azure Site Recovery](site-recovery-overview.md).

Este artigo mostra como utilizar o PowerShell para automatizar tarefas comuns, que terá de efetuar quando configurou o Azure Site Recovery para replicar máquinas virtuais de Hyper-V em nuvens VMM do System Center para o armazenamento do Azure.

O artigo inclui os pré-requisitos para o cenário e mostra-lhe

* Como configurar um cofre dos serviços de recuperação
* Instalar o fornecedor do Azure Site Recovery no servidor do VMM de origem
* Registar o servidor no cofre, adicione uma conta de armazenamento do Azure
* Instalar o agente do Azure Recovery Services nos servidores de anfitrião do Hyper-V
* Configurar definições de proteção de nuvens do VMM, que serão aplicadas a todas as máquinas virtuais protegidas
* Ative a proteção para as máquinas virtuais.
* Teste a ativação pós-falha para se certificar de que tudo está a funcionar conforme esperado.

Caso se depare com problemas de configuração neste cenário, publique as suas perguntas sobre o [fórum de serviços de recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

> [!NOTE]
> O Azure tem dois modelos de implementação diferentes para criar e trabalhar com recursos: [Resource Manager e clássico](../azure-resource-manager/resource-manager-deployment-model.md). Este artigo abrange utilizando o modelo de implementação Resource Manager.
>
>

## <a name="before-you-start"></a>Antes de começar
Certifique-se de que tem os pré-requisitos:

### <a name="azure-prerequisites"></a>Pré-requisitos do Azure
* Precisará de uma conta do [Microsoft Azure](https://azure.microsoft.com/). Se não tiver uma, comece por um [conta gratuita](https://azure.microsoft.com/free). Além disso, pode ler sobre o [preços do Microsoft Azure Site Recovery Manager](https://azure.microsoft.com/pricing/details/site-recovery/).
* Irá precisar de uma subscrição do CSP se estiver a tentar terminar a replicação para um cenário de subscrição do CSP. Saiba mais sobre o programa CSP no [como inscrever no programa de CSP](https://msdn.microsoft.com/library/partnercenter/mt156995.aspx).
* Irá precisar de uma conta de armazenamento (Resource Manager) v2 do Azure para armazenar dados replicados para o Azure. A conta tem de georreplicação ativada. Deve estar na mesma região que o serviço do Azure Site Recovery e estar associado a mesma subscrição ou a subscrição do CSP. Para saber mais sobre como configurar o armazenamento do Azure, consulte o [introdução ao Storage do Microsoft Azure](../storage/common/storage-introduction.md) para referência.
* Tem de certificar-se de que estão em conformidade com as máquinas virtuais que pretende proteger o [pré-requisitos de máquina virtual do Azure](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements).

> [!NOTE]
> Atualmente, apenas operações ao nível de VM são possíveis através do Powershell. Suporte para operações de nível de plano de recuperação será feito em breve.  Por agora, está limitado a efetuar ativações pós-falha de datacenters apenas granularidade 'protected VM' e não um nível plano de recuperação.
>
>

### <a name="vmm-prerequisites"></a>Pré-requisitos do VMM
* Irá precisar de servidor do VMM em execução no System Center 2012 R2.
* Nenhum servidor VMM que contêm máquinas virtuais que pretende proteger tem de executar o fornecedor do Azure Site Recovery. Este é instalado durante a implementação do Azure Site Recovery.
* Irá precisar de pelo menos uma nuvem no servidor do VMM que pretende proteger. A nuvem deve conter:
  * Um ou mais grupos de anfitriões VMM.
  * Um ou mais servidores anfitrião Hyper-V ou clusters em cada grupo anfitrião.
  * Um ou mais máquinas virtuais no servidor de Hyper-V de origem.
* Saiba mais sobre como configurar as nuvens do VMM:
  * Saiba mais sobre nuvens privadas do VMM no [Novidades na nuvem privada com o System Center 2012 R2 VMM](http://go.microsoft.com/fwlink/?LinkId=324952) e na [VMM 2012 e nuvens](http://go.microsoft.com/fwlink/?LinkId=324956).
  * Saiba mais sobre [configurar o VMM recursos de infraestrutura de nuvem](https://msdn.microsoft.com/library/azure/dn469075.aspx#BKMK_Fabric)
  * Depois dos elementos de recursos de infraestrutura de nuvem estão assegurados saber sobre a criação de nuvens privadas em [criar uma nuvem privada no VMM](http://go.microsoft.com/fwlink/p/?LinkId=324953) e na [instruções: criar nuvens privadas com o VMM do System Center 2012 SP1](http://go.microsoft.com/fwlink/p/?LinkId=324954).

### <a name="hyper-v-prerequisites"></a>Pré-requisitos do Hyper-V
* Os servidores de anfitrião Hyper-V tem de estar em execução, pelo menos, **Windows Server 2012** com a função Hyper-V ou **Microsoft Hyper-V Server 2012** e instalou as atualizações mais recentes.
* Se estiver a executar Hyper-V num cluster, tenha em atenção que o mediador de clusters não é criado automaticamente se tiver um cluster com base no endereço IP estático. Precisará de configurar manualmente o mediador de clusters. Para
* Para obter instruções, consulte [como configurar o Mediador de réplicas do Hyper-V](http://blogs.technet.com/b/haroldwong/archive/2013/03/27/server-virtualization-series-hyper-v-replica-broker-explained-part-15-of-20-by-yung-chou.aspx).
* Qualquer servidor de anfitrião do Hyper-V ou cluster para o qual pretende gerir proteção têm de ser incluído numa nuvem VMM.

### <a name="network-mapping-prerequisites"></a>Pré-requisitos de mapeamento da rede
Quando protege máquinas virtuais no Azure, os mapas de mapeamento de rede redes de máquina virtual no servidor do VMM de origem e redes de destino do Azure para ativar o seguinte:

* Todas as máquinas que efetuar a ativação pós-falha na mesma rede podem ligar-se entre si, independentemente do plano de recuperação onde se encontram no.
* Se um gateway de rede está configurado na rede de destino do Azure, as máquinas virtuais podem ligar-se a outras máquinas virtuais no local.
* Se não configurar o mapeamento da rede, apenas as máquinas virtuais com ativação pós-falha no plano de recuperação será capazes de ligar-se entre si após a ativação pós-falha para o Azure.

Se pretender implementar o mapeamento da rede precisará do seguinte:

* As máquinas virtuais que pretende proteger no servidor VMM de origem devem estar ligadas a uma rede VM. Essa rede deve ser ligada a uma rede lógica que está associada à nuvem.
* Uma rede do Azure para o qual as máquinas virtuais replicadas podem ligar após a ativação pós-falha. Selecionará esta rede no momento da ativação pós-falha. A rede deve estar na mesma região que a sua subscrição do Azure Site Recovery.

Saiba mais sobre o mapeamento da rede no

* [Como configurar redes lógicas no VMM](http://go.microsoft.com/fwlink/p/?LinkId=386307)
* [Como configurar gateways e redes VM no VMM](http://go.microsoft.com/fwlink/p/?LinkId=386308)
* [Como configurar e monitorizar redes virtuais no Azure](https://azure.microsoft.com/documentation/services/virtual-network/)

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
1. Criar um grupo de recursos no Gestor de recursos do Azure se ainda não tiver um

        New-AzureRmResourceGroup -Name #ResourceGroupName -Location #location
2. Criar um novo cofre de serviços de recuperação e guarde o objeto de cofre ASR criado numa variável (serão utilizados mais tarde). Também pode obter a criação de objeto cofre do ASR post utilizando o cmdlet Get-AzureRMRecoveryServicesVault:-

        $vault = New-AzureRmRecoveryServicesVault -Name #vaultname -ResouceGroupName #ResourceGroupName -Location #location

## <a name="step-3-set-the-recovery-services-vault-context"></a>Passo 3: Definir o contexto do Cofre de serviços de recuperação

Definir o contexto do cofre, executando o comando abaixo.

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

## <a name="step-5-create-an-azure-storage-account"></a>Passo 5: Criar uma conta de armazenamento do Azure

Se não tiver uma conta de armazenamento do Azure, crie uma conta de georreplicação ativada na mesma geografia que o cofre, executando o seguinte comando:

        $StorageAccountName = "teststorageacc1"    #StorageAccountname
        $StorageAccountGeo  = "Southeast Asia"     
        $ResourceGroupName =  “myRG”             #ResourceGroupName
        $RecoveryStorageAccount = New-AzureRmStorageAccount -ResourceGroupName $ResourceGroupName -Name $StorageAccountName -Type “StandardGRS” -Location $StorageAccountGeo

Tenha em atenção que a conta de armazenamento tem de estar na mesma região que o serviço do Azure Site Recovery e estar associado a mesma subscrição.

## <a name="step-6-install-the-azure-recovery-services-agent"></a>Passo 6: Instalar o agente de serviços de recuperação do Azure
1. Transferir o agente dos serviços de recuperação do Azure em [http://aka.ms/latestmarsagent](http://aka.ms/latestmarsagent) e instalá-la em cada servidor de anfitrião de Hyper-V localizado em nuvens VMM que pretende proteger.
2. Execute o seguinte comando em todos os anfitriões VMM:

       marsagentinstaller.exe /q /nu

## <a name="step-7-configure-cloud-protection-settings"></a>Passo 7: Configurar a nuvem as definições de proteção
1. Crie uma política de replicação para o Azure executando o seguinte comando:

        $ReplicationFrequencyInSeconds = "300";        #options are 30,300,900
        $PolicyName = “replicapolicy”
        $Recoverypoints = 6                    #specify the number of recovery points

        $policryresult = New-AzureRmSiteRecoveryPolicy -Name $policyname -ReplicationProvider HyperVReplicaAzure -ReplicationFrequencyInSeconds $replicationfrequencyinseconds -RecoveryPoints $recoverypoints -ApplicationConsistentSnapshotFrequencyInHours 1 -RecoveryAzureStorageAccountId "/subscriptions/q1345667/resourceGroups/test/providers/Microsoft.Storage/storageAccounts/teststorageacc1"

1. Obter um contentor de proteção, executando os seguintes comandos:

       $PrimaryCloud = "testcloud"
       $protectionContainer = Get-AzureRmSiteRecoveryProtectionContainer -friendlyName $PrimaryCloud;  
2. Obterem os detalhes da política para uma variável utilizando a tarefa que foi criada e mentioning o nome amigável de política:

       $policy = Get-AzureRmSiteRecoveryPolicy -FriendlyName $policyname
3. Começar a associação do contentor de proteção com a política de replicação:

       $associationJob  = Start-AzureRmSiteRecoveryPolicyAssociationJob -Policy     $Policy -PrimaryProtectionContainer $protectionContainer  
4. Depois da tarefa estiver concluída, execute o seguinte comando:

       $job = Get-AzureRmSiteRecoveryJob -Job $associationJob

       if($job -eq $null -or $job.StateDescription -ne "Completed")
       {
         $isJobLeftForProcessing = $true;
       }
5. Depois de concluída a tarefa de processamento, execute o seguinte comando:

       if($isJobLeftForProcessing)
       {
         Start-Sleep -Seconds 60
       }
       }While($isJobLeftForProcessing)

Para verificar a conclusão da operação, siga os passos no [monitorizar a atividade](#monitor).

## <a name="step-8-configure-network-mapping"></a>Passo 8: Configurar o mapeamento da rede
Antes de iniciar o mapeamento da rede, certifique-se que as máquinas virtuais no servidor VMM de origem estão ligadas a uma rede VM. Além disso, crie um ou mais redes virtuais do Azure.

Saiba mais sobre como criar uma rede virtual com o Azure Resource Manager e o PowerShell, no [criar uma rede virtual com uma ligação de VPN de site para site com o Azure Resource Manager e o PowerShell](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)

Tenha em atenção que várias redes de máquinas virtuais podem ser mapeadas para uma única rede Azure. Se a rede de destino tem várias sub-redes e um dessas sub-redes tem o mesmo nome de sub-rede que está localizada a máquina virtual de origem, em seguida, a máquina virtual de réplica será ligada a essa sub-rede de destino após a ativação pós-falha. Se não existir nenhuma sub-rede de destino com um nome correspondente, a máquina virtual será ligada à primeira sub-rede da rede.

1. O primeiro comando obtém os servidores do Cofre de recuperação de sites do Azure atual. O comando armazena os servidores do Microsoft Azure Site Recovery na variável de matriz $Servers.

        $Servers = Get-AzureRmSiteRecoveryServer
2. O segundo comando obtém a rede de recuperação de site para o primeiro servidor na matriz $Servers. O comando armazena as redes na variável $Networks.

        $Networks = Get-AzureRmSiteRecoveryNetwork -Server $Servers[0]

1. O terceiro comando obtém redes virtuais do Azure e, em seguida, esse valor na variável $AzureVmNetworks.

        $AzureVmNetworks =  Get-AzureRmVirtualNetwork
2. O cmdlet final cria um mapeamento entre a rede principal e a rede de máquina virtual do Azure. O cmdlet Especifica a rede principal como o primeiro elemento da $Networks. O cmdlet Especifica uma rede de máquina virtual, como o primeiro elemento da $AzureVmNetworks.

        New-AzureRmSiteRecoveryNetworkMapping -PrimaryNetwork $Networks[0] -AzureVMNetworkId $AzureVmNetworks[0]

## <a name="step-9-enable-protection-for-virtual-machines"></a>Passo 9: Ativar a proteção para máquinas virtuais
Depois de servidores, nuvens e redes estão configuradas corretamente, pode ativar a proteção para máquinas virtuais na nuvem.

 Tenha em atenção o seguinte:

* Máquinas virtuais têm de cumprir os requisitos do Azure. Verifique estes na [pré-requisitos e suporte](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements) no guia de planeamento.
* Para ativar a proteção, o sistema operativo e o sistema operativo tem de definir as propriedades de disco para a máquina virtual. Quando cria uma máquina virtual no VMM utilizando um modelo de máquina virtual, pode definir a propriedade. Também pode definir estas propriedades para as máquinas virtuais existentes nos separadores **Geral** e **Configuração de Hardware** das propriedades da máquina virtual. Se não definir estas propriedades no VMM, poderá configurá-las no portal do Azure Site Recovery.

1. Para ativar a proteção, execute o seguinte comando para obter o contentor de proteção:

          $ProtectionContainer = Get-AzureRmSiteRecoveryProtectionContainer -friendlyName $CloudName
2. Obter a entidade de proteção (VM) executando o seguinte comando:

           $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -friendlyName $VMName -ProtectionContainer $protectionContainer
3. Ative a DR para a VM, executando o seguinte comando:

          $jobResult = Set-AzureRmSiteRecoveryProtectionEntity -ProtectionEntity $protectionentity -Protection Enable –Force -Policy $policy -RecoveryAzureStorageAccountId  $storageID "/subscriptions/217653172865hcvkchgvd/resourceGroups/rajanirgps/providers/Microsoft.Storage/storageAccounts/teststorageacc1

## <a name="test-your-deployment"></a>Testar a implementação
Para testar a implementação pode executar uma teste de ativação pós-falha para uma única máquina virtual, ou criar um plano de recuperação consiste em várias máquinas virtuais e executar uma teste de ativação pós-falha para o plano. Pós-falha de teste simula o mecanismo de ativação pós-falha e recuperação numa rede isolada. Tenha em atenção que:

* Se pretender ligar à máquina virtual no Azure utilizando o ambiente de trabalho remoto após a ativação pós-falha, ative a ligação de ambiente de trabalho remoto na máquina virtual antes de executar a ativação pós-falha de teste.
* Após a ativação pós-falha, utilizará um endereço IP público para ligar à máquina virtual no Azure utilizando o ambiente de trabalho remoto. Se pretender efetuar este procedimento, certifique-se de que não tem quaisquer políticas de domínio a impedir a ligação a uma máquina virtual utilizando um endereço público.

Para verificar a conclusão da operação, siga os passos no [monitorizar a atividade](#monitor).

### <a name="run-a-test-failover"></a>Executar uma ativação pós-falha de teste
- Inicie ativação pós-falha de teste, executando o seguinte comando:

       $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -Name $VMName -ProtectionContainer $protectionContainer

       $jobIDResult =  Start-AzureRmSiteRecoveryTestFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity -AzureVMNetworkId <string>  

### <a name="run-a-planned-failover"></a>Executar uma ativação pós-falha planeada
- Inicie a ativação pós-falha planeada, executando o seguinte comando:

        $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -Name $VMName -ProtectionContainer $protectionContainer

        $jobIDResult =  Start-AzureRmSiteRecoveryPlannedFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity -AzureVMNetworkId <string>  

### <a name="run-an-unplanned-failover"></a>Executar uma ativação pós-falha não planeada
- Inicie a ativação pós-falha não planeada, executando o seguinte comando:

        $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -Name $VMName -ProtectionContainer $protectionContainer

        $jobIDResult =  Start-AzureRmSiteRecoveryUnPlannedFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity -AzureVMNetworkId <string>  

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

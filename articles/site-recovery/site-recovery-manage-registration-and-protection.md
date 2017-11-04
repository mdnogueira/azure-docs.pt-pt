---
title: "Remova os servidores e desative a proteção | Microsoft Docs"
description: "Este artigo descreve como anular o registo de servidores a partir de um cofre de recuperação de Site e para desativar a proteção para máquinas virtuais e servidores físicos."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: cfreeman
editor: 
ms.assetid: ef1f31d5-285b-4a0f-89b5-0123cd422d80
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 10/3/2017
ms.author: raynew
ms.openlocfilehash: 471d68742668e2b1b1c72579cee9dd493f1bd042
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="remove-servers-and-disable-protection"></a>Remover servidores e desativar proteção
Este artigo descreve como anular o registo de servidores a partir de um cofre dos serviços de recuperação e como desativar a proteção para as máquinas protegidas pela recuperação de sites.


## <a name="unregister-a--configuration-server"></a>Anular o registo de um servidor de configuração

Se replicar VMs de VMware ou servidores físicos Windows/Linux para o Azure, pode anular registo um servidor de configuração desligado de um cofre da seguinte forma:

1. [Desative a proteção de máquinas virtuais](#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure).
2. [Desassocie](site-recovery-setup-replication-settings-vmware.md#dissociate-a-configuration-server-from-a-replication-policy) e [eliminar](site-recovery-setup-replication-settings-vmware.md#delete-a-replication-policy) todas as políticas de replicação
3. [Eliminar o servidor de configuração](site-recovery-vmware-to-azure-manage-configuration-server.md#delete-or-unregister-a-configuration-server)

## <a name="unregister-a-vmm-server"></a>Anular o registo de um servidor do VMM

1. Pare máquinas virtuais em nuvens no servidor do VMM que pretende remover a replicar.
2. Elimine quaisquer mapeamentos de rede utilizados pelo nuvens no servidor VMM que pretende eliminar. No **infraestrutura de recuperação de Site** > **para o System Center VMM** > **mapeamento da rede**, faça duplo clique o mapeamento da rede > **Eliminar**.
3. Tenha em atenção o ID do servidor VMM.
4. Desassocie políticas de replicação das nuvens no servidor do VMM que pretende remover.  No **infraestrutura de recuperação de Site** > **para o System Center VMM** >  **políticas de replicação**, faça duplo clique a política associada. Clique com o botão direito nuvem > **Disassociate**.
5. Elimine o servidor do VMM ou o nó ativo. No **infraestrutura de recuperação de Site** > **para o System Center VMM** > **servidores VMM**, clique com o botão direito do servidor > **eliminar** .
6. Se o servidor do VMM estiver num estado desligado, em seguida, transferir e executar o [script de limpeza](http://aka.ms/asr-cleanup-script-vmm) no servidor do VMM. Abra o PowerShell com o **executar como administrador** opção, para alterar a política de execução para o âmbito de predefinição (LocalMachine). O script, especifique o ID do servidor VMM que pretende remover. O script remove informações do servidor de emparelhamento da nuvem e de registo.
5. Execute o script de limpeza em qualquer servidor secundário do VMM.
6. Execute o script de limpeza em quaisquer outros VMM cluster nós passivos com o fornecedor instalado.
7. Desinstale o fornecedor manualmente no servidor do VMM. Se tiver um cluster, remova todos os nós.
8. Se as máquinas virtuais foram replicar para o Azure, terá de desinstalar o agente dos serviços de recuperação do Microsoft de anfitriões de Hyper-V em nuvens eliminados.

## <a name="unregister-a-hyper-v-host-in-a-hyper-v-site"></a>Anular o registo de um anfitrião de Hyper-V num Site Hyper-V

Anfitriões Hyper-V que não são geridas pelo VMM foram recolhidas para um site de Hyper-V. Remova um anfitrião num site Hyper-V da seguinte forma:

1. Desative a replicação para as VMs de Hyper-V localizada no anfitrião.
2. Desassocie políticas para o site Hyper-V. No **infraestrutura de recuperação de Site** > **para Sites de Hyper-V** >  **políticas de replicação**, faça duplo clique a política associada. Clique com o botão direito do site > **Disassociate**.
3. Elimine anfitriões Hyper-V. No **infraestrutura de recuperação de Site** > **para Sites de Hyper-V** > **anfitriões Hyper-V**, clique com o botão direito do servidor > **eliminar** .
4. Elimine o site Hyper-V, depois de todos os anfitriões foram removidos do mesmo. No **infraestrutura de recuperação de Site** > **para Sites de Hyper-V** > **Sites Hyper-V**, clique com o botão direito do site > **eliminar** .
5. Se o anfitrião Hyper-V estava a ser um **desligado** de estado, em seguida, execute o seguinte script em cada anfitrião de Hyper-V que é removido. O script limpa as definições no servidor e anula o registo do cofre.



        pushd .
        try
        {
             $windowsIdentity=[System.Security.Principal.WindowsIdentity]::GetCurrent()
             $principal=new-object System.Security.Principal.WindowsPrincipal($windowsIdentity)
             $administrators=[System.Security.Principal.WindowsBuiltInRole]::Administrator
             $isAdmin=$principal.IsInRole($administrators)
             if (!$isAdmin)
             {
                "Please run the script as an administrator in elevated mode."
                $choice = Read-Host
                return;       
             }

            $error.Clear()    
            "This script will remove the old Azure Site Recovery Provider related properties. Do you want to continue (Y/N) ?"
            $choice =  Read-Host

            if (!($choice -eq 'Y' -or $choice -eq 'y'))
            {
            "Stopping cleanup."
            return;
            }

            $serviceName = "dra"
            $service = Get-Service -Name $serviceName
            if ($service.Status -eq "Running")
            {
                "Stopping the Azure Site Recovery service..."
                net stop $serviceName
            }

            $asrHivePath = "HKLM:\SOFTWARE\Microsoft\Azure Site Recovery"
            $registrationPath = $asrHivePath + '\Registration'
            $proxySettingsPath = $asrHivePath + '\ProxySettings'
            $draIdvalue = 'DraID'

            if (Test-Path $asrHivePath)
            {
                if (Test-Path $registrationPath)
                {
                    "Removing registration related registry keys."    
                    Remove-Item -Recurse -Path $registrationPath
                }

                if (Test-Path $proxySettingsPath)
            {
                    "Removing proxy settings"
                    Remove-Item -Recurse -Path $proxySettingsPath
                }

                $regNode = Get-ItemProperty -Path $asrHivePath
                if($regNode.DraID -ne $null)
                {            
                    "Removing DraId"
                    Remove-ItemProperty -Path $asrHivePath -Name $draIdValue
                }
                "Registry keys removed."
            }

            # First retrive all the certificates to be deleted
            $ASRcerts = Get-ChildItem -Path cert:\localmachine\my | where-object {$_.friendlyname.startswith('ASR_SRSAUTH_CERT_KEY_CONTAINER') -or $_.friendlyname.startswith('ASR_HYPER_V_HOST_CERT_KEY_CONTAINER')}
            # Open a cert store object
            $store = New-Object System.Security.Cryptography.X509Certificates.X509Store("My","LocalMachine")
            $store.Open('ReadWrite')
            # Delete the certs
            "Removing all related certificates"
            foreach ($cert in $ASRcerts)
            {
                $store.Remove($cert)
            }
        }catch
        {    
            [system.exception]
            Write-Host "Error occured" -ForegroundColor "Red"
            $error[0]
            Write-Host "FAILED" -ForegroundColor "Red"
        }
        popd



## <a name="disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure"></a>Desative a proteção para uma VM de VMware ou o servidor físico (VMware para o Azure)

1. No **itens protegidos** > **itens replicados**, clique com o botão direito a máquina > **desative a replicação**.
2. No **desative a replicação** página, selecione uma destas opções:
    - **Desative a replicação e remover (recomendado)** - esta opção Remover o item replicado do Azure Site Recovery e a replicação para a máquina está parada. Configuração de replicação no servidor de configuração está a ser limpo e faturação da recuperação de sites para este servidor protegido é interrompida.
    - **Remover** -esta opção deve ser utilizada apenas se o ambiente de origem é eliminada ou não está acessível (não ligado). Esta ação remove o item replicado do Azure Site Recovery (faturação está parada). Configuração de replicação no servidor de configuração **não** ser limpa. 

> [!NOTE]
> Nas ambas as opções do serviço de mobilidade não será desinstalado dos servidores protegidos, terá de desinstalá-lo manualmente. Se planeia proteger o servidor novamente utilizando o mesmo servidor de configuração, pode ignorar a desinstalar o serviço de mobilidade.

## <a name="disable-protection-for-a-hyper-v-virtual-machine-hyper-v-to-azure"></a>Desative a proteção para uma máquina virtual de Hyper-V (Hyper-V para o Azure)

> [!NOTE]
> Utilize este procedimento se está a replicar VMs Hyper-V para o Azure sem um servidor do VMM. Se estiver a replicar as máquinas virtuais com o **System Center VMM para o Azure** cenário, em seguida, siga as instruções [desative a proteção para uma máquina virtual de Hyper-V a replicar com o System Center VMM para Cenário do Azure](#disable-protection-for-a-hyper-v-virtual-machine-replicating-using-the-system-centet-vmm-to-azure-scenario)

1. No **itens protegidos** > **itens replicados**, clique com o botão direito a máquina > **desative a replicação**.
2. No **desative a replicação**, pode selecionar as seguintes opções:
     - **Desative a replicação e remover (recomendado)** - esta opção Remover o item replicado do Azure Site Recovery e a replicação para a máquina está parada. Configuração de replicação na máquina virtual no local serão limpos e faturação da recuperação de sites para este servidor protegido é interrompida.
    - **Remover** -esta opção deve ser utilizada apenas se o ambiente de origem é eliminada ou não está acessível (não ligado). Esta ação remove o item replicado do Azure Site Recovery (faturação está parada). Configuração de replicação na máquina virtual no local **não** ser limpa. 

    > [!NOTE]
    > Se tiver escolhido o **remover** opção, em seguida, execute o seguinte conjunto de scripts para limpar as definições de replicação Hyper-V Server local.
1. No servidor de origem Hyper-V anfitrião, para remover a replicação da máquina virtual. Substituir SQLVM1 com o nome da sua máquina virtual e executar o script do PowerShell administrativo


    
    $vmName = "SQLVM1" $vm = Get-WmiObject - espaço de nomes "root\virtualization\v2"-consulta "selecionar * de Msvm_ComputerSystem onde ElementName = '$vmName'" $replicationService = Get-WmiObject - espaço de nomes "root\virtualization\v2"-"selecionar * de Msvm_ de consulta $ReplicationService.RemoveReplicationRelationship($vm.__PATH) ReplicationService"
    

## <a name="disable-protection-for-a-hyper-v-virtual-machine-replicating-to-azure-using-the-system-center-vmm-to-azure-scenario"></a>Desative a proteção para uma máquina virtual de Hyper-V replicar para o Azure utilizando o System Center VMM para o cenário do Azure

1. No **itens protegidos** > **itens replicados**, clique com o botão direito a máquina > **desative a replicação**.
2. No **desative a replicação**, selecione uma destas opções:

    - **Desative a replicação e remover (recomendado)** - esta opção Remover o item replicado do Azure Site Recovery e a replicação para a máquina está parada. Configuração de replicação na máquina virtual no local é limpa e faturação da recuperação de sites para este servidor protegido é interrompida.
    - **Remover** -esta opção deve ser utilizada apenas se o ambiente de origem é eliminada ou não está acessível (não ligado). Esta ação remove o item replicado do Azure Site Recovery (faturação está parada). Configuração de replicação na máquina virtual no local **não** ser limpa. 

    > [!NOTE]
    > Se tiver escolhido o **remover** opção, em seguida, tun de scripts é o seguinte para limpar as definições de replicação no VMM Server local.
3. Execute este script no servidor do VMM de origem, utilizando o PowerShell (necessários privilégios de administrador) a partir da consola do VMM. Substitua o marcador de posição **SQLVM1** com o nome da sua máquina virtual.

        $vm = get-scvirtualmachine -Name "SQLVM1"
        Set-SCVirtualMachine -VM $vm -ClearDRProtection
4. Os passos acima limpar as definições de replicação no servidor do VMM. Para parar a replicação da máquina virtual em execução no servidor de anfitrião do Hyper-V, execute este script. Substitua SQLVM1 com o nome da sua máquina virtual e host01.contoso.com com o nome do servidor de anfitrião Hyper-V.

    
    $vmName = "SQLVM1" $hostName = "host01.contoso.com" $vm = Get-WmiObject - espaço de nomes "root\virtualization\v2"-consulta "selecionar * de Msvm_ComputerSystem onde ElementName = '$vmName'" - computername $hostName $replicationService = Get-WmiObject - Espaço de nomes "root\virtualization\v2"-"Selecionar * de Msvm_ReplicationService" - computername $hostName $replicationService.RemoveReplicationRelationship($vm.__PATH) de consulta
    
       
 
## <a name="disable-protection-for-a-hyper-v-virtual-machine-replicating-to-secondary-vmm-server-using-the-system-center-vmm-to-vmm-scenario"></a>Desative a proteção para uma máquina virtual de Hyper-V replicar para o servidor secundário do VMM, utilizando o System Center VMM para o cenário VMM

1. No **itens protegidos** > **itens replicados**, clique com o botão direito a máquina > **desative a replicação**.
2. No **desative a replicação**, selecione uma destas opções:

    - **Desative a replicação e remover (recomendado)** - esta opção Remover o item replicado do Azure Site Recovery e a replicação para a máquina está parada. Configuração de replicação na máquina virtual no local é limpa e faturação da recuperação de sites para este servidor protegido é interrompida.
    - **Remover** -esta opção deve ser utilizada apenas se o ambiente de origem é eliminada ou não está acessível (não ligado). Esta ação remove o item replicado do Azure Site Recovery (faturação está parada). Configuração de replicação na máquina virtual no local **não** ser limpa. Execute o seguinte conjunto de scripts para limpar as máquinas de virtuais de local de definições de replicação.
> [!NOTE]
> Se tiver escolhido o **remover** opção, em seguida, tun de scripts é o seguinte para limpar as definições de replicação no VMM Server local.

3. Execute este script no servidor do VMM de origem, utilizando o PowerShell (necessários privilégios de administrador) a partir da consola do VMM. Substitua o marcador de posição **SQLVM1** com o nome da sua máquina virtual.

         $vm = get-scvirtualmachine -Name "SQLVM1"
         Set-SCVirtualMachine -VM $vm -ClearDRProtection
4. No servidor secundário do VMM, execute este script para limpar as definições para a máquina virtual secundária:

        $vm = get-scvirtualmachine -Name "SQLVM1"
        Remove-SCVirtualMachine -VM $vm -Force
5. No servidor secundário do VMM, Atualize as máquinas virtuais no servidor de anfitrião do Hyper-V, para que a VM secundária obtém detetada novamente, na consola do VMM.
6. Os passos acima limpar as definições de replicação no servidor do VMM. Se pretender parar a replicação da máquina virtual, execute o seguinte script esqueceu as VMs primários e secundários. Substitua SQLVM1 com o nome da sua máquina virtual.

        Remove-VMReplication –VMName “SQLVM1”





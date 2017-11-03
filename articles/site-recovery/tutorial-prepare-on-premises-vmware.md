---
title: "Preparar servidores do VMware no local para a recuperação após desastre de VMs de VMware para Azure | Microsoft Docs"
description: "Saiba como preparar servidores do VMware no local para a recuperação após desastre para o Azure utilizando o serviço do Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 90a4582c-6436-4a54-a8f8-1fee806b8af7
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/01/2017
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 33ec5775a371a04074f07d589d35d1c05bd64d30
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/01/2017
---
# <a name="prepare-on-premises-vmware-servers-for-disaster-recovery-to-azure"></a>Preparar servidores do VMware no local para a recuperação após desastre para o Azure

Este tutorial mostra como preparar a infraestrutura do VMware no local quando pretende replicar VMware VMs para Azure. Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Preparar uma conta no vCenter server ou vSphere ESXi anfitrião, ao automatizar a deteção VM
> * Preparar uma conta de instalação automática do serviço de mobilidade em VMs de VMware
> * Analisar os requisitos de servidor do VMware
> * Reveja os requisitos de VM de VMware

Este tutorial série, mostramos como fazer cópias de segurança uma única VM utilizando o Azure Site Recovery. Se estiver a planear proteger várias VMs de VMware, deve transferir o [ferramenta de implementação Planner](https://aka.ms/asr-deployment-planner) para replicação de VMware. Esta ferramenta recolher informações sobre a compatibilidade VM, discos por VM e fluxo de dados por disco. A ferramenta também abrange os requisitos de largura de banda de rede e a infraestrutura do Azure necessários para ativação pós-falha com êxito de replicação e teste. [Saiba mais](site-recovery-deployment-planner.md) sobre como executar a ferramenta.

Este é o segundo tutorial da série. Certifique-se de que tem [configurar os componentes do Azure](tutorial-prepare-azure.md) conforme descrito no tutorial anterior.

## <a name="prepare-an-account-for-automatic-discovery"></a>Preparar uma conta de deteção automática

Recuperação de sites tem acesso aos servidores do VMware para:

- Deteta automaticamente as VMs. É necessária, pelo menos, uma conta de só de leitura.
- Orquestrar a replicação, ativação pós-falha e a reativação pós-falha. Precisa de uma conta que pode executar operações, tais como criar e remoção de discos e ligar em VMs.

Crie a conta da seguinte forma:

1. Para utilizar uma conta dedicada, crie uma função ao nível do vCenter. Dê um nome de função, tal como **Azure_Site_Recovery**.
2. Atribua a função permissões resumidas na tabela abaixo.
3. Crie um utilizador no anfitrião do servidor ou vSphere do vCenter. Atribua a função ao utilizador.

### <a name="vmware-account-permissions"></a>Permissões de conta de VMware

**Tarefa** | **Permissões de função /** | **Detalhes**
--- | --- | ---
**Deteção VM** | Pelo menos um utilizador só de leitura<br/><br/> Objeto de centro de dados –> Propagate ao objeto do subordinado função = só de leitura | Utilizador atribuído ao nível do datacenter e tem acesso a todos os objetos no Centro de dados.<br/><br/> Para restringir o acesso, atribua o **sem acesso** função com o **Propagate para subordinado** objeto, para os objetos subordinados (anfitriões vSphere, datastores, VMs e as redes).
**Total de replicação, a ativação pós-falha, a reativação pós-falha** |  Criar uma função (Azure_Site_Recovery) com as permissões necessárias e, em seguida, atribua a função a um grupo ou utilizador de VMware<br/><br/> Objeto de centro de dados –> Propagate ao objeto do subordinado função = Azure_Site_Recovery<br/><br/> Arquivo de dados -> atribuir espaço em, procurar o arquivo de dados, as operações de baixo nível de ficheiro, remova o ficheiro, atualizar ficheiros de máquina virtual<br/><br/> Rede -> atribuição de rede<br/><br/> Recursos -> VM atribuir ao agrupamento de recursos, migrar alimentado desligar a VM, migrar alimentado na VM<br/><br/> Tarefas -> tarefas de criação, a tarefa de atualização<br/><br/> Configuração -> de máquina virtual<br/><br/> Máquina virtual -> interagir -> pergunta de resposta, a ligação de dispositivos, configurar suporte de dados do CD, configurar o suporte de dados de disquetes, desligar, ligar, instalação de ferramentas do VMware<br/><br/> Máquina virtual -> inventário -> criar, registar, anular o registo<br/><br/> Máquina virtual -> aprovisionamento -> Permitir transferências de máquina virtual, permitem carregar ficheiros de máquina virtual<br/><br/> Máquina virtual -> instantâneos -> Remover instantâneos | Utilizador atribuído ao nível do datacenter e tem acesso a todos os objetos no Centro de dados.<br/><br/> Para restringir o acesso, atribua o **sem acesso** função com o **Propagate para subordinado** objeto, para os objetos subordinados (anfitriões vSphere, datastores, VMs e as redes).

## <a name="prepare-an-account-for-mobility-service-installation"></a>Preparar uma conta de instalação do serviço de mobilidade

O serviço de mobilidade tem de estar instalado na VM que pretende replicar. Recuperação de site instala automaticamente este serviço ao ativar a replicação para a VM. Para a instalação automática, terá de preparar uma conta que a recuperação de sites irá utilizar para aceder a VM. Terá de especificar esta conta ao configurar a recuperação após desastre na consola do Azure.

1. Prepare um domínio ou conta local com permissões para instalar na VM.
2. Para instalar em VMs do Windows, se não estiver a utilizar uma conta de domínio, desative o controlo de acesso de utilizador remoto no computador local.
   - A partir do registo, sob **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System**, adicione a entrada DWORD **LocalAccountTokenFilterPolicy**, com um valor de 1.
3. Para instalar em VMs do Linux, prepare uma conta de raiz no servidor de Linux de origem.


## <a name="check-vmware-server-requirements"></a>Verificar os requisitos de servidor do VMware

Certifique-se a servidores VMware cumpram os seguintes requisitos.

**Componente** | **Requisito**
--- | ---
**servidor vCenter** | vCenter 6.5, 6.0 ou 5.5
**anfitriões vSphere** | vSphere 6.5, 6.0, 5.5

## <a name="check-vmware-vm-requirements"></a>Verificar os requisitos de VM de VMware

Certifique-se de que a VM está em conformidade com os requisitos do Azure resumidos na tabela seguinte.

**Requisito de VM** | **Detalhes**
--- | ---
**Tamanho do disco do sistema operativo** | 2048 GB.
**Contagem de discos do sistema operativo** | 1
**Contagem de discos de dados** | 64 ou menos
**Tamanho VHD do disco de dados** | 4095 GB
**Adaptadores de rede** | São suportados vários adaptadores
**VHD partilhado** | Não suportado
**Disco FC** | Não suportado
**Formato de disco rígido** | VHD ou VHDX.<br/><br/> Embora VHDX não é atualmente suportado no Azure, a recuperação de sites converte automaticamente VHDX VHD, quando efetuar a ativação pós-falha para o Azure. Quando falhar novamente para VMs no local continue a utilizar o formato VHDX.
**BitLocker** | Não é suportado. Desative antes de ativar a replicação para uma VM.
**Nome da VM** | Entre 1 e 63 carateres.<br/><br/> Restritas a letras, números e hífenes. O nome da VM tem de começar e terminar com uma letra ou número.
**Tipo VM** | Geração 1 - Linux ou do Windows<br/><br/>Geração 2 - apenas o Windows

A VM tem também de executar um sistema operativo suportado. Consulte o [matriz de suporte de recuperação de Site](site-recovery-support-matrix-to-azure.md#support-for-replicated-machine-os-versions) para uma lista completa das versões suportadas.

## <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Preparar a ligação para VMs do Azure após a ativação pós-falha

Durante um cenário de ativação pós-falha poderá pretender ligar ao seu replicadas VMs no Azure da sua rede no local.

Para ligar a VMs do Windows com RDP após a ativação pós-falha, efetue o seguinte:

1. Para aceder através da internet, ative o RDP na VM no local antes da ativação pós-falha. Certifique-se de que TCP e regras UDP estão adicionadas para o **pública** perfil e que o RDP é permitido na **Firewall do Windows** > **aplicações permitidas** para todos os perfis.
2. Para aceder através de VPN de site a site, ative o RDP na máquina no local. RDP deve ser permitido no **Firewall do Windows** -> **aplicações e funcionalidades permitidas** para **domínio e privada** redes.
   Verifique se a política de SAN do sistema operativo estiver definida como **OnlineAll**. [Saiba mais](https://support.microsoft.com/kb/3031135). Não deverá haver nenhum atualizações pendentes do Windows na VM quando acione uma ativação pós-falha. Se existirem, não poderá iniciar sessão para a máquina virtual enquanto a atualização for concluída.
3. No Windows VM do Azure após a ativação pós-falha, verifique **diagnóstico de arranque** para ver uma captura de ecrã da VM. Se não conseguir ligar, verifique se a VM está em execução e reveja estes [sugestões de resolução de problemas](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).

Para ligar a VMs com Linux através do SSH após a ativação pós-falha, efetue o seguinte:

1. Na máquina no local antes da ativação pós-falha, verifique se o serviço de Secure Shell está definido para iniciar automaticamente no arranque do sistema. Certifique-se de que as regras de firewall permitem uma ligação SSH.

2. Na VM do Azure após a ativação pós-falha, permita ligações de entrada na porta SSH para as regras de grupo de segurança de rede na ativação pós-falha VM e para a sub-rede do Azure à qual está ligado.
   [Adicionar um endereço IP público](site-recovery-monitoring-and-troubleshooting.md#adding-a-public-ip-on-a-resource-manager-virtual-machine) para a VM. Pode verificar **diagnóstico de arranque** para ver uma captura de ecrã da VM.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Configure a recuperação de desastre para o Azure para VMware VMs](tutorial-vmware-to-azure.md)

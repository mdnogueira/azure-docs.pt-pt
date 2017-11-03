---
title: " Gerir um servidor VMware vCenter no Azure Site Recovery | Microsoft Docs"
description: Este artigo descreve como adicionar e gerir o VMware vCenter no Azure Site Recovery.
services: site-recovery
documentationcenter: 
author: AnoopVasudavan
manager: gauravd
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: backup-recovery
ms.date: 06/29/2017
ms.author: anoopkv
ms.openlocfilehash: 091f0884417535427c52beee7bcdc5ed1dd83315
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="manage-vmware-vcenter-server-in-azure-site-recovery"></a>Gerir o VMware vCenter Server no Azure Site Recovery
Este artigo aborda as várias operações de recuperação de sites que podem ser executadas no VMware vCenter.

## <a name="prerequisites"></a>Pré-requisitos

**Suporta o VMware vCenter e VMware vSphere anfitrião do ESX** | **Detalhes** |
|--- | --- |
|**Servidores do VMware no local** | Um ou mais VMware vSphere servidores, com 6.0, 5.5, 5.1 com as atualizações mais recentes. Servidores devem estar localizados na mesma rede que o servidor de configuração (ou o servidor de processo separado).<br/><br/> Recomendamos que um servidor vCenter para gerir anfitriões, com 6.0 ou 5.5 com as atualizações mais recentes. Apenas as funcionalidades que estão disponíveis no 5.5 são suportadas quando implementar a versão 6.0.|

## <a name="prepare-an-account-for-automatic-discovery"></a>Preparar uma conta de deteção automática
Recuperação de sites precisa de aceder ao VMware para o servidor de processos detetar automaticamente máquinas virtuais e para ativação pós-falha e a reativação pós-falha de máquinas virtuais.

* **Migrar**: se apenas pretender migrar máquinas virtuais VMware para o Azure, sem nunca voltar a falhá-los, pode utilizar uma conta do VMware com uma função só de leitura. Este tipo uma função pode executar a ativação pós-falha, mas não é possível encerrar máquinas de origem protegida. Não é necessário para a migração.
* **Replicar/recuperar**: Se pretender implementar uma replicação completa (replicar ativação pós-falha, a reativação pós-falha) a conta deve ser capaz de executar operações, tais como criar e remoção de discos, a ligar a máquina virtual.
* **A deteção automática**: é necessária, pelo menos, uma conta de só de leitura.


|**Tarefas** | **Conta/função necessários** | **Permissões** | **Detalhes**|
|--- | --- | --- | ---|
|**Servidor de processos Deteta automaticamente máquinas virtuais VMware** | Precisa de, pelo menos, um utilizador só de leitura | Objeto de centro de dados –> Propagate ao objeto do subordinado função = só de leitura | Utilizador atribuído ao nível do datacenter e tem acesso a todos os objetos no Centro de dados.<br/><br/> Para restringir o acesso, atribua o **sem acesso** função com o **Propagate para subordinado** objeto, para os objetos subordinados (anfitriões vSphere, datastores, máquinas virtuais e redes).|
|**Ativação pós-falha** | Precisa de, pelo menos, um utilizador só de leitura | Objeto de centro de dados –> Propagate ao objeto do subordinado função = só de leitura | Utilizador atribuído ao nível do datacenter e tem acesso a todos os objetos no Centro de dados.<br/><br/> Para restringir o acesso, atribua o **sem acesso** função com o **Propagate para subordinado** objeto para os objetos subordinados (anfitriões vSphere, datastores, máquinas virtuais e redes).<br/><br/> Útil para fins de migração, mas não completa replicação, ativação pós-falha, reativação pós-falha.|
|**Ativação pós-falha e a reativação pós-falha** | Sugerimos que crie uma função (AzureSiteRecoveryRole) com as permissões necessárias e, em seguida, atribua a função a um grupo ou utilizador de VMware | Objeto de centro de dados –> Propagate ao objeto do subordinado função = AzureSiteRecoveryRole<br/><br/> Arquivo de dados -> atribuir espaço em, procurar o arquivo de dados, as operações de baixo nível de ficheiro, remova o ficheiro, atualizar ficheiros de máquina virtual<br/><br/> Rede -> atribuição de rede<br/><br/> Recursos -> VM atribuir ao agrupamento de recursos, migrar alimentado desligar a VM, migrar alimentado na VM<br/><br/> Tarefas -> tarefas de criação, a tarefa de atualização<br/><br/> Configuração -> de máquina virtual<br/><br/> Máquina virtual -> interagir -> pergunta de resposta, a ligação de dispositivos, configurar suporte de dados do CD, configurar o suporte de dados de disquetes, desligar, ligar, instalação de ferramentas do VMware<br/><br/> Máquina virtual -> inventário -> criar, registar, anular o registo<br/><br/> Máquina virtual -> aprovisionamento -> Permitir transferências de máquina virtual, permitem carregar ficheiros de máquina virtual<br/><br/> Máquina virtual -> instantâneos -> Remover instantâneos | Utilizador atribuído ao nível do datacenter e tem acesso a todos os objetos no Centro de dados.<br/><br/> Para restringir o acesso, atribua o **sem acesso** função com o **Propagate para subordinado** objeto, para os objetos subordinados (anfitriões vSphere, datastores, máquinas virtuais e redes).|

## <a name="create-an-account-to-connect-to-vmware-vcenter-server-vmware-vsphere-exsi-host"></a>Criar uma conta para ligar ao servidor VMware vCenter / anfitrião do VMware vSphere EXSi
1. Início de sessão para o servidor de configuração e inicie o cspsconfigtool.exe utilizando o atalho para o colocar no ambiente de trabalho.
2. Clique em **adicionar conta** no **gerir conta** separador.

  ![adicionar a conta](./media/site-recovery-vmware-to-azure-manage-vcenter/addaccount.png)
3. Forneça os detalhes da conta e clique em OK para adicionar a conta. A conta deve ter os privilégios listados no [preparar uma conta de deteção automática](#prepare-an-account-for-automatic-discovery) secção.

  >[!NOTE]
  Demora cerca de 15 minutos para as informações de conta sincronizado com o serviço de recuperação de sites.


## <a name="associate-a-vmware-vcenter-vmware-vsphere-esx-host-add-vcenter"></a>Associar um VMware vCenter / ESX do VMware vSphere alojar (adicionar vCenter)
* No portal do Azure, navegue para *YourRecoveryServicesVault* > **infraestrutura de recuperação de Site** > **servidores de configuração** > *ConfigurationServer*
* Na página de detalhes do servidor de configuração, clique o botão do vCenter +.

[!INCLUDE [site-recovery-add-vcenter](../../includes/site-recovery-add-vcenter.md)]

## <a name="modify-credentials-used-to-connect-to-the-vcenter-server-vsphere-esxi-host"></a>Modificar as credenciais utilizadas para ligar ao servidor vCenter / anfitrião ESXi de vSphere

1. Início de sessão para o servidor de configuração e inicie o cspsconfigtool.exe
2. Clique em **adicionar conta** no **gerir conta** separador.

  ![adicionar a conta](./media/site-recovery-vmware-to-azure-manage-vcenter/addaccount.png)
3. Forneça os detalhes da conta nova e clique em OK para adicionar a conta. A conta deve ter os privilégios listados no [preparar uma conta de deteção automática](#prepare-an-account-for-automatic-discovery) secção.
4. No portal do Azure, navegue para *YourRecoveryServicesVault* > **infraestrutura de recuperação de Site** > **servidores de configuração** > *ConfigurationServer*
5. Na página de detalhes do servidor de configuração, clique o **atualização do servidor** botão.
6. Uma vez concluída a tarefa de atualização do servidor, selecione o vCenter Server para abrir a página de resumo do vCenter.
7. Selecione a conta recentemente adicionada no **conta de anfitriões vSphere/servidor vCenter** campo e clique em de **guardar** botão.

  ![conta modificar](./media/site-recovery-vmware-to-azure-manage-vcenter/modify-vcente-creds.png)

## <a name="delete-a-vcenter-in-azure-site-recovery"></a>Eliminar um vCenter no Azure Site Recovery
1. No portal do Azure, navegue para *YourRecoveryServicesVault* > **infraestrutura de recuperação de Site** > **servidores de configuração** > *ConfigurationServer*
2. Na página de detalhes do servidor de configuração, selecione o vCenter Server para abrir a página de resumo do vCenter.
3. Clique em de **eliminar** botão para eliminar o vCenter

  ![eliminar conta](./media/site-recovery-vmware-to-azure-manage-vcenter/delete-vcenter.png)

> [!NOTE]
Se necessitar de modificar a vCenters endereço de IP/FQDN, os detalhes da porta, em seguida, terá de eliminar o vCenter Server e adicioná-la novamente novamente.

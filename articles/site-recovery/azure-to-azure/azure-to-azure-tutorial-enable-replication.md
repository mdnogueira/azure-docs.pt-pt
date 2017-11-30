---
title: "Configurar a recuperação após desastre para as VMs do Azure para uma região secundária do Azure com o Azure Site Recovery (pré-visualização)"
description: "Saiba como configurar a recuperação após desastre para as VMs do Azure para uma região do Azure diferente, utilizando o serviço do Azure Site Recovery."
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 11/01/2017
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 7dfe543f30772fc11174a7fff43369c1e2f19029
ms.sourcegitcommit: cfd1ea99922329b3d5fab26b71ca2882df33f6c2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/30/2017
---
# <a name="set-up-disaster-recovery-for-azure-vms-to-a-secondary-azure-region-preview"></a>Configurar a recuperação após desastre para as VMs do Azure para uma região secundária do Azure (pré-visualização)

O [do Azure Site Recovery](../site-recovery-overview.md) serviço contribui para a sua estratégia de recuperação após desastre, gerir e da orquestração de replicação, ativação pós-falha e a reativação pós-falha de máquinas no local e máquinas de virtuais (VMs) do Azure.

Este tutorial mostra como configurar a recuperação de desastres para uma região secundária do Azure para as VMs do Azure. Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar um cofre dos Serviços de Recuperação 
> * Verifique as definições do recurso de destino
> * Configurar o acesso de saída para VMs
> * Ativar a replicação para uma VM

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial:

- Certifique-se de que compreende o [arquitetura do cenário e componentes](concepts-azure-to-azure-architecture.md).
- Reveja o [suportar requisitos](site-recovery-support-matrix-azure-to-azure.md) para todos os componentes.

## <a name="create-a-vault"></a>Criar um cofre

Crie o cofre em qualquer região, exceto a região de origem.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) > **Serviços de Recuperação**.
2. Clique em **Novo** > **Monitorização e Gestão** > **Backup e Site Recovery**.
3. Em **Nome**, especifique um nome amigável para identificar o cofre. Se tiver mais do que uma subscrição, selecione a adequado.
4. Criar um grupo de recursos ou selecione um existente. Selecione uma região do Azure. Para verificar as regiões suportadas, veja a disponibilidade geográfica em [Detalhes dos Preços do Azure Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/).
5. Para aceder rapidamente ao Cofre a partir do dashboard, clique em **afixar ao dashboard** e, em seguida, clique em **criar**.

   ![Novo cofre](./media/azure-to-azure-tutorial-enable-replication/new-vault-settings.png)

   O novo cofre é adicionado ao **Dashboard** em **todos os recursos**e no principal **cofres dos serviços de recuperação** página.

## <a name="verify-target-resources"></a>Certifique-se de recursos de destino

1. Certifique-se de que a sua subscrição do Azure permite-lhe criar VMs na região de destino utilizada para recuperação após desastre. Contacte o suporte para ativar a quota necessária.

2. Certifique-se a que sua subscrição tem recursos suficientes para suportar as VMs com tamanhos correspondem as VMs de origem. Recuperação de site escolhe o mesmo tamanho ou o tamanho mais próximo possível da VM de destino.

## <a name="configure-outbound-network-connectivity"></a>Configurar a conectividade de rede de saída

Para a recuperação de Site a funcionar conforme esperado, terá de efetuar algumas alterações na conectividade de rede de saída, a partir de VMs que pretende replicar.

- Recuperação de sites não suporta a utilização de um proxy de autenticação a conectividade de rede do controlo.
- Se tiver um proxy de autenticação, não é possível ativar a replicação.

### <a name="outbound-connectivity-for-urls"></a>Conectividade de saída para os URLs

Se estiver a utilizar um proxy de firewall baseada no URL para controlar a conectividade de saída, permitir o acesso aos URLs seguintes utilizado pela recuperação de sites.

| **URL** | **Detalhes** |
| ------- | ----------- |
| *.blob.core.windows.net | Permite a ser escrito da VM para a conta de armazenamento de cache na região de origem de dados. |
| login.microsoftonline.com | Fornece autorização e autenticação para os URLs do serviço de recuperação de sites. |
| *.hypervrecoverymanager.windowsazure.com | Permite que a VM comunicar com o serviço de recuperação de sites. |
| *. servicebus.windows.net | Permite que a VM ao escrever a monitorização de recuperação de Site e os dados de diagnóstico. |

### <a name="outbound-connectivity-for-ip-address-ranges"></a>Conectividade de saída para intervalos de endereços IP

Quando utilizar qualquer baseada em IP firewall, proxy ou as regras do NSG para controlar a conectividade de saída, os seguintes intervalos de endereços IP tem de estar na lista de permissões. Transferir uma lista de intervalos das seguintes ligações:

  - [Intervalos de IP de centro de dados do Microsoft Azure](http://www.microsoft.com/download/details.aspx?id=41653)
  - [Windows intervalos de IP do Datacenter do Azure na Alemanha](http://www.microsoft.com/download/details.aspx?id=54770)
  - [Windows intervalos de IP do Datacenter do Azure na China](http://www.microsoft.com/download/details.aspx?id=42064)
  - [Intervalos de endereços IP e URLs do Office 365](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2#bkmk_identity)
  - [Endereços de IP do ponto final de serviço de recuperação de site](https://aka.ms/site-recovery-public-ips)

Utilize estas listas para configurar os controlos de acesso de rede na sua rede. Pode utilizar este [script](https://gallery.technet.microsoft.com/Azure-Recovery-script-to-0c950702) para criar regras NSG necessárias.

## <a name="verify-azure-vm-certificates"></a>Verificar os certificados de VM do Azure

Verifique se todos os certificados de raiz mais recentes estão presentes no Windows ou VMs do Linux que pretende replicar. Se os certificados de raiz mais recentes não estão, a VM não é registado no Site Recovery, due restrições de segurança.

- Para VMs do Windows, instale todas as atualizações mais recentes do Windows na VM, para que todos os certificados de raiz fidedigna no computador. Num ambiente desligado, a seguir o padrão de Windows Update e os processos de atualização de certificados para a sua organização.

- Para VMs com Linux, siga as orientações fornecidas pelo distribuidor do Linux, para obter os certificados de raiz fidedigna mais recentes e a lista de revogação de certificados na VM.

## <a name="set-permissions-on-the-account"></a>Defina as permissões da conta

O Azure Site Recovery fornece três funções incorporadas para controlar as operações de gestão do Site Recovery.

- **Contribuinte de recuperação de sites** -esta função tem todas as permissões necessárias para gerir as operações do Azure Site Recovery num cofre dos serviços de recuperação. Um utilizador com esta função, no entanto, não é possível criar ou eliminar um cofre dos serviços de recuperação ou atribuir direitos de acesso a outros utilizadores. Esta função é mais adequada para os administradores de recuperação de desastres que podem ativar e gerir a recuperação após desastre para aplicações ou organizações completos.

- **Operador de recuperação de sites** -esta função tem permissões para executar e operations manager de ativação pós-falha e a reativação pós-falha. Um utilizador com esta função não é possível ativar ou desativar a replicação, criar ou eliminar cofres, registar a nova infraestrutura ou atribuir direitos de acesso a outros utilizadores. Esta função é mais adequada para um operador de recuperação de desastres que pode efetuar a ativação pós-falha de máquinas virtuais ou aplicações quando indicado pelos proprietários da aplicação e os administradores de TI. Post resolução de desastre, o operador de DR pode voltar a proteger e a reativação pós-falha as máquinas virtuais.

- **Leitor de recuperação de sites** -esta função tem permissões para ver todas as operações de gestão do Site Recovery. Esta função é mais adequada para um executivo de monitorização de IT que pode monitorizar o estado atual da proteção e emitir pedidos de suporte.

Saber mais sobre [funções incorporadas do RBAC do Azure](../../active-directory/role-based-access-built-in-roles.md)

## <a name="enable-replication"></a>Ativar a replicação

### <a name="select-the-source"></a>Selecione a origem

1. No cofres dos serviços de recuperação, clique no nome do cofre > **+ replicar**.
2. No **origem**, selecione **Azure - pré-visualização**.
3. No **localização de origem**, selecione a origem de região do Azure onde as VMs estão a ser executados.
4. Selecione o **modelo de implementação da máquina virtual do Azure** para VMs: **Resource Manager** ou **clássico**.
5. Selecione o **grupo de recursos de origem** para VMs do Gestor de recursos, ou **serviço em nuvem** para VMs clássicas.
6. Clique em **OK** para guardar as definições.

### <a name="select-the-vms"></a>Selecione as máquinas virtuais

Recuperação de site obtém uma lista de VMs associados à subscrição e serviço de nuvem/grupo de recursos.

1. No **máquinas virtuais**, selecione as VMs que pretende replicar.
2. Clique em **OK**.

### <a name="configure-replication-settings"></a>Configurar as definições de replicação

Recuperação de sites cria as predefinições e política de replicação para a região de destino. Pode alterar as definições com base nos seus requisitos.

1. Clique em **definições** para ver as definições de destino.
2. Para substituir as predefinições de destino, clique em **personalizar**. 

![Configurar definições](./media/azure-to-azure-tutorial-enable-replication/settings.png)


- **Localização de destino**: A região de destino utilizada para recuperação após desastre. Recomendamos que a localização de destino corresponde a localização do cofre do Site Recovery.

- **Grupo de recursos de destino**: O grupo de recursos na região de destino que contém as VMs do Azure após a ativação pós-falha. Por predefinição, a recuperação de Site cria um novo grupo de recursos na região de destino com um sufixo "asr".

- **Rede virtual de destino**: A rede na região de destino que as VMs estão localizadas após a ativação pós-falha.
  Por predefinição, a recuperação de sites cria uma nova rede virtual (e sub-redes) na região de destino com um sufixo "asr".

- **As contas de armazenamento em cache**: recuperação de Site utiliza uma conta do storage na região de origem. As alterações para as VMs de origem são enviadas para esta conta antes de replicação para a localização de destino.

- **As contas de armazenamento de destino**: por predefinição, a recuperação de sites cria uma nova conta do storage na região de destino para espelhar a conta de armazenamento VM de origem.

- **Conjuntos de disponibilidade de destino**: por predefinição, a recuperação de Site cria uma novo conjunto de disponibilidade na região de destino com o sufixo "asr". Só é possível adicionar conjuntos de disponibilidade se VMs fizerem parte de um conjunto na região de origem.

- **Nome da política de replicação**: nome da política.

- **Retenção do ponto de recuperação**: por predefinição, a recuperação de sites mantém pontos de recuperação durante 24 horas. Pode configurar um valor entre 1 e 72 horas.

- **Frequência de instantâneos consistentes com aplicação**: por predefinição, a recuperação de sites tira um instantâneo consistentes da aplicação todas as 4 horas. Pode configurar qualquer valor entre 1 e 12 horas. Um instantâneo consistentes da aplicação é um instantâneo de ponto no tempo dos dados de aplicação dentro da VM. Serviço de cópia de sombra de volume (VSS) assegura que aplicações na VM estão num estado consistente quando se obtém o instantâneo.

### <a name="track-replication-status"></a>Controlar o estado de replicação

1. No **definições**, clique em **atualizar** para obter o estado mais recente.

2. Pode controlar o progresso do **ativar a proteção** da tarefa no **definições** > **tarefas** > **tarefas de recuperação de Site**.

3. No **definições** > **itens replicados**, pode ver o estado de VMs e o progresso da replicação inicial. Clique na VM para desagregar as respetivas definições.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial que configurou recuperação após desastre para uma VM do Azure. Passo seguinte consiste em testar a configuração.

> [!div class="nextstepaction"]
> [Executar um teste de recuperação após desastre](azure-to-azure-tutorial-dr-drill.md)

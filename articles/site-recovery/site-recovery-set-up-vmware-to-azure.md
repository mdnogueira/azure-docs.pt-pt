---
title: Configurar o ambiente de origem (VMware para o Azure) | Microsoft Docs
description: "Este artigo descreve como configurar o seu ambiente no local para iniciar a replicação de máquinas virtuais VMware para o Azure."
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
ms.workload: storage-backup-recovery
ms.date: 11/23/2017
ms.author: anoopkv
ms.openlocfilehash: 32a3f7498d3c8891178818436e33221f91ae2f8f
ms.sourcegitcommit: 5bced5b36f6172a3c20dbfdf311b1ad38de6176a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/27/2017
---
# <a name="set-up-the-source-environment-vmware-to-azure"></a>Configurar o ambiente de origem (VMware para o Azure)
> [!div class="op_single_selector"]
> * [VMware para o Azure](./site-recovery-set-up-vmware-to-azure.md)
> * [Físico para o Azure](./site-recovery-set-up-physical-to-azure.md)

Este artigo descreve como configurar o seu ambiente no local para iniciar a replicação de máquinas virtuais em execução no VMware para o Azure.

## <a name="prerequisites"></a>Pré-requisitos

O artigo assume que já criou:
- Um cofre dos serviços de recuperação no [portal do Azure](http://portal.azure.com "portal do Azure").
- Uma conta dedicada o VMware vcenter que podem ser utilizadas para [a deteção automática](./site-recovery-vmware-to-azure.md).
- Uma máquina virtual em que pretende instalar o servidor de configuração.

## <a name="configuration-server-minimum-requirements"></a>Requisitos mínimos do servidor de configuração
A tabela seguinte lista o mínimos de hardware, software e requisitos de rede para um servidor de configuração.

> [!IMPORTANT]
> Quando implementar um servidor de configuração de proteção de máquinas virtuais VMware, recomendamos que implemente-o como um **altamente disponível (HA)** máquina virtual.

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]

> [!NOTE]
> Servidores proxy baseado em HTTPS não são suportadas pelo servidor de configuração.

## <a name="choose-your-protection-goals"></a>Escolha os seus objetivos de proteção

1. No portal do Azure, visite o **dos serviços de recuperação** painel do cofre e selecione o cofre.
2. No menu de recurso do cofre, aceda a **introdução** > **recuperação de Site** > **passo 1: preparar a infraestrutura** > **objetivo de proteção**.

    ![Selecione os objetivos](./media/site-recovery-set-up-vmware-to-azure/choose-goals.png)
3. No **objetivo de proteção**, selecione **para o Azure**e escolha **Sim, com o VMware vSphere hipervisor**. Em seguida, clique em **OK**.

    ![Selecione os objetivos](./media/site-recovery-set-up-vmware-to-azure/choose-goals2.png)

## <a name="set-up-the-source-environment"></a>Configurar o ambiente de origem
Configurar o ambiente de origem envolve dois principais atividades:

- Instalar e registar um servidor de configuração com a recuperação de Site.
- Detete máquinas virtuais no local através da ligação de recuperação de sites ao seu local no VMware vCenter ou vSphere EXSi anfitriões.

### <a name="step-1-install-and-register-a-configuration-server"></a>Passo 1: Instalar e registar um servidor de configuração

1. Clique em **passo 1: preparar infraestrutura** > **origem**. No **preparar a origem**, se não tiver um servidor de configuração, clique em **+ o servidor de configuração** para adicionar um.

    ![Configurar a origem](./media/site-recovery-set-up-vmware-to-azure/set-source1.png)
2. No **Adicionar servidor** painel, verifique se **servidor de configuração** aparece no **tipo de servidor**.
4. Transfira o ficheiro de instalação de configuração do Unified Site Recovery.
5. Transferir a chave de registo do cofre. Tem a chave de registo ao executar a configuração do Unified. A chave é válida durante cinco dias depois de gerá-la.

    ![Configurar a origem](./media/site-recovery-set-up-vmware-to-azure/set-source2.png)
6. No computador que está a utilizar como o servidor de configuração, execute **configuração de unificada do Azure Site Recovery** para instalar o servidor de configuração, o servidor de processos e o servidor de destino principal.

#### <a name="run-azure-site-recovery-unified-setup"></a>Configuração de unificada de execução do Azure Site Recovery

> [!TIP]
> Registo do servidor de configuração falha se a hora no relógio do sistema do computador é diferente da hora local em mais de cinco minutos. Sincronizar o relógio do seu sistema com um [hora Server](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/get-started/windows-time-service/windows-time-service) antes de iniciar a instalação.

[!INCLUDE [site-recovery-add-configuration-server](../../includes/site-recovery-add-configuration-server.md)]

> [!NOTE]
> O servidor de configuração pode ser instalado através de linha de comandos. Para obter mais informações, consulte [a instalar o servidor de configuração com as ferramentas da linha de comandos](http://aka.ms/installconfigsrv).

#### <a name="add-the-vmware-account-for-automatic-discovery"></a>Adicione a conta de VMware para a deteção automática

[!INCLUDE [site-recovery-add-vcenter-account](../../includes/site-recovery-add-vcenter-account.md)]

### <a name="step-2-add-a-vcenter"></a>Passo 2: Adicionar um vCenter
Para permitir que o Azure Site Recovery ao detetar máquinas virtuais em execução no seu ambiente no local, terá de ligar o servidor vCenter VMware ou vSphere ESXi anfitriões com a recuperação de Site.

Selecione **+ vCenter** para iniciar a ligação um servidor VMware vCenter ou um anfitrião do VMware vSphere ESXi.

[!INCLUDE [site-recovery-add-vcenter](../../includes/site-recovery-add-vcenter.md)]


## <a name="common-issues"></a>Problemas comuns
[!INCLUDE [site-recovery-vmware-to-azure-install-register-issues](../../includes/site-recovery-vmware-to-azure-install-register-issues.md)]


## <a name="next-steps"></a>Passos seguintes
[Configurar o ambiente de destino](./site-recovery-prepare-target-vmware-to-azure.md) no Azure.

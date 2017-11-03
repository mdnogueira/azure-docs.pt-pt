---
title: " Gerir um servidor de processos em execução no Azure (Resource Manager) | Microsoft Docs"
description: "Este artigo descreve como configurar um servidor de processos de reativação pós-falha (Resource Manager) no Azure."
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
ms.date: 06/29/2017
ms.author: anoopkv
ms.openlocfilehash: 2b9b31abd5d11d02935a74e47d26be9803cdc920
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="manage-a-process-server-running-in-azure-resource-manager"></a>Gerir um servidor de processos em execução no Azure (Resource Manager)
> [!div class="op_single_selector"]
> * [Resource Manager](./site-recovery-vmware-setup-azure-ps-resource-manager.md)
> * [Clássico](./site-recovery-vmware-setup-azure-ps-classic.md)

Durante a reativação pós-falha, recomenda-se para implementar o servidor de processos no Azure se não houver latência elevada entre a rede Virtual do Azure e a sua rede no local. Este artigo descreve como pode configurar, configurar e gerir servidores de processos em execução no Azure.

> [!NOTE]
> Este artigo é para ser utilizada se tiver utilizado **Resource Manager** como o modelo de implementação para as máquinas virtuais durante a ativação pós-falha. Se utilizou **clássico** como o modelo de implementação, siga os passos no [como definir e configurar um servidor de processos de reativação pós-falha (clássica)](./site-recovery-vmware-setup-azure-ps-classic.md)

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [site-recovery-vmware-process-server-prerequ](../../includes/site-recovery-vmware-azure-process-server-prereq.md)]

## <a name="deploy-a-process-server-on-azure"></a>Implementar um servidor de processos no Azure
1. No cofre > **infraestrutura de recuperação de Site** (sob o cabeçalho de "Gerir") > **servidores de configuração** (sob o cabeçalho de "Para VMware e máquinas físicas"), selecione o servidor de configuração.
2. Na página de detalhes do servidor de configuração que se abre, clique em "+ o servidor de processo"

  ![Adicionar a galeria do servidor de processo](./media/site-recovery-vmware-setup-azure-ps-arm/add-ps.png)

3.  No **Adicionar servidor de processos** página, selecione os seguintes valores

  ![Adicionar item de galeria do servidor de processo](./media/site-recovery-vmware-setup-azure-ps-arm/add-ps-page-1.png)
|**Nome do campo**|**Valor**|
|-|-|
|Escolher se quer implementar o servidor de processos|Selecione o valor **implementar um servidor de processos de reativação pós-falha no Azure** |
|Subscrição|Selecione a subscrição do Azure onde executar a ativação pós-falha de máquinas virtuais|
|Grupo de Recursos|Pode criar um grupo de recursos para implementar este servidor de processo ou optar por implementar o servidor de processos num grupo de recursos existente|
|Localização|Selecione o Centro de dados do Azure na qual as máquinas virtuais em que a ativação pós-falha no|
|Rede do Azure|Selecione o Network(VNet) Virtual do Azure que as máquinas virtuais em que a ativação pós-falha em. Se efetuar a ativação pós-falha de máquinas virtuais em várias VNets do Azure, em seguida, precisa de um servidor de processos implementado por VNet|

4. Preencha o resto das propriedades para o servidor de processos

  ![Adicionar o servidor de processos resumo](./media/site-recovery-vmware-setup-azure-ps-arm/add-ps-page-2.png)
|**Nome do campo**|**Valor**|
|-|-|
|Nome do servidor|Nome a apresentar & o nome de anfitrião para a máquina de virtual do servidor de processo|
| Nome de Utilizador|Um nome de utilizador torna-se um administrador nessa máquina virtual|
|Conta de Armazenamento|Nome da conta do Storage onde é colocado do disco virtual da máquina virtual|
|Subrede|A sub-rede da VNet do Azure à qual a máquina virtual está ligada|
| Endereço IP|Endereço IP que pretende que o servidor de processos para partem do princípio de uma vez efetua o arranque|
5. Clique no botão OK para iniciar a implementação da máquina de virtual do servidor de processo.

> [!NOTE]
> Para poder utilizar este servidor de processos para reativação pós-falha, tem de registá-lo com o servidor de configuração no local.

## <a name="registering-the-process-server-running-in-azure-to-a-configuration-server-running-on-premises"></a>Registar o servidor de processos (em execução no Azure) para um servidor de configuração (em execução no local)

[!INCLUDE [site-recovery-vmware-register-process-server](../../includes/site-recovery-vmware-register-process-server.md)]

## <a name="upgrading-the-process-server-to-latest-version"></a>Atualizar o servidor de processos para a versão mais recente.

[!INCLUDE [site-recovery-vmware-upgrade-process-server](../../includes/site-recovery-vmware-upgrade-process-server.md)]

## <a name="unregistering-the-process-server-running-in-azure-from-a-configuration-server-running-on-premises"></a>Anulação do registo do servidor de processos (em execução no Azure) de um servidor de configuração (em execução no local)

[!INCLUDE [site-recovery-vmware-unregister-process-server](../../includes/site-recovery-vmware-unregister-process-server.md)]

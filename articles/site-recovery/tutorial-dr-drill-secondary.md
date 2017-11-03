---
title: "Executar um exercício de recuperação após desastre para o site secundário no local com o Azure Site Recovery | Microsoft Docs"
description: "Saiba mais sobre como executar um exercício de recuperação após desastre para o site secundário no local com o Azure Site Recovery"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 445878e2-6682-49ba-914d-4c6824ab08a6
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 09/18/2017
ms.author: raynew
ms.openlocfilehash: 8737c2a22bd729cfc15d5448e1ec0becef643fd5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="run-a-disaster-recovery-drill-for-hyper-v-vms-to-your-secondary-on-premises-site"></a>Executar um exercício de recuperação após desastre para as VMs de Hyper-V para o site secundário no local

O [do Azure Site Recovery](site-recovery-overview.md) serviço contribui para a sua estratégia de recuperação após desastre, gerir e da orquestração de replicação, ativação pós-falha e a reativação pós-falha de máquinas no local e máquinas de virtuais (VMs) do Azure.

Este tutorial mostra como executar um exercício de recuperação após desastre para as VMs de Hyper-V para o site secundário no local. VMs Hyper-V tem de ser geridas numa nuvem privada do System Center Virtual Machine Manager (VMM). Um exercício valida a estratégia de replicação sem perda de dados ou o período de indisponibilidade e não afeta a afetar o seu ambiente de produção. Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Preparar os pré-requisitos de ativação pós-falha de teste
> * Executar uma ativação pós-falha de teste para uma única máquina


## <a name="prerequisites"></a>Pré-requisitos

- Pode executar uma ativação pós-falha de teste com algumas opções de funcionamento em rede no site secundário. Pode executar a ativação pós-falha sem uma rede, com uma rede existente ou permitem criar automaticamente uma rede de teste de recuperação de Site. 
**Se pretender utilizar uma rede de produção existente para a ativação pós-falha de teste:**:
    - A VM principal deve ser encerrada quando estiver a fazer a ativação pós-falha de teste. Caso contrário, duas VMs com a mesma identidade estarão em execução na mesma rede, ao mesmo tempo. 
    - Se efetuar alterações para testar as VMs, as alterações serão perdidas quando limpar a ativação pós-falha de teste. As alterações não são replicadas para a VM principal.
    - Uma rede de produção de teste faz com que o período de indisponibilidade para cargas de trabalho de produção. Peça aos seus utilizadores para não utilizar aplicações relacionadas quando o exercício de recuperação após desastre está em curso. 
- A rede de teste de réplica não necessita fazer corresponder o tipo de rede lógica de VMM utilizado para ativação pós-falha de teste. No entanto, algumas combinações não funcionam. Por exemplo se a réplica utiliza DHCP e do isolamento baseado em VLAN, não é possível utilizar a virtualização de rede do Windows para a rede de ativação pós-falha de teste, porque tem de conjuntos de endereços IP. 
- Recomendamos que não utiliza a rede que selecionou mapeamento da rede, para ativação pós-falha de teste.


## <a name="run-a-test-failover-for-a-vm"></a>Executar uma ativação pós-falha de teste para uma VM

1. No **itens replicados**, clique na VM > **ativação pós-falha de teste**.
2. No **ativação pós-falha de teste**, especifique como VMs de teste será ligada a redes após a ativação pós-falha de teste. Recomendamos que para efeitos deste tutorial, lhe permitem criar automaticamente uma rede de teste de recuperação de Site. [Saiba mais](site-recovery-test-failover-vmm-to-vmm.md#network-options-in-site-recovery).
3. Clique em **OK** para iniciar a ativação pós-falha. Controlar o progresso no **tarefas** separador.
4. Após a conclusão da ativação pós-falha, certifique-se de que o teste VMs iniciam com êxito.
5. Quando tiver terminado, clique em **ativação pós-falha de teste de limpeza**. Isto elimina o teste de VMs e quaisquer redes criadas durante a ativação pós-falha de teste.
6. No **notas**, registar e guardar todas as observações associadas à ativação pós-falha de teste. 


## <a name="next-steps"></a>Passos seguintes

[Executar uma ativação pós-falha de produção](tutorial-vmm-to-vmm-failover-failback.md)







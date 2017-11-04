---
title: Migrar VMs do AWS para o Azure | Microsoft Docs
description: "Este artigo descreve como migrar máquinas virtuais em execução nos Amazon Web Services (AWS) para o Azure utilizando o Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: bsiva
manager: jwhit
editor: 
ms.assetid: ddb412fd-32a8-4afa-9e39-738b11b91118
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 05/31/2017
ms.author: bsiva
ms.openlocfilehash: b3c0727a279649f4f7dae30d41027129ce5b04ee
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="migrate-virtual-machines-in-amazon-web-services-aws-to-azure-with-azure-site-recovery"></a>Migrar máquinas virtuais nos Amazon Web Services (AWS) para o Azure com o Azure Site Recovery

Este artigo descreve como migrar instâncias de Windows em AWS para máquinas virtuais do Azure com o [do Azure Site Recovery](site-recovery-overview.md) serviço.

Migração de forma eficaz é uma ativação pós-falha do AWS para o Azure. Não é possível máquinas de reativação pós-falha para o AWS, e não existe nenhuma replicação em curso. Este artigo descreve os passos de migração no portal do Azure e baseiam-se nas instruções para [replicar uma máquina física para o Azure](site-recovery-vmware-to-azure.md).

Publique comentários ou perguntas na parte inferior deste artigo ou no [fórum de serviços de recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)

## <a name="supported-operating-systems"></a>Sistemas operativos suportados

Recuperação de sites pode ser utilizada para migrar instâncias de EC2 que execute qualquer um dos seguintes sistemas operativos:

- Windows (apenas 64 bits)
    - Windows Server 2008 R2 SP1 + (controladores do Citrix PV ou apenas os controladores de AWS PV. **As instâncias em execução controladores de VM de RedHat PV não são suportadas**) Windows Server 2012 Windows Server 2012 R2
- Linux (apenas 64 bits)
    - Red Hat Enterprise Linux 6.7 (apenas para instâncias HVM virtualizada)

## <a name="prerequisites"></a>Pré-requisitos

Eis o que precisa para esta implementação:

* **Servidor de configuração**: um Amazon EC2 implementação da VM com o Windows Server 2012 R2 como o servidor de configuração. Por predefinição, os outros componentes do Azure Site Recovery (servidor de processos e o servidor de destino principal) são instalados quando implementar o servidor de configuração. Este artigo descreve os passos de migração no portal do Azure e baseiam-se nas instruções para [Saiba mais](site-recovery-components.md)

* **Instâncias de EC2**: O Amazon EC2 instâncias de máquinas virtuais que pretende migrar.

## <a name="deployment-steps"></a>Passos da implementação

1. Crie um cofre dos Serviços de Recuperação.
2. O grupo de segurança das suas instâncias EC2 deve ter regras configuradas para permitir a comunicação entre a instância de EC2 que pretende migrar e a instância no qual planeia implementar o servidor de configuração.

3. Na mesma Amazon Virtual nuvem privada como as instâncias de EC2, implemente um servidor de configuração de ASR. Consulte o VMware / físico para pré-requisitos do Azure para os requisitos de implementação do servidor de configuração.

    ![DeployCS](./media/site-recovery-migrate-aws-to-azure/migration_pic2.png)

4.  Depois do servidor de configuração é implementado no AWS e registado com o Cofre dos serviços de recuperação, deverá ver o servidor de configuração e o servidor de processos em infraestrutura de recuperação de sites como mostrado abaixo:

    ![CSinVault](./media/site-recovery-migrate-aws-to-azure/migration_pic3.png)

5. Após implementar o servidor de configuração (poderá demorar até 15 minustes pela mesma aparecem), validar que este consegue comunicar com as VMs que pretende migrar.

6. [Configurar as definições de replicação](site-recovery-setup-replication-settings-vmware.md).

7. Ativar a replicação: Ativar a replicação para as VMs que pretende migrar. Pode detetar as instâncias de EC2 utilizando os endereços IP privados, que pode obter a partir da consola EC2.

    ![SelectVM](./media/site-recovery-migrate-aws-to-azure/migration_pic4.png)

8. Depois das instâncias de EC2 tenham sido protegidas e a replicação para o Azure estiver concluída, [executar uma ativação pós-falha de teste](site-recovery-test-failover-to-azure.md) para validar o desempenho da sua aplicação no Azure.

    ![TFI](./media/site-recovery-migrate-aws-to-azure/migration_pic5.png)

9. Execute uma ativação pós-falha do AWS para o Azure para cada VM. Opcionalmente, pode criar um plano de recuperação e executar uma ativação pós-falha, para migrar várias máquinas virtuais do AWS para o Azure. [Saiba mais](site-recovery-create-recovery-plans.md) sobre os planos de recuperação.

10. Para a migração, não precisa de consolidar uma ativação pós-falha. Em vez disso, selecione a opção de concluir a migração para cada máquina que pretende migrar. A ação de concluir a migração termina o processo de migração de configuração, remove a replicação da máquina e deixa de faturação de recuperação de Site para a máquina.

    ![Migrar](./media/site-recovery-migrate-aws-to-azure/migration_pic6.png)

## <a name="next-steps"></a>Passos seguintes

- [Preparar máquinas migradas para ativar a replicação](site-recovery-azure-to-azure-after-migration.md) para outra região para as necessidades da recuperação após desastre.
- Comece a proteger as suas cargas de trabalho ao [replicar máquinas virtuais do Azure.](site-recovery-azure-to-azure.md)

---
title: "Rever a arquitetura da replicação do Hyper-V para um site secundário com o Azure Site Recovery | Microsoft Docs"
description: "Este artigo apresenta uma descrição geral da arquitetura para replicar VMs Hyper-V no local para um site do System Center VMM secundário com o Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 26475782-a21a-408a-b089-35382d7e010e
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/10/2017
ms.author: raynew
ms.openlocfilehash: a7a493097a4eaacc2c8d8449906b4a57eb411827
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="hyper-v-replication-to-a-secondary-site"></a>Replicação de Hyper-V para um site secundário

Este artigo descreve os componentes e os processos envolvidos ao replicar máquinas virtuais (VMs) de Hyper-V no local em clouds do System Center Virtual Machine Manager (VMM) para um site de VMM secundário com o serviço [Azure Site Recovery](site-recovery-overview.md) no portal do Azure.


## <a name="architectural-components"></a>Componentes da arquitetura

A seguinte tabela e o gráfico fornecem uma vista detalhada dos componentes utilizados para replicação de Hyper-V para um site secundário.

**Componente** | **Requisito** | **Detalhes**
--- | --- | ---
**Azure** | Subscrição do Azure | Criar um cofre dos Serviços de Recuperação na subscrição do Azure para orquestrar e gerir a replicação entre localizações do VMM.
**Servidor VMM** | Precisa de uma localização primária e secundária do VMM. | Recomenda-se um servidor VMM no site primário e um no site secundário.
**Servidor Hyper-V** |  Um ou mais servidores de anfitriões Hyper-V nas clouds do VMM primárias e secundárias. | Os dados são replicados entre os servidores anfitrião primário e secundário Hyper-V através de LAN ou VPN mediante a utilização de Kerberos ou da autenticação de certificados.  
**VMs de Hyper-V** | No servidor de anfitrião Hyper-V. | O servidor de anfitrião de origem deve ter, pelo menos, uma VM que queira replicar.

**No local com a arquitetura de no local**

![Local para local](./media/concepts-hyper-v-to-secondary-architecture/arch-onprem-onprem.png)

## <a name="replication-process"></a>Processo de replicação

1. Quando a replicação inicial é acionada, um [instantâneo VM de Hyper-V](https://technet.microsoft.com/library/dd560637.aspx) instantâneo é tirado.
2. Os discos rígidos virtuais na VM são replicados um de cada, para a localização secundária.
3. Se ocorrerem alterações de disco enquanto a replicação inicial está em curso, 
4. Quando concluir a replicação inicial, começa a replicação de diferenças. O controlador de replicação de réplica do Hyper-V controla as alterações como registos de replicação de Hyper-V (. hrl). Estes ficheiros de registo estão localizados na mesma pasta que os discos. Cada disco tem um ficheiro. hrl associado que é enviado para a localização secundária. Os ficheiros de instantâneo e de registo consomem recursos do disco quando a replicação inicial está em curso.
5. As alterações de disco delta no registo são sincronizadas e unidas ao disco principal.
6. 

## <a name="failover-and-failback-process"></a>Processo de ativação pós-falha e de reativação pós-falha

1. Pode efetuar a ativação pós-falha de um único computador ou, criar planos de recuperação, para orquestrar a ativação pós-falha de várias máquinas.
2. Pode executar uma ativação pós-falha planeada ou não planeada entre sites no local. Se executar uma ativação pós-falha planeada, as VMs de origem são desligadas para garantir que não há perda de dados.
    - Se efetuar uma ativação pós-falha não planeada para um site secundário, depois das máquinas de ativação pós-falha na localização secundária não estão protegidas.
    - Se tiver executado uma ativação pós-falha planeada, as máquinas na localização secundária estão protegidas após a ativação pós-falha.
3. Após a ativação pós-falha inicial é executada, a consolidação-lo, para iniciar a aceder a carga de trabalho da VM de réplica.

Quando a localização principal esteja novamente disponível, pode efetuar a cópia.

1. Iniciar a replicação inversa, iniciar a replicação do site secundário para o site primário. A replicação inversa coloca as máquinas virtuais num estado protegido, mas a localização ativa continua a ser o datacenter secundário.
2. Para que o site primário volte a ser a localização ativa, inicie uma ativação pós-falha planeada do site secundário para o primário, seguida de outra replicação inversa.



## <a name="next-steps"></a>Passos seguintes

Rever a matriz de suporte siga o tutorial para ativar a replicação de Hyper-V entre nuvens do VMM.
Execute uma ativação pós-falha e a reativação pós-falha.

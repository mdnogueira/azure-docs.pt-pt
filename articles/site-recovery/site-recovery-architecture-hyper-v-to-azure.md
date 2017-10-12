---
title: "Como funciona a replicação de Hyper-V para o Azure no Azure Site Recovery? | Microsoft Docs"
description: "Este artigo apresenta uma descrição geral dos componentes e da arquitetura utilizada ao replicar VMs Hyper-V no local para o Azure com o serviço Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: c413efcd-d750-4b22-b34b-15bcaa03934a
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 06/23/2017
ms.author: raynew
ms.openlocfilehash: 28f775afaf72b11eec0c22f755e4dbd6a485c895
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-does-hyper-v-replication-to-azure-work-in-site-recovery"></a>Como funciona a replicação de Hyper-V para o Azure no Site Recovery?


Este artigo descreve os componentes e os processos envolvidos ao replicar máquinas virtuais Hyper-V no Azure com o serviço [Azure Site Recovery](site-recovery-overview.md).

O Site Recovery consegue replicar VMs Hyper-V em clusters Hyper-V e anfitriões autónomos que são geridos com ou sem o System Center Virtual Machine Manager (VMM).

Publique comentários na parte inferior deste artigo ou no [Fórum dos Serviços de Recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).



## <a name="architectural-components"></a>Componentes da arquitetura

Existem vários componentes envolvidos ao replicar VMs Hyper-V para o Azure.

**Componente** | **Localização** | **Detalhes**
--- | --- | ---
**Azure** | No Azure, precisa de uma conta do Microsoft Azure, de uma conta de armazenamento do Azure e de uma rede do Azure. | Os dados replicados são armazenados na conta de armazenamento e as VMs do Azure são criadas com os dados replicados quando ocorre a ativação pós-falha a partir do site no local.<br/><br/> As VMs do Azure ligam-se à rede virtual do Azure quando são criadas.
**Servidor VMM** | Os anfitriões Hyper-V encontram-se em clouds do VMM | Se os anfitriões Hyper-V forem geridos em clouds do VMM, pode registar o servidor VMM no cofre dos Serviços de Recuperação.<br/><br/> No servidor VMM, pode instalar o Fornecedor do Site Recovery para orquestrar a replicação com o Azure.<br/><br/> Precisa de redes lógicas e de VM configuradas para configurar o mapeamento da rede. Uma rede VM deve ser ligada a uma rede lógica que está associada à nuvem.
**Anfitrião Hyper-V** | Os anfitriões e os clusters Hyper-V podem ser implementados com ou sem o servidor VMM. | Se não existir nenhum servidor VMM, o Fornecedor do Site Recovery é instalado no anfitrião para orquestrar a replicação com o Site Recovery através da Internet. Se existir um servidor VMM, o fornecedor é instalado no mesmo, e não no anfitrião.<br/><br/> O agente dos Serviços de Recuperação é instalado no anfitrião para processar a replicação de dados.<br/><br/> As comunicações provenientes do Fornecedor e do agente são seguras e encriptadas. Também são encriptados os dados replicados no armazenamento do Azure.
**VMs de Hyper-V** | Precisa de uma ou mais VMs em execução num servidor de anfitrião Hyper-V. | Nada tem de estar explicitamente instalado nas VMs.

Saiba mais sobre os pré-requisitos de implementação e os requisitos para cada um destes componentes na [matriz de suporte](site-recovery-support-matrix-to-azure.md).

**Figura 1: Replicação de site Hyper-V para o Azure**

![Componentes](./media/site-recovery-components/arch-onprem-azure-hypervsite.png)

**Figura 2: Replicação de Hyper-V em clouds do VMM para o Azure**

![Componentes](./media/site-recovery-components/arch-onprem-onprem-azure-vmm.png)


## <a name="replication-process"></a>Processo de replicação

**Figura 3: Processo de replicação e de recuperação da replicação do Hyper-V para o Azure**

![fluxo de trabalho](./media/site-recovery-components/arch-hyperv-azure-workflow.png)

### <a name="enable-protection"></a>Ativar a proteção

1. Depois de ativar a proteção para uma VM Hyper-V, no portal do Azure ou no local, **Ativar a proteção** é iniciado.
2. A tarefa verifica se a máquina está em conformidade com os pré-requisitos, antes de invocar o [CreateReplicationRelationship](https://msdn.microsoft.com/library/hh850036.aspx), para configurar a replicação com as definições que configurou.
3. A tarefa inicia a replicação inicial ao invocar o método [StartReplication](https://msdn.microsoft.com/library/hh850303.aspx), para inicializar uma replicação de VM completa e enviar os discos virtuais da VM para o Azure.
4. Pode monitorizar a tarefa no separador **Tarefas**.      ![Lista de tarefas](media/site-recovery-hyper-v-azure-architecture/image1.png)![Ativar a desagregação da proteção](media/site-recovery-hyper-v-azure-architecture/image2.png)

### <a name="replicate-the-initial-data"></a>Replicar os dados iniciais

1. Um [instantâneo de VM Hyper-V](https://technet.microsoft.com/library/dd560637.aspx) é tirado quando a replicação inicial é acionada.
2. Os discos rígidos virtuais são replicados, um de cada vez, até serem todos copiados para o Azure. Pode demorar algum tempo, consoante o tamanho da VM e a largura de banda da rede. Para otimizar a utilização da rede, veja [Como gerir a utilização de largura de banda da rede de proteção no local do Azure](https://support.microsoft.com/kb/3056159).
3. Se ocorrerem alterações de disco enquanto a replicação inicial estiver em curso, o Controlador de Replicação de Réplica do Hyper-V controla essas alterações como Registos de Replicação do Hyper-V (.hrl). Estes ficheiros estão localizados na mesma pasta que os discos. Cada disco tem um ficheiro de .hrl associado que será enviado para o armazenamento secundário.
4. Os ficheiros de instantâneo e de registo consomem recursos do disco quando a replicação inicial está em curso.
5. Quando a replicação inicial for concluída, o instantâneo da VM é eliminado. As alterações de disco delta no registo são sincronizadas e unidas ao disco principal.


### <a name="finalize-protection"></a>Finalizar a proteção

1. Após a conclusão da replicação inicial, a tarefa **Finalizar proteção na máquina virtual** configura a rede e outras definições de pós-replicação, para que a máquina virtual fique protegida.
    ![Finalizar a tarefa de proteção](media/site-recovery-hyper-v-azure-architecture/image3.png)
2. Se está a replicar para o Azure, poderá ter de otimizar as definições para a máquina virtual para que fique preparada para a ativação pós-falha. Nesta altura, pode executar uma ativação pós-falha de teste para verificar que tudo está a funcionar conforme esperado.

### <a name="replicate-the-delta"></a>Replicar o delta

1. Depois da replicação inicial, começa a sincronização delta, de acordo com as definições de replicação.
2. O Controlador de Replicação de Réplica do Hyper-V verifica as alterações efetuadas num disco rígido virtual como ficheiros .hrl. Cada disco que está configurado para replicação tem um ficheiro .hrl associado. Este registo é enviado para a conta de armazenamento do cliente depois de concluída a replicação inicial. Quando um registo está em trânsito para o Azure, as alterações ao disco principal são controladas noutro ficheiro de registo no mesmo diretório.
3. Durante a replicação inicial e delta, pode monitorizar a VM na vista de VM. [Saiba mais](site-recovery-monitoring-and-troubleshooting.md#monitor-replication-health-for-virtual-machines).  

### <a name="synchronize-replication"></a>Sincronizar replicação

1. Se a replicação delta falhar, e uma replicação completa seria dispendiosa em termos de largura de banda ou de tempo, então uma VM fica marcada para ressincronização. Por exemplo, se os ficheiros de .hrl atingirem 50% do tamanho do disco, a VM será marcada para ressincronização.
2.  A ressincronização minimiza a quantidade de dados enviados por computação de somas de verificação das máquinas virtuais de origem e de destino, enviando apenas dados delta. A ressincronização utiliza um algoritmo de segmentação com blocos fixos, em que os ficheiros de origem e de destino estão divididos em segmentos fixos. As somas de verificação para cada segmento são geradas e, em seguida, são comparadas para determinar os blocos da origem que devem ser aplicados ao destino.
3. Após a conclusão da ressincronização, a replicação delta normal deve ser retomada. Por predefinição, a ressincronização está agendada para ser executada automaticamente fora do horário de expediente, mas pode ressincronizar manualmente uma máquina virtual. Por exemplo, pode retomar a ressincronização se ocorrer uma indisponibilidade de rede ou de outro tipo. Para tal, selecione a VM no portal > **Ressincronizar**.

    ![Ressincronização manual](media/site-recovery-hyper-v-azure-architecture/image4.png)


### <a name="retry-logic"></a>Repetir a lógica

Se ocorrer um erro de replicação, haverá uma repetição interna. Esta lógica pode ser classificada em duas categorias:

**Categoria** | **Detalhes**
--- | ---
**Erros não recuperáveis** | Não é efetuada qualquer tentativa. O estado da VM será **Crítico**, e é necessária a intervenção do administrador. Exemplos destes erros incluem: rutura da cadeia VHD; estado inválido da VM de réplica; erros de autenticação de rede: erros de autorização; a VM não encontrou erros (para os servidores Hyper-V autónomos)
**Erros recuperáveis** | As tentativas ocorrem a cada intervalo de replicação, utilizando um término exponencial que aumenta o intervalo entre tentativas, desde o início da primeira tentativa para 1, 2, 4, 8 e 10 minutos. Se o erro persistir, tente novamente a cada 30 minutos. Os exemplos incluem: erros de rede; erros de espaço insuficiente no disco; condições de falta de memória |



## <a name="failover-and-failback-process"></a>Processo de ativação pós-falha e de reativação pós-falha

1. Executa uma [ativação pós-falha](site-recovery-failover.md) planeada ou não planeada a partir das VMs de Hyper-V no local para o Azure. Se executar uma ativação pós-falha planeada, as VMs de origem são desligadas para garantir que não há perda de dados.
2. Pode fazer a ativação pós-falha de uma máquina individual ou criar [planos de recuperação](site-recovery-create-recovery-plans.md) para orquestrar a ativação pós-falha de várias máquinas.
4. Depois de executar a ativação pós-falha, deverá conseguir ver as VMs de réplica criadas no Azure. Pode atribuir um endereço IP público à VM, se necessário.
5. Em seguida, consolida a ativação pós-falha para começar a aceder à carga de trabalho da VM do Azure de réplica.
6. Quando o site no local primário estiver novamente disponível, pode fazer a [reativação pós-falha](site-recovery-failback-from-azure-to-hyper-v.md). Arranca uma ativação pós-falha planeada do Azure para o site primário. Nas ativações pós-falha planeadas, pode optar por ativar para a mesma VM ou para uma localização alternativa, bem como sincronizar as alterações entre o Azure e o site no local, para garantir que não se perdem dados. Quando as VMs são criadas no local, é consolidada a ativação pós-falha.




## <a name="next-steps"></a>Passos seguintes

Analise a [matriz de suporte](site-recovery-support-matrix-to-azure.md)

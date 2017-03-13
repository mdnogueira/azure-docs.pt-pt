---
title: "Como funciona a replicação de Hyper-V para o Azure no Site Recovery? | Microsoft Docs"
description: "Este artigo fornece uma descrição geral de como funciona a replicação de Hyper-V no Azure Site Recovery"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: 
ms.assetid: c413efcd-d750-4b22-b34b-15bcaa03934a
ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/05/2017
ms.author: raynew
translationtype: Human Translation
ms.sourcegitcommit: d9dad6cff80c1f6ac206e7fa3184ce037900fc6b
ms.openlocfilehash: 9adf266c6a2ac00c3aaa34e2a29aefe34abe2871
ms.lasthandoff: 03/06/2017


---
# <a name="how-does-hyper-v-replication-to-azure-work"></a>Como funciona a replicação de Hyper-V para o Azure?

Leia este artigo para compreender a arquitetura e os fluxos de trabalho para replicação de Hyper-V para o Azure com o serviço [Azure Site Recovery](site-recovery-overview.md).

Publique comentários na parte inferior deste artigo ou no [Fórum dos Serviços de Recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

Pode replicar o que se segue para o Azure:
- **Hyper-V com o VMM**: as VMs localizadas em anfitriões Hyper-V no local geridos em clouds do System Center Virtual Machine Manager (VMM). Os anfitriões podem executar qualquer [sistema operativo suportado](site-recovery-support-matrix-to-azure.md#support-for-datacenter-management-servers). Pode replicar VMs Hyper-V que executem qualquer sistema operativo convidado [suportado pelo Hyper-V e o Azure](https://technet.microsoft.com/en-us/windows-server-docs/compute/hyper-v/supported-windows-guest-operating-systems-for-hyper-v-on-windows).
- **Hyper-V sem o VMM**: as VMs no local localizadas em anfitriões Hyper-V que não são geridos em clouds do VMM. Os anfitriões podem executar qualquer um dos [sistemas operativos suportados](site-recovery-support-matrix-to-azure.md#support-for-replicated-machine-os-versions). Pode replicar VMs Hyper-V que executem qualquer sistema operativo convidado [suportado pelo Hyper-V e o Azure](https://technet.microsoft.com/en-us/windows-server-docs/compute/hyper-v/supported-windows-guest-operating-systems-for-hyper-v-on-windows).


## <a name="architectural-components"></a>Componentes da arquitetura

**Área** | **Componente** | **Detalhes**
--- | --- | ---
**Azure** | No Azure, precisa de uma conta do Microsoft Azure, de uma conta de armazenamento do Azure e de uma rede do Azure. | O armazenamento e a rede podem ser contas baseadas no Resource Manager ou contas clássicas.<br/><br/> Os dados replicados são armazenados na conta de armazenamento e as VMs do Azure são criadas com os dados replicados quando ocorre a ativação pós-falha a partir do site no local.<br/><br/> As VMs do Azure ligam-se à rede virtual do Azure quando são criadas.
**Servidor VMM** | Anfitriões de Hyper-V localizados em clouds do VMM | Se os anfitriões Hyper-V forem geridos em clouds do VMM, pode registar o servidor VMM no cofre dos Serviços de Recuperação.<br/><br/> No servidor VMM, pode instalar o Fornecedor do Site Recovery para orquestrar a replicação com o Azure.<br/><br/> Precisa de redes lógicas e de VM configuradas para configurar o mapeamento da rede. Uma rede VM deve ser ligada a uma rede lógica que está associada à nuvem.
**Anfitrião Hyper-V** | Os servidores Hyper-V podem ser implementados com ou sem o servidor VMM. | Se não existir nenhum servidor VMM, o Fornecedor do Site Recovery é instalado no anfitrião para orquestrar a replicação com o Site Recovery através da Internet. Se existir um servidor VMM, o fornecedor é instalado no mesmo, e não no anfitrião.<br/><br/> O agente dos Serviços de Recuperação é instalado no anfitrião para processar a replicação de dados.<br/><br/> As comunicações provenientes do Fornecedor e do agente são seguras e encriptadas. Também são encriptados os dados replicados no armazenamento do Azure.
**VMs de Hyper-V** | Precisa de uma ou mais VMs no servidor de anfitrião Hyper-V. | Nada tem de estar explicitamente instalado nas VMs

## <a name="deployment-steps"></a>Passos da implementação

1. **Azure**: são configurados os componentes do Azure. Recomendamos que configure as contas de armazenamento e de rede antes de iniciar a implementação do Site Recovery.
2. **Cofre**: é criado um cofre dos Serviços de Recuperação para o Site Recovery e são configuradas as definições do cofre, incluindo a configuração das definições de origem e de destino, a configuração de uma política de replicação e a ativação da replicação.
3. **Origem e destino**:
    - **Anfitriões Hyper-V em clouds do VMM**: como parte da especificação de definições de origem, é transferido e instalado o Fornecedor do Azure Site Recovery no servidor VMM e o agente dos Serviços de Recuperação do Microsoft Azure em cada anfitrião Hyper-V. A origem será o servidor VMM. O destino é o Azure.
    - Anfitriões Hyper-V sem o VMM: quando especifica definições de origem, é transferido e instalado o Fornecedor e o agente em cada anfitrião Hyper-V. Durante a implementação, são reunidos os anfitriões para um site Hyper-V, e este site é especificado como a origem. O destino é o Azure.

    ![Replicação de Hyper-V/VMM para o Azure](./media/site-recovery-components/arch-onprem-onprem-azure-vmm.png)
    ![Replicação de site Hyper-V para o Azure](./media/site-recovery-components/arch-onprem-azure-hypervsite.png)


4. **Política de replicação**: cria uma política de replicação para o site Hyper-V ou para a cloud do VMM. A política é aplicada a todas as VMs localizadas nos anfitriões no site ou na cloud.
5. **Ativar a replicação**: ativa a replicação nas VMs Hyper-V. A replicação inicial ocorre de acordo com as definições da política de replicação. As alterações aos dados são registadas e a replicação das alterações delta para o Azure começa depois de concluída a replicação inicial. As alterações registadas relativas a um item são guardadas num ficheiro .hrl.
6. **Ativação pós-falha de teste**: é executada uma ativação pós-falha de teste, para confirmar que está tudo a funcionar conforme esperado.

Saiba mais sobre a implementação:
- [Introdução à replicação de VMs Hyper-V para o Azure - com o VMM](site-recovery-vmm-to-azure.md)
- [Introdução à replicação de VMs Hyper-V para o Azure - sem o VMM](site-recovery-hyper-v-site-to-azure.md)

## <a name="hyper-v-replication-workflow"></a>Fluxo de trabalho da replicação de Hyper-V

### <a name="enable-protection"></a>Ativar a proteção

1. Depois de ativar a proteção para uma VM Hyper-V, no portal do Azure ou no local, **Ativar a proteção** é iniciado.
2. A tarefa verifica se a máquina está em conformidade com os pré-requisitos, antes de invocar o [CreateReplicationRelationship](https://msdn.microsoft.com/library/hh850036.aspx), para configurar a replicação com as definições que configurou.
3. A tarefa inicia a replicação inicial ao invocar o método [StartReplication](https://msdn.microsoft.com/library/hh850303.aspx), para inicializar uma replicação de VM completa e enviar os discos virtuais da VM para o Azure.
4. Pode monitorizar a tarefa no separador **Tarefas**.
        ![Lista de tarefas](media/site-recovery-hyper-v-azure-architecture/image1.png)
        ![Ativar a desagregação da proteção](media/site-recovery-hyper-v-azure-architecture/image2.png)

### <a name="initial-replication"></a>Replicação inicial

1. Um [instantâneo de VM Hyper-V](https://technet.microsoft.com/library/dd560637.aspx) é tirado quando a replicação inicial é acionada.
2. Os discos rígidos virtuais são replicados, um de cada vez, até serem todos copiados para o Azure. Pode demorar algum tempo, consoante o tamanho da VM e a largura de banda da rede. Para otimizar a utilização da rede, veja [Como gerir a utilização de largura de banda da rede de proteção no local do Azure](https://support.microsoft.com/kb/3056159).
3. Se ocorrerem alterações de disco enquanto a replicação inicial estiver em curso, o Controlador de Replicação de Réplica do Hyper-V controla essas alterações como Registos de Replicação do Hyper-V (.hrl). Estes ficheiros estão localizados na mesma pasta que os discos. Cada disco tem um ficheiro de .hrl associado que será enviado para o armazenamento secundário.
4. Os ficheiros de instantâneo e de registo consomem recursos do disco quando a replicação inicial está em curso.
5. Quando a replicação inicial for concluída, o instantâneo da VM é eliminado. As alterações de disco delta no registo são sincronizadas e unidas ao disco principal.


### <a name="finalize-protection"></a>Finalizar a proteção

1. Após a conclusão da replicação inicial, a tarefa **Finalizar proteção na máquina virtual** configura a rede e outras definições de pós-replicação, para que a máquina virtual fique protegida.
    ![Finalizar a tarefa de proteção](media/site-recovery-hyper-v-azure-architecture/image3.png)
2. Se está a replicar para o Azure, poderá ter de otimizar as definições para a máquina virtual para que fique preparada para a ativação pós-falha. Nesta altura, pode executar uma ativação pós-falha de teste para verificar que tudo está a funcionar conforme esperado.

### <a name="delta-replication"></a>Replicação delta

1. Depois da replicação inicial, começa a sincronização delta, de acordo com as definições de replicação.
2. O Controlador de Replicação de Réplica do Hyper-V verifica as alterações efetuadas num disco rígido virtual como ficheiros .hrl. Cada disco que está configurado para replicação tem um ficheiro .hrl associado. Este registo é enviado para a conta de armazenamento do cliente depois de concluída a replicação inicial. Quando um registo está em trânsito para o Azure, as alterações ao disco principal são controladas noutro ficheiro de registo no mesmo diretório.
3. Durante a replicação inicial e delta, pode monitorizar a VM na vista de VM. [Saiba mais](site-recovery-monitoring-and-troubleshooting.md#monitor-replication-health-for-virtual-machines).  

### <a name="replication-synchronization"></a>Sincronização da replicação

1. Se a replicação delta falhar, e uma replicação completa seria dispendiosa em termos de largura de banda ou de tempo, então uma VM fica marcada para ressincronização. Por exemplo, se os ficheiros de .hrl atingirem 50% do tamanho do disco, a VM será marcada para ressincronização.
2.  A ressincronização minimiza a quantidade de dados enviados por computação de somas de verificação das máquinas virtuais de origem e de destino, enviando apenas dados delta. A ressincronização utiliza um algoritmo de segmentação com blocos fixos, em que os ficheiros de origem e de destino estão divididos em segmentos fixos. As somas de verificação para cada segmento são geradas e, em seguida, são comparadas para determinar os blocos da origem que devem ser aplicados ao destino.
3. Após a conclusão da ressincronização, a replicação delta normal deve ser retomada. Por predefinição, a ressincronização está agendada para ser executada automaticamente fora do horário de expediente, mas pode ressincronizar manualmente uma máquina virtual. Por exemplo, pode retomar a ressincronização se ocorrer uma indisponibilidade de rede ou de outro tipo. Para tal, selecione a VM no portal > **Ressincronizar**.

    ![Ressincronização manual](media/site-recovery-hyper-v-azure-architecture/image4.png)


### <a name="retries"></a>Tentativas

Se ocorrer um erro de replicação, haverá uma repetição interna. Esta lógica pode ser classificada em duas categorias:

**Categoria** | **Detalhes**
--- | ---
**Erros não recuperáveis** | Não é efetuada qualquer tentativa. O estado da VM será **Crítico**, e é necessária a intervenção do administrador. Exemplos destes erros incluem: rutura da cadeia VHD; estado inválido da VM de réplica; erros de autenticação de rede: erros de autorização; a VM não encontrou erros (para os servidores Hyper-V autónomos)
**Erros recuperáveis** | As tentativas ocorrem a cada intervalo de replicação, utilizando um término exponencial que aumenta o intervalo entre tentativas, desde o início da primeira tentativa para 1, 2, 4, 8 e 10 minutos. Se o erro persistir, tente novamente a cada 30 minutos. Os exemplos incluem: erros de rede; erros de espaço insuficiente no disco; condições de falta de memória |

## <a name="protection-and-recovery-lifecycle"></a>Ciclo de vida de proteção e recuperação

![fluxo de trabalho](./media/site-recovery-components/arch-hyperv-azure-workflow.png)

## <a name="next-steps"></a>Passos seguintes

- [Verificar pré-requisitos da implementação](site-recovery-prereq.md)
- Resolver problemas com:
    - [Monitorizar e resolver problemas de proteção](site-recovery-monitoring-and-troubleshooting.md)
    - [Ajuda do Suporte da Microsoft](site-recovery-monitoring-and-troubleshooting.md#reach-out-for-microsoft-support)
    - [Erros comuns e resoluções](site-recovery-monitoring-and-troubleshooting.md#common-azure-site-recovery-errors-and-their-resolutions)


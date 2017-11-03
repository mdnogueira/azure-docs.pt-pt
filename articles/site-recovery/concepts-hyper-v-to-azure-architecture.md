---
title: "Reveja a arquitetura da replicação de Hyper-V para o Azure com o Azure Site Recovery | Microsoft Docs"
description: "Este artigo apresenta uma descrição geral dos componentes e da arquitetura utilizada ao replicar VMs Hyper-V (sem o VMM) no local para o Azure com o serviço Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 737cd30b-3994-4b18-9bd4-78c723601310
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/10/2017
ms.author: raynew
ms.openlocfilehash: 991c72352eaa4c3b12fcdc1e4112063fb698e772
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="hyper-v-to-azure-replication-architecture"></a>Hyper-V com a arquitetura de replicação do Azure


Este artigo descreve a arquitetura e processos utilizados quando replicar, efetuar a ativação pós-falha e recuperar máquinas de virtuais de Hyper-V (VMs) entre anfitriões de Hyper-V no local e o Azure, utilizando o [do Azure Site Recovery](site-recovery-overview.md) serviço.

Anfitriões Hyper-V, opcionalmente, podem ser geridos em nuvens privadas do System Center Virtual Machine Manager (VMM).



## <a name="architectural-components---hyper-v-without-vmm"></a>Componentes da arquitetura - Hyper-V sem o VMM

A seguinte tabela e o gráfico fornecem uma vista detalhada dos componentes utilizados para replicação de Hyper-V para o Azure, quando os anfitriões Hyper-V não são geridas pelo VMM.

**Componente** | **Requisito** | **Detalhes**
--- | --- | ---
**Azure** | Uma subscrição do Azure, uma conta de armazenamento do Azure e uma rede do Azure. | Os dados replicados de cargas de trabalho VM de locais são armazenados na conta de armazenamento. As VMs do Azure são criadas com os dados replicados de carga de trabalho quando ocorre a ativação pós-falha do seu site no local.<br/><br/> As VMs do Azure ligam-se à rede virtual do Azure quando são criadas.
**Hyper-V** | Durante a implementação da recuperação de sites, recolha anfitriões e clusters Hyper-V para sites Hyper-V. Instale o fornecedor do Azure Site Recovery e agente dos serviços de recuperação em cada máquina Hyper-V. | O Fornecedor orquestra a replicação com o Site Recovery através da Internet. O agente do Site Recovery trata da replicação de dados.<br/><br/> As comunicações provenientes do Fornecedor e do agente são seguras e encriptadas. Também são encriptados os dados replicados no armazenamento do Azure.
**VMs de Hyper-V** | Uma ou mais VMs em execução no Hyper-V. | Nada tem de ser explicitamente instalados em VMs.


**Hyper-V com a arquitetura do Azure (sem VMM)**

![Arquitetura](./media/concepts-hyper-v-to-azure-architecture/arch-onprem-azure-hypervsite.png)



## <a name="architectural-components---hyper-v-with-vmm"></a>Componentes da arquitetura - Hyper-V com o VMM

A seguinte tabela e o gráfico fornecem uma vista detalhada dos componentes utilizados para replicação de Hyper-V para o Azure, quando os anfitriões Hyper-V geridos em nuvens VMM.

**Componente** | **Requisito** | **Detalhes**
--- | --- | ---
**Azure** | Uma subscrição do Azure, uma conta de armazenamento do Azure e uma rede do Azure. | Os dados replicados de cargas de trabalho VM de locais são armazenados na conta de armazenamento. As VMs do Azure são criadas com os dados replicados quando ocorre a ativação pós-falha do seu site no local.<br/><br/> As VMs do Azure ligam-se à rede virtual do Azure quando são criadas.
**Servidor VMM** | O servidor VMM tem uma ou mais clouds que contêm anfitriões Hyper-V. | Instale o fornecedor de recuperação de Site no servidor do VMM, para orquestrar a replicação com a recuperação de Site e registar o servidor no cofre dos serviços de recuperação.
**Anfitrião Hyper-V** | Um ou mais anfitriões/clusters de Hyper-V geridos pelo VMM. |  Instale o agente dos Serviços de Recuperação em cada anfitrião ou membro do cluster.
**VMs de Hyper-V** | Uma ou mais VMs em execução num servidor de anfitrião Hyper-V. | Nada tem de estar explicitamente instalado nas VMs.
**Redes** | Redes lógicas e de VMs configuradas no servidor VMM. A rede VM deve ser ligada a uma rede lógica que tiver associado à nuvem. | Redes VM estão mapeadas para redes virtuais do Azure. Quando as VMs do Azure são criadas após a ativação pós-falha, eles são adicionados à rede do Azure que está mapeada para a rede VM.

**Hyper-V com a arquitetura do Azure (com o VMM)**

![Componentes](./media/concepts-hyper-v-to-azure-architecture/arch-onprem-onprem-azure-vmm.png)



## <a name="replication-process"></a>Processo de replicação

![Hyper-V para replicação do Azure](./media/concepts-hyper-v-to-azure-architecture/arch-hyperv-azure-workflow.png)

**Processo de replicação e recuperação**


### <a name="enable-protection"></a>Ativar a proteção

1. Depois de ativar a proteção para uma VM Hyper-V, no portal do Azure ou no local, **Ativar a proteção** é iniciado.
2. A tarefa verifica se a máquina está em conformidade com os pré-requisitos, antes de invocar o [CreateReplicationRelationship](https://msdn.microsoft.com/library/hh850036.aspx), para configurar a replicação com as definições que configurou.
3. A tarefa inicia a replicação inicial ao invocar o método [StartReplication](https://msdn.microsoft.com/library/hh850303.aspx), para inicializar uma replicação de VM completa e enviar os discos virtuais da VM para o Azure.
4. Pode monitorizar a tarefa no separador **Tarefas**.      ![Lista de tarefas](media/concepts-hyper-v-to-azure-architecture/image1.png)![Ativar a desagregação da proteção](media/concepts-hyper-v-to-azure-architecture/image2.png)


### <a name="initial-data-replication"></a>Replicação inicial de dados

1. Quando a replicação inicial é acionada, um [instantâneo VM de Hyper-V](https://technet.microsoft.com/library/dd560637.aspx) instantâneo é tirado.
2. Os discos rígidos virtuais na VM são replicados um de cada, até estarem todos copiados para o Azure. Isto poderá demorar algum tempo, dependendo do tamanho VM e largura de banda de rede. [Saiba como](https://support.microsoft.com/kb/3056159) para aumentar a largura de banda de rede.
3. Se ocorrerem alterações de disco enquanto a replicação inicial está em curso, o controlador de replicação de réplica do Hyper-V controla as alterações como registos de replicação de Hyper-V (. hrl). Estes ficheiros de registo estão localizados na mesma pasta que os discos. Cada disco tem um ficheiro. hrl associado que é enviado para o armazenamento secundário. Os ficheiros de instantâneo e de registo consomem recursos do disco quando a replicação inicial está em curso.
4. Quando a replicação inicial for concluída, o instantâneo da VM é eliminado.
5. As alterações de disco delta no registo são sincronizadas e unidas ao disco principal.


### <a name="finalize-protection-process"></a>Finalizar o processo de proteção

1. Depois de concluída a replicação inicial, o **finalizar proteção na máquina virtual** tarefa é executada. Configura rede e outras definições de pós-replicação para que a VM está protegida.
2. Nesta fase, pode verificar as definições de VM para se certificar de que está preparada para ativação pós-falha. Pode executar um exercício de recuperação após desastre (ativação pós-falha de teste) para a VM, para verificar se não for mais conforme esperado. 


## <a name="delta-replication"></a>Replicação delta

1. Após a replicação inicial, começa a replicação delta, de acordo com a política de replicação.
2. O controlador de replicação de réplica do Hyper-V regista as alterações para um disco rígido virtual, como ficheiros. hrl. Cada disco que está configurado para replicação tem um ficheiro .hrl associado.
3. O registo é enviado para a conta de armazenamento do cliente. Quando um registo se encontram em trânsito para o Azure, as alterações ao disco principal são registadas no outro ficheiro de registo, na mesma pasta.
4. Durante a replicação inicial e diferenças, pode monitorizar a VM no portal do Azure.

### <a name="resynchronization-process"></a>Processo de ressincronização

1. Se a replicação delta falhar, e uma replicação completa seria dispendiosa em termos de largura de banda ou de tempo, então uma VM fica marcada para ressincronização.
    - Por exemplo, se os ficheiros de .hrl atingirem 50% do tamanho do disco, a VM será marcada para ressincronização.
    -  Por predefinição, a ressincronização está agendada para ser executada automaticamente fora do horário.
1.  A ressincronização envia apenas dados de diferenças.
    - Minimiza a quantidade de dados enviados por computação de somas de verificação das VMs de origem e de destino.
    - Utiliza um algoritmo de segmentação de bloco fixo onde os ficheiros de origem e de destino são divididos em segmentos fixos.
    - São geradas somas de verificação para cada segmento. Estes são comparadas para determinar os blocos da origem tem de ser aplicados ao destino.
2. Após a conclusão da ressincronização, a replicação delta normal deve ser retomada.
3. Se não pretender aguardar ressincronização predefinido fora do horário, pode ressincronizar manualmente uma VM. Por exemplo, se ocorrer uma falha. Para tal, no portal do Azure, selecione a VM > **ressincronizar**.

    ![Ressincronização manual](./media/concepts-hyper-v-to-azure-architecture/image4-site.png)


### <a name="retry-process"></a>Repita o processo

Se ocorrer um erro de replicação, haverá uma repetição interna. Repetir é classificado como descrito na tabela.

**Categoria** | **Detalhes**
--- | ---
**Erros não recuperáveis** | Não é efetuada qualquer tentativa. O estado da VM será **Crítico**, e é necessária a intervenção do administrador.<br/><br/> Exemplos destes erros incluir uma cadeia VHD quebrada, um Estado inválido para a VM, erros de autenticação de rede, erros de autorização, de réplica e VM erros não encontrado (para servidores autónomos do Hyper-V.
**Erros recuperáveis** | As tentativas ocorrem a cada intervalo de replicação, utilizando um término exponencial que aumenta o intervalo entre tentativas, desde o início da primeira tentativa para 1, 2, 4, 8 e 10 minutos. Se o erro persistir, tente novamente a cada 30 minutos. Estes exemplos incluem erros de rede, os erros de espaço em disco insuficiente e condições de falta de memória.



## <a name="failover-and-failback-process"></a>Processo de ativação pós-falha e de reativação pós-falha

1. Pode executar uma ativação pós-falha planeada ou não planeada de VMs de Hyper-V no local para o Azure. Se executar uma ativação pós-falha planeada, as VMs de origem são desligadas para garantir que não há perda de dados. Se o site primário não estiver acessível, execute uma ativação pós-falha não planeada.
2. Pode efetuar a ativação pós-falha de um único computador ou, criar planos de recuperação, para orquestrar a ativação pós-falha de várias máquinas.
3. Executar uma ativação pós-falha. Depois de concluir a primeira fase de ativação pós-falha, deverá conseguir ver a réplica de criação de VMs no Azure. Pode atribuir um endereço IP público à VM, se necessário.
4. Em seguida, consolidar ativação pós-falha para iniciar a aceder a carga de trabalho a partir da réplica VM do Azure.

Após a sua infraestrutura no local novamente em funcionamento, pode efetuar a cópia. Reativação pós-falha ocorre em três fases:

1. Iniciar uma ativação pós-falha planeada do Azure para o site no local:
    - **Minimizar o período de indisponibilidade**: Se utilizar esta opção de recuperação de Site sincroniza os dados antes da ativação pós-falha. Verifica a existência de blocos de dados alterados e transfere-os para o site no local, enquanto mantém a VM do Azure em execução, minimizar o período de indisponibilidade. Quando especificar manualmente que deve efetuar a ativação pós-falha, a VM do Azure está a ser encerrada, quaisquer alterações de final delta são copiadas e inicia a ativação pós-falha.
    - **Transferência completa**: com esta opção os dados estão sincronizados durante a ativação pós-falha. Esta opção transfere o disco completo. É mais rápida porque nenhum somas de verificação são calculadas, mas não há mais tempo de inatividade. Utilize esta opção se tiver sido a executar a réplica VMs do Azure durante algum tempo ou se a VM no local tiver sido eliminada.
    - **Criar a VM**:, pode optar por efetuar a mesma VM ou para uma VM alternativa. Pode especificar que a recuperação de Site deve criar a VM, se ainda não existir.

2. Após a conclusão da sincronização inicial, selecione para concluir a ativação pós-falha. Depois de terminar, pode iniciar sessão no VM no local para verificar que tudo está a funcionar conforme esperado. No portal do Azure, pode ver que as VMs do Azure foram paradas.
3.  Em seguida, confirme a ativação pós-falha para concluir a cópia de segurança e iniciar novamente aceder à carga de trabalho da VM no local.
4. Depois de cargas de trabalho falharam novamente, ativar a replicação inversa, para que as VMs no local novamente a replicar para o Azure.



## <a name="next-steps"></a>Passos seguintes

Rever a matriz de suporte siga o tutorial para ativar o Hyper-V para a replicação do Azure.
Execute uma ativação pós-falha e a reativação pós-falha.


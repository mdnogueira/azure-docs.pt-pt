---
title: "Como efetuar a reativação a partir do Azure para VMware | Microsoft Docs"
description: "Após a ativação pós-falha de máquinas virtuais do Azure, pode iniciar uma reativação pós-falha para colocar máquinas virtuais no local. Saiba os passos sobre como falhar novamente."
services: site-recovery
documentationcenter: 
author: ruturaj
manager: gauravd
editor: 
ms.assetid: 44813a48-c680-4581-a92e-cecc57cc3b1e
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 11/28/2017
ms.author: ruturajd
ms.openlocfilehash: ad424818f41e6b48e754dd0d39771248a1cd04fb
ms.sourcegitcommit: f847fcbf7f89405c1e2d327702cbd3f2399c4bc2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/28/2017
---
# <a name="fail-back-from-azure-to-an-on-premises-site"></a>Falhar novamente a partir do Azure para um site no local

Este artigo descreve como falhar back máquinas virtuais de máquinas virtuais do Azure para o site no local. Siga as instruções neste artigo para efetuar a fazer uma cópia a máquinas virtuais VMware ou o Windows/Linux em servidores físicos depois de ter pós-falha no local do site para o Azure com o [máquinas virtuais VMware replicar e servidores físicos para Azure com o Azure Site Recovery](site-recovery-vmware-to-azure-classic.md) tutorial.

> [!WARNING]
> Não é possível reativação pós-falha depois de ter um [concluído migração](site-recovery-migrate-to-azure.md#what-do-we-mean-by-migration), mover uma máquina virtual para outro grupo de recursos ou eliminar a máquina virtual do Azure. Se desativar a proteção da máquina virtual, não é possível reativação pós-falha.

> [!NOTE]
> Se tiver a executar a ativação pós-falha de máquinas virtuais VMware, em seguida, não é possível a reativação pós-falha num anfitrião Hyper-v.

## <a name="overview-of-failback"></a>Descrição geral da reativação pós-falha
Eis como funciona a reativação pós-falha. Depois de ter a pós-falha para o Azure, falhará para o site no local no alguns fases:

1. [Proteja](site-recovery-how-to-reprotect.md) as máquinas virtuais do Azure para que possam iniciar replicar máquinas virtuais de VMware no seu site no local. Como parte deste processo, terá também de:
    1. Configurar um destino principal no local: destino principal do Windows para máquinas virtuais Windows e [destino principal do Linux](site-recovery-how-to-install-linux-master-target.md) para máquinas virtuais do Linux.
    2. Configurar um [servidor de processos](site-recovery-vmware-setup-azure-ps-resource-manager.md).
    3. Iniciar [Proteja](site-recovery-how-to-reprotect.md). Isto irá desativar a máquina virtual no local e sincronizar os dados da máquina virtual do Azure com os discos no local.
5. Depois das máquinas virtuais no Azure estiver a replicar para o site no local, iniciar uma falha através do Azure para o site no local.

Depois dos dados falhou novamente, proteja máquinas virtuais no local que não foi possível voltar a, para que possam iniciar replicar no Azure.

Para uma descrição geral, veja o seguinte vídeo sobre como efetuar a ativação pós-falha do Azure para um site no local.
> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/VMware-to-Azure-with-ASR-Video5-Failback-from-Azure-to-On-premises/player]

### <a name="fail-back-to-the-original-or-alternate-location"></a>A localização original ou alternativa

Se tiver falhado através de uma máquina virtual VMware, pode falhar ao mesmo local máquina virtual de origem, caso ainda exista. Neste cenário, apenas as alterações são replicadas novamente. Este cenário é conhecido como a recuperação da localização original. Se a máquina virtual no local não existe, o cenário é uma recuperação numa localização alternativa.

> [!NOTE]
> Pode apenas reativação pós-falha para o servidor vCenter e a configuração original. Não é possível implementar um novo servidor de configuração e a reativação pós-falha utilizá-la. Além disso, não é possível adicionar um novo vCenter para o servidor de configuração de saída e a reativação pós-falha para o novo vCenter.

#### <a name="original-location-recovery"></a>Recuperação da localização original

Se falhar novamente para a máquina virtual original, as seguintes condições são necessárias:
* Se a máquina virtual é gerida por um vCenter server, o anfitrião do ESX o destino principal deve ter acesso ao arquivo de dados da máquina virtual.
* Se a máquina virtual está num anfitrião ESX, mas não é gerida pelo vCenter, em seguida, o disco rígido da máquina virtual deve estar no arquivo de dados que o anfitrião de destino principal pode aceder.
* Se a máquina virtual está num anfitrião do ESX e não utilize o vCenter, deve efetuar a deteção do anfitrião ESX de destino principal antes de a voltar a proteger. Isto aplica-se se está a reativar novamente físicas, demasiado.
* Pode efetuar para uma rede de área de armazenamento virtual (vSAN) ou um disco com base no dispositivo sem formato mapeamento (RDM) se os discos já existem e estão ligados à máquina virtual no local.

#### <a name="alternate-location-recovery"></a>Recuperação numa localização alternativa
Se a máquina virtual no local não existe antes de proteger novamente a máquina virtual, o cenário seja designado uma recuperação numa localização alternativa. O fluxo de trabalho de reproteção cria novamente a máquina virtual no local. Isto também fará com que uma transferência de dados completa.

* Quando falhar novamente para uma localização alternativa, a máquina virtual será recuperada para o mesmo anfitrião ESX no qual o servidor de destino principal está implementado. O arquivo de dados que é utilizado para criar o disco será o mesmo arquivo de dados que foi selecionado quando proteger novamente a máquina virtual.
* Pode efetuar a cópia apenas a um sistema de ficheiros de máquina virtual (VMFS) ou o arquivo de dados de vSAN. Se tiver um RDM, reproteção e a reativação pós-falha não irão funcionar.
* Reproteção envolve uma transferência de dados inicial grande que é seguida das alterações. Este processo existe porque a máquina virtual não existe no local. Os dados completos tem de ser replicadas de volta. Este reproteção também irá demorar mais tempo do que uma recuperação da localização original.
* Não pode falhar novamente para discos com base em RDM. Apenas novos discos da máquina virtual (VMDKs) podem ser criados num arquivo de dados VMFS/vSAN.

Um computador físico, quando a ativação pós-falha para o Azure, pode ser não conseguiu fazer uma cópia apenas como máquina virtual VMware (também referida como P2A2V). Este fluxo está sob a recuperação para localização alternativa.

* Um servidor físico do Windows Server 2008 R2 SP1, se protegido e efetuar a ativação pós-falha para o Azure, não é possível efetuar a novamente.
* Certifique-se de que o se detetar pelo menos um servidor de destino principal e os anfitriões ESX/ESXi necessários para o qual terá de voltar a falhar.

## <a name="have-you-completed-reprotection"></a>Concluiu o só?
Antes de continuar, conclua os passos de reproteção para que as máquinas virtuais estão num estado replicado, e pode iniciar uma ativação pós-falha para um site no local. Para obter mais informações, consulte [como voltar a proteger a partir do Azure no local](site-recovery-how-to-reprotect.md).

## <a name="prerequisites"></a>Pré-requisitos

> [!IMPORTANT]
> Durante a ativação pós-falha para o Azure, o site no local poderá não estar acessível e, por conseguinte, o servidor de configuração pode ser não disponível ou encerramento. Durante a reproteção e a reativação pós-falha, o servidor de configuração no local deve ser em execução e no estado OK ligado.

* É necessário um servidor de configuração no local quando fizer uma reativação pós-falha. O servidor deve estar no estado de execução e ligado ao serviço de forma a que o seu estado de funcionamento está OK. Durante a reativação pós-falha, a máquina virtual tem de existir na base de dados de servidor de configuração ou reativação pós-falha não será concluída com êxito. Assim, certifique-se de que demorar agendadas regularmente cópias de segurança do seu servidor. Se tiver ocorrido um desastre, terá de restaurar o servidor com o mesmo endereço IP para reativação pós-falha trabalhar.
* O servidor de destino mestre não deve ter todos os instantâneos antes de acionar reproteção/reativação pós-falha.

## <a name="steps-to-fail-back"></a>Passos para falhar novamente

> [!IMPORTANT]
> Antes de iniciar a reativação pós-falha, certifique-se de que concluiu que as máquinas virtuais. As máquinas virtuais devem estar num estado protegido e o respetivo estado de funcionamento deve ser **OK**. Para voltar a proteger as máquinas virtuais, leia [como voltar a proteger](site-recovery-how-to-reprotect.md).

1. Na página de itens replicados, selecione a máquina virtual e faça duplo clique nele para selecionar **ativação pós-falha não planeada**.
2. No **confirmar ativação pós-falha**, certifique-se a direção de ativação pós-falha (a partir do Azure) e, em seguida, selecione o ponto de recuperação (mais recente ou para a mais recente aplicação consistente) que pretende utilizar para a ativação pós-falha. O ponto consistente de aplicação é atrás do ponto mais recente no tempo e faz com que alguma perda de dados.
3. Durante a ativação pós-falha, a recuperação de Site encerra as máquinas virtuais no Azure. Depois de verificar que esse reativação pós-falha concluída conforme esperado, pode verificar que as máquinas virtuais do Azure foi encerradas.

### <a name="to-what-recovery-point-can-i-fail-back-the-virtual-machines"></a>Para o ponto de recuperação pode posso falhar novamente as máquinas virtuais?

Durante a reativação pós-falha, tem duas opções para falhar novamente o plano de recuperação da máquina virtual /.

Se selecionar o ponto de processamento mais recente no tempo, todas as máquinas virtuais serão ativadas pós-falha para o ponto mais recente no tempo. Caso exista um grupo de replicação no plano de recuperação, em seguida, cada máquina virtual do grupo de replicação serão ativadas pós-falha para o respetivo independente ponto mais recente no tempo.

Não pode falhar novamente uma máquina virtual até ter, pelo menos, um ponto de recuperação. Não pode falhar novamente um plano de recuperação até que todas as respetivas máquinas virtuais tenha, pelo menos, um ponto de recuperação.

> [!NOTE]
> Um ponto de recuperação mais recente é um ponto de recuperação consistentes com falhas.

Se selecionar o ponto de recuperação consistente da aplicação, uma reativação pós-falha única máquina virtual será recuperada para o respetivo ponto de recuperação consistentes com aplicações disponíveis mais recente. No caso de um plano de recuperação com um grupo de replicação, cada grupo de replicação irá recuperar para o respetivo ponto de recuperação disponíveis comuns.
Tenha em atenção que os pontos de recuperação consistentes com aplicações podem ser atrás no tempo e poderá haver perda de dados.

### <a name="what-happens-to-vmware-tools-post-failback"></a>O que acontece a reativação pós-falha de post de ferramentas do VMware?

Durante a ativação pós-falha para o Azure, as ferramentas VMware não podem estar em execução na máquina virtual do Azure. Em caso de uma máquina virtual do Windows, a ASR desativa as ferramentas do VMware durante a ativação pós-falha. Em caso de máquina virtual do Linux, a ASR desinstala as ferramentas do VMware durante a ativação pós-falha.

Durante a reativação pós-falha da máquina virtual Windows, as ferramentas do VMware são novamente ativadas após a reativação pós-falha. Da mesma forma, para uma máquina virtual do linux, as ferramentas do VMware são reinstaladas na máquina durante a reativação pós-falha.

## <a name="next-steps"></a>Passos seguintes

Após a conclusão da reativação pós-falha, tem de consolidar a máquina virtual para garantir que são eliminadas recuperadas máquinas virtuais no Azure.

### <a name="commit"></a>Consolidação
É necessária confirmação para remover a ativação pós-falha da máquina virtual do Azure.
Clique no item protegido e, em seguida, clique em **consolidar**. Uma tarefa irá remover a ativação pós-falha de máquinas virtuais no Azure.

### <a name="reprotect-from-on-premises-to-azure"></a>Voltar a proteger no local para o Azure

Após a consolidação, a máquina virtual está novamente no site no local, mas não será possível protegê-lo. Para começar a replicar para o Azure novamente, efetue o seguinte:

1. No **cofre** > **definição** > **replicado itens**, selecione as máquinas virtuais que falharam novamente e, em seguida, clique em  **Voltar a proteger**.
2. Atribua o valor do servidor de processos que tem de ser utilizada para enviar dados para o Azure.
3. Clique em **OK** para iniciar a tarefa de reproteção.

> [!NOTE]
> Depois de uma máquina de virtual no local é iniciada, demora algum tempo para o agente registar novamente para o servidor de configuração (até 15 minutos). Durante este período, reproteção falha e devolve uma mensagem de erro a indicar que o agente não está instalado. Aguarde alguns minutos e, em seguida, tente reproteção novamente.

Após a conclusão da tarefa de reproteção, a máquina virtual está a replicar para o Azure e pode efetuar uma ativação pós-falha.

## <a name="common-issues"></a>Problemas comuns
Certifique-se de que o vCenter está num estado ligado antes de efetuar uma reativação pós-falha. Caso contrário, desligar discos e ligá-los para a máquina virtual irão falhar.

### <a name="common-error-codes"></a>Códigos de erro comuns

#### <a name="error-code-8038"></a>Código de erro 8038

*Falha ao colocar a máquina de virtual no local devido ao erro*

Isto acontece quando 
1. A máquina virtual no local seja colocada de cópia de segurança num anfitrião que não tem memória suficiente aprovisionada.

Para resolver este problema
1. Pode aprovisionar mais memória no anfitrião ESXi.
2. vMotion a VM para outro anfitrião ESXi que tenha memória suficiente para a máquina virtual de arranque.


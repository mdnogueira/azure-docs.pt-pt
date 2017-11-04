---
title: Migrar as suas VMs do Windows para o armazenamento Premium do Azure com o Azure Site Recovery | Microsoft Docs
description: "Migre as máquinas virtuais existentes para o Premium Storage do Azure utilizando a recuperação de Site. Armazenamento Premium oferece suporte de disco de elevado desempenho, baixa latência para cargas de trabalho de I/O intensivo em execução em máquinas de virtuais do Azure."
services: virtual-machines-windows
cloud: Azure
documentationcenter: na
author: luywang
manager: jeconnoc
ms.assetid: 
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2017
ms.author: luywang
ms.openlocfilehash: ed92255264a155fff5ad1a8d9a3cd1a7bda4e972
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/02/2017
---
# <a name="migrate-to-premium-storage-by-using-azure-site-recovery"></a>Migrar para o Premium Storage, utilizando o Azure Site Recovery

[Armazenamento Premium do Azure](premium-storage.md) fornece suporte de disco de elevado desempenho, baixa latência para as máquinas virtuais (VMs) que estejam a executar cargas de trabalho de I/O intensivo. Este guia ajuda-o a migrar os discos VM a partir de uma conta de armazenamento standard para uma conta de armazenamento premium utilizando [do Azure Site Recovery](../../site-recovery/site-recovery-overview.md).

Recuperação de sites é um serviço Azure que contribui para a sua estratégia de continuidade do negócio e recuperação após desastre através da orquestração da replicação de servidores físicos no local e as VMs para a nuvem (Azure) ou para um datacenter secundário. Quando ocorrem falhas na sua localização principal, pode efetuar a ativação pós-falha para a localização secundária para manter as aplicações e cargas de trabalho disponíveis. Falhar voltar à sua localização principal quando se retomam funcionamento normal. 

Recuperação de sites fornece ativações pós-falha de teste manobras recuperação após desastre sem afetar os ambientes de produção. Pode executar ativações pós-falha planeadas com perda mínima de dados (dependendo da frequência de replicação) perante desastres inesperados. No cenário de migração para o Premium Storage, pode utilizar o [ativação pós-falha na recuperação de Site](../../site-recovery/site-recovery-failover.md) para migrar discos de destino para uma conta de armazenamento premium.

Recomendamos a migração para o Premium Storage utilizando a recuperação de Site porque esta opção fornece o período de indisponibilidade mínimo. Esta opção também evita a execução manual de copiar os discos e criar novas VMs. Recuperação de site sistematicamente irá copiar os discos e criar novas VMs durante a ativação pós-falha. 

Recuperação de sites suporta um número de tipos de ativação pós-falha mínima com ou sem períodos de indisponibilidade. Planear o período de indisponibilidade e estimar a perda de dados, consulte o [tipos de ativação pós-falha na recuperação de Site](../../site-recovery/site-recovery-failover.md). Se lhe [preparar a ligação para VMs do Azure após a ativação pós-falha](../../site-recovery/vmware-walkthrough-overview.md), deverá conseguir ligar à VM do Azure com RDP após a ativação pós-falha.

![Diagrama de recuperação de desastre][1]

## <a name="azure-site-recovery-components"></a>Componentes de recuperação de sites do Azure

Estes componentes da recuperação de Site são relevantes para este cenário de migração:

* **Servidor de configuração** é uma VM do Azure que coordena a comunicação e gere os processos de replicação e recuperação de dados. Esta VM, executar um ficheiro de configuração único para instalar o servidor de configuração e um componente, invocou um servidor de processos, como um gateway de replicação. Leia sobre [pré-requisitos do servidor de configuração](../../site-recovery/vmware-walkthrough-overview.md). Configurar o servidor de configuração apenas uma vez e pode utilizá-la para todas as migrações à mesma região.

* **Servidor de processos** é um gateway de replicação que: 

  1. Recebe dados de replicação de VMs de origem.
  2. Otimiza os dados com a colocação em cache, compressão e encriptação.
  3. Envia os dados para uma conta de armazenamento. 

  Também processa a instalação push do serviço de mobilidade para VMs de origem e efetua a descoberta automática de VMs de origem. O servidor de processos de predefinição está instalado no servidor de configuração. Pode implementar servidores de processos de autónomo adicionais ao dimensionar a sua implementação. Leia sobre [melhores práticas para implementação do servidor de processos](https://azure.microsoft.com/blog/best-practices-for-process-server-deployment-when-protecting-vmware-and-physical-workloads-with-azure-site-recovery/) e [implementar servidores de processos adicionais](../../site-recovery/site-recovery-plan-capacity-vmware.md#deploy-additional-process-servers). Configurar o servidor de processos apenas uma vez e pode utilizá-la para todas as migrações à mesma região.

* **Serviço de mobilidade** é um componente que é implementado em cada VM padrão que pretende replicar. Este obtém dados, escreve na VM padrão e reencaminha-os para o servidor de processos. Leia sobre [replicado pré-requisitos máquina](../../site-recovery/vmware-walkthrough-overview.md).

Este gráfico mostra como interagem estes componentes:

![Interação de componentes da recuperação de Site][15]

> [!NOTE]
> Recuperação de sites não suporta a migração dos discos de espaços de armazenamento.

Para os componentes adicionais para obter outros cenários, consulte [arquitetura do cenário](../../site-recovery/vmware-walkthrough-overview.md).

## <a name="azure-essentials"></a>Essentials do Azure

Estes são os requisitos do Azure para este cenário de migração:

* Uma subscrição do Azure.
* Uma conta de armazenamento premium do Azure para armazenar dados replicados.
* Uma rede virtual do Azure para o qual as VMs ligará quando são criados na ativação pós-falha. A rede virtual do Azure tem de ser na mesma região que aquela na qual a recuperação de Site é executada.
* Uma conta de armazenamento padrão do Azure para armazenar os registos de replicação. Isto pode ser a mesma conta de armazenamento para os discos VM que estão a ser migrados.

## <a name="prerequisites"></a>Pré-requisitos

* Compreenda os componentes de cenário de migração relevantes na secção anterior.
* Planear o período de indisponibilidade através da aprendizagem sobre [ativação pós-falha na recuperação de Site](../../site-recovery/site-recovery-failover.md).

## <a name="setup-and-migration-steps"></a>Passos de configuração e migração

Pode utilizar a recuperação de sites para migrar VMs IaaS do Azure entre regiões ou na mesma região. As instruções que se seguem são adaptadas para este cenário de migração do artigo [replicar VMs de VMware ou servidores físicos para o Azure](../../site-recovery/vmware-walkthrough-overview.md). Siga as ligações para obter passos detalhados para além das instruções neste artigo.

### <a name="step-1-create-a-recovery-services-vault"></a>Passo 1: Criar um cofre dos serviços de recuperação

1. Abra o [Portal do Azure](https://portal.azure.com).
2. Selecione **novo** > **gestão** > **cópia de segurança** e **(OMS) de recuperação de sites**. Em alternativa, pode selecionar **procurar** > **cofre dos serviços de recuperação** > **adicionar**. 
3. Especifique uma região que as VMs serão replicadas. Para fins de migração na mesma região, selecione a região onde estão as VMs de origem e as contas de armazenamento de origem. 

### <a name="step-2-choose-your-protection-goals"></a>Passo 2: Escolher os seus objetivos de proteção 

1. Na VM onde pretende instalar o servidor de configuração, abra o [portal do Azure](https://portal.azure.com).
2. Aceda a **cofres dos serviços de recuperação** > **definições** > **recuperação de Site** > **passo 1: preparar Infraestrutura** > **objetivo de proteção**.

   ![Navegar para o painel objetivo de proteção][2]

3. Em **objetivo de proteção**, na primeira lista pendente, selecione **para o Azure**. A segundo na lista pendente, selecionar **não virtualizados / outras**e, em seguida, selecione **OK**.

   ![Painel objetivo de proteção com caixas de preenchida][3]

### <a name="step-3-set-up-the-source-environment-configuration-server"></a>Passo 3: Configurar o ambiente de origem (servidor de configuração)

1. Transferir **configuração de unificada do Azure Site Recovery** e a chave de registo do cofre, acedendo ao **preparar a infraestrutura** > **preparar a origem**  >  **Adicionar servidor** painéis. 
 
   Terá da chave de registo do cofre para executar a configuração unificada. A chave é válida durante cinco dias depois de gerá-la.

   ![Navegar para o painel Adicionar servidor][4]

2. No **Adicionar servidor** painel, adicionar um servidor de configuração.

   ![Adicionar o painel do servidor com o servidor de configuração selecionado][5]

3. Na VM que estiver a utilizar como o servidor de configuração, execute a configuração do Unified para instalar o servidor de configuração e o servidor de processos. Pode [percorrer as capturas de ecrã](../../site-recovery/vmware-walkthrough-overview.md) para concluir a instalação. Pode consultar as capturas de ecrã seguintes para obter os passos especificados para este cenário de migração.

   1. No **antes de começar**, selecione **instalar o servidor de configuração e o servidor de processos**.

      ![Antes de começar a página][6]

   2. No **registo**, procurar e selecionar a chave de registo que transferiu a partir do cofre.

      ![Página de registo][7]

   3. Em **Detalhes do ambiente**, selecione se pretende replicar VMs do VMware. Para este cenário de migração, escolha **não**.

      ![Página de detalhes de ambiente][8]

4. Depois de concluída a instalação, efetue o seguinte procedimento **o servidor de configuração do Microsoft Azure Site Recovery** janela:
 
   1. Utilize o **gerir contas** separador para criar a conta que a recuperação de sites pode utilizar para a deteção automática. (O cenário sobre como proteger máquinas físicas, ao configurar a conta não é relevante, mas precisa de, pelo menos, uma conta para ativar um dos seguintes passos. Neste caso, pode atribuir o nome de conta e palavra-passe como qualquer.) 
   2. Utilize o **registo de cofre** separador para carregar o ficheiro de credenciais do cofre.

      ![Separador de registo do Cofre][9]

### <a name="step-4-set-up-the-target-environment"></a>Passo 4: Configurar o ambiente de destino

Selecione **preparar a infraestrutura** > **destino**e especifique o modelo de implementação que pretende utilizar para as VMs após a ativação pós-falha. Pode escolher **clássico** ou **Resource Manager**, consoante o seu cenário.

![Painel de destino][10]

A Recuperação de Sites verifica que tem uma ou mais contas de armazenamento e redes do Azure compatíveis. 

> [!NOTE]
> Se estiver a utilizar uma conta de armazenamento premium para os dados replicados, terá de configurar uma conta de armazenamento padrão adicional para armazenar os registos de replicação.

### <a name="step-5-set-up-replication-settings"></a>Passo 5: Configurar as definições de replicação

Para verificar se o servidor de configuração está associado com êxito a política de replicação que criar, siga [configurar as definições de replicação](../../site-recovery/vmware-walkthrough-overview.md).

### <a name="step-6-plan-capacity"></a>Passo 6: Capacidade de plano

1. Utilize o [Planeador de capacidade](../../site-recovery/site-recovery-capacity-planner.md) para calcular com exatidão a largura de banda de rede, armazenamento e outros requisitos para satisfazer a replicação precisa. 
2. Quando estiver pronto, selecione **Sim, ter efetuado esta** no **concluiu o planeamento de capacidade?**.

   ![Caixa para confirmar se concluiu o planeamento de capacidade][11]

### <a name="step-7-install-the-mobility-service-and-enable-replication"></a>Passo 7: Instalar o serviço de mobilidade e ativar a replicação

1. Pode optar por [instalação push](../../site-recovery/vmware-walkthrough-overview.md) para as VMs de origem ou a [instalar manualmente o serviço de mobilidade](../../site-recovery/site-recovery-vmware-to-azure-install-mob-svc.md) nas VMs de origem. Pode encontrar o requisito de enviar a instalação e o caminho do programa de instalação manual na ligação fornecida. Se estiver a efetuar uma instalação manual, poderá ter de utilizar um endereço IP para localizar o servidor de configuração.

   ![Página de detalhes do servidor de configuração][12]

   A VM de efetuada a ativação pós-falha terá dois discos temporários: um da VM principal e o outro criado durante o aprovisionamento da VM na região de recuperação. Para excluir o disco temporário antes de replicação, instale o serviço de mobilidade antes de ativar a replicação. Para obter mais informações sobre como excluir o disco temporário, consulte o artigo [excluir discos de replicação](../../site-recovery/vmware-walkthrough-overview.md).

2. Ative a replicação da seguinte forma:
   1. Selecione **replicar aplicação** > **origem**. Depois de ativar a replicação pela primeira vez, selecione **+ replicar** no cofre para ativar a replicação para máquinas adicionais.
   2. No passo 1, configurar **origem** como o servidor de processos.
   3. No passo 2, especifique o modelo de implementação de pós-falha, uma conta de armazenamento premium para migrar para uma conta de armazenamento standard para guardar os registos e uma rede virtual para conseguir.
   4. No passo 3, adicione VMs protegidas por endereço IP. (Poderá ter um endereço IP a encontrá-los.)
   5. Passo 4, configure as propriedades selecionando as contas que configurou anteriormente no servidor de processos.
   6. Passo 5, escolha a política de replicação que criou anteriormente no "passo 5: configurar as definições de replicação."
   7. Selecione **OK**.

   > [!NOTE]
   > Quando uma VM do Azure é anulada e iniciar novamente, não há nenhuma garantia que obterá o mesmo endereço IP. Se alterar o endereço IP do servidor de processo do servidor de configuração ou VMs protegidas do Azure, a replicação este cenário poderá não funcionar corretamente.

   ![Ativar o painel de replicação com origem selecionada][13]

Ao conceber o ambiente de armazenamento do Azure, recomendamos que utilize contas de armazenamento separada para cada VM num conjunto de disponibilidade. Recomendamos que siga o procedimento recomendado na camada de armazenamento para [utilizar várias contas do storage para cada conjunto de disponibilidade](../linux/manage-availability.md). Distribuição de discos VM para várias contas de armazenamento ajuda a melhorar a disponibilidade de armazenamento e distribui a e/s em toda a infraestrutura de armazenamento do Azure.

Se as suas VMs num conjunto de disponibilidade, em vez de replicar os discos de todas as VMs para uma conta de armazenamento, recomendamos vivamente a migrar de várias VMs várias vezes. Dessa forma, as VMs no mesmo conjunto de disponibilidade não partilham uma única conta de armazenamento. Utilize o **ativar replicação** painel para configurar uma conta de armazenamento de destino para cada VM, um de cada vez.
 
Pode escolher um modelo de implementação de pós-falha, de acordo com satisfaça as suas necessidades. Se escolher o Gestor de recursos do Azure como o modelo de implementação de pós-falha, pode efetuar a ativação pós-falha numa VM (Resource Manager) para uma VM (Resource Manager) ou, pode efetuar uma ativação pós-falha numa VM (clássica) para uma VM (Resource Manager).

### <a name="step-8-run-a-test-failover"></a>Passo 8: Executar uma ativação pós-falha de teste

Para verificar se a replicação esteja concluída, selecione a instância de recuperação de Site e, em seguida, selecione **definições** > **itens replicados**. Irá ver o estado e a percentagem do processo de replicação. 

Após a replicação inicial está concluída, execute uma ativação pós-falha de teste para validar a sua estratégia de replicação. Para obter passos detalhados de uma ativação pós-falha de teste, consulte [executar uma ativação pós-falha no Site Recovery](../../site-recovery/vmware-walkthrough-overview.md). 

> [!NOTE]
> Antes de executar qualquer ativação pós-falha, certifique-se de que as VMs e a estratégia de replicação cumprem os requisitos. Para obter mais informações sobre como executar uma ativação pós-falha de teste, consulte [testar a ativação pós-falha para o Azure Site Recovery](../../site-recovery/site-recovery-test-failover-to-azure.md).

Pode ver o estado da sua ativação pós-falha de teste no **definições** > **tarefas** > *YOUR_FAILOVER_PLAN_NAME*. No painel, pode ver uma divisão dos passos e resultados de êxito/falha. Se a ativação pós-falha de teste falhar em qualquer passo, selecione o passo para verificar a mensagem de erro. 

### <a name="step-9-run-a-failover"></a>Passo 9: Executar uma ativação pós-falha

Depois do teste de ativação pós-falha estiver concluída, execute uma ativação pós-falha para migrar os discos para o Premium Storage e replicar as instâncias de VM. Siga os passos detalhados [executar uma ativação pós-falha](../../site-recovery/site-recovery-failover.md#run-a-failover). 

Lembre-se de que selecione **encerrar VMs e sincronizar os dados mais recentes**. Esta opção especifica que a recuperação de sites deve tentar por encerrar as máquinas virtuais protegidas e sincronizar os dados para que a versão mais recente dos dados será efetuada a ativação pós-falha. Se não selecionar esta opção ou a tentativa não teve êxito, a ativação pós-falha será o ponto de recuperação disponível mais recente para a VM. 

Recuperação de sites irá criar uma instância VM cujo tipo é o mesmo como ou semelhante a uma VM com capacidade de armazenamento Premium. Pode verificar o desempenho e o preço de várias instâncias VM, acedendo a [preços de máquinas virtuais Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) ou [preços de máquinas virtuais do Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/).

## <a name="post-migration-steps"></a>Passos de pós-migração

1. **Configurar VMs replicadas para o conjunto se aplicável disponibilidade**. Recuperação de sites não suporta VMs migrar juntamente com o conjunto de disponibilidade. Dependendo da implementação de VM replicada, efetue um dos seguintes:
   * Para uma VM criada através do modelo de implementação clássica: adicionar a VM para a conjunto de disponibilidade no portal do Azure. Para obter passos detalhados, aceda a [adicionar uma máquina virtual existente para um conjunto de disponibilidade](../linux/classic/configure-availability.md#addmachine).
   * Para uma VM criada através do modelo de implementação Resource Manager: guardar a configuração da VM e, em seguida, eliminar e voltar a criar as VMs no conjunto de disponibilidade. Para tal, utilize o script em [definir do Azure Resource Manager VM conjunto de disponibilidade](https://gallery.technet.microsoft.com/Set-Azure-Resource-Manager-f7509ec4). Antes de executar este script, verifique o respetivas limitações e planear os períodos de indisponibilidade.

2. **Eliminar antigo VMs e discos**. Certifique-se de que os discos Premium são consistentes com discos de origem e que as VMs novas executem a mesma função que as VMs de origem. No modelo de implementação Resource Manager, elimine a VM e elimine os discos das suas contas de armazenamento de origem no portal do Azure. No modelo de implementação clássica, pode eliminar a VM e os discos no portal clássico ou no portal do Azure. Se não existir um problema em que o disco não é eliminada, apesar de eliminar a VM, consulte [resolver erros ao eliminar os VHDs](../../storage/common/storage-resource-manager-cannot-delete-storage-account-container-vhd.md).

3. **Limpar a infraestrutura do Azure Site Recovery**. Se a recuperação de sites já não for necessário, pode apagar a respetiva infraestrutura. Eliminar itens replicados, o servidor de configuração e a política de recuperação e, em seguida, elimine o cofre do Azure Site Recovery.

## <a name="troubleshooting"></a>Resolução de problemas

* [Monitorizar e resolver problemas de proteção para máquinas virtuais e servidores físicos](../../site-recovery/site-recovery-monitoring-and-troubleshooting.md)
* [Fórum do Microsoft Azure Site Recovery](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr)

## <a name="next-steps"></a>Passos seguintes

Para cenários específicos para migrar máquinas virtuais, consulte os seguintes recursos:

* [Migrar máquinas virtuais do Azure entre contas de armazenamento](https://azure.microsoft.com/blog/2014/10/22/migrate-azure-virtual-machines-between-storage-accounts/)
* [Criar e carregar um VHD do Windows Server para o Azure](classic/createupload-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)
* [Criar e carregar um disco rígido virtual que contém o sistema operativo Linux](../linux/classic/create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [Migração de máquinas virtuais do Amazon AWS ao Microsoft Azure](http://channel9.msdn.com/Series/Migrating-Virtual-Machines-from-Amazon-AWS-to-Microsoft-Azure)

Além disso, consulte os seguintes recursos para saber mais sobre o Storage do Azure e Virtual Machines do Azure:

* [Armazenamento do Azure](https://azure.microsoft.com/documentation/services/storage/)
* [Máquinas virtuais do Azure](https://azure.microsoft.com/documentation/services/virtual-machines/)
* [Armazenamento Premium: Armazenamento de elevado desempenho para cargas de trabalho de máquina virtual do Azure](premium-storage.md)

[1]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-1.png
[2]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-2.png
[3]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-3.png
[4]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-4.png
[5]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-5.png
[6]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-6.PNG
[7]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-7.PNG
[8]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-8.PNG
[9]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-9.PNG
[10]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-10.png
[11]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-11.PNG
[12]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-12.PNG
[13]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-13.png
[14]:../site-recovery/media/site-recovery-vmware-to-azure/v2a-architecture-henry.png
[15]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-14.png

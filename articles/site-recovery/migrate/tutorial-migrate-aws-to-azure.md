---
title: Migrar VMs do AWS para o Azure com o Azure Site Recovery | Microsoft Docs
description: "Este artigo descreve como migrar VMs em execução nos Amazon Web Services (AWS) para o Azure, utilizando o Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: ddb412fd-32a8-4afa-9e39-738b11b91118
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 11/27/2017
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 6eb6489deacab71e870585d209e26dad801ddc07
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/29/2017
---
# <a name="migrate-amazon-web-services-aws-vms-to-azure"></a>Migrar VMs do Amazon Web Services (AWS) para o Azure

Este tutorial informa como migrar Amazon Web Services (AWS) máquinas virtuais (VMs), para VMs do Azure com a recuperação de Site. Quando migrar instâncias de EC2 para o Azure, VMsare tratado como se físico, computadores no local. Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Preparar os recursos do Azure
> * Preparar as instâncias de AWS EC2 para migração
> * Implementar um servidor de configuração
> * Ativar a replicação para VMs
> * Teste a ativação pós-falha para Certifique-se de que tudo está a funcionar
> * Executar uma ativação pós-falha única para o Azure

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) antes de começar.


## <a name="prepare-azure-resources"></a>Preparar os recursos do Azure 

Tem de ter alguns recursos prontos no Azure para as instâncias migradas de EC2 a utilizar. Estes incluem uma conta de armazenamento, um cofre e uma rede virtual.

### <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

Imagens de máquinas replicadas são guardadas no armazenamento do Azure. As VMs do Azure são criadas do armazenamento, quando a ativação pós-falha do local para Azure.

1. No [portal do Azure](https://portal.azure.com) menu, clique em **novo** -> **armazenamento** -> **conta de armazenamento**.
2. Introduza um nome para a conta do Storage. Para estes tutoriais, utilizamos o nome **awsmigrated2017**. O nome tem de ser exclusivo no Azure e de ter entre 3 e 24 carateres, apenas os números e letras minúsculas.
3. Mantenha as predefinições para **modelo de implementação**, **conta kind**, **desempenho**, e **Secure transferência necessária**.
5. Selecionar a predefinição **RA-GRS** para **replicação**.
6. Selecione a subscrição que pretende utilizar para este tutorial.
7. Para **grupo de recursos**, selecione **criar nova**. Neste exemplo, utilizamos **migrationRG** como o nome.
8. Selecione **Europa Ocidental** como a localização. 
9. Clique em **Criar** para criar a conta do Storage.

### <a name="create-a-vault"></a>Criar um cofre

1. No [portal do Azure](https://portal.azure.com), no painel de navegação esquerdo, clique em **mais serviços** , procure e selecione **cofres dos serviços de recuperação**.
2. Na página de cofres dos serviços de recuperação, clique em **+ adicionar** no canto superior esquerdo da página.
3. Para **nome**, tipo *myVault*. 
4. Para **subscrição**, selecione a subscrição adequada.
4. Para **grupo de recursos**, selecione **utilizar existente** e selecione *migrationRG*. 
5. No **localização**, selecione *Europa Ocidental*. 
5. Para aceder rapidamente o novo cofre a partir do dashboard, selecione **afixar ao dashboard**.
7. Quando tiver terminado, clique em **criar**.

O novo cofre é apresentado no **Dashboard** > **todos os recursos**e no principal **cofres dos serviços de recuperação** página.

### <a name="set-up-an-azure-network"></a>Configurar uma rede do Azure

Quando as VMs do Azure são criadas após a migração (ativação pós-falha), se estiver a associados a esta rede.

1. No [portal do Azure](https://portal.azure.com), clique em **novo** > **redes** >
    **rede Virtual**
3. Para **nome**, tipo *myMigrationNetwork*.
4. Deixe o valor predefinido para **espaço de endereços**.
5. Para **subscrição**, selecione a subscrição adequada.
6. Para **grupo de recursos**, selecione **utilização existente** e escolha *migrationRG* da lista pendente.
7. Para **localização**, selecione **Europa Ocidental**. 
8. Deixe as predefinições para **sub-rede**, ambos os **nome** e **intervalo IP**.
9. Deixe **pontos finais de serviço** desativada.
10. Quando tiver terminado, clique em **criar**.


## <a name="prepare-the-ec2-instances"></a>Preparar as instâncias de EC2

Precisa de uma ou mais VMs que pretende migrar. Estes instância EC2 deve executar a versão de 64 bits do Windows Server 2008 R2 SP1 ou posterior, Windows Server 2012, Windows Server 2012 R2 ou 6.7 do Red Hat Enterprise Linux (apenas para instâncias HVM virtualizada). O servidor deve ter apenas os controladores de Citrix PV ou AWS PV. As instâncias em execução controladores de VM de RedHat PV não são suportadas.

O serviço de mobilidade tem de ser instalado em cada VM que pretende replicar. Recuperação de site instala automaticamente este serviço ao ativar a replicação para a VM. Para a instalação automática, terá de preparar uma conta nas instâncias de EC2 recuperação de sites irá utilizar para aceder a VM.

Pode utilizar um domínio ou conta local. Para VMs com Linux, a conta deve ser raiz no servidor de Linux de origem. Para VMs do Windows, se não estiver a utilizar uma conta de domínio, desative o controlo de acesso de utilizador remoto no computador local:

  - No registo, sob **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System**, adicione a entrada DWORD **LocalAccountTokenFilterPolicy** e defina o valor para 1.
    
Também precisa de uma instância separada do EC2 que pode utilizar como o servidor de configuração da recuperação de sites. Deve executar o Windows Server 2012 R2 esta instância. 
    

## <a name="prepare-the-infrastructure"></a>Preparar a infraestrutura 

Na página de portal para o cofre, selecione **recuperação de Site** do **introdução** secção e, em seguida, clique em **preparar infraestrutura**.

### <a name="1-protection-goal"></a>1 objetivo de proteção

Selecione os seguintes valores no **objetivo de proteção** página:
   
|    |  | 
|---------|-----------|
| Onde estão localizadas as máquinas? | **No local**|
| Onde pretende replicar as máquinas? |**Para o Azure**|
| As máquinas estão virtualizadas? | **Não virtualizados / outros**|

Quando tiver terminado, clique em **OK** mover para a secção seguinte.

### <a name="2-source-prepare"></a>Preparar a origem de 2 

No **preparar a origem** página, clique em **+ o servidor de configuração**. 

1. Utilize uma instância de EC2 com o Windows Server 2012 R2 para criar um servidor de configuração e registá-lo com o Cofre de recuperação.

2. Configurar o proxy na instância EC2 VM estiver a utilizar como o servidor de configuração para que possam aceder a [URLs do serviço](../site-recovery-support-matrix-to-azure.md).

3. Transferir o [configuração de unificada do Microsoft Azure Site Recovery](http://aka.ms/unifiedinstaller_wus) programa. Pode transferi-lo no seu computador local e, em seguida, copie-o ao longo para a VM estiver a utilizar como o servidor de configuração.

4. Clique em de **transferir** botão para transferir a chave de registo do cofre. Copie o ficheiro transferido para a VM estiver a utilizar como o servidor de configuração.

5. Na VM, clique com botão direito instalador que transferiu para a **configuração de unificada do Microsoft Azure Site Recovery** e selecione **executar como administrador**. 

    1. No **antes de começar**, selecione **instalar o servidor de configuração e o servidor de processos** e, em seguida, clique em **seguinte**.
    2. No **licença de Software de terceiros**, selecione **aceito o contrato de licença de terceiros.** e, em seguida, clique em **seguinte**.
    3. No **registo**, clique em Procurar e navegue para onde colocar o ficheiro de chave de registo de cofre e, em seguida, clique em **seguinte**.
    4. No **definições da Internet**, selecione **ligar ao Azure Site Recovery sem um servidor proxy.** e, em seguida, clique em **seguinte**.
    5. No **verificação de pré-requisitos** página, é executada verifica a existência de vários itens. Quando estiver concluída, clique em **seguinte**. 
    6. No **MySQL configuração**, forneça as palavras-passe necessárias e, em seguida, clique em **seguinte**.
    7. No **ambiente detalhes**, selecione **não**, não precisa de proteger máquinas VMware e, em seguida, clique em **seguinte**.
    8. No **instalar localização**, clique em **seguinte** para aceitar a predefinição.
    9. No **seleção de rede**, clique em **seguinte** para aceitar a predefinição.
    10. No **resumo** clique **instalar**.
    11. **Progresso da instalação** mostra-lhe informações sobre onde estiver no processo de instalação. Quando estiver concluída, clique em **concluir**. Obter um pop-up sobre necessitar de um reinício possíveis, clique em **OK**. Pode também obter um pop-up sobre o frase de acesso de ligação de servidor de configuração, copie o frase de acesso à sua área de transferência e guardá-lo algures seguro.
    
6. Na VM, execute **cspsconfigtool.exe** para criar uma ou mais contas de gestão no servidor de configuração. Certifique-se de que as contas de gestão têm permissões de administrador nas instâncias do EC2 que pretende migrar. 

Quando terminar de configurar o servidor de configuração, volte atrás para o portal e selecione o servidor que acabou de criar para **servidor de configuração** e clique em *OK** para avançar para o passo 3 Preparar de destino.

### <a name="3-target-prepare"></a>Preparar o destino 3 

Nesta secção introduzir informações sobre os recursos que criou quando ocorreu de [recursos Azure preparar](#prepare-azure-resources) secção, anteriormente neste tutorial.

1. No **subscrição**, selecione a subscrição do Azure que utilizou para o [preparar Azure](../tutorial-prepare-azure.md) tutorial.
2. Selecione **Resource Manager** como o modelo de implementação.
3. A Recuperação de Sites verifica que tem uma ou mais contas de armazenamento e redes do Azure compatíveis. Devem ser os recursos que criou quando ocorreu de [recursos Azure preparar](#prepare-azure-resources) secção, anteriormente neste tutorial
4. Quando tiver terminado, clique em **OK**.


### <a name="4-replication-settings-prepare"></a>4 as definições de replicação preparar 

Tem de criar uma política de replicação antes de poder ativar a replicação

1. Clique em **+ replicar e associe**.
2. No **nome**, tipo **myReplicationPolicy**.
3. Deixe as restantes predefinições e clique em **OK** para criar uma política. A nova política é automaticamente associada ao servidor de configuração. 

### <a name="5-deployment-planning-select"></a>Implementação 5 planeamento selecione 

No **concluiu o planeamento da implementação?**, selecione **irei fazê-lo mais tarde** da lista pendente e, em seguida, clique em **OK**.

Quando todos os terminar com todas as 5 secções da **preparar a infraestrutura**, clique em **OK**.


## <a name="enable-replication"></a>Ativar a replicação

Ative a replicação para cada VM que pretende migrar. Quando a replicação está ativada, a recuperação de Site instala automaticamente o serviço de mobilidade. 

1. Abra o [Portal do Azure](htts://portal.azure.com).
1. Na página do seu Cofre, sob **introdução**, clique em **recuperação de Site**.
2. Em **para máquinas no local e as VMs do Azure**, clique em **passo 1:Replicate aplicação**. Concluir as páginas do assistente com as seguintes informações e clique em **OK** em cada página quando concluída:
    - Configurar 1 origem:
      
    |  |  |
    |-----|-----|
    | Origem: | **No local**|
    | Localização de origem:| O nome da sua instância de servidor EC2 de configuração.|
    |Tipo de computador: | **Máquinas físicas**|
    | Servidor de processos: | Selecione o servidor de configuração na lista pendente.|
    
    - Configurar o destino de 2
        
    |  |  |
    |-----|-----|
    | Alvo: | Deixe a predefinição.|
    | Subscrição: | Selecione a subscrição que estiver a utilizar.|
    | Grupo de recursos de pós-falha:| Utilize o grupo de recursos que criou no [recursos Azure preparar](#prepare-azure-resources) secção.|
    | Modelo de implementação de pós-falha: | Escolha **Gestor de recursos**|
    | Conta de armazenamento: | Escolha a conta de armazenamento que criou no [recursos Azure preparar](#prepare-azure-resources) secção.|
    | Rede do Azure: | Escolha **configurar agora para as máquinas selecionadas**|
    | Pós-falha rede do Azure: | Escolha a rede que criou no [recursos Azure preparar](#prepare-azure-resources) secção.|
    | Sub-rede: | Selecione o **predefinido** da lista pendente.|
    
    - Selecione 3 máquinas físicas
        
        Clique em **+ máquina física** e, em seguida, introduza o **nome**, a **endereço IP** e **tipo de SO** da instância EC2 que pretende migrar e em seguida, clique em **OK**.
        
    - 4 propriedades configurar propriedades
        
        Selecione a conta que criou no servidor de configuração a partir da lista pendente e clique em **OK**.
        
    - 5 configurar as definições de replicação as definições de replicação
    
        Certifique-se de que a política de replicação selecionada na lista pendente **myReplicationPolicy** e, em seguida, clique em **OK**.
        
3. Quando o assistente estiver concluído, clique em **ativar a replicação**.
        

Pode controlar o progresso do **ativar proteção** da tarefa no **monitorização e relatórios** > **tarefas** > **tarefas de recuperação de Site** . Depois da tarefa **Finalizar Proteção** ser executada, a máquina está preparada para ativação pós-falha.        
        
Ao ativar a replicação para uma VM, este pode demorar 15 minutos ou já para as alterações que tenha efeito e aparecer no portal.

## <a name="run-a-test-failover"></a>Executar uma ativação pós-falha de teste

Quando executar uma ativação pós-falha de teste, ocorrerá o seguinte:

1. Verificação de um pré-requisitos é executado para se certificar de que todas as condições necessárias para a ativação pós-falha estão em vigor.
2. Ativação pós-falha processa os dados, para que uma VM do Azure podem ser criada. Se selecionar o ponto de recuperação mais recente, um ponto de recuperação é criado a partir de dados.
3. VM do Azure é criada utilizando os dados processados no passo anterior.

No portal, execute a ativação pós-falha de teste da seguinte forma:

1. Na página para o cofre, aceda a **itens protegidos** > **itens replicados**> clique na VM > **+ ativação pós-falha de teste**.

2. Selecione um ponto de recuperação a utilizar para a ativação pós-falha:
    - **Mais recentes processados**: a ativação pós-falha da VM para o ponto de recuperação mais recente que foi processado pela recuperação de sites. O carimbo de hora é apresentado. Com esta opção, não tempo é gasto a processar dados, pelo que fornece um RTO baixa (objetivo de tempo de recuperação).
    - **Mais recente consistentes da aplicação**: esta opção se falhar a todas as VMs do ponto de recuperação consistentes com aplicação mais recente. O carimbo de hora é apresentado.
    - **Personalizada**: selecionar qualquer ponto de recuperação.
3. No **ativação pós-falha de teste**, selecione o destino do Azure rede para as VMs do Azure serão ligadas após a ocorrência da ativação pós-falha. Isto deve ser a rede que criou no [recursos Azure preparar](#prepare-azure-resources) secção.
4. Clique em **OK** para iniciar a ativação pós-falha. Pode controlar o progresso clicando na VM para abrir as respetivas propriedades. Ou pode clicar no **ativação pós-falha de teste** tarefa na página do seu Cofre **monitorização e relatórios** > **tarefas** >
   **Site As tarefas de recuperação**.
5. Após a ativação pós-falha, a réplica VM do Azure é apresentado no portal do Azure > **máquinas virtuais**. Certifique-se de que a VM é o tamanho adequado, tenha ligado à rede à direita, e que está em execução.
6. Agora deverá conseguir ligar à VM replicada no Azure.
7. Para eliminar as VMs do Azure criado durante a ativação pós-falha de teste, clique em **ativação pós-falha de teste de limpeza** no plano de recuperação. No **notas**, registar e guardar todas as observações associadas à ativação pós-falha de teste.

Em alguns cenários, a ativação pós-falha requer processamento adicional, que demora cerca de oito a dez minutos a concluir. 


## <a name="migrate-to-azure"></a>Migrar para o Azure

Execute uma ativação pós-falha real para as instâncias de EC2 para os migrar para as VMs do Azure. 

1. No **itens protegidos** > **replicado itens** clique as instâncias do AWS > **ativação pós-falha**.
2. No **ativação pós-falha** selecionar um **ponto de recuperação** a ativação pós-falha. Selecione o ponto de recuperação mais recente.
3. Selecione **encerrar a máquina antes de iniciar a ativação pós-falha** se pretender que a recuperação de sites para tentar efetuar um encerramento de máquinas virtuais de origem antes de acionar a ativação pós-falha. Ativação pós-falha continua, mesmo se falha de encerramento. Pode seguir o progresso de ativação pós-falha no **tarefas** página.
4. Verifique se a VM aparece no **replicado itens**. 
5. Clique com o botão direito cada VM > **concluir a migração**. Isto termina o processo de migração, deixa de replicação para a VM do AWS e deixa de faturação de recuperação de Site para a VM.

    ![Concluir a migração](./media/tutorial-migrate-aws-to-azure/complete-migration.png)

> [!WARNING]
> **Não cancele uma ativação pós-falha em curso**: antes de iniciar a ativação pós-falha, a replicação de VM está parada. Se cancelar uma ativação pós-falha em curso, deixa de ativação pós-falha, mas a VM não irá replicar novamente.  


    

## <a name="next-steps"></a>Passos seguintes

Neste tópico, aprendeu como migrar instâncias de AWS EC2 para as VMs do Azure. Para saber mais sobre as VMs do Azure, continue os tutoriais para VMs do Windows.

> [!div class="nextstepaction"]
> [Tutoriais de máquinas virtuais do Windows do Azure](../../virtual-machines/windows/tutorial-manage-vm.md)

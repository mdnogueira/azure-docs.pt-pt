---
title: "Planear a capacidade e Dimensionar para replicação de VMware no Azure com o Azure Site Recovery | Microsoft Docs"
description: Utilize este artigo para a capacidade de plano e a escala quando replicar VMs de VMware para o Azure com o Azure Site Recovery
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: 
ms.assetid: 0a1cd8eb-a8f7-4228-ab84-9449e0b2887b
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 10/30/2017
ms.author: rayne
ms.openlocfilehash: 0f4d82d450a6ca2e73c68452a409f300841dbf32
ms.sourcegitcommit: 804db51744e24dca10f06a89fe950ddad8b6a22d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2017
---
# <a name="plan-capacity-and-scaling-for-vmware-replication-with-azure-site-recovery"></a>Planear a capacidade e o dimensionamento para replicação de VMware com o Azure Site Recovery

Utilize este artigo para descobrir o planeamento de capacidade e dimensionamento, quando replicar VMs de VMware no local e servidores físicos para o Azure com [do Azure Site Recovery](site-recovery-overview.md).

## <a name="how-do-i-start-capacity-planning"></a>Como começar a planear a capacidade?

Recolher informações sobre o ambiente de replicação ao executar o [Planeador de implementação do Azure Site Recovery](https://aka.ms/asr-deployment-planner-doc) para replicação de VMware. [Saiba mais](site-recovery-deployment-planner.md) sobre esta ferramenta. Irá recolher informações sobre compatíveis e incompatíveis VMs, discos por VM, e dados churn por disco. A ferramenta também abrange os requisitos de largura de banda de rede e a infraestrutura do Azure necessários para ativação pós-falha com êxito de replicação e teste.

## <a name="capacity-considerations"></a>Considerações sobre a capacidade

**Componente** | **Detalhes** |
--- | --- | ---
**Replicação** | **A velocidade máxima da alteração diária:** uma máquina protegida só pode utilizar um servidor de processos e um servidor de processos único pode processar um diariamente 2 TB de taxa de alteração. Se, portanto, 2 TB que dados diárias máximos alterar a frequência com que é suportada para uma máquina protegida.<br/><br/> **Débito máximo:** uma máquina replicada pode pertencer a uma conta de armazenamento no Azure. Uma conta de armazenamento standard pode processar um máximo de 20 000 pedidos por segundo e recomendamos que mantenha o número de operações de entrada/saída por segundo (IOPS) através de uma máquina de origem a 20.000. Por exemplo, se tiver uma máquina de origem com 5 discos e cada disco gera 120 IOPS (tamanho de 8K) na máquina de origem, em seguida, será dentro do Azure por limite IOPS de disco de 500. (O número de contas de armazenamento necessária é igual à máquina de origem total IOPS, dividida pelo 20.000.)
**Servidor de configuração** | O servidor de configuração deve ser capaz de lidar com a capacidade de taxa de alteração diárias em todas as cargas de trabalho em execução nas máquinas protegidas e tem de largura de banda suficiente para continuamente replicar dados para armazenamento do Azure.<br/><br/> Como melhor prática, localize o servidor de configuração na mesma rede e o segmento de LAN que as máquinas que pretende proteger. Podem estar localizado numa rede diferentes, mas as máquinas que pretende proteger devem ter visibilidade de rede 3 camada ao mesmo.<br/><br/> Recomendações de tamanho para o servidor de configuração estão resumidas na tabela na secção seguinte.
**Servidor de processos** | O primeiro servidor de processos é instalado por predefinição no servidor de configuração. Pode implementar servidores de processos adicionais para dimensionar o seu ambiente. <br/><br/> O servidor de processos recebe dados de replicação de máquinas protegidas e otimiza-as com colocação em cache, compressão e encriptação. Em seguida, envia os dados para o Azure. A máquina do servidor de processo deve ter recursos suficientes para efetuar estas tarefas.<br/><br/> O servidor de processos utiliza uma cache com base em disco. Utilize um disco de cache separado de 600 GB ou mais para processar alterações de dados armazenadas na eventualidade de ocorrer um congestionamento de rede ou uma interrupção.

## <a name="size-recommendations-for-the-configuration-server"></a>Recomendações de tamanho para o servidor de configuração

**CPU** | **Memória** | **Tamanho da cache do disco** | **Taxa de alteração de dados** | **Máquinas protegidas**
--- | --- | --- | --- | ---
8 vCPUs (2 sockets * 4 núcleos @ gigahertz 2,5 [GHz]) | 16 GB | 300 GB | 500 GB ou inferior | Replicar máquinas inferior a 100.
12 vCPUs (2 sockets * 6 núcleos @ 2,5 GHz) | 18 GB | 600 GB | 500 GB a 1 TB | Replicar entre 100 150 máquinas.
16 vCPUs (2 sockets * 8 núcleos @ 2,5 GHz) | 32 GB | 1 TB | 1 TB para 2 TB | Replicar entre 150 200 máquinas.
Implementar a outro servidor de processos | | | > 2 TB | Implemente servidores de processos adicionais se está a replicar mais de 200 máquinas ou, se alterar os dados diários taxa excede 2 TB.

Em que:

* Cada máquina de origem está configurada com 3 discos de 100 GB.
* Utilizámos o direcionamento de caminhos de armazenamento de 8 unidades SAS de 10 mil RPM, com RAID 10, de medidas de disco de cache.

## <a name="size-recommendations-for-the-process-server"></a>Recomendações de tamanho para o servidor de processos

Se pretender proteger mais de 200 máquinas, ou a taxa de alteração diária é maior do que 2 TB, pode adicionar servidores de processos para processar a carga de replicação. Para aumentar horizontalmente, pode:

* Aumente o número de servidores de configuração. Por exemplo, pode proteger até 400 máquinas com dois servidores de configuração.
* Adicionar mais servidores de processos e utilizá-las para processar o tráfego em vez de (ou além de) o servidor de configuração.

A tabela seguinte descreve um cenário em que:

* Não está a planear utilizar o servidor de configuração como um servidor de processos.
* Configurou um servidor de processos adicionais.
* Configurou máquinas virtuais protegidas para utilizar o servidor de processos adicionais.
* Cada máquina de origem protegida está configurada com três discos de 100 GB.

**Servidor de configuração** | **Servidor de processos adicionais** | **Tamanho da cache do disco** | **Taxa de alteração de dados** | **Máquinas protegidas**
--- | --- | --- | --- | ---
8 vCPUs (2 sockets * 4 núcleos @ GHz 2,5), 16 GB de memória | 4 vCPUs (2 sockets * 2 núcleos @ GHz 2,5), 8 GB de memória | 300 GB | 250 GB ou inferior | Replicar máquinas 85 ou menos.
8 vCPUs (2 sockets * 4 núcleos @ GHz 2,5), 16 GB de memória | 8 vCPUs (2 sockets * 4 núcleos @ GHz 2,5), 12 GB de memória | 600 GB | 250 GB a 1 TB | Replicar entre 85 150 máquinas.
12 vCPUs (2 sockets * 6 núcleos @ GHz 2,5), 18 GB de memória | 12 vCPUs (2 sockets * 6 núcleos @ GHz 2,5) 24 GB de memória | 1 TB | 1 TB para 2 TB | Replicar entre 150 225 máquinas.

A forma como dimensionar os servidores depende da sua preferência para um modelo de vertical ou Escalamento horizontal.  Aumentar verticalmente ao implementar alguns configuração de gama alta de classe e servidores de processos, ou aumentar horizontalmente ao implementar mais servidores com menos recursos. Por exemplo, se pretender proteger 220 máquinas, pode efetuar uma das seguintes opções:

* Configure o servidor de configuração com 12 vCPU, 18 GB de memória e um servidor de processos adicionais com 12 vCPU, 24 GB de memória. Configure máquinas protegidas para utilizar apenas o servidor de processos adicionais.
* Configure dois servidores de configuração (2, 8 vCPU, 16 GB de RAM) e dois servidores de processos adicionais (1 x 8 vCPU e 4 vCPU x 1 ao identificador 135 + 85 [220] máquinas). Configure máquinas protegidas para utilizar apenas os servidores de processos adicionais.


## <a name="control-network-bandwidth"></a>Largura de banda de rede de controlo

Depois de utilizou o [a ferramenta de implementação Planner](site-recovery-deployment-planner.md) para calcular a largura de banda que precisa para a replicação (replicação inicial e, em seguida, delta), pode controlar a quantidade de largura de banda utilizada na replicação através de duas opções:

* **Limitar largura de banda**: tráfego do VMware que são replicados para o Azure passa através de um servidor de processo específico. Pode limitar a largura de banda nas máquinas em execução como servidores de processos.
* **Influenciar a largura de banda**: pode influenciar a largura de banda utilizada na replicação através da utilização de um par de chaves de registo de:
  * O **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication\UploadThreadsPerVM** valor de registo Especifica o número de threads que são utilizados para a transferência de dados (replicação inicial ou delta) de um disco. Um valor mais alto aumenta a largura de banda de rede utilizada na replicação.
  * O **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication\DownloadThreadsPerVM** Especifica o número de threads utilizados para a transferência de dados durante a reativação pós-falha.

### <a name="throttle-bandwidth"></a>Limitar largura de banda

1. Abra o snap-in MMC de cópia de segurança do Azure na máquina a agir como o servidor de processos. Por predefinição, um atalho para cópia de segurança está disponível no ambiente de trabalho ou na seguinte pasta: c:\Programas\Microsoft c:\Programas\Microsoft Azure Recovery Services agent\bin\wabadmin.
2. No snap-in, clique em **Alterar Propriedades**.

    ![Captura de ecrã da MMC de cópia de segurança do Azure snap-in da opção para alterar as propriedades](./media/site-recovery-vmware-to-azure/throttle1.png)
3. No **limitação** separador, selecione **ativar a limitação para operações de cópia de segurança de utilização de largura de banda de internet**. Defina os limites para o trabalho e de descanso horas. Os intervalos válidos variam entre 512 Kbps e 102 Mbps por segundo.

    ![Caixa de diálogo de captura de ecrã de propriedades de cópia de segurança do Azure](./media/site-recovery-vmware-to-azure/throttle2.png)

Também pode utilizar o cmdlet [Set-OBMachineSetting](https://technet.microsoft.com/library/hh770409.aspx) para configurar a limitação. Exemplo:

    $mon = [System.DayOfWeek]::Monday
    $tue = [System.DayOfWeek]::Tuesday
    Set-OBMachineSetting -WorkDay $mon, $tue -StartWorkHour "9:00:00" -EndWorkHour "18:00:00" -WorkHourBandwidth  (512*1024) -NonWorkHourBandwidth (2048*1024)

**Set-OBMachineSetting -NoThrottle** indica que não é necessária nenhuma limitação.

### <a name="influence-network-bandwidth-for-a-vm"></a>Influenciar a largura de banda de rede para uma VM

1. No registo da VM, navegue para **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication**.
   * A influenciar o tráfego de largura de banda num disco replicação, modifique o valor do **UploadThreadsPerVM**, ou crie a chave, se não existir.
   * A influenciar a largura de banda para o tráfego de reativação pós-falha a partir do Azure, modifique o valor do **DownloadThreadsPerVM**.
2. O valor predefinido é 4. Numa rede “sobreaprovisionada”, os valores predefinidos destas chaves de registo devem ser alterados. O valor máximo é 32. Monitorize o tráfego para otimizar o valor.


## <a name="deploy-additional-process-servers"></a>Implementar servidores de processos adicionais

Se tiver Dimensionar fora da sua implementação, para além de 200 máquinas de origem, ou se tiver um total diariamente churn taxa de mais do que 2 TB, terá de servidores de processos adicionais para processar o volume de tráfego. Siga estas instruções para configurar o servidor de processos. Depois de configurar o servidor, migre as máquinas de origem para utilizá-lo.

1. No **servidores do Site Recovery**, clique no servidor de configuração e, em seguida, clique em **servidor de processos**.

    ![Opção de servidores de captura de ecrã de recuperação de sites para adicionar um servidor de processos](./media/site-recovery-vmware-to-azure/migrate-ps1.png)
2. No **tipo de servidor**, clique em **(no local) do servidor de processos**.

    ![Caixa de diálogo de captura de ecrã do servidor de processos](./media/site-recovery-vmware-to-azure/migrate-ps2.png)
3. Transfira o ficheiro de configuração do Unified Site Recovery e executá-la para instalar o servidor de processos. Isto também regista-no cofre.
4. Em **Antes de começar**, selecione **Adicionar servidores de processo adicionais para aumentar horizontalmente a implementação**.
5. Conclua o Assistente da mesma forma que fez quando [configurar](#step-2-set-up-the-source-environment) o servidor de configuração.

    ![Assistente de captura de ecrã de configuração Azure Site Recovery unificada](./media/site-recovery-vmware-to-azure/add-ps1.png)
6. No **detalhes do servidor de configuração**, especifique o endereço IP do servidor de configuração e o frase de acesso. Para obter o frase de acesso, execute **[SiteRecoveryInstallationFolder]\home\sysystems\bin\genpassphrase.exe – n** no servidor de configuração.

    ![Página de detalhes do servidor captura de ecrã de configuração](./media/site-recovery-vmware-to-azure/add-ps2.png)

### <a name="migrate-machines-to-use-the-new-process-server"></a>Migrar as máquinas para utilizar o novo servidor de processos
1. No **definições** > **servidores do Site Recovery**, clique no servidor de configuração e, em seguida, expanda **processar servidores**.

    ![Caixa de diálogo de captura de ecrã do servidor de processos](./media/site-recovery-vmware-to-azure/migrate-ps2.png)
2. O servidor de processos atualmente em utilização com o botão direito e clique em **comutador**.

    ![Caixa de diálogo de servidor de captura de ecrã de configuração](./media/site-recovery-vmware-to-azure/migrate-ps3.png)
3. No **servidor de processos de destino selecione**, selecione o novo servidor de processos que pretende utilizar e, em seguida, selecione as máquinas virtuais que o servidor irá processar. Clique no ícone de informações para obter informações sobre o servidor. Para ajudar a tomar decisões de carga, é apresentado o espaço médio para replicar a cada máquina virtual selecionada para o novo servidor de processo. Clique na marca de verificação para iniciar a replicação para o novo servidor de processo.


## <a name="next-steps"></a>Passos seguintes

Transfira e execute o [Planeador de implementação do Azure Site Recovery](https://aka.ms/asr-deployment-planner)

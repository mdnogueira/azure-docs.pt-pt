---
title: Ferramenta de Planeador de capacidade Hyper-V para o Azure Site Recovery | Microsoft Docs
description: Este artigo descreve como executar a ferramenta capacity planner do Hyper-V para o Azure Site Recovery
services: site-recovery
documentationcenter: na
author: rayne-wiselman
manager: jwhit
editor: 
ms.assetid: 2bc3832f-4d6e-458d-bf0c-f00567200ca0
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 10/30/2017
ms.author: nisoneji
ms.openlocfilehash: db790f9dc56605b5b752e7ab797903e32b2fc675
ms.sourcegitcommit: 43c3d0d61c008195a0177ec56bf0795dc103b8fa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/01/2017
---
# <a name="hyper-v-capacity-planner-tool-for-site-recovery"></a>Ferramenta de Planeador de capacidade Hyper-V para a recuperação de Site

Como parte da implementação do Azure Site Recovery, tem de descobrir os seus requisitos de largura de banda e de replicação. A ferramenta do Hyper-V capacity planner para recuperação de sites ajuda-o para o fazer, para replicação da máquina virtual de Hyper-V.

Este artigo descreve como executar a ferramenta capacity planner do Hyper-V. Esta ferramenta deve ser utilizada em conjunto com as informações no [planeamento da capacidade para a recuperação de sites](site-recovery-capacity-planner.md).

## <a name="before-you-start"></a>Antes de começar
A ferramenta é executada num nó servidor ou cluster Hyper-V no seu site primário. Para executar a ferramenta tem dos servidores de anfitrião do Hyper-V:

* Sistema operativo: Windows Server 2012 ou 2012 R2
* Memória: 20 MB (no mínimo)
* Da CPU: a sobrecarga de 5 porcento (no mínimo)
* Espaço em disco: 5 MB (no mínimo)

Antes de executar a ferramenta, terá de preparar o site primário. Se estiver a replicar entre dois sites no local e pretende verificar a largura de banda, terá de preparar o servidor de réplica.

## <a name="step-1-prepare-the-primary-site"></a>Passo 1: Preparar o site primário

1. No site primário, se uma lista de todas as VMs de Hyper-V que pretende replicar e anfitriões/clusters de Hyper-V no qual está localizados. A ferramenta pode ser executado para vários anfitriões autónomos ou para um único cluster, mas não ambos em conjunto. Também necessita de ser executado em separado para cada sistema operativo, pelo que deve reunir informações sobre servidores de Hyper-V da seguinte forma:

   * Servidores autónomos do Windows Server 2012
   * Clusters do Windows Server 2012
   * Servidores autónomos do Windows Server 2012 R2
   * Clusters do Windows Server 2012 R2
2. Ative o acesso remoto ao WMI em todos os anfitriões Hyper-V e clusters. Execute este comando em cada servidor/cluster, para se certificar de que o conjunto de regras de firewall e permissões de utilizador:

        netsh firewall set service RemoteAdmin enable
3. Ative monitorização do desempenho nos servidores e clusters, da seguinte forma:

   * Abra a Firewall do Windows com o **segurança avançada** snapin e, em seguida, ativar a seguinte entrada regras: **COM + acesso à rede (DCOM-IN)** e todas as regras no **registo de eventos remoto Grupo de gestão**.

## <a name="step-2-prepare-a-replica-server-on-premises-to-on-premises-replication"></a>Passo 2: Preparar um servidor de réplica (no local para replicação no local)
Não precisa de efetuar este procedimento se está a replicar para o Azure.

Recomendamos que configurou um único anfitrião Hyper-V como um servidor de recuperação, para que uma VM fictício pode ser replicada para a mesma para verificar a largura de banda.  Pode ignorar este, mas não conseguirá medir a largura de banda, a menos que pode fazê-lo.

1. Se pretender utilizar um nó de cluster, como a réplica de configurar o Mediador de réplicas do Hyper-V:

   * No **Gestor de servidor**, abra **Gestor de clusters de ativação pós-falha**.
   * Ligar ao cluster, realce o nome do cluster e clique em **ações** > **configurar função** para abrir o Assistente de elevada disponibilidade.
   * No **selecionar função**, clique em **Mediador de réplicas do Hyper-V**. No Assistente de fornecer um **nome NetBIOS** e **endereço IP** a ser utilizado como ponto de ligação ao cluster (chamado um ponto de acesso de cliente). O **Mediador de réplicas do Hyper-V** será configurado, resultando num nome de ponto de acesso de cliente que deve ter em consideração.
   * Certifique-se de que a função de Mediador de réplicas do Hyper-V fica online com sucesso e pode efetuar a ativação pós-falha entre todos os nós do cluster. Para tal, clique com o botão direito do rato na função, aponte para **mover**e, em seguida, clique em **selecionar nó**. Selecione um nó > **OK**.
   * Se estiver a utilizar a autenticação baseada em certificados, certifique-se de que cada nó do cluster e o cliente do ponto de acesso têm o certificado instalado.
2. Permitir que um servidor de réplica:

   * Para um cluster de abrir o Gestor de clusters de falha, ligar ao cluster e clique em **funções** > selecione função > **as definições de replicação** > **ativar este cluster como uma réplica servidor**. Se utilizar um cluster como a réplica, terá de ter a função de Mediador de réplicas do Hyper-V existe no cluster no site primário.
   * Para um servidor autónomo abra o Gestor de Hyper-V. No **ações** painel, clique em **definições de Hyper-V** para o servidor que pretende ativar e no **configuração de replicação** clique **ativar este computador como um Servidor de réplica**.
3. Configure a autenticação:

   * No **autenticação e portas**, selecione a forma como autenticar o servidor primário e a autenticação portas. Se utilizar um certificado clique **selecionar certificado** para selecionar uma. Utilize o Kerberos se os anfitriões de Hyper-V principal e de recuperação estão no mesmo domínio ou em domínios fidedignos. Utilize certificados para domínios diferentes, ou para uma implementação de grupo de trabalho.
   * No **autorização e armazenamento**, permitir **qualquer** autenticado (principal) servidor para enviar dados de replicação para este servidor de réplica.

     ![](./media/site-recovery-capacity-planning-for-hyper-v-replication/image1.png)
   * Executar **netsh http show servicestate**, para verificar se o serviço de escuta está em execução para a porta/protocolo especificado:  
4. Configure firewalls. Durante a instalação do Hyper-V, são criadas as regras de firewall para permitir tráfego no predefinido portas (HTTPS no 443, Kerberos em 80). Ative estas regras da seguinte forma:
  - Autenticação de certificado no cluster (443):``Get-ClusterNode | ForEach-Object {Invoke-command -computername \$\_.name -scriptblock {Enable-Netfirewallrule -displayname "Hyper-V Replica HTTPS Listener (TCP-In)"}}``
  - Autenticação Kerberos no cluster (80):``Get-ClusterNode | ForEach-Object {Invoke-command -computername \$\_.name -scriptblock {Enable-Netfirewallrule -displayname "Hyper-V Replica HTTP Listener (TCP-In)"}}``
  - Autenticação de certificado no servidor autónomo:``Enable-Netfirewallrule -displayname "Hyper-V Replica HTTPS Listener (TCP-In)"``
  - Autenticação Kerberos num servidor autónomo:``Enable-Netfirewallrule -displayname "Hyper-V Replica HTTP Listener (TCP-In)"``

## <a name="step-3-run-the-capacity-planner-tool"></a>Passo 3: Executar a ferramenta capacity planner
Depois de ter preparado o seu site primário e configurar um servidor de recuperação, pode executar a ferramenta.

1. [Transferir](https://www.microsoft.com/download/details.aspx?id=39057) a ferramenta do Microsoft Download Center.
2. Execute a ferramenta a partir de um dos servidores primários (ou um de nós do cluster principal). O ficheiro .exe com o botão direito e, em seguida, escolha **executar como administrador**.
3. No **antes de começar**, especifique durante quanto pretende recolher dados. Recomendamos que execute a ferramenta durante o horário de produção para assegurar que os dados são representante. Se estiver apenas a tentar validar a conectividade de rede, pode recolher para apenas um minuto.

    ![](./media/site-recovery-capacity-planning-for-hyper-v-replication/image2.png)
4. No **detalhes de site primário**, especifique o nome do servidor ou o FQDN para um anfitrião autónomo ou para um cluster, especifique o FQDN do cliente aceitar ponto, o nome ou de qualquer nó no cluster do cluster e, em seguida, clique em **seguinte**. A ferramenta Deteta automaticamente o nome do servidor que é executada. A ferramenta escolherá VMs que podem ser monitorizadas para os servidores especificados.

    ![](./media/site-recovery-capacity-planning-for-hyper-v-replication/image3.png)
5. No **detalhes de Site de réplica**, se estiver a replicar para o Azure ou, se está a replicar para um datacenter secundário e ainda não configurar um servidor de réplica, selecione **ignorar testes que envolve o site réplica**. Se estiver a replicar para um datacenter secundário e configurou um tipo de réplica, introduza o FQDN do servidor autónomo ou o ponto de acesso de cliente para o cluster, **Server name (ou) extremidade de Mediador de réplicas do Hyper-V**.

    ![](./media/site-recovery-capacity-planning-for-hyper-v-replication/image4.png)
6. No **detalhes de réplica expandido**, ativar **ignorar os testes que envolve o site de réplica expandida**. Estes testes não são suportadas pela recuperação de sites.
7. No **escolha VMs replicar**, as ferramentas estabelece ligação ao servidor ou cluster e apresenta as VMs e discos em execução no servidor primário, de acordo com as definições que especificou no **detalhes de Site primário** página. VMs que já estão a ser ativada para replicação, ou que não estão em execução, não será apresentado. Selecione as VMs para o qual pretende recolher a métrica. Selecionar os VHDs automaticamente, recolhe dados para as VMs demasiado.
8. Se tiver configurado a um servidor de réplica ou cluster, no **informações de rede**, especifique a largura de banda WAN aproximada pensa que será utilizado entre os sites primário e réplica e selecione os certificados, se tiver configurado autenticação de certificado.

    ![](./media/site-recovery-capacity-planning-for-hyper-v-replication/image5.png)
9. No **resumo**, verifique as definições e clique em **seguinte** para começar a recolher métricas. Progresso da ferramenta e o estado é apresentado no **calcular a capacidade** página. Quando a ferramenta de conclusão da execução, clique em **Ver relatório** para ver o resultado. Por predefinição, os registos e relatórios são armazenados em **%systemdrive%\Users\Public\Documents\Capacity Planner**.

   ![](./media/site-recovery-capacity-planning-for-hyper-v-replication/image6.png)

## <a name="step-4-interpret-the-results"></a>Passo 4: Interpretar os resultados

Seguem-se as métricas importantes. Pode ignorar as métricas que não estão listadas aqui. Não está relevantes para a recuperação de Site.

### <a name="on-premises-to-on-premises-replication"></a>No local para replicação no local

* Impacto da replicação de computação principal do anfitrião, memória
* Impacto da replicação de principal, espaço de disco de armazenamento do anfitriões de recuperação, IOPS
* A largura de banda total necessária para replicação de diferenças (Mbps)
* Largura de banda de rede observado entre o anfitrião primário e o anfitrião de recuperação (Mbps)
* Sugestão para o número ideal de Active Directory transferências paralelas entre dois anfitriões/clusters

### <a name="on-premises-to-azure-replication"></a>No local para a replicação do Azure

* Impacto da replicação de computação principal do anfitrião, memória
* Impacto da replicação no espaço de disco de armazenamento do anfitrião primário, IOPS
* A largura de banda total necessária para replicação de diferenças (Mbps)

## <a name="more-resources"></a>Mais recursos
* Para obter informações detalhadas sobre a ferramenta, ler o documento que acompanha a transferência da ferramenta.
* Assista a obter instruções sobre a ferramenta no de Keith Mayer [blogue do TechNet](http://blogs.technet.com/b/keithmayer/archive/2014/02/27/guided-hands-on-lab-capacity-planner-for-windows-server-2012-hyper-v-replica.aspx).
* [Obtenha os resultados](site-recovery-performance-and-scaling-testing-on-premises-to-on-premises.md) dos nosso testes de desempenho para no local para a replicação de Hyper-V no local

## <a name="next-steps"></a>Passos seguintes

Depois de terminar planeamento de capacidade pode começar a implementar a recuperação de sites:

* [Replicar VMs de Hyper-V em nuvens VMM para o Azure](site-recovery-vmm-to-azure.md)
* [Replicar VMs de Hyper-V (sem VMM) para o Azure](site-recovery-hyper-v-site-to-azure.md)
* [Replicar VMs Hyper-V entre sites do VMM](site-recovery-vmm-to-vmm.md)

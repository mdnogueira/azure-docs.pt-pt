---
title: Voltar a proteger a partir do Azure para um site no local | Microsoft Docs
description: "Após a ativação pós-falha de VMs para o Azure, pode iniciar uma reativação pós-falha para colocar as VMs no local. Saiba como voltar a proteger antes de uma reativação pós-falha."
services: site-recovery
documentationcenter: 
author: rajani-janaki-ram
manager: gauravd
editor: 
ms.assetid: 44813a48-c680-4581-a92e-cecc57cc3b1e
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 06/05/2017
ms.author: rajanaki
ms.openlocfilehash: 17a43de3faaa3a146fa9d8f43d36545d6d82b274
ms.sourcegitcommit: 651a6fa44431814a42407ef0df49ca0159db5b02
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/28/2017
---
# <a name="reprotect-from-azure-to-an-on-premises-site"></a>Voltar a proteger a partir do Azure para um site no local



## <a name="overview"></a>Descrição geral
Este artigo descreve como voltar a proteger máquinas virtuais do Azure a partir do Azure para um site no local. Siga as instruções neste artigo quando estiver pronto para efetuar a cópia a máquinas virtuais VMware ou o Windows/Linux em servidores físicos depois de ter pós-falha no local do site para o Azure (conforme descrito em [VMware replicar máquinas virtuais e servidores físicos para o Azure com o Azure Site Recovery](site-recovery-failover.md)).

> [!WARNING]
> Não é possível reativação pós-falha depois de ter um [concluído migração](site-recovery-migrate-to-azure.md#what-do-we-mean-by-migration), mover uma máquina virtual para outro grupo de recursos ou eliminar a máquina virtual do Azure. Se desativar a proteção da máquina virtual, não é possível reativação pós-falha. Se a máquina virtual foi criada pela primeira vez no Azure (born na nuvem) não é possível Proteja-no local. A máquina deve ter sido protegido inicialmente no local e a ativação pós-falha para o Azure antes de reproteção.


Depois de voltar concluída e estiver a replicar máquinas virtuais protegidas, pode iniciar uma reativação pós-falha nas máquinas virtuais para colocá-los para o site no local.

> [!NOTE]
> Pode apenas reproteção e reativação pós-falha para um anfitrião ESXi. Não é possível a reativação pós-falha da máquina virtual como anfitriões Hyper-v, VMware estações de trabalho ou qualquer outra plataforma de virtualização.

Publique comentários ou perguntas no final deste artigo ou no [fórum de serviços de recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

Para uma descrição geral, veja o seguinte vídeo sobre como efetuar a ativação pós-falha do Azure para um site no local.
> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/VMware-to-Azure-with-ASR-Video5-Failback-from-Azure-to-On-premises/player]


## <a name="prerequisites"></a>Pré-requisitos

> [!IMPORTANT]
> Durante a ativação pós-falha para o Azure, o site no local poderá não estar acessível e, por conseguinte, o servidor de configuração pode ser não disponível ou encerramento. Durante a reproteção e a reativação pós-falha, o servidor de configuração no local deve ser em execução e no estado OK ligado.


Quando preparar para voltar a proteger máquinas virtuais, tomar ou considere as seguintes ações de pré-requisitos:

* Se um servidor vCenter gere as máquinas virtuais que pretende para a reativação pós-falha, certifique-se de que tem o [as permissões necessárias](site-recovery-vmware-to-azure-classic.md) para a deteção de máquinas virtuais em servidores do vCenter.

  > [!WARNING]
  > Se existirem instantâneos no destino principal no local ou a máquina virtual, irá falhar. Pode eliminar os instantâneos no destino principal antes de avançar para voltar a proteger. Os instantâneos da máquina virtual são intercalados automaticamente durante uma tarefa de reproteção.

* Antes de falhar novamente, crie dois componentes adicionais:

  * **Servidor de processos**: O [servidor de processos](site-recovery-vmware-setup-azure-ps-resource-manager.md) recebe dados de máquina virtual protegida no Azure e envia dados para o site no local. Uma rede de latência baixa é necessária entre o servidor de processos e a máquina virtual protegida. Assim, pode ter um servidor de processos no local, se estiver a utilizar uma ligação ExpressRoute do Azure ou um servidor de processos baseado no Azure e uma VPN.
  
  * **Servidor de destino mestre**: O servidor de destino mestre recebe dados de reativação pós-falha. O servidor de gestão no local que criou tem um servidor de destino mestre instalado por predefinição. No entanto, consoante o volume de tráfego de cópia falhou, poderá ter de criar um servidor de destino principal separado para reativação pós-falha.
    * [Uma máquina virtual Linux necessita de um servidor de destino principal do Linux](site-recovery-how-to-install-linux-master-target.md).
    * Máquina virtual do Windows precisa de um servidor de destino principal do Windows. Pode utilizar as máquinas no local processo servidor e o mestre de destino novamente.
    * O destino principal tem outros pré-requisitos listados em [coisas comuns para verificar um destino principal antes de reproteção](site-recovery-how-to-reprotect.md#common-things-to-check-after-completing-installation-of-the-master-target-server).

> [!NOTE]
> Todas as máquinas virtuais de um grupo de replicação deve ser do mesmo tipo de sistema operativo (todos os Windows ou Linux todas as). Um grupo de replicação com sistemas de operativos mistos não é atualmente suportado para reproteção e a reativação pós-falha no local. Isto acontece porque o destino principal deve ser do mesmo sistema operativo que a máquina virtual e todas as máquinas virtuais de um grupo de replicação deve ter o mesmo destino mestre. 

    

* Um servidor de configuração é necessário no local ao efetuar a cópia. Durante a reativação pós-falha, a máquina virtual tem de existir na base de dados de servidor de configuração. Caso contrário, a reativação pós-falha é bem-sucedida. 

> [!IMPORTANT]
> Certifique-se de que efetua cópias de segurança agendadas regularmente do seu servidor de configuração. Se ocorrer um desastre, restaure o servidor com o mesmo endereço IP, de modo a que funciona de reativação pós-falha.

> [!WARNING]
> Um grupo de replicação deve ter apenas VMs do Windows ou Linux VMs e não uma mistura de ambos porque todas as VMs num grupo replictaion utiliza o mesmo servidor de destino mestre e VM com Linux necessita de um servidor de destino principal do Linux e que aconselhado para a VM do Windows.

* Definir o `disk.EnableUUID=true` definição os parâmetros de configuração da máquina virtual destino principal no VMware. Se esta linha não existir, adicioná-lo. Esta definição é necessário para fornecer um UUID consistente para o disco de máquina virtual (VMDK) para que o se monta corretamente.

* *Não é possível utilizar o armazenamento vMotion no servidor de destino mestre*. Isto pode provocar a reativação pós-falha falhou. Não é possível iniciar a máquina virtual porque os discos não estão disponíveis. Para evitar este problema, exclua os servidores de destino principal da sua lista de vMotion.

* Adicionar uma nova unidade para o servidor de destino mestre: uma unidade de retenção. Adicione um novo disco e formatar o disco.


### <a name="frequently-asked-questions"></a>Perguntas mais frequentes

#### <a name="why-do-i-need-a-s2s-vpn-or-an-expressroute-connection-to-replicate-data-back-to-the-on-premises-site"></a>Por que motivo é necessário uma ligação ExpressRoute ou de uma VPN S2S para replicar dados para o site no local?
Enquanto que a replicação no local para o Azure pode acontecer através da Internet ou de uma ligação de ExpressRoute tem um peering público, e reativação pós-falha necessitam de uma VPN de site a site (S2S) replicar os dados. Forneça a rede para que as máquinas virtuais de efetuada a ativação pós-falha no Azure pode alcançar o servidor de configuração no local (ping). Também pode implementar um servidor de processos na rede da máquina virtual efetuada a ativação pós-falha do Azure. Este servidor de processos deve também conseguir comunicar com o servidor de configuração no local.

#### <a name="when-should-i-install-a-process-server-in-azure"></a>Quando deve instalar um servidor de processos no Azure?


As máquinas virtuais do Azure que pretende voltar a proteger enviar dados de replicação para um servidor de processos. Configure a rede para que as máquinas virtuais no Azure podem aceder ao servidor de processo.

Pode implementar um servidor de processos no Azure ou utilizar o servidor de processos existentes que utilizou durante a ativação pós-falha. O ponto importante a ter em consideração é a latência para enviar os dados das máquinas virtuais no Azure para o servidor de processos.

Se tiver configurada uma ligação do ExpressRoute, pode utilizar um servidor de processos no local para enviar dados porque a latência entre a máquina virtual e o servidor de processos é baixa.

 ![Diagrama de arquitetura para o ExpressRoute](./media/site-recovery-failback-azure-to-vmware-classic/architecture.png)



No entanto, se tiver apenas uma VPN S2S, recomendamos que implementar o servidor de processos no Azure.

 ![Diagrama de arquitetura para VPN](./media/site-recovery-failback-azure-to-vmware-classic/architecture2.png)


Lembre-se de que a replicação a partir do Azure no local pode acontecer apenas através de S2S VPN ou o peering privado da sua rede de ExpressRoute. Certifique-se de que a largura de banda suficiente está disponível através do canal essa rede.

Para obter informações sobre como instalar um servidor de processos baseado no Azure, consulte [gerir um servidor de processos em execução no Azure](site-recovery-vmware-setup-azure-ps-resource-manager.md).

> [!TIP]
> Recomendamos que utilize um servidor de processos baseado no Azure durante a reativação pós-falha. O desempenho de replicação é superior se o servidor de processos estiver próximo da máquina virtual em replicação (a máquina de efetuada a ativação pós-falha no Azure). No entanto, durante a prova de conceito (POC) ou de demonstração, pode utilizar o servidor de processos no local, juntamente com o ExpressRoute com o peering privado para concluir a POC mais rápida.

> [!NOTE]
> Replicação no local para o Azure pode acontecer apenas através de internet ou ExpressRoute com o peering público. A replicação do Azure no local pode acontecer apenas através de S2S VPN ou ExpressRoute com o peering privado


#### <a name="what-ports-should-i-open-on-different-components-so-that-reprotection-can-work"></a>As portas que deve abro nos componentes diferentes para que só pode funcionar?

![Portas para ativação pós-falha e a reativação pós-falha](./media/site-recovery-failback-azure-to-vmware-classic/Failover-Failback.png)

#### <a name="which-master-target-server-should-i-use-for-reprotection"></a>O servidor de destino mestre deve utilizar para voltar?
É necessário um servidor de destino principal no local para receber dados do servidor de processos e, em seguida, escrever VMDK da máquina de virtual no local. Se estiver a proteger máquinas virtuais do Windows, é necessário um servidor de destino principal do Windows. Pode reutilizar o destino de servidor e o mestre de processo no local<!-- !todo component -->. Para máquinas virtuais do Linux, tem de configurar um destino principal do Linux no local adicionais.


Para informações sobre como instalar um servidor de destino mestre, consulte:

* [Como instalar o servidor de destino principal do Windows](site-recovery-vmware-to-azure.md)
* [Como instalar o servidor de destino principal do Linux](site-recovery-how-to-install-linux-master-target.md)


#### <a name="what-datastore-types-are-supported-on-the-on-premises-esxi-host-during-failback"></a>Que tipos de arquivo de dados são suportados no anfitrião ESXi no local durante a reativação pós-falha?

Atualmente, o Azure Site Recovery suporta a falhar back apenas a um sistema de ficheiros de máquina virtual (VMFS) ou o arquivo de dados de vSAN. Não é suportado um arquivo de dados do NFS. Devido a esta limitação, a seleção de arquivo de dados de entrada de reproteção ecrã está vazio no caso de NFS datastores ou que mostra o arquivo de dados de vSAN, mas ocorre uma falha durante a tarefa. Se tenciona voltar a falhar, pode criar um arquivo de dados VMFS no local e não-lo. Este reativação pós-falha fará com que uma transferência completa da VMDK.

### <a name="common-things-to-check-after-completing-installation-of-the-master-target-server"></a>Comuns aspetos a verificar depois de concluir a instalação do servidor de destino mestre

* Se a máquina virtual está presente no local no vCenter server, o servidor de destino mestre precisa de acesso à VMDK da máquina de virtual no local. É necessário acesso ao escrever os dados replicados para discos da máquina virtual. Certifique-se de que o arquivo de dados da máquina de virtual no local está montado no anfitrião de destino principal com acesso de leitura/escrita.

* Se a máquina virtual não está presente no local no vCenter server, tem de criar uma nova máquina virtual durante a o serviço de recuperação de Site. Esta máquina virtual é criada no anfitrião do ESX no qual criar o destino principal. Escolha o anfitrião do ESX cuidadosamente, para que a máquina virtual de reativação pós-falha é criada no anfitrião que pretende.

* *Não é possível utilizar vMotion de armazenamento para o servidor de destino mestre*. Isto pode provocar a reativação pós-falha falhou. Não é possível iniciar a máquina virtual porque os discos não estão disponíveis.

  > [!WARNING]
  > Se um destino principal sofre uma tarefa de vMotion de armazenamento após só, os discos de máquinas virtuais protegidas que estão anexados ao destino principal migrar para o destino da tarefa vMotion. Se tentar falhar novamente após este, Desanexação do disco falha porque os discos não forem encontrados. Em seguida, torna-se difícil localizar os discos nas suas contas do storage. Terá de encontrá-los manualmente e anexe-os para a máquina virtual. Depois disso, a máquina virtual no local pode ser reiniciada.

* Adicione uma unidade de retenção para o servidor de destino principal existente do Windows. Adicione um novo disco e formatar o disco. A unidade de retenção é utilizada para parar os pontos no tempo quando a máquina virtual são replicados para o site no local. Seguem-se alguns critérios de uma unidade de retenção. Sem estes critérios, a unidade não será listada para o servidor de destino principal.

   * O volume não é utilizado para qualquer outra finalidade, tais como um destino de replicação.

   * O volume não está no modo de bloqueio.

   * O volume não é um volume de cache. A instalação de destino mestre não deve existir nesse volume. O volume de instalação personalizada para o destino de servidor e o mestre de processo não é elegível para um volume de retenção. Quando o servidor de processos e o destino principal estão instalados num volume, o volume é um volume de cache de destino principal.

   * O tipo de sistema de ficheiros do volume não é FAT ou FAT32.

   * A capacidade do volume for diferente de zero.

   * O volume de retenção predefinido para o Windows é o volume de R.

   * O volume de retenção predefinido para Linux é /mnt/retention.

  > [!IMPORTANT]
  > Tem de adicionar uma nova unidade se estiver a utilizar uma máquina de servidor de configuração do servidor de processo existente ou uma escala ou um computador de servidor de destino do processo servidor/principal. A nova unidade deve satisfazer os requisitos de anteriores. Se a unidade de retenção não estiver presente, não aparecem na lista pendente de seleção no portal. Depois de adicionar uma unidade para o destino principal no local, que demora até 15 minutos para a unidade a aparecer na seleção no portal. Também pode atualizar o servidor de configuração se a unidade não aparecer após 15 minutos.

* Uma máquina virtual do Linux efetuada a ativação pós-falha requer um servidor de destino principal do Linux. Máquina virtual efetuada a ativação pós-falha do Windows necessita de um servidor de destino principal do Windows.

* Instale as ferramentas do VMware no servidor de destino mestre. Sem as ferramentas do VMware, não é possível detetar o datastores no anfitrião ESXi do destino principal.

* Ativar o `disk.EnableUUID=true` parâmetro na máquina virtual de destino mestre, utilizando as propriedades do vCenter. <!-- !todo Needs link. -->

* O destino principal deve ter pelo menos um arquivo de dados VMFS ligado. Se não houver nenhum, o **arquivo de dados** entrada na página de reproteção estará vazia, e não é possível continuar.

* O servidor de destino principal não pode ter instantâneos de discos. Se existirem instantâneos, só e a reativação pós-falha falharem.

* O destino principal não pode ter um controlador de Paravirtual SCSI. O controlador só pode ser um controlador de LSI Logic. Sem um controlador de LSI Logic só irá falhar.

* Em qualquer instância especificada, o destino principal pode ter Step-by-atmst 60 discos ligados ao mesmo. Se o número de máquinas virtuais que está a ser proteger ao destino principal no local têm um número total de soma de mais de 60 discos, em seguida, reprotects ao destino principal começarão a falhar. Certifique-se de que tem suficiente mestre ranhuras de disco de destino ou implementar servidores de destino mestre adicionais.

<!--
### Failback policy
To replicate back to on-premises, you will need a failback policy. This policy get automatically created when you create a forward direction policy. Note that

1. This policy gets auto associated with the configuration server during creation.
2. This policy is not editable.
3. The set values of the policy are (RPO Threshold = 15 Mins, Recovery Point Retention = 24 Hours, App Consistency Snapshot Frequency = 60 Mins)
   ![](./media/site-recovery-failback-azure-to-vmware-new/failback-policy.png)

-->

## <a name="steps-to-reprotect"></a>Passos para voltar a proteger

> [!NOTE]
> Depois de uma máquina virtual arranca no Azure, demora algum tempo para o agente registar novamente para o servidor de configuração (até 15 minutos). Durante este tempo, ocorre uma falha e uma mensagem de erro indica que o agente não está instalado. Aguarde alguns minutos e, em seguida, tente voltar novamente.



1. No **cofre** > **replicado itens**, faça duplo clique na máquina virtual que é foi efetuada a ativação pós-falha e, em seguida, selecione **voltar a proteger**. Também pode clique na máquina e selecione **voltar a proteger** de botões de comando.
2. No painel, repare que a direção da proteção, **do Azure para o local**, já está selecionada.
3. No **servidor de destino mestre** e **servidor de processos**, selecione o servidor de destino principal no local e o servidor de processos.
4. Para **arquivo de dados**, selecione o arquivo de dados para o qual pretende recuperar o discos no local. Esta opção é utilizada quando a máquina virtual no local é eliminada e tem de criar novos discos. Esta opção é ignorada se os discos já existem, mas ainda tem de especificar um valor.
5. Escolha a unidade de retenção.
6. A política de reativação pós-falha é selecionada automaticamente.
7. Clique em **OK** para começar a só. Uma tarefa começa a replicar a máquina virtual do Azure para o site no local. Pode controlar o progresso no **tarefas** separador.

Se pretender recuperar para uma localização alternativa (quando a máquina virtual no local é eliminada), selecione a unidade de retenção e arquivo de dados que estão configurados para o servidor de destino principal. Quando houver uma para o site no local, as máquinas virtuais de VMware no plano de proteção de reativação pós-falha utilizar o mesmo arquivo de dados como servidor de destino mestre. Uma nova máquina virtual, em seguida, é criada no vCenter.

Se pretender recuperar a máquina virtual no Azure para uma máquina virtual no local, monte datastores da máquina de virtual no local com acesso de leitura/escrita no anfitrião ESXi de servidor de destino mestre.
    ![Caixa de diálogo de voltar a proteger](./media/site-recovery-failback-azure-to-vmware-new/reprotectinputs.png)

Também pode voltar a proteger no nível de um plano de recuperação. Um grupo de replicação pode proteger apenas através de um plano de recuperação. Quando voltar a proteger através da utilização de um plano de recuperação, tem de fornecer os valores para cada máquina protegida.

> [!NOTE]
> Utilize o mesmo servidor de destino mestre para voltar a proteger um grupo de replicação. Se utilizar um servidor de destino mestre diferente para voltar a proteger um grupo de replicação, o servidor não é possível fornecer um ponto de comuns no tempo.

> [!NOTE]
> As máquinas no local é desativada durante só. Isto ajuda a assegurar a consistência de dados durante a replicação. Não ative a máquina virtual após a conclusão da só.

Depois do que for bem sucedida, a máquina virtual entrará num estado protegido.

## <a name="next-steps"></a>Passos seguintes

Depois da máquina virtual entrou num estado protegido, pode [iniciar uma reativação pós-falha](site-recovery-how-to-failback-azure-to-vmware.md#steps-to-fail-back). 

A reativação pós-falha irá encerrar a máquina virtual no Azure e efetuar o arranque de máquina virtual no local. Prevê algum período de indisponibilidade para a aplicação. Escolha uma hora de reativação pós-falha quando a aplicação pode tolerar o período de indisponibilidade.

## <a name="common-problems"></a>Problemas comuns

* Se utilizou um modelo para criar as máquinas virtuais, certifique-se de que cada máquina virtual tem o seu próprio UUID de discos. Se o UUID no local da máquina de virtual entra em conflito com que o destino principal porque ambos foram criadas a partir do mesmo modelo, irá falhar. Implemente outro destino mestre não tiver sido criado a partir do mesmo modelo.

* Se executar uma deteção de utilizadores de só de leitura do vCenter e proteger máquinas virtuais, proteção com êxito e a ativação pós-falha funciona. Que, durante a ativação pós-falha falha porque não é possível detetar o datastores. Um sintoma é que o datastores não estão listados durante só. Para resolver este problema, pode atualizar a credencial do vCenter com uma conta apropriada que tenha permissões e repita a tarefa. Para obter mais informações, consulte [máquinas virtuais VMware replicar e servidores físicos para o Azure com o Azure Site Recovery](site-recovery-vmware-to-azure-classic.md).

* Quando uma máquina virtual Linux a efetuar pós-falha e executá-la no local, pode ver que o pacote do Gestor de rede foi desinstalado do computador. Esta desinstalação acontece porque o pacote do Gestor de rede é removido quando a máquina virtual é recuperada no Azure.

* Quando uma máquina virtual Linux está configurada com um endereço IP estático e ativação pós-falha do Azure, o endereço IP é adquirido de DHCP. Quando efetuar a ativação pós-falha no local, a máquina virtual continua a utilizar DHCP para adquirir o endereço IP. Manualmente iniciar sessão máquina e definir o endereço IP para um endereço estático se necessário. Máquina virtual do Windows pode adquirir novamente o IP estático.

* Se utilizar a edição gratuita ESXi 5.5 ou edição gratuita do hipervisor vSphere 6, ativação pós-falha é bem-sucedida, mas não ativar a reativação pós-falha. Para ativar a reativação pós-falha, atualize a licença de avaliação de um programa.

* Se não conseguirem contactar o servidor de configuração do servidor de processos, utilize o Telnet para verificar a conectividade ao servidor de configuração na porta 443. Também pode tentar executar o ping do servidor de configuração do servidor de processos. Um servidor de processos também deve ter um heartbeat quando está ligado ao servidor de configuração.

* Se estiver a tentar para a reativação pós-falha um vCenter alternativo, certifique-se de que o seu novo vCenter e o servidor de destino principal são detetados. Um típico sintoma é que o datastores não são acessíveis ou visível no **Proteja** caixa de diálogo.

* Um servidor do Windows Server 2008 R2 SP1 que está protegido como um servidor físico no local não pode ser falhado novamente a partir do Azure para um site no local.

### <a name="common-error-codes"></a>Códigos de erro comuns

#### <a name="error-code-95226"></a>Código de erro 95226

*Reproteção falhou porque a máquina virtual do Azure não conseguiu aceder ao servidor de configuração no local.*

Isto acontece quando 
1. A máquina virtual do Azure não foi possível alcançar o servidor de configuração no local e, por conseguinte, pode não ser detetada e registada no servidor de configuração. 
2. O serviço de aplicação do InMage Scout na máquina virtual do Azure que tem de estar em execução para comunicar o servidor de configuração no local poderá não estar em execução ativação pós-falha de post.

Para resolver este problema
1. Tem de garantir que a rede da máquina virtual do Azure está configurada de forma a que a máquina virtual pode comunicar com o servidor de configuração no local. Para tal, configurar uma VPN de Site a Site para o seu centro de dados no local ou configurar uma ligação ExpressRoute com o peering privado na rede virtual da máquina virtual do Azure. 
2. Se já tiver uma rede configurada de forma a que a máquina virtual do Azure pode comunicar com o servidor de configuração no local, em seguida, iniciar sessão na máquina virtual e consulte o 'InMage Scout Application Service'. Se observar a que o InMage Scout Application Service não está em execução, em seguida, inicie manualmente o serviço e certifique-se de que o tipo de início do serviço está definido como automático.

### <a name="error-code-78052"></a>Código de erro 78052
Reproteção falha apresentando a mensagem de erro: *não foi possível concluir a proteção da máquina virtual.*

Isto pode acontecer devido a dois razões
1. A máquina virtual que são trocar é um Windows Server 2016. Está atualmente este sistema operativo não é suportado para reativação pós-falha, mas será suportado muito em breve.
2. Já existe uma máquina virtual com o mesmo nome no mestre de servidor de destino estão a falhar novamente.

Para resolver este problema pode selecionar um servidor de destino mestre diferente num anfitrião diferente, para que o reproteção irá criar a máquina num anfitrião diferente, onde os nomes estão em conflito. Também pode vMotion o destino principal para outro anfitrião onde não acontece a colisão de nomes. Se a máquina virtual existente é uma máquina stray, pode apenas alterá-la para que a nova máquina virtual podem ser criada no mesmo anfitrião ESXi.

### <a name="error-code-78093"></a>Código de erro 78093

*A VM não está em execução, num estado bloqueado ou não está acessível.*

Para voltar a proteger uma falha na ativação pós-falha da máquina virtual no local, terá de máquina virtual do Azure em execução. Isto é para que o serviço de mobilidade regista com o servidor de configuração no local e pode iniciar a replicação através da comunicação com o servidor de processos. Se o computador estiver numa rede incorreta ou em execução não (estado bloqueado ou encerramento), o servidor de configuração não consegue contactar o serviço de mobilidade na máquina virtual para começar a reproteção. Pode reiniciar a máquina virtual para que possa começar a comunicar back no local. Reinicie a tarefa de reproteção depois de iniciar a máquina virtual do Azure

### <a name="error-code-8061"></a>Código de erro 8061

*O arquivo de dados não está acessível a partir do anfitrião ESXi.*

Consulte o [mestra pré-requisitos de destino](site-recovery-how-to-reprotect.md#common-things-to-check-after-completing-installation-of-the-master-target-server) e o [suporta datastores](site-recovery-how-to-reprotect.md#what-datastore-types-are-supported-on-the-on-premises-esxi-host-during-failback) para reativação pós-falha


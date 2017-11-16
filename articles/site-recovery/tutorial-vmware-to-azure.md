---
title: "Configure a recuperação de desastre para o Azure para as VMs de VMware no local com o Azure Site Recovery | Microsoft Docs"
description: "Saiba como configurar a recuperação após desastre para o Azure para as VMs de VMware no local com o serviço do Azure Site Recovery."
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/01/2017
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 461feb952f7e2eddba9c7218b3463868e8cb7965
ms.sourcegitcommit: c25cf136aab5f082caaf93d598df78dc23e327b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/15/2017
---
# <a name="set-up-disaster-recovery-to-azure-for-on-premises-vmware-vms"></a>Configure a recuperação de desastre para o Azure para as VMs de VMware no local

Este tutorial mostra como configurar a recuperação após desastre para o Azure para a VM de VMware no local Windows em execução. Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar um cofre dos serviços de recuperação para a recuperação de sites
> * Configurar a origem e ambientes de replicação de destino
> * Criar uma política de replicação
> * Ativar a replicação para uma VM

Este é o terceiro tutorial uma série. Este tutorial parte do princípio de que já concluiu as tarefas nos tutoriais anteriores:

1. [Preparar o Azure](tutorial-prepare-azure.md)
2. [Preparar o VMware no local](tutorial-prepare-on-premises-vmware.md)

Antes de começar, é útil [rever a arquitetura](concepts-vmware-to-azure-architecture.md) para cenário de recuperação após desastre.

## <a name="configure-vmware-account-permissions"></a>Configurar permissões de conta de VMware

1. Crie uma função ao nível do vCenter. Atribua o nome de função de **Azure_Site_Recovery**.
2. Atribuir as permissões seguintes para o **Azure_Site_Recovery** função.

   **Tarefa** | **Permissões de função /** | **Detalhes**
   --- | --- | ---
   **Deteção VM** | Objeto de centro de dados –> Propagate ao objeto do subordinado função = só de leitura | Pelo menos um utilizador só de leitura.<br/><br/> Utilizador atribuído ao nível do datacenter e tem acesso a todos os objetos no Centro de dados.<br/><br/> Para restringir o acesso, atribua o **sem acesso** função com o **Propagate para subordinado** objeto, para os objetos subordinados (anfitriões vSphere, datastores, VMs e as redes).
   **Total de replicação, a ativação pós-falha, a reativação pós-falha** |  Objeto de centro de dados –> Propagate ao objeto do subordinado função = Azure_Site_Recovery<br/><br/> Arquivo de dados -> atribuir espaço em, procurar o arquivo de dados, as operações de baixo nível de ficheiro, remova o ficheiro, atualizar ficheiros de máquina virtual<br/><br/> Rede -> atribuição de rede<br/><br/> Recursos -> VM atribuir ao agrupamento de recursos, migrar alimentado desligar a VM, migrar alimentado na VM<br/><br/> Tarefas -> tarefas de criação, a tarefa de atualização<br/><br/> Configuração -> de máquina virtual<br/><br/> Máquina virtual -> interagir -> pergunta de resposta, a ligação de dispositivos, configurar suporte de dados do CD, configurar o suporte de dados de disquetes, desligar, ligar, instalação de ferramentas do VMware<br/><br/> Máquina virtual -> inventário -> criar, registar, anular o registo<br/><br/> Máquina virtual -> aprovisionamento -> Permitir transferências de máquina virtual, permitem carregar ficheiros de máquina virtual<br/><br/> Máquina virtual -> instantâneos -> Remover instantâneos | Utilizador atribuído ao nível do datacenter e tem acesso a todos os objetos no Centro de dados.<br/><br/> Para restringir o acesso, atribua o **sem acesso** função com o **Propagate para subordinado** objeto, para os objetos subordinados (anfitriões vSphere, datastores, VMs e as redes).

3. Crie um utilizador no anfitrião do servidor ou vSphere do vCenter. Atribua a função ao utilizador.

## <a name="specify-what-you-want-to-replicate"></a>Especifique que pretende replicar

O serviço de mobilidade tem de ser instalado em cada VM que pretende replicar. Recuperação de site instala automaticamente este serviço ao ativar a replicação para a VM. Para a instalação automática, terá de preparar uma conta que a recuperação de sites irá utilizar para aceder a VM.

Pode utilizar um domínio ou conta local. Para VMs com Linux, a conta deve ser raiz no servidor de Linux de origem. Para VMs do Windows, se não estiver a utilizar uma conta de domínio, desative o controlo de acesso de utilizador remoto no computador local:

  - No registery, sob **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System**, adicione a entrada DWORD **LocalAccountTokenFilterPolicy** e defina o valor para 1.

## <a name="set-up-the-source-environment"></a>Configurar o ambiente de origem

Configurar o ambiente de origem é composta por transferir a configuração do Site Recovery unificado, configurar o servidor de configuração e registando-a no cofre e detetar VMs.

O servidor de configuração é um único local VM de VMware para alojar todos os componentes do Site Recovery. Esta VM é executado o servidor de configuração, o servidor de processos e o servidor de destino principal.

- O servidor de configuração coordena as comunicações entre o local e o Azure, e gere a replicação de dados.
- O servidor de processos atua como um gateway de replicação. Recebe dados de replicação, otimiza-os com a colocação em cache, compressão e encriptação, e envia-os para o armazenamento do Azure. O servidor de processos também instala o serviço de mobilidade em VMs que pretende replicar, e efetua a descoberta automática de VMs nos servidores do VMware no local.
- O servidor de destino mestre processa dados de replicação durante a reativação pós-falha a partir do Azure.

O servidor de configuração VM deve ser uma VM de VMware altamente disponível que cumpre os seguintes requisitos:

| **Requisito** | **Detalhes** |
|-----------------|-------------|
| Número de núcleos de CPU| 8 |
| RAM | 12 GB |
| Número de discos | 3 - disco do SO, disco de cache do servidor de processos, unidade de retenção (para reativação pós-falha) |
| Espaço livre no disco (cache do servidor de processos) | 600 GB |
| Espaço livre no disco (disco de retenção) | 600 GB |
| Versão do sistema operativo | Windows Server 2012 R2 |
| Região do sistema operativo | Inglês (en-us) |
| Versão do VMware vSphere PowerCLI | [PowerCLI 6.0](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1 "PowerCLI 6.0") |
| Funções do Windows Server | Não ative estas funções: serviços de domínio do Active Directory, serviços de informação de Internet, Hyper-V |
| Tipo NIC | VMXNET3 |
| Tipo de endereço IP | Estático |
| Portas | 443 (Canal de controlo e orquestração)<br/>9443 (Transporte de dados)|

O servidor de configuração de VM, certifique-se de que o relógio do sistema está sincronizado com um servidor de tempo.
Tempo têm de ser sincronizado para em 15 minutos. Se a diferença de tempo é superior a 15 minutos, o programa de configuração falha.

Certifique-se de que o servidor de configuração pode aceder a estes URLs:

   [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]
    
    - Quaisquer regras de firewall baseadas no endereço IP devem permitir a comunicação com o Azure.

- Permita os [Intervalos de IP do Datacenter do Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653) e a porta HTTPS (443).
    - Permita intervalos de endereços IP para a região do Azure da sua subscrição e para EUA oeste (utilizado para gestão de identidade e controlo de acesso).

Quaisquer regras de firewall baseadas no endereço IP devem permitir a comunicação para [intervalos de IP do Datacenter do Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653)e as portas 443 (HTTPS) e 9443 (replicação de dados). Lembre-se de que permitir intervalos de endereços IP para a região do Azure da sua subscrição e para EUA oeste (utilizado para gestão de identidade e controlo de acesso).

### <a name="download-the-site-recovery-unified-setup"></a>Transferir o Site de recuperação Unified programa de configuração

1. Abra o [portal do Azure](https://portal.azure.com) e clique em **todos os recursos**.
2. Clique no cofre do serviço de recuperação com o nome **ContosoVMVault**.
3. Clique em **recuperação de Site** > **preparar infraestrutura** > **objetivo de proteção**.
4. Selecione **no local** para onde as máquinas estão localizadas, **para o Azure** para onde pretende replicar as máquinas e **Sim, com o VMware vSphere hipervisor**. Em seguida, clique em **OK**.
5. No painel de origem de preparação, clique em **+ o servidor de configuração**.
6. No **Adicionar servidor**, verifique se **servidor de configuração** aparece no **tipo de servidor**.
7. Transfira o ficheiro de instalação de configuração do Unified Site Recovery.
8. Transferir a chave de registo do cofre. Isto tem quando executar a configuração do Unified. A chave é válida durante cinco dias depois de gerá-la.

   ![Configurar a origem](./media/tutorial-vmware-to-azure/source-settings.png)

### <a name="set-up-the-configuration-server"></a>Configurar o servidor de configuração

1. Execute o ficheiro de instalação Configuração Unificada.
2. No **antes de começar**, selecione **instalar o servidor de configuração e o servidor de processos** , em seguida, clique em **seguinte**.

3. No **licença de Software de terceiros**, clique em **aceito** para transferir e instalar o MySQL, em seguida, clique em **seguinte**.

4. Em **Registo**, selecione a chave de registo que transferiu a partir do cofre.

5. Em **Definições da Internet**, especifique a forma como o Fornecedor em execução no servidor estabelece ligação ao Azure Site Recovery através da Internet.

   - Se pretende estabelecer ligação com o proxy que está atualmente configurado no computador, selecione **ligar ao Azure Site Recovery com um servidor proxy**.
   - Se pretender que o fornecedor se ligue diretamente, selecione **ligar diretamente ao Azure Site Recovery sem um servidor proxy**.
   - Se o proxy existente requer autenticação ou se pretender utilizar um proxy personalizado para a ligação de fornecedor, selecione **ligar com definições de proxy personalizado**e especifique o endereço, porta e credenciais.

   ![Firewall](./media/tutorial-vmware-to-azure/combined-wiz4.png)

6. Em **Verificação de Pré-requisitos**, a Configuração executa uma verificação para se certificar de que a instalação pode ser executada. Se for apresentado um aviso sobre a **Verificação de sincronização de hora global**, certifique-se de que a hora no relógio do sistema (definições de **Data e Hora**) é a mesma que o fuso horário.

   ![Pré-requisitos](./media/tutorial-vmware-to-azure/combined-wiz5.png)

7. Em **Configuração do MySQL**, crie as credenciais para iniciar sessão na instância de servidor do MySQL que está instalada.

8. No **ambiente detalhes**, selecione **Sim** para proteger as VMs de VMware. Programa de configuração verifica se o PowerCLI 6.0 está instalada.

9. Em **Localização de Instalação**, selecione onde pretende instalar os binários e armazenar a cache. A unidade que selecionar tem de ter, pelo menos, 5 GB de espaço disponível no disco, mas recomenda-se uma unidade de cache com, pelo menos, 600 GB de espaço livre.

10. Em **Seleção de Rede**, especifique o serviço de escuta (placa de rede e porta SSL) no qual o servidor de configuração envia e recebe dados de replicação. A porta 9443 é a porta predefinida utilizada para envio e receção de tráfego de replicação, mas pode modificar este número de porta para melhor corresponda às necessidades do seu ambiente. Podemos também abrir a porta 443, que é utilizada para orquestrar as operações de replicação. Não utilize a porta 443 para enviar ou receber tráfego de replicação.

11. Em **Resumo**, consulte as informações e clique em **Instalar**. A configuração instala o servidor de configuração e regista-o serviço do Azure Site Recovery.

    ![Resumo](./media/tutorial-vmware-to-azure/combined-wiz10.png)

    Quando a instalação estiver concluída, é gerada uma frase de acesso. Irá precisar dela, quando ativar a replicação, por isso, copie-a e mantenha-a numa localização segura. O servidor é apresentado no **definições** > **servidores** painel no cofre.

### <a name="configure-automatic-discovery"></a>Configurar a deteção automática

Detetar VMs, o servidor de configuração tem de ligar aos servidores do VMware no local. Para efeitos deste tutorial, adicione o servidor vCenter ou anfitriões vSphere, utilizando uma conta que tenha privilégios de administrador no servidor.

1. No seu servidor de configuração, iniciar **CSPSConfigtool.exe**. Está disponível como um atalho no ambiente de trabalho e encontra-se na pasta *localização de instalação*\home\svsystems\bin.

2. Clique em **Gerir Contas** > **Adicionar Conta**.

   ![Adicionar conta](./media/tutorial-vmware-to-azure/credentials1.png)

3. Em **Detalhes da Conta**, adicione a conta que será utilizada para a deteção automática.

   ![Detalhes](./media/tutorial-vmware-to-azure/credentials2.png)

Para adicionar um servidor:

1. Abra o [portal do Azure](https://portal.azure.com) e clique em **todos os recursos**.
2. Clique no cofre do serviço de recuperação com o nome **ContosoVMVault**.
3. Clique em **recuperação de sites** > **preparar infraestrutura** > **origem**
4. Selecione **+ vCenter** para ligar ao vCenter server ou vSphere ESXi anfitrião.
5. No **adicionar vCenter**, especifique um nome amigável para o servidor. Em seguida, especifique o endereço IP ou FQDN.
6. Deixe a porta definida como 443, a menos que os servidores do VMware escutam os pedidos numa porta diferente.
7. Selecione a conta a utilizar para ligar ao servidor. Clique em **OK**.

Recuperação de sites liga-se aos servidores do VMware com as definições especificadas e Deteta as VMs.

> [!NOTE]
> Pode demorar 15 minutos ou mais para o nome da conta aparecer no portal. Para atualizar imediatamente, clique em **Servidores de Configuração** > ***nome do servidor*** > **Atualizar Servidor**.

## <a name="set-up-the-target-environment"></a>Configurar o ambiente de destino

Selecione e certifique-se de recursos de destino.

1. Clique em **Preparar a infraestrutura** > **Destino** e selecione a subscrição do Azure que pretende utilizar.
2. Especifique se o seu modelo de implementação de destino é baseado no Resource Manager ou clássico.
3. A Recuperação de Sites verifica que tem uma ou mais contas de armazenamento e redes do Azure compatíveis.

   ![Alvo](./media/tutorial-vmware-to-azure/storage-network.png)

## <a name="create-a-replication-policy"></a>Criar uma política de replicação

1. Abra o [portal do Azure](https://portal.azure.com) e clique em **todos os recursos**.
2. Clique no cofre do serviço de recuperação com o nome **ContosoVMVault**.
3. Para criar uma política de replicação, clique em **infraestrutura de recuperação de Site** > **políticas de replicação** > **+ política de replicação**.
4. No **criar política de replicação**, especifique um nome de política **VMwareRepPolicy**.
5. No **limiar RPO**, utilizar a predefinição de 60 minutos. Este valor define com que frequência são criados pontos de recuperação. É gerado um alerta se a replicação contínua excede este limite.
6. No **retenção do ponto de recuperação**, utilizar a predefinição de 24 horas para o período de retenção da janela de retenção para cada ponto de recuperação. Para este tutorial, iremos selecionar 72 horas. VMs replicadas podem ser recuperadas para qualquer ponto numa janela.
7. No **frequência de instantâneos consistentes com aplicação**, utilizar a predefinição de 60 minutos para a frequência de que os instantâneos consistentes com aplicações foram criados. Clique em **OK** para criar uma política.

   ![Política](./media/tutorial-vmware-to-azure/replication-policy.png)

A política é automaticamente associada ao servidor de configuração. Por predefinição, uma política correspondente é criada automaticamente para reativação pós-falha. Por exemplo, se a política de replicação é **rep política** a política de reativação pós-falha será **rep-política-reativação pós-falha**. Esta política não é utilizada depois de iniciar uma reativação pós-falha a partir do Azure.

## <a name="enable-replication"></a>Ativar a replicação

Recuperação de site instala o serviço de mobilidade quando a replicação está ativada para uma VM. Pode demorar 15 minutos ou já para as alterações que tenha efeito e aparecer no portal.

Ative a replicação da seguinte forma:

1. Clique em **replicar aplicação** > **origem**.
2. No **origem**, selecione o servidor de configuração.
3. No **máquina tipo**, selecione **máquinas virtuais**.
4. No **vCenter/vSphere hipervisor**, selecione o servidor vCenter que gere o anfitrião do Vcenter ou selecione o anfitrião.
5. Selecione o servidor de processos (servidor de configuração). Clique IThen **OK**.
6. No **destino**, selecione a subscrição e o grupo de recursos no qual pretende criar a ativação pós-falha das VMs. Escolha o modelo de implementação que pretende utilizar no Azure (clássica ou recurso management), para a ativação pós-falha das VMs com falhas.
7. Selecione a conta de armazenamento do Azure que pretende utilizar para replicar os dados.
8. Selecione a rede e a sub-rede do Azure às quais as VMs do Azure se vão ligar quando forem criadas após a ativação pós-falha.
9. Selecione **Configurar agora para as máquinas selecionadas** para aplicar a definição de rede a todas as máquinas selecionadas para proteção. Selecione **Configurar mais tarde** para selecionar a rede do Azure por máquina.
10. Em **Máquinas Virtuais** > **Selecionar máquinas virtuais**, clique e selecione cada máquina que pretende replicar. Só pode selecionar máquinas para as quais a replicação pode ser ativada. Em seguida, clique em **OK**.
11. No **propriedades** > **configurar propriedades**, selecione a conta que será utilizada pelo servidor de processos para instalar automaticamente o serviço de mobilidade na máquina.
12. No **as definições de replicação** > **configurar as definições de replicação**, certifique-se de que a política de replicação correto está selecionada.
13. Clique em **ativar a replicação**.

Pode controlar o progresso do **ativar proteção** da tarefa no **definições** > **tarefas** > **tarefas de recuperação de Site**. Depois da tarefa **Finalizar Proteção** ser executada, a máquina está preparada para ativação pós-falha.

Para monitorizar as VMs que adiciona, pode verificar a última vez detetada para VMs no **servidores de configuração**
> **Último contacto em**. Para adicionar VMs sem aguardar que a deteção agendada, realce o servidor de configuração (não clique nele) e clique em **atualizar**.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Executar um teste de recuperação após desastre](site-recovery-test-failover-to-azure.md)

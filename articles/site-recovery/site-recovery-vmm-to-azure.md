<properties
    pageTitle="Replicar máquinas virtuais Hyper-V em nuvens VMM para o Azure utilizando a Recuperação de Sites com o portal do Azure | Microsoft Azure"
    description="Descreve como implementar o Azure Site Recovery para orquestrar a replicação, a ativação pós-falha e a recuperação de VMs Hyper-V em nuvens VMM, para o Azure, utilizando o Portal do Azure"
    services="site-recovery"
    documentationCenter=""
    authors="rayne-wiselman"
    manager="jwhit"
    editor="tysonn"/>

<tags
    ms.service="site-recovery"
    ms.workload="backup-recovery"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="09/16/2016"
    ms.author="raynew"/>


# Replicar máquinas virtuais Hyper-V em nuvens VMM para o Azure utilizando o Azure Site Recovery com o Portal do Azure | Microsoft Azure

> [AZURE.SELECTOR]
- [Portal do Azure](site-recovery-vmm-to-azure.md)
- [Azure clássico](site-recovery-vmm-to-azure-classic.md)
- [PowerShell Resource Manager](site-recovery-vmm-to-azure-powershell-resource-manager.md)
- [PowerShell clássico](site-recovery-deploy-with-powershell.md)

Bem-vindo ao Azure Site Recovery! Utilize este artigo se pretender replicar máquinas virtuais Hyper-V no local geridas em nuvens do System Center Virtual Machine Manager (VMM), para o Azure, utilizando o Azure Site Recovery no portal do Azure.

> [AZURE.NOTE] O Azure tem dois [modelos de implementação](../resource-manager-deployment-model
> ) diferentes para criar e trabalhar com recursos: o Azure Resource Manager e o clássico. O Azure também tem dois portais – o Portal Clássico do Azure que suporta o modelo de implementação clássica, e o Portal do Azure com suporte para ambos os modelos de implementação.


O Azure Site Recovery no portal do Azure fornece várias novas funcionalidades:

- No portal do Azure, os serviços de Cópia de Segurança do Azure e os do Azure Site Recovery são combinados num único cofre de Serviços de Recuperação, para que possa configurar e gerir a continuidade do negócio e a recuperação após desastre (BCDR) a partir de uma única localização. Um dashboard unificado permite monitorizar e gerir operações em todos os sites no local e na nuvem pública do Azure.
- Os utilizadores com subscrições do Azure aprovisionadas com o programa de Fornecedor de Solução da Nuvem (CSP) já podem gerir as operações de Recuperação de Sites no Portal do Azure.
- A Recuperação de Sites no portal do Azure pode replicar máquinas para contas de armazenamento do Azure Resource Manager. Na ativação pós-falha, a Recuperação de Sites cria VMs baseadas no Resource Manager no Azure.
- A Recuperação de Sites continua a suportar a replicação para contas de armazenamento clássicas. Na ativação pós-falha, a Recuperação de Sites cria VMs utilizando o modelo clássico.


Depois de ler este artigo, publique quaisquer comentários na parte inferior dos comentários do Disqus. Coloque questões técnicas no [Fórum de Serviços de Recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## Descrição geral

As organizações necessitam de uma estratégia BCDR que determine como as aplicações, cargas de trabalho e dados continuam em execução e disponíveis durante o período de indisponibilidade planeado e imprevisto, e como retomar as condições de trabalho normais com a maior brevidade possível. A estratégia de BCDR deve manter os dados de negócio seguros e recuperáveis, e garantir que as cargas de trabalho continuam a estar continuamente disponíveis quando ocorrer um desastre.

A Recuperação de Sites é um serviço do Azure que contribui para a sua estratégia de BCDR através da orquestração da replicação dos servidores físicos no local e das máquinas virtuais para a nuvem (Azure) ou para um datacenter secundário. Quando ocorrem falhas na sua localização principal, faz-se a ativação pós-falha para a localização secundária para manter disponíveis as aplicações e cargas de trabalho. A localização principal é reativada quando se retomam as operações normais. Saiba mais em [O que é o Azure Site Recovery?](site-recovery-overview.md)

Este artigo fornece todas as informações necessárias para replicar as VMs Hyper-V no local nas nuvens VMM para o Azure. Inclui uma descrição geral da arquitetura, informações de planeamento e os passos de implementação para configurar o Azure, servidores no local, definições de replicação e planeamento da capacidade. Após ter configurado a infraestrutura, pode ativar a replicação em máquinas que pretende proteger e verificar que a ativação pós-falha funciona.


## Vantagens de negócio

- A Recuperação de Sites fornece proteção externa para cargas de trabalho de empresas e aplicações em execução em VMs Hyper-V.
- O portal dos Serviços de Recuperação fornece uma localização única para configurar, gerir e monitorizar a replicação, ativação pós-falha e recuperação.
- Pode executar facilmente ativações pós-falha a partir da infraestrutura no local para o Azure e reativações pós-falha (recuperação) a partir dos servidores anfitrião Hyper-V no seu site no local.
- Pode configurar o plano de recuperação com várias máquinas de modo a que cargas de trabalho da aplicação em camadas ativem-se em conjunto.


## Arquitetura do cenário


Estes são os componentes de cenário:

- **Servidor VMM**: um servidor VMM no local com uma ou mais nuvens.
- **Anfitrião ou cluster Hyper-V**: servidores anfitrião Hyper-V ou clusters geridos em nuvens VMM.
- **Fornecedor do Azure Site Recovery e agente dos Serviços de Recuperação**: durante a implementação, instale o Fornecedor do Azure Site Recovery no servidor VMM, e o agente dos Serviços de Recuperação do Microsoft Azure nos servidores anfitrião Hyper-V. O Fornecedor no servidor VMM comunica com a Recuperação de Sites através de HTTPS 443 para replicar a orquestração. O agente no servidor de anfitrião Hyper-V replica os dados para armazenamento do Azure através de HTTPS 443 por predefinição.
- **Azure**: precisa de uma subscrição do Azure, uma conta de armazenamento do Azure para dados de arquivo replicado, e uma rede virtual do Azure, para que as VMs do Azure estejam ligadas a uma rede após a ativação pós-falha.

![Topologia de E2A](./media/site-recovery-vmm-to-azure/architecture.png)


## Pré-requisitos do Azure

Eis o que necessita no Azure para implementar este cenário.

**Pré-requisito** | **Detalhes**
--- | ---
**Conta do Azure **| Necessita de uma conta do [Microsoft Azure](http://azure.microsoft.com/). Pode começar com uma [avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/). [Saiba mais](https://azure.microsoft.com/pricing/details/site-recovery/) sobre os preços da Recuperação de Sites.
**Storage do Azure** | Necessita de uma conta de armazenamento do Azure standard para armazenar dados replicados. Pode utilizar uma conta de armazenamento LRS ou GRS. Recomendamos GRS para que os dados sejam resilientes se ocorrer uma falha regional ou se a região primária não pode ser recuperada. [Saiba mais](../storage/storage-redundancy.md). A conta tem de ser na mesma região que o cofre de Serviços de Recuperação.<br/><br/>Não há suporte para o armazenamento Premium.<br/><br/> Os dados replicados são guardados no armazenamento do Azure e as VMs do Azure são criadas quando ocorre a ativação pós-falha. <br/><br/> [Leia sobre](../storage/storage-introduction.md) o armazenamento do Azure.
**Rede do Azure** | Necessita de uma rede virtual do Azure à qual se ligarão as VMs do Azure quando ocorrer a ativação pós-falha. A rede tem de estar na mesma região que o cofre de Serviços de Recuperação.

## Pré-requisitos no local

Eis o que precisa no ambiente no local

**Pré-requisito** | **Detalhes**
--- | ---
**VMM**| Um ou mais servidores VMM em execução no System Center 2012 R2. Cada servidor VMM deve ter uma ou mais nuvens configuradas. Uma nuvem deve conter:<br/><br/> Um ou mais grupos de anfitriões VMM.<br/><br/> Um ou mais servidores anfitrião Hyper-V ou clusters em cada grupo anfitrião.<br/><br/>[Saiba mais](http://www.server-log.com/blog/2011/8/26/vmm-2012-and-the-clouds.html) sobre como configurar as nuvens do VMM.
**Hyper-V** | Os servidores de anfitrião Hyper-V devem executar, pelo menos, o Windows Server 2012 R2 com a função Hyper-V e ter as atualizações mais recentes instaladas.<br/><br/> Um servidor Hyper-V deve conter uma ou mais VMs.<br/><br/> Um servidor de anfitrião Hyper-V ou cluster que inclui VMs que pretende replicar tem de ser gerido numa nuvem VMM.<br/><br/>Os servidores Hyper-V devem estar ligados à Internet, diretamente ou através de um proxy.<br/><br/>Os servidores Hyper-V devem ter instaladas as correções mencionadas no artigo [2961977](https://support.microsoft.com/kb/2961977).<br/><br/>Os servidores anfitrião Hyper-V tem de ter acesso à Internet para replicação de dados no Azure.
**Fornecedor e agente** | Durante a implementação do Azure Site Recovery, instale o Azure Site Recovery Provider no servidor VMM e o agente dos Serviços de Recuperação nos anfitriões Hyper-V. O Fornecedor e o agente têm de ligar ao Azure através da Internet diretamente ou através de um proxy. Tenha em atenção que um proxy baseado em HTTPS não é suportado. O servidor de proxy no servidor VMM e anfitriões Hyper-V deve permitir acesso a: <br/><br/> *.hypervrecoverymanager.windowsazure.com <br/><br/> *.accesscontrol.windows.net <br/><br/> *.backup.windowsazure.com <br/><br/> *.blob.core.windows.net <br/><br/> *.store.core.windows.net<br/><br/>Se tiver regras de firewall baseadas no endereço IP no servidor VMM, verifique que as regras permitem a comunicação com o Azure. Terá de permitir os [Intervalos de IP do Datacenter do Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653) e a porta HTTPS (443).<br/><br/>Permitir intervalos de endereços IP para a região do Azure da sua subscrição e para EUA Oeste.<br/><br/>Além disso, o servidor proxy no servidor VMM terá de aceder a https://www.msftncsi.com/ncsi.txt


## Pré-requisitos do computador protegido


**Pré-requisito** | **Detalhes**
--- | ---
**VMs protegidas** | Antes da ativação pós-falha numa VM, terá de certificar-se de que o nome que será atribuído à VM do Azure está em conformidade com os [Pré-requisitos do Azure](site-recovery-best-practices.md#azure-virtual-machine-requirements). Pode modificar o nome depois de ter ativado a replicação para a VM. <br/><br/> A capacidade de disco individual nas máquinas protegidas não deve ser superior a 1023 GB. Uma VM pode ter até 64 discos (portanto, 64 TB).<br/><br/> Os clusters de disco partilhado convidado não são suportados.<br/><br/> Não é suportada a interface UEFI (Unified Extensible Firmware Interface), nem o arranque da interface EFI (Extensible Firmware Interface) <br/><br/> Se a VM de origem tem o agrupamento NIC, este é convertido para um único NIC após a ativação pós-falha para o Azure.<br/><br/>Não é suportada a proteção de VMs que executam Linux com um endereço IP estático.

## Preparar para a implementação

Para se preparar para a implementação, é necessário:

1. [Configurar uma rede Azure](#set-up-an-azure-network) na qual as VMs do Azure serão localizadas após a ativação pós-falha.
2. [Configurar uma conta de armazenamento do Azure](#set-up-an-azure-storage-account) para dados replicados.
4. [Preparar o servidor VMM](#prepare-the-vmm-server) para a implementação de Recuperação de Sites.
5. [Preparar o mapeamento da rede](#prepare-for-network-mapping). Configure redes de modo a poder configurar o mapeamento da rede durante a implementação da Recuperação de Sites.

### Configurar uma rede do Azure

Necessita de uma rede do Azure para que as VMs do Azure criadas após a ativação pós-falha estabeleçam ligação a esta.

- A rede deve estar na região onde implementará o cofre dos Serviços de Recuperação.
- Consoante o modelo de recursos que pretende utilizar para efetuar a ativação pós-falha nas VMs do Azure, configure a rede Azure no [modo Resource Manager](../virtual-network/virtual-networks-create-vnet-arm-pportal.md) ou no [modo clássico](../virtual-network/virtual-networks-create-vnet-classic-pportal.md).
- Recomendamos que configure uma rede antes de começar. Caso contrário, terá de fazê-lo durante a implementação da Recuperação de Sites.

> [AZURE.NOTE] A [migração de redes](../resource-group-move-resources.md) através de grupos de recursos na mesma subscrição ou em várias subscrições não é suportada em redes utilizadas para implementar o Site Recovery.


### Configurar uma conta de armazenamento do Azure

- Precisará de uma conta de armazenamento padrão do Azure para armazenar dados replicados para o Azure. A conta tem de ser na mesma região que o cofre de Serviços de Recuperação.
- Consoante o modelo de recursos que pretende utilizar para efetuar a ativação pós-falha nas VMs do Azure, configure uma conta no [modo Resource Manager](../storage/storage-create-storage-account.md) ou no [modo clássico](../storage/storage-create-storage-account-classic-portal.md).
- Recomendamos que configure uma conta antes de começar. Caso contrário, terá de fazê-lo durante a implementação da Recuperação de Sites.

> [AZURE.NOTE] A [migração de contas de armazenamento](../resource-group-move-resources.md) através de grupos de recursos na mesma subscrição ou em várias subscrições não é suportada em contas de armazenamento utilizadas para implementar o Site Recovery.

### Preparar o servidor VMM

- Certifique-se de que o servidor VMM está em conformidade com os [pré-requisitos](#on-premises-prerequisites).
- Durante a implementação da Recuperação de Sites, pode especificar que todas as nuvens num servidor VMM estão disponíveis no portal do Azure. Se pretende que apenas determinadas nuvens sejam apresentadas no portal, pode ativar essa definição na nuvem na consola do administrador VMM.


### Preparar o mapeamento da rede

Terá de configurar o mapeamento da rede durante a implementação da Recuperação de Sites. Os mapas de mapeamento da rede entre as redes VM do VMM de origem e as redes de destino do Azure para ativar o seguinte:

- As máquinas que efetuem a ativação pós-falha na mesma rede podem ligar-se entre si, mesmo que não tenham ativação pós-falha da mesma forma ou estejam no mesmo plano de recuperação.
- Se um gateway de rede está configurado na rede de destino do Azure, as máquinas virtuais do Azure podem ligar-se a máquinas virtuais no local.
- Para configurar o mapeamento da rede, eis o que necessita para se preparar:

    - Certifique-se de que as VMs no servidor de anfitrião Hyper-V de origem estão ligadas a uma rede VM do VMM. Essa rede deve ser ligada a uma rede lógica que está associada à nuvem.
    - Um rede de Azure conforme descrito [acima](#set-up-an-azure-network)

- [Saiba mais](site-recovery-network-mapping.md) sobre como funciona o mapeamento da rede.


## Criar um cofre dos Serviços de Recuperação 

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
2. Clique em **Novo** > **Gestão** > **Serviços de Recuperação**. Em alternativa, pode clicar em **Procurar** > **Cofres dos Serviços de Recuperação** > **Adicionar**.

    ![Novo cofre](./media/site-recovery-vmm-to-azure/new-vault3.png)

3. Em **Nome**, especifique um nome amigável para identificar o cofre. Se tiver mais do que uma subscrição, selecione uma delas.
4. [Crie um grupo de recursos](../resource-group-template-deploy-portal.md) ou selecione um existente. Selecione uma região do Azure. As máquinas serão replicadas para esta região. Para verificar as regiões suportadas veja Disponibilidade Geográfica em [Detalhes dos Preços do Azure Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/).
4. Se pretender aceder rapidamente ao cofre a partir do Dashboard, clique em **Afixar ao dashboard** > **Criar cofre**.

    ![Novo cofre](./media/site-recovery-vmm-to-azure/new-vault-settings.png)

O novo cofre é apresentado em **Dashboard** > **Todos os recursos** e no painel principal **Cofres dos Serviços de Recuperação**.

## Introdução

A Recuperação de Sites fornece uma introdução à experiência que o ajuda a implementar o mais rapidamente possível. A Introdução verifica os pré-requisitos e orienta-o durante os passos de implementação da Recuperação de Sites na ordem correta.

Na Introdução, seleciona o tipo de máquinas que pretende replicar e para onde pretende que sejam replicadas. Você configura os servidores no local, contas de armazenamento do Azure e as redes. Pode criar políticas de replicação e efetuar o planeamento da capacidade. Após configurar a sua infraestrutura, pode ativar a replicação para as VMs. Pode executar as ativações pós-falha para máquinas específicas ou criar o plano de recuperação para efetuar a ativação pós-falha de várias máquinas.

Comece a Introdução com a escolha de como pretende implementar a Recuperação de Sites. O fluxo da Introdução muda ligeiramente dependendo dos seus requisitos de replicação.



## Passo 1: Escolher os seus objetivos de proteção

Selecione o que pretende replicar e para onde pretende que seja replicado.

1. No painel **Cofres dos Serviços de Recuperação**, selecione o cofre e clique em **Definições**.
2. Em **Introdução**, clique em **Recuperação de Sites** > **Passo 1: Preparar a Infraestrutura** > **Objetivo de proteção**.

    ![Selecione os objetivos](./media/site-recovery-vmm-to-azure/choose-goals.png)

3. Em **Objetivo de proteção**, selecione **Para o Azure** e, em seguida, selecione **Sim, com o Hyper-V**. Selecione **Sim** para confirmar que está a utilizar o VMM para gerir anfitriões Hyper-V e o site de recuperação. Em seguida, clique em **OK**.

    ![Selecione os objetivos](./media/site-recovery-vmm-to-azure/choose-goals2.png)



## Passo 2: Configurar o ambiente de origem

Instale o Fornecedor do Azure Site Recovery no servidor VMM e registe o servidor no cofre. Instale o agente do Serviços de Recuperação do Azure nos anfitriões Hyper-V

1. Clique em **Passo 2: Preparar a Infraestrutura** > **Origem**.

    ![Configurar a origem](./media/site-recovery-vmm-to-azure/set-source1.png)

2. Em **Preparar a origem**, clique em **+ VMM** para adicionar um servidor VMM.

    ![Configurar a origem](./media/site-recovery-vmm-to-azure/set-source2.png)

3. No painel **Adicionar Servidor**, verifique se o **Servidor VMM do System Center** aparece em **Tipo de servidor** e se o servidor VMM cumpre os [pré-requisitos e os requisitos de URLs](#on-premises-prerequisites).
4. Transfira o ficheiro de instalação do Fornecedor do Azure Site Recovery.
5. Transfira a chave de registo. Precisará disto quando executar a configuração. A chave é válida durante cinco dias depois de gerá-la.

    ![Configurar a origem](./media/site-recovery-vmm-to-azure/set-source3.png)

6. Instale o Fornecedor do Azure Site Recovery no servidor VMM.


### Instale o Fornecedor do Azure Site Recovery

1.  Execute o ficheiro de configuração do Fornecedor.
2. Em **Microsoft Update**, pode optar pelas atualizações de modo a que as atualizações do Fornecedor sejam instaladas de acordo com a política do Microsoft Update.
3. Em **Instalação**, aceite ou modifique a localização de instalação do Fornecedor predefinida e clique em **Instalar**.

    ![Localização de instalação](./media/site-recovery-vmm-to-azure/provider2.png)

4. Quando a instalação for concluída, clique em **Registar** para registar o servidor VMM no cofre.
5. Na página **Definições do Cofre**, clique em **Procurar** para selecionar o ficheiro de chave do cofre. Especifique a subscrição do Azure Site Recovery e o nome do cofre.

    ![Registo do servidor](./media/site-recovery-vmm-to-azure/provider10.PNG)

6. Em **Ligação à Internet**, especifique a forma como o Fornecedor em execução no servidor VMM estabelece ligação à Recuperação de Sites através da Internet.

    - Se pretender que o Fornecedor se ligue diretamente, selecione **Ligar diretamente ao Azure Site Recovery sem um proxy**.
    - Se o seu proxy existente requer autenticação ou se pretende utilizar um proxy personalizado, selecione **Ligar ao Azure Site Recovery com um servidor proxy**.
    - Se utilizar um proxy personalizado, especifique o endereço, a porta e as credenciais.
    - Se estiver a utilizar um proxy, já deverá ter permitido os URLs descritos em [pré-requisitos](#on-premises-prerequisites).
    - Se utilizar um proxy personalizado será automaticamente criada uma conta RunAs do VMM (DRAProxyAccount) utilizando as credenciais de proxy especificado. Configure o servidor proxy para que esta conta possa autenticar-se com êxito. As definições de conta RunAs do VMM podem ser modificadas na consola do VMM. Em **Definições**, expanda **Segurança** > **Contas Run As** e modifique a palavra-passe de DRAProxyAccount. Terá de reiniciar o serviço VMM para que esta definição surta efeito.

    ![Internet](./media/site-recovery-vmm-to-azure/provider13.PNG)

7. Aceite ou modifique a localização de um certificado SSL que é gerado automaticamente para a encriptação de dados. Este certificado é utilizado se ativar a encriptação de dados para uma nuvem protegida pelo Azure no portal do Azure Site Recovery. Mantenha este certificado num local seguro. Quando executar uma ativação pós-falha para o Azure, precisará das mesmas para desencriptar, caso a encriptação de dados esteja ativada.


8. Em **Nome do servidor**, especifique um nome amigável para identificar o servidor VMM no cofre. Numa configuração de cluster, especifique o nome da função de cluster do VMM.
9. Ative **Sincronizar metadados de nuvem** se pretender sincronizar os metadados para todas as nuvens no servidor VMM com o cofre. Esta ação só deverá ocorrer uma vez em cada servidor. Se não quiser sincronizar todas as nuvens, pode deixar esta definição desmarcada e sincronizar cada nuvem individualmente nas propriedades de nuvem na consola do VMM. Clique em **Registar** para concluir o processo.

    ![Registo do servidor](./media/site-recovery-vmm-to-azure/provider16.PNG)

10. Inicia-se o registo. Após a conclusão de registo, o servidor é apresentado no painel **Definições** > **Servidores** no cofre.


#### Instalação de linha de comandos para o Fornecedor do Azure Site Recovery

O Fornecedor do Azure Site Recovery pode ser instalado a partir da linha de comandos. Este método pode ser utilizado para instalar o Fornecedor num Server Core do Windows Server 2012 R2.

1. Transfira o ficheiro de instalação do Fornecedor e a chave de registo para uma pasta. Por exemplo: C:\ASR.
2. Numa linha de comandos elevada, execute estes comandos para extrair o instalador de Fornecedor com estes comandos:

            C:\Windows\System32> CD C:\ASR
            C:\ASR> AzureSiteRecoveryProvider.exe /x:. /q
3. Execute este comando para instalar os componentes:

            C:\ASR> setupdr.exe /i

4. Em seguida, execute estes comandos para registar o servidor no cofre:

        CD C:\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin
        C:\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin\> DRConfigurator.exe /r  /Friendlyname <friendly name of the server> /Credentials <path of the credentials file> /EncryptionEnabled <full file name to save the encryption certificate>       

Em que:

- **/Credentials**: é o parâmetro obrigatório que especifica onde se encontra o ficheiro da chave de registo.  
- **/ FriendlyName**: é o parâmetro obrigatório do nome do servidor anfitrião Hyper-V que é apresentado no portal do Azure Site Recovery.
- - **/ /Encryptionenabled**: é o parâmetro opcional quando está a replicar VMs Hyper-V em nuvens VMM para o Azure. Especifique se pretende encriptar máquinas virtuais no Azure (encriptação de inativos). Certifique-se de que o nome do ficheiro tem uma extensão **.pfx**. A encriptação está desativada por predefinição.
- **/proxyAddress**: é o parâmetro opcional que especifica o endereço do servidor proxy.
- **/proxyport**: é o parâmetro opcional que especifica a porta do servidor proxy.
- **/proxyUsername**: é o parâmetro opcional que especifica o nome de utilizador do proxy (caso o proxy necessite de autenticação).
- **/proxyPassword**: é o parâmetro opcional que especifica a palavra-passe para se autenticar com o servidor proxy (caso o proxy necessite de autenticação).


### Instale o agente dos Serviços de Recuperação do Azure nos anfitriões Hyper-V

1. Após ter configurado o Fornecedor, tem de transferir o ficheiro de instalação para o agente dos Serviços de Recuperação do Azure. Execute a configuração em cada servidor Hyper-V na nuvem VMM.

    ![Sites Hyper-V](./media/site-recovery-vmm-to-azure/hyperv-agent1.png)

2. Na página **Verificação de Pré-requisitos**, clique em **Seguinte**. Os pré-requisitos em falta serão instalados automaticamente.

    ![Pré-requisitos do Agente dos Serviços de Recuperação](./media/site-recovery-vmm-to-azure/hyperv-agent2.png)

3. Na página **Definições de Instalação**, aceite ou modifique a localização de instalação e a localização da cache. Pode configurar a cache numa unidade que tem, pelo menos, 5 GB de armazenamento disponível, mas recomenda-se uma unidade de cache com 600 GB ou mais de espaço livre. Em seguida, clique em **Instalar**.
4. Após a conclusão da instalação, clique em **Fechar** para concluir.

    ![Registar o Agente MARS](./media/site-recovery-vmm-to-azure/hyperv-agent3.png)

#### Instalação de linha de comandos para o agente do Azure Site Recovery Services

Pode instalar o Agente dos Serviços de Recuperação do Microsoft Azure a partir da linha de comandos utilizando o comando seguinte:

     marsagentinstaller.exe /q /nu

#### Configure o acesso de proxy da Internet para Recuperação de Sites a partir de anfitriões Hyper-V

O agente dos Serviços de Recuperação em execução em anfitriões Hyper-V tem acesso à Internet para o Azure para a replicação de VM. Se está a aceder à Internet através de um proxy, configure-a da seguinte forma:

1. Abra o snap-in MMC da Cópia de Segurança do Microsoft Azure no anfitrião Hyper-V. Por predefinição, um atalho para Cópia de Segurança do Microsoft Azure está disponível no ambiente de trabalho ou em C:\Programas\Microsoft Azure Recovery Services Agent\bin\wabadmin.
2. No snap-in, clique em **Alterar Propriedades**.
3. No separador **Configuração de Proxy**, especifique as informações do servidor proxy.

    ![Registar o Agente MARS](./media/site-recovery-vmm-to-azure/mars-proxy.png)

4. Certifique-se de que o agente pode contactar os URLs descritos em [pré-requisitos](#on-premises-prerequisites).


## Passo 3: Configurar o ambiente de origem

Especifique a conta de armazenamento do Azure a ser utilizada para replicação e a rede Azure ao qual as VMs do Azure estabelecerão ligação após a ativação pós-falha.

1.  Clique em **Preparar a Infraestrutura** > **Destino** e selecione a subscrição do Azure que pretende utilizar.
2.  Especifique o modelo de implementação que pretende utilizar para as VMs após a ativação pós-falha.
3.  A Recuperação de Sites verifica que tem uma ou mais contas de armazenamento e redes do Azure compatíveis.

    ![Armazenamento](./media/site-recovery-vmm-to-azure/compatible-storage.png)

4.  Se ainda não criou uma conta de armazenamento e pretender criar uma com o Resource Manager, clique em **+Conta de armazenamento** para fazê-lo de forma integrada.  No painel **Criar conta de armazenamento**, especifique um nome de conta, o tipo, a subscrição e a localização. A conta tem de ter a mesma localização que o cofre de Serviços de Recuperação.

    ![Armazenamento](./media/site-recovery-vmm-to-azure/gs-createstorage.png)

    Tenha em atenção que:

    - Se pretende criar uma conta de armazenamento utilizando o modelo clássico, pode fazê-lo no portal do Azure. [Saiba mais](../storage/storage-create-storage-account-classic-portal.md)
    - Se estiver a utilizar uma conta de armazenamento Premium para os dados replicados, terá de configurar uma conta de armazenamento standard adicional para armazenar os registos de replicação que capturam as alterações em curso dos dados no local.

4.  Se ainda não criou uma rede Azure e pretende criar uma com o Resource Manager, clique em **+Rede** para fazê-lo de forma integrada. No painel **Criar rede virtual**, especifique um nome de rede, o intervalo de endereços, os detalhes da sub-rede, a subscrição e a localização. A rede tem de ter a mesma localização que o cofre de Serviços de Recuperação.

    ![Rede](./media/site-recovery-vmm-to-azure/gs-createnetwork.png)

    Se pretender criar uma rede utilizando o modelo clássico, vai fazê-lo no portal do Azure. [Saiba mais](../virtual-network/virtual-networks-create-vnet-classic-pportal.md).

### Configurar o mapeamento da rede

- [Leia](#prepare-for-network-mapping) uma rápida descrição geral do que faz o mapeamento da rede. [Leia este artigo](site-recovery-network-mapping.md) para obter uma explicação mais aprofundada.
- Certifique-se de que as máquinas virtuais no servidor VMM estão ligadas a uma rede VM e que tenha criado pelo menos uma rede virtual do Azure. Várias redes VM podem ser mapeadas para uma única rede Azure.

Configure o mapeamento da seguinte forma:

1. Em **Definições** > **Infraestrutura de Recuperação de Sites** > **Mapeamentos da Rede** > **Mapeamento da Rede**, clique no ícone **+ Mapeamento da Rede**.

    ![Mapeamento da rede](./media/site-recovery-vmm-to-azure/network-mapping1.png)

2. Em **Adicionar mapeamento da rede**, selecione o servidor VMM como origem e o **Azure** como destino.
3. Verifique a subscrição e o modelo de implementação, após a ativação pós-falha.
4. Em **Rede de origem**, selecione a rede VM de origem no local que pretende mapear para a lista associada ao servidor VMM.
5. Em **Rede de destino**, selecione a rede do Azure na qual as VMs da réplica do Azure serão localizadas quando forem criadas. Em seguida, clique em **OK**.

    ![Mapeamento da rede](./media/site-recovery-vmm-to-azure/network-mapping2.png)

Veja a seguir o que acontece quando começa o mapeamento da rede:

- As VMs existentes na rede VM de origem estão ligadas à rede de destino quando o mapeamento é iniciado. As novas VMs ligadas à rede VM de origem aparecem ligadas à rede do Azure mapeada quando ocorrer a replicação.
- Se modificar um mapeamento de rede existente, as máquinas virtuais de réplica serão ligadas utilizando as novas definições.
- Se a rede de destino tem várias sub-redes e um dessas sub-redes tem o mesmo nome que a sub-rede onde está localizada a máquina virtual de origem, a máquina virtual de réplica será ligada a essa sub-rede de destino após a ativação pós-falha.
- Se não existir nenhuma sub-rede de destino com um nome correspondente, a máquina virtual será ligada à primeira sub-rede da rede.



## Passo 4: Configurar as definições de replicação


1. Para criar uma nova política de replicação, clique em **Preparar a infraestrutura** > **Definições de Replicação** > **+Criar e associar**.

    ![Rede](./media/site-recovery-vmm-to-azure/gs-replication.png)

2. Em **Criar e associar política**, especifique um nome de política.
3. Em **Frequência de cópia**, especifique a frequência com que pretende replicar dados delta após a replicação inicial (a cada 30 segundos, 5 ou 15 minutos).
4. Em **Retenção do ponto de recuperação**, especifique, em horas, qual será a duração da janela de retenção para cada ponto de recuperação. As máquinas protegidas podem ser recuperadas para qualquer ponto nessa janela.
6. Em **Frequência de instantâneos consistentes com a aplicação**, especifique a frequência (1 a 12 horas) com que os pontos de recuperação que contêm os instantâneos consistentes com aplicações serão criados. O Hyper-V utiliza dois tipos de instantâneos – um instantâneo padrão que fornece um instantâneo incremental de toda a máquina virtual e um instantâneo consistente com aplicações que cria um instantâneo pontual dos dados de aplicação no interior da máquina virtual. Os instantâneos consistentes com aplicações utilizam o Serviço de Cópia de Sombra de Volumes (VSS) para se certificar de que as aplicações estão num estado consistente quando se obtém o instantâneo. Tenha em atenção que se ativar instantâneos consistentes com aplicações, afetará o desempenho das aplicações em execução nas máquinas virtuais de origem. Certifique-se de que o valor que define é menor que o número de pontos de recuperação adicionais que está a configurar.
3. Em **Hora de início da replicação inicial**, especifique quando deve começar a replicação inicial. A replicação ocorre através da sua largura de banda de Internet, pelo que poderá querer agendá-la fora do horário de trabalho.
5. Em **Encriptar dados armazenados no Azure**, especifique se pretende encriptar os dados REST no armazenamento do Azure. Em seguida, clique em **OK**.

    ![Política de replicação](./media/site-recovery-vmm-to-azure/gs-replication2.png)

6. Quando cria uma nova política, automaticamente fica associada à nuvem do VMM. Clique em **OK**. Pode associar nuvens adicionais do VMM (e as VMs) com esta política de replicação em **Definições** > **Replicação** > nome da política > **Associar Nuvem do VMM**.

    ![Política de replicação](./media/site-recovery-vmm-to-azure/policy-associate.png)

## Passo 5: Planeamento de capacidade

Agora que tem a infraestrutura configurada, pode planear o planeamento da capacidade e averiguar se precisa de recursos adicionais.

A Recuperação de Sites fornece um Capacity Planner para o ajudar a alocar os recursos corretos ao seu ambiente de origem, aos componentes de recuperação do site, redes e armazenamento. Pode executar o planeador no modo rápido para obter estimativas com base num número médio de VMs, discos e armazenamento, ou no modo detalhado no qual poderá especificar números para o nível da carga de trabalho. Antes de iniciar, precisará de:

- Recolher informações sobre o ambiente de replicação, incluindo VMs, discos por VMs e armazenamento por disco.
- Calcular a taxa de alteração (renovação) diária para os dados replicados. Pode utilizar o [ Capacity Planner para as Réplicas do Hyper-V](https://www.microsoft.com/download/details.aspx?id=39057) que o ajudar nesta tarefa.

1.  Clique em **Transferir** para transferir a ferramenta e execute-a. [Leia o artigo](site-recovery-capacity-planner.md) que acompanha a ferramenta.
2.  Quando terminar, selecione **Sim** em **Executou o Capacity Planner**?

    ![Planeamento de capacidade](./media/site-recovery-vmm-to-azure/gs-capacity-planning.png)

### Considerações sobre a largura de banda de rede

Pode utilizar a ferramenta Capacity Planner para calcular a largura de banda que precisa para a replicação (replicação inicial e posteriormente delta). Para controlar a utilização da largura de banda da replicação, tem algumas opções:

- **Limitar largura de banda**: o tráfego do Hyper-V que é replicado para um site secundário passa através de um anfitrião Hyper-V específico. Pode limitar a largura de banda no servidor de anfitrião.
- **Otimizar a largura de banda**: pode influenciar a largura de banda utilizada na replicação através de um par de chaves de registo.

#### Limitar largura de banda

1. Abra o snap-in MMC da Cópia de Segurança do Microsoft Azure no servidor de anfitrião Hyper-V. Por predefinição, um atalho para Cópia de Segurança do Microsoft Azure está disponível no ambiente de trabalho ou em C:\Programas\Microsoft Azure Recovery Services Agent\bin\wabadmin.
2. No snap-in, clique em **Alterar Propriedades**.
3. No separador **Limitação**, selecione **Ativar a limitação de utilização da largura de banda de Internet para operações de cópia de segurança ** e defina os limites das horas de trabalho e de descanso. Os intervalos válidos variam entre 512 Kbps e 102 Mbps por segundo.

    ![Limitar largura de banda](./media/site-recovery-vmm-to-azure/throttle2.png)

Também pode utilizar o cmdlet [Set-OBMachineSetting](https://technet.microsoft.com/library/hh770409.aspx) para configurar a limitação. Exemplo:

    $mon = [System.DayOfWeek]::Monday
    $tue = [System.DayOfWeek]::Tuesday
    Set-OBMachineSetting -WorkDay $mon, $tue -StartWorkHour "9:00:00" -EndWorkHour "18:00:00" -WorkHourBandwidth  (512*1024) -NonWorkHourBandwidth (2048*1024)

**Set-OBMachineSetting -NoThrottle** indica que não é necessária nenhuma limitação.


#### Influência da largura de banda de rede

O valor de registo **UploadThreadsPerVM** controla o número de processos utilizados na transferência de dados (replicação inicial ou delta) de um disco. Um valor mais alto aumenta a largura de banda de rede utilizada na replicação. O valor de registo **DownloadThreadsPerVM** especifica o número de processos utilizados para a transferência de dados durante a reativação pós-falha.

1. No registo, navegue para **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication**.

    - Modifique o valor **UploadThreadsPerVM** (ou crie a chave, se não existir) para processos de controlo utilizados para replicação de discos.
    - Modifique o valor **DownloadThreadsPerVM** (ou crie a chave, se não existir) para processos de controlo utilizados para reativação pós-falha desde o Azure.
2. O valor predefinido é 4. Numa rede “sobreaprovisionada”, os valores predefinidos destas chaves de registo devem ser alterados. O valor máximo é 32. Monitorize o tráfego para otimizar o valor.

## Passo 6: Ativar a replicação

Agora, ative a replicação da seguinte forma:

1. Clique em **Passo 2: Replicar aplicação** > **Origem**. Depois de ativar a replicação pela primeira vez, clique em **+Replicar** no cofre para ativar a replicação para máquinas adicionais.

    ![Ativar a replicação](./media/site-recovery-vmm-to-azure/enable-replication1.png)

2. No painel **Origem**, selecione o servidor VMM e a nuvem em que estão localizados os anfitriões Hyper-V. Em seguida, clique em **OK**.

    ![Ativar a replicação](./media/site-recovery-vmm-to-azure/enable-replication-source.png)

3. Em **Destino**, selecione a subscrição, o modelo de implementação de ativação pós-falha e a conta de armazenamento está a utilizar para os dados replicados.

    ![Ativar a replicação](./media/site-recovery-vmm-to-azure/enable-replication-target.png)

4. Clique na conta de armazenamento que pretende utilizar. Se pretender utilizar uma conta de armazenamento diferente das que tem, pode [criar uma](#set-up-an-azure-storage-account). Para criar uma conta de armazenamento utilizando o modelo do Resource Manager, clique em **Criar nova**. Se quiser criar uma conta de armazenamento utilizando o modelo clássico, pode fazê-lo [no portal do Azure](../storage/storage-create-storage-account-classic-portal.md). Em seguida, clique em **OK**.
5. Selecione a rede e a sub-rede do Azure às quais as VMs do Azure estabelecerão uma ligação quando se puserem em marcha após a ativação pós-falha. Selecione **Configurar agora para as máquinas selecionadas** para aplicar a definição de rede para todas as máquinas selecionadas para proteção. Selecione **Configurar mais tarde** para selecionar a rede do Azure por máquina. Se pretender utilizar uma rede de armazenamento diferente das que tem, pode [criar uma](#set-up-an-azure-network). Para criar uma rede utilizando o modelo Resource Manager, clique em **Criar nova**. Se quiser criar uma rede utilizando o modelo clássico, deverá fazê-lo [no portal do Azure](../virtual-network/virtual-networks-create-vnet-classic-pportal.md). Selecione uma sub-rede, se aplicável. Em seguida, clique em **OK**.
6. Em **Virtual Machines** > **Selecionar máquinas virtuais**, clique e selecione cada máquina que pretende replicar. Só pode selecionar máquinas para as quais a replicação pode ser ativada. Em seguida, clique em **OK**.

    ![Ativar a replicação](./media/site-recovery-vmm-to-azure/enable-replication5.png)

5. Em **Propriedades** > **Configurar propriedades**, selecione o sistema operativo para as VMs selecionadas e o disco do sistema operativo. Em seguida, clique em **OK**. Pode definir as propriedades adicionais mais tarde.

    ![Ativar a replicação](./media/site-recovery-vmm-to-azure/enable-replication6.png)


12. Em **Definições de replicação** > **Configurar as definições de replicação**, selecione a política de replicação que pretende aplicar para as máquinas virtuais protegidas. Em seguida, clique em **OK**. Pode modificar a política de replicação em **Definições** > **Políticas de replicação** > nome da política > **Editar Definições**. As alterações que aplicar são utilizadas para as máquinas que já estão a replicar e para as novas máquinas.

    ![Ativar a replicação](./media/site-recovery-vmm-to-azure/enable-replication7.png)

Pode controlar o progresso da tarefa **Ativar Proteção** em **Definições** > **Tarefas** > **Tarefas da Recuperação de Sites**. Depois da tarefa **Finalizar Proteção** ser executada, a máquina está preparada para ativação pós-falha.

### Ver e gerir propriedades da VM

Recomendamos que verifique as propriedades da máquina de origem. Lembre-se de que o nome da VM do Azure deve estar em conformidade com os [Requisitos de máquina virtual do Azure](site-recovery-best-practices.md#azure-virtual-machine-requirements).

1. Clique em **Definições** > **Itens Protegidos** > **Itens Replicados** e selecione a máquina para ver os respetivos detalhes.

    ![Ativar a replicação](./media/site-recovery-vmm-to-azure/vm-essentials.png)

2. Em **Propriedades**, pode ver as informações de replicação e ativação pós-falha da VM.

    ![Ativar a replicação](./media/site-recovery-vmm-to-azure/test-failover2.png)

3. Em **Computação e Rede** > **Propriedades de computação**, pode especificar o nome e o tamanho do destino da VM do Azure. Altere o nome para estar em conformidade com os [Requisitos do Azure](site-recovery-best-practices.md#azure-virtual-machine-requirements), se necessário. Também pode ver e modificar as informações sobre a rede de destino, a sub-rede e o endereço IP atribuído à VM do Azure. Tenha em atenção o seguinte:

    - Pode definir o endereço IP de destino. Se não fornecer um endereço, a máquina na ativação pós-falha utilizará o DHCP. Se definir um endereço que não está disponível na ativação pós-falha, a ativação falhará. O mesmo endereço IP de destino pode ser utilizado para ativação pós-falha de teste se o endereço está disponível na rede de ativação pós-falha de teste.
    - O número de adaptadores de rede é ditado pelo tamanho especificado para a máquina virtual de destino, da seguinte forma:

        - Se o número de adaptadores de rede na máquina de origem for menor ou igual ao número de adaptadores permitido para o tamanho da máquina de destino, o destino terá o mesmo número de adaptadores que os de origem.
        - Se o número de adaptadores da máquina virtual de origem exceder o número permitido para o tamanho de destino, será utilizado o tamanho máximo de destino.
        - Por exemplo, se uma máquina de origem tem duas placas de rede e o tamanho da máquina de destino suporta quatro, a máquina de destino terá dois adaptadores. Se a máquina de origem tiver dois adaptadores, mas o tamanho de destino só suportar um, a máquina de destino terá apenas um adaptador.     
        - Se a VM possui vários adaptadores de rede, todos serão ligados à mesma rede.

    ![Ativar a replicação](./media/site-recovery-vmm-to-azure/test-failover4.png)

5.  Em **Discos**, pode ver o sistema operativo e os discos de dados na VM que será replicada.



## Passo 7: Testar a implementação

Para testar a implementação pode executar uma ativação pós-falha de teste para uma única máquina virtual ou um plano de recuperação que contém uma ou mais máquinas virtuais.


### Preparar para ativação pós-falha

- Para executar uma ativação pós-falha, recomenda-se que crie uma nova rede Azure que está isolada da sua rede de produção Azure (este é o comportamento predefinido quando cria uma nova rede no Azure). [Saiba mais](site-recovery-failover.md#run-a-test-failover) sobre como executar as ativações pós-falha de teste.
- Para obter o melhor desempenho quando houver uma ativação pós-falha para o Azure, instale o Agente do Azure na máquina protegida. Este torna o arranque mais rápido e ajuda na resolução de problemas. Instale o agente [Linux](https://github.com/Azure/WALinuxAgent) ou [Windows](http://go.microsoft.com/fwlink/?LinkID=394789).
- Para testar totalmente a implementação, necessita de uma infraestrutura para a máquina replicada funcionar conforme esperado. Se pretender testar o Active Directory e o DNS pode criar uma máquina virtual como controlador de domínio com o DNS e replicar esta no Azure com o Azure Site Recovery. Leia mais em [Considerações de ativação pós-falha de teste para o Active Directory](site-recovery-active-directory.md#considerations-for-test-failover).
- Se pretender executar uma ativação pós-falha não planeada em vez de uma ativação pós-falha de teste, tenha em atenção o seguinte:

    - Se for possível deve encerrar as máquinas primárias antes de executar uma ativação pós-falha não planeada. Isto garante que não tenha as máquinas de origem e de réplica em execução ao mesmo tempo.
    - Quando executa uma ativação pós-falha não planeada, a replicação de dados para nas máquinas primárias de modo que não são transferidos quaisquer dados delta após o início de uma ativação pós-falha não planeada. Além disso, se executar uma ativação pós-falha não planeada num plano de recuperação, este será executado até concluir o procedimento, mesmo que ocorra um erro.

### Preparar a ligação para VMs do Azure após a ativação pós-falha

Se pretende ligar-se para VMs do Azure com RDP após a ativação pós-falha, certifique-se de que efetua o seguinte procedimento:

**Na máquina no local antes da ativação pós-falha**:

- Para ter acesso através da Internet, ative o RDP, certifique-se de que as regras TCP e UDP estão adicionadas para o **Público**, e certifique-se de que o RDP é permitido na **Firewall do Windows** -> **Aplicações e funcionalidades permitidas** para todos os perfis.
- Para ter acesso através de uma ligação de site a site, ative o RDP na máquina e certifique-se de que o RDP é permitido na **Firewall do Windows** -> **Aplicações e funcionalidades permitidas** para **Domínio** e redes **Privadas**.
- Instale o [Agente VM do Azure](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409) na máquina no local.
- Certifique-se de que a política de SAN do sistema operativo está definida como OnlineAll. [Saiba mais]( https://support.microsoft.com/kb/3031135)
- Desative o serviço IPSec antes de executar a ativação pós-falha.

**Na VM do Azure após a ativação pós-falha**:

- Adicione um ponto final público para o protocolo RDP (porta 3389) e especifique as credenciais de início de sessão.
- Certifique-se de que não tem políticas de domínio a impedir a ligação a uma máquina virtual utilizando um endereço público.
- Tente ligar-se. Se não conseguir ligar-se, certifique-se de que a VM está em execução. Para mais sugestões de resolução de problemas, leia este [artigo](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).

Se pretender aceder a uma VM do Azure a executar o Linux após a ativação pós-falha utilizando um cliente Secure Shell (ssh), efetue o seguinte:

**Na máquina no local antes da ativação pós-falha**:

- Certifique-se de que o serviço de Secure Shell na VM do Azure está definido para ser iniciado automaticamente no arranque do sistema.
- Verifique se as regras de firewall permitem uma ligação SSH ao mesmo.

**Na VM do Azure após a ativação pós-falha**:

- As regras do grupo de segurança de rede na VM com ativação pós-falha e na sub-rede Azure à qual está ligada precisa de permitir ligações de entrada na porta SSH.
- Deverá ser criado um ponto final público para permitir ligações de entrada na porta SSH (a porta TCP 22 por predefinição).
- Se o acesso à VM for feito através de uma ligação VPN (Express Route ou VPN de site a site), o cliente pode ser utilizado para ligar diretamente à VM através do SSH.


### Executar uma ativação pós-falha de teste

Para executar o teste de ativação pós-falha, efetue o seguinte:

1. Para efetuar a ativação pós-falha numa única VM, em **Definições** > **Itens Replicados**, clique na VM > **+ Ativação Pós-Falha de Teste**.
2. Para efetuar a ativação pós-falha de um plano de recuperação, em **Definições** > **Planos de Recuperação**, clique com o botão direito no plano > **Ativação Pós-Falha de Teste**. Para criar um plano de recuperação, [siga estas instruções](site-recovery-create-recovery-plans.md).

3. Em **Ativação Pós-Falha de Teste**, selecione a rede do Azure para as VMs do Azure que serão ligadas após a ocorrência da ativação pós-falha.
4. Clique em **OK** para iniciar a ativação pós-falha. Pode controlar o progresso clicando na VM para abrir as respetivas propriedades ou na tarefa **Ativação Pós-Falha de Teste** em **Definições** > **Tarefas da Recuperação de Sites**.
5. Quando a ativação pós-falha chegar à fase **Concluir teste**, efetue o seguinte:

    1. Veja a máquina virtual de réplica no portal do Azure. Verifique se as máquinas virtuais são iniciadas corretamente.
    2. Se tiver configurado para aceder a máquinas virtuais a partir da rede no local, pode iniciar uma ligação de Ambiente de Trabalho Remoto para a máquina virtual.
    3. Clique em **Concluir o teste** para terminar.
    4. Clique em **Notas** para registar e guardar todas as observações associadas à ativação pós-falha de teste.
    5. Clique em **A ativação pós-falha de teste está concluída**. Limpe o ambiente de teste para automaticamente desligar e eliminar a máquina virtual de teste.
    6. Nesta fase são eliminados todos os elementos ou VMs criados automaticamente pela Recuperação de Sites durante a ativação pós-falha de teste. Não são eliminados quaisquer elementos adicionais criados para a ativação pós-falha de teste.

    > [AZURE.NOTE] Se uma ativação pós-falha de teste continua durante mais de duas semanas, terminará de maneira forçada.

6. Depois da ativação pós-falha concluir, deve também conseguir ver a máquina de réplica do Azure que aparece no Portal do Azure > **Virtual Machines**. Certifique-se de que a VM tem um tamanho adequado, está ligada à rede pretendida e está em execução.
7. Se [preparou para as ligações após a ativação pós-falha](#prepare-to-connect-to-Azure-VMs-after-failover), deve ser capaz de ligar para a VM do Azure.


## Monitorizar a implementação

Veja como pode monitorizar as definições de configuração, o estado e o estado de funcionamento da implementação da Recuperação de Sites:

1. Clique no nome do cofre para aceder ao dashboard **Essentials**. Neste dashboard, pode ver as tarefas de Recuperação de Sites, o estado de replicação, os planos de recuperação, o estado de funcionamento do servidor e os eventos.  Pode personalizar Essentials para mostrar os mosaicos e modelos que são mais úteis para si, incluindo o estado de outros cofres de Recuperação de Sites e de Cópia de Segurança.

    ![Essentials](./media/site-recovery-vmm-to-azure/essentials.png)

2. No mosaico **Estado de Funcionamento**, pode monitorizar os servidores de site (servidores VMM ou de configuração) que estão a ter problemas e os eventos gerados pela Recuperação de Sites nas últimas 24 horas.
3. Pode gerir e monitorizar a replicação nos mosaicos **Itens Replicados**, **Planos de Recuperação**, e **Tarefas da Recuperação de Sites**. Pode ver os pormenores das tarefas em **Definições** -> **Tarefas** -> **Tarefas da Recuperação de Sites**.


## Passos seguintes

Depois da implementação estar instalada e em execução, [saiba mais](site-recovery-failover.md) sobre os diferentes tipos de ativação pós-falha.



<!--HONumber=Sep16_HO4-->



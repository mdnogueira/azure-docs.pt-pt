<properties
    pageTitle="Aprovisionar uma Máquina Virtual do SQL Server | Microsoft Azure"
    description="Crie e ligue a uma máquina virtual do SQL Server no Azure através do Portal. Este tutorial utiliza o modo Resource Manager."
    services="virtual-machines-windows"
    documentationCenter="na"
    authors="rothja"
    editor=""
    manager="jhubbard"
    tags="azure-resource-manager" />
<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="09/21/2016"
    ms.author="jroth" />


# Aprovisionar uma máquina virtual do SQL Server no Portal do Azure

> [AZURE.SELECTOR]
- [Portal](virtual-machines-windows-portal-sql-server-provision.md)
- [PowerShell](virtual-machines-windows-ps-sql-create.md)

Este tutorial ponto-a-ponto mostra-lhe como utilizar o Portal do Azure para Aprovisionar uma máquina virtual com o SQL Server.

A galeria da máquina virtual (VM) do Azure inclui várias imagens que contêm o Microsoft SQL Server. Com alguns cliques, pode selecionar uma das imagens da VM do SQL na galeria e aprovisioná-la no seu ambiente do Azure.

Neste tutorial, irá:

- [Selecionar uma imagem da VM do SQL na galeria](#select-a-sql-vm-image-from-the-gallery)
- [Configurar e criar a VM](#configure-the-vm)
- [Abrir a VM com o Ambiente de Trabalho Remoto](#open-the-vm-with-remote-desktop)
- [Ligar ao SQL Server remotamente](#connect-to-sql-server-remotely)

## Selecionar uma imagem da VM do SQL na galeria

1. Inicie sessão no [Portal do Azure](https://portal.azure.com) utilizando a sua conta.

    >[AZURE.NOTE] Se não tiver uma conta do Azure, aceda a [Versão de avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

1. No Portal do Azure, clique em **Novo**. O portal abre o painel **Novo**. Os recursos da VM do SQL Server estão no **Virtual Machines** do Marketplace.

1. No painel **Novo**, clique em **Virtual Machines**.

1. Para ver todas as imagens disponíveis, clique em **Ver todas** no painel **Virtual Machines**.

    ![Painel Virtual Machines do Azure](./media/virtual-machines-windows-portal-sql-server-provision/azure-compute-blade.png)

1. Em **Servidores de bases de dados**, clique em **SQL Server**. Poderá ter de se deslocar para baixo para localizar a secção **Servidores de bases de dados**. Reveja os modelos do SQL Server disponíveis.

    ![Imagens do SQL da Galeria da Máquina Virtual](./media/virtual-machines-windows-portal-sql-server-provision/virtual-machine-gallery-sql-server.png)

1. Cada modelo identifica uma versão do SQL Server e um sistema operativo. Selecione uma destas imagens na lista. Em seguida, reveja o painel de detalhes que fornece uma descrição da imagem da máquina virtual.

    >[AZURE.NOTE] As imagens da VM do SQL incluem os custos de licenciamento para o SQL Server nos preços por minuto da VM que cria. Há outra opção que consiste em trazer a sua própria licença (BYOL) e pagar apenas a própria VM. Aos nomes dessas imagem é adicionado o prefixo {BYOL}. Para obter mais informações sobre esta opção, veja [Introdução ao SQL Server em Máquinas Virtuais do Azure](virtual-machines-windows-sql-server-iaas-overview.md).

1. Em **Selecionar um modelo de implementação**, confirme se **Resource Manager** está selecionado. O modelo de implementação recomendado para máquinas virtuais novas é o Resource Manager. Clique em **Criar**.

    ![Criar a VM do SQL com o Resource Manager](./media/virtual-machines-windows-portal-sql-server-provision/azure-compute-sql-deployment-model.png)

## Configurar a VM
Existem cinco painéis para configurar uma máquina virtual do SQL Server.

| Passo               | Descrição                          |
|---------------------|-------------------------------|
| **Noções básicas**              | [Configurar as definições básicas](#1-configure-basic-settings)      |
| **Tamanho**                | [Selecionar o tamanho da máquina virtual](#2-choose-virtual-machine-size)   |
| **Definições**            | [Configurar funcionalidades opcionais](#3-configure-optional-features)   |
| **Definições do SQL Server** | [Configurar definições do SQL Server](#4-configure-sql-server-settings) |
| **Resumo**             | [Reveja o resumo](#5-review-the-summary)            |

## 1. Configurar as definições básicas
No painel **Noções básicas**, forneça as seguintes informações:

* Introduza um **Nome** exclusivo para a máquina virtual.
* Especifique um **Nome de utilizador** para a conta de administrador local no VM. Esta conta também é adicionada à função de servidor fixa **sysadmin** do SQL Server.
* Forneça uma **Palavra-passe** forte.
* Se tiver várias subscrições, certifique-se de que a subscrição está correta para a nova VM.
* Na caixa **Grupo de recursos**, escreva um nome para um novo grupo de recursos. Em alternativa, para utilizar um grupo de recursos existente clique em **Selecionar existente**. Um grupo de recursos é uma coleção de recursos relacionados no Azure (máquinas virtuais, contas do Storage, redes virtuais, etc.).

    >[AZURE.NOTE] Utilizar um novo grupo de recursos é útil se estiver apenas a testar ou a saber mais sobre implementações do SQL Server no Azure. Depois de terminar o teste, elimine o grupo de recursos para eliminar automaticamente a VM e todos os recursos associados a esse grupo de recursos. Para mais informações sobre grupos de recursos, consulte o artigo [Descrição Geral do Azure Resource Manager](../resource-group-overview.md).

* Selecione uma **Localização** para esta implementação.
* Clique em **OK** para guardar as definições.

    ![Painel de Noções Básicas do SQL](./media/virtual-machines-windows-portal-sql-server-provision/azure-sql-basic.png)

## 2. Selecionar o tamanho da máquina virtual
No passo **Tamanho**, escolha um tamanho da máquina virtual no painel **Escolher um tamanho**. O painel inicialmente apresenta os tamanhos recomendados da máquina baseados no modelo que selecionou. Também calcula o custo mensal para executar a VM.

![Opções de Tamanho da VM do SQL](./media/virtual-machines-windows-portal-sql-server-provision/azure-sql-vm-choose-a-size.png)

Para cargas de trabalho de produção, recomendamos que selecione um tamanho da máquina virtual que suporte o [Premium Storage](../storage/storage-premium-storage.md). Se não necessitar desse nível de desempenho, utilize o botão **Ver todas**, que mostra todas as opções de tamanho da máquina. Por exemplo, poderá utilizar um tamanho mais pequeno da máquina para um ambiente de teste ou de desenvolvimento.

>[AZURE.NOTE] Para obter mais informações sobre os tamanhos da máquina virtual, consulte [Tamanhos das Virtual Machines](virtual-machines-windows-sizes.md). Para considerações acerca dos tamanhos da VM do SQL Server, consulte [Práticas recomendadas do SQL Server em Virtual Machines do Azure](virtual-machines-windows-sql-performance.md).

Selecione o tamanho da máquina e, em seguida, clique em **Selecionar**.

## 3. Configurar funcionalidades opcionais
No painel **Definições**, configure o Storage do Azure, as redes e a monitorização para a máquina virtual.

- Em **Armazenamento**, especifique um **Tipo de disco** Standard ou Premium (SSD). O Premium Storage é recomendado para cargas de trabalho de produção.

>[AZURE.NOTE] Se selecionar Premium (SSD) para um tamanho da máquina que não suporta o Premium Storage, o tamanho da máquina é alterado automaticamente.  

- Em **Conta do Storage**, pode aceitar o nome da conta do Storage automaticamente aprovisionado. Também pode clicar em **Conta do Storage** para escolher uma conta existente e configurar o tipo de conta do Storage. Por predefinição, o Azure cria uma nova conta do Storage com o armazenamento localmente redundante. Para mais informações sobre as opções de armazenamento, consulte o artigo [Replicação do Storage do Azure](../storage/storage-redundancy.md).

- Em **Rede**, pode aceitar os valores automaticamente preenchidos. Também pode clicar em cada funcionalidade para configurar manualmente a **Virtual Network**, a **Sub-rede**, o **Endereço IP público** e o **Grupo de Segurança de Rede**. Para efeitos deste tutorial, mantenha os valores predefinidos.

- O Azure ativa a **Monitorização** por predefinição com a mesma conta do Storage designada para a VM. Pode alterar estas definições aqui.

- Em **Conjunto de Disponibilidade**, especifique um conjunto de disponibilidade. Para efeitos deste tutorial, pode selecionar **Nenhum**. Se planear configurar Grupos de Disponibilidade AlwaysOn do SQL Server, configure a disponibilidade para evitar recriar a máquina virtual.  Para obter mais informações, consulte o artigo [Gerir a Disponibilidade das Virtual Machines](virtual-machines-windows-manage-availability.md).

Quando tiver terminado de configurar estas definições, clique em **OK**.

## 4. Configurar definições do SQL Server
No painel **Definições do SQL Server**, configure definições e otimizações específicas para o SQL Server. As definições que pode configurar para o SQL Server incluem o seguinte.

| Definição                |
|---------------------|
| [Conectividade](#connectivity)              |
| [Autenticação](#authentication)                |
| [Configuração do armazenamento](#storage-configuration)            |
| [Aplicação de Patches Automatizada](#automated-patching) |
| [Cópia de Segurança Automatizada](#automated-backup)             |
| [Integração do Cofre de Chaves do Azure](#azure-key-vault-integration)             |
| [Serviços R](#r-services) |

### Conectividade
Em **Conectividade do SQL**, especifique o tipo de acesso que pretende para a instância do SQL Server nesta VM. Para efeitos deste tutorial, selecione **Público (Internet)** para permitir ligações ao SQL Server a partir de máquinas ou serviços na Internet. Com esta opção selecionada, o Azure configura automaticamente a firewall e o grupo de segurança de rede para permitir tráfego na porta 1433.  

![Opções de Conectividade do SQL](./media/virtual-machines-windows-portal-sql-server-provision/azure-sql-arm-connectivity-alt.png)

Para ligar ao SQL Server através da Internet, também tem de ativar a Autenticação do SQL Server, que se descreve na secção seguinte.

>[AZURE.NOTE] É possível adicionar mais restrições para as comunicações de rede para a VM do SQL Server. Pode fazê-lo ao editar o Grupo de Segurança de Rede após a VM ser criada. Para obter mais informações, consulte o artigo [O que é um Grupo de Segurança de Rede (NSG)?](../virtual-network/virtual-networks-nsg.md)

Se preferir não ativar ligações para o Motor de Base de dados através da Internet, escolha uma das seguintes opções:

- **Local (no interior da VM)** para permitir ligações ao SQL Server, apenas a partir de dentro da VM.
- **Privada (na Virtual Network)** para permitir ligações ao SQL Server a partir de máquinas ou serviços na mesma rede virtual.

>[AZURE.NOTE] A imagem da máquina virtual da edição SQL Server Express não ativa automaticamente o protocolo TCP/IP. Isto verifica-se mesmo nas opções de conectividade Pública e Privada. Relativamente à edição Express, tem de utilizar o Gestor de Configuração do SQL Server para [ativar manualmente o protocolo TCP/IP](#configure-sql-server-to-listen-on-the-tcp-protocol) depois de criar a VM.

Em geral, melhore a segurança, selecionando a conectividade mais restritiva que permite o seu cenário. Mas todas as opções têm capacidade de segurança através das regras do Grupo de Segurança de Rede e Autenticação do SQL/Windows.

A predefinição da **Porta** é 1433. Pode especificar um número de porta diferente.
Para obter mais informações, consulte o artigo [Ligar a uma Máquina Virtual do SQL Server (Resource Manager) | Microsoft Azure](virtual-machines-windows-sql-connect.md).

### Autenticação
Se necessitar da Autenticação do SQL Server, clique em **Ativar** em **Autenticação do SQL**.

![Autenticação do SQL Server](./media/virtual-machines-windows-portal-sql-server-provision/azure-sql-arm-authentication.png)

>[AZURE.NOTE] Se pretender aceder ao SQL Server através da Internet (ou seja, a opção de Conectividade pública), tem de ativar a autenticação do SQL aqui. O acesso público ao SQL Server requer a utilização da Autenticação do SQL.

Se ativar a Autenticação do SQL Server, especifique um **Nome de início de sessão** e uma **Palavra-passe**. Este nome de utilizador está configurado como um início de sessão de Autenticação do SQL Server e é membro da função de servidor fixa **sysadmin**. Consulte o artigo [Escolher um Modo de Autenticação](http://msdn.microsoft.com/library/ms144284.aspx) para obter mais informações sobre Modos de Autenticação.

Se não ativar a Autenticação do SQL Server, em seguida, pode utilizar a conta de Administrador local na VM para ligar à instância do SQL Server.

### Configuração do armazenamento
Clique em **Configuração do armazenamento** para especificar os requisitos de armazenamento.

![Configuração do Armazenamento do SQL](./media/virtual-machines-windows-portal-sql-server-provision/azure-sql-arm-storage.png)

>[AZURE.NOTE] Se selecionar Armazenamento Standard, esta opção não está disponível. A otimização de armazenamento automática só está disponível para o Premium Storage.

Pode especificar os requisitos como operações de entrada/saída por segundo (IOPs), débito em MB/s e tamanho de armazenamento total. Configure estes valores, utilizando as escalas deslizantes. O portal calcula automaticamente o número de discos com base nestes requisitos.

Por predefinição, o Azure otimiza o armazenamento de 5000 IOPs, 200 MB e 1 TB de espaço de armazenamento. Pode alterar estas definições de armazenamento com base na carga de trabalho. Em **Armazenamento otimizado para**, selecione uma das seguintes opções:

- **Geral** é a predefinição e suporta a maioria das cargas de trabalho.
- O processamento **Transacional** otimiza o armazenamento para cargas de trabalho OLTP de bases de dados tradicionais.
- O **Armazenamento de dados** otimiza o armazenamento para cargas de trabalho analíticas e de relatórios.

>[AZURE.NOTE] Os limites superiores nos controlos de deslize variam consoante o tamanho da máquina virtual selecionada.

### Aplicação de patches automatizada
A **Aplicação de patches automatizada** está ativada por predefinição. A Aplicação de patches automatizada permite ao Azure automaticamente aplicar o patch do SQL Server e o sistema operativo. Especifique um dia da semana, a hora e a duração de uma janela de manutenção. O Azure executa a aplicação de patches nesta janela de manutenção. A agenda da janela de manutenção utiliza a região da VM para a hora. Se não pretender que o Azure aplique automaticamente o patch do SQL Server e o sistema operativo, clique em **Desativar**.  

![Aplicação de Patches Automatizada do SQL](./media/virtual-machines-windows-portal-sql-server-provision/azure-sql-arm-patching.png)

Para obter mais informações, consulte o artigo [Aplicação de Patches Automatizada para o SQL Server nas Virtual Machines do Azure](virtual-machines-windows-sql-automated-patching.md).

### Cópia de segurança automatizada
Ative as cópias de segurança de bases de dados automáticas em **Cópia de segurança automatizada**. A cópia de segurança automatizada está desativada por predefinição.

Quando ativa a cópia de segurança automatizada do SQL, pode configurar o seguinte:

- Período de retenção (dias) para cópias de segurança
- Conta do Storage a utilizar para as cópias de segurança
- Opção de encriptação e palavra-passe para as cópias de segurança

Para encriptar a cópia de segurança, clique em **Ativar**. Em seguida, especifique a **Palavra-passe**. O Azure cria um certificado para encriptar as cópias de segurança e utiliza a palavra-passe especificada para proteger o certificado.

![Cópia de Segurança Automatizada do SQL](./media/virtual-machines-windows-portal-sql-server-provision/azure-sql-arm-autobackup.png)

 Para obter mais informações, consulte o artigo [Cópia de Segurança Automatizada para o SQL Server nas Virtual Machines do Azure](virtual-machines-windows-sql-automated-backup.md).

### Integração do Cofre de Chaves do Azure
Para armazenar segredos de segurança no Azure para a encriptação, clique em **Integração do cofre de chaves do Azure** e clique em **Ativar**.

![Integração do Cofre de Chaves do SQL Azure](./media/virtual-machines-windows-portal-sql-server-provision/azure-sql-arm-akv.png)

A tabela seguinte lista os parâmetros necessários para configurar a Integração do Cofre de Chaves do Azure.

|PARÂMETRO|DESCRIÇÃO|EXEMPLO|
|----------|----------|-------|
|**URL do Cofre de Chaves** |A localização do cofre de chaves.|https://contosokeyvault.vault.azure.net/ |
|**Nome principal** |Nome principal do serviço Azure Active Directory. Este nome também é referido como o ID de Cliente.  |fde2b411-33d5-4e11-af04eb07b669ccf2|
| **Segredo principal**|Segredo principal do serviço Azure Active Directory. Este segredo também é referido como o Segredo do Cliente. | 9VTJSQwzlFepD8XODnzy8n2V01Jd8dAjwm/azF1XDKM=|
|**Nome da credencial**|**Nome da credencial**: a Integração AKV cria uma credencial dentro do SQL Server, permitindo que a VM tenha acesso ao cofre de chaves. Escolha um nome para esta credencial.| mycred1|

Para obter mais informações, consulte o artigo [Configurar a Integração do Cofre de Chaves do Azure para o SQL Server em VMs do Azure](virtual-machines-windows-ps-sql-keyvault.md).

Quando tiver terminado de configurar as definições do SQL Server, clique em **OK**.

### Serviços R
Para a edição SQL Server 2016 Enterprise, tem a opção de ativar os [Serviços R do SQL Server](https://msdn.microsoft.com/library/mt604845.aspx). Esta ação permite-lhe utilizar a análise avançada com o SQL Server 2016. Clique em **Ativar** no painel **Definições do SQL Server**.

![Ativar os Serviços R do SQL Server](./media/virtual-machines-windows-portal-sql-server-provision/azure-vm-sql-server-r-services.png)

>[AZURE.NOTE] Para imagens do SQL Server que não são da edição 2016 Enterprise, a opção para ativar os Serviços R está desativada.

## 5. Reveja o resumo
No painel **Resumo**, reveja o resumo e clique em **OK** para criar o SQL Server, o grupo de recursos e recursos especificados para esta VM.

Pode monitorizar a implementação a partir do Portal do Azure. O botão **Notificações** na parte superior do ecrã mostra o estado básico da implementação.

>[AZURE.NOTE] Para lhe fornecer uma ideia dos tempos da implementação, implementei uma VM do SQL para a região EUA Leste com as predefinições. Esta implementação de teste demorou um total de 26 minutos a concluir. Mas poderá ter uma implementação mais lenta ou mais rápida com base na sua região e nas definições selecionadas.

## Abrir a VM com o Ambiente de Trabalho Remoto

Utilize os seguintes passos para ligar à máquina virtual com o Ambiente de Trabalho Remoto:

1. Após criar a VM do Azure, o ícone para a VM aparece no dashboard do Azure. Também pode encontrá-lo ao navegar pelas suas máquinas virtuais existentes. Clique na sua nova máquina virtual do SQL. Um painel **Máquina virtual** apresenta os detalhes da máquina virtual.
1. Na parte superior do painel **Máquina virtual**, clique em **Ligar**.
1. O browser transfere um ficheiro RDP para a VM. Abra o ficheiro RDP.
    ![Ambiente de Trabalho Remoto para a VM do SQL](./media/virtual-machines-windows-portal-sql-server-provision/azure-sql-vm-remote-desktop.png)
1. A Ligação ao Ambiente de Trabalho Remoto notifica-o de que não é possível identificar o publicador desta ligação remota. Clique em **Ligar** para continuar.
1. Na caixa de diálogo **Segurança do Windows**, clique em **Utilizar outra conta**.
1. Para o **Nome de utilizador**, escreva **\<nome de utilizador>**, em que <user name> é o nome de utilizador que especificou quando configurou a VM. Tem de adicionar uma barra invertida inicial antes do nome.
1. Escreva a **Palavra-passe** que configurou previamente para esta VM e, em seguida, clique em **OK** para estabelecer ligação.
1. Se outra **Ligação ao Ambiente de Trabalho Remoto** lhe perguntar se pretende ligar, clique em **Sim**.

Depois de ligar à máquina virtual do SQL Server, pode iniciar o SQL Server Management Studio e estabelecer ligação à Autenticação do Windows com as suas credenciais de administrador local. Se ativou a Autenticação do SQL Server, também pode ligar com Autenticação do SQL Server com o início de sessão do SQL e a palavra-passe que configurou durante o aprovisionamento.

O acesso ao computador permite-lhe alterar diretamente as definições do SQL Server e da máquina com base nos seus requisitos. Por exemplo, pode configurar as definições da firewall ou alterar as definições de configuração do SQL Server.

## Ligar ao SQL Server remotamente

Neste tutorial, selecionamos o acesso **Público** para a máquina virtual e a **Autenticação do SQL Server**. Estas definições configuraram automaticamente a máquina virtual para permitir ligações ao SQL Server a partir de qualquer cliente através da Internet (partindo do princípio de que tem o início de sessão SQL correto).

>[AZURE.NOTE] Se não tiver selecionado a opção Público durante o aprovisionamento, são necessários passos adicionais para aceder à sua instância do SQL Server através da Internet. Para obter mais informações, consulte o artigo [Ligar a uma Máquina Virtual do SQL Server](virtual-machines-windows-sql-connect.md).

As secções seguintes mostram como ligar à sua instância do SQL Server na VM a partir de um computador diferente, através da Internet.

> [AZURE.INCLUDE [Connect to SQL Server in a VM Resource Manager](../../includes/virtual-machines-sql-server-connection-steps-resource-manager.md)]

## Passos Seguintes
Para outras informações sobre como utilizar o SQL Server no Azure, consulte o artigo [SQL Server em Virtual Machines do Azure](virtual-machines-windows-sql-server-iaas-overview.md) e as [Perguntas Mais Frequentes](virtual-machines-windows-sql-server-iaas-faq.md).

Para obter uma descrição geral do vídeo do SQL Server em Virtual Machines do Azure, veja [A VM do Azure é a melhor plataforma para o SQL Server 2016](https://channel9.msdn.com/Events/DataDriven/SQLServer2016/Azure-VM-is-the-best-platform-for-SQL-Server-2016).

[Explorar o Percurso de Aprendizagem](https://azure.microsoft.com/documentation/learning-paths/sql-azure-vm/) para o SQL Server em máquinas virtuais do Azure.



<!--HONumber=Sep16_HO3-->



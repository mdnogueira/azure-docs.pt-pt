---
title: SQL Server Business Intelligence | Microsoft Docs
description: "Este tópico utiliza recursos criados com o modelo de implementação clássica e descreve as funcionalidades de Business Intelligence (BI) disponíveis para o SQL Server em execução no Azure máquinas virtuais (VMs)."
services: virtual-machines-windows
documentationcenter: na
author: guyinacube
manager: erikre
editor: monicar
tags: azure-service-management
ms.assetid: c681e7a7-eeda-48aa-bc35-6277f4828244
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/30/2017
ms.author: asaxton
ms.openlocfilehash: 65bada117e7d005362b0ac0ce7cc5336a92e0889
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="sql-server-business-intelligence-in-azure-virtual-machines"></a>SQL Server Business Intelligence em Máquinas Virtuais do Azure
> [!IMPORTANT] 
> O Azure tem dois modelos de implementação diferentes para criar e trabalhar com recursos: [Resource Manager e clássico](../../../azure-resource-manager/resource-manager-deployment-model.md). Este artigo abrange utilizando o modelo de implementação clássica. A Microsoft recomenda que as implementações mais novas utilizem o modelo Resource Manager.

Galeria da máquina de Virtual do Microsoft Azure inclui imagens que contêm instalações do SQL Server. As edições do SQL Server suportadas nas imagens de galeria são os mesmos ficheiros de instalação, que pode instalar em computadores no local e máquinas virtuais. Este tópico resume as funcionalidades do SQL Server Business Intelligence (BI) instaladas nas imagens e passos de configuração necessários depois de uma máquina virtual estar aprovisionada. Este tópico também descreve as topologias de implementação suportado para funcionalidades de BI e melhores práticas.

## <a name="license-considerations"></a>Considerações de licença
Existem duas formas de licença do SQL Server em máquinas virtuais do Microsoft Azure:

1. Vantagens de mobilidade de licenças que fazem parte do Software Assurance. Para obter mais informações, consulte [licença mobilidade através do Software Assurance no Azure](https://azure.microsoft.com/pricing/license-mobility/).
2. Paga por taxa de hora de Virtual Machines do Azure com o SQL Server instalada. Consulte a secção "SQL Server" [preços das Virtual Machines](https://azure.microsoft.com/pricing/details/virtual-machines/#Sql).

Para obter mais informações sobre as taxas de licenciamento e atuais, consulte [FAQ de licenciamento de máquinas virtuais](https://azure.microsoft.com/pricing/licensing-faq/%20/).

## <a name="sql-server-images-available-in-azure-virtual-machine-gallery"></a>SQL Server imagens disponíveis na Galeria de Máquina Virtual do Azure
Galeria da máquina de Virtual do Microsoft Azure inclui várias imagens que contêm o Microsoft SQL Server. O software instalado nas imagens de máquina virtual varia consoante a versão do sistema operativo e a versão do SQL Server. A lista de imagens disponíveis na galeria da máquina virtual do Azure é alterado frequentemente.

<!--![SQL image in azure VM gallery](./media/virtual-machines-windows-classic-ps-sql-bi/IC741367.png)-->
![Imagem SQL na Galeria de VM do Azure](./media/virtual-machines-windows-classic-ps-sql-bi/vm-sql-images.png)

![PowerShell](./media/virtual-machines-windows-classic-ps-sql-bi/IC660119.gif) O seguinte script do PowerShell devolve a lista de imagens do Azure que contêm "Do SQL Server-" no ImageName:

    # assumes you have already uploaded a management certificate to your Microsoft Azure Subscription. View the thumbprint value from the "Subscriptions" menu in Azure portal.

    $subscriptionID = ""    # REQUIRED: Provide your subscription ID.
    $subscriptionName = "" # REQUIRED: Provide your subscription name.
    $thumbPrint = "" # REQUIRED: Provide your certificate thumbprint.
    $certificate = Get-Item cert:\currentuser\my\$thumbPrint # REQUIRED: If your certificate is in a different store, provide it here.-Ser  store is the one specified with the -ss parameter on MakeCert

    Set-AzureSubscription -SubscriptionName $subscriptionName -Certificate $certificate -SubscriptionID $subscriptionID

    Write-Host -foregroundcolor green "List of available gallery images where imagename contains 2016"
    Write-Host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
    get-azurevmimage | where {$_.ImageName -Like "*SQL-Server-2016*"} | select imagename,category, location, label, description

    Write-Host -foregroundcolor green "List of available gallery images where imagename contains 2014"
    Write-Host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
    get-azurevmimage | where {$_.ImageName -Like "*SQL-Server-2014*"} | select imagename,category, location, label, description

Para obter mais informações sobre edições e funcionalidades suportadas pelo SQL Server, consulte o seguinte:

* [Edições do SQL Server](https://www.microsoft.com/server-cloud/products/sql-server-editions/#fbid=Zae0-E6r5oh)
* [Funcionalidades suportadas pelas edições do SQL Server 2016](https://msdn.microsoft.com/library/cc645993.aspx)

### <a name="bi-features-installed-on-the-sql-server-virtual-machine-gallery-images"></a>Funcionalidades BI instaladas nas imagens de Galeria de Máquina Virtual do servidor de SQL
A tabela seguinte resume as funcionalidades de Business Intelligence instaladas nas imagens de galeria comuns do Microsoft Azure Virtual Machine para o SQL Server:

* SQL Server 2016 SP1 Enterprise
* SQL Server 2016 SP1 padrão
* Enterprise do SQL Server 2014 SP2
* SQL Server 2014 SP2 Standard
* SQL Server 2012 SP3 Enterprise
* SQL Server 2012 SP3 padrão

| Funcionalidade do SQL Server BI | Instalado na imagem da Galeria | Notas |
| --- | --- | --- |
| **Reporting Services de modo nativo** |Sim |Instalado, mas necessita de configuração, incluindo o URL do Gestor de relatórios. Consulte a secção [configurar Reporting Services](#configure-reporting-services). |
| **Modo do SharePoint do Reporting Services** |Não |A imagem de galeria da máquina de Virtual do Microsoft Azure não inclui SharePoint ou o SharePoint ficheiros de instalação. <sup>1</sup> |
| **Extração de dados e Analysis Services Multidimensional (OLAP)** |Sim |Instalado e configurado como a instância do Analysis Services predefinida |
| **Tabela do Analysis Services** |Não |Suportado no SQL Server 2012, 2014 e 2016 imagens, mas não está instalado por predefinição. Instale outra instância do Analysis Services. Consulte a secção instalar outros serviços do SQL Server e as funcionalidades neste tópico. |
| **Analysis Services Power Pivot para SharePoint** |Não |A imagem de galeria da máquina de Virtual do Microsoft Azure não inclui SharePoint ou o SharePoint ficheiros de instalação. <sup>1</sup> |

<sup>1</sup> para obter informações adicionais no SharePoint e máquinas virtuais do Azure, consulte [Microsoft Azure arquiteturas para SharePoint 2013](https://technet.microsoft.com/library/dn635309.aspx) e [SharePoint implementação em do Microsoft Azure Virtual Machines](https://www.microsoft.com/download/details.aspx?id=34598).

![PowerShell](./media/virtual-machines-windows-classic-ps-sql-bi/IC660119.gif) Execute o seguinte comando do PowerShell para obter uma lista de serviços instalados que contêm "SQL" no nome do serviço.

    get-service | Where-Object{ $_.DisplayName -like '*SQL*' } | Select DisplayName, status, servicetype, dependentservices | format-Table -AutoSize

## <a name="general-recommendations-and-best-practices"></a>Melhores práticas e recomendações gerais
* O mínimo recomendado tamanho para uma máquina virtual é **A3** ao utilizar o SQL Server Enterprise Edition. O **A4** tamanho da máquina virtual é recomendado para implementações SQL Server BI Analysis Services e o Reporting Services.
  
    Para obter informações sobre os tamanhos atuais da VM, consulte [tamanhos de Máquina Virtual do Azure](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* É uma prática de segurança para gestão de discos para armazenar dados, registo e ficheiros de cópia de segurança em unidades que **C**: e **D**:. Por exemplo, criar discos de dados **i**: e **F**:.
  
  * A unidade de colocação em cache da política para a unidade de predefinição **C**: não é o ideal para trabalhar com dados.
  * O **D**: unidade é uma unidade temporária que é utilizada principalmente para o ficheiro de paginação. O **D**: unidade não é continuada e não é guardada no armazenamento de Blobs. Reposição de tamanho de tarefas de gestão, tais como uma alteração à máquina virtual a **D**: unidade. É recomendado **não** utilizar o **D**: unidade para os ficheiros de base de dados, incluindo tempdb.
    
    Para obter mais informações sobre como criar e anexar discos, consulte [como anexar um disco de dados para uma Máquina Virtual](../classic/attach-disk.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).
* Parar ou desinstalar os serviços que não planeia utilizar. Para o exemplo se a máquina virtual só é utilizada para o Reporting Services, parar ou desinstalar o Analysis Services e do SQL Server Integration Services. A imagem seguinte é um exemplo dos serviços que são iniciadas por predefinição.
  
    ![Serviços do SQL Server](./media/virtual-machines-windows-classic-ps-sql-bi/IC650107.gif)
  
  > [!NOTE]
  > O motor de base de dados do SQL Server é necessário em cenários de BI suportados. Num servidor único topologia VM, o motor de base de dados é necessário para estar em execução na mesma VM.
  
    Para obter mais informações, consulte o seguinte: [desinstalar Reporting Services](https://msdn.microsoft.com/library/hh479745.aspx) e [desinstalar uma instância do Analysis Services](https://msdn.microsoft.com/library/ms143687.aspx).
* Verifique **Windows Update** novas 'atualizações importantes'. As imagens de máquinas de virtuais do Microsoft Azure são frequentemente atualizadas; No entanto, as atualizações importantes pode ficar disponíveis a partir do **Windows Update** depois da imagem VM foi atualizada pela última vez.

## <a name="example-deployment-topologies"></a>Topologias de implementação de exemplo
Seguem-se as implementações de exemplo que utilizam máquinas virtuais do Microsoft Azure. As topologias destas diagramas são apenas algumas das topologias possíveis que pode utilizar com as funcionalidades do SQL Server BI e máquinas virtuais do Microsoft Azure.

### <a name="single-virtual-machine"></a>Única máquina Virtual
Analysis Services, Reporting Services, o motor de base de dados do SQL Server e origens de dados numa única máquina virtual.

![cenário de BI iass com 1 máquina virtual](./media/virtual-machines-windows-classic-ps-sql-bi/IC650108.gif)

### <a name="two-virtual-machines"></a>Duas máquinas virtuais
* Analysis Services, o Reporting Services e o motor de base de dados do SQL Server numa única máquina virtual. Esta implementação inclui as bases de dados do servidor de relatórios.
* Origens de dados numa VM segundo. A segunda VM inclui o motor de base de dados do SQL Server como origem de dados.

![cenário de BI iaas com 2 máquinas virtuais](./media/virtual-machines-windows-classic-ps-sql-bi/IC650109.gif)

### <a name="mixed-azure--data-on-azure-sql-database"></a>Azure misto – dados na base de dados SQL do Azure
* Analysis Services, o Reporting Services e o motor de base de dados do SQL Server numa única máquina virtual. Esta implementação inclui as bases de dados do servidor de relatórios.
* Origem de dados é a base de dados SQL do Azure.

![vm do BI iaas cenários e AzureSQL como origem de dados](./media/virtual-machines-windows-classic-ps-sql-bi/IC650110.gif)

### <a name="hybrid-data-on-premises"></a>Híbrida – dados no local
* Este exemplo de implementação do Analysis Services, Reporting Services e o motor de base de dados do SQL Server executam numa única máquina virtual. A máquina virtual aloja as bases de dados do servidor de relatórios. A máquina virtual está associada a um domínio no local através da rede Virtual do Azure ou alguns outro VPN túnel solução.
* Origem de dados está no local.

![BI iaas cenários vm e no local origens de dados](./media/virtual-machines-windows-classic-ps-sql-bi/IC654384.gif)

## <a name="reporting-services-native-mode-configuration"></a>Configuração de modo nativo do Reporting Services
Imagem da galeria da máquina virtual para o SQL Server inclui modo Reporting Services nativo instalado, no entanto, o servidor de relatórios não está configurado. Os passos nesta secção Configurar o servidor de relatórios do Reporting Services. Para obter mais informações sobre como configurar o modo nativo de serviços de relatórios, consulte [instalar Reporting Services nativo modo Report Server (SSRS)](https://msdn.microsoft.com/library/ms143711.aspx).

> [!NOTE]
> Para obter conteúdo semelhante que utiliza scripts do Windows PowerShell para configurar o servidor de relatórios, consulte [utilize o PowerShell para criar um Azure VM com um nativo modo de servidor de relatórios](../classic/ps-sql-report.md).

### <a name="connect-to-the-virtual-machine-and-start-the-reporting-services-configuration-manager"></a>Ligar à máquina Virtual e iniciar o Gestor de configuração do Reporting Services
Existem dois fluxos de trabalho comuns para ligar uma máquina de Virtual do Azure:

* Para ligar o nome da máquina virtual, clique em e, em seguida, clique em **Connect**. Abre-se de uma ligação de ambiente de trabalho remoto e o nome do computador é preenchido automaticamente.
  
    ![ligar à máquina virtual do azure](./media/virtual-machines-windows-classic-ps-sql-bi/IC650112.gif)
* Ligar à máquina virtual com ligação de ambiente de trabalho remoto do Windows. Na interface de utilizador do ambiente de trabalho remoto:
  
  1. Tipo de **nome do serviço de nuvem** como o nome do computador.
  2. Tipo de dois pontos (:) e o número de porta pública que esteja configurado para o TCP ambiente de trabalho ponto final remoto.
     
      Myservice.cloudapp.NET:63133
     
      Para obter mais informações, consulte [o que é um serviço em nuvem?](https://azure.microsoft.com/manage/services/cloud-services/what-is-a-cloud-service/).


**Iniciar o Reporting Services Configuration Manager**

No **Windows Server 2012/2016**:

1. Do **iniciar** ecrã, escreva **Reporting Services** para ver uma lista de aplicações.
2. Clique com botão direito **Reporting Services Configuration Manager** e clique em **executar como administrador**.

No **Windows Server 2008 R2**:

1. Clique em **iniciar**e, em seguida, clique em **todos os programas**.
2. Clique em **Microsoft SQL Server 2016**.
3. Clique em **ferramentas de configuração**.
4. Clique com botão direito **Reporting Services Configuration Manager** e clique em **executar como administrador**.

Ou:

1. Clique em **iniciar**.
2. No **procurar programas e ficheiros** tipo de caixa de diálogo **o reporting services**. Se a VM com o Windows Server 2012, escreva **o reporting services** no ecrã Iniciar o Windows Server 2012.
3. Clique com botão direito **Reporting Services Configuration Manager** e clique em **executar como administrador**.
   
    ![pesquisa do ssrs configuration manager](./media/virtual-machines-windows-classic-ps-sql-bi/IC650113.gif)

### <a name="configure-reporting-services"></a>Configurar o Reporting Services
**Conta de serviço e o URL do serviço web:**

1. Certifique-se a **nome do servidor** é o nome do servidor local e clique em **Connect**.
2. Tenha em atenção a em branco **o nome de base de dados do servidor de relatório**. A base de dados é criado quando a configuração for concluída.
3. Certifique-se a **estado do servidor de relatórios** é **iniciado**. Se pretender verificar o serviço no Gestor de servidor do Windows, o serviço está a **SQL Server Reporting Services** serviço do Windows.
4. Clique em **conta de serviço** e alterar a conta, conforme necessário. Se a máquina virtual for utilizada num ambiente associados a um domínio, incorporada **ReportServer** conta é suficiente. Para obter mais informações sobre a conta de serviço, consulte [conta de serviço](https://msdn.microsoft.com/library/ms189964.aspx).
5. Clique em **URL do serviço Web** no painel esquerdo.
6. Clique em **aplicar** para configurar os valores predefinidos.
7. Tenha em atenção o **URLs do serviço do servidor Web de relatórios**. Tenha em atenção que a porta TCP predefinida é 80 e faz parte do URL. Num passo posterior, pode criar um Endpoint de Máquina Virtual do Microsoft Azure para a porta.
8. No **resultados** painel, verifique as ações foi concluídas com êxito.

**Base de dados:**

1. Clique em **base de dados** no painel esquerdo.
2. Clique em **alterar base de dados**.
3. Certifique-se **criar uma nova base de dados de servidor de relatório** está selecionada e, em seguida, clique em seguinte.
4. Certifique-se **nome do servidor** e clique em **Testar ligação**.
5. Se o resultado é **Testar ligação foi concluída com êxito**, clique em **OK** e, em seguida, clique em **seguinte**.
6. Tenha em atenção é o nome de base de dados **ReportServer** e **modo de servidor de relatórios** é **nativo** , em seguida, clique em **seguinte**.
7. Clique em **seguinte** no **credenciais** página.
8. Clique em **seguinte** no **resumo** página.
9. Clique em **seguinte** no **progresso e concluir** página.

**Web URL do Portal ou URL do Gestor de relatórios para o 2012 e 2014:**

1. Clique em **URL do Portal Web**, ou **URL do Gestor de relatórios** para 2014 e 2012, no painel esquerdo.
2. Clique em **Aplicar**.
3. No **resultados** painel, verifique as ações foi concluídas com êxito.
4. Clique em **saída**.

Para obter informações sobre as permissões do servidor de relatórios, consulte [conceder permissões num servidor de relatórios de modo nativo](https://msdn.microsoft.com/library/ms156014.aspx).

### <a name="browse-to-the-local-report-manager"></a>Navegue para o Gestor de relatórios local
Para verificar a configuração, navegue para o Gestor de relatórios na VM.

1. Na VM, inicie o Internet Explorer com privilégios de administrador.
2. Procure http://localhost/reports na VM.

### <a name="to-connect-to-remote-web-portal-or-report-manager-for-2014-and-2012"></a>Para ligar ao portal web remoto, ou o Gestor de relatórios para 2014 e 2012
Se pretender ligar para o web portal ou o Gestor de relatórios para 2014 e de 2012, a máquina virtual de um computador remoto, crie uma nova máquina virtual ponto final TCP. Por predefinição, o servidor de relatórios escuta pedidos de HTTP no **porta 80**. Se configurar os URLs do servidor de relatórios para utilizar uma porta diferente, tem de especificar esse número de porta as instruções seguintes.

1. Crie um ponto final da Máquina Virtual de porta TCP 80. Para obter mais informações consulte, a [pontos finais de Máquina Virtual e portas de Firewall](#virtual-machine-endpoints-and-firewall-ports) secção neste documento.
2. Abra a porta 80 na firewall da máquina virtual.
3. Navegar para o portal web ou report manager, utilizando a Máquina Virtual do Azure **nome DNS** como o nome do servidor no URL. Por exemplo:
   
    **Servidor de relatórios**: http://uebi.cloudapp.net/reportserver **Web portal**: http://uebi.cloudapp.net/reports
   
    [Configurar uma Firewall para acesso ao servidor de relatórios](https://msdn.microsoft.com/library/bb934283.aspx)

### <a name="to-create-and-publish-reports-to-the-azure-virtual-machine"></a>Para criar e publicar os relatórios para a Máquina Virtual do Azure
A tabela seguinte resume algumas das opções disponíveis para publicar relatórios existentes a partir de um computador no local para o servidor de relatórios alojados do Microsoft Azure Virtual Machine:

* **Report Builder**: A máquina virtual inclui a, clique em-uma vez a versão do Microsoft SQL Server Report Builder para SQL Server 2014 e 2012. Para Report builder a primeira hora de início na máquina virtual com o SQL Server 2016:
  
  1. Inicie o browser com privilégios administrativos.
  2. Navegue até ao portal da web, na máquina virtual e selecione o **transferir** ícone no canto superior direito.
  3. Selecione **Report Builder**.
     
     Para obter mais informações, consulte [iniciar o Report Builder](https://msdn.microsoft.com/library/ms159221.aspx).
* **SQL Server Data Tools**: VM: SQL Server Data Tools está instalada na máquina virtual e pode ser utilizada para criar **Report Server Projects** e os relatórios na máquina virtual. SQL Server Data Tools pode publicar os relatórios para o servidor de relatórios na máquina virtual.
* **SQL Server Data Tools: Remoto**: no seu computador local, criar um projeto do Reporting Services no SQL Server Data Tools que contém relatórios do Reporting Services. Configure o projeto para estabelecer ligação com o URL do serviço web.
  
    ![Propriedades do projeto SSDT para o projeto do SSRS](./media/virtual-machines-windows-classic-ps-sql-bi/IC650114.gif)
* Criar um. VHD unidade de disco rígido que contém relatórios e, em seguida, carregar e anexar a unidade.
  
  1. Criar um. Unidade de disco rígido de VHD no computador local que contém os seus relatórios.
  2. Crie e instale um certificado de gestão.
  3. Carregar o ficheiro VHD para o Azure utilizando o cmdlet Add-AzureVHD [criar e carregar um VHD do Windows Server para o Azure](../classic/createupload-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).
  4. Anexe o disco à máquina virtual.

## <a name="install-other-sql-server-services-and-features"></a>Instalar outros serviços do SQL Server e funcionalidades
Para instalar serviços do SQL Server adicionais, tais como o Analysis Services no modo de tabela, execute o Assistente de configuração do SQL server. Os ficheiros de configuração estiverem num disco local da máquina virtual.

1. Clique em **iniciar** e, em seguida, clique em **todos os programas**.
2. Clique em **Microsoft SQL Server 2016**, **Microsoft SQL Server 2014** ou **Microsoft SQL Server 2012** e, em seguida, clique em **ferramentas de configuração**.
3. Clique em **Centro de instalação do SQL Server**.

Ou execute C:\SQLServer_13.0_full\setup.exe, C:\SQLServer_12.0_full\setup.exe ou C:\SQLServer_11.0_full\setup.exe

> [!NOTE]
> Na primeira vez que executar a configuração do SQL Server, mais ficheiros de configuração podem ser transferidos e requerem um reinício da máquina virtual e um reinício do programa de configuração do SQL Server.
> 
> Se precisar de personalizar repetidamente a imagem selecionada a partir do Microsoft Azure Virtual Machine, considere criar sua própria imagem do SQL Server. Funcionalidade do Analysis Services SysPrep foi ativada com o SQL Server 2012 SP1 CU2. Para obter mais informações, consulte [considerações para instalar o SQL Server utilizando o SysPrep](https://msdn.microsoft.com/library/ee210754.aspx) e [suporte de Sysprep para funções de servidor](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles).
> 
> 

### <a name="to-install-analysis-services-tabular-mode"></a>Para instalar o modo de tabela do Analysis Services
Os passos nesta secção **resumir** a instalação do modo de tabela do Analysis Services. Para obter mais informações, consulte o seguinte:

* [Instalar o Analysis Services no modo de tabela](https://msdn.microsoft.com/library/hh231722.aspx)
* [Tabela modelação (Adventure Works Tutorial)](https://msdn.microsoft.com/library/140d0b43-9455-4907-9827-16564a904268)

**Para instalar o modo de tabela do Analysis Services:**

1. No Assistente de instalação do SQL Server, clique em **instalação** no painel esquerdo e, em seguida, clique em **instalação autónoma do novo SQL server ou adicionar funcionalidades a uma instalação existente**.
   
   * Se vir o **Procurar pasta**, navegue até à c:\SQLServer_13.0_full, c:\SQLServer_12.0_full ou c:\SQLServer_11.0_full e, em seguida, clique em **Ok**.
2. Clique em **seguinte** na página atualizações de produto.
3. No **tipo de instalação** página, selecione **efetuar uma nova instalação do SQL Server** e clique em **seguinte**.
4. No **função de configuração** página, clique em **instalação de funcionalidades do SQL Server**.
5. No **a seleção de funcionalidades** página, clique em **Analysis Services**.
6. No **configuração de instância** página, introduza um nome descritivo, tal como **tabela** para **com o nome de instância** e **Id de instância** caixas de texto.
7. No **Analysis Services Configuration** página, selecione **modo de tabela**. Adicione o utilizador atual à lista de permissões administrativas.
8. Conclua e fechar o Assistente de instalação do SQL Server.

## <a name="analysis-services-configuration"></a>Configuração do Analysis Services
### <a name="remote-access-to-analysis-services-server"></a>Acesso remoto ao servidor do Analysis Services
Servidor do Analysis Services só suporta a autenticação do windows. Para aceder remotamente o Analysis Services a partir de aplicações de cliente, como o SQL Server Management Studio ou o SQL Server Data Tools, a máquina virtual tem de ser associados ao seu domínio local, utilizando a rede Virtual do Azure. Para obter mais informações, consulte [Azure Virtual Network](../../../virtual-network/virtual-networks-overview.md).

A **instância predefinida** do Analysis Services escuta na porta TCP **2383**. Abra a porta na firewall de máquinas virtuais. Um cluster com o nome a instância do Analysis Services também escuta na porta **2383**.

Para um **instância nomeada** do Analysis Services, o serviço SQL Server Browser é necessário para gerir o acesso de porta. A configuração predefinida do SQL Server Browser é a porta **2382**.

Na firewall de máquinas virtuais, abra a porta **2382** e criar um com o nome de porta de instância de Analysis Services estático.

1. Para verificar as portas que já estão em utilização no VM e que processo está a utilizar as portas, execute o seguinte comando com privilégios administrativos:
   
        netstat /ao
2. Utilize o SQL Server Management Studio para criar um estático Analysis Services com o nome porta instância atualizando 'Porta' valor existente na tabela AS propriedades gerais de instância. Para obter mais informações, consulte o "utilizar uma porta fixa para uma predefinição ou instância nomeada" em [configurar a Firewall do Windows para permitir acesso de serviços de análise](https://msdn.microsoft.com/library/ms174937.aspx#bkmk_fixed).
3. Reinicie a instância de tabela do serviço do Analysis Services.

Para obter mais informações consulte, a **pontos finais de Máquina Virtual e portas de Firewall** secção neste documento.

## <a name="virtual-machine-endpoints-and-firewall-ports"></a>Pontos finais de máquina virtual e portas de Firewall
Esta secção resume os pontos finais de Máquina Virtual do Microsoft Azure para criar e portas para abrir nas firewalls da máquina virtual. A tabela seguinte resume a **TCP** portas para criar pontos finais para e as portas para abrir na firewall de máquinas virtuais.

* Se estiver a utilizar uma única VM e os seguintes dois itens forem verdadeiros, não é necessário criar pontos finais VM e não terá de abrir as portas na firewall na VM.
  
  * Não ligar remotamente para as funcionalidades do SQL Server na VM. Estabelecer uma ligação de ambiente de trabalho remota para a VM e aceder as funcionalidades do SQL Server localmente na VM não é considerado uma ligação remota para as funcionalidades do SQL Server.
  * Não associar a VM a um domínio no local através da rede Virtual do Azure ou outra solução de túnel VPN.
* Se a máquina virtual não está associada a um domínio, mas pretende remotamente ligar às funcionalidades do SQL Server numa VM:
  
  * Abra as portas na firewall na VM.
  * Cria pontos finais de máquina virtual para as portas apontou (*).
* Se a máquina virtual está associada a um domínio utilizando um túnel VPN, tais como a rede Virtual do Azure, em seguida, os pontos finais não são necessários. No entanto, abra as portas na firewall na VM.
  
  | Porta | Tipo | Descrição |
  | --- | --- | --- |
  | **80** |TCP |Servidor de relatórios de acesso remoto (*). |
  | **1433** |TCP |SQL Server Management Studio (*). |
  | **1434** |UDP |SQL Server Browser. Isto é necessário quando a VM associado a um domínio. |
  | **2382** |TCP |SQL Server Browser. |
  | **2383** |TCP |Instância do SQL Server Analysis Services predefinida e as instâncias nomeadas em cluster. |
  | **Definido pelo utilizador** |TCP |Criar um estático Analysis Services com o nome de porta de instância para um número de porta que escolher e, em seguida, desbloquear o número da porta na firewall. |

Para obter mais informações sobre a criação de pontos finais, consulte o seguinte:

* Criar pontos finais:[como configurar pontos finais para uma Máquina Virtual](../classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).
* SQL Server: Consulte a secção "Configuração concluir os passos para ligar à máquina virtual Using SQL Server Management Studio" [aprovisionamento de uma Máquina Virtual do SQL Server no Azure](../sql/virtual-machines-windows-portal-sql-server-provision.md).

O diagrama seguinte ilustra as portas para abrir na firewall VM para permitir o acesso remoto para funcionalidades e componentes na VM.

![portas abertas para aplicações de bi em VMs do Azure](./media/virtual-machines-windows-classic-ps-sql-bi/IC654385.gif)

## <a name="resources"></a>Recursos
* Rever a política de suporte para o software de servidor de Microsoft utilizada no ambiente de Máquina Virtual do Azure. O tópico seguinte resume suporte para funcionalidades, tais como o BitLocker, de Clustering de ativação pós-falha e balanceamento de carga na rede. [O suporte do software de servidor da Microsoft para o Azure Virtual Machines](http://support.microsoft.com/kb/2721672).
* [SQL Server em Descrição geral de máquinas virtuais do Azure](../sql/virtual-machines-windows-sql-server-iaas-overview.md)
* [Máquinas Virtuais](https://azure.microsoft.com/documentation/services/virtual-machines/)
* [Aprovisionamento de uma Máquina Virtual do SQL Server no Azure](../sql/virtual-machines-windows-portal-sql-server-provision.md)
* [Como anexar um disco de dados para uma Máquina Virtual](../classic/attach-disk.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)
* [Migrar uma base de dados para o SQL Server numa VM do Azure](../sql/virtual-machines-windows-migrate-sql.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fsqlclassic%2ftoc.json)
* [Determinar o modo de servidor de uma instância do Analysis Services](https://msdn.microsoft.com/library/gg471594.aspx)
* [Modelação multidimensional (Adventure Works Tutorial)](https://technet.microsoft.com/library/ms170208.aspx)
* [Centro de documentação do Azure](https://azure.microsoft.com/documentation/)
* [Através do Power BI num ambiente híbrido](https://msdn.microsoft.com/library/dn798994.aspx)

> [!NOTE]
> [Submeter comentários e informações de contacto através da ligação do Microsoft SQL Server](https://connect.microsoft.com/SQLServer/Feedback)

### <a name="community-content"></a>Conteúdo da Comunidade
* [Gestão de base de dados SQL do Azure com o PowerShell](http://blogs.msdn.com/b/windowsazure/archive/2013/02/07/windows-azure-sql-database-management-with-powershell.aspx)


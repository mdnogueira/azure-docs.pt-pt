---
title: Configurar o grupo de disponibilidade Always On numa VM do Azure utilizando o PowerShell | Microsoft Docs
description: "Este tutorial utiliza recursos que foram criados com o modelo de implementação clássica. Utilizar o PowerShell para criar um grupo de disponibilidade Always On no Azure."
services: virtual-machines-windows
documentationcenter: na
author: MikeRayMSFT
manager: jhubbard
editor: 
tags: azure-service-management
ms.assetid: a4e2f175-fe56-4218-86c7-a43fb916cc64
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/17/2017
ms.author: mikeray
ms.openlocfilehash: b99cf767fb931d3f7fe14fcbe7990126244613ed
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="configure-the-always-on-availability-group-on-an-azure-vm-with-powershell"></a>Configurar o grupo de disponibilidade Always On numa VM do Azure com o PowerShell
> [!div class="op_single_selector"]
> * [Clássico: IU](../classic/portal-sql-alwayson-availability-groups.md)
> * [Clássico: PowerShell](../classic/ps-sql-alwayson-availability-groups.md)
<br/>

Antes de começar, considere o que pode concluir esta tarefa no modelo de Gestor de recursos do Azure. Recomendamos que o modelo de Gestor de recursos do Azure para novas implementações. Consulte [SQL Server Always On nos grupos de disponibilidade em máquinas virtuais do Azure](../sql/virtual-machines-windows-portal-sql-availability-group-overview.md).

> [!IMPORTANT]
> Recomendamos que as implementações mais novas utilizem o modelo do Resource Manager. O Azure tem dois modelos de implementação diferentes para criar e trabalhar com recursos: [Resource Manager e clássico](../../../azure-resource-manager/resource-manager-deployment-model.md). Este artigo cobre a utilização do modelo de implementação clássica.

Máquinas virtuais do Azure (VMs) pode ajudar os administradores de base de dados para reduzir o custo de um sistema do SQL Server de elevada disponibilidade. Este tutorial mostra como implementar um grupo de disponibilidade com o SQL Server Always On ponto-a-ponto dentro de um ambiente do Azure. No final do tutorial, a solução SQL Server Always On no Azure serão consistem os seguintes elementos:

* Uma rede virtual que contém várias sub-redes, incluindo um front-end e uma sub-rede de back-end.
* Um controlador de domínio com um domínio do Active Directory.
* Duas VMs de SQL Server que são implementadas para a sub-rede de back-end e associado ao domínio do Active Directory.
* Cluster de ativação pós-falha de Windows três nós com o modelo de quórum maioria de nós.
* Um grupo de disponibilidade com duas réplicas com consolidação síncrona de uma base de dados de disponibilidade.

Este cenário é uma boa opção para a sua simplicidade no Azure, não para a respetiva eficácia de custos ou outros fatores. Por exemplo, pode minimizar o número de VMs para um grupo de disponibilidade da réplica de dois guardar as horas de computação no Azure utilizando o controlador de domínio como o testemunho de partilha de ficheiros de quórum num cluster de ativação pós-falha de dois nós. Este método reduz o número de VMS por um na configuração acima.

Este tutorial destina-se para mostrar-lhe os passos necessários para configurar a solução descrita acima, sem elaborating nos detalhes de cada passo. Por conseguinte, em vez de fornecer os passos de configuração de GUI, utiliza o orienta rapidamente por cada passo do scripting do PowerShell. Este tutorial assume o seguinte:

* Já tem uma conta do Azure com a subscrição da máquina virtual.
* Instalou o [cmdlets Azure PowerShell](/powershell/azure/overview).
* Já existe uma compreensão sólida Always On nos grupos de disponibilidade para soluções no local. Para obter mais informações, consulte [Always On nos grupos de disponibilidade (SQL Server)](https://msdn.microsoft.com/library/hh510230.aspx).

## <a name="connect-to-your-azure-subscription-and-create-the-virtual-network"></a>Ligar a sua subscrição do Azure e criar a rede virtual
1. Numa janela do PowerShell no computador local, importe o módulo do Azure, transfira o ficheiro de definições de publicação para a máquina e ligar a sessão do PowerShell para a sua subscrição do Azure ao importar as definições de publicação transferidas.

        Import-Module "C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\Azure\Azure.psd1"
        Get-AzurePublishSettingsFile
        Import-AzurePublishSettingsFile <publishsettingsfilepath>

    O **Get-AzurePublishSettingsFile** comandos automaticamente gera um certificado de gestão com o Azure e transfere-la ao seu computador. Um browser é aberto automaticamente e é-lhe pedido que introduza as credenciais da conta Microsoft para a sua subscrição do Azure. O transferido **. publishsettings** ficheiro contém todas as informações que precisa de gerir a sua subscrição do Azure. Depois de guardar este ficheiro para um diretório local, importe-o utilizando o **importação AzurePublishSettingsFile** comando.

   > [!NOTE]
   > O ficheiro. publishsettings contém as suas credenciais (não codificadas) que são utilizadas para administrar os seus serviços e subscrições do Azure. É a prática de segurança para este ficheiro armazenar temporariamente fora dos diretórios de origem (por exemplo, na pasta Libraries\Documents) e, em seguida, elimine-a após a importação foi concluída. Um utilizador mal intencionado que obtém acesso ao ficheiro. publishsettings pode editar, criar e eliminar os serviços do Azure.

2. Defina uma série de variáveis que irá utilizar para criar a sua infraestrutura de TI de nuvem.

        $location = "West US"
        $affinityGroupName = "ContosoAG"
        $affinityGroupDescription = "Contoso SQL HADR Affinity Group"
        $affinityGroupLabel = "IaaS BI Affinity Group"
        $networkConfigPath = "C:\scripts\Network.netcfg"
        $virtualNetworkName = "ContosoNET"
        $storageAccountName = "<uniquestorageaccountname>"
        $storageAccountLabel = "Contoso SQL HADR Storage Account"
        $storageAccountContainer = "https://" + $storageAccountName + ".blob.core.windows.net/vhds/"
        $winImageName = (Get-AzureVMImage | where {$_.Label -like "Windows Server 2008 R2 SP1*"} | sort PublishedDate -Descending)[0].ImageName
        $sqlImageName = (Get-AzureVMImage | where {$_.Label -like "SQL Server 2012 SP1 Enterprise*"} | sort PublishedDate -Descending)[0].ImageName
        $dcServerName = "ContosoDC"
        $dcServiceName = "<uniqueservicename>"
        $availabilitySetName = "SQLHADR"
        $vmAdminUser = "AzureAdmin"
        $vmAdminPassword = "Contoso!000"
        $workingDir = "c:\scripts\"

    Preste atenção aos seguintes para se certificar de que os seus comandos terá êxito mais tarde:

   * Variáveis **$storageAccountName** e **$dcServiceName** tem de ser exclusivo porque são utilizados para identificar o seu armazenamento conta e da nuvem servidor de nuvem, respetivamente, na Internet.
   * Os nomes que especificar para variáveis **$affinityGroupName** e **$virtualNetworkName** estão configuradas no documento de configuração de rede virtual que irá utilizar mais tarde.
   * **$sqlImageName** Especifica o nome atualizado da imagem VM que contém o SQL Server 2012 Service Pack 1 Enterprise Edition.
   * Para uma simplicidade **Contoso! 000** é a mesma palavra-passe é utilizado em todo o tutorial completo.

3. Crie um grupo de afinidade.

        New-AzureAffinityGroup `
            -Name $affinityGroupName `
            -Location $location `
            -Description $affinityGroupDescription `
            -Label $affinityGroupLabel

4. Crie uma rede virtual através da importação de um ficheiro de configuração.

        Set-AzureVNetConfig `
            -ConfigurationPath $networkConfigPath

    O ficheiro de configuração contém o seguinte documento XML. No brief, especifica uma rede virtual denominada **ContosoNET** no grupo de afinidade denominado **ContosoAG**. Tem o espaço de endereços **10.10.0.0/16** e tem duas sub-redes, **10.10.1.0/24** e **10.10.2.0/24**, que se a sub-rede do front- e a sub-rede de back-respetivamente. A sub-rede do front-é onde pode colocar aplicações de cliente como o Microsoft SharePoint. A sub-rede de back-é onde irá colocar as VMs de SQL Server. Se alterar o **$affinityGroupName** e **$virtualNetworkName** variáveis anteriormente, também tem de alterar os nomes correspondentes abaixo.

        <NetworkConfiguration xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
          <VirtualNetworkConfiguration>
            <Dns />
            <VirtualNetworkSites>
              <VirtualNetworkSite name="ContosoNET" AffinityGroup="ContosoAG">
                <AddressSpace>
                  <AddressPrefix>10.10.0.0/16</AddressPrefix>
                </AddressSpace>
                <Subnets>
                  <Subnet name="Front">
                    <AddressPrefix>10.10.1.0/24</AddressPrefix>
                  </Subnet>
                  <Subnet name="Back">
                    <AddressPrefix>10.10.2.0/24</AddressPrefix>
                  </Subnet>
                </Subnets>
              </VirtualNetworkSite>
            </VirtualNetworkSites>
          </VirtualNetworkConfiguration>
        </NetworkConfiguration>

5. Crie uma conta de armazenamento associado ao grupo de afinidade que criou e defini-lo como a conta de armazenamento atual na sua subscrição.

        New-AzureStorageAccount `
            -StorageAccountName $storageAccountName `
            -Label $storageAccountLabel `
            -AffinityGroup $affinityGroupName
        Set-AzureSubscription `
            -SubscriptionName (Get-AzureSubscription).SubscriptionName `
            -CurrentStorageAccount $storageAccountName

6. Crie o servidor de controlador de domínio no novo conjunto de disponibilidade e o serviço de nuvem.

        New-AzureVMConfig `
            -Name $dcServerName `
            -InstanceSize Medium `
            -ImageName $winImageName `
            -MediaLocation "$storageAccountContainer$dcServerName.vhd" `
            -DiskLabel "OS" |
            Add-AzureProvisioningConfig `
                -Windows `
                -DisableAutomaticUpdates `
                -AdminUserName $vmAdminUser `
                -Password $vmAdminPassword |
                New-AzureVM `
                    -ServiceName $dcServiceName `
                    –AffinityGroup $affinityGroupName `
                    -VNetName $virtualNetworkName

    Estes comandos piped efetue os seguintes procedimentos:

   * **Novo AzureVMConfig** cria uma configuração de VM.
   * **AzureProvisioningConfig adicionar** fornece os parâmetros de configuração de um servidor autónomo do Windows.
   * **AzureDataDisk adicionar** adiciona o disco de dados que irá utilizar para armazenar dados do Active Directory, com a opção de colocação em cache definida como None.
   * **Novo-AzureVM** cria um novo serviço de nuvem e cria a nova VM do Azure no novo serviço em nuvem.

7. Aguarde que a nova VM completamente estar aprovisionada e transfira o ficheiro de ambiente de trabalho remoto para o diretório de trabalho. Porque a nova VM do Azure demora muito tempo a aprovisionar, o `while` ciclo continua a consultar a nova VM até estar pronto a utilizar.

        $VMStatus = Get-AzureVM -ServiceName $dcServiceName -Name $dcServerName

        While ($VMStatus.InstanceStatus -ne "ReadyRole")
        {
            write-host "Waiting for " $VMStatus.Name "... Current Status = " $VMStatus.InstanceStatus
            Start-Sleep -Seconds 15
            $VMStatus = Get-AzureVM -ServiceName $dcServiceName -Name $dcServerName
        }

        Get-AzureRemoteDesktopFile `
            -ServiceName $dcServiceName `
            -Name $dcServerName `
            -LocalPath "$workingDir$dcServerName.rdp"

O servidor de controlador de domínio com êxito está agora aprovisionado. Em seguida, irá configurar o domínio do Active Directory neste servidor de controlador de domínio. Deixe a janela do PowerShell aberta no computador local. Irá utilizá-lo novamente mais tarde para criar o duas VMs de SQL Server.

## <a name="configure-the-domain-controller"></a>Configurar o controlador de domínio
1. Liga ao servidor de controlador de domínio, abrindo o ficheiro de ambiente de trabalho remoto. Utilize o nome de utilizador AzureAdmin e a palavra-passe do administrador do virtual **Contoso! 000**, que especificou quando criou a VM nova.
2. Abra uma janela do PowerShell no modo de administrador.
3. Execute o seguinte **DCPROMO. EXE** comando para configurar o **corp.contoso.com** domínio, com os diretórios de dados na unidade M.

        dcpromo.exe `
            /unattend `
            /ReplicaOrNewDomain:Domain `
            /NewDomain:Forest `
            /NewDomainDNSName:corp.contoso.com `
            /ForestLevel:4 `
            /DomainNetbiosName:CORP `
            /DomainLevel:4 `
            /InstallDNS:Yes `
            /ConfirmGc:Yes `
            /CreateDNSDelegation:No `
            /DatabasePath:"C:\Windows\NTDS" `
            /LogPath:"C:\Windows\NTDS" `
            /SYSVOLPath:"C:\Windows\SYSVOL" `
            /SafeModeAdminPassword:"Contoso!000"

    Depois de concluir o comando, a VM é reiniciado automaticamente.

4. Liga ao servidor de controlador de domínio novamente, abrindo o ficheiro de ambiente de trabalho remoto. Neste momento, inicie sessão como **CORP\Administrator**.
5. Abra uma janela do PowerShell no modo de administrador e importar o módulo PowerShell do Active Directory, utilizando o seguinte comando:

        Import-Module ActiveDirectory

6. Execute os seguintes comandos para adicionar três utilizadores ao domínio.

        $pwd = ConvertTo-SecureString "Contoso!000" -AsPlainText -Force
        New-ADUser `
            -Name 'Install' `
            -AccountPassword  $pwd `
            -PasswordNeverExpires $true `
            -ChangePasswordAtLogon $false `
            -Enabled $true
        New-ADUser `
            -Name 'SQLSvc1' `
            -AccountPassword  $pwd `
            -PasswordNeverExpires $true `
            -ChangePasswordAtLogon $false `
            -Enabled $true
        New-ADUser `
            -Name 'SQLSvc2' `
            -AccountPassword  $pwd `
            -PasswordNeverExpires $true `
            -ChangePasswordAtLogon $false `
            -Enabled $true

    **CORP\Install** é utilizado para configurar tudo relacionados com instâncias de serviço do SQL Server, o cluster de ativação pós-falha e o grupo de disponibilidade. **CORP\SQLSvc1** e **CORP\SQLSvc2** são utilizados como contas de serviço do SQL Server para as duas VMs de SQL Server.
7. Em seguida, execute os seguintes comandos para dar **CORP\Install** as permissões para criar objetos de computador no domínio.

        Cd ad:
        $sid = new-object System.Security.Principal.SecurityIdentifier (Get-ADUser "Install").SID
        $guid = new-object Guid bf967a86-0de6-11d0-a285-00aa003049e2
        $ace1 = new-object System.DirectoryServices.ActiveDirectoryAccessRule $sid,"CreateChild","Allow",$guid,"All"
        $corp = Get-ADObject -Identity "DC=corp,DC=contoso,DC=com"
        $acl = Get-Acl $corp
        $acl.AddAccessRule($ace1)
        Set-Acl -Path "DC=corp,DC=contoso,DC=com" -AclObject $acl

    O GUID especificado acima é o GUID para o tipo de objeto de computador. O **CORP\Install** conta as necessidades de **ler todas as propriedades** e **criar objetos de computador** permissão para criar os objetos Active Directory direta para o cluster de ativação pós-falha. O **ler todas as propriedades** permissão já é dado a CORP\Install por predefinição, por isso não terá de conceder explicitamente. Para obter mais informações sobre as permissões que são necessárias para criar o cluster de ativação pós-falha, consulte [guia de passo a passo do Cluster de ativação pós-falha: Configurar contas no Active Directory](https://technet.microsoft.com/library/cc731002%28v=WS.10%29.aspx).

    Agora que concluiu a configuração do Active Directory e os objetos de utilizador, irá criar duas VMs de SQL Server e associe-las a este domínio.

## <a name="create-the-sql-server-vms"></a>Criar as VMs de SQL Server
1. Continue a utilizar a janela do PowerShell que é aberta no computador local. Defina as seguintes variáveis adicionais:

        $domainName= "corp"
        $FQDN = "corp.contoso.com"
        $subnetName = "Back"
        $sqlServiceName = "<uniqueservicename>"
        $quorumServerName = "ContosoQuorum"
        $sql1ServerName = "ContosoSQL1"
        $sql2ServerName = "ContosoSQL2"
        $availabilitySetName = "SQLHADR"
        $dataDiskSize = 100
        $dnsSettings = New-AzureDns -Name "ContosoBackDNS" -IPAddress "10.10.0.4"

    O endereço IP **10.10.0.4** normalmente é atribuído à VM do primeiro que criar no **10.10.0.0/16** sub-rede da rede virtual do Azure. Deve verificar que este é o endereço do seu servidor de controlador de domínio através da execução **IPCONFIG**.
2. Execute os seguintes comandos para criar a primeira VM no cluster de ativação pós-falha, com o nome de direcionado **ContosoQuorum**:

        New-AzureVMConfig `
            -Name $quorumServerName `
            -InstanceSize Medium `
            -ImageName $winImageName `
            -MediaLocation "$storageAccountContainer$quorumServerName.vhd" `
            -AvailabilitySetName $availabilitySetName `
            -DiskLabel "OS" |
            Add-AzureProvisioningConfig `
                -WindowsDomain `
                -AdminUserName $vmAdminUser `
                -Password $vmAdminPassword `
                -DisableAutomaticUpdates `
                -Domain $domainName `
                -JoinDomain $FQDN `
                -DomainUserName $vmAdminUser `
                -DomainPassword $vmAdminPassword |
                Set-AzureSubnet `
                    -SubnetNames $subnetName |
                    New-AzureVM `
                        -ServiceName $sqlServiceName `
                        –AffinityGroup $affinityGroupName `
                        -VNetName $virtualNetworkName `
                        -DnsSettings $dnsSettings

    Tenha em atenção o seguinte sobre o comando acima:

   * **Novo AzureVMConfig** cria uma configuração de VM com o nome do conjunto de disponibilidade pretendidos. As VMs subsequentes serão criadas com o mesmo nome de conjunto de disponibilidade, para que estes estão associados ao mesmo conjunto de disponibilidade.
   * **AzureProvisioningConfig adicionar** associa a VM para o domínio do Active Directory que criou.
   * **Conjunto AzureSubnet** coloca a VM na sub-rede anterior.
   * **Novo-AzureVM** cria um novo serviço de nuvem e cria a nova VM do Azure no novo serviço em nuvem. O **DnsSettings** parâmetro especifica se o servidor DNS para os servidores no novo serviço de nuvem tem o endereço IP **10.10.0.4**. Este é o endereço IP do servidor de controlador de domínio. Este parâmetro é necessário para ativar as VMs novas no serviço em nuvem associar ao domínio do Active Directory com êxito. Sem este parâmetro, tem de configurar manualmente as definições de IPv4 na VM para utilizar o servidor de controlador de domínio como o servidor DNS primário depois da VM está aprovisionada e, em seguida, associar a VM ao domínio do Active Directory.
3. Execute os seguintes comandos para criar as VMs de SQL Server, com o nome de direcionado **ContosoSQL1** e **ContosoSQL2**.

        # Create ContosoSQL1...
        New-AzureVMConfig `
            -Name $sql1ServerName `
            -InstanceSize Large `
            -ImageName $sqlImageName `
            -MediaLocation "$storageAccountContainer$sql1ServerName.vhd" `
            -AvailabilitySetName $availabilitySetName `
            -HostCaching "ReadOnly" `
            -DiskLabel "OS" |
            Add-AzureProvisioningConfig `
                -WindowsDomain `
                -AdminUserName $vmAdminUser `
                -Password $vmAdminPassword `
                -DisableAutomaticUpdates `
                -Domain $domainName `
                -JoinDomain $FQDN `
                -DomainUserName $vmAdminUser `
                -DomainPassword $vmAdminPassword |
                Set-AzureSubnet `
                    -SubnetNames $subnetName |
                    Add-AzureEndpoint `
                        -Name "SQL" `
                        -Protocol "tcp" `
                        -PublicPort 1 `
                        -LocalPort 1433 |
                        New-AzureVM `
                            -ServiceName $sqlServiceName

        # Create ContosoSQL2...
        New-AzureVMConfig `
            -Name $sql2ServerName `
            -InstanceSize Large `
            -ImageName $sqlImageName `
            -MediaLocation "$storageAccountContainer$sql2ServerName.vhd" `
            -AvailabilitySetName $availabilitySetName `
            -HostCaching "ReadOnly" `
            -DiskLabel "OS" |
            Add-AzureProvisioningConfig `
                -WindowsDomain `
                -AdminUserName $vmAdminUser `
                -Password $vmAdminPassword `
                -DisableAutomaticUpdates `
                -Domain $domainName `
                -JoinDomain $FQDN `
                -DomainUserName $vmAdminUser `
                -DomainPassword $vmAdminPassword |
                Set-AzureSubnet `
                    -SubnetNames $subnetName |
                    Add-AzureEndpoint `
                        -Name "SQL" `
                        -Protocol "tcp" `
                        -PublicPort 2 `
                        -LocalPort 1433 |
                        New-AzureVM `
                            -ServiceName $sqlServiceName

    Tenha em atenção o seguinte sobre os comandos acima:

   * **Novo AzureVMConfig** utiliza o mesmo nome de conjunto de disponibilidade do servidor de controlador de domínio e utiliza a imagem do SQL Server 2012 Service Pack 1 Enterprise Edition na galeria da máquina virtual. Também define o disco do sistema operativo para leitura cache apenas (não escrita a colocação em cache). Recomendamos que migre os ficheiros de base de dados para um disco de dados separada que anexa a VM e configurá-la sem leitura nem escrita a colocação em cache. No entanto, a coisa melhor seguinte consiste em remover a escrita a colocação em cache no disco do sistema operativo porque não é possível remover a cache de leitura no disco do sistema operativo.
   * **AzureProvisioningConfig adicionar** associa a VM para o domínio do Active Directory que criou.
   * **Conjunto AzureSubnet** coloca a VM na sub-rede anterior.
   * **AzureEndpoint adicionar** Adiciona pontos finais de acesso para que as aplicações de cliente possam aceder estas instâncias de serviços do SQL Server na Internet. Portas diferentes recebem ContosoSQL1 e ContosoSQL2.
   * **Novo-AzureVM** cria a nova VM do SQL Server no mesmo serviço em nuvem como ContosoQuorum. Tem de colocar as VMs no mesmo serviço em nuvem se pretende que estar no mesmo conjunto de disponibilidade.
4. Aguarde para cada VM completamente estar aprovisionada e para cada VM transferir os ficheiros de ambiente de trabalho remoto para o diretório de trabalho. O `for` ciclo percorre as três VMs novas e executa os comandos dentro de chavetas de nível superior para cada um deles.

        Foreach ($VM in $VMs = Get-AzureVM -ServiceName $sqlServiceName)
        {
            write-host "Waiting for " $VM.Name "..."

            # Loop until the VM status is "ReadyRole"
            While ($VM.InstanceStatus -ne "ReadyRole")
            {
                write-host "  Current Status = " $VM.InstanceStatus
                Start-Sleep -Seconds 15
                $VM = Get-AzureVM -ServiceName $VM.ServiceName -Name $VM.InstanceName
            }

            write-host "  Current Status = " $VM.InstanceStatus

            # Download remote desktop file
            Get-AzureRemoteDesktopFile -ServiceName $VM.ServiceName -Name $VM.InstanceName -LocalPath "$workingDir$($VM.InstanceName).rdp"
        }

    As VMs de SQL Server está agora aprovisionados e em execução, mas estiver instalado com o SQL Server com opções predefinidas.

## <a name="initialize-the-failover-cluster-vms"></a>Inicializar o cluster de ativação pós-falha de VMs
Nesta secção, terá de modificar os três servidores que irá utilizar no cluster de ativação pós-falha e a instalação do SQL Server. Especificamente:

* Todos os servidores: tem de instalar o **Clustering de ativação pós-falha** funcionalidade.
* Todos os servidores: tem de adicionar **CORP\Install** que a máquina **administrador**.
* ContosoSQL1 e ContosoSQL2 apenas: tem de adicionar **CORP\Install** como um **sysadmin** função na base de dados predefinido.
* ContosoSQL1 e ContosoSQL2 apenas: tem de adicionar **NT AUTHORITY\System** como um início de sessão com as seguintes permissões:

  * Alterar qualquer grupo de disponibilidade
  * Estabelecer a ligação SQL
  * Ver estado do servidor
* ContosoSQL1 e ContosoSQL2 apenas: O **TCP** protocolo já estiver ativado na VM do servidor do SQL Server. No entanto, ainda tem de abrir a firewall para acesso remoto do SQL Server.

Agora, está pronto para iniciar. Começando com **ContosoQuorum**, siga os passos abaixo:

1. Ligar ao **ContosoQuorum** , iniciando os ficheiros de ambiente de trabalho remotos. Utilize o nome de utilizador do administrador do virtual **AzureAdmin** e palavra-passe **Contoso! 000**, que especificou quando criou as VMs.
2. Certifique-se de que os computadores foram associados com êxito ao **corp.contoso.com**.
3. Aguarde pela instalação do SQL Server concluir as tarefas de inicialização automatizada antes de continuar a ser executado.
4. Abra uma janela do PowerShell no modo de administrador.
5. Instale a funcionalidade de Clustering de ativação pós-falha do Windows.

        Import-Module ServerManager
        Add-WindowsFeature Failover-Clustering
6. Adicionar **CORP\Install** como um administrador local.

        net localgroup administrators "CORP\Install" /Add
7. Terminar sessão ContosoQuorum. Tiver terminado com este servidor agora.

        logoff.exe

Em seguida, inicializar **ContosoSQL1** e **ContosoSQL2**. Siga os passos abaixo, são idênticos para ambas as VMs de SQL Server.

1. Liga para o duas VMs de SQL Server, iniciando os ficheiros de ambiente de trabalho remotos. Utilize o nome de utilizador do administrador do virtual **AzureAdmin** e palavra-passe **Contoso! 000**, que especificou quando criou as VMs.
2. Certifique-se de que os computadores foram associados com êxito ao **corp.contoso.com**.
3. Aguarde pela instalação do SQL Server concluir as tarefas de inicialização automatizada antes de continuar a ser executado.
4. Abra uma janela do PowerShell no modo de administrador.
5. Instale a funcionalidade de Clustering de ativação pós-falha do Windows.

        Import-Module ServerManager
        Add-WindowsFeature Failover-Clustering
6. Adicionar **CORP\Install** como um administrador local.

        net localgroup administrators "CORP\Install" /Add
7. Importe o fornecedor de PowerShell do SQL Server.

        Set-ExecutionPolicy -Execution RemoteSigned -Force
        Import-Module -Name "sqlps" -DisableNameChecking
8. Adicionar **CORP\Install** como a função sysadmin para a instância do SQL Server predefinida.

        net localgroup administrators "CORP\Install" /Add
        Invoke-SqlCmd -Query "EXEC sp_addsrvrolemember 'CORP\Install', 'sysadmin'" -ServerInstance "."
9. Adicionar **NT AUTHORITY\System** como um início de sessão com as permissões de três descrito acima.

        Invoke-SqlCmd -Query "CREATE LOGIN [NT AUTHORITY\SYSTEM] FROM WINDOWS" -ServerInstance "."
        Invoke-SqlCmd -Query "GRANT ALTER ANY AVAILABILITY GROUP TO [NT AUTHORITY\SYSTEM] AS SA" -ServerInstance "."
        Invoke-SqlCmd -Query "GRANT CONNECT SQL TO [NT AUTHORITY\SYSTEM] AS SA" -ServerInstance "."
        Invoke-SqlCmd -Query "GRANT VIEW SERVER STATE TO [NT AUTHORITY\SYSTEM] AS SA" -ServerInstance "."
10. Abra a firewall para acesso remoto do SQL Server.

         netsh advfirewall firewall add rule name='SQL Server (TCP-In)' program='C:\Program Files\Microsoft SQL Server\MSSQL11.MSSQLSERVER\MSSQL\Binn\sqlservr.exe' dir=in action=allow protocol=TCP
11. Terminar sessão ambas as VMs.

         logoff.exe

Por fim, está pronto para configurar o grupo de disponibilidade. Irá utilizar o fornecedor de PowerShell do SQL Server para efetuar todo o trabalho **ContosoSQL1**.

## <a name="configure-the-availability-group"></a>Configurar o grupo de disponibilidade
1. Ligar ao **ContosoSQL1** novamente, iniciando os ficheiros de ambiente de trabalho remotos. Em vez de iniciar sessão utilizando a conta de computador, inicie sessão com **CORP\Install**.
2. Abra uma janela do PowerShell no modo de administrador.
3. Defina as seguintes variáveis:

        $server1 = "ContosoSQL1"
        $server2 = "ContosoSQL2"
        $serverQuorum = "ContosoQuorum"
        $acct1 = "CORP\SQLSvc1"
        $acct2 = "CORP\SQLSvc2"
        $password = "Contoso!000"
        $clusterName = "Cluster1"
        $timeout = New-Object System.TimeSpan -ArgumentList 0, 0, 30
        $db = "MyDB1"
        $backupShare = "\\$server1\backup"
        $quorumShare = "\\$server1\quorum"
        $ag = "AG1"
4. Importe o fornecedor de PowerShell do SQL Server.

        Set-ExecutionPolicy RemoteSigned -Force
        Import-Module "sqlps" -DisableNameChecking
5. Altere a conta de serviço do SQL Server para ContosoSQL1 para CORP\SQLSvc1.

        $wmi1 = new-object ("Microsoft.SqlServer.Management.Smo.Wmi.ManagedComputer") $server1
        $wmi1.services | where {$_.Type -eq 'SqlServer'} | foreach{$_.SetServiceAccount($acct1,$password)}
        $svc1 = Get-Service -ComputerName $server1 -Name 'MSSQLSERVER'
        $svc1.Stop()
        $svc1.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Stopped,$timeout)
        $svc1.Start();
        $svc1.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Running,$timeout)
6. Altere a conta de serviço do SQL Server para ContosoSQL2 para CORP\SQLSvc2.

        $wmi2 = new-object ("Microsoft.SqlServer.Management.Smo.Wmi.ManagedComputer") $server2
        $wmi2.services | where {$_.Type -eq 'SqlServer'} | foreach{$_.SetServiceAccount($acct2,$password)}
        $svc2 = Get-Service -ComputerName $server2 -Name 'MSSQLSERVER'
        $svc2.Stop()
        $svc2.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Stopped,$timeout)
        $svc2.Start();
        $svc2.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Running,$timeout)
7. Transferir **CreateAzureFailoverCluster.ps1** de [criar Cluster de ativação pós-falha para grupos de disponibilidade Always na VM do Azure](http://gallery.technet.microsoft.com/scriptcenter/Create-WSFC-Cluster-for-7c207d3a) para o diretório de trabalho local. Irá utilizar este script para o ajudar a criar um cluster de ativação pós-falha funcional. Para obter informações importantes sobre como o Clustering de ativação pós-falha do Windows interage com a rede do Azure, consulte [elevada disponibilidade e recuperação após desastre para o SQL Server em Azure Virtual Machines](../sql/virtual-machines-windows-sql-high-availability-dr.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fsqlclassic%2ftoc.json).
8. Mude para o diretório de trabalho e criar o cluster de ativação pós-falha com o script transferido.

        Set-ExecutionPolicy Unrestricted -Force
        .\CreateAzureFailoverCluster.ps1 -ClusterName "$clusterName" -ClusterNode "$server1","$server2","$serverQuorum"
9. Ative Always On nos grupos de disponibilidade para instâncias do SQL Server predefinida no **ContosoSQL1** e **ContosoSQL2**.

        Enable-SqlAlwaysOn `
            -Path SQLSERVER:\SQL\$server1\Default `
            -Force
        Enable-SqlAlwaysOn `
            -Path SQLSERVER:\SQL\$server2\Default `
            -NoServiceRestart
        $svc2.Stop()
        $svc2.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Stopped,$timeout)
        $svc2.Start();
        $svc2.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Running,$timeout)
10. Criar um diretório de cópia de segurança e conceder permissões para as contas de serviço do SQL Server. Irá utilizar este diretório para preparar a base de dados de disponibilidade na réplica secundária.

         $backup = "C:\backup"
         New-Item $backup -ItemType directory
         net share backup=$backup "/grant:$acct1,FULL" "/grant:$acct2,FULL"
         icacls.exe "$backup" /grant:r ("$acct1" + ":(OI)(CI)F") ("$acct2" + ":(OI)(CI)F")
11. Criar uma base de dados **ContosoSQL1** chamado **MyDB1**, coloque uma cópia de segurança completa e uma cópia de segurança do registo e restaurá-las em **ContosoSQL2** com o **WITH NORECOVERY**  opção.

         Invoke-SqlCmd -Query "CREATE database $db"
         Backup-SqlDatabase -Database $db -BackupFile "$backupShare\db.bak" -ServerInstance $server1
         Backup-SqlDatabase -Database $db -BackupFile "$backupShare\db.log" -ServerInstance $server1 -BackupAction Log
         Restore-SqlDatabase -Database $db -BackupFile "$backupShare\db.bak" -ServerInstance $server2 -NoRecovery
         Restore-SqlDatabase -Database $db -BackupFile "$backupShare\db.log" -ServerInstance $server2 -RestoreAction Log -NoRecovery
12. Criar a disponibilidade de pontos finais do grupo de VMs de SQL Server e defina as permissões adequadas nos pontos finais.

         $endpoint =
             New-SqlHadrEndpoint MyMirroringEndpoint `
             -Port 5022 `
             -Path "SQLSERVER:\SQL\$server1\Default"
         Set-SqlHadrEndpoint `
             -InputObject $endpoint `
             -State "Started"
         $endpoint =
             New-SqlHadrEndpoint MyMirroringEndpoint `
             -Port 5022 `
             -Path "SQLSERVER:\SQL\$server2\Default"
         Set-SqlHadrEndpoint `
             -InputObject $endpoint `
             -State "Started"

         Invoke-SqlCmd -Query "CREATE LOGIN [$acct2] FROM WINDOWS" -ServerInstance $server1
         Invoke-SqlCmd -Query "GRANT CONNECT ON ENDPOINT::[MyMirroringEndpoint] TO [$acct2]" -ServerInstance $server1
         Invoke-SqlCmd -Query "CREATE LOGIN [$acct1] FROM WINDOWS" -ServerInstance $server2
         Invoke-SqlCmd -Query "GRANT CONNECT ON ENDPOINT::[MyMirroringEndpoint] TO [$acct1]" -ServerInstance $server2
13. Crie as réplicas de disponibilidade.

         $primaryReplica =
             New-SqlAvailabilityReplica `
             -Name $server1 `
             -EndpointURL "TCP://$server1.corp.contoso.com:5022" `
             -AvailabilityMode "SynchronousCommit" `
             -FailoverMode "Automatic" `
             -Version 11 `
             -AsTemplate
         $secondaryReplica =
             New-SqlAvailabilityReplica `
             -Name $server2 `
             -EndpointURL "TCP://$server2.corp.contoso.com:5022" `
             -AvailabilityMode "SynchronousCommit" `
             -FailoverMode "Automatic" `
             -Version 11 `
             -AsTemplate
14. Por fim, crie o grupo de disponibilidade e associar a réplica secundária ao grupo de disponibilidade.

         New-SqlAvailabilityGroup `
             -Name $ag `
             -Path "SQLSERVER:\SQL\$server1\Default" `
             -AvailabilityReplica @($primaryReplica,$secondaryReplica) `
             -Database $db
         Join-SqlAvailabilityGroup `
             -Path "SQLSERVER:\SQL\$server2\Default" `
             -Name $ag
         Add-SqlAvailabilityDatabase `
             -Path "SQLSERVER:\SQL\$server2\Default\AvailabilityGroups\$ag" `
             -Database $db

## <a name="next-steps"></a>Passos seguintes
Tiver já agora com êxito implementado a SQL Server Always On através da criação de um grupo de disponibilidade no Azure. Para configurar um serviço de escuta para este grupo de disponibilidade, consulte [configurar um serviço de escuta do ILB para grupos de disponibilidade Always On no Azure](../classic/ps-sql-int-listener.md).

Para outras informações sobre como utilizar o SQL Server no Azure, consulte [SQL Server em virtual machines do Azure](../sql/virtual-machines-windows-sql-server-iaas-overview.md).

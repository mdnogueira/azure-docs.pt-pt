---
title: "Utilizar o PowerShell para criar uma VM com um servidor de relatórios de modo nativo | Microsoft Docs"
description: "Este tópico descreve e orienta-o através da implementação e configuração de um servidor de relatórios de modo nativo do SQL Server Reporting Services uma Máquina Virtual no Azure. "
services: virtual-machines-windows
documentationcenter: na
author: guyinacube
manager: erikre
editor: monicar
tags: azure-service-management
ms.assetid: 553af55b-d02e-4e32-904c-682bfa20fa0f
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/11/2017
ms.author: asaxton
ms.openlocfilehash: 5e5c11251cd316e8161dbe362b300be76927ac01
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="use-powershell-to-create-an-azure-vm-with-a-native-mode-report-server"></a>Utilizar o PowerShell para Criar uma VM do Azure com um Servidor de Relatórios no Modo Nativo
> [!IMPORTANT] 
> O Azure tem dois modelos de implementação diferentes para criar e trabalhar com recursos: [Resource Manager e clássico](../../../azure-resource-manager/resource-manager-deployment-model.md). Este artigo abrange utilizando o modelo de implementação clássica. A Microsoft recomenda que as implementações mais novas utilizem o modelo Resource Manager.

Este tópico descreve e orienta-o através da implementação e configuração de um servidor de relatórios de modo nativo do SQL Server Reporting Services uma Máquina Virtual no Azure. Os passos neste documento utilizam uma combinação de passos manuais para criar a máquina virtual e um script do Windows PowerShell para configurar o Reporting Services na VM. O script de configuração inclui ao abrir uma porta de firewall para HTTP ou HTTPs.

> [!NOTE]
> Se não necessitar de **HTTPS** no servidor de relatórios, **ignore o passo 2**.
> 
> Depois de criar a VM no passo 1, aceda à secção de script de utilização para configurar o servidor de relatórios e HTTP. Depois de executar o script, estará pronto a utilizar o servidor de relatórios.

## <a name="prerequisites-and-assumptions"></a>Pré-requisitos e pressupostos
* **Subscrição do Azure**: verificar o número de núcleos disponíveis na sua subscrição do Azure. Se criar o tamanho da VM recomendado de **A3**, terá de **4** núcleos disponíveis. Se utilizar um tamanho VM de **A2**, terá de **2** núcleos disponíveis.
  
  * Para verificar o limite de núcleos da sua subscrição, no portal clássico do Azure, clique em definições no painel esquerdo e, em seguida, clique em utilização no menu superior.
  * Para aumentar a quota de núcleos, contacte [suporte do Azure](https://azure.microsoft.com/support/options/). Para informações de tamanho VM, consulte [tamanhos de Máquina Virtual do Azure](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* **Scripting do Windows PowerShell**: O tópico pressupõe que tem um conhecimento básico de trabalho do Windows PowerShell. Para obter mais informações sobre como utilizar o Windows PowerShell, consulte o seguinte:
  
  * [A partir do Windows PowerShell no Windows Server](https://technet.microsoft.com/library/hh847814.aspx)
  * [Introdução ao Windows PowerShell](https://technet.microsoft.com/library/hh857337.aspx)

## <a name="step-1-provision-an-azure-virtual-machine"></a>Passo 1: Aprovisionar uma Máquina Virtual do Azure
1. Navegue até ao portal clássico do Azure.
2. Clique em **máquinas virtuais** no painel esquerdo.
   
    ![máquinas virtuais do Microsoft azure](./media/virtual-machines-windows-classic-ps-sql-report/IC660124.gif)
3. Clique em **Novo**.
   
    ![Botão novo](./media/virtual-machines-windows-classic-ps-sql-report/IC692019.gif)
4. Clique em **da galeria**.
   
    ![nova vm a partir da Galeria](./media/virtual-machines-windows-classic-ps-sql-report/IC692020.gif)
5. Clique em **SQL Server 2014 RTM Standard – Windows Server 2012 R2** e, em seguida, clique na seta para continuar.
   
    ![seguinte](./media/virtual-machines-windows-classic-ps-sql-report/IC692021.gif)
   
    Se precisar dos dados do Reporting Services orientadas por funcionalidade de subscrições, escolha **SQL Server 2014 RTM Enterprise – Windows Server 2012 R2**. Para obter mais informações sobre edições do SQL Server e o suporte de funcionalidades, consulte [funcionalidades suportadas pelas edições do SQL Server 2012](https://msdn.microsoft.com/library/cc645993.aspx#Reporting).
6. No **configuração da Máquina Virtual** página, edite os campos seguintes:
   
   * Se existir mais do que um **data de lançamento da versão**, selecione a versão mais recente.
   * **Nome da máquina virtual**: O nome do computador também é utilizado na página de configuração seguinte como o nome de DNS do serviço de nuvem predefinido. O nome DNS tem de ser exclusivo em todo o serviço do Azure. Considere configurar a VM com um nome de computador que descreve o que a VM é utilizada para. Por exemplo ssrsnativecloud.
   * **Camada**: Standard
   * **Tamanho: A3** é o tamanho da VM recomendado para cargas de trabalho do SQL Server. Se uma VM é utilizada apenas como um servidor de relatórios, um tamanho VM de A2 é suficiente, a menos que o servidor de relatórios ocorre com uma grande carga de trabalho. Para obter informações sobre preços de VM, consulte [preços das Virtual Machines](https://azure.microsoft.com/pricing/details/virtual-machines/).
   * **Novo nome de utilizador**: o nome terá de fornecer é criado como um administrador na VM.
   * **Nova palavra-passe** e **confirmar**. Esta palavra-passe é utilizado para a nova conta de administrador e é recomendado que utilize uma palavra-passe segura.
   * Clique em **Seguinte**. ![seguinte](./media/virtual-machines-windows-classic-ps-sql-report/IC692021.gif)
7. Na página seguinte, edite os campos seguintes:
   
   * **O serviço em nuvem**: selecione **criar um novo serviço de nuvem**.
   * **Nome de DNS do serviço de nuvem**: Este é o nome DNS público do serviço de nuvem que está associado a VM. O nome predefinido é o nome que introduziu para o nome da VM. Se em passos posteriores do tópico, criar um certificado SSL fidedigno e, em seguida, o nome DNS é utilizado para o valor de "**emitido para**" do certificado.
   * **Afinidade de região/grupo/Virtual Network**: Escolha a região mais próxima aos seus utilizadores finais.
   * **Conta de armazenamento**: utilizar uma conta de armazenamento gerado automaticamente.
   * **Conjunto de disponibilidade**: nenhuma.
   * **Pontos finais** manter o **ambiente de trabalho remoto** e **PowerShell** pontos finais e, em seguida, adicionar ponto final de um HTTP ou HTTPS, dependendo do seu ambiente.
     
     * **HTTP**: portas públicas e privadas predefinidas são **80**. Tenha em atenção que se utilizar uma porta privada diferente 80, modificar **$HTTPport = 80** no script de http.
     * **HTTPS**: portas públicas e privadas predefinidas são **443**. É melhor prática de segurança alterar a porta privada e configurar a firewall e o servidor de relatórios para utilizar a porta privada. Para obter mais informações sobre os pontos finais, consulte [como cópia de segurança comunicação com uma Máquina Virtual](../classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json). Tenha em atenção que se utilizar uma porta diferente da 443, alterar o parâmetro **$HTTPsport = 443** no script de HTTPS.
   * Clique em seguinte. ![seguinte](./media/virtual-machines-windows-classic-ps-sql-report/IC692021.gif)
8. Na última página do assistente, mantenha a predefinição **instale o agente VM** selecionado. Os passos neste tópico não utilizar o agente da VM, mas se pretender manter esta VM, o agente da VM e as extensões permite-lhe melhorá-lo CM.  Para obter mais informações sobre o agente da VM, consulte [extensões – parte 1 e o agente da VM](https://azure.microsoft.com/blog/2014/04/11/vm-agent-and-extensions-part-1/). Um dos ad extensões instaladas predefinido em execução é a extensão "BGINFO" que é apresentada no ambiente de trabalho VM, as informações do sistema, tais como IP internos e espaço no disco livre.
9. Clique em Concluir. ![OK](./media/virtual-machines-windows-classic-ps-sql-report/IC660122.gif)
10. O **estado** da VM, apresenta como **inicial (aprovisionamento)** durante o processo de aprovisionamento e, em seguida, é apresentado como **executar** quando a VM está aprovisionado e pronta a utilizar.

## <a name="step-2-create-a-server-certificate"></a>Passo 2: Criar um certificado de servidor
> [!NOTE]
> Se não necessitar de HTTPS no servidor de relatórios, pode **ignore o passo 2** e aceda à secção **utilizar um script para configurar o servidor de relatórios e HTTP**. Utilizar o script HTTP para configurar rapidamente o servidor de relatórios e o servidor de relatórios estarem pronto a utilizar.

Para utilizar HTTPS numa VM, é necessário um certificado fidedigno do SSL. Dependendo do seu cenário, pode utilizar um dos seguintes dois métodos:

* Um certificado SSL válido emitido por uma autoridade de certificação (AC) e considerada fidedigna pela Microsoft. Os certificados de AC de raiz são necessários para ser distribuído via Microsoft Root Certificate Program. Para obter mais informações sobre este programa, consulte [Windows e Windows Phone 8 SSL Root Certificate Program (membro CAs)](http://social.technet.microsoft.com/wiki/contents/articles/14215.windows-and-windows-phone-8-ssl-root-certificate-program-member-cas.aspx) e [introdução para o Microsoft Root Certificate Program](http://social.technet.microsoft.com/wiki/contents/articles/3281.introduction-to-the-microsoft-root-certificate-program.aspx).
* Um certificado autoassinado. Certificados autoassinados não são recomendados para ambientes de produção.

### <a name="to-use-a-certificate-created-by-a-trusted-certificate-authority-ca"></a>Para utilizar um certificado criado por uma fidedigna autoridade de certificação (CA)
1. **Pedir um certificado de servidor para o Web site a uma autoridade de certificação**. 
   
    Pode utilizar o Assistente de certificado de servidor Web para gerar um ficheiro de pedido de certificado (Certreq.txt) que envia para uma autoridade de certificação ou para gerar um pedido de uma autoridade de certificação online. Por exemplo, Microsoft os serviços de certificados no Windows Server 2012. Consoante o nível de garantia de identificação oferecida pelo seu certificado de servidor, é vários dias vários meses da autoridade de certificação aprovar o pedido e enviar-lhe um ficheiro de certificado. 
   
    Para obter mais informações sobre a pedir certificados de servidor, consulte o seguinte: 
   
   * Utilize [Certreq](https://technet.microsoft.com/library/cc725793.aspx), [Certreq](https://technet.microsoft.com/library/cc725793.aspx).
   * Ferramentas de segurança para administrar o Windows Server 2012.
     
     [Ferramentas de segurança para administrar o Windows Server 2012](https://technet.microsoft.com/library/jj730960.aspx)
     
     > [!NOTE]
     > O **emitido para** campo do certificado SSL fidedigno deve ser o mesmo que o **nome de DNS do serviço de nuvem** que utilizou para a nova VM.

2. **Instalar o certificado de servidor no servidor Web**. Neste caso, o servidor Web é a VM que aloja o servidor de relatórios e o Web site é criado em passos posteriores quando configurou o Reporting Services. Para obter mais informações sobre como instalar o certificado de servidor no servidor Web utilizando o snap-in MMC de certificados, consulte [instalar um certificado de servidor](https://technet.microsoft.com/library/cc740068).
   
    Se pretender utilizar o script incluído com este tópico para configurar o servidor de relatórios, o valor dos certificados **Thumbprint** , é necessário como um parâmetro do script. Consulte a secção seguinte para obter detalhes sobre como obter o thumbprint do certificado.
3. Atribua o certificado de servidor para o servidor de relatórios. A atribuição é concluída na secção seguinte, quando configurar o servidor de relatórios.

### <a name="to-use-the-virtual-machines-self-signed-certificate"></a>As máquinas virtuais de utilizar um certificado autoassinado
Um certificado autoassinado foi criado na VM quando a VM foi aprovisionada. O certificado tem o mesmo nome que o nome DNS de VM. Para evitar erros de certificado, é necessário que o certificado é fidedigno na própria VM bem como por todos os utilizadores do site.

1. Para confiar na AC de raiz do certificado da VM Local, adicionar o certificado para o **autoridades de certificação de raiz fidedigna**. Segue-se um resumo dos passos necessários. Para obter passos detalhados sobre como fidedigna a AC, consulte [instalar um certificado de servidor](https://technet.microsoft.com/library/cc740068).
   
   1. A partir do portal clássico do Azure, selecione a VM e clique em ligar. Consoante a configuração do browser, poderá ser-lhe pedido para guardar um ficheiro. rdp para ligar à VM.
      
       ![ligar à máquina virtual do azure](./media/virtual-machines-windows-classic-ps-sql-report/IC650112.gif) Utilize o nome VM de utilizador, o nome de utilizador e a palavra-passe que configurou quando criou a VM. 
      
       Por exemplo, na imagem seguinte, o nome da VM é **ssrsnativecloud** e o nome de utilizador é **testuser**.
      
       ![nome de vm de inlcudes de início de sessão](./media/virtual-machines-windows-classic-ps-sql-report/IC764111.png)
   2. Execute mmc.exe. Para obter mais informações, consulte [como: ver certificados com o Snap-in MMC](https://msdn.microsoft.com/library/ms788967.aspx).
   3. Na aplicação de consola **ficheiro** menu, adicione o **certificados** snap-in, selecione **conta de computador** quando lhe for pedido e, em seguida, clique em **seguinte**.
   4. Selecione **computador Local** para gerir e, em seguida, clique em **concluir**.
   5. Clique em **Ok** e, em seguida, expanda o **certificados - pessoal** nós e, em seguida, clique em **certificados**. O certificado é denominado depois do nome DNS da VM e termina com **cloudapp.net**. O nome do certificado com o botão direito e clique em **cópia**.
   6. Expanda o **autoridades de certificação de raiz fidedigna** nós e, em seguida, contexto **certificados** e, em seguida, clique em **colar**.
   7. Para validar, faça duplo clique no nome do certificado em **autoridades de certificação de raiz fidedigna** e verifique se existirem erros e consulte o seu certificado. Se pretender utilizar o script HTTPS, incluído com este tópico para configurar o servidor de relatórios, o valor dos certificados **Thumbprint** , é necessário como um parâmetro do script. **Para obter o valor de thumbprint**, concluir os seguintes procedimentos. Também é um exemplo do PowerShell para obter o thumbprint na secção [utilizar um script para configurar o servidor de relatórios e o HTTPS](#use-script-to-configure-the-report-server-and-HTTPS).
      
      1. Faça duplo clique o nome do certificado, por exemplo ssrsnativecloud.cloudapp.net.
      2. Clique em de **detalhes** separador.
      3. Clique em **Thumbprint**. O valor do thumbprint é apresentado no campo de detalhes, por exemplo a6 08 3C df f9 0b f7 e3 7c 25 ed a4 ed 7e ac 91 9c c de 2 fb 2f.
      4. Copie o thumbprint e guardar o valor para utilizar mais tarde ou edite o script agora.
      5. (*) Antes de executar o script, remova os espaços entre os pares de valores. Por exemplo o thumbprint anotou antes de agora seria a6083cdff90bf7e37c25eda4ed7eac919c2cfb2f.
      6. Atribua o certificado de servidor para o servidor de relatórios. A atribuição é concluída na secção seguinte, quando configurar o servidor de relatórios.

Se estiver a utilizar um certificado SSL autoassinado, o nome do certificado já corresponde ao nome do anfitrião da VM. Por conseguinte, o DNS da máquina já está registado global e podem ser acedido a partir de qualquer cliente.

## <a name="step-3-configure-the-report-server"></a>Passo 3: Configurar o servidor de relatórios
Esta secção explica como configurar a VM como um servidor de relatórios de modo nativo do Reporting Services. Pode utilizar um dos seguintes métodos para configurar o servidor de relatórios:

* Utilize o script para configurar o servidor de relatórios
* Utilize o Gestor de configuração para configurar o servidor de relatórios.

Para obter mais passos, consulte a secção [ligar à máquina Virtual e iniciar o Gestor de configuração do Reporting Services](virtual-machines-windows-classic-ps-sql-bi.md#connect-to-the-virtual-machine-and-start-the-reporting-services-configuration-manager).

**Nota de autenticação:** autenticação do Windows é o método de autenticação recomendada e a autenticação do Reporting Services predefinido. Apenas os utilizadores que estão configurados na VM podem aceder a serviços de relatórios e atribuir funções de Reporting Services.

### <a name="use-script-to-configure-the-report-server-and-http"></a>Utilizar um script para configurar o servidor de relatórios e HTTP
Para utilizar o script do Windows PowerShell para configurar o servidor de relatórios, execute os seguintes passos. A configuração inclui HTTP, HTTPS não:

1. A partir do portal clássico do Azure, selecione a VM e clique em ligar. Consoante a configuração do browser, poderá ser-lhe pedido para guardar um ficheiro. rdp para ligar à VM.
   
    ![ligar à máquina virtual do azure](./media/virtual-machines-windows-classic-ps-sql-report/IC650112.gif) Utilize o nome VM de utilizador, o nome de utilizador e a palavra-passe que configurou quando criou a VM. 
   
    Por exemplo, na imagem seguinte, o nome da VM é **ssrsnativecloud** e o nome de utilizador é **testuser**.
   
    ![nome de vm de inlcudes de início de sessão](./media/virtual-machines-windows-classic-ps-sql-report/IC764111.png)
2. Na VM, abra **ISE do Windows PowerShell** com privilégios administrativos. ISE do PowerShell está instalado por predefinição no Windows server 2012. É recomendado que utilizar o ISE em vez de uma janela do Windows PowerShell padrão, para que possam cole o script no ISE do, modificar o script e, em seguida, execute o script.
3. No ISE do Windows PowerShell, clique em de **vista** menu e, em seguida, clique em **Mostrar painel de Script**.
4. Copie o seguinte script e cole o script no painel de script de ISE do Windows PowerShell.
   
        ## This script configures a Native mode report server without HTTPS
        $ErrorActionPreference = "Stop"
   
        $server = $env:COMPUTERNAME
        $HTTPport = 80 # change the value if you used a different port for the private HTTP endpoint when the VM was created.
   
        ## Set PowerShell execution policy to be able to run scripts
        Set-ExecutionPolicy RemoteSigned -Force
   
        ## Utility method for verifying an operation's result
        function CheckResult
        {
            param($wmi_result, $actionname)
            if ($wmi_result.HRESULT -ne 0) {
                write-error "$actionname failed. Error from WMI: $($wmi_result.Error)"
            }
        }
   
        $starttime=Get-Date
        write-host -foregroundcolor DarkGray $starttime StartTime
   
        ## ReportServer Database name - this can be changed if needed
        $dbName='ReportServer'
   
        ## Register for MSReportServer_ConfigurationSetting
        ## Change the version portion of the path to "v11" to use the script for SQL Server 2012
        $RSObject = Get-WmiObject -class "MSReportServer_ConfigurationSetting" -namespace "root\Microsoft\SqlServer\ReportServer\RS_MSSQLSERVER\v12\Admin"
   
        ## Report Server Configuration Steps
   
        ## Setting the web service URL ##
        write-host -foregroundcolor green "Setting the web service URL"
        write-host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time
   
        ## SetVirtualDirectory for ReportServer site
            write-host 'Calling SetVirtualDirectory'
            $r = $RSObject.SetVirtualDirectory('ReportServerWebService','ReportServer',1033)
            CheckResult $r "SetVirtualDirectory for ReportServer"
   
        ## ReserveURL for ReportServerWebService - port $HTTPport (for local usage)
            write-host "Calling ReserveURL port $HTTPport"
            $r = $RSObject.ReserveURL('ReportServerWebService',"http://+:$HTTPport",1033)
            CheckResult $r "ReserveURL for ReportServer port $HTTPport" 
   
        ## Setting the Database ##
        write-host -foregroundcolor green "Setting the Database"
        write-host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time
   
        ## GenerateDatabaseScript - for creating the database
            write-host "Calling GenerateDatabaseCreationScript for database $dbName"
            $r = $RSObject.GenerateDatabaseCreationScript($dbName,1033,$false)
            CheckResult $r "GenerateDatabaseCreationScript"
            $script = $r.Script
   
        ## Execute sql script to create the database
            write-host 'Executing Database Creation Script'
            $savedcvd = Get-Location
            Import-Module SQLPS              ## this automatically changes to sqlserver provider
            Invoke-SqlCmd -Query $script
            Set-Location $savedcvd
   
        ## GenerateGrantRightsScript 
            $DBUser = "NT Service\ReportServer"
            write-host "Calling GenerateDatabaseRightsScript with user $DBUser"
            $r = $RSObject.GenerateDatabaseRightsScript($DBUser,$dbName,$false,$true)
            CheckResult $r "GenerateDatabaseRightsScript"
            $script = $r.Script
   
        ## Execute grant rights script
            write-host 'Executing Database Rights Script'
            $savedcvd = Get-Location
            cd sqlserver:\
            Invoke-SqlCmd -Query $script
            Set-Location $savedcvd
   
        ## SetDBConnection - uses Windows Service (type 2), username is ignored
            write-host "Calling SetDatabaseConnection server $server, DB $dbName"
            $r = $RSObject.SetDatabaseConnection($server,$dbName,2,'','')
            CheckResult $r "SetDatabaseConnection"  
   
        ## Setting the Report Manager URL ##
   
        write-host -foregroundcolor green "Setting the Report Manager URL"
        write-host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time
   
        ## SetVirtualDirectory for Reports (Report Manager) site
            write-host 'Calling SetVirtualDirectory'
            $r = $RSObject.SetVirtualDirectory('ReportManager','Reports',1033)
            CheckResult $r "SetVirtualDirectory"
   
        ## ReserveURL for ReportManager  - port $HTTPport
            write-host "Calling ReserveURL for ReportManager, port $HTTPport"
            $r = $RSObject.ReserveURL('ReportManager',"http://+:$HTTPport",1033)
            CheckResult $r "ReserveURL for ReportManager port $HTTPport"
   
        write-host -foregroundcolor green "Open Firewall port for $HTTPport"
        write-host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time
   
        ## Open Firewall port for $HTTPport
            New-NetFirewallRule -DisplayName “Report Server (TCP on port $HTTPport)” -Direction Inbound –Protocol TCP –LocalPort $HTTPport
            write-host "Added rule Report Server (TCP on port $HTTPport) in Windows Firewall"
   
        write-host 'Operations completed, Report Server is ready'
        write-host -foregroundcolor DarkGray $starttime StartTime
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time
5. Se criou a VM com uma porta HTTP que não sejam 80, modifique o parâmetro $HTTPport = 80.
6. O script está atualmente configurado para o Reporting Services. Se pretender executar o script para o Reporting Services, modifique a parte da versão do caminho para o espaço de nomes para "v11", na instrução ' Get-WmiObject.
7. Execute o script.

**Validação**: para verificar se a funcionalidade de servidor de relatório básico está a funcionar, consulte o [verificar a configuração](#verify-the-configuration) posterior deste tópico.

### <a name="use-script-to-configure-the-report-server-and-https"></a>Utilizar um script para configurar o servidor de relatórios e o HTTPS
Para utilizar o Windows PowerShell para configurar o servidor de relatórios, conclua os seguintes passos. A configuração inclui HTTPS, não HTTP.

1. A partir do portal clássico do Azure, selecione a VM e clique em ligar. Consoante a configuração do browser, poderá ser-lhe pedido para guardar um ficheiro. rdp para ligar à VM.
   
    ![ligar à máquina virtual do azure](./media/virtual-machines-windows-classic-ps-sql-report/IC650112.gif) Utilize o nome VM de utilizador, o nome de utilizador e a palavra-passe que configurou quando criou a VM. 
   
    Por exemplo, na imagem seguinte, o nome da VM é **ssrsnativecloud** e o nome de utilizador é **testuser**.
   
    ![nome de vm de inlcudes de início de sessão](./media/virtual-machines-windows-classic-ps-sql-report/IC764111.png)
2. Na VM, abra **ISE do Windows PowerShell** com privilégios administrativos. ISE do PowerShell está instalado por predefinição no Windows server 2012. É recomendado que utilizar o ISE em vez de uma janela do Windows PowerShell padrão, para que possam cole o script no ISE do, modificar o script e, em seguida, execute o script.
3. Para ativar a execução de scripts, execute o seguinte comando do Windows PowerShell:
   
        Set-ExecutionPolicy RemoteSigned
   
    Pode, em seguida, execute o seguinte procedimento para verificar a política:
   
        Get-ExecutionPolicy
4. No **ISE do Windows PowerShell**, clique em de **vista** menu e, em seguida, clique em **Mostrar painel de Script**.
5. Copie o seguinte script e cole-o painel de script de ISE do Windows PowerShell.
   
        ## This script configures the report server, including HTTPS
        $ErrorActionPreference = "Stop"
        $httpsport=443 # modify if you used a different port number when the HTTPS endpoint was created.
   
        # You can run the following command to get (.cloudapp.net certificates) so you can copy the thumbprint / certificate hash
        #dir cert:\LocalMachine -rec | Select-Object * | where {$_.issuer -like "*cloudapp*" -and $_.pspath -like "*root*"} | select dnsnamelist, thumbprint, issuer
        #
        # The certifacte hash is a REQUIRED parameter
        $certificatehash="" 
        # the certificate hash should not contain spaces
   
        if ($certificatehash.Length -lt 1) 
        {
            write-error "certificatehash is a required parameter"
        } 
        # Certificates should be all lower case
        $certificatehash=$certificatehash.ToLower()
        $server = $env:COMPUTERNAME
        # If the certificate is not a wildcard certificate, comment out the following line, and enable the full $DNSNAme reference.
        $DNSName="+"
        #$DNSName="$server.cloudapp.net"
        $DNSNameAndPort = $DNSName + ":$httpsport"
   
        ## Utility method for verifying an operation's result
        function CheckResult
        {
            param($wmi_result, $actionname)
            if ($wmi_result.HRESULT -ne 0) {
                write-error "$actionname failed. Error from WMI: $($wmi_result.Error)"
            }
        }
   
        $starttime=Get-Date
        write-host -foregroundcolor DarkGray $starttime StartTime
   
        ## ReportServer Database name - this can be changed if needed
        $dbName='ReportServer'
   
        write-host "The script will use $DNSNameAndPort as the DNS name and port" 
   
        ## Register for MSReportServer_ConfigurationSetting
        ## Change the version portion of the path to "v11" to use the script for SQL Server 2012
        $RSObject = Get-WmiObject -class "MSReportServer_ConfigurationSetting" -namespace "root\Microsoft\SqlServer\ReportServer\RS_MSSQLSERVER\v12\Admin"
   
        ## Reporting Services Report Server Configuration Steps
   
        ## 1. Setting the web service URL ##
        write-host -foregroundcolor green "Setting the web service URL"
        write-host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time
   
        ## SetVirtualDirectory for ReportServer site
            write-host 'Calling SetVirtualDirectory'
            $r = $RSObject.SetVirtualDirectory('ReportServerWebService','ReportServer',1033)
            CheckResult $r "SetVirtualDirectory for ReportServer"
   
        ## ReserveURL for ReportServerWebService - port 80 (for local usage)
            write-host 'Calling ReserveURL port 80'
            $r = $RSObject.ReserveURL('ReportServerWebService','http://+:80',1033)
            CheckResult $r "ReserveURL for ReportServer port 80" 
   
        ## ReserveURL for ReportServerWebService - port $httpsport
            write-host "Calling ReserveURL port $httpsport, for URL: https://$DNSNameAndPort"
            $r = $RSObject.ReserveURL('ReportServerWebService',"https://$DNSNameAndPort",1033)
            CheckResult $r "ReserveURL for ReportServer port $httpsport" 
   
        ## CreateSSLCertificateBinding for ReportServerWebService port $httpsport
            write-host "Calling CreateSSLCertificateBinding port $httpsport, with certificate hash: $certificatehash"
            $r = $RSObject.CreateSSLCertificateBinding('ReportServerWebService',$certificatehash,'0.0.0.0',$httpsport,1033)
            CheckResult $r "CreateSSLCertificateBinding for ReportServer port $httpsport" 
   
        ## 2. Setting the Database ##
        write-host -foregroundcolor green "Setting the Database"
        write-host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time
   
        ## GenerateDatabaseScript - for creating the database
            write-host "Calling GenerateDatabaseCreationScript for database $dbName"
            $r = $RSObject.GenerateDatabaseCreationScript($dbName,1033,$false)
            CheckResult $r "GenerateDatabaseCreationScript"
            $script = $r.Script
   
        ## Execute sql script to create the database
            write-host 'Executing Database Creation Script'
            $savedcvd = Get-Location
            Import-Module SQLPS                    ## this automatically changes to sqlserver provider
            Invoke-SqlCmd -Query $script
            Set-Location $savedcvd
   
        ## GenerateGrantRightsScript 
            $DBUser = "NT Service\ReportServer"
            write-host "Calling GenerateDatabaseRightsScript with user $DBUser"
            $r = $RSObject.GenerateDatabaseRightsScript($DBUser,$dbName,$false,$true)
            CheckResult $r "GenerateDatabaseRightsScript"
            $script = $r.Script
   
        ## Execute grant rights script
            write-host 'Executing Database Rights Script'
            $savedcvd = Get-Location
            cd sqlserver:\
            Invoke-SqlCmd -Query $script
            Set-Location $savedcvd
   
        ## SetDBConnection - uses Windows Service (type 2), username is ignored
            write-host "Calling SetDatabaseConnection server $server, DB $dbName"
            $r = $RSObject.SetDatabaseConnection($server,$dbName,2,'','')
            CheckResult $r "SetDatabaseConnection"  
   
        ## 3. Setting the Report Manager URL ##
   
        write-host -foregroundcolor green "Setting the Report Manager URL"
        write-host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time
   
        ## SetVirtualDirectory for Reports (Report Manager) site
            write-host 'Calling SetVirtualDirectory'
            $r = $RSObject.SetVirtualDirectory('ReportManager','Reports',1033)
            CheckResult $r "SetVirtualDirectory"
   
        ## ReserveURL for ReportManager  - port 80
            write-host 'Calling ReserveURL for ReportManager, port 80'
            $r = $RSObject.ReserveURL('ReportManager','http://+:80',1033)
            CheckResult $r "ReserveURL for ReportManager port 80"
   
        ## ReserveURL for ReportManager - port $httpsport
            write-host "Calling ReserveURL port $httpsport, for URL: https://$DNSNameAndPort"
            $r = $RSObject.ReserveURL('ReportManager',"https://$DNSNameAndPort",1033)
            CheckResult $r "ReserveURL for ReportManager port $httpsport" 
   
        ## CreateSSLCertificateBinding for ReportManager port $httpsport
            write-host "Calling CreateSSLCertificateBinding port $httpsport with certificate hash: $certificatehash"
            $r = $RSObject.CreateSSLCertificateBinding('ReportManager',$certificatehash,'0.0.0.0',$httpsport,1033)
            CheckResult $r "CreateSSLCertificateBinding for ReportManager port $httpsport" 
   
        write-host -foregroundcolor green "Open Firewall port for $httpsport"
        write-host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time
   
        ## Open Firewall port for $httpsport
            New-NetFirewallRule -DisplayName “Report Server (TCP on port $httpsport)” -Direction Inbound –Protocol TCP –LocalPort $httpsport
            write-host "Added rule Report Server (TCP on port $httpsport) in Windows Firewall"
   
        write-host 'Operations completed, Report Server is ready'
        write-host -foregroundcolor DarkGray $starttime StartTime
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time
6. Modificar o **$certificatehash** parâmetro do script:
   
   * Este é um **necessário** parâmetro. Se não tiver guardado o valor do certificado dos passos anteriores, utilize um dos seguintes métodos para copiar o valor de hash de certificado do thumbprint de certificados.:
     
       Na VM, abra o ISE do Windows PowerShell e execute o seguinte comando:
     
           dir cert:\LocalMachine -rec | Select-Object * | where {$_.issuer -like "*cloudapp*" -and $_.pspath -like "*root*"} | select dnsnamelist, thumbprint, issuer
     
       O resultado será semelhante ao seguinte. Se o script devolver uma linha em branco, a VM não tem um certificado configurado, por exemplo, consulte a secção [para utilizar o certificado autoassinado de máquinas virtuais](#to-use-the-virtual-machines-self-signed-certificate).
     
     OU
   * Na VM execute mmc.exe e, em seguida, adicione o **certificados** snap-in.
   * Sob o **autoridades de certificação de raiz fidedigna** nó faça duplo clique em seu nome de certificado. Se estiver a utilizar o certificado autoassinado da VM, o certificado é denominado depois do nome DNS da VM e termina com **cloudapp.net**.
   * Clique em de **detalhes** separador.
   * Clique em **Thumbprint**. O valor do thumbprint é apresentado no campo de detalhes, por exemplo af 11 60 b6 4b 28 8 d 89 0a 82 12 ff 6b a9 c3 66 4f 31 90 48
   * **Antes de executar o script**, remova os espaços entre os pares de valores. Por exemplo af1160b64b288d890a8212ff6ba9c3664f319048
7. Modificar o **$httpsport** parâmetro: 
   
   * Se utilizou a porta 443 para o ponto final HTTPS, não é necessário atualizar este parâmetro no script. Caso contrário, utilize o valor da porta selecionada quando configurou o ponto de final privado HTTPS na VM.
8. Modificar o **$DNSName** parâmetro: 
   
   * O script está configurado para um certificado de caráter universal $DNSName = "+". Se o fizer não pretende configurar para um enlace de certificado de caráter universal, comente $DNSName ="+"e ative a seguinte linha, referência $DNSNAme completa, $DNSName="$server.cloudapp.net # #".
     
       Altere o valor de $DNSName se não pretender utilizar o nome DNS da máquina virtual para o Reporting Services. Se utilizar o parâmetro, o certificado também tem de utilizar este nome e registar o nome globalmente num servidor DNS.
9. O script está atualmente configurado para o Reporting Services. Se pretender executar o script para o Reporting Services, modifique a parte da versão do caminho para o espaço de nomes para "v11", na instrução ' Get-WmiObject.
10. Execute o script.

**Validação**: para verificar se a funcionalidade de servidor de relatório básico está a funcionar, consulte o [verificar a configuração](#verify-the-connection) posterior deste tópico. Para verificar o certificado de enlace abra uma linha de comandos com privilégios administrativos e, em seguida, execute o seguinte comando:

    netsh http show sslcert

O resultado incluirá o seguinte:

    IP:port                      : 0.0.0.0:443

    Certificate Hash             : f98adf786994c1e4a153f53fe20f94210267d0e7

### <a name="use-configuration-manager-to-configure-the-report-server"></a>Utilize o Gestor de configuração para configurar o servidor de relatórios
Se não pretender executar o script do PowerShell para configurar o servidor de relatórios, siga os passos nesta secção para utilizar o Gestor de configuração de modo nativo do Reporting Services para configurar o servidor de relatórios.

1. A partir do portal clássico do Azure, selecione a VM e clique em ligar. Utilize o nome de utilizador e palavra-passe que configurou quando criou a VM.
   
    ![ligar à máquina virtual do azure](./media/virtual-machines-windows-classic-ps-sql-report/IC650112.gif)
2. Execute o Windows update e instalar atualizações para a VM. Se for necessário um reinício da VM, reiniciar a VM e voltar a ligar à VM do portal clássico do Azure.
3. A partir do menu Iniciar na VM, escreva **Reporting Services** e abra **Reporting Services Configuration Manager**.
4. Deixe os valores predefinidos para **nome do servidor** e **instância do servidor de relatórios**. Clique em **Ligar**.
5. No painel esquerdo, clique em **URL do serviço Web**.
6. Por predefinição, RS está configurado para a porta 80 de HTTP com o IP "Atribuído todos os". Para adicionar HTTPS:
   
   1. No **certificado SSL**: selecione o certificado que pretende utilizar, por exemplo [nome da VM]. cloudapp.net. Se não forem apresentados certificados, consulte a secção **passo 2: criar um certificado de servidor** para obter informações sobre como instalar e confiar no certificado na VM.
   2. Em **porta SSL**: escolha 443. Se tiver configurado o ponto de final privado HTTPS na VM com uma porta privada diferente, utilize esse valor aqui.
   3. Clique em **aplicar** e aguarde pela conclusão da operação.
7. No painel esquerdo, clique em **base de dados**.
   
   1. Clique em **alterar Databas**i.
   2. Clique em **criar uma nova base de dados de servidor de relatório** e, em seguida, clique em **seguinte**.
   3. Deixe a predefinição **nome do servidor**: da VM name e deixe a predefinição **tipo de autenticação** como **utilizador atual** – **a segurança integrada**. Clique em **Seguinte**.
   4. Deixe a predefinição **nome de base de dados** como **ReportServer** e clique em **seguinte**.
   5. Deixe a predefinição **tipo de autenticação** como **credenciais do serviço** e clique em **seguinte**.
   6. Clique em **seguinte** no **resumo** página.
   7. Quando a configuração está concluída, clique em **concluir**.
8. No painel esquerdo, clique em **URL do Gestor de relatórios**. Deixe a predefinição **diretório Virtual** como **relatórios** e clique em **aplicar**.
9. Clique em **saída** para fechar o Gestor de configuração do Reporting Services.

## <a name="step-4-open-windows-firewall-port"></a>Passo 4: Porta de Firewall Abra Windows
> [!NOTE]
> Se utilizou um dos scripts para configurar o servidor de relatórios, pode ignorar esta secção. O script incluído um passo para abrir a porta de firewall. A predefinição foi porta 80 para HTTP e a porta 443 para HTTPS.
> 
> 

Para ligar remotamente ao Gestor de relatórios ou o servidor de relatórios na máquina virtual, é necessário um ponto final TCP na VM. É necessário abrir a mesma porta na firewall da VM. O ponto final foi criado quando a VM foi aprovisionada.

Esta secção fornece informações básicas sobre como abrir a porta de firewall. Para obter mais informações, consulte [configurar uma Firewall para acesso ao servidor de relatórios](https://technet.microsoft.com/library/bb934283.aspx)

> [!NOTE]
> Se utilizou o script para configurar o servidor de relatórios, pode ignorar esta secção. O script incluído um passo para abrir a porta de firewall.
> 
> 

Se tiver configurado uma porta privada para HTTPS sem ser a 443, modifique o seguinte script conforme apropriado. Para abrir a porta **443** na Firewall do Windows, concluir os seguintes procedimentos:

1. Abra uma janela do Windows PowerShell com privilégios administrativos.
2. Se utilizou uma porta diferente da 443 quando configurou o ponto final de HTTPS na VM, atualize a porta no comando seguinte e, em seguida, execute o comando:
   
        New-NetFirewallRule -DisplayName “Report Server (TCP on port 443)” -Direction Inbound –Protocol TCP –LocalPort 443
3. Quando o comando for concluído, **Ok** é apresentado na linha de comandos.

Para verificar que a porta é aberta, abra uma janela do Windows PowerShell e execute o seguinte comando:

    get-netfirewallrule | where {$_.displayname -like "*report*"} | select displayname,enabled,action

## <a name="verify-the-configuration"></a>Verificar a configuração
Para verificar se a funcionalidade de servidor de relatório básico agora está a funcionar, abra o browser com privilégios administrativos e, em seguida, navegue até à seguinte relatório Gestor de servidor ad relatório URLS:

* Na VM, navegue para o URL do servidor de relatório:
  
        http://localhost/reportserver
* Na VM, navegue para o URL do relatório do Configuration Manager:
  
        http://localhost/Reports
* Do seu computador local, navegue para o **remoto** Gestor de relatórios na VM. Atualize o nome DNS no exemplo seguinte, conforme apropriado. Quando lhe for pedido para uma palavra-passe, utilize as credenciais de administrador que criou quando a VM foi aprovisionada. O nome de utilizador está no [domínio]\[nome de utilizador] formato, onde o domínio é o nome de computador VM, por exemplo ssrsnativecloud\testuser. Se não estiver a utilizar HTTP**S**, remova o **s** no URL. Consulte a secção seguinte para obter informações sobre como criar utilizadores adicionais na VM.
  
        https://ssrsnativecloud.cloudapp.net/Reports
* Do seu computador local, navegue para o URL do servidor de relatórios remoto. Atualize o nome DNS no exemplo seguinte, conforme apropriado. Se não estiver a utilizar o HTTPS, remova o s no URL.
  
        https://ssrsnativecloud.cloudapp.net/ReportServer

## <a name="create-users-and-assign-roles"></a>Criar utilizadores e atribuir funções
Depois de configurar e verificar o servidor de relatórios, uma tarefa de administrativa comum é criar um ou mais utilizadores e atribuir utilizadores a funções de Reporting Services. Para obter mais informações, consulte o seguinte:

* [Criar uma conta de utilizador local](https://technet.microsoft.com/library/cc770642.aspx)
* [Acesso de utilizador de conceder a um servidor de relatórios (Gestor de relatórios)](https://msdn.microsoft.com/library/ms156034.aspx))
* [Criar e gerir atribuições de função](https://msdn.microsoft.com/library/ms155843.aspx)

## <a name="to-create-and-publish-reports-to-the-azure-virtual-machine"></a>Para criar e publicar os relatórios para a Máquina Virtual do Azure
A tabela seguinte resume algumas das opções disponíveis para publicar relatórios existentes a partir de um computador no local para o servidor de relatórios alojados do Microsoft Azure Virtual Machine:

* **Script de RS.exe**: Utilize RS.exe script para copiar itens de relatório do e servidor de relatórios existentes para a máquina de Virtual do Microsoft Azure. Para obter mais informações, consulte a secção "Para o modo nativo – máquinas de virtuais do Microsoft Azure de modo nativo" em [exemplo Reporting Services rs.exe Script ao migrar conteúdo entre servidores de relatório](https://msdn.microsoft.com/library/dn531017.aspx).
* **Report Builder**: A máquina virtual inclui a, clique em-uma vez a versão do Microsoft SQL Server Report Builder. Para Report builder a primeira hora de início na máquina virtual:
  
  1. Inicie o browser com privilégios administrativos.
  2. Navegue para o Gestor de relatórios na máquina virtual e clique em **Report Builder** no Friso.
     
     Para obter mais informações, consulte [instalar, desinstalar e que suportam o Report Builder](https://technet.microsoft.com/library/dd207038.aspx).
* **SQL Server Data Tools: VM**: Se criou a VM com o SQL Server 2012, então, SQL Server Data Tools está instalada na máquina virtual e pode ser utilizada para criar **Report Server Projects** e os relatórios na máquina virtual. SQL Server Data Tools pode publicar os relatórios para o servidor de relatórios na máquina virtual.
  
    Se criou a VM com o SQL server 2014, pode instalar o SQL Server dados ferramentas - BI para o visual Studio. Para obter mais informações, consulte o seguinte:
  
  * [Microsoft SQL Server Data Tools - Business Intelligence para o Visual Studio 2013](https://www.microsoft.com/download/details.aspx?id=42313)
  * [Microsoft SQL Server Data Tools - Business Intelligence do Visual Studio 2012](https://www.microsoft.com/download/details.aspx?id=36843)
  * [Ferramentas de dados do SQL Server e SQL Server Business Intelligence (SSDT BI)](http://curah.microsoft.com/30004/sql-server-data-tools-ssdt-and-sql-server-business-intelligence)
* **SQL Server Data Tools: Remoto**: no seu computador local, criar um projeto do Reporting Services no SQL Server Data Tools que contém relatórios do Reporting Services. Configure o projeto para estabelecer ligação com o URL do serviço web.
  
    ![Propriedades do projeto SSDT para o projeto do SSRS](./media/virtual-machines-windows-classic-ps-sql-report/IC650114.gif)
* **Utilizar script**: utilizar o script para copiar o conteúdo do servidor de relatórios. Para obter mais informações, consulte [exemplo Reporting Services rs.exe Script ao migrar conteúdo entre servidores de relatório](https://msdn.microsoft.com/library/dn531017.aspx).

## <a name="minimize-cost-if-you-are-not-using-the-vm"></a>Minimizar os custos se não estiver a utilizar a VM
> [!NOTE]
> Para minimizar os custos para as máquinas de virtuais do Azure quando não está em utilização, encerre a VM a partir do portal clássico do Azure. Se utilizar as opções de energia do Windows dentro de uma VM para encerre a VM, ainda lhe serem cobrados mesma quantidade para a VM. Para reduzir os custos, tem de encerrar a VM no portal clássico do Azure. Se já não precisar de VM, lembre-se ao eliminar a VM e os ficheiros. vhd associados, para evitar custos de armazenamento. Para obter mais informações, consulte a secção de FAQ [detalhes de preços de máquinas virtuais](https://azure.microsoft.com/pricing/details/virtual-machines/).

## <a name="more-information"></a>Mais Informações
### <a name="resources"></a>Recursos
* Para o conteúdo semelhante, relacionados com uma implementação de servidor único do SQL Server Business Intelligence e o SharePoint 2013, consulte [utilize o Windows PowerShell para criar uma VM de Azure com o SQL Server BI e o SharePoint 2013](https://msdn.microsoft.com/library/azure/dn385843.aspx).
* Para o conteúdo semelhante, relacionados com uma implementação de vários servidor do SQL Server Business Intelligence e o SharePoint 2013, consulte [implementar o SQL Server Business Intelligence em Azure Virtual Machines](https://msdn.microsoft.com/library/dn321998.aspx).
* Para obter informações gerais relacionados com implementações do SQL Server Business Intelligence em Azure Virtual Machines, consulte [SQL Server Business Intelligence em Azure Virtual Machines](virtual-machines-windows-classic-ps-sql-bi.md).
* Para obter mais informações sobre o custo de encargos de computação do Azure, consulte o separador de máquinas virtuais de [Calculadora de preços do Azure](https://azure.microsoft.com/pricing/calculator/?scenario=virtual-machines).

### <a name="community-content"></a>Conteúdo da Comunidade
* Para instruções passo a passo sobre como criar um servidor de relatórios do Reporting Services nativo modo sem utilizar o script, consulte [aloja serviço SQL Server Reporting Services na máquina de Virtual do Azure](http://adititechnologiesblog.blogspot.in/2012/07/hosting-sql-reporting-service-on-azure.html).

### <a name="links-to-other-resources-for-sql-server-in-azure-vms"></a>Ligações para outros recursos para o SQL Server em VMs do Azure
[SQL Server em Descrição geral de máquinas virtuais do Azure](../sql/virtual-machines-windows-sql-server-iaas-overview.md)


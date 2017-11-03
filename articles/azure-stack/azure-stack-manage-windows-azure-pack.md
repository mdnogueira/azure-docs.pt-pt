---
title: "Gerir as máquinas virtuais do Windows Azure Pack de pilha do Azure | Microsoft Docs"
description: Saiba como gerir VMs do Windows Azure Pack (WAP) a partir do portal de utilizador na pilha do Azure.
services: azure-stack
documentationcenter: 
author: walterov
manager: byronr
editor: 
ms.assetid: 213c2792-d404-4b44-8340-235adf3f8f0b
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: walterov
ms.openlocfilehash: b07a18055d149e20cd605a892063eccecf3df8a4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="manage-windows-azure-pack-virtual-machines-from-azure-stack"></a>Gerir as máquinas virtuais do Windows Azure Pack de pilha do Azure

*Aplica-se a: Azure da pilha Kit de desenvolvimento*

No Kit de desenvolvimento de pilha do Azure, pode ativar o acesso a partir do portal de utilizador da pilha do Azure para máquinas virtuais em execução no Windows Azure Pack de inquilino. Os utilizadores podem utilizar o portal de pilha do Azure para gerir as respetivas máquinas virtuais de IaaS existentes e redes virtuais. Estes recursos são disponibilizados no Windows Azure Pack através de componentes do Service Provider Foundation (SPF) e o Virtual Machine Manager (VMM) subjacentes. Especificamente, os utilizadores podem:

* Procurar recursos
* Examine os valores de configuração
* Parar ou iniciar uma máquina virtual
* Ligar a uma máquina virtual através do protocolo de ambiente de trabalho remoto (RDP)
* Criar e gerir pontos de verificação
* Elimine as máquinas virtuais e redes virtuais

Esta funcionalidade é fornecida através do conector do Windows Azure Pack para pilha do Azure (pré-visualização). Este artigo mostra como configurar o conector para um ambiente do Azure pilha de nó único.

Para esta versão de pré-visualização do conector, tenha em atenção o seguinte:
* Utilize o conector do Windows Azure Pack apenas em ambientes de teste (para a pilha do Azure e no Windows Azure Pack) e não nas implementações de produção.
* Tem de ter o Windows Azure Pack Update Rollup (UR) 9.1 ou posterior e o System Center SPF e o VMM UR 9 ou posterior.
* Os componentes do VMM e o SPF podem ser um System Center 2012 R2 ou o System Center 2016.
* Tem de efetuar os passos de configuração na pilha ambas do Azure e no Windows Azure Pack.
* As instruções aplicam-se a ambientes de não - Cloud Platform System CPS.
* Para rever os problemas conhecidos, consulte [resolução de problemas do Microsoft Azure pilha](azure-stack-troubleshooting.md).


## <a name="architecture"></a>Arquitetura
O diagrama seguinte mostra os componentes principais do conector do Windows Azure Pack.

![Os componentes do conector do Windows Azure Pack](media/azure-stack-manage-wap/image1.png)

Tenha em atenção os seguintes detalhes:
* O portal de utilizador de pilha do Azure acede as informações de recursos de ambas as nuvens (pilha do Azure e o Windows Azure Pack).
* O utilizador tem de ter uma conta que é válida em ambos os ambientes.
* O portal de utilizador de pilha do Azure tem de ter acesso à rede para os componentes a ser executado no Windows Azure Pack.
* No **WAP** secção de diagrama, pode ver os novos módulos de conector do Windows Azure Pack (WAP extensão e conector) e existente API do Windows Azure Pack inquilino com componentes do SPF e do VMM.


## <a name="identity-management"></a>Gestão de identidades
A API de inquilino do Windows Azure Pack têm de confiar o serviço de token de segurança do Azure pilha (STS).

Quando um utilizador executa uma ação através do portal do Azure pilha que visa recursos do Windows Azure Pack, o portal utiliza a API de inquilino do Windows Azure Pack. Por conseguinte, o token de autenticação de utilizador fornecido tem de ser de um STS fidedigna. Consulte o diagrama a seguir:

![Diagrama de autenticação do conector do Windows Azure Pack](media/azure-stack-manage-wap/image2.png)

No ambiente do kit de desenvolvimento, Windows Azure Pack e do Azure pilha tenham fornecedores de identidade independentes. Os utilizadores que ambos os ambientes de acesso a partir do portal do Azure pilha tem de ter o mesmo nome de nome principal (UPN) do utilizador em ambos os fornecedores de identidade. Por exemplo, a conta de  *azurestackadmin@azurestack.local*  também deve existir no STS para o Windows Azure Pack. Em que o AD FS não está configurado para suportar as relações de confiança de saída, irá estabelecer confiança de componentes do Windows Azure Pack (API de inquilinos) para a instância de pilha do Azure do AD FS.

## <a name="prerequisites"></a>Pré-requisitos

### <a name="download-the-windows-azure-pack-connector"></a>Transferir o conector do Windows Azure Pack
No [Microsoft Download Center](https://aka.ms/wapconnectorazurestackdlc), transfira o ficheiro .exe e extraia-o para o seu computador local. Mais tarde, copie o conteúdo para um computador que pode aceder ao seu ambiente do Windows Azure Pack.

### <a name="deployment-option-requirement"></a>Requisito da opção de implementação
Para integrar com o Windows Azure Pack, pode implementar a pilha do Azure utilizando a opção do AD FS ou a opção do Azure Active Directory.

### <a name="connectivity-requirements"></a>Requisitos de conectividade
1. Do computador no qual pretende aceder ao portal de utilizador de pilha do Azure, certifique-se de que pode aceder ao portal a Windows Azure Pack inquilino através do browser.
2. A máquina virtual de AzS WASP01 na pilha do Azure tem de ser capaz de ligar ao computador portal inquilino de Windows Azure Pack. Utilize o Ping.exe para verificar a conectividade de rede. 
3.  Tem de ter certificados válidos para os novos serviços de conector. Estes certificados SSL devem ser emitidos por uma autoridade de certificação fidedigna (AC). Não é possível utilizar certificados autoassinados. Os certificados SSL têm de confiar pilha do Azure (especificamente a VM AzS WASP01) e qualquer outro computador que o inquilino pode utilizar para aceder ao portal de utilizador de pilha do Azure.
 
    >[!NOTE]
    Porque AzS WASP01 com o Windows Server com a opção de instalação Server Core, pode utilizar uma ferramenta da linha de comandos como Certutil.ext para importar o certificado. Por exemplo, foi possível copiar o ficheiro. cer para c:\temp no AzS WASP01 e, em seguida, execute o comando **"CA" "c:\temp\certname.cer" certutil - addstore**.

4.  Para estabelecer conectividade RDP a máquinas virtuais de inquilino de Windows Azure Pack através do portal de pilha do Azure, o ambiente do Windows Azure Pack têm de permitir tráfego de ambiente de trabalho remoto para as VMs de inquilino.
5.  Para conectividade entre máquinas de virtuais de inquilino do Azure pilha e máquinas de virtuais de inquilino do Windows Azure Pack, os respetivos endereços IP externos tem de ser encaminháveis entre os dois ambientes. Também pode incluir esta conectividade associar um servidor DNS para resolver nomes de máquina virtual entre os ambientes.

### <a name="iis-requirements"></a>Requisitos do IIS
O computador que aloja o portal de inquilino do Windows Azure Pack (que pode ser um anfitrião físico, uma máquina virtual ou várias máquinas virtuais) tem de ter a extensão do URL Rewrite IIS instalada. Se ainda não estiver instalado, poderá transferi-lo de [aqui](https://www.iis.net/downloads/microsoft/url-rewrite). Se várias máquinas virtuais do anfitrião do portal inquilino, instale a extensão em cada máquina virtual.

## <a name="configure-azure-stack"></a>Configurar a pilha do Azure
Antes de configurar o conector do Windows Azure Pack, tem de ativar o modo de cloud multi no portal de utilizador de pilha do Azure. Este modo permite que os utilizadores selecionarem que em nuvem para aceder a recursos.

Para ativar o modo de cloud multi, tem de executar o script de adicionar AzurePackConnector.ps1 após a implementação de pilha do Azure. A tabela seguinte descreve os parâmetros do script.


|  Parâmetro | Descrição | Exemplo |   
| -------- | ------------- | ------- |  
| AzurePackClouds | URIs dos conectores do Windows Azure Pack. Estes URIs deve corresponder à portais de inquilino do Windows Azure Pack. | @{CloudName = "AzurePack1"; CloudEndpoint = "https://waptenantportal1:40005"},@{CloudName = "AzurePack2"; CloudEndpoint = "https://waptenantportal2:40005"}<br><br>  (Por predefinição, o valor da porta é 40005.) |  
| AzureStackCloudName | Etiqueta para representar a nuvem do Azure pilha local.| "AzureStack" |
| DisableMultiCloud | Um comutador para desativar o modo de vários cloud.| N/D |
| | |

Pode executar o script de adicionar AzurePackConnector.ps1 imediatamente após a implementação, ou posterior. Para executar o script imediatamente após a implementação, utilize a mesma sessão do Windows PowerShell em que foi concluída a implementação da pilha do Azure. Caso contrário, pode abrir uma nova sessão do Windows PowerShell como administrador (iniciada sessão como conta de azurestackadmin).

1. Execute o script de adicionar AzurePackConnector.ps1 usando os comandos seguintes (com valores específicos ao seu ambiente). Tenha em atenção que o script de adicionar AzurePackConnector permite-lhe adicionar mais do que um ponto final de conector do Windows Azure Pack.
 
 ```powershell
    $cred = New-Object System.Management.Automation.PSCredential("cloudadmin@azurestack.local", `
    (ConvertTo-SecureString -String "<password>" -AsPlainText -Force))
    $session = New-PSSession -ComputerName 'azs-ercs01' `
     -Credential $cred `
     -ConfigurationName PrivilegedEndpoint `
     -Authentication Credssp

    # Enable Multicloud
    Invoke-Command -Session $session -ScriptBlock { Add-AzurePackConnector -AzurePackClouds `
    @{CloudName = "AzurePack_1"; CloudEndpoint = "https://waptenantportal1:40005"},`
    @{CloudName = "AzurePack_2"; CloudEndpoint = "https://waptenantportal2:40005" } `
    -AzureStackCloudName "AzureStack" }

 ```
> [!NOTE]
> Na compilação atual é um problema onde após termina o script de adicionar AzurePackConnector, permanecer num ciclo de consulta por um longo período de tempo (vários minutos) até que termina. Depois de ver a mensagem **VERBOSO: passo 'Configurar o Azure Pack conector' Estado: 'Êxito'**, pode terminar o script ou aguarde até que este deixa de por si só. -Não efetuar uma diferença porque a configuração de já ter sido bem sucedida.

2. Tome nota dos ficheiros de saída que são produzidos por este script, um para cada ambiente do Windows Azure Pack que especificou. Os ficheiros estão localizados em: \\\su1fileserver\SU1_Infrastructure_1\AzurePackConnectorOutput. Estes ficheiros contêm as informações necessárias para configurar os ambientes do Windows Azure Pack de destino. Transmitir este ficheiro como um parâmetro para um script mais tarde nestas instruções. Este ficheiro contém as seguintes informações:

    * **AzurePackConnectorEndpoint**: contém o ponto final para o serviço do conector do Windows Azure Pack.
    * **AuthenticationIdentityProviderPartner**: contém o par de valor seguintes:
        * Token de autenticação, assinatura de certificado que tem a API de inquilino do Windows Azure Pack para confiar para aceitar as chamadas a partir da extensão de portal de pilha do Azure.

        * O "Realm" associada com o certificado de assinatura. Por exemplo: https://adfs.local.azurestack.global.external/adfs/c1d72562-534e-4aa5-92aa-d65df289a107/.

3.  Procure a pasta que contém os ficheiros de saída (\\su1fileserver\SU1_Infrastructure_1\AzurePackConnectorOutput) e copie os ficheiros para o seu computador local. Os ficheiros terá um aspeto semelhantes a isto: AzurePack-06-27-15-50.txt.

4.  Teste a configuração.

    a. Abra o browser e inicie sessão no portal de utilizador de pilha do Azure (https://portal.local.azurestack.external/).
    
    b. Depois de iniciar sessão como um inquilino e carrega a portal, verá erros sobre não conseguir obter subscrições ou extensões da nuvem do Azure Pack. Clique em **OK** fechar estas mensagens. (Estas mensagens de erro desaparecerá depois de configurar o Windows Azure Pack.)

    c. Tenha em atenção o **nuvem** na lista pendente no canto superior esquerdo do portal.

    ![O Seletor de nuvem na interface de utilizador de pilha do Azure](media/azure-stack-manage-wap/image3.png)

## <a name="configure-windows-azure-pack"></a>Configurar o Windows Azure Pack
Para este conector versão só de pré-visualização, o Windows Azure Pack requer configuração manual.

>[!IMPORTANT]
Para esta versão de pré-visualização, utilize o conector do Windows Azure Pack apenas em ambientes de teste e não nas implementações de produção.

1.  Instalar ficheiros de MSI do conector da máquina de virtual do portal inquilino do Windows Azure Pack e instalar certificados. (Se tiver várias máquinas virtuais portal inquilino, tem de concluir este passo em cada máquina virtual.)

    a. No Explorador de ficheiros, copie o **WAPConnector** pasta (o que transferiu anteriormente) para uma pasta denominada **c:\temp** na máquina virtual portal inquilino.

    b. Abra uma ligação de consola ou RDP para a máquina virtual portal inquilino.

    c. Altere os diretórios para **c:\temp\wapconnector\setup\scripts**e execute o **instalação Connector.ps1** script para instalar três ficheiros MSI. Não existem parâmetros são necessários.

     ```powershell
    cd C:\temp\wapconnector\setup\scripts\

    .\Install-Connector.ps1
    ```
     d. Altere os diretórios para **c:\inetpub** e certifique-se de que os três novos sites estão instalados:

       * Conector MgmtSvc

       * MgmtSvc ConnectorExtension

       * MgmtSvc ConnectorController

    e. Do mesmo **c:\temp\wapconnector\setup\scripts** pasta, execute o **configurar Certificates.ps1** script para instalar os certificados. Por predefinição, irá utilizar o mesmo certificado que está disponível para o site do Portal inquilino no Windows Azure Pack. Certifique-se de que este é um certificado válido (fidedigno pela máquina virtual do Azure pilha AzS-WASP01 e qualquer computador cliente que acede ao portal do Azure pilha). Caso contrário, a comunicação não funcionará. (Em alternativa, pode explicitamente passar um thumbprint de certificado como um parâmetro utilizando o parâmetro-Thumbprint.)

     ```powershell
        cd C:\temp\wapconnector\setup\scripts\

        .\Configure-Certificates.ps1
    ```

    f. Para concluir a configuração destes três serviços, execute o **configurar WapConnector.ps1** script para atualizar os parâmetros do ficheiro Web. config.

    |  Parâmetro | Descrição | Exemplo |   
    | -------- | ------------- | ------- |  
    | TenantPortalFQDN | O portal de inquilino do Windows Azure Pack FQDN. | tenant.contoso.com | 
    | TenantAPIFQDN | O FQDN de API do Windows Azure Pack inquilino. | tenantapi.contoso.com  |
    | AzureStackPortalFQDN | O portal de utilizador do Azure pilha FQDN. | Portal.local.azurestack.external |
    | | |
    
     ```powershell
    .\Configure-WapConnector.ps1 -TenantPortalFQDN "tenant.contoso.com" `
         -TenantAPIFQDN "tenantapi.contoso.com" `
         -AzureStackPortalFQDN "portal.local.azurestack.external"
    ```
    g. Se tiver várias máquinas virtuais portal inquilino, repita o passo 1 para cada uma destas máquinas virtuais.

2. Instale novo MSI de API de inquilino em cada máquina virtual de API de inquilino do Windows Azure Pack.

    a. Se um balanceador de carga está a ser utilizado, poderá marcar a máquina virtual como offline.

    b. No Explorador de ficheiros, copie o **WAPConnector** pasta para uma pasta denominada **c:\temp** em cada máquina de API de inquilinos.

    c. Copie o ficheiro de AzurePackConnectorOutput.txt que guardou anteriormente, a **c:\temp\WAPConnector**.

    d. Abra uma ligação de consola ou RDP para a VM de API de inquilinos copiados os ficheiros a.
    
    e. Altere os diretórios para **c:\temp\wapconnector\setup\scripts**e execute **atualização TenantAPI.ps1**. Esta nova versão da API de inquilinos WAP contém uma alteração para ativar uma relação de confiança, não apenas com o STS atual, mas também com a instância do AD FS na pilha do Azure.

     ```powershell
    cd C:\temp\wapconnector\setup\packages\

    .\Update-TenantAPI.ps1
    ```

    f.  Repita o passo 2 em qualquer máquina virtual com a API de inquilinos.
3. De **apenas um** das VMs de API de inquilinos, execute o script de configurar TrustAzureStack.ps1 para adicionar uma relação de confiança entre a API de inquilinos e a instância do AD FS na pilha do Azure. Tem de utilizar uma conta com acesso de administrador do sistema na base de dados Microsoft.MgmtSvc.Store. Este script tem os seguintes parâmetros:

    | Parâmetro | Descrição | Exemplo |
    | --------- | ------------| ------- |
   | SqlServer | O nome do SQL Server que contém a base de dados Microsoft.MgmtSvc.Store. Este parâmetro é necessário. | SQLServer | 
   | DataFile | O ficheiro de saída que foi gerado durante a configuração do modo de cloud multi pilha do Azure de versões anteriores. Este parâmetro é necessário. | AzurePack-06-27-15-50.txt | 
   | PromptForSqlCredential | Indica que o script deve solicitar-lhe interativamente para uma credencial de autenticação do SQL Server a utilizar quando ligar à instância do SQL Server. A credencial fornecida tem de ter permissões suficientes para desinstalar as bases de dados, esquemas e eliminar inícios de sessão do utilizador. Se não for fornecido nenhum, o script parte do princípio de que contexto de utilizador atual tem acesso. | Não é necessário nenhum valor. |
   |  |  |

    Se não souber o SQL Server para utilizar, pode detetá-la. Ligue o computador da API de inquilinos, utilize o comando de Unprotect-MgmtSvc desproteger o ficheiro Web. config de API de inquilinos e procure o nome do servidor na cadeia de ligação. Lembre-se executar MgmtSvc de proteger novamente para proteger o ficheiro Web. config de API de inquilinos.

  ```powershell
  cd C:\temp\wapconnector\setup\scripts\

 .\Configure-TrustAzureStack.ps1 -SqlServer "SQLServer" `
       -DataFile "C:\temp\wapconnector\AzurePackConnectorOutput.txt"
  ```

## <a name="example"></a>Exemplo
O exemplo seguinte mostra uma implementação completa do conector do Windows Azure Pack numa configuração de pilha do Azure de nó único e dois instalações do Windows Azure Pack Express. (Cada instalação rápida estiver num computador único, com os nomes de exemplo *wapcomputer1* e*wapcomputer2*.)

```powershell
# Run the following script on the Azure Stack host
$cred = New-Object System.Management.Automation.PSCredential("cloudadmin@azurestack.local",`
     (ConvertTo-SecureString -String "p@ssw0rd" -AsPlainText -Force))
$session = New-PSSession -ComputerName 'azs-ercs01' -Credential $cred `
     -ConfigurationName PrivilegedEndpoint -Authentication Credssp
 
# Enable Multicloud
invoke-command -Session $session -ScriptBlock { Add-AzurePackConnector -AzurePackClouds `
     @{CloudName = "AzurePack_1"; CloudEndpoint = "https://wapcomputer1.contoso.com:40005"},`
     @{CloudName = "AzurePack_2"; CloudEndpoint = "https://wapcomputer2.contoso.com:40005"}`
     -AzureStackCloudName "AzureStack" }  

```
Transfira o ficheiro .exe o [Microsoft Download Center](https://aka.ms/wapconnectorazurestackdlc), extraia- e copie a pasta de WAPConnector para um **c:\temp** pasta no computador do Windows Azure Pack. Copie os ficheiros que foram gerados como resultado do script anterior (localizado em \\\su1fileserver\SU1_Infrastructure_1\AzurePackConnectorOutput) para o **c:\temp\WAPConnector** pasta. (Os ficheiros serão procura semelhante a isto: AzurePack-06-27-15-50.txt.) Em seguida, execute o script seguinte, uma vez por instância do Windows Azure Pack:

 ```powershell
# Install Connector components
cd C:\temp\WAPConnector\Setup\Scripts
.\Install-Connector.ps1

# Configure Certificates for the new Connector services
.\Configure-Certificates.ps1

# Configure the Connector services
.\Configure-WapConnector.ps1 -TenantPortalFQDN "wapcomputer1.contoso.com" `
     -TenantAPIFQDN "wapcomputer1.contoso.com" `
     -AzureStackPortalFQDN "portal.local.azurestack.external"

# Install the updated TenantAPI
.\Update-TenantAPI.ps1

# Establish trust with the Azure Stack AD FS
.\Configure-TrustAzureStack.ps1 -SqlServer "wapcomputer1" `
     -DataFile "C:\temp\wapconnector\AzurePack-06-27-15-50.txt" 

```
## <a name="troubleshooting-tips"></a>Sugestões de resolução de problemas
1.  Certifique-se existe conetividade de rede entre a pilha do Azure e o Windows Azure Pack. Este conectividade deve ser entre os computadores de inquilino que acede ao portal do Azure pilha e a máquina de virtual do portal inquilino do Windows Azure Pack em que estiver a executar os novos serviços de conector.
2.  Certifique-se de que todos os especificado que FQDNs estão corretos.
3.  Certifique-se de que os certificados SSL utilizados nos novos serviços de conector são consideradas fidedignas pela pilha do Azure (especificamente a VM AzS WASP01) e qualquer outro computador inquilino pode utilizar para aceder ao portal de utilizador de pilha do Azure.
4. Para problemas conhecidos, consulte [resolução de problemas do Microsoft Azure pilha](azure-stack-troubleshooting.md).


## <a name="next-steps"></a>Passos seguintes
[Os portais de administrador e utilizador a utilizar na pilha do Azure](azure-stack-manage-portals.md)

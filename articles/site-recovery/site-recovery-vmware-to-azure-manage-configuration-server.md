---
title: " Gerir um servidor de configuração no Azure Site Recovery | Microsoft Docs"
description: "Este artigo descreve como definir e gerir um servidor de configuração."
services: site-recovery
documentationcenter: 
author: AnoopVasudavan
manager: gauravd
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: backup-recovery
ms.date: 10/06/2017
ms.author: anoopkv
ms.openlocfilehash: e4740c96383468713976e5a98881bec13b0c1921
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="manage-a-configuration-server"></a>Gerir um servidor de configuração

Servidor de configuração atua como um coordenador entre os serviços de recuperação de Site e da sua infraestrutura no local. Este artigo descreve como pode configurar, configurar e gerir o servidor de configuração.

> [!NOTE]
> [Planeamento da capacidade](site-recovery-capacity-planner.md) é um passo importante para se certificar de que irá implementar o servidor de configuração com uma configuração de conjuntos de que os requisitos de carga. Leia mais sobre [requisitos para um servidor de configuração de dimensionamento](#sizing-requirements-for-a-configuration-server).


## <a name="prerequisites"></a>Pré-requisitos
Seguem-se a mínimos de hardware, software e configuração de rede necessária para configurar um servidor de configuração.
> [!IMPORTANT]
> Quando implementar um servidor de configuração de proteção de máquinas virtuais VMware, recomendamos que implemente-o como um **altamente disponível (HA)** máquina virtual.

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]

## <a name="downloading-the-configuration-server-software"></a>Transferir o software de servidor de configuração

1. Inicie sessão no portal do Azure e navegue para o Cofre de serviços de recuperação.
2. Navegue até à **infraestrutura de recuperação de sites** > **servidores de configuração** (em para VMware e máquinas físicas).

  ![Adicionar página de servidores](./media/site-recovery-vmware-to-azure-manage-configuration-server/AddServers.png)
3. Clique em de **+ servidores** botão.
4. No **Adicionar servidor** página, clique no botão de transferência para transferir a chave de registo. Esta chave é necessário durante a instalação do servidor de configuração para o registar no serviço Azure Site Recovery.
5. Clique em de **transferir a configuração do Microsoft Azure Site Recovery Unified** hiperligação para transferir a versão mais recente do servidor de configuração.

  ![Página de transferência](./media/site-recovery-vmware-to-azure-manage-configuration-server/downloadcs.png)

  > [!TIP]
  Pode ser transferida a versão mais recente do servidor de configuração diretamente a partir do [página de transferências da Microsoft Download Center](http://aka.ms/unifiedsetup)

## <a name="installing-and-registering-a-configuration-server-from-gui"></a>Instalar e registar um servidor de configuração de GUI
[!INCLUDE [site-recovery-add-configuration-server](../../includes/site-recovery-add-configuration-server.md)]

## <a name="installing-and-registering-a-configuration-server-using-command-line"></a>Instalar e registar um servidor de configuração utilizando a linha de comandos

  ```
  UnifiedSetup.exe [/ServerMode <CS/PS>] [/InstallDrive <DriveLetter>] [/MySQLCredsFilePath <MySQL credentials file path>] [/VaultCredsFilePath <Vault credentials file path>] [/EnvType <VMWare/NonVMWare>] [/PSIP <IP address to be used for data transfer] [/CSIP <IP address of CS to be registered with>] [/PassphraseFilePath <Passphrase file path>]
  ```

### <a name="sample-usage"></a>Utilização de exemplo
  ```
  MicrosoftAzureSiteRecoveryUnifiedSetup.exe /q /xC:\Temp\Extracted
  cd C:\Temp\Extracted
  UNIFIEDSETUP.EXE /AcceptThirdpartyEULA /servermode "CS" /InstallLocation "D:\" /MySQLCredsFilePath "C:\Temp\MySQLCredentialsfile.txt" /VaultCredsFilePath "C:\Temp\MyVault.vaultcredentials" /EnvType "VMWare"
  ```


### <a name="configuration-server-installer-command-line-arguments"></a>Configuração servidor da linha de comandos os argumentos do instalador.
[!INCLUDE [site-recovery-unified-setup-parameters](../../includes/site-recovery-unified-installer-command-parameters.md)]


### <a name="create-a-mysql-credentials-file"></a>Criar um ficheiro de credenciais MySql
Parâmetro de MySQLCredsFilePath assume um ficheiro como entrada. Crie o ficheiro utilizando o seguinte formato e transmita-o como parâmetro de entrada MySQLCredsFilePath.
```
[MySQLCredentials]
MySQLRootPassword = "Password>"
MySQLUserPassword = "Password"
```
### <a name="create-a-proxy-settings-configuration-file"></a>Criar um ficheiro de configuração de definições de proxy
Parâmetro de ProxySettingsFilePath assume um ficheiro como entrada. Crie o ficheiro utilizando o seguinte formato e transmita-o como parâmetro de entrada ProxySettingsFilePath.

```
[ProxySettings]
ProxyAuthentication = "Yes/No"
Proxy IP = "IP Address"
ProxyPort = "Port"
ProxyUserName="UserName"
ProxyPassword="Password"
```
## <a name="modifying-proxy-settings-for-configuration-server"></a>Modificar definições de proxy do servidor de configuração
1. Iniciar sessão servidor de configuração.
2. Inicie o cspsconfigtool.exe utilizando o atalho no seu.
3. Clique em de **registo de cofre** separador.
4. Transfira um novo ficheiro de registo do cofre do portal e fornecê-lo como entrada para a ferramenta.

  ![servidor de configuração de registo](./media/site-recovery-vmware-to-azure-manage-configuration-server/register-csonfiguration-server.png)
5. Forneça os detalhes novos do servidor Proxy e clique em de **registar** botão.
6. Abra uma janela de comando do PowerShell de administrador.
7. Execute o seguinte comando
  ```
  $pwd = ConvertTo-SecureString -String MyProxyUserPassword
  Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber – ProxyUserName domain\username -ProxyPassword $pwd
  net stop obengine
  net start obengine
  ```

  >[!WARNING]
  Se tiver ligados a este servidor de configuração de servidores de processos de escalamento horizontal, terá de [corrija as definições de proxy de todos os servidores do processo de escalamento horizontal](site-recovery-vmware-to-azure-manage-scaleout-process-server.md#modifying-proxy-settings-for-scale-out-process-server) na sua implementação.

## <a name="modify-user-accounts-and-passwords"></a>Modificar contas de utilizador e palavras-passe

O CSPSConfigTool.exe é utilizado para gerir as contas de utilizador utilizadas para **a deteção automática de máquinas virtuais VMware** e efetuar * * de instalação de emissão do serviço de mobilidade nas máquinas protegidas. 

1. Inicie sessão no seu servidor de configuração.
2. Inicie o CSPSConfigtool.exe ao clicar no atalho disponível no ambiente de trabalho.
3. Clique em de **gerir contas** separador.
4. Selecione a conta para que a palavra-passe tem de ser modificadas e clique em de **editar** botão.
5. Introduza a palavra-passe nova e clique em **OK**


## <a name="re-register-a-configuration-server-with-the-same-recovery-services-vault"></a>Volte a registar um servidor de configuração com o mesmo Cofre de serviços de recuperação
  1. Iniciar sessão servidor de configuração.
  2. Inicie o cspsconfigtool.exe utilizando o atalho no ambiente de trabalho.
  3. Clique em de **registo de cofre** separador.
  4. Transfira um novo ficheiro de registo a partir do portal e fornecê-lo como entrada para a ferramenta.
        ![servidor de configuração de registo](./media/site-recovery-vmware-to-azure-manage-configuration-server/register-csonfiguration-server.png)
  5. Forneça os detalhes do servidor de Proxy e clique em de **registar** botão.  
  6. Abra uma janela de comando do PowerShell de administrador.
  7. Execute o seguinte comando

      ```
      $pwd = ConvertTo-SecureString -String MyProxyUserPassword
      Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber – ProxyUserName domain\username -ProxyPassword $pwd
      net stop obengine
      net start obengine
      ```

  >[!WARNING]
  Se tiver ligados a este servidor de configuração de servidores de processos de escalamento horizontal, terá de [voltar a registar todos os servidores do processo de escalamento horizontal](site-recovery-vmware-to-azure-manage-scaleout-process-server.md#re-registering-a-scale-out-process-server) na sua implementação.

## <a name="registering-a-configuration-server-with-a-different-recovery-services-vault"></a>Registar um servidor de configuração com um cofre de serviços de recuperação diferente.

> [!WARNING]
> O passo seguinte disassociates a configuração a partir do cofre atual e a replicação de todas as máquinas virtuais protegidas sob o servidor de configuração está parada.

1. Iniciar sessão servidor de configuração.
2. a partir de uma linha de comandos de administrador, execute o comando

    ```
    reg delete HKLM\Software\Microsoft\Azure Site Recovery\Registration
    net stop dra
    ```
3. Inicie o cspsconfigtool.exe utilizando o atalho no seu.
4. Clique em de **registo de cofre** separador.
5. Transfira um novo ficheiro de registo a partir do portal e fornecê-lo como entrada para a ferramenta.

    ![servidor de configuração de registo](./media/site-recovery-vmware-to-azure-manage-configuration-server/register-csonfiguration-server.png)
6. Forneça os detalhes do servidor de Proxy e clique em de **registar** botão.  
7. Abra uma janela de comando do PowerShell de administrador.
8. Execute o seguinte comando
    ```
    $pwd = ConvertTo-SecureString -String MyProxyUserPassword
    Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber – ProxyUserName domain\username -ProxyPassword $pwd
    net stop obengine
    net start obengine
    ```

## <a name="upgrading-a-configuration-server"></a>Atualizar um servidor de configuração

> [!WARNING]
> As atualizações são suportadas apenas para a versão de N-4th. Por exemplo, se a versão mais recente no mercado 9.11, em seguida, pode atualizar da versão 9.10, 9.9, 9.8 ou 9.7 diretamente ao 9.11. Mas se estiver em qualquer versão menor ou igual a 9.6 então poderá ter de atualizar para, pelo menos, 9.7 antes de pode aplicar as atualizações mais recentes para o servidor de configuração. Transferir as ligações para a versão anterior pode ser encontrado em [atualizações de serviço do Azure Site Recovery](https://social.technet.microsoft.com/wiki/contents/articles/38544.azure-site-recovery-service-updates.aspx)

1. Transferir o instalador da atualização no seu servidor de configuração.
2. Inicie o instalador, fazendo duplo clique o instalador.
3. O instalador Deteta a versão dos componentes do Site Recovery presentes na máquina e da linha de comandos para uma confirmação. 
4. Clique no botão OK para fornecer a confirmação & prosseguir com a atualização.


## <a name="delete-or-unregister-a-configuration-server"></a>Elimine ou anular o registo de um servidor de configuração

> [!WARNING]
> Certifique-se o seguinte antes de iniciar a desativação do servidor de configuração.
> 1. [Desative a proteção](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure) todas as máquinas virtuais neste servidor de configuração.
> 2. [Desassocie](site-recovery-setup-replication-settings-vmware.md#dissociate-a-configuration-server-from-a-replication-policy) e [eliminar](site-recovery-setup-replication-settings-vmware.md#delete-a-replication-policy) todas as políticas de replicação do servidor de configuração.
> 3. [Eliminar](site-recovery-vmware-to-azure-manage-vCenter.md#delete-a-vcenter-in-azure-site-recovery) todos os anfitriões de servidores/vSphere vCenters que estão associados ao servidor de configuração.


### <a name="delete-the-configuration-server-from-azure-portal"></a>Elimine o servidor de configuração do portal do Azure
1. No portal do Azure, navegue para **infraestrutura de recuperação de Site** > **servidores de configuração** no menu do cofre.
2. Clique no servidor de configuração que pretende desativar.
3. Na página de detalhes do servidor de configuração, clique no botão Eliminar.

  ![servidor de configuração de eliminação](./media/site-recovery-vmware-to-azure-manage-configuration-server/delete-configuration-server.PNG)
4. Clique em **Sim** para confirmar a eliminação do servidor.

### <a name="uninstall-the-configuration-server-software-and-its-dependencies"></a>Desinstalar o software de servidor de configuração e as respetivas dependências
  > [!TIP]
  Se pretender reutilizar o servidor de configuração com o Azure Site Recovery novamente, em seguida, pode avançar para o passo 4 diretamente

1. Inicie sessão no servidor de configuração como administrador.
2. Abra o painel de controlo > programa > desinstalar programas
3. Desinstale os programas na seguinte sequência:
  * Agente dos Serviços de Recuperação do Microsoft Azure
  * Servidor de destino mestre do serviço de mobilidade recuperação Microsoft Azure Site
  * Fornecedor do Microsoft Azure Site Recovery
  * Servidor de processo do servidor de configuração do Microsoft Azure Site Recovery
  * Dependências de servidor de configuração de recuperação de sites de Microsoft Azure
  * Servidor de MySQL 5.5
4. Execute o comando seguinte a partir e a linha de comandos de administrador.
  ```
  reg delete HKLM\Software\Microsoft\Azure Site Recovery\Registration
  ```

## <a name="delete-or-unregister-a-configuration-server-powershell"></a>Elimine ou anular o registo de um servidor de configuração (PowerShell)

1. [Instalar](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-4.4.0) módulo Azure PowerShell
2. Início de sessão na sua conta do Azure utilizando o comando
    
    `Login-AzureRmAccount`
3. Selecione a subscrição em que o Cofre está presente

     `Get-AzureRmSubscription –SubscriptionName <your subscription name> | Select-AzureRmSubscription`
3.  Configurar agora o contexto do Cofre
    
    ```
    $vault = Get-AzureRmRecoveryServicesVault -Name <name of your vault>
    Set-AzureRmSiteRecoveryVaultSettings -ARSVault $vault
    ```
4. Obter selecione o servidor de configuração

    `$fabric = Get-AzureRmSiteRecoveryFabric -FriendlyName <name of your configuration server>`
6. Eliminar o servidor de configuração

    `Remove-AzureRmSiteRecoveryFabric -Fabric $fabric [-Force] `

> [!NOTE]
> O **-forçar** opção em Remove-AzureRmSiteRecoveryFabric pode ser utilizada para forçar a remoção eliminação do servidor de configuração.

## <a name="renew-configuration-server-secure-socket-layerssl-certificates"></a>Renovar certificados de Layer(SSL) de Secure Socket de servidor de configuração
O servidor de configuração tem um servidor Web integrada, o que orquestra as atividades dos servidores de serviço de mobilidade, servidores de processos e destino mestre ligados ao servidor de configuração. Servidor Web do servidor de configuração utiliza um certificado SSL para autenticar os clientes. Este certificado tem uma expiração de três anos e pode ser renovado em qualquer altura utilizando o método seguinte:

> [!WARNING]
Expiração do certificado pode ser efetuada apenas na versão 9.4.XXXX. X ou superior. Atualizar componentes do Azure Site Recovery (servidor de configuração, servidor de processos, servidor de destino mestre, serviço de mobilidade) antes de iniciar o fluxo de trabalho de renovar certificados.

1. No portal do Azure, navegue para o Cofre > infraestrutura de recuperação de Site > servidor de configuração.
2. Clique no servidor de configuração para que precisa renovar o certificado de SSL para.
3. Sob o estado de funcionamento do servidor de configuração, pode ver a data de expiração do certificado SSL.
4. Renovar o certificado clicando a **renovar certificados** ação conforme mostrado na imagem seguinte:

  ![servidor de configuração de eliminação](./media/site-recovery-vmware-to-azure-manage-configuration-server/renew-cert-page.png)

### <a name="secure-socket-layer-certificate-expiry-warning"></a>Aviso de expiração de certificado do Secure Socket Layer

> [!NOTE]
Validade do certificado SSL para todas as instalações que tenham acontecido antes de Maio de 2016 foi definida para um ano. iniciou a ver as notificações de expiração do certificado ser apresentado no portal do Azure.

1. Se o certificado SSL do servidor de configuração está prestes a expirar nos próximos dois meses, o serviço for iniciado notificar os utilizadores através do portal do Azure e correio eletrónico (tem de ser subscritos notificações do Azure Site Recovery). Começar a ver uma faixa de notificação na página de recurso do cofre.

  ![certificado de notificação](./media/site-recovery-vmware-to-azure-manage-configuration-server/ssl-cert-renew-notification.png)
2. Clique na faixa para obter detalhes adicionais sobre a expiração de certificado.

  ![Detalhes do certificado](./media/site-recovery-vmware-to-azure-manage-configuration-server/ssl-cert-expiry-details.png)

  >[!TIP]
  Se, em vez de um **Renovar agora** botão vir um **atualizar agora** botão. O botão Atualizar agora indica que existem alguns componentes no seu ambiente que ainda não tenham sido atualizados para 9.4.xxxx.x ou versões superiores.

## <a name="revive-a-configuration-server-if-the-secure-socket-layer-ssl-certificate-expired"></a>Revive um servidor de configuração se o certificado Secure Socket Layer (SSL) expirou

1. Atualizar o servidor de configuração para o [versão mais recente](http://aka.ms/unifiedinstaller)
2. Se tiver quaisquer servidores de processos de escalamento horizontal, servidores de destino mestre de reativação pós-falha, os servidores de processos de reativação pós-falha atualização-las para a versão mais recente
3. Atualize o serviço de mobilidade em todas as máquinas virtuais protegidas para a versão mais recente.
4. Inicie sessão no servidor de configuração e abra uma linha de comandos com privilégios de administrador.
5. Navegue para a pasta %ProgramData%\ASR\home\svsystems\bin
6. Execute RenewCerts.exe para renovar o certificado SSL no servidor de configuração.
7. Se o processo for bem sucedida, deverá ver a mensagem "a renovação do certificado é êxito"


## <a name="sizing-requirements-for-a-configuration-server"></a>Requisitos de dimensionamento para um servidor de configuração

| **CPU** | **Memória** | **Tamanho da cache do disco** | **Taxa de alteração de dados** | **Máquinas protegidas** |
| --- | --- | --- | --- | --- |
| 8 vCPUs (2 sockets * 4 núcleos @ 2,5 GHz) |16 GB |300 GB |500 GB ou inferior |Replicar menos de 100 máquinas. |
| 12 vCPUs (2 sockets * 6 núcleos @ 2,5 GHz) |18 GB |600 GB |500 GB a 1 TB |Replicar entre 100 150 máquinas. |
| 16 vCPUs (2 sockets * 8 núcleos @ 2,5 GHz) |32 GB |1 TB |1 TB para 2 TB |Replicar entre 150 200 máquinas. |

  >[!TIP]
  Se o fluxo de dados diária excede 2 TB ou que planeia replicar mais de 200 máquinas virtuais, recomenda-se para implementar servidores de processos adicionais para balancear o tráfego de replicação. Saiba mais sobre como implementar o processo de escalamento horizontal servidores.


## <a name="common-issues"></a>Problemas comuns
[!INCLUDE [site-recovery-vmware-to-azure-install-register-issues](../../includes/site-recovery-vmware-to-azure-install-register-issues.md)]

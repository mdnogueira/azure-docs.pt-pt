---
title: " Gerir um servidor de processos de escalamento horizontal no Azure Site Recovery | Microsoft Docs"
description: Este artigo descreve como configurar e gerir um servidor de processos de escalamento horizontal no Azure Site Recovery.
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
ms.date: 06/29/2017
ms.author: anoopkv
ms.openlocfilehash: e5c01de19917235c34c035415df86291b9152bf0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="manage-a-scale-out-process-server"></a>Gerir um servidor de processos de escalamento horizontal

Servidor de processos de escalamento horizontal atua como um coordenador de transferência de dados entre os serviços de recuperação de Site e da sua infraestrutura no local. Este artigo descreve como pode configurar, configurar e gerir um servidor de processos de escalamento horizontal.

## <a name="prerequisites"></a>Pré-requisitos
Seguem-se o hardware recomendado, software e configuração de rede necessários para configurar um servidor de processos de escalamento horizontal.

> [!NOTE]
> [Planeamento da capacidade](site-recovery-capacity-planner.md) é um passo importante para se certificar de que implementar o servidor de processos de escalamento horizontal com uma configuração de conjuntos de que os requisitos de carga. Leia mais sobre [dimensionamento características para um servidor de processos de escalamento horizontal](#sizing-requirements-for-a-configuration-server).

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]

## <a name="downloading-the-scale-out-process-server-software"></a>Transferir o software de servidor de processos de escalamento horizontal
1. Inicie sessão no portal do Azure e navegue para o Cofre de serviços de recuperação.
2. Navegue até à **infraestrutura de recuperação de sites** > **servidores de configuração** (em para VMware e máquinas físicas).
3. Selecione o seu servidor de configuração para desagregar da página de detalhes do servidor de configuração.
4. Clique em de **+ o servidor de processos** botão.
5. No **servidor de processos adicionar** página, selecione **no local do servidor de processos de implementar, escalamento horizontal** opção do **escolha onde pretende implementar o servidor de processos** pendente.

  ![Adicionar página de servidores](./media/site-recovery-vmware-to-azure-manage-scaleout-process-server/add-process-server.png)
6. Clique em de **transferir a configuração do Microsoft Azure Site Recovery Unified** hiperligação para transferir a versão mais recente da instalação do servidor de processos de escalamento horizontal.

  > [!WARNING]
  A versão do seu servidor de processos de escalamento horizontal deve ser igual ou menor que a versão do servidor de configuração em execução no seu ambiente. É uma forma simples para garantir a compatibilidade da versão a utilizar o bits instalador mesmo que utilizaram recentemente para instalar/atualizar o servidor de configuração.

## <a name="installing-and-registering-a-scale-out-process-server-from-gui"></a>Instalar e registar um servidor de processos de escalamento horizontal de GUI
Se tiver para ampliar a implementação para além de 200 máquinas de origem ou uma taxa de rotatividade diário total de mais do que 2 TB, terá de servidores de processos adicionais para processar o volume de tráfego.

Verifique o [tamanho recomendações para servidores de processos](#size-recommendations-for-the-process-server)e, em seguida, siga estas instruções para configurar o servidor de processos. Depois de configurar o servidor, migre as máquinas de origem para utilizá-lo.

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-add-process-server.md)]


## <a name="installing-and-registering-a-scale-out-process-server-using-command-line"></a>Instalar e registar um servidor de processos de escalamento horizontal através da linha de comandos

```
UnifiedSetup.exe [/ServerMode <CS/PS>] [/InstallDrive <DriveLetter>] [/MySQLCredsFilePath <MySQL credentials file path>] [/VaultCredsFilePath <Vault credentials file path>] [/EnvType <VMWare/NonVMWare>] [/PSIP <IP address to be used for data transfer] [/CSIP <IP address of CS to be registered with>] [/PassphraseFilePath <Passphrase file path>]
```

### <a name="sample-usage"></a>Utilização de exemplo
```
MicrosoftAzureSiteRecoveryUnifiedSetup.exe /q /xC:\Temp\Extracted
cd C:\Temp\Extracted
UNIFIEDSETUP.EXE /AcceptThirdpartyEULA /servermode "PS" /InstallLocation "D:\" /EnvType "VMWare" /CSIP "10.150.24.119" /PassphraseFilePath "C:\Users\Administrator\Desktop\Passphrase.txt" /DataTransferSecurePort 443
```

### <a name="scale-out-process-server-installer-command-line-arguments"></a>Escalamento horizontal servidor de processos da linha de comandos os argumentos do instalador.
[!INCLUDE [site-recovery-unified-setup-parameters](../../includes/site-recovery-unified-installer-command-parameters.md)]


### <a name="create-a-proxy-settings-configuration-file"></a>Criar um ficheiro de configuração de definições de proxy
Parâmetro de ProxySettingsFilePath assume um ficheiro como entrada. Crie o ficheiro utilizando o seguinte formato e transmita-o como parâmetro de entrada ProxySettingsFilePath.
```
* [ProxySettings]
* ProxyAuthentication = "Yes/No"
* Proxy IP = "IP Address"
* ProxyPort = "Port"
* ProxyUserName="UserName"
* ProxyPassword="Password"
```
## <a name="modifying-proxy-settings-for-scale-out-process-server"></a>Modificar definições de proxy do servidor de processos de escalamento horizontal
1. Inicie sessão no seu servidor de processos de escalamento horizontal.
2. Abra uma janela de comando do PowerShell de administrador.
3. Execute o seguinte comando
  ```
  $pwd = ConvertTo-SecureString -String MyProxyUserPassword
  Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber –ProxyUserName domain\username -ProxyPassword $pwd
  net stop obengine
  net start obengine
  ```
4. Em seguida, navegue para o diretório **%PROGRAMDATA%\ASR\Agent** e execute o seguinte comando
  ```
  cmd
  cdpcli.exe --registermt

  net stop obengine

  net start obengine

  exit
  ```

## <a name="re-registering-a-scale-out-process-server"></a>Volte a registar um servidor de processos de escalamento horizontal
[!INCLUDE [site-recovery-vmware-register-process-server](../../includes/site-recovery-vmware-register-process-server.md)]

* Em seguida, abra uma linha de comandos de administrador.
* Procure o diretório **%PROGRAMDATA%\ASR\Agent** e execute o comando

```
cdpcli.exe --registermt

net stop obengine

net start obengine
```

## <a name="upgrading-a-scale-out-process-server"></a>Atualizar um servidor de processos de escalamento horizontal
[!INCLUDE [site-recovery-vmware-upgrade -process-server](../../includes/site-recovery-vmware-upgrade-process-server-internal.md)]

## <a name="decommissioning-a-scale-out-process-server"></a>Desativar um servidor de processos de escalamento horizontal
1. Certifique-se de que:
  - Mostra o estado de ligação do servidor de configuração como **ligado** no portal do Azure
  - Servidor de processos é ainda consegue comunicar com o servidor de configuração.
2. Inicie sessão no servidor de processos como um administrador
3. Abra o painel de controlo > programa > desinstalar programas
4. Desinstale os programas na sequência de indicado a seguir:
  * Servidor de processo do servidor de configuração do Microsoft Azure Site Recovery
  * Dependências de servidor de configuração de recuperação de sites de Microsoft Azure
  * Agente dos Serviços de Recuperação do Microsoft Azure

Pode demorar até-15 minutos para que a eliminação do servidor de processos refletir no portal do Azure.

  > [!NOTE]
  Se o servidor de processos não consegue comunicar com o servidor de configuração (estado de ligação no portal estiver desligado), em seguida, tem de seguir os seguintes passos para removê-lo a partir do servidor de configuração.

## <a name="unregistering-a-disconnected-scale-out-process-server-from-a-configuration-server"></a>Anulação do registo do servidor de processos de escalamento horizontal desligado de um servidor de configuração

[!INCLUDE [site-recovery-vmware-upgrade-process-server](../../includes/site-recovery-vmware-unregister-process-server.md)]

## <a name="sizing-requirements-for-a-scale-out-process-server"></a>Requisitos de dimensionamento para um servidor de processos de escalamento horizontal

| **Servidor de processos adicionais** | **Tamanho da cache do disco** | **Taxa de alteração de dados** | **Máquinas protegidas** |
| --- | --- | --- | --- |
|4 vCPUs (2 sockets * 2 núcleos @ GHz 2,5), 8 GB de memória |300 GB |250 GB ou inferior |Replicar máquinas 85 ou menos. |
|8 vCPUs (2 sockets * 4 núcleos @ GHz 2,5), 12 GB de memória |600 GB |250 GB a 1 TB |Replicar entre 85 150 máquinas. |
|12 vCPUs (2 sockets * 6 núcleos @ GHz 2,5) 24 GB de memória |1 TB |1 TB para 2 TB |Replicar entre 150 225 máquinas. |

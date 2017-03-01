---
title: 
description: 
services: site-recovery
documentationcenter: 
author: 
manager: 
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: 
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 
ms.author: 
translationtype: Human Translation
ms.sourcegitcommit: 4871e5959483058aa957e3373ddc5121b398e1d9
ms.openlocfilehash: 1d6d595bf788fc3fdba0bbc98887614e17c791dc


---



### <a name="install-the-mobility-service"></a>Instalar o serviço de Mobilidade
O primeiro passo para ativar a proteção para máquinas virtuais e servidores físicos é instalar o serviço de Mobilidade. Pode fazê-lo de duas formas:

* **Push do servidor de processos**: Quando ativa a replicação numa máquina, emita e instale o componente do serviço de Mobilidade a partir do servidor de processos. 
*Tenha em atenção* que a instalação push não ocorre se as máquinas já estiverem a executar uma versão atualizada do componente.
* **Push empresarial**: Instale o componente automaticamente através do seu processo push empresarial, como o WSUS ou o System Center Configuration Manager, ou a [Automatização do Azure e a configuração de Estado Pretendido](site-recovery-automate-mobility-service-install.md). Configure o servidor de configuração antes de realizar este procedimento.
* **Instalação manual**: Instale o componente manualmente em cada máquina que quer replicar. Configure o servidor de configuração antes de realizar este procedimento.

#### <a name="prepare-for-automatic-push-on-windows-machines"></a>Preparar para push automático em máquinas do Windows
Eis como preparar máquinas do Windows para que o serviço de Mobilidade possa ser instalado automaticamente pelo servidor de processos.

1. Crie uma conta que possa ser utilizada pelo servidor de processos para aceder à máquina. A conta deve ter privilégios de administrador (locais ou de domínio) e ser apenas utilizada para a instalação push.

   > [!NOTE]
   > Se não estiver a utilizar uma conta de domínio, terá de desativar o Controlo de Acesso de Utilizador Remoto na máquina local. Para fazer isto, no registo em HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System, adicione a entrada DWORD LocalAccountTokenFilterPolicy com um valor de 1. Para adicionar a entrada de registo a partir de um tipo CLI **`REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1`**.
   >
   >
2. Na Firewall do Windows da máquina que quer proteger, selecione **Permitir uma aplicação ou funcionalidade através da Firewall**. Ative a **Partilha de Ficheiros e Impressoras** e a **Windows Management Instrumentation**. Para as máquinas que pertençam a um domínio, pode configurar as definições de firewall com um GPO.

   ![Definições de firewall](./media/site-recovery-vmware-to-azure/mobility1.png)
3. Adicione a conta que criou:

   * Abra o **cspsconfigtool**. Está disponível como um atalho no ambiente de trabalho e encontra-se na pasta [LOCALIZAÇÃO DA INSTALAÇÃO]\home\svsystems\bin.
   * No separador **Gerir Contas**, clique em **Adicionar Conta**.
   * Adicione a conta que criou. Depois de adicionar a conta, tem de fornecer as credenciais quando ativar a replicação para uma máquina.

#### <a name="prepare-for-automatic-push-on-linux-servers"></a>Preparar para push automático em servidores do Linux
1. Certifique-se de que a máquina do Linux que quer proteger é suportada conforme descrito em [pré-requisitos de máquina protegida](#protected-machine-prerequisites). Certifique-se de que há conectividade de rede entre a máquina do Linux e o servidor de processos.
2. Crie uma conta que possa ser utilizada pelo servidor de processos para aceder à máquina. A conta deve ser um utilizador raiz no servidor do Linux de origem e apenas ser utilizada para a instalação push.

   * Abra o **cspsconfigtool**. Está disponível como um atalho no ambiente de trabalho e encontra-se na pasta [LOCALIZAÇÃO DA INSTALAÇÃO]\home\svsystems\bin.
   * No separador **Gerir Contas**, clique em **Adicionar Conta**.
   * Adicione a conta que criou. Depois de adicionar a conta, tem de fornecer as credenciais quando ativar a replicação para uma máquina.
3. Verifique se o ficheiro /etc/hosts no servidor do Linux de origem contém entradas que mapeiam o nome de anfitrião local para endereços IP associados a todos os adaptadores de rede.
4. Instale os openssh, openssh-server, pacotes openssl mais recentes na máquina que quer replicar.
5. Certifique-se de que o SSH está ativado e em execução na porta 22.
6. Ative a autenticação de subsistema e palavra-passe SFTP no ficheiro sshd_config da seguinte forma:

   * Inicie sessão como raiz.
   * No ficheiro /etc/ssh/sshd_config, encontre a linha que começa por **PasswordAuthentication**.
   * Remova os comentários da linha e altere o valor de **não** para **sim**.
   * Encontre a linha que começa por **Subsystem** e remova os comentários da mesma.

     ![Linux](./media/site-recovery-vmware-to-azure/mobility2.png)

### <a name="install-the-mobility-service-manually"></a>Instalar o Serviço de Mobilidade manualmente
Os programas de instalação estão disponíveis no servidor de Configuração em **C:\Program Files (x86)\Microsoft Azure Site Recovery\home\svsystems\pushinstallsvc\repository**.

| Sistema operativo de origem | Ficheiro de instalação do serviço de Mobilidade |
| --- | --- |
| Windows Server (apenas&64; bits) |Microsoft-ASR_UA_9.*.0.0_Windows_* release.exe |
| CentOS 6.4, 6.5, 6.6 (apenas 64 bits) |Microsoft-ASR_UA_9.*.0.0_RHEL6-64_*release.tar.gz |
| SUSE Linux Enterprise Server 11 SP3 (apenas 64 bits) |Microsoft-ASR_UA_9.*.0.0_SLES11-SP3-64_*release.tar.gz |
| Oracle Enterprise Linux 6.4, 6.5 (apenas 64 bits) |Microsoft-ASR_UA_9.*.0.0_OL6-64_*release.tar.gz |

#### <a name="install-mobility-service-on-a-windows-server"></a>Instalar o Serviço de Mobilidade num Windows Server
1. Transfira e execute o instalador relevante.
2. Em **Antes de começar**, selecione **Serviço de Mobilidade**.

    ![Serviço de Mobilidade](./media/site-recovery-vmware-to-azure/mobility3.png)
3. Em **Detalhes do Servidor de Configuração**, especifique o endereço IP do servidor de configuração e a frase de acesso que foi gerada quando executou a Configuração Unificada. Para obter a frase de acesso, execute: ** <SiteRecoveryInstallationFolder>\home\sysystems\bin\genpassphrase.exe –v** no servidor de configuração.

    ![Serviço de Mobilidade](./media/site-recovery-vmware-to-azure/mobility6.png)
4. Em **Localização da Instalação**, deixe a predefinição e clique em **Seguinte** para iniciar a instalação.
5. Em **Progresso da Instalação**, monitorize a instalação e reinicie a máquina se lhe for solicitado. Depois de instalar o serviço, pode demorar cerca de 15 minutos até que o estado seja atualizado no portal.

#### <a name="install-mobility-service-on-a-windows-server-using-the-command-prompt"></a>Instalar o Serviço de Mobilidade num servidor do Windows através da Linha de comandos
1. Copie o instalador para uma pasta local (por exemplo, C:\Temp) no servidor que quer proteger. O instalador pode ser encontrado no servidor de configuração, em **[Localização da Instalação]\home\svsystems\pushinstallsvc\repository**. O pacote para os Sistemas Operativos Windows tem um nome semelhante a Microsoft-ASR_UA_9.3.0.0_Windows_GA_17thAug2016_release.exe
2. Mude o nome deste ficheiro para MobilitySvcInstaller.exe
3. Execute o seguinte comando para extrair o instalador MSI:

    ``C:\> cd C:\Tempww
    ``C:\Temp> MobilitySvcInstaller.exe /q /xC:\Temp\Extracted``
    ``C:\Temp> cd Extracted``
    ``C:\Temp\Extracted> UnifiedAgent.exe /Role "Agente" /CSEndpoint "Endereço IP do Servidor de Configuração" /PassphraseFilePath <Full path to the passphrase file>``

##### <a name="full-command-line-syntax"></a>Sintaxe completa da linha de comandos
    UnifiedAgent.exe [/Role <Agent/MasterTarget>] [/InstallLocation <Installation Directory>] [/CSIP <IP address of CS to be registered with>] [/PassphraseFilePath <Passphrase file path>] [/LogFilePath <Log File Path>]<br/>

**Parâmetros**

* **/Role:** Obrigatório. Especifica se o serviço de Mobilidade deve ser instalado. Valores de entrada Agente|DestinoPrincipal
* **/InstallLocation:** Obrigatório. Especifica onde instalar o serviço.
* **/PassphraseFilePath:** Obrigatório. A frase de acesso do servidor de configuração.
* **/LogFilePath:** Obrigatório. Localização onde os ficheiros de registo da instalação devem ser criados.

#### <a name="uninstall-the-mobility-service-manually"></a>Desinstalar o serviço de Mobilidade manualmente
Pode desinstalar o serviço de mobilidade através da opção Adicionar/Remover Programas no Painel de Controlo ou desta instrução da linha de comandos: MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1}

#### <a name="install-the-mobility-service-on-a-linux-server"></a>Instalar o Serviço de Mobilidade num servidor do Linux
1. Copie o arquivo tar adequado, com base na tabela acima, para a máquina do Linux que quer replicar.
2. Abra um programa shell e extraia o arquivo tar zipado para um caminho local ao executar: `tar -xvzf Microsoft-ASR_UA_8.5.0.0*`
3. Crie um ficheiro passphrase.txt no diretório local para onde extraiu o conteúdo do arquivo tar. Para isso, copie a frase de acesso em C:\ProgramData\Microsoft Azure Site Recovery\private\connection.passphrase no servidor de configuração e guarde-a no passphrase.txt ao executar *`echo <passphrase> >passphrase.txt`* na shell.
4. Instale o serviço de Mobilidade ao executar *`sudo ./install -t both -a host -R Agent -d /usr/local/ASR -i <IP address> -p <port> -s y -c https -P passphrase.txt`*.
5. Especifique o endereço IP interno do servidor de configuração e certifique-se de que a porta 443 está selecionada. Depois de instalar o serviço, pode demorar cerca de 15 minutos até que o estado seja atualizado no portal.

**Também pode instalar a partir da linha de comandos**:

Copie a frase de acesso em C:\Program Files (x86)\InMage Systems\private\connection no servidor de configuração e guarde-a como "passphrase.txt" no servidor de configuração. Em seguida, execute estes comandos. No nosso exemplo, o endereço IP do servidor de configuração é 104.40.75.37 e a porta HTTPS deve ser 443:


Para instalar num servidor de produção:

    ./install -t both -a host -R Agent -d /usr/local/ASR -i 104.40.75.37 -p 443 -s y -c https -P passphrase.txt

Para instalar no servidor de destino principal:

    ./install -t both -a host -R MasterTarget -d /usr/local/ASR -i 104.40.75.37 -p 443 -s y -c https -P passphrase.txt






<!--HONumber=Feb17_HO3-->



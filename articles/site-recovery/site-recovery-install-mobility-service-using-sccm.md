---
title: "Automatizar a instalação do serviço de mobilidade para o Azure Site Recovery utilizando ferramentas de implementação de software | Microsoft Docs"
description: "Este artigo ajuda-o a automatizar a instalação do serviço de mobilidade através da utilização de ferramentas de implementação de software, como o System Center Configuration Manager."
services: site-recovery
documentationcenter: 
author: AnoopVasudavan
manager: gauravd
editor: 
ms.assetid: 
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/29/2017
ms.author: anoopkv
ms.openlocfilehash: cfcc20b55b9e9b662ea8eec0802727b9a5a0ebf0
ms.sourcegitcommit: 5bced5b36f6172a3c20dbfdf311b1ad38de6176a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/27/2017
---
# <a name="automate-mobility-service-installation-by-using-software-deployment-tools"></a>Automatizar a instalação do serviço de mobilidade através da utilização de ferramentas de implementação de software

>[!IMPORTANT]
Este documento assume que está a utilizar versão **9.9.4510.1** ou superior.

Este artigo fornece um exemplo de como pode utilizar o System Center Configuration Manager para implementar o serviço de mobilidade do Azure Site Recovery no seu centro de dados. Utilizar uma ferramenta de implementação de software, tal como o Configuration Manager possui as seguintes vantagens:
* Agendamento da implementação de novas instalações e atualizações, durante a janela de manutenção planeada para atualizações de software
* Dimensionamento de implementação para centenas de servidores em simultâneo


> [!NOTE]
> Este artigo utiliza o System Center Configuration Manager 2012 R2 para demonstrar a atividade de implementação. Também pode automatizar a instalação do serviço de mobilidade utilizando [automatização do Azure e a configuração de estado pretendido](site-recovery-automate-mobility-service-install.md).

## <a name="prerequisites"></a>Pré-requisitos
1. Uma implementação ferramenta de software, como o Configuration Manager, que já está implementada no seu ambiente.
  Criar dois [coleções de dispositivos](https://technet.microsoft.com/library/gg682169.aspx), um para todos os **servidores Windows**e outra para todos os **servidores Linux**, que pretende proteger os utilizando a recuperação de Site.
3. Um servidor de configuração que já está registado com a recuperação de Site.
4. Uma rede segura partilha de ficheiros (partilha Server Message Block) que pode ser acedida pelo servidor do Configuration Manager.

## <a name="deploy-mobility-service-on-computers-running-windows"></a>Implementar o serviço de mobilidade em computadores com Windows
> [!NOTE]
> Este artigo assume que o endereço IP do servidor de configuração está 192.168.3.121, e que a partilha de ficheiros de rede segura \\\ContosoSecureFS\MobilityServiceInstallers.

### <a name="step-1-prepare-for-deployment"></a>Passo 1: Preparar para a implementação
1. Crie uma pasta na partilha de rede e dê-lhe nome **MobSvcWindows**.
2. Inicie sessão no seu servidor de configuração e, abra uma linha de comandos administrativa.
3. Execute os seguintes comandos para gerar um ficheiro de frase de acesso:

    `cd %ProgramData%\ASR\home\svsystems\bin`

    `genpassphrase.exe -v > MobSvc.passphrase`
4. Copiar o **MobSvc.passphrase** de ficheiros para o **MobSvcWindows** pasta na partilha de rede.
5. Navegue para o repositório de instalador no servidor de configuração executando o seguinte comando:

   `cd %ProgramData%\ASR\home\svsystems\puhsinstallsvc\repository`

6. Copiar o  **Microsoft ASR\_UA\_*versão*\_Windows\_GA\_*data* \_Release.exe** para o **MobSvcWindows** pasta na partilha de rede.
7. Copie o seguinte código e guarde-o como **install.bat** para o **MobSvcWindows** pasta.

   > [!NOTE]
   > Substitua os marcadores de posição [CSIP] neste script dos valores reais do endereço IP do seu servidor de configuração.

```DOS
Time /t >> C:\Temp\logfile.log
REM ==================================================
REM ==== Clean up the folders ========================
RMDIR /S /q %temp%\MobSvc
MKDIR %Temp%\MobSvc
MKDIR C:\Temp
REM ==================================================

REM ==== Copy new files ==============================
COPY M*.* %Temp%\MobSvc
CD %Temp%\MobSvc
REN Micro*.exe MobSvcInstaller.exe
REM ==================================================

REM ==== Extract the installer =======================
MobSvcInstaller.exe /q /x:%Temp%\MobSvc\Extracted
REM ==== Wait 10s for extraction to complete =========
TIMEOUT /t 10
REM =================================================

REM ==== Perform installation =======================
REM =================================================

CD %Temp%\MobSvc\Extracted
whoami >> C:\Temp\logfile.log
SET PRODKEY=HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Uninstall
REG QUERY %PRODKEY%\{275197FC-14FD-4560-A5EB-38217F80CBD1}
IF NOT %ERRORLEVEL% EQU 0 (
    echo "Product is not installed. Goto INSTALL." >> C:\Temp\logfile.log
    GOTO :INSTALL
) ELSE (
    echo "Product is installed." >> C:\Temp\logfile.log

    echo "Checking for Post-install action status." >> C:\Temp\logfile.log
    GOTO :POSTINSTALLCHECK
)

:POSTINSTALLCHECK
    REG QUERY "HKLM\SOFTWARE\Wow6432Node\InMage Systems\Installed Products\5" /v "PostInstallActions" | Find "Succeeded"
    If %ERRORLEVEL% EQU 0 (
        echo "Post-install actions succeeded. Checking for Configuration status." >> C:\Temp\logfile.log
        GOTO :CONFIGURATIONCHECK
    ) ELSE (
        echo "Post-install actions didn't succeed. Goto INSTALL." >> C:\Temp\logfile.log
        GOTO :INSTALL
    )

:CONFIGURATIONCHECK
    REG QUERY "HKLM\SOFTWARE\Wow6432Node\InMage Systems\Installed Products\5" /v "AgentConfigurationStatus" | Find "Succeeded"
    If %ERRORLEVEL% EQU 0 (
        echo "Configuration has succeeded. Goto UPGRADE." >> C:\Temp\logfile.log
        GOTO :UPGRADE
    ) ELSE (
        echo "Configuration didn't succeed. Goto CONFIGURE." >> C:\Temp\logfile.log
        GOTO :CONFIGURE
    )


:INSTALL
    echo "Perform installation." >> C:\Temp\logfile.log
    UnifiedAgent.exe /Role MS /InstallLocation "C:\Program Files (x86)\Microsoft Azure Site Recovery" /Platform "VmWare" /Silent
    IF %ERRORLEVEL% EQU 0 (
        echo "Installation has succeeded." >> C:\Temp\logfile.log
        (GOTO :CONFIGURE)
    ) ELSE (
        echo "Installation has failed." >> C:\Temp\logfile.log
        GOTO :ENDSCRIPT
    )

:CONFIGURE
    echo "Perform configuration." >> C:\Temp\logfile.log
    cd "C:\Program Files (x86)\Microsoft Azure Site Recovery\agent"
    UnifiedAgentConfigurator.exe  /CSEndPoint "[CSIP]" /PassphraseFilePath %Temp%\MobSvc\MobSvc.passphrase
    IF %ERRORLEVEL% EQU 0 (
        echo "Configuration has succeeded." >> C:\Temp\logfile.log
    ) ELSE (
        echo "Configuration has failed." >> C:\Temp\logfile.log
    )
    GOTO :ENDSCRIPT

:UPGRADE
    echo "Perform upgrade." >> C:\Temp\logfile.log
    UnifiedAgent.exe /Platform "VmWare" /Silent
    IF %ERRORLEVEL% EQU 0 (
        echo "Upgrade has succeeded." >> C:\Temp\logfile.log
    ) ELSE (
        echo "Upgrade has failed." >> C:\Temp\logfile.log
    )
    GOTO :ENDSCRIPT

:ENDSCRIPT
    echo "End of script." >> C:\Temp\logfile.log


```

### <a name="step-2-create-a-package"></a>Passo 2: Criar um pacote

1. Inicie sessão consola do Configuration Manager.
2. Navegue até à **biblioteca de Software** > **gestão de aplicações** > **pacotes**.
3. Clique com botão direito **pacotes**e selecione **criar pacote**.
4. Fornece valores para o nome, descrição, fabricante, idioma e a versão.
5. Selecione o **este pacote contém ficheiros de origem** caixa de verificação.
6. Clique em **procurar**e selecione a partilha de rede onde está armazenado o instalador (\\\ContosoSecureFS\MobilityServiceInstaller\MobSvcWindows).

  ![Assistente de captura de ecrã de criar pacote e programa](./media/site-recovery-install-mobility-service-using-sccm/create_sccm_package.png)

7. No **escolha o tipo de programa que pretende criar** página, selecione **programa padrão**e clique em **seguinte**.

  ![Assistente de captura de ecrã de criar pacote e programa](./media/site-recovery-install-mobility-service-using-sccm/sccm-standard-program.png)

8. No **especifique informações sobre este programa padrão** página, forneça as seguintes entradas e clique em **seguinte**. (As entradas de outras podem utilizar os respetivos valores predefinidos.)

  | **Nome do parâmetro** | **Valor** |
  |--|--|
  | Nome | Instalar o serviço de mobilidade Microsoft Azure (Windows) |
  | Linha de comandos | Install.bat |
  | Programa pode ser executado | Se pretende ou não um utilizador tenha iniciado sessão |

  ![Assistente de captura de ecrã de criar pacote e programa](./media/site-recovery-install-mobility-service-using-sccm/sccm-program-properties.png)

9. Na página seguinte, selecione os sistemas operativos de destino. Serviço de mobilidade pode ser instalado apenas no Windows Server 2012 R2, Windows Server 2012 e Windows Server 2008 R2.

  ![Assistente de captura de ecrã de criar pacote e programa](./media/site-recovery-install-mobility-service-using-sccm/sccm-program-properties-page2.png)

10. Para concluir o assistente, clique em **seguinte** duas vezes.


> [!NOTE]
> O script suporta as novas instalações de agentes do serviço de mobilidade e atualizações para os agentes que já estão instalados.

### <a name="step-3-deploy-the-package"></a>Passo 3: Implementar o pacote
1. Na consola do Configuration Manager, o pacote com o botão direito e selecione **distribuir conteúdo**.
  ![Consola de captura de ecrã do Configuration Manager](./media/site-recovery-install-mobility-service-using-sccm/sccm_distribute.png)
2. Selecione o  **[pontos de distribuição](https://technet.microsoft.com/library/gg712321.aspx#BKMK_PlanForDistributionPoints)**  para que os pacotes devem ser copiados.
3. Conclua o assistente. O pacote, em seguida, começa a replicar para pontos de distribuição especificados.
4. Depois de terminar a distribuição do pacote, o pacote com o botão direito e selecione **implementar**.
  ![Consola de captura de ecrã do Configuration Manager](./media/site-recovery-install-mobility-service-using-sccm/sccm_deploy.png)
5. Selecione a coleção de dispositivos do Windows Server como a coleção de destino para implementação que criou na secção pré-requisitos.

  ![Assistente de captura de ecrã da implementação de Software](./media/site-recovery-install-mobility-service-using-sccm/sccm-select-target-collection.png)

6. No **especificar o destino do conteúdo** página, selecione o **pontos de distribuição**.
7. No **especificar definições para controlar a forma como este software é implementado** página, certifique-se de que o objetivo é **necessário**.

  ![Assistente de captura de ecrã da implementação de Software](./media/site-recovery-install-mobility-service-using-sccm/sccm-deploy-select-purpose.png)

8. No **especifique o agendamento para esta implementação** página, especifique uma agenda. Para obter mais informações, consulte [agendamento pacotes](https://technet.microsoft.com/library/gg682178.aspx).
9. No **pontos de distribuição** página, configure as propriedades de acordo com as necessidades do seu centro de dados. Em seguida, conclua o assistente.

> [!TIP]
> Para evitar reinícios desnecessários, agende a instalação do pacote durante a janela de manutenção mensal ou janela de atualizações de software.

Pode monitorizar o progresso da implementação através da consola do Configuration Manager. Aceda a **monitorização** > **implementações** > *[nome do seu pacote]*.

  ![Opção de captura de ecrã do Configuration Manager para monitorizar implementações](./media/site-recovery-install-mobility-service-using-sccm/report.PNG)

## <a name="deploy-mobility-service-on-computers-running-linux"></a>Implementar o serviço de mobilidade em computadores com Linux
> [!NOTE]
> Este artigo assume que o endereço IP do servidor de configuração está 192.168.3.121, e que a partilha de ficheiros de rede segura \\\ContosoSecureFS\MobilityServiceInstallers.

### <a name="step-1-prepare-for-deployment"></a>Passo 1: Preparar para a implementação
1. Crie uma pasta na partilha de rede e atribua-o como o nome **MobSvcLinux**.
2. Inicie sessão no seu servidor de configuração e, abra uma linha de comandos administrativa.
3. Execute os seguintes comandos para gerar um ficheiro de frase de acesso:

    `cd %ProgramData%\ASR\home\svsystems\bin`

    `genpassphrase.exe -v > MobSvc.passphrase`
4. Copiar o **MobSvc.passphrase** de ficheiros para o **MobSvcLinux** pasta na partilha de rede.
5. Navegue para o repositório de instalador no servidor de configuração ao executar o comando:

   `cd %ProgramData%\ASR\home\svsystems\puhsinstallsvc\repository`

6. Copie os seguintes ficheiros para o **MobSvcLinux** pasta na partilha de rede:
   * Microsoft-ASR\_UA\*RHEL6 64*release.tar.gz
   * Microsoft-ASR\_UA\*RHEL7-64\*release.tar.gz
   * Microsoft-ASR\_UA\*SLES11-SP3-64\*release.tar.gz
   * Microsoft-ASR\_UA\*SLES11-SP4-64\*release.tar.gz
   * Microsoft-ASR\_UA\*OL6-64\*release.tar.gz
   * Microsoft-ASR\_UA\*UBUNTU 14.04-64\*release.tar.gz


7. Copie o seguinte código e guarde-o como **install_linux.sh** para o **MobSvcLinux** pasta.
   > [!NOTE]
   > Substitua os marcadores de posição [CSIP] neste script dos valores reais do endereço IP do seu servidor de configuração.

```Bash
#!/usr/bin/env bash

rm -rf /tmp/MobSvc
mkdir -p /tmp/MobSvc
INSTALL_DIR='/usr/local/ASR'
VX_VERSION_FILE='/usr/local/.vx_version'

echo "=============================" >> /tmp/MobSvc/sccm.log
echo `date` >> /tmp/MobSvc/sccm.log
echo "=============================" >> /tmp/MobSvc/sccm.log

if [ -f /etc/oracle-release ] && [ -f /etc/redhat-release ]; then
    if grep -q 'Oracle Linux Server release 6.*' /etc/oracle-release; then
        if uname -a | grep -q x86_64; then
            OS="OL6-64"
            echo $OS >> /tmp/MobSvc/sccm.log
            cp *OL6*.tar.gz /tmp/MobSvc
        fi
    fi
elif [ -f /etc/redhat-release ]; then
    if grep -q 'Red Hat Enterprise Linux Server release 6.* (Santiago)' /etc/redhat-release || \
        grep -q 'CentOS Linux release 6.* (Final)' /etc/redhat-release || \
        grep -q 'CentOS release 6.* (Final)' /etc/redhat-release; then
        if uname -a | grep -q x86_64; then
            OS="RHEL6-64"
            echo $OS >> /tmp/MobSvc/sccm.log
            cp *RHEL6*.tar.gz /tmp/MobSvc
        fi
    elif grep -q 'Red Hat Enterprise Linux Server release 7.* (Maipo)' /etc/redhat-release || \
        grep -q 'CentOS Linux release 7.* (Core)' /etc/redhat-release; then
        if uname -a | grep -q x86_64; then
            OS="RHEL7-64"
            echo $OS >> /tmp/MobSvc/sccm.log
            cp *RHEL7*.tar.gz /tmp/MobSvc
                fi
    fi
elif [ -f /etc/SuSE-release ] && grep -q 'VERSION = 11' /etc/SuSE-release; then
    if grep -q "SUSE Linux Enterprise Server 11" /etc/SuSE-release && grep -q 'PATCHLEVEL = 3' /etc/SuSE-release; then
        if uname -a | grep -q x86_64; then
            OS="SLES11-SP3-64"
            echo $OS >> /tmp/MobSvc/sccm.log
            cp *SLES11-SP3*.tar.gz /tmp/MobSvc
        fi
    elif grep -q "SUSE Linux Enterprise Server 11" /etc/SuSE-release && grep -q 'PATCHLEVEL = 4' /etc/SuSE-release; then
        if uname -a | grep -q x86_64; then
            OS="SLES11-SP4-64"
            echo $OS >> /tmp/MobSvc/sccm.log
            cp *SLES11-SP4*.tar.gz /tmp/MobSvc
        fi
    fi
elif [ -f /etc/lsb-release ] ; then
    if grep -q 'DISTRIB_RELEASE=14.04' /etc/lsb-release ; then
       if uname -a | grep -q x86_64; then
           OS="UBUNTU-14.04-64"
           echo $OS >> /tmp/MobSvc/sccm.log
           cp *UBUNTU-14*.tar.gz /tmp/MobSvc
       fi
    fi
else
    exit 1
fi

if [ -z "$OS" ]; then
    exit 1
fi

Install()
{
    echo "Perform Installation." >> /tmp/MobSvc/sccm.log
    ./install -q -d ${INSTALL_DIR} -r MS -v VmWare
    RET_VAL=$?
    echo "Installation Returncode: $RET_VAL" >> /tmp/MobSvc/sccm.log
    if [ $RET_VAL -eq 0 ]; then
        echo "Installation has succeeded. Proceed to configuration." >> /tmp/MobSvc/sccm.log
        Configure
    else
        echo "Installation has failed." >> /tmp/MobSvc/sccm.log
        exit $RET_VAL
    fi
}

Configure()
{
    echo "Perform configuration." >> /tmp/MobSvc/sccm.log
    ${INSTALL_DIR}/Vx/bin/UnifiedAgentConfigurator.sh -i [CSIP] -P MobSvc.passphrase
    RET_VAL=$?
    echo "Configuration Returncode: $RET_VAL" >> /tmp/MobSvc/sccm.log
    if [ $RET_VAL -eq 0 ]; then
        echo "Configuration has succeeded." >> /tmp/MobSvc/sccm.log
    else
        echo "Configuration has failed." >> /tmp/MobSvc/sccm.log
        exit $RET_VAL
    fi
}

Upgrade()
{
    echo "Perform Upgrade." >> /tmp/MobSvc/sccm.log
    ./install -q -v VmWare
    RET_VAL=$?
    echo "Upgrade Returncode: $RET_VAL" >> /tmp/MobSvc/sccm.log
    if [ $RET_VAL -eq 0 ]; then
        echo "Upgrade has succeeded." >> /tmp/MobSvc/sccm.log
    else
        echo "Upgrade has failed." >> /tmp/MobSvc/sccm.log
        exit $RET_VAL
    fi
}

cp MobSvc.passphrase /tmp/MobSvc
cd /tmp/MobSvc

tar -zxvf *.tar.gz

if [ -e ${VX_VERSION_FILE} ]; then
    echo "${VX_VERSION_FILE} exists. Checking for configuration status." >> /tmp/MobSvc/sccm.log
    agent_configuration=$(grep ^AGENT_CONFIGURATION_STATUS "${VX_VERSION_FILE}" | cut -d"=" -f2 | tr -d " ")
    echo "agent_configuration=$agent_configuration" >> /tmp/MobSvc/sccm.log
     if [ "$agent_configuration" == "Succeeded" ]; then
        echo "Agent is already configured. Proceed to Upgrade." >> /tmp/MobSvc/sccm.log
        Upgrade
    else
        echo "Agent is not configured. Proceed to Configure." >> /tmp/MobSvc/sccm.log
        Configure
    fi
else
    Install
fi

cd /tmp

```

### <a name="step-2-create-a-package"></a>Passo 2: Criar um pacote

1. Inicie sessão consola do Configuration Manager.
2. Navegue até à **biblioteca de Software** > **gestão de aplicações** > **pacotes**.
3. Clique com botão direito **pacotes**e selecione **criar pacote**.
4. Fornece valores para o nome, descrição, fabricante, idioma e a versão.
5. Selecione o **este pacote contém ficheiros de origem** caixa de verificação.
6. Clique em **procurar**e selecione a partilha de rede onde está armazenado o instalador (\\\ContosoSecureFS\MobilityServiceInstaller\MobSvcLinux).

  ![Assistente de captura de ecrã de criar pacote e programa](./media/site-recovery-install-mobility-service-using-sccm/create_sccm_package-linux.png)

7. No **escolha o tipo de programa que pretende criar** página, selecione **programa padrão**e clique em **seguinte**.

  ![Assistente de captura de ecrã de criar pacote e programa](./media/site-recovery-install-mobility-service-using-sccm/sccm-standard-program.png)

8. No **especifique informações sobre este programa padrão** página, forneça as seguintes entradas e clique em **seguinte**. (As entradas de outras podem utilizar os respetivos valores predefinidos.)

    | **Nome do parâmetro** | **Valor** |
  |--|--|
  | Nome | Instalar o serviço de mobilidade Microsoft Azure (Linux) |
  | Linha de comandos | ./install_linux.SH |
  | Programa pode ser executado | Se pretende ou não um utilizador tenha iniciado sessão |

  ![Assistente de captura de ecrã de criar pacote e programa](./media/site-recovery-install-mobility-service-using-sccm/sccm-program-properties-linux.png)

9. Na página seguinte, selecione **este programa pode ser executado em qualquer plataforma**.
  ![Assistente de captura de ecrã de criar pacote e programa](./media/site-recovery-install-mobility-service-using-sccm/sccm-program-properties-page2-linux.png)

10. Para concluir o assistente, clique em **seguinte** duas vezes.

> [!NOTE]
> O script suporta as novas instalações de agentes do serviço de mobilidade e atualizações para os agentes que já estão instalados.

### <a name="step-3-deploy-the-package"></a>Passo 3: Implementar o pacote
1. Na consola do Configuration Manager, o pacote com o botão direito e selecione **distribuir conteúdo**.
  ![Consola de captura de ecrã do Configuration Manager](./media/site-recovery-install-mobility-service-using-sccm/sccm_distribute.png)
2. Selecione o  **[pontos de distribuição](https://technet.microsoft.com/library/gg712321.aspx#BKMK_PlanForDistributionPoints)**  para que os pacotes devem ser copiados.
3. Conclua o assistente. O pacote, em seguida, começa a replicar para pontos de distribuição especificados.
4. Depois de terminar a distribuição do pacote, o pacote com o botão direito e selecione **implementar**.
  ![Consola de captura de ecrã do Configuration Manager](./media/site-recovery-install-mobility-service-using-sccm/sccm_deploy.png)
5. Selecione a coleção de dispositivos de servidor Linux que criou na secção pré-requisitos, como a coleção de destino para implementação.

  ![Assistente de captura de ecrã da implementação de Software](./media/site-recovery-install-mobility-service-using-sccm/sccm-select-target-collection-linux.png)

6. No **especificar o destino do conteúdo** página, selecione o **pontos de distribuição**.
7. No **especificar definições para controlar a forma como este software é implementado** página, certifique-se de que o objetivo é **necessário**.

  ![Assistente de captura de ecrã da implementação de Software](./media/site-recovery-install-mobility-service-using-sccm/sccm-deploy-select-purpose.png)

8. No **especifique o agendamento para esta implementação** página, especifique uma agenda. Para obter mais informações, consulte [agendamento pacotes](https://technet.microsoft.com/library/gg682178.aspx).
9. No **pontos de distribuição** página, configure as propriedades de acordo com as necessidades do seu centro de dados. Em seguida, conclua o assistente.

Serviço de mobilidade obtém instalado numa coleção de dispositivos de servidor Linux, de acordo com a agenda configurada.

## <a name="other-methods-to-install-mobility-service"></a>Outros métodos para instalar o serviço de mobilidade
Seguem-se algumas outras opções para instalar o serviço de mobilidade:
* [Instalação manual utilizando o GUI](http://aka.ms/mobsvcmanualinstall)
* [Instalação manual através da linha de comandos](http://aka.ms/mobsvcmanualinstallcli)
* [Instalação de push utilizando o servidor de configuração](http://aka.ms/pushinstall)
* [Instalação automática através da automatização do Azure e a configuração de estado pretendido](http://aka.ms/mobsvcdscinstall)

## <a name="uninstall-mobility-service"></a>Desinstale o serviço de mobilidade
Pode criar pacotes do Configuration Manager para desinstalar o serviço de mobilidade. Utilize o seguinte script para o fazer:

```
Time /t >> C:\logfile.log
REM ==================================================
REM ==== Check if Mob Svc is already installed =======
REM ==== If not installed no operation required ========
REM ==== Else run uninstall command =====================
REM ==== {275197FC-14FD-4560-A5EB-38217F80CBD1} is ====
REM ==== guid for Mob Svc Installer ====================
whoami >> C:\logfile.log
NET START | FIND "InMage Scout Application Service"
IF  %ERRORLEVEL% EQU 1 (GOTO :INSTALL) ELSE GOTO :UNINSTALL
:NOOPERATION
                echo "No Operation Required." >> c:\logfile.log
                GOTO :ENDSCRIPT
:UNINSTALL
                echo "Uninstall" >> C:\logfile.log
                MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1} /L+*V "C:\ProgramData\ASRSetupLogs\UnifiedAgentMSIUninstall.log"
:ENDSCRIPT

```

## <a name="next-steps"></a>Passos seguintes
Agora, está pronto para [ativar a proteção](https://docs.microsoft.com/en-us/azure/site-recovery/site-recovery-vmware-to-azure#step-6-replicate-applications) para as máquinas virtuais.

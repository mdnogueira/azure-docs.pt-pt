---
title: "Configurar o mapa de serviço no Operations Management Suite | Microsoft Docs"
description: "Mapa de serviço é uma solução de Operations Management Suite que Deteta os componentes da aplicação em sistemas Windows e Linux e mapeia a comunicação entre os serviços automaticamente. Este artigo fornece detalhes para implementar o mapa de serviço no seu ambiente e utilizá-la numa variedade de cenários."
services: operations-management-suite
documentationcenter: 
author: daveirwin1
manager: jwhit
editor: tysonn
ms.assetid: d3d66b45-9874-4aad-9c00-124734944b2e
ms.service: operations-management-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/18/2016
ms.author: daseidma;bwren;dairwin
ms.openlocfilehash: 209631536d8c611b46a2ad3ff6c685062b17c649
ms.sourcegitcommit: adf6a4c89364394931c1d29e4057a50799c90fc0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/09/2017
---
# <a name="configure-service-map-in-operations-management-suite"></a>Configurar o mapa de serviço no Operations Management Suite
O Mapa de Serviço deteta automaticamente componentes de aplicações em sistemas Windows e Linux e mapeia a comunicação entre serviços. Pode utilizá-lo para ver os servidores que acha que deles – como interligados sistemas que fornecem serviços críticos. Mapa de serviço mostra as ligações entre servidores, processos e portas em qualquer arquitetura TCP ligados sem qualquer configuração necessária, que não seja a instalação de um agente.

Este artigo descreve os detalhes de configuração de agentes de mapa de serviço e integração. Para obter informações sobre como utilizar o mapa de serviço, consulte [utilizar a solução de mapa de serviço no Operations Management Suite](operations-management-suite-service-map.md).

## <a name="dependency-agent-downloads"></a>Transferências de agente de dependência
| Ficheiro | SO | Versão | SHA-256 |
|:--|:--|:--|:--|
| [InstallDependencyAgent Windows.exe](https://aka.ms/dependencyagentwindows) | Windows | 9.0.5 | 73B3F6A2A76A08D58F72A550947FF839B588591C48E6EDDD6DDF73AA3FD82B43 |
| [InstallDependencyAgent Linux64.bin](https://aka.ms/dependencyagentlinux) | Linux | 9.0.5 | A1BAD0B36EBF79F2B69113A07FCF48C68D90BD169C722689F9C83C69FC032371 |


## <a name="connected-sources"></a>Origens ligadas
Mapa de serviço obtém os dados do agente de dependência do Microsoft. O agente de dependência depende do agente do OMS para as suas ligações no Operations Management Suite. Isto significa que um servidor tem de ter o agente do OMS instalado e configurado pela primeira vez e, em seguida, pode ser instalado o agente de dependência. A tabela seguinte descreve as origens ligadas que suporte a solução de mapa de serviço.

| Origem ligada | Suportado | Descrição |
|:--|:--|:--|
| Agentes do Windows | Sim | Mapa de serviço analisa e recolhe dados de computadores de agente do Windows. <br><br>Para além de [agente do OMS](../log-analytics/log-analytics-windows-agents.md), agentes Windows requerem o agente de dependência da Microsoft. Consulte o [sistemas operativos suportados](#supported-operating-systems) para uma lista completa das versões do sistema operativo. |
| Agentes do Linux | Sim | Mapa de serviço analisa e recolhe dados de computadores de agente do Linux. <br><br>Para além de [agente do OMS](../log-analytics/log-analytics-linux-agents.md), agentes Linux requerem o agente de dependência da Microsoft. Consulte o [sistemas operativos suportados](#supported-operating-systems) para uma lista completa das versões do sistema operativo. |
| Grupo de gestão do System Center Operations Manager | Sim | Mapa de serviço analisa e recolhe dados do Windows e Linux agentes num ligado [grupo de gestão do System Center Operations Manager](../log-analytics/log-analytics-om-agents.md). <br><br>Não é necessária uma ligação direta a partir do computador de agente do System Center Operations Manager no Operations Management Suite. Dados seja reencaminhados do grupo de gestão para o repositório do Operations Management Suite.|
| Conta de armazenamento do Azure | Não | Mapa de serviço recolhe dados de computadores de agente, pelo que não existem dados a partir do mesmo para recolher do armazenamento do Azure. |

Mapa de serviço suporta apenas plataformas de 64 bits.

No Windows, o Microsoft Monitoring Agent (MMA) é utilizado pelo System Center Operations Manager e Operations Management Suite para recolher e enviar dados de monitorização. (Este agente é denominado o agente do System Center Operations Manager, agente do OMS, agente de análise do registo, MMA ou agente direta, dependendo no contexto.) System Center Operations Manager e Operations Management Suite fornecem versões de caixa de fora os diferentes do MMA. Estas versões de cada relatório para o System Center Operations Manager, ao Operations Management Suite ou a ambos.  

No Linux, o agente do OMS para reúne de Linux e envia dados para o Operations Management Suite de monitorização. Pode utilizar o mapa de serviço em servidores com agentes direta do OMS ou em servidores que estão ligados ao Operations Management Suite através de grupos de gestão do System Center Operations Manager.  

Neste artigo, iremos irá referir-se a todos os agentes – se Linux ou Windows, se ligada a um grupo de gestão do System Center Operations Manager ou diretamente ao Operations Management Suite – como o "agente OMS." Iremos utilizar o nome da implementação específico do agente apenas se for necessário para o contexto.

O agente de mapa de serviço não transmitir quaisquer dados propriamente ditos, e não requer quaisquer alterações aos firewalls ou portas. Os dados no mapa de serviço é sempre transmitidos pelo agente OMS no Operations Management Suite, diretamente ou através do Gateway do OMS.

![Agentes de mapa de serviço](media/oms-service-map/agents.png)

Se for um cliente do System Center Operations Manager com um grupo de gestão ligado ao Operations Management Suite:

- Se os agentes do System Center Operations Manager podem aceder à Internet para ligar ao Operations Management Suite, é necessária nenhuma configuração adicional.  
- Se os agentes do System Center Operations Manager não é possível aceder ao Operations Management Suite através da Internet, terá de configurar o Gateway do OMS para trabalhar com o System Center Operations Manager.
  
Se estiver a utilizar o agente do OMS direta, terá de configurar o agente de OMS estabelecer ligação ao Operations Management Suite ou para o seu Gateway OMS. O Gateway do OMS pode ser transferido a partir de [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=52666).

### <a name="management-packs"></a>Pacotes de gestão
Quando o mapa de serviço está ativado numa área de trabalho do Operations Management Suite, é enviado um pacote de gestão de 300 KB para todos os servidores do Windows na área de trabalho. Se estiver a utilizar os agentes do System Center Operations Manager num [grupo de gestão ligado](../log-analytics/log-analytics-om-agents.md), é implementado o pacote de gestão de mapa de serviço do System Center Operations Manager. Se os agentes estarem ligados diretamente, o Operations Management Suite fornece o pacote de gestão.

O pacote de gestão é denominado Microsoft.IntelligencePacks.ApplicationDependencyMonitor. Este é escrito %Programfiles%\Microsoft monitorização Agent\Agent\Health serviço State\Management Packs\. A origem de dados que utiliza o pacote de gestão é % programa files%\Microsoft monitorização Agent\Agent\Health serviço State\Resources\<AutoGeneratedID > \ Microsoft.EnterpriseManagement.Advisor.ApplicationDependencyMonitorDataSource.dll.

## <a name="installation"></a>Instalação
### <a name="install-the-dependency-agent-on-microsoft-windows"></a>Instale o agente de dependência no Microsoft Windows
São necessários privilégios de administrador para instalar ou desinstalar o agente.

O agente de dependência é instalado em computadores com Windows através do InstallDependencyAgent Windows.exe. Se executar este ficheiro executável sem quaisquer opções, inicia um assistente que pode seguir para instalar de forma interativa.  

Utilize os seguintes passos para instalar o agente de dependência em cada computador do Windows:

1.  Instalar o agente do OMS, utilizando as instruções apresentadas em [computadores Windows ligar para o serviço de análise de registos do Azure](../log-analytics/log-analytics-windows-agents.md).
2.  Transferir o agente do Windows e execute-o utilizando o seguinte comando: <br>`InstallDependencyAgent-Windows.exe`
3.  Siga o Assistente para instalar o agente.
4.  Se o agente de dependência não conseguir iniciar, verifique os registos para obter informações de erro detalhadas. Nos agentes do Windows, o diretório de registo é %Programfiles%\Microsoft Agent\logs de dependência. 

#### <a name="windows-command-line"></a>Linha de comandos do Windows
Utilize as opções da tabela seguinte para instalar a partir de uma linha de comandos. Para ver uma lista dos sinalizadores de instalação, execute o instalador utilizando o /? Sinalizador da seguinte forma.

    InstallDependencyAgent-Windows.exe /?

| Sinalizador | Descrição |
|:--|:--|
| /? | Obter uma lista das opções da linha de comandos. |
| /S | Efetue uma instalação automática com sem avisos do utilizador. |

Os ficheiros para o agente de dependência do Windows são colocados no agente de dependência C:\Program Files\Microsoft por predefinição.

### <a name="install-the-dependency-agent-on-linux"></a>Instale o agente de dependência no Linux
É necessário acesso de raiz para instalar ou configurar o agente.

O agente de dependência é instalado em computadores com Linux através do InstallDependencyAgent-Linux64.bin, um script de shell com um binário de extração. Pode executar o ficheiro utilizando ostrar ou adicionar permissões para o próprio ficheiro de execução.
 
Utilize os seguintes passos para instalar o agente de dependência em cada computador Linux:

1.  Instalar o agente do OMS, utilizando as instruções apresentadas em [recolher e gerir dados a partir de computadores com Linux](https://technet.microsoft.com/library/mt622052.aspx).
2.  Instale o agente de dependência de Linux como raiz usando o seguinte comando:<br>`sh InstallDependencyAgent-Linux64.bin`
3.  Se o agente de dependência não conseguir iniciar, verifique os registos para obter informações de erro detalhadas. Nos agentes Linux, o diretório de registo é /var/opt/microsoft/dependency-agent/log.

Para ver uma lista dos sinalizadores de instalação, execute a instalação do programa com-ajuda sinalizador da seguinte forma.

    InstallDependencyAgent-Linux64.bin -help

| Sinalizador | Descrição |
|:--|:--|
| -ajudar | Obter uma lista das opções da linha de comandos. |
| -s | Efetue uma instalação automática com sem avisos do utilizador. |
| -Verifique | Verifique as permissões e o sistema operativo, mas não instale o agente. |

Os ficheiros para o agente de dependência são colocados nas seguintes diretórios:

| Ficheiros | Localização |
|:--|:--|
| Ficheiros de núcleo | /OPT/Microsoft/Dependency-Agent |
| Ficheiros de registo | /var/OPT/Microsoft/Dependency-Agent/log |
| Ficheiros de configuração | /etc/OPT/Microsoft/Dependency-Agent/Config |
| Ficheiros executáveis do serviço | /OPT/Microsoft/Dependency-Agent/bin/Microsoft-Dependency-Agent<br>/OPT/Microsoft/Dependency-Agent/bin/Microsoft-Dependency-Agent-Manager |
| Ficheiros de armazenamento binário | /var/OPT/Microsoft/Dependency-Agent/Storage |

## <a name="installation-script-examples"></a>Exemplos de script de instalação
Para facilmente implementar o agente de dependência em vários servidores ao mesmo tempo, ajuda a para utilizar um script. Pode utilizar os seguintes exemplos de script para transferir e instalar o agente de dependência no Windows ou Linux.

### <a name="powershell-script-for-windows"></a>Script do PowerShell para Windows
```PowerShell
Invoke-WebRequest "https://aka.ms/dependencyagentwindows" -OutFile InstallDependencyAgent-Windows.exe

.\InstallDependencyAgent-Windows.exe /S
```

### <a name="shell-script-for-linux"></a>Script de shell para Linux
```
wget --content-disposition https://aka.ms/dependencyagentlinux -O InstallDependencyAgent-Linux64.bin
sudo sh InstallDependencyAgent-Linux64.bin -s
```

## <a name="azure-vm-extension"></a>Extensão da VM do Azure
Pode facilmente implementar o agente de dependência para as VMs do Azure utilizando um [extensão da VM do Azure](https://docs.microsoft.com/azure/virtual-machines/windows/classic/agents-and-extensions).  Com a extensão de VM do Azure, pode implementar o agente de dependência para as suas VMs através de um script do PowerShell ou diretamente no modelo do Azure Resource Manager a VM.  Uma extensão está disponível para Windows (DependencyAgentWindows) e do Linux (DependencyAgentLinux).  Se implementar através da extensão da VM do Azure, os agentes podem ser atualizados automaticamente para as versões mais recentes.

Para implementar a extensão da VM do Azure através do PowerShell, pode utilizar o exemplo seguinte:
```PowerShell
#
# Deploy the Dependency Agent to every VM in a Resource Group
#

$version = "9.1"
$ExtPublisher = "Microsoft.Azure.Monitoring.DependencyAgent"
$OsExtensionMap = @{ "Windows" = "DependencyAgentWindows"; "Linux" = "DependencyAgentLinux" }
$rmgroup = "<Your Resource Group Here>"

Get-AzureRmVM -ResourceGroupName $rmgroup |
ForEach-Object {
    ""
    $name = $_.Name
    $os = $_.StorageProfile.OsDisk.OsType
    $location = $_.Location
    $vmRmGroup = $_.ResourceGroupName
    "${name}: ${os} (${location})"
    Date -Format o
    $ext = $OsExtensionMap.($os.ToString())
    $result = Set-AzureRmVMExtension -ResourceGroupName $vmRmGroup -VMName $name -Location $location `
    -Publisher $ExtPublisher -ExtensionType $ext -Name "DependencyAgent" -TypeHandlerVersion $version
    $result.IsSuccessStatusCode
}
```

Uma forma mesmo mais fácil de garantir a é o agente de dependência em cada uma das suas VMs é para incluir o agente no seu modelo do Azure Resource Manager.  Tenha em atenção que o agente de dependência depende ainda o agente do OMS, por isso, o [extensão de VM de agente do OMS](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-vm-extension) tem de ser implementada pela primeira vez.  É possível adicionar o seguinte fragmento de JSON para o *recursos* secção do seu modelo.
```JSON
"type": "Microsoft.Compute/virtualMachines/extensions",
"name": "[concat(parameters('vmName'), '/DependencyAgent')]",
"apiVersion": "2017-03-30",
"location": "[resourceGroup().location]",
"dependsOn": [
"[concat('Microsoft.Compute/virtualMachines/', parameters('vmName'))]"
],
"properties": {
    "publisher": "Microsoft.Azure.Monitoring.DependencyAgent",
    "type": "DependencyAgentWindows",
    "typeHandlerVersion": "9.1",
    "autoUpgradeMinorVersion": true
}

```


## <a name="desired-state-configuration"></a>Configuração do Estado Pretendido
Para implementar o agente de dependência através da configuração de estado pretendida, pode utilizar o módulo de xPSDesiredStateConfiguration e um bit de código como o seguinte:
```
configuration ServiceMap {

Import-DscResource -ModuleName xPSDesiredStateConfiguration

$DAPackageLocalPath = "C:\InstallDependencyAgent-Windows.exe"

Node localhost
{ 
    # Download and install the Dependency Agent
    xRemoteFile DAPackage 
    {
        Uri = "https://aka.ms/dependencyagentwindows"
        DestinationPath = $DAPackageLocalPath
    }

    xPackage DA
    {
        Ensure="Present"
        Name = "Dependency Agent"
        Path = $DAPackageLocalPath
        Arguments = '/S'
        ProductId = ""
        InstalledCheckRegKey = "HKEY_LOCAL_MACHINE\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\DependencyAgent"
        InstalledCheckRegValueName = "DisplayName"
        InstalledCheckRegValueData = "Dependency Agent"
        DependsOn = "[xRemoteFile]DAPackage"
    }
  }
}
```

## <a name="uninstallation"></a>Desinstalação
### <a name="uninstall-the-dependency-agent-on-windows"></a>Desinstale o agente de dependência no Windows
Um administrador pode desinstalar o agente de dependência para o Windows através do painel de controlo.

Um administrador também pode executar %Programfiles%\Microsoft dependência Agent\Uninstall.exe para desinstalar o agente de dependência.

### <a name="uninstall-the-dependency-agent-on-linux"></a>Desinstale o agente de dependência no Linux
Para desinstalar completamente o agente de dependência do Linux, tem de remover o próprio agente e o conector, que é instalado automaticamente com o agente. Pode desinstalar ambos usando o seguinte comando único.
<br>RHEL, CentOs ou Oracle:
```
sudo rpm -e dependency-agent dependency-agent-connector
```
Ubuntu:
```
sudo dpkg --purge dependency-agent dependency-agent-connector
```
## <a name="troubleshooting"></a>Resolução de problemas
Se tiver quaisquer problemas de instalação e execução de mapa de serviço, esta secção pode ajudá-lo. Se ainda não é possível resolver o problema, contacte Support da Microsoft.

### <a name="dependency-agent-installation-problems"></a>Problemas de instalação do agente de dependência
#### <a name="installer-asks-for-a-reboot"></a>Instalador pede-lhe uma reinicialização
O agente de dependência *geralmente* não requer um reinício após a instalação ou desinstalação. No entanto, em certos casos raros, Windows Server requer uma reinicialização para prosseguir com uma instalação. Isto acontece quando uma dependência, normalmente, o Microsoft Visual C++ Redistributable, requer uma reinicialização devido a um ficheiro de bloqueado.

#### <a name="message-unable-to-install-dependency-agent-visual-studio-runtime-libraries-failed-to-install-code--codenumber-appears"></a>Mensagem "não é possível instalar o agente de dependência: não foi possível instalar o Runtime do Visual Studio bibliotecas (código = [code_number])" é apresentada

O agente de dependência Microsoft baseia-se as bibliotecas de runtime do Microsoft Visual Studio. Irá receber uma mensagem se houver um problema durante a instalação das bibliotecas. 

Os programas de instalação de biblioteca de tempo de execução criar registos na pasta %LOCALAPPDATA%\temp. O ficheiro é dd_vcredist_arch_yyyymmddhhmmss.log, onde *arquitetura* é "x86" ou "amd64" e *yyyymmddhhmmss* é a data e hora (relógio de 24 horas) quando o registo foi criado. O registo fornece detalhes sobre o problema que está a bloquear a instalação.

Poderá ser útil instalar o [bibliotecas de tempo de execução mais recentes](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads) sozinho primeiro.

A tabela seguinte apresenta uma lista de números de código e resoluções sugeridas.

| Código | Descrição | Resolução |
|:--|:--|:--|
| 0x17 | O instalador de biblioteca requer uma atualização do Windows que não foi instalada. | Procure no registo do instalador mais recente da biblioteca.<br><br>Se uma referência a "Windows8.1-KB2999226-x64.msu" seguida de uma linha "erro 0x80240017: Falha ao executar o pacote MSU," não tem os pré-requisitos para instalar KB2999226. Siga as instruções na secção pré-requisitos [Universal C tempo de execução no Windows](https://support.microsoft.com/kb/2999226). Poderá ter de executar o Windows Update e reiniciar várias vezes para instalar os pré-requisitos.<br><br>Execute novamente o instalador do agente de dependência da Microsoft. |

### <a name="post-installation-issues"></a>Problemas de pós-instalação
#### <a name="server-doesnt-appear-in-service-map"></a>Servidor não são apresentados no mapa de serviço
Se a instalação do agente de dependência com êxito, mas não vir o seu servidor na solução de mapa de serviço:
* O agente de dependência é instalado com êxito? Pode validar esta verificação para ver se o serviço está instalado e em execução.<br><br>
**Windows**: procure o serviço com o nome "Microsoft Dependency agente".<br>
**Linux**: procure para a execução processo "microsoft-dependência-agent."

* São-na [livre preços escalão do Operations Management Suite/Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions#offers-and-pricing-tiers)? O plano gratuito permite até cinco servidores exclusivos de mapa de serviço. Quaisquer servidores subsequentes não serão apresentados no mapa de serviço, mesmo que os cinco anterior já não estão a enviar dados.

* É o envio de registo do servidor e dados de desempenho para o Operations Management Suite? Aceda a pesquisa de registo e execute a seguinte consulta para o seu computador: 

        * Computer="<your computer name here>" | measure count() by Type
        
  Obteve uma variedade de eventos nos resultados? Os dados é recente? Se Sim, o agente do OMS está a funcionar corretamente e comunicação com o serviço do Operations Management Suite. Caso contrário, verifique o agente do OMS no seu servidor: [resolução de problemas do agente do OMS para Windows](https://support.microsoft.com/help/3126513/how-to-troubleshoot-operations-management-suite-onboarding-issues) ou [agente do OMS para resolução de problemas do Linux](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/Troubleshooting.md).

#### <a name="server-appears-in-service-map-but-has-no-processes"></a>O servidor é apresentado no mapa de serviço, mas não tem nenhum processo
Se vir o seu servidor no mapa de serviço, mas não tem nenhum processo ou da ligação de dados, que indica que o agente de dependência está instalado e em execução, mas não carregar o controlador de kernel. 

Verifique o ficheiro de C:\Program Files\Microsoft dependência Agent\logs\wrapper.log (Windows) ou o ficheiro /var/opt/microsoft/dependency-agent/log/service.log (Linux). As últimas linhas do ficheiro devem indicar por que motivo o kernel não carregar. Por exemplo, o kernel poderá não ser suportado no Linux, se atualizar o kernel.

## <a name="data-collection"></a>Recolha de dados
Pode esperar cada agente a transmitir aproximadamente 25 MB por dia, consoante complexos como as dependências de sistema são. Cada agente envia dados de dependência de mapa de serviço a cada 15 segundos.  

O agente de dependência normalmente consome 0.1 percentagem de memória de sistema e 0.1 percentagem de CPU do sistema.

## <a name="supported-azure-regions"></a>Regiões do Azure suportadas
Mapa de serviço está atualmente disponível em regiões do Azure do seguintes:
- EUA Leste
- Europa Ocidental
- EUA Centro-Oeste
- Sudeste Asiático


## <a name="supported-operating-systems"></a>Sistemas operativos suportados
As secções seguintes listam os sistemas operativos suportados para o agente de dependência. Mapa de serviço não suporta arquiteturas de 32 bits para qualquer sistema operativo.

### <a name="windows-server"></a>Windows Server
- Windows Server 2016
- Windows Server 2012 R2
- Windows Server 2012
- Windows Server 2008 R2 SP1

### <a name="windows-desktop"></a>Ambiente de trabalho do Windows
- Windows 10
- Windows 8.1
- Windows 8
- Windows 7

### <a name="red-hat-enterprise-linux-centos-linux-and-oracle-linux-with-rhel-kernel"></a>Red Hat Enterprise Linux, CentOS Linux e Oracle Linux (com o Kernel do RHEL)
- São suportadas apenas predefinido e versões de kernel do SMP Linux.
- Versões kernel não padrão, tais como PAE e Xen, não são suportados para qualquer distribuição de Linux. Por exemplo, um sistema com a cadeia de versão de "2.6.16.21-0.8-xen" não é suportado.
- Kernels personalizados, incluindo recompilações de kernels padrão, não são suportadas.
- CentOSPlus kernel não é suportada.
- Oracle Unbreakable Enterprise Kernel (UEK) é abrangida numa secção posterior deste artigo.


#### <a name="red-hat-linux-7"></a>Red Hat Linux 7
| Versão do SO | Versão de kernel |
|:--|:--|
| 7.0 | 3.10.0-123 |
| 7.1 | 3.10.0-229 |
| 7.2 | 3.10.0-327 |
| 7.3 | 3.10.0-514 |
| 7.4 | 3.10.0-693 |

#### <a name="red-hat-linux-6"></a>Red Hat Linux 6
| Versão do SO | Versão de kernel |
|:--|:--|
| 6.0 | 2.6.32-71 |
| 6.1 | 2.6.32-131 |
| 6.2 | 2.6.32-220 |
| 6.3 | 2.6.32-279 |
| 6.4 | 2.6.32-358 |
| 6.5 | 2.6.32-431 |
| 6.6 | 2.6.32-504 |
| 6.7 | 2.6.32-573 |
| 6.8 | 2.6.32-642 |
| 6.9 | 2.6.32-696 |

#### <a name="red-hat-linux-5"></a>Red Hat Linux 5
| Versão do SO | Versão de kernel |
|:--|:--|
| 5.8 | 2.6.18-308 |
| 5.9 | 2.6.18-348 |
| 5.10 | 2.6.18-371 |
| 5.11 | 2.6.18-398<br>2.6.18-400<br>2.6.18-402<br>2.6.18-404<br>2.6.18-406<br>2.6.18-407<br>2.6.18-408<br>2.6.18-409<br>2.6.18-410<br>2.6.18-411<br>2.6.18-412<br>2.6.18-416<br>2.6.18-417<br>2.6.18-419<br>2.6.18-420 |

### <a name="ubuntu-server"></a>Ubuntu Server
- Kernels personalizados, incluindo recompilações de kernels padrão, não são suportadas.

| Versão do SO | Versão de kernel |
|:--|:--|
| 16.04 | 4.4.0-98 |
| 14.04 | 3.13.0-135<br>4.4.0-98 |

### <a name="oracle-enterprise-linux-with-unbreakable-enterprise-kernel"></a>Oracle Enterprise Linux com o Kernel Unbreakable Enterprise
#### <a name="oracle-linux-6"></a>Oracle Linux 6
| Versão do SO | Versão de kernel
|:--|:--|
| 6.2 | Oracle 2.6.32-300 (UEK R1) |
| 6.3 | Oracle 2.6.39-200 (UEK R2) |
| 6.4 | Oracle 2.6.39-400 (UEK R2) |
| 6.5 | Oracle 2.6.39-400 (UEK R2 i386) |
| 6.6 | Oracle 2.6.39-400 (UEK R2 i386) |

#### <a name="oracle-linux-5"></a>Oracle Linux 5

| Versão do SO | Versão de kernel
|:--|:--|
| 5.8 | Oracle 2.6.32-300 (UEK R1) |
| 5.9 | Oracle 2.6.39-300 (UEK R2) |
| 5.10 | Oracle 2.6.39-400 (UEK R2) |
| 5.11 | Oracle 2.6.39-400 (UEK R2) |

#### <a name="suse-linux-enterprise-server"></a>Servidor Linux Empresarial SUSE

#### <a name="suse-linux-11"></a>SUSE Linux 11
| Versão do SO | Versão de kernel
|:--|:--|
| 11 | 2.6.27 |
| 11 SP1 | 2.6.32 |
| 11 SP2 | 3.0.13 |
| 11 SP3 | 3.0.76 |
| 11 SP4 | 3.0.101 |

#### <a name="suse-linux-10"></a>SUSE Linux 10
| Versão do SO | Versão de kernel
|:--|:--|
| 10 SP4 | 2.6.16.60 |

## <a name="diagnostic-and-usage-data"></a>dados de diagnóstico e utilização
A Microsoft recolhe automaticamente dados de utilização e desempenho através da utilização do serviço de mapa de serviço. A Microsoft utiliza estes dados para fornecer e melhorar a qualidade, segurança e integridade do serviço de mapa de serviço. Os dados incluem informações sobre a configuração do seu software, como o sistema operativo e versão. Também inclui endereço IP, o nome DNS e o nome da estação de trabalho para fornecer capacidades de resolução de problemas exatas e eficientes. Não recolhemos nomes, moradas ou outras informações de contacto.

Para obter mais informações sobre a utilização e recolha de dados, consulte o [declaração de privacidade do Microsoft Online Services](https://go.microsoft.com/fwlink/?LinkId=512132).



## <a name="next-steps"></a>Passos seguintes
- Saiba como [utilizar o mapa de serviço](operations-management-suite-service-map.md) depois foi implementado e configurado.

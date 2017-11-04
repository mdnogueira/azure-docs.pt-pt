---
title: "Ligar a solução de dados no Log Analytics | Microsoft Docs"
description: "Durante a transmissão de dados são consolidados dados de rede e o desempenho dos computadores com agentes do OMS, incluindo do Operations Manager e agentes Windows ligados. Dados de rede são combinados com os seus dados de registo para o ajudar a correlacionar dados."
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
ms.assetid: fc3d7127-0baa-4772-858a-5ba995d1519b
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/19/2017
ms.author: magoedte;banders
ms.openlocfilehash: 3bb4c82268fe7805227c213000dc803307876fe7
ms.sourcegitcommit: 963e0a2171c32903617d883bb1130c7c9189d730
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/20/2017
---
# <a name="wire-data-20-preview-solution-in-log-analytics"></a>Durante a transmissão 2.0 de dados (pré-visualização) solução na análise de registos

![Símbolo de dados de transmissão](./media/log-analytics-wire-data/wire-data2-symbol.png)

Durante a transmissão de dados são consolidados dados de rede e de desempenho recolhidos a partir de computadores ligados do Windows e Linux-ligado com o agente do OMS, incluindo os que são monitorizados pelo Operations Manager no seu ambiente. Dados de rede são combinados com os outros dados de registo para o ajudar a correlacionar dados.

Para além do agente do OMS, a solução de dados por fio utiliza agentes de dependência da Microsoft instalado nos computadores na sua infraestrutura de TI. Dependência agentes monitorizam dados de rede enviados de e para os seus computadores, para 2-3 em níveis de rede a [modelo OSI](https://en.wikipedia.org/wiki/OSI_model), incluindo os vários protocolos e portas utilizadas. É, em seguida, enviados dados para análise de registos com agentes.  

> [!NOTE]
> Não é possível adicionar a versão anterior da solução de dados por fio a áreas de trabalho de novo. Se tiver a solução de dados por fio original ativada, pode continuar a utilizá-lo. No entanto, para utilizar durante a transmissão dados 2.0, deverá remover primeiro a versão original.

Por predefinição, análise de registos os registos de dados para a CPU, memória, disco e dados de desempenho de rede do contadores incorporados no Windows e Linux, bem como outros contadores de desempenho que pode especificar. Rede e outra recolha de dados é efetuada em tempo real para cada agente, incluindo sub-redes e protocolos de nível de aplicação que está a ser utilizados pelo computador.  Dados por fio analisa os dados de rede ao nível da aplicação, não a para baixo na camada de transporte de TCP.  A solução não observe individuais confirmações e SYNs.  Depois do handshake estiver concluído, é considerada uma ligação em direto e marcada como ligado. Se a ligação permanece em direto, desde que ambos os lados aceitar o socket está aberta e dados podem passar anterior e descritos.  Uma vez nos dois lados fecha a ligação, está marcado como desligado.  Por conseguinte, esta só contabiliza a largura de banda de pacotes concluídas com êxito, não comunica no reenvia ou falha de pacotes.

Se utilizou [sFlow](http://www.sflow.org/) ou outro software com [protocolo de NetFlow da Cisco](http://www.cisco.com/c/en/us/products/collateral/ios-nx-os-software/ios-netflow/prod_white_paper0900aecd80406232.html), as estatísticas e dados vê a partir de dados por fio será familiares.

Alguns dos tipos de consultas de pesquisa de registo incorporadas incluem:

- Agentes que fornecem dados por fio
- Endereço IP de agentes que fornecem dados por fio
- Comunicações de saída por endereços IP
- Número de bytes enviados por protocolos de aplicação
- Número de bytes enviados por um serviço de aplicações
- Bytes recebidos por protocolos diferentes
- Total de bytes enviados e recebidos por versão IP
- Latência média de ligações que foram avaliadas fiável
- Processos de computador que iniciou ou receberam tráfego de rede
- Quantidade de tráfego de rede para um processo

Durante a pesquisa com dados por fio, pode filtrar e os dados de grupo para ver informações sobre os agentes principais e os protocolos superiores. Ou pode ver quando determinados computadores (endereços de MAC/endereços IP) comunicadas com outros, como tempo e a quantidade de dados foi enviada — basicamente, pode ver os metadados sobre o tráfego de rede que é baseada na procura.

No entanto, uma vez que está a ver metadados, não é necessariamente útil para resolução de problemas detalhada. Durante a transmissão de dados na análise de registos não são um total de captura de dados de rede.  Não se destina de resolução de problemas de profundidade ao nível do pacote. A vantagem de utilizar o agente, em comparação comparado outros métodos de coleção, é que não tem de instalar aplicações, reconfigurar os comutadores de rede ou efetuar configurações mais complicadas. Durante a transmissão de dados são simplesmente agente com base em — instale o agente num computador e irá monitorizar o seus próprios tráfego de rede. Outra vantagem é quando pretender monitorizar cargas de trabalho em execução no fornecedores de nuvem ou o fornecedor de serviços de alojamento ou o Microsoft Azure, onde o utilizador não possui a camada de recursos de infraestrutura.

## <a name="connected-sources"></a>Origens ligadas

Dados por fio obtém os dados do agente de dependência do Microsoft. O agente de dependência depende do agente do OMS para as suas ligações à análise de registos. Isto significa que um servidor tem de ter o agente do OMS instalado e configurado pela primeira vez e, em seguida, instalar o agente de dependência. A tabela seguinte descreve as origens de ligado que a solução de dados por fio suporta.

| **Origem ligada** | **Suportado** | **Descrição** |
| --- | --- | --- |
| Agentes do Windows | Sim | Dados por fio analisa e recolhe dados de computadores de agente do Windows. <br><br> Para além de [agente do OMS](log-analytics-windows-agents.md), agentes Windows requerem o agente de dependência da Microsoft. Consulte o [sistemas operativos suportados](../operations-management-suite/operations-management-suite-service-map-configure.md#supported-operating-systems) para uma lista completa das versões do sistema operativo. |
| Agentes do Linux | Sim | Dados por fio analisa e recolhe dados de computadores de agente do Linux.<br><br> Para além de [agente do OMS](log-analytics-quick-collect-linux-computer.md), agentes Linux requerem o agente de dependência da Microsoft. Consulte o [sistemas operativos suportados](../operations-management-suite/operations-management-suite-service-map-configure.md#supported-operating-systems) para uma lista completa das versões do sistema operativo. |
| Grupo de gestão do System Center Operations Manager | Sim | Dados por fio analisa e recolhe dados do Windows e Linux agentes num ligado [grupo de gestão do System Center Operations Manager](log-analytics-om-agents.md). <br><br> Não é necessária uma ligação direta do computador de agente do System Center Operations Manager para análise de registos. Dados seja reencaminhados do grupo de gestão para análise de registos. |
| Conta de armazenamento do Azure | Não | Dados por fio recolhe dados de computadores de agente, pelo que não existem dados a partir do mesmo para recolher do armazenamento do Azure. |

No Windows, o Microsoft Monitoring Agent (MMA) é utilizado pelo System Center Operations Manager e análise de registos para recolher e enviar dados. Dependendo no contexto, o agente é denominado o agente do System Center Operations Manager, agente do OMS, agente de análise do registo, MMA ou agente direta. System Center Operations Manager e análise de registos fornecem ligeiramente diferentes versões do MMA. Estas versões de cada relatório para o System Center Operations Manager, para análise de registos ou para ambos.

No Linux, o agente do OMS para Linux recolhe e envia dados para análise de registos. Pode utilizar durante a transmissão de dados em servidores com agentes direta do OMS ou em servidores que estejam anexados a análise de registos através de grupos de gestão do System Center Operations Manager.

Neste artigo, referências a todos os agentes, se Linux ou Windows, se encontra ligada a um grupo de gestão do System Center Operations Manager ou diretamente ao Log Analytics são denominado padrão de _agente do OMS_. Iremos utilizar o nome da implementação específico do agente apenas se for necessário para o contexto.

O agente de dependência não transmitir quaisquer dados propriamente ditos, e não requer quaisquer alterações aos firewalls ou portas. Os dados nos dados durante a transmissão é sempre transmitidos pelo agente OMS à análise de registos, seja diretamente ou através do Gateway do OMS.

![Diagrama de agente](./media/log-analytics-wire-data/agents.png)

Se for um utilizador do System Center Operations Manager com um grupo de gestão ligado à análise de registos:

- É necessária nenhuma configuração adicional quando os agentes do System Center Operations Manager podem aceder à Internet para ligar ao Log Analytics.
- Terá de configurar o Gateway do OMS para trabalhar com o System Center Operations Manager quando os agentes do System Center Operations Manager não consegue aceder à análise de registos através da Internet.

Se estiver a utilizar o agente direta, terá de configurar o agente OMS para ligar à análise de registos ou para o seu Gateway OMS. Pode transferir o Gateway do OMS do [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=52666).

## <a name="prerequisites"></a>Pré-requisitos

- Requer o [informações e análises](https://www.microsoft.com/cloud-platform/operations-management-suite-pricing) oferta de soluções.
- Se estiver a utilizar a versão anterior da solução de dados por fio, tem primeiro de o remover. No entanto, todos os dados capturados através da solução de dados por fio original ainda está disponível na transmissão dados 2.0 e a pesquisa de registo.
- São necessários privilégios de administrador para instalar ou desinstalar o agente de dependência.
- O agente de dependência tem de estar instalado num computador com um sistema operativo de 64 bits.

### <a name="operating-systems"></a>Sistemas operativos

As secções seguintes listam os sistemas operativos suportados para o agente de dependência. Durante a transmissão dados não suportam arquiteturas de 32 bits para qualquer sistema operativo.

#### <a name="windows-server"></a>Windows Server

- Windows Server 2016
- Windows Server 2012 R2
- Windows Server 2012
- Windows Server 2008 R2 SP1

#### <a name="windows-desktop"></a>Ambiente de trabalho do Windows

- Windows 10
- Windows 8.1
- Windows 8
- Windows 7

#### <a name="red-hat-enterprise-linux-centos-linux-and-oracle-linux-with-rhel-kernel"></a>Red Hat Enterprise Linux, CentOS Linux e Oracle Linux (com o Kernel do RHEL)

- São suportadas apenas predefinido e versões de kernel do SMP Linux.
- Versões kernel não padrão, tais como PAE e Xen, não são suportados para qualquer distribuição de Linux. Por exemplo, um sistema com a cadeia de versão de _2.6.16.21-0.8-xen_ não é suportada.
- Kernels personalizados, incluindo recompilações de kernels padrão, não são suportadas.
- CentOSPlus kernel não é suportada.
- Oracle Unbreakable Enterprise Kernel (UEK) é abrangida numa secção posterior deste artigo.

#### <a name="red-hat-linux-7"></a>Red Hat Linux 7

| **Versão do SO** | **Versão de kernel** |
| --- | --- |
| 7.0 | 3.10.0-123 |
| 7.1 | 3.10.0-229 |
| 7.2 | 3.10.0-327 |
| 7.3 | 3.10.0-514 |

#### <a name="red-hat-linux-6"></a>Red Hat Linux 6

| **Versão do SO** | **Versão de kernel** |
| --- | --- |
| 6.0 | 2.6.32-71 |
| 6.1 | 2.6.32-131 |
| 6.2 | 2.6.32-220 |
| 6.3 | 2.6.32-279 |
| 6.4 | 2.6.32-358 |
| 6.5 | 2.6.32-431 |
| 6.6 | 2.6.32-504 |
| 6.7 | 2.6.32-573 |
| 6.8 | 2.6.32-642 |

#### <a name="red-hat-linux-5"></a>Red Hat Linux 5

| **Versão do SO** | **Versão de kernel** |
| --- | --- |
| 5.8 | 2.6.18-308 |
| 5.9 | 2.6.18-348 |
| 5.10 | 2.6.18-371 |
| 5.11 | 2.6.18-398 <br> 2.6.18-400 <br>2.6.18-402 <br>2.6.18-404 <br>2.6.18-406 <br> 2.6.18-407 <br> 2.6.18-408 <br> 2.6.18-409 <br> 2.6.18-410 <br> 2.6.18-411 <br> 2.6.18-412 <br> 2.6.18-416 <br> 2.6.18-417 <br> 2.6.18-419 |

#### <a name="oracle-enterprise-linux-with-unbreakable-enterprise-kernel"></a>Oracle Enterprise Linux com o Kernel Unbreakable Enterprise

#### <a name="oracle-linux-6"></a>Oracle Linux 6

| **Versão do SO** | **Versão de kernel** |
| --- | --- |
| 6.2 | Oracle 2.6.32-300 (UEK R1) |
| 6.3 | Oracle 2.6.39-200 (UEK R2) |
| 6.4 | Oracle 2.6.39-400 (UEK R2) |
| 6.5 | Oracle 2.6.39-400 (UEK R2 i386) |
| 6.6 | Oracle 2.6.39-400 (UEK R2 i386) |

#### <a name="oracle-linux-5"></a>Oracle Linux 5

| **Versão do SO** | **Versão de kernel** |
| --- | --- |
| 5.8 | Oracle 2.6.32-300 (UEK R1) |
| 5.9 | Oracle 2.6.39-300 (UEK R2) |
| 5.10 | Oracle 2.6.39-400 (UEK R2) |
| 5.11 | Oracle 2.6.39-400 (UEK R2) |

#### <a name="suse-linux-enterprise-server"></a>Servidor Linux Empresarial SUSE

#### <a name="suse-linux-11"></a>SUSE Linux 11

| **Versão do SO** | **Versão de kernel** |
| --- | --- |
| 11 | 2.6.27 |
| 11 SP1 | 2.6.32 |
| 11 SP2 | 3.0.13 |
| 11 SP3 | 3.0.76 |
| 11 SP4 | 3.0.101 |

#### <a name="suse-linux-10"></a>SUSE Linux 10

| **Versão do SO** | **Versão de kernel** |
| --- | --- |
| 10 SP4 | 2.6.16.60 |

#### <a name="dependency-agent-downloads"></a>Transferências de agente de dependência

| **Ficheiro** | **SO** | **Versão** | **SHA-256** |
| --- | --- | --- | --- |
| [InstallDependencyAgent Windows.exe](https://aka.ms/dependencyagentwindows) | Windows | 9.0.5 | 73B3F6A2A76A08D58F72A550947FF839B588591C48E6EDDD6DDF73AA3FD82B43 |
| [InstallDependencyAgent Linux64.bin](https://aka.ms/dependencyagentlinux) | Linux | 9.0.5 | A1BAD0B36EBF79F2B69113A07FCF48C68D90BD169C722689F9C83C69FC032371 |



## <a name="configuration"></a>Configuração

Execute os seguintes passos para configurar a solução de dados por fio para as áreas de trabalho.

1. Ativar a solução de análise de registos de atividade do [do Azure marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.WireData2OMS?tab=Overview) ou utilizando o processo descrito no [soluções de análise de registos adicionar da galeria do soluções](log-analytics-add-solutions.md).
2. Instale o agente de dependência em cada computador onde pretende obter dados. O agente de dependência pode monitorizar as ligações ao vizinhos imediatas, pelo que poderá não ser necessário um agente em cada computador.

### <a name="install-the-dependency-agent-on-windows"></a>Instale o agente de dependência no Windows

São necessários privilégios de administrador para instalar ou desinstalar o agente.

O agente de dependência é instalado em computadores com o Windows através do InstallDependencyAgent Windows.exe. Se executar este ficheiro executável sem quaisquer opções, inicia um assistente que pode seguir para instalar de forma interativa.

Utilize os seguintes passos para instalar o agente de dependência em cada computador com o Windows:

1. Instalar o agente do OMS seguir os passos em [recolher dados de computadores com o Windows alojados no seu ambiente](log-analytics-windows-agents.md).
2. Transferir o agente de dependência do Windows através da ligação na secção anterior e, em seguida, execute-o utilizando o seguinte comando:`InstallDependencyAgent-Windows.exe`
3. Siga o Assistente para instalar o agente.
4. Se o agente de dependência não conseguir iniciar, verifique os registos para obter informações de erro detalhadas. Para agentes do Windows, o diretório de registo é %Programfiles%\Microsoft Agent\logs de dependência.

#### <a name="windows-command-line"></a>Linha de comandos do Windows

Utilize as opções da tabela seguinte para instalar a partir de uma linha de comandos. Para ver uma lista dos sinalizadores de instalação, execute o instalador utilizando o /? Sinalizador da seguinte forma.

InstallDependencyAgent Windows.exe /?

| **Sinalizador** | **Descrição** |
| --- | --- |
| <code>/?</code> | Obter uma lista das opções da linha de comandos. |
| <code>/S</code> | Efetue uma instalação automática com sem avisos do utilizador. |

Os ficheiros para o agente de dependência do Windows são colocados no agente de dependência C:\Program Files\Microsoft por predefinição.

### <a name="install-the-dependency-agent-on-linux"></a>Instale o agente de dependência no Linux

É necessário acesso de raiz para instalar ou configurar o agente.

O agente de dependência é instalado em computadores com Linux através do InstallDependencyAgent-Linux64.bin, um script de shell com um binário de extração. Pode executar o ficheiro utilizando _ostrar_ ou adicionar permissões para o próprio ficheiro de execução.

Utilize os seguintes passos para instalar o agente de dependência em cada computador Linux:

1. Instalar o agente do OMS seguir os passos em [recolher dados de computadores Linux alojados no seu ambiente](log-analytics-quick-collect-linux-computer.md#obtain-workspace-id-and-key).
2. Transferir o agente de dependência de Linux através da ligação na secção anterior e, em seguida, instalá-los como raiz, utilizando o seguinte comando: partilhar InstallDependencyAgent Linux64.bin
3. Se o agente de dependência não conseguir iniciar, verifique os registos para obter informações de erro detalhadas. Nos agentes Linux, é o diretório de registo: /var/opt/microsoft/dependency-agent/log.

Para ver uma lista dos sinalizadores de instalação, execute o programa de instalação com o `-help` sinalizador da seguinte forma.

```
InstallDependencyAgent-Linux64.bin -help
```

| **Sinalizador** | **Descrição** |
| --- | --- |
| <code>-help</code> | Obter uma lista das opções da linha de comandos. |
| <code>-s</code> | Efetue uma instalação automática com sem avisos do utilizador. |
| <code>--check</code> | Verifique as permissões e o sistema operativo, mas não instale o agente. |

Os ficheiros para o agente de dependência são colocados nas seguintes diretórios:

| **Ficheiros** | **Localização** |
| --- | --- |
| Ficheiros de núcleo | /OPT/Microsoft/Dependency-Agent |
| Ficheiros de registo | /var/OPT/Microsoft/Dependency-Agent/log |
| Ficheiros de configuração | /etc/OPT/Microsoft/Dependency-Agent/Config |
| Ficheiros executáveis do serviço | /OPT/Microsoft/Dependency-Agent/bin/Microsoft-Dependency-Agent<br><br>/OPT/Microsoft/Dependency-Agent/bin/Microsoft-Dependency-Agent-Manager |
| Ficheiros de armazenamento binário | /var/OPT/Microsoft/Dependency-Agent/Storage |

### <a name="installation-script-examples"></a>Exemplos de script de instalação

Para facilmente implementar o agente de dependência em vários servidores ao mesmo tempo, ajuda a para utilizar um script. Pode utilizar os seguintes exemplos de script para transferir e instalar o agente de dependência no Windows ou Linux.

#### <a name="powershell-script-for-windows"></a>Script do PowerShell para Windows

```PowerShell

Invoke-WebRequest &quot;https://aka.ms/dependencyagentwindows&quot; -OutFile InstallDependencyAgent-Windows.exe

.\InstallDependencyAgent-Windows.exe /S

```

#### <a name="shell-script-for-linux"></a>Script de shell para Linux

```
wget --content-disposition https://aka.ms/dependencyagentlinux -O InstallDependencyAgent-Linux64.bin
```

```
sh InstallDependencyAgent-Linux64.bin -s
```

### <a name="desired-state-configuration"></a>Configuração do Estado Pretendido

Para implementar o agente de dependência através da configuração de estado pretendida, pode utilizar o módulo de xPSDesiredStateConfiguration e um bit de código como o seguinte:

```
Import-DscResource -ModuleName xPSDesiredStateConfiguration

$DAPackageLocalPath = &quot;C:\InstallDependencyAgent-Windows.exe&quot;



Node $NodeName

{

    # Download and install the Dependency Agent

    xRemoteFile DAPackage

    {

        Uri = &quot;https://aka.ms/dependencyagentwindows&quot;

        DestinationPath = $DAPackageLocalPath

        DependsOn = &quot;[Package]OI&quot;

    }

    xPackage DA

    {

        Ensure=&quot;Present&quot;

        Name = &quot;Dependency Agent&quot;

        Path = $DAPackageLocalPath

        Arguments = '/S'

        ProductId = &quot;&quot;

        InstalledCheckRegKey = &quot;HKEY\_LOCAL\_MACHINE\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\DependencyAgent&quot;

        InstalledCheckRegValueName = &quot;DisplayName&quot;

        InstalledCheckRegValueData = &quot;Dependency Agent&quot;

    }

}

```
### <a name="uninstall-the-dependency-agent"></a>Desinstale o agente de dependência

Utilize as secções seguintes para o ajudar a remover o agente de dependência.

#### <a name="uninstall-the-dependency-agent-on-windows"></a>Desinstale o agente de dependência no Windows

Um administrador pode desinstalar o agente de dependência para o Windows através do painel de controlo.

Um administrador também pode executar %Programfiles%\Microsoft dependência Agent\Uninstall.exe para desinstalar o agente de dependência.

#### <a name="uninstall-the-dependency-agent-on-linux"></a>Desinstale o agente de dependência no Linux

Para desinstalar completamente o agente de dependência do Linux, tem de remover o próprio agente e o conector, que é instalado automaticamente com o agente. Pode desinstalar ambos usando o seguinte comando único:

```
rpm -e dependency-agent dependency-agent-connector
```

## <a name="management-packs"></a>Pacotes de gestão

Quando os dados por fio está ativado numa área de trabalho de análise de registos, é enviado um pacote de gestão de 300 KB para todos os servidores do Windows na área de trabalho. Se estiver a utilizar os agentes do System Center Operations Manager num [grupo de gestão ligado](log-analytics-om-agents.md), o pacote de gestão de Monitor de dependência for implementado a partir do System Center Operations Manager. Se os agentes estarem ligados diretamente, análise de registos fornece o pacote de gestão.

O pacote de gestão é denominado Microsoft.IntelligencePacks.ApplicationDependencyMonitor. É escrito: %Programfiles%\Microsoft monitorização Agent\Agent\Health serviço State\Management pacotes. É a origem de dados que utiliza o pacote de gestão: % programa files%\Microsoft monitorização Agent\Agent\Health serviço State\Resources&lt;AutoGeneratedID&gt;\ Microsoft.EnterpriseManagement.Advisor.ApplicationDependencyMonitorDataSource.dll.

## <a name="using-the-solution"></a>Utilizar a solução

**Instalar e configurar a solução**

Utilize as seguintes informações para instalar e configurar a solução.

- A solução de dados por fio adquire dados a partir de computadores que executam o Windows Server 2012 R2, Windows 8.1 e sistemas operativos posteriores.
- É necessário o Microsoft .NET Framework 4.0 ou posterior em computadores onde pretende adquirir durante a transmissão de dados do.
- Adicionar a solução de dados por fio à sua área de trabalho de análise de registos com o processo descrito no [soluções de análise de registos adicionar da galeria do soluções](log-analytics-add-solutions.md). Não há nenhuma configuração adicional.
- Se pretender ver os dados de transmissão para uma solução específica, tem de ter a solução já adicionada à sua área de trabalho.

Depois de instalar agentes e instalar a solução, o mosaico 2.0 de dados durante a transmissão é apresentado na sua área de trabalho.

> [!NOTE]
> Atualmente, tem de utilizar o portal do OMS para ver os dados de transmissão. Não é possível utilizar o portal do Azure para ver os dados de transmissão.

![Mosaico de dados por fio](./media/log-analytics-wire-data/wire-data-tile.png)

## <a name="using-the-wire-data-20-solution"></a>Utilizando a solução de transmissão dados 2.0

No portal do OMS, clique em de **durante a transmissão dados 2.0** mosaico para abrir o dashboard de dados por fio. O dashboard inclui os painéis na seguinte tabela. Cada painel lista até 10 itens correspondentes aos critérios de nesse painel para o âmbito especificado e o intervalo de tempo. Pode executar uma pesquisa de registo que devolve todos os registos clicando **ver todos os** na parte inferior do painel ou ao clicar no cabeçalho do painel.

| **Painel** | **Descrição** |
| --- | --- |
| Agentes que capturam tráfego de rede | Mostra o número de agentes que está a capturar o tráfego de rede e apresenta uma lista de principais 10 computadores que estão a capturar o tráfego. Clique no número para executar uma pesquisa de registo para <code>Type:WireData &#124; measure Sum(TotalBytes) by Computer &#124; top 500000</code>. Clique num computador na lista para executar uma pesquisa de registo devolver o número total de bytes capturadas. |
| Sub-redes locais | Mostra o número de sub-redes locais que tenham detetados agentes.  Clique no número para executar uma pesquisa de registo para <code>Type:WireData &#124; Measure Sum(TotalBytes) by LocalSubnet</code> que apresenta uma lista de todas as sub-redes com o número de bytes enviados através de cada um deles. Clique uma sub-rede na lista para executar uma pesquisa de registo devolver o número total de bytes enviados através de sub-rede. |
| Protocolos de nível de aplicação | Mostra o número de protocolos de nível de aplicação em utilização, tal como detetados por agentes. Clique no número para executar uma pesquisa de registo para <code>Type:WireData &#124; Measure Sum(TotalBytes) by ApplicationProtocol</code>. Clique num protocolo para executar uma pesquisa de registo devolver o número total de bytes enviados através do protocolo. |

[!include[log-analytics-log-search-nextgeneration](../../includes/log-analytics-log-search-nextgeneration.md)]

![Dashboard de dados de transmissão](./media/log-analytics-wire-data/wire-data-dash.png)

Pode utilizar o **agentes que capturam tráfego de rede** painel para determinar a quantidade largura de banda de rede está a ser consumida pelos computadores. Este painel pode ajudar a encontrar facilmente o _chattiest_ computador no seu ambiente. Esses computadores podem estar sobrecarregados atuar anormalmente ou mais recursos de rede que o habitual a utilizar.

![exemplo de pesquisa de registo](./media/log-analytics-wire-data/log-search-example01.png)

Da mesma forma, pode utilizar o **sub-redes locais** painel para determinar a quantidade tráfego de rede é mover através do seu sub-redes. Os utilizadores, muitas vezes, definir as sub-redes em torno áreas críticas para as aplicações. Este painel oferece uma vista para esses áreas.

![exemplo de pesquisa de registo](./media/log-analytics-wire-data/log-search-example02.png)

O **protocolos de nível de aplicação** painel é útil porque é útil saber quais são protocolos em utilização. Por exemplo, poderá esperar SSH não ser utilizados no seu ambiente de rede. Ver as informações disponíveis no painel pode rapidamente confirmar ou disprove a expectativa.

![exemplo de pesquisa de registo](./media/log-analytics-wire-data/log-search-example03.png)

Neste exemplo, foi Desagregue para detalhes SSH para ver quais os computadores que estão a utilizar o SSH e muitos outros detalhes de comunicação.

![partilhar resultados da pesquisa](./media/log-analytics-wire-data/ssh-details.png)

Também é útil saber se o tráfego de protocolo é aumentar ou diminuir ao longo do tempo. Por exemplo, se estiver a aumentar a quantidade de dados que está a ser transmitidos por uma aplicação, poderá que ser algo que deve ter conhecimento, ou que encontrará noteworthy.

## <a name="input-data"></a>Dados de entrada

Recolhe os dados por fio metadados sobre o tráfego de rede com os agentes que tiver ativado. Cada agente envia dados sobre a cada 15 segundos.

## <a name="output-data"></a>dados de saída

Um registo com um tipo de _WireData_ é criada para cada tipo de dados de entrada. Registos de WireData têm propriedades mostradas na tabela seguinte:

| Propriedade | Descrição |
|---|---|
| Computador | Nome do computador em que foram recolhidos dados |
| TimeGenerated | Tempo do registo |
| LocalIP | Endereço IP do computador local |
| SessionState | Ligado ou desligado |
| receivedBytes | Quantidade de bytes recebidos |
| ProtocolName | Nome do protocolo de rede utilizado |
| IPVersion | Versão do IP |
| Direção | Entrada ou de saída |
| MaliciousIP | Endereço IP de uma origem de malicioso conhecido |
| Gravidade | Gravidade potencialmente maligno |
| RemoteIPCountry | País do endereço IP remoto |
| ManagementGroupName | Nome do grupo de gestão do Operations Manager |
| SourceSystem | Em que foram recolhidos dados de origem |
| SessionStartTime | Hora de início de sessão |
| SessionEndTime | Hora de fim da sessão |
| LocalSubnet | Em que foram recolhidos dados de sub-rede |
| LocalPortNumber | Número de porta local |
| RemoteIP | Endereço IP remoto utilizado pelo computador remoto |
| RemotePortNumber | Número de porta utilizado pelo endereço IP remoto |
| ID de sessão | Um valor exclusivo que identifica a sessão de comunicações entre dois endereços IP |
| sentBytes | Número de bytes enviados |
| TotalBytes | Número total de bytes enviados durante a sessão |
| ApplicationProtocol | Tipo de protocolo de rede utilizado   |
| ID do processo | ID de processo do Windows |
| ProcessName | Nome de ficheiro e caminho do processo |
| RemoteIPLongitude | Valor de longitude IP |
| RemoteIPLatitude | Valor de latitude do IP |


## <a name="next-steps"></a>Passos seguintes

- [Pesquisar registos](log-analytics-log-searches.md) para ver registos de pesquisa de dados de transmissão detalhada.

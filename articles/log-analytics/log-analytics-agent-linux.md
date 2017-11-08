---
title: "Ligar as computadores com Linux a análise de registos do Azure | Microsoft Docs"
description: "Este artigo descreve como ligar computadores Linux alojados no Azure, outra nuvem ou no local para análise de registos com o agente do OMS para Linux."
services: log-analytics
documentationcenter: 
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: 
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/07/2017
ms.author: magoedte
ms.openlocfilehash: 56c666d1a18937df21a6aca8acde87beda1cad8e
ms.sourcegitcommit: 0930aabc3ede63240f60c2c61baa88ac6576c508
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2017
---
# <a name="connect-your-linux-computers-to-log-analytics"></a>Ligar as computadores com Linux a análise de registos 

Com o Log Analytics do Azure, pode recolher e agir sobre dados gerados a partir de computadores com Linux e soluções de contentor como Docker, que reside no seu centro de dados no local, como servidores físicos ou máquinas virtuais, máquinas virtuais num serviço alojado na nuvem como Amazon Web Services (AWS) ou o Microsoft Azure. Também pode utilizar as soluções de gestão disponíveis na [da automatização do Azure](../automation/automation-intro.md) , tais como controlo de alterações, para identificar as alterações de configuração e gestão de atualizações para gerir atualizações de software para gerir o ciclo de vida do Linux VMs. 

O agente do OMS para Linux comunica saída com a análise de registos e os serviços de automatização do Azure através de TCP da porta 443 e se o computador liga ao servidor de firewall ou proxy para comunicar através da Internet, reveja [configurar o agente para utilização com um servidor proxy ou Gateway do OMS](#configuring-the-agent-for-use-with-a-proxy-server-or-oms-gateway) para compreender que configuração é alterado será tem de ser aplicadas.  Se estiver a monitorizar o computador com o System Center 2016 - Operations Manager ou do Operations Manager 2012 R2, pode ser multihomed com o serviço de análise de registos para recolher dados e reencaminhá-los para o serviço e ainda ser monitorizados pelo Operations Manager.  Computadores com Linux monitorizados por um grupo de gestão do Operations Manager que está integrado com o Log Analytics (atualmente referido Operations Management Suite na consola de operações do Operations Manager) não receber a configuração de origens de dados e reencaminhar dados recolhidos através do grupo de gestão.  O agente do OMS não pode ser configurado para relatar a mais do que uma área de trabalho de análise de registos.  

Se as políticas de segurança de TI não permitir que os computadores na sua rede para ligar à Internet, o agente pode ser configurado para ligar ao Gateway para receber as informações de configuração e enviar os dados recolhidos consoante a solução que tiver ativado o OMS. Para obter mais informações e os passos sobre como configurar o agente Linux do OMS para comunicar através de um Gateway do OMS para os serviços, consulte [ligar computadores ao OMS utilizando o Gateway do OMS](log-analytics-oms-gateway.md).  

O diagrama seguinte ilustra a ligação entre os computadores com Linux geridos por agente e análise de registos, incluindo as portas e direção.

![comunicação do agente direta com diagrama de serviços do Azure](./media/log-analytics-agent-linux/log-analytics-agent-linux-communication.png)

## <a name="system-requirements"></a>Requisitos de sistema
Antes de começar, reveja os detalhes seguintes para verificar que cumpre os pré-requisitos.

### <a name="supported-linux-operating-systems"></a>Sistemas operativos Linux suportados
As distribuições de Linux seguintes são suportadas oficialmente.  No entanto, o agente do OMS para Linux também podem executar nos outras distribuições não listadas.

* Linux Amazon 2012.09 para 2015.09 (x86/x64)
* CentOS Linux 5, 6 e 7 (x86/x64)
* Oracle Linux 5, 6 e 7 (x86/x64)
* Red Hat Enterprise Linux Server 5, 6 e 7 (x86/x64)
* Debian GNU/Linux 6, 7 e 8 (x86/x64)
* Ubuntu 12.04 LTS, 14.04 LTS, 15.04, 15.10, 16.04 LTS (x86/x64)
* SUSE Linux Enterprise Server 11 e 12 (x86/x64)

### <a name="network"></a>Rede
As informações abaixo lista as informações de configuração de proxy e de firewall necessárias para o agente do Linux comunicar com a análise de registos e a automatização do Azure. O tráfego é de saída da sua rede para o serviço. 

|Recursos do Agente| Portas |  
|------|---------|  
|*.ods.opinsights.azure.com | Porta 443|   
|*.oms.opinsights.azure.com | Porta 443|   
|*.blob.Core.Windows.NET/ | Porta 443|   
|*.azure-automation.net | Porta 443|  

### <a name="package-requirements"></a>Requisitos do pacote

 **Pacote necessário**   | **Descrição**   | **Versão mínima**
--------------------- | --------------------- | -------------------
Glibc | Biblioteca de C GNU   | 2.5-12 
OpenSSL | Bibliotecas de OpenSSL | 0.9.8E ou 1.0
Curl | cURL web cliente | 7.15.5
Python ctypes | | 
PAM | Módulos de autenticação incorporável | 

> [!NOTE]
>  Rsyslog ou syslog ng são necessárias para recolher as mensagens syslog. O daemon de syslog de predefinição na versão 5 do Red Hat Enterprise Linux, CentOS, Oracle Linux versão e (sysklog) não é suportado para a recolha de eventos do syslog. Para recolher dados de syslog nesta versão destes distribuições, o daemon de rsyslog deve ser instalado e configurado para substituir sysklog, 

O agente inclui vários pacotes. O ficheiro de versão contém os seguintes pacotes disponíveis ao executar o pacote de shell com `--extract`:

**Pacote** | **Versão** | **Descrição**
----------- | ----------- | --------------
omsagent | 1.4.0 | O agente do Operations Management Suite para Linux
omsconfig | 1.1.1 | Agente de configuração para o agente do OMS
OMI | 1.2.0 | Abra o Management Infrastructure (OMI) - uma simples de servidor CIM
scx | 1.6.3 | Fornecedores de CIM OMI para as métricas de desempenho do sistema operativo
Apache cimprov | 1.0.1 | Fornecedor para OMI de monitorização do desempenho Apache HTTP Server. Instalado se for detetado Apache HTTP Server.
MySQL cimprov | 1.0.1 | Desempenho do servidor de MySQL monitorização fornecedor para OMI. Instalado se for detetado MySQL/MariaDB servidor.
docker cimprov | 1.0.0 | Fornecedor de docker para OMI. Instalado se for detetado Docker.

### <a name="compatibility-with-system-center-operations-manager"></a>Compatibilidade com o System Center Operations Manager
O agente do OMS para Linux partilha binários do agente com o agente do System Center Operations Manager. Se instalar o agente do OMS para Linux num sistema atualmente gerido pelo Operations Manager, atualiza os pacotes OMI e SCX no computador para uma versão mais recente. Nesta versão, o OMS e o System Center 2016 - Operations Manager/Operations Manager 2012 R2 agentes para Linux são compatíveis. 

> [!NOTE]
> System Center 2012 SP1 e versões anteriores atualmente não são suportados ou compatível com o agente do OMS para Linux.<br>
> Se o agente do OMS de Linux está instalado num computador que não monitorizados pelo Operations Manager, e, em seguida, pretende monitorizar o computador com o Operations Manager, tem de modificar o [configuração OMI](#enable-the-oms-agent-for-linux-to-report-to-system-center-operations-manager) antes da deteção o computador. **Este passo é *não* necessário se o agente do Operations Manager está instalado antes do agente do OMS para Linux.**

### <a name="system-configuration-changes"></a>Alterações de configuração do sistema
Depois de instalar o agente do OMS para pacotes de Linux, são aplicadas as seguintes alterações de configuração de todo sistema adicional. Estes artefactos são removidos quando o pacote de omsagent é desinstalado.

* Um utilizador sem privilégios com o nome: `omsagent` é criado. Esta é a conta que o daemon de omsagent é executada.
* É criado um ficheiro de "incluem" de sudoers em /etc/sudoers.d/omsagent. Isto autoriza omsagent para reiniciar os daemons syslog e omsagent. Se o sudo "incluem" diretivas não são suportadas na versão instalada do sudo, estas entradas são escritas /etc/sudoers.
* A configuração do syslog é modificada para reencaminhar um subconjunto de eventos para o agente. Para obter mais informações, consulte o **configurar recolha de dados** secção abaixo

### <a name="upgrade-from-a-previous-release"></a>Atualizar a partir de uma versão anterior
Atualizar a partir de versões anteriores daquele 1.0.0-47 é suportada nesta versão. A executar a instalação com o `--upgrade` comando atualiza todos os componentes do agente para a versão mais recente.

## <a name="installing-the-agent"></a>Instalação do agente

Esta secção descreve como instalar o agente do OMS para Linux manualmente utilizando um bunndle, que contém pacotes Debian e RPM para cada um dos componentes do agente.  Pode ser instalado diretamente ou extraído para obter os pacotes individuais.  Se estiver a planear instalar o agente no VM Linux do Azure, consulte o tópico seguinte [recolher dados sobre máquinas virtuais do Azure](log-analytics-quick-collect-azurevm.md) para saber como instalar o agente utilizando a extensão de VM de análise do registo.  Siga os passos na secção *ativar a extensão de VM de análise do registo*.  Para computadores com Linux alojados no seu ambiente, pode simplificar o processo de instalação utilizando um método com script descrito no artigo, [recolher dados de computadores Linux alojados no seu ambiente](log-analytics-quick-collect-linux-computer.md).  

> [!NOTE]
> Enquanto os dois artigos referenciados acima destinam-se a alguém que há de novo à análise de registos e começar a utilizar o serviço rapidamente, os passos para configurar o computador são relevantes.  Se já tiver uma área de trabalho e estiver à procura para ligar o computador Linux, selecione uma área de trabalho existente se uma VM com Linux do Azure ou para um computador alojados fora do Azure, o ID da área de trabalho de cópia e a chave para passar para o script.  

Antes de instalar o agente do OMS para Linux, tem do ID da área de trabalho e a chave para a sua área de trabalho de análise de registos.  

1. Inicie sessão no portal do Azure em [https://portal.azure.com](https://portal.azure.com). 
2. No portal do Azure, clique em **Mais serviços**, que se encontra no canto inferior esquerdo. Na lista de recursos, escreva **Log Analytics**. À medida que começa a escrever, a lista filtra com base na sua entrada. Selecione **Iniciar análise**.
3. Na lista de áreas de trabalho de análise de registos, selecione a área de trabalho que pretende que o computador para que reportem a.
3. Selecione **definições avançadas**.<br><br> ![Definições avançadas da análise de registo](media/log-analytics-quick-collect-azurevm/log-analytics-advanced-settings-01.png)<br><br>  
4. Selecione **origens ligadas**e, em seguida, selecione **servidores Linux**.   
5. O valor para a direita da **ID da área de trabalho** e **chave primária**. Copie e cole-o no seu editor favorito.  
6. Transferir a versão mais recente [agente do OMS para Linux (x64)](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/download/OMSAgent_GA_v1.4.0-45/omsagent-1.4.0-45.universal.x64.sh) ou [agente do OMS para Linux x86](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/download/OMSAgent_GA_v1.4.0-45/omsagent-1.4.0-45.universal.x86.sh) a partir do GitHub.  
7. Transfira o pacote adequado (x86 ou x64) para o seu computador Linux utilizando scp/sftp.
8. Instalar o pacote utilizando o `--install` ou `--upgrade` argumento. 

    > [!NOTE]
    > Se estiverem instalados quaisquer pacotes existentes, como quando o agente do System Center Operations Manager para Linux já está instalado, utilize o `--upgrade` argumento. Para ligar ao Operations Management Suite durante a instalação, forneça o `-w <WorkspaceID>` e `-s <Shared Key>` parâmetros.


#### <a name="to-install-and-onboard-directly"></a>Para instalar e carregar diretamente
```
sudo sh ./omsagent-<version>.universal.x64.sh --upgrade -w <workspace id> -s <shared key>
```

#### <a name="to-upgrade-the-agent-package"></a>Para atualizar o pacote de agente
```
sudo sh ./omsagent-<version>.universal.x64.sh --upgrade
```

#### <a name="to-install-and-onboard-to-a-workspace-in-us-government-cloud"></a>Para instalar e carregar para uma área de trabalho US Government nuvem
```
sudo sh ./omsagent-<version>.universal.x64.sh --upgrade -w <workspace id> -s <shared key> -d opinsights.azure.us
```

## <a name="configuring-the-agent-for-use-with-a-proxy-server-or-oms-gateway"></a>Configurar o agente para utilização com um servidor proxy ou Gateway do OMS
O agente do OMS para Linux suporta comunicar através de um servidor proxy ou Gateway do OMS para o serviço de análise de registos utilizando o protocolo HTTPS.  Autenticação básica e anónima (nome de utilizador/palavra-passe) é suportada.  

### <a name="proxy-configuration"></a>Configuração de proxy
O valor de configuração de proxy tem a seguinte sintaxe:

`[protocol://][user:password@]proxyhost[:port]`

Propriedade|Descrição
-|-
Protocolo|https
utilizador|Nome de utilizador opcional para a autenticação de proxy
palavra-passe|Palavra-passe opcional para a autenticação de proxy
proxyhost|Endereço ou o FQDN do proxy servidor/OMS Gateway
porta|Número de porta opcional para o proxy servidor/OMS Gateway

Por exemplo: `https://user01:password@proxy01.contoso.com:30443`

O servidor proxy pode ser especificado durante a instalação ou ao modificar o ficheiro de configuração proxy.conf após a instalação.   

### <a name="specify-proxy-configuration-during-installation"></a>Especificar a configuração de proxy durante a instalação
O `-p` ou `--proxy` argumento para o pacote de instalação omsagent Especifica a configuração de proxy a utilizar. 

```
sudo sh ./omsagent-<version>.universal.x64.sh --upgrade -p https://<proxy user>:<proxy password>@<proxy address>:<proxy port> -w <workspace id> -s <shared key>
```

### <a name="define-the-proxy-configuration-in-a-file"></a>Definir a configuração de proxy num ficheiro
A configuração de proxy pode ser definida nos ficheiros `/etc/opt/microsoft/omsagent/proxy.conf` e `/etc/opt/microsoft/omsagent/conf/proxy.conf `. Os ficheiros podem ser criados ou editados diretamente, mas as respetivas permissões devem ser atualizadas para conceder que ao utilizador omiuser permissão de leitura nos ficheiros. Por exemplo:
```
proxyconf="https://proxyuser:proxypassword@proxyserver01:30443"
sudo echo $proxyconf >>/etc/opt/microsoft/omsagent/proxy.conf
sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/proxy.conf
sudo chmod 600 /etc/opt/microsoft/omsagent/proxy.conf /etc/opt/microsoft/omsagent/conf/proxy.conf  
sudo /opt/microsoft/omsagent/bin/service_control restart [<workspace id>]
```

### <a name="removing-the-proxy-configuration"></a>A remover a configuração de proxy
Para remover uma configuração de proxy definida anteriormente e reverter para a ligação direta, remova o ficheiro proxy.conf:
```
sudo rm /etc/opt/microsoft/omsagent/proxy.conf /etc/opt/microsoft/omsagent/conf/proxy.conf
sudo /opt/microsoft/omsagent/bin/service_control restart 
```

## <a name="onboarding-with-log-analytics"></a>Integração com a análise de registos
Se uma ID da área de trabalho e a chave não foram fornecidas durante a instalação do pacote, o agente tem subsequentemente registado com a análise de registos.

### <a name="onboarding-using-the-command-line"></a>Integração com a linha de comandos
Execute o comando de omsadmin.sh fornecer o id da área de trabalho e a chave para a sua área de trabalho. Este comando deve ser executado como raiz (com elevação de sudo):
```
cd /opt/microsoft/omsagent/bin
sudo ./omsadmin.sh -w <WorkspaceID> -s <Shared Key>
```

### <a name="register-using-a-file"></a>Registar utilizando um ficheiro
1.  Criar o ficheiro `/etc/omsagent-onboard.conf`. O ficheiro tem de ser legível e gravável para a raiz.
`sudo vi /etc/omsagent-onboard.conf`
2.  Insira as seguintes linhas no ficheiro com o ID e a chave partilhada:

        WORKSPACE_ID=<WorkspaceID>  
        SHARED_KEY=<Shared Key>  
   
3.  Execute o seguinte comando para registar a análise de registos:`sudo /opt/microsoft/omsagent/bin/omsadmin.sh`
4.  O ficheiro for eliminado na integração com êxito.

## <a name="enable-the-oms-agent-for-linux-to-report-to-system-center-operations-manager"></a>Ativar o agente do OMS para Linux comunicar ao System Center Operations Manager
Execute os seguintes passos para configurar o agente do OMS para Linux para que reportem a um grupo de gestão do System Center Operations Manager.  

1. Edite o ficheiro`/etc/opt/omi/conf/omiserver.conf`
2. Certifique-se de que o início da linha com **httpsport =** define a porta 1270. Tal como:`httpsport=1270`
3. Reinicie o servidor OMI:`sudo /opt/omi/bin/service_control restart`

## <a name="agent-logs"></a>Registos do agente
Os registos para o agente do OMS para Linux podem ser encontrados em: `/var/opt/microsoft/omsagent/<workspace id>/log/` os registos para o programa de omsconfig (configuração do agente) podem ser encontrados em: `/var/opt/microsoft/omsconfig/log/` registos para os componentes OMI e SCX (que fornecem dados de métricas de desempenho) podem ser encontrados em:`/var/opt/omi/log/ and /var/opt/microsoft/scx/log`

### <a name="log-rotation-configuration"></a>Registo rotação configuração # #
A configuração de rodar de registo para omsagent pode ser encontrada em:`/etc/logrotate.d/omsagent-<workspace id>`

As predefinições são: 
```
/var/opt/microsoft/omsagent/<workspace id>/log/omsagent.log {
    rotate 5
    missingok
    notifempty
    compress
    size 50k
    copytruncate
}
```

## <a name="uninstalling-the-oms-agent-for-linux"></a>Desinstalar o agente do OMS para Linux
Os pacotes de agente podem ser desinstalados executando o ficheiro de .sh do pacote com o `--purge` argumento, o que remove completamente a respetiva configuração e o agente do computador.   

```
> sudo rpm -e omsconfig
> sudo rpm -e omsagent
> sudo /opt/microsoft/scx/bin/uninstall
```

## <a name="troubleshooting"></a>Resolução de problemas

### <a name="issue-unable-to-connect-through-proxy-to-log-analytics"></a>Problema: Não é possível ligar através do proxy para análise de registos

#### <a name="probable-causes"></a>Causas prováveis
* O proxy especificado durante a integração estava incorreto
* A análise de registos e pontos finais de serviço de automatização do Azure não estão na lista de permissões no seu centro de dados 

#### <a name="resolutions"></a>Resoluções
1. Reonboard para o serviço de análise de registos com o agente do OMS para Linux ao utilizar o seguinte comando com a opção `-v` ativada. Isto permite uma saída verbosa do agente que se ligam através do proxy para o serviço do OMS. 
`/opt/microsoft/omsagent/bin/omsadmin.sh -w <OMS Workspace ID> -s <OMS Workspace Key> -p <Proxy Conf> -v`

2. Reveja a secção [configurar o agente para utilização com um servidor proxy ou Gateway do OMS](#configuring the-agent-for-use-with-a-proxy-server-or-oms-gateway) para verificar que configurou corretamente o agente para comunicar através de um servidor proxy.    
* Volte a verificar que os pontos finais de serviço de análise de registos seguintes estão na lista de permissões:

    |Recursos do Agente| Portas |  
    |------|---------|  
    |*.ods.opinsights.azure.com | Porta 443|   
    |*.oms.opinsights.azure.com | Porta 443|   
    |ods.systemcenteradvisor.com | Porta 443|   
    |*.blob.Core.Windows.NET/ | Porta 443|   

### <a name="issue-you-receive-a-403-error-when-trying-to-onboard"></a>Problema: Recebe um erro 403 ao tentar carregar

#### <a name="probable-causes"></a>Causas prováveis
* Data e hora está incorreta no servidor do Linux 
* ID de área de trabalho e a chave de área de trabalho utilizado não estão corretos

#### <a name="resolution"></a>Resolução

1. Verifique a hora no seu servidor Linux com a data de comando. Se a hora é + /-15 minutos da hora atual, em seguida, integração irá falhar. Para correto isto atualizar a data e/ou o fuso horário do servidor Linux. 
2. Certifique-se de que instalou a versão mais recente do agente do OMS para Linux.  A versão mais recente notifica agora, se o tempo de desfasamento está a causar a falha de integração.
3. Reonboard utilizando correto ID e a chave de área de trabalho seguir as instruções de instalação anterior deste tópico.

### <a name="issue-you-see-a-500-and-404-error-in-the-log-file-right-after-onboarding"></a>Problema: Vir um erro 404 e 500 no ficheiro de registo imediatamente após a integração
Este é um problema conhecido que ocorre no primeiro carregamento de dados de Linux para uma área de trabalho de análise de registos. Isto não afeta os dados que está a ser enviada ou serviço experiência.

### <a name="issue-you-are-not-seeing-any-data-in-the-azure-portal"></a>Problema: Não vir quaisquer dados no portal do Azure

#### <a name="probable-causes"></a>Causas prováveis

- Falha de integração para o serviço de análise de registos
- Ligação ao serviço de análise de registos está bloqueada
- Agente do OMS Linux dados de cópia de segurança

#### <a name="resolutions"></a>Resoluções
1. Verifique se a integração o serviço de análise de registos foi concluída com êxito, verificando se o ficheiro seguinte:`/etc/opt/microsoft/omsagent/<workspace id>/conf/omsadmin.conf`
2. Reonboard utilizando o `omsadmin.sh` instruções da linha de comandos
3. Se utilizar um proxy, consulte os passos de resolução de proxy fornecidos anteriormente.
4. Em alguns casos, quando o agente do OMS para Linux não é possível comunicar com o serviço, dados no agente é colocado em fila para o tamanho de memória intermédia completa, o que é 50 MB. O agente do OMS para Linux deve ser reiniciado, executando o seguinte comando: `/opt/microsoft/omsagent/bin/service_control restart [<workspace id>]`. 

    >[!NOTE]
    >Este problema ser corrigido 1.1.0-28 de versão do agente e mais tarde.

### <a name="issue-omsagent-creates-excessive-number-of-user-process-on-computer-and-never-terminates-them"></a>Problema: OMSAgent cria um número excessivo de processo do utilizador no computador e nunca termina-las
Quando ativa as soluções de gestão que suportam a gerir as VMs do Linux, inicia um número de processos no agente Linux. Mas antes do processo termina, o outro processo é iniciado devido a um problema conhecido. 

#### <a name="resolutions"></a>Resoluções
Para alterar o número de processos de utilizador que podem ser geradas pelo OMSAgent, configure o agente utilizando omsadmin.sh.  O número de processos que são gerados por predefinição é 75 e antes de alterar o limite, primeiro, deve executar o seguinte comando para ver quantas OMSAgent processos atualmente em execução: `ps aux | grep -E '^omsagent' | wc -l`.  
Pode verificar que o limite atual está definido como executando o seguinte comando:`cat /etc/security/limits.conf | grep -E '^omsagent'`

Utilize os seguintes comandos para optar por configurar um limite de processo personalizado ou para definir o limite de processo no respectivo valor predefinido.

1. Para definir o limite de processo para OMSAgent: `sudo /opt/microsoft/omsagent/bin/omsadmin.sh -n <specific number limit>`.<br>Tenha em atenção que o limite mínimo que pode ser definido para 5.  

2. Para definir o limite de processo para OMSAgent novamente para o valor predefinido:`sudo /opt/microsoft/omsagent/bin/omsadmin.sh -N`

Certifique-se de que a nova definição foi aplicada ao executar o seguinte comando: `cat /etc/security/limits.conf | grep -E '^omsagent'`.  Se não vir a nova configuração aplicada, poderá ser porque a definir o limite de processo demasiado baixa.  

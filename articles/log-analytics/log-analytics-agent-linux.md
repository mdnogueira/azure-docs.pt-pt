---
title: Ligar as computadores com Linux no Operations Management Suite (OMS) | Microsoft Docs
description: Este artigo descreve como ligar computadores Linux alojados no Azure, outra nuvem ou no local com o agente do OMS para Linux do OMS.
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
ms.date: 09/29/2017
ms.author: magoedte
ms.openlocfilehash: c9902e1b8644c2b0a894f9cde98f2056564775c7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="connect-your-linux-computers-to-operations-management-suite-oms"></a>Ligar as computadores com Linux no Operations Management Suite (OMS) 

Com o Microsoft Operations Management Suite (OMS), pode recolher e atuar sobre dados gerados a partir de computadores com Linux e soluções de contentor como Docker, que reside no seu centro de dados no local, como servidores físicos ou máquinas virtuais, máquinas virtuais num serviço alojado na nuvem como Amazon Web Services (AWS) ou o Microsoft Azure. Também pode utilizar as soluções de gestão disponíveis na OMS, tais como controlo de alterações, para identificar as alterações de configuração e gestão de atualizações para gerir atualizações de software para gerir o ciclo de vida das suas VMs do Linux. 

O agente do OMS para Linux comunica saído com o serviço do OMS através da porta TCP 443 e, se o computador liga ao servidor de firewall ou proxy para comunicar através da Internet, reveja [configurar o agente para utilização com um servidor de proxy HTTP ou do OMS Gateway](#configuring-the-agent-for-use-with-an-http-proxy-server-or-oms-gateway) para compreender que configuração é alterado será tem de ser aplicadas.  Se estiver a monitorizar o computador com o System Center 2016 - Operations Manager ou do Operations Manager 2012 R2, pode ser multihomed com o serviço do OMS para recolher dados e reencaminhá-los para o serviço e ainda ser monitorizados pelo Operations Manager.  Computadores com Linux monitorizados por um grupo de gestão do Operations Manager que está integrado com o OMS não receber a configuração de origens de dados e reencaminhar recolhidos dados através do grupo de gestão.  O agente do OMS não pode ser configurado para relatar a mais do que uma área de trabalho.  

Se as políticas de segurança de TI não permitir que os computadores na sua rede para ligar à Internet, o agente pode ser configurado para ligar ao Gateway para receber as informações de configuração e enviar os dados recolhidos consoante a solução que tiver ativado o OMS. Para obter mais informações e os passos sobre como configurar o agente Linux do OMS para comunicar através de um Gateway do OMS para o serviço do OMS, consulte [ligar computadores ao OMS utilizando o Gateway do OMS](log-analytics-oms-gateway.md).  

O diagrama seguinte ilustra a ligação entre os computadores com Linux geridos por agente e o OMS, incluindo as portas e direção.

![comunicação do agente direta com diagrama do OMS](./media/log-analytics-agent-linux/log-analytics-agent-linux-communication.png)

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
As informações abaixo lista as informações de configuração de proxy e de firewall necessárias para o agente do Linux comunicar com o OMS. O tráfego é de saída da sua rede para o serviço do OMS. 

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
omsagent | 1.4.1 | O agente do Operations Management Suite para Linux
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

* Um utilizador sem privilégios com o nome: `omsagent` é criado. O daemon de omsagent é executado como esta conta.
* É criado um ficheiro de "incluem" de sudoers em /etc/sudoers.d/omsagent. Este ficheiro autoriza omsagent para reiniciar os daemons syslog e omsagent. Se o sudo "incluem" diretivas não são suportadas na versão instalada do sudo, estas entradas são escritas /etc/sudoers.
* A configuração do syslog é modificada para reencaminhar um subconjunto de eventos para o agente. Para obter mais informações, consulte o **configurar recolha de dados** secção abaixo.

### <a name="upgrade-from-a-previous-release"></a>Atualizar a partir de uma versão anterior
Atualizar a partir de versões anteriores daquele 1.0.0-47 é suportada nesta versão. A executar a instalação com o `--upgrade` comando atualiza todos os componentes do agente para a versão mais recente.

## <a name="installing-the-agent"></a>Instalação do agente

Esta secção descreve como instalar o agente do OMS para Linux utilizando um bunndle, que contém pacotes Debian e RPM para cada um dos componentes do agente.  Pode ser instalado diretamente ou extraído para obter os pacotes individuais.  

Primeiro tem do ID da área de trabalho OMS e a chave, o que pode encontrar ao mudar para o [portal clássico do OMS](https://mms.microsoft.com).  No **descrição geral** página, no menu superior, selecione **definições**e, em seguida, navegue para **ligado servidores de Sources\Linux**.  Consulte o valor para a direita da **ID da área de trabalho** e **chave primária**.  Copie e cole-o no seu editor favorito.    

1. Transferir a versão mais recente [agente do OMS para Linux (x64)](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/download/OMSAgent_GA_v1.4.1-45/omsagent-1.4.1-45.universal.x64.sh) ou [agente do OMS para Linux x86](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/download/OMSAgent_GA_v1.4.1-45/omsagent-1.4.1-45.universal.x86.sh) a partir do GitHub.  
2. Transfira o pacote adequado (x86 ou x64) para o seu computador Linux utilizando scp/sftp.
3. Instalar o pacote utilizando o `--install` ou `--upgrade` argumento. 

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
O agente do OMS para Linux suporta comunicar através de um servidor proxy ou Gateway do OMS para o serviço do OMS utilizando o protocolo HTTPS.  Autenticação básica e anónima (nome de utilizador/palavra-passe) é suportada.  

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

## <a name="onboarding-with-operations-management-suite"></a>Integração com o Operations Management Suite
Se uma ID da área de trabalho e a chave não foram fornecidas durante a instalação do pacote, o agente tem subsequentemente registado com o Operations Management Suite.

### <a name="onboarding-using-the-command-line"></a>Integração com a linha de comandos
Execute o comando de omsadmin.sh fornecer o ID da área de trabalho e a chave para a sua área de trabalho. Este comando deve ser executado como raiz (com elevação de sudo):
```
cd /opt/microsoft/omsagent/bin
sudo ./omsadmin.sh -w <WorkspaceID> -s <Shared Key>
```

### <a name="onboarding-using-a-file"></a>Integração utilizando um ficheiro
1.  Criar o ficheiro `/etc/omsagent-onboard.conf`. O ficheiro tem de ser legível e gravável para a raiz.
`sudo vi /etc/omsagent-onboard.conf`
2.  Insira as seguintes linhas no ficheiro com o ID e a chave partilhada:

        WORKSPACE_ID=<WorkspaceID>  
        SHARED_KEY=<Shared Key>  
   
3.  Execute o seguinte comando para Onboard OMS:`sudo /opt/microsoft/omsagent/bin/omsadmin.sh`
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

### <a name="issue-unable-to-connect-through-proxy-to-oms"></a>Problema: Não é possível ligar através do proxy para OMS

#### <a name="probable-causes"></a>Causas prováveis
* O proxy especificado durante a integração estava incorreto
* Os pontos finais de serviço de OMS não são whitelistested no seu centro de dados 

#### <a name="resolutions"></a>Resoluções
1. Reonboard para o serviço do OMS com o agente do OMS para Linux ao utilizar o seguinte comando com a opção `-v` ativada. Este settubg permite uma saída verbosa do agente que se ligam através do proxy para o serviço do OMS. 
`/opt/microsoft/omsagent/bin/omsadmin.sh -w <OMS Workspace ID> -s <OMS Workspace Key> -p <Proxy Conf> -v`

2. Reveja a secção [configurar o agente para utilização com um servidor proxy ou Gateway do OMS](#configuring the-agent-for-use-with-a-proxy-server-or-oms-gateway) para verificar que configurou corretamente o agente para comunicar através de um servidor proxy.    
* Volte a verificar se os seguintes pontos finais de serviço do OMS estão na lista de permissões:

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
Este erro é um problema conhecido que ocorre no primeiro carregamento de dados de Linux para uma área de trabalho do OMS. Este erro não afeta os dados que está a ser enviada ou serviço experiência.

### <a name="issue-you-are-not-seeing-any-data-in-the-oms-portal"></a>Problema: Não vir quaisquer dados no portal do OMS

#### <a name="probable-causes"></a>Causas prováveis

- Falha de integração para o serviço do OMS
- Ligação ao serviço do OMS está bloqueada
- Agente do OMS Linux dados de cópia de segurança

#### <a name="resolutions"></a>Resoluções
1. Verifique se a integração de serviço do OMS foi concluída com êxito, verificando se o ficheiro seguinte:`/etc/opt/microsoft/omsagent/<workspace id>/conf/omsadmin.conf`
2. Reonboard utilizando o `omsadmin.sh` instruções da linha de comandos
3. Se utilizar um proxy, consulte os passos de resolução de proxy fornecidos anteriormente.
4. Em alguns casos, quando o agente do OMS para Linux não é possível comunicar com o serviço do OMS, dados no agente é colocado em fila para o tamanho de memória intermédia completa, o que é 50 MB. O agente do OMS para Linux deve ser reiniciado, executando o seguinte comando: `/opt/microsoft/omsagent/bin/service_control restart [<workspace id>]`. 

    >[!NOTE]
    >Este problema ser corrigido 1.1.0-28 de versão do agente e mais tarde.


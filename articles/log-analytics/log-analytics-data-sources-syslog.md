---
title: "Recolher e analisar as mensagens Syslog na análise de registos do OMS | Microsoft Docs"
description: "Syslog é um protocolo de registo de eventos que é comum para Linux. Este artigo descreve como configurar a recolha de mensagens Syslog na análise de registos e os detalhes dos registos que criarem no repositório de OMS."
services: log-analytics
documentationcenter: 
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: f1d5bde4-6b86-4b8e-b5c1-3ecbaba76198
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/28/2017
ms.author: magoedte;bwren
ms.openlocfilehash: 061c32fe39530f8b67899b1b9e1104e7fe006380
ms.sourcegitcommit: 5735491874429ba19607f5f81cd4823e4d8c8206
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2017
---
# <a name="syslog-data-sources-in-log-analytics"></a>Origens de dados de syslog no Log Analytics
Syslog é um protocolo de registo de eventos que é comum para Linux.  Aplicações vai enviar mensagens que podem ser armazenadas no computador local ou entregar para um coletor Syslog.  Quando é instalado o agente do OMS para Linux, configura o daemon de Syslog local para reencaminhar mensagens para o agente.  O agente, em seguida, envia a mensagem para análise de registos em que é criado um registo correspondente no repositório de OMS.  

> [!NOTE]
> Análise de registos suporta a coleção de mensagens enviadas pelo rsyslog ou syslog ng, onde rsyslog é o daemon de predefinição. O daemon de syslog de predefinição na versão 5 do Red Hat Enterprise Linux, CentOS, Oracle Linux versão e (sysklog) não é suportado para a recolha de eventos do syslog. Para recolher dados de syslog nesta versão destes distribuições, o [rsyslog daemon](http://rsyslog.com) deve ser instalado e configurado para substituir sysklog.
>
>

![Coleção de syslog](media/log-analytics-data-sources-syslog/overview.png)

## <a name="configuring-syslog"></a>Configurar Syslog
O agente do OMS para Linux só irá recolher eventos com as gravidades especificadas na respetiva configuração e instalações.  Pode configurar Syslog através do portal do OMS ou ao gerir ficheiros de configuração nos seus agentes Linux.

### <a name="configure-syslog-in-the-oms-portal"></a>Configurar Syslog no portal do OMS
Configurar o Syslog do [menu dados nas definições de análise do registo](log-analytics-data-sources.md#configuring-data-sources).  Esta configuração é entregue ao ficheiro de configuração em cada agente do Linux.

Pode adicionar uma nova instalação, escrevendo no respetivo nome e clicar em  **+** .  Para cada função, apenas as mensagens com as gravidades selecionadas serão recolhidas.  Verifique as gravidades para a instalações específica que pretende recolher.  Não é possível fornecer quaisquer critérios adicionais para filtrar mensagens.

![Configurar o Syslog](media/log-analytics-data-sources-syslog/configure.png)

Por predefinição, todas as alterações de configuração são automaticamente enviadas por push para todos os agentes.  Se pretender configurar Syslog manualmente em cada agente do Linux, em seguida, desmarque a caixa de *aplicar configuração abaixo aos meus computadores Linux*.

### <a name="configure-syslog-on-linux-agent"></a>Configurar Syslog no agente Linux
Quando o [agente do OMS está instalado num cliente Linux](log-analytics-linux-agents.md), instala um ficheiro de configuração de syslog predefinido que define a instalação e a gravidade das mensagens que são recolhidos.  Pode modificar este ficheiro para alterar a configuração.  O ficheiro de configuração é diferente consoante o daemon de Syslog que tem instalado o cliente.

> [!NOTE]
> Se editar a configuração de syslog, tem de reiniciar o daemon de syslog para que as alterações entrem em vigor.
>
>

#### <a name="rsyslog"></a>Rsyslog
O ficheiro de configuração para rsyslog está localizado em **/etc/rsyslog.d/95-omsagent.conf**.  O seu conteúdo predefinido é mostrado abaixo.  Este recolhe as mensagens syslog enviadas do agente local para todas as instalações com um nível de aviso ou superior.

    kern.warning       @127.0.0.1:25224
    user.warning       @127.0.0.1:25224
    daemon.warning     @127.0.0.1:25224
    auth.warning       @127.0.0.1:25224
    syslog.warning     @127.0.0.1:25224
    uucp.warning       @127.0.0.1:25224
    authpriv.warning   @127.0.0.1:25224
    ftp.warning        @127.0.0.1:25224
    cron.warning       @127.0.0.1:25224
    local0.warning     @127.0.0.1:25224
    local1.warning     @127.0.0.1:25224
    local2.warning     @127.0.0.1:25224
    local3.warning     @127.0.0.1:25224
    local4.warning     @127.0.0.1:25224
    local5.warning     @127.0.0.1:25224
    local6.warning     @127.0.0.1:25224
    local7.warning     @127.0.0.1:25224

Pode remover uma função ao remover a secção do ficheiro de configuração.  Pode limitar as gravidades que são recolhidas para uma determinada instalação ao modificar a entrada desse instalações.  Por exemplo, para limitar a instalações de utilizador para as mensagens com uma gravidade de erro ou superior que faria para modificar dessa linha do ficheiro de configuração para o seguinte:

    user.error    @127.0.0.1:25224


#### <a name="syslog-ng"></a>Syslog ng
O ficheiro de configuração para syslog ng é a localização em **/etc/syslog-ng/syslog-ng.conf**.  O seu conteúdo predefinido é mostrado abaixo.  Este recolhe as mensagens syslog enviadas do agente local para todas as instalações e todas as gravidades.   

    #
    # Warnings (except iptables) in one file:
    #
    destination warn { file("/var/log/warn" fsync(yes)); };
    log { source(src); filter(f_warn); destination(warn); };

    #OMS_Destination
    destination d_oms { udp("127.0.0.1" port(25224)); };

    #OMS_facility = auth
    filter f_auth_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(auth); };
    log { source(src); filter(f_auth_oms); destination(d_oms); };

    #OMS_facility = authpriv
    filter f_authpriv_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(authpriv); };
    log { source(src); filter(f_authpriv_oms); destination(d_oms); };

    #OMS_facility = cron
    filter f_cron_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(cron); };
    log { source(src); filter(f_cron_oms); destination(d_oms); };

    #OMS_facility = daemon
    filter f_daemon_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(daemon); };
    log { source(src); filter(f_daemon_oms); destination(d_oms); };

    #OMS_facility = kern
    filter f_kern_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(kern); };
    log { source(src); filter(f_kern_oms); destination(d_oms); };

    #OMS_facility = local0
    filter f_local0_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(local0); };
    log { source(src); filter(f_local0_oms); destination(d_oms); };

    #OMS_facility = local1
    filter f_local1_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(local1); };
    log { source(src); filter(f_local1_oms); destination(d_oms); };

    #OMS_facility = mail
    filter f_mail_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(mail); };
    log { source(src); filter(f_mail_oms); destination(d_oms); };

    #OMS_facility = syslog
    filter f_syslog_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(syslog); };
    log { source(src); filter(f_syslog_oms); destination(d_oms); };

    #OMS_facility = user
    filter f_user_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(user); };
    log { source(src); filter(f_user_oms); destination(d_oms); };

Pode remover uma função ao remover a secção do ficheiro de configuração.  Pode limitar as gravidades que são recolhidas para uma função específica, removendo-os partir da respetiva lista.  Por exemplo, para limitar a instalações de utilizador para mensagens apenas alertas e críticas, seriam modificar essa secção do ficheiro de configuração para o seguinte:

    #OMS_facility = user
    filter f_user_oms { level(alert,crit) and facility(user); };
    log { source(src); filter(f_user_oms); destination(d_oms); };


### <a name="collecting-data-from-additional-syslog-ports"></a>Recolher dados de portas adicionais do Syslog
O agente do OMS escuta mensagens Syslog no cliente local na porta 25224.  Quando o agente está instalado, uma configuração de syslog predefinida é aplicada e encontrada na seguinte localização:

* Rsyslog:`/etc/rsyslog.d/95-omsagent.conf`
* Syslog-ng:`/etc/syslog-ng/syslog-ng.conf`

Pode alterar o número de porta ao criar dois ficheiros de configuração: um ficheiro de configuração FluentD e um ficheiro de ng de rsyslog ou syslog, consoante o daemon de Syslog que instalou.  

* O ficheiro de configuração FluentD deve ser um novo ficheiro, localizado em: `/etc/opt/microsoft/omsagent/conf/omsagent.d` e substitua o valor no **porta** entrada com o número de porta personalizado.

        <source>
          type syslog
          port %SYSLOG_PORT%
          bind 127.0.0.1
          protocol_type udp
          tag oms.syslog
        </source>
        <filter oms.syslog.**>
          type filter_syslog
        </filter>

* Para rsyslog, deve criar um novo ficheiro de configuração localizado em: `/etc/rsyslog.d/` e substitua o valor % SYSLOG_PORT % com o número de porta personalizado.  

    > [!NOTE]
    > Se alterar este valor no ficheiro de configuração `95-omsagent.conf`, será substituído quando o agente aplica-se uma configuração predefinida.
    >

        # OMS Syslog collection for workspace %WORKSPACE_ID%
        kern.warning              @127.0.0.1:%SYSLOG_PORT%
        user.warning              @127.0.0.1:%SYSLOG_PORT%
        daemon.warning            @127.0.0.1:%SYSLOG_PORT%
        auth.warning              @127.0.0.1:%SYSLOG_PORT%

* A configuração de syslog ng deve ser modificada por copiar a configuração de exemplo mostrada abaixo e adicionar as definições de modificação personalizadas ao fim do ficheiro de configuração do syslog ng.conf localizado no `/etc/syslog-ng/`.  Efetue **não** utilizar a etiqueta predefinida **% WORKSPACE_ID % _oms** ou **% WORKSPACE_ID_OMS**, definir uma etiqueta personalizada para ajudar a distinguir as suas alterações.  

    > [!NOTE]
    > Se modificar os valores predefinidos no ficheiro de configuração, estes serão substituídas quando o agente aplica-se uma configuração predefinida.
    >

        filter f_custom_filter { level(warning) and facility(auth; };
        destination d_custom_dest { udp("127.0.0.1" port(%SYSLOG_PORT%)); };
        log { source(s_src); filter(f_custom_filter); destination(d_custom_dest); };

Depois de concluir as alterações, o Syslog e o agente do OMS serviço tem de ser reiniciada para garantir que as alterações de configuração em vigor.   

## <a name="syslog-record-properties"></a>Propriedades de registo do syslog
Registos de syslog tem um tipo de **Syslog** e ter as propriedades na tabela seguinte.

| Propriedade | Descrição |
|:--- |:--- |
| Computador |Computador que o evento foi recolhido a partir de. |
| Instalações |Define a parte do sistema que gerou a mensagem. |
| HostIP |Endereço IP do sistema enviar a mensagem. |
| Nome de anfitrião |Nome do sistema enviar a mensagem. |
| Nível de gravidade |Nível de gravidade do evento. |
| SyslogMessage |Texto da mensagem. |
| ID do processo |ID do processo que gerou a mensagem. |
| eventTime |Data e hora em que o evento foi gerado. |

## <a name="log-queries-with-syslog-records"></a>Consultas de registo com registos de Syslog
A tabela seguinte fornece exemplos diferentes de consultas de registo que obter registos de Syslog.

| Consulta | Descrição |
|:--- |:--- |
| Syslog |Todos os auditáveis. |
| Syslog &#124; em que nível de gravidade = = "erro" |Todos os registos de Syslog com uma gravidade de erro. |
| Syslog &#124; resumir AggregatedValue = existente pelo computador |Registos de contagem de Syslog por computador. |
| Syslog &#124; resumir AggregatedValue = existente através da instalação |Registos de contagem de Syslog das instalações. |

## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre [pesquisas de registo](log-analytics-log-searches.md) para analisar os dados recolhidos a partir de origens de dados e soluções.
* Utilize [campos personalizados](log-analytics-custom-fields.md) para analisar dados de registos de syslog em campos individuais.
* [Configurar agentes Linux](log-analytics-linux-agents.md) para recolher outros tipos de dados.

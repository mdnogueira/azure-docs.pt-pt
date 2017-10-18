---
title: "Ligar os produtos de segurança à Solução de Segurança e Auditoria Operations Management Suite (OMS) | Microsoft Docs"
description: "Este documento ajuda-o a ligar os seus produtos de segurança à Solução de Segurança e Auditoria Operations Management Suite utilizando o Common Event Format."
services: operations-management-suite
documentationcenter: na
author: YuriDio
manager: swadhwa
editor: 
ms.assetid: 46eee484-e078-4bad-8c89-c88a3508f6aa
ms.service: operations-management-suite
ms.custom: oms-security
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/18/2017
ms.author: yurid
ms.openlocfilehash: 710a1fe0ce2b7a1841187cf75f4ffb090cc161e5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="connecting-your-security-products-to-the-operations-management-suite-oms-security-and-audit-solution"></a>Ligar os produtos de segurança à Solução de Segurança e Auditoria Operations Management Suite (OMS) 
Este documento ajuda-o a ligar os seus produtos de segurança à Solução de Segurança e Auditoria OMS. São suportadas as seguintes origens:

- Eventos CEF (Common Event Format)
- Eventos Cisco ASA


## <a name="what-is-cef"></a>O que é o CEF?
O CEF (Common Event Format) é um formato padrão da indústria sobre as mensagens Syslog, utilizado por vários fornecedores de segurança para permitir a interoperabilidade de eventos entre diferentes plataformas. A Solução de Segurança e Auditoria OMS suporta a ingestão de dados com o CEF, que lhe permite ligar os seus produtos de segurança à Segurança OMS. 

Ao ligar a sua origem de dados ao OMS, pode tirar partido das seguintes capacidades que fazem parte desta plataforma:

- Pesquisa e Correlação
- Auditoria
- Alerta
- Informações sobre Ameaças
- Problemas Relevantes

## <a name="collection-of-security-solution-logs"></a>Recolha de registos de soluções de segurança

A Segurança OMS suporta a recolha de registos com o CEF sobre Syslogs e registos [Cisco ASA](https://blogs.technet.microsoft.com/msoms/2016/08/25/add-your-cisco-asa-logs-to-oms-security/). Neste exemplo, a origem (o computador que gera os registos) é um computador Linux com o daemon syslog-ng e o destino é a Segurança OMS. Para preparar o computador Linux, terá de realizar as seguintes tarefas:

- Transferir o Agente do OMS para Linux, versão 1.2.0-25 ou superior.
- Siga a secção **Guia de Instalação Rápida** [deste artigo](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md#steps-to-install-the-oms-agent-for-linux) para instalar e carregar o agente para a área de trabalho.

Normalmente, o agente está instalado num computador diferente daquele onde os registos são gerados. Reencaminhar os registos para o computador agente normalmente exige os seguintes passos:

- Configure o computador/produto de registo para reencaminhar os eventos necessários para o syslog daemon (rsyslog ou syslog-ng) no computador agente.
- Ative o syslog daemon no computador agente para receber mensagens de um sistema remoto.

No computador agente, os eventos têm de ser enviados a partir do syslog daemon para a porta UDP local 25226. O agente está à escuta de eventos recebidos nesta porta. Segue-se um exemplo de configuração para o envio de todos os eventos do sistema local para o agente (pode modificar a configuração para se ajustar às definições locais):

1. Abra a janela de terminal e aceda ao diretório */etc/syslog-ng/* 
2. Crie um novo ficheiro *security-config-omsagent.conf* e adicione o seguinte conteúdo: OMS_facility = local4
    
    filter f_local4_oms { facility(local4); };

    destination security_oms { tcp("127.0.0.1" port(25226)); };

    log { source(src); filter(f_local4_oms); destination(security_oms); };
    
3. Transfira o ficheiro *security_events.conf* e coloque-o em */etc/opt/microsoft/omsagent/conf/omsagent.d/* no computador do Agente do OMS.
4. Escreva o comando abaixo para reiniciar o syslog daemon: *Para execução de syslog-ng:*
    
    ```
    sudo service rsyslog restart
    ```

    *Para execução de rsyslog:*
    
    ```
    /etc/init.d/syslog-ng restart
    ```
5. Escreva o comando abaixo para reiniciar o Agente do OMS:

    *Para execução de syslog-ng:*
    
    ```
    sudo service omsagent restart
    ```

    *Para execução de rsyslog:*
    
    ```
    systemctl restart omsagent
    ```
6. Escreva o comando abaixo e reveja o resultado para confirmar que não existem erros no registo do Agente do OMS:

    ``` 
    tail /var/opt/microsoft/omsagent/log/omsagent.log
    ```

## <a name="reviewing-collected-security-events"></a>Rever os eventos de segurança recolhidos

[!include[log-analytics-log-search-nextgeneration](../../includes/log-analytics-log-search-nextgeneration.md)]

Depois de terminada a configuração, o evento de segurança vai começar a ser ingerido pela Segurança OMS. Para visualizar esses eventos, abra a Pesquisa de Registos, escreva o comando *Type=CommonSecurityLog* no campo de pesquisa e prima ENTER. O exemplo seguinte mostra o resultado deste comando. Repare que, neste caso, a Segurança OMS já ingeriu registos de segurança de vários fornecedores:
   
![Segurança OMS e Avaliação da Linha de Base de Auditoria](./media/oms-security-connect-products/oms-security-connect-products-fig1.png)

Pode refinar esta pesquisa para que um fornecedor único, por exemplo, visualize registos Cisco online; escreva: *Type=CommonSecurityLog DeviceVendor=Cisco*. O "CommonSecurityLog" predefiniu campos para qualquer cabeçalho CEF, incluindo as extensões básicas, enquanto que qualquer outra extensão, quer se trate de uma "Extensão Personalizada" ou não, será inserida no campo "AdditionalExtensions". Pode utilizar a funcionalidade Campos Personalizados para obter campos dedicados a partir da mesma. 

### <a name="accessing-computers-missing-baseline-assessment"></a>Aceder a computadores com avaliação da linha de base em falta
O OMS suporta a linha da base de membro de domínio no Windows Server 2008 R2 para Windows Server 2012 R2. A linha de base do Windows Server 2016 ainda não é final e será adicionada assim que for publicada. Todos os outros sistemas operativos analisados através da avaliação da linha de base de Segurança e Auditoria do OMS aparecem na secção **Computadores com a avaliação da linha de base em falta**.

## <a name="see-also"></a>Consultar também
Neste documento, aprendeu a ligar a sua solução CEF ao OMS. Para saber mais sobre a Segurança do OMS, veja os artigos seguintes:

* [Descrição geral do Operations Management Suite (OMS)](operations-management-suite-overview.md)
* [Monitorização e Resposta aos Alertas de Segurança na Solução de Segurança e Auditoria do Operations Management Suite](oms-security-responding-alerts.md)
* [Recursos de Monitorização na Solução de Segurança e Auditoria do Operations Management Suite](oms-security-monitoring-resources.md)


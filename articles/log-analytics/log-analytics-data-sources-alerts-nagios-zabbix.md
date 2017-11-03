---
title: "Recolher alertas da Nagios e da Zabbix na análise de registos do OMS | Microsoft Docs"
description: "Nagios e Zabbix são de fonte aberta ferramentas de monitorização. Pode recolher alertas destas ferramentas para análise de registos para poder analisá-las, juntamente com alertas a partir de outras origens.  Este artigo descreve como configurar o agente do OMS para Linux recolher alertas a partir desses sistemas."
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
ms.date: 05/04/2017
ms.author: magoedte
ms.openlocfilehash: 0b64c32e1031e704d50aab0b38eaea41e27d134b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="collect-alerts-from-nagios-and-zabbix-in-log-analytics-from-oms-agent-for-linux"></a>Recolher alertas da Nagios e da Zabbix na análise de registos do agente do OMS para Linux 
[Nagios](https://www.nagios.org/) e [Zabbix](http://www.zabbix.com/) são de fonte aberta ferramentas de monitorização.  Pode recolher alertas destas ferramentas para análise de registos para poder analisá-las juntamente com [alertas a partir de outras origens](log-analytics-alerts.md).  Este artigo descreve como configurar o agente do OMS para Linux recolher alertas a partir desses sistemas.
 
## <a name="configure-alert-collection"></a>Configurar a recolha de alertas

### <a name="configuring-nagios-alert-collection"></a>Configurar a recolha de alertas da Nagios
Execute os seguintes passos no servidor da Nagios para recolher alertas.

1. Conceder ao utilizador **omsagent** acesso de leitura para o ficheiro de registo da Nagios (ou seja, `/var/log/nagios/nagios.log`). Pressupondo que o ficheiro nagios.log proprietário é o grupo `nagios`, pode adicionar o utilizador **omsagent** para o **nagios** grupo. 

    sudo usermod - a -G da nagios omsagent

2.  Modificar o ficheiro de configuração em (`/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf`). Certifique-se de que as seguintes entradas estão presentes e não comentado saída:  

        <source>  
          type tail  
          #Update path to point to your nagios.log  
          path /var/log/nagios/nagios.log  
          format none  
          tag oms.nagios  
        </source>  
      
        <filter oms.nagios>  
          type filter_nagios_log  
        </filter>  

3. Reinicie o daemon de omsagent

    ```
    sudo sh /opt/microsoft/omsagent/bin/service_control restart
    ```

### <a name="configuring-zabbix-alert-collection"></a>Configurar a recolha de alertas da Zabbix
Recolher alertas de um servidor da Zabbix, tem de especificar um utilizador e palavra-passe no *apague texto*. Não é ideal, mas recomendamos que crie o utilizador e conceder permissões para monitorizar onlu.

Execute os seguintes passos no servidor da Nagios para recolher alertas.

1. Modificar o ficheiro de configuração em (`/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf`). Certifique-se de que as seguintes entradas estão presentes e não comentado enviados.  Altere o nome de utilizador e palavra-passe para os valores para o seu ambiente da Zabbix.

        <source>
         type zabbix_alerts
         run_interval 1m
         tag oms.zabbix
         zabbix_url http://localhost/zabbix/api_jsonrpc.php
         zabbix_username Admin
         zabbix_password zabbix
        </source>

2. Reinicie o daemon de omsagent

    sudo ostrar /opt/microsoft/omsagent/bin/service_control reiniciar


## <a name="alert-records"></a>Registos de alerta
Pode obter registos de alertas a partir da Nagios e da Zabbix utilizando [pesquisas de registo](log-analytics-log-searches.md) na análise de registos.

### <a name="nagios-alert-records"></a>Registos de alerta da Nagios

Alerta registos recolhidos da Nagios tem um **tipo** de **alerta** e um **SourceSystem** de **Nagios**.  Têm as propriedades na tabela seguinte.

| Propriedade | Descrição |
|:--- |:--- |
| Tipo |*Alerta* |
| SourceSystem |*Nagios* |
| AlertName |Nome do alerta. |
| AlertDescription | Descrição do alerta. |
| AlertState | Estado do serviço ou anfitrião.<br><br>OK<br>AVISO<br>CÓPIA DE SEGURANÇA<br>PARA BAIXO |
| Nome de anfitrião | Nome do anfitrião que criou o alerta. |
| PriorityNumber | Nível de prioridade do alerta. |
| StateType | O tipo de estado do alerta.<br><br>Configuração SOFT - problema que não tenha sido novamente-verificado.<br>RÍGIDO - problema que foi novamente verificados um número de vezes especificado.  |
| TimeGenerated |Data e hora em que o alerta foi criado. |


### <a name="zabbix-alert-records"></a>Registos de alerta da Zabbix
Alerta registos recolhidos da Zabbix tem um **tipo** de **alerta** e um **SourceSystem** de **Zabbix**.  Têm as propriedades na tabela seguinte.

| Propriedade | Descrição |
|:--- |:--- |
| Tipo |*Alerta* |
| SourceSystem |*Zabbix* |
| AlertName | Nome do alerta. |
| AlertPriority | Gravidade do alerta.<br><br>Não foi classificado<br>Informações<br>aviso<br>Média<br>Elevada<br>após desastre  |
| AlertState | Estado do alerta.<br><br>0 - o estado é atualizado.<br>1 - estado for desconhecido.  |
| AlertTypeNumber | Especifica se o alerta pode gerar vários eventos de problema.<br><br>0 - o estado é atualizado.<br>1 - estado for desconhecido.    |
| Comentários | Comentários adicionais para o alerta. |
| Nome de anfitrião | Nome do anfitrião que criou o alerta. |
| PriorityNumber | Valor que indica a gravidade do alerta.<br><br>0 - não classificado<br>1 - informações<br>2 - aviso<br>3 – média<br>4 - elevada<br>5 - desastre |
| TimeGenerated |Data e hora em que o alerta foi criado. |
| TimeLastModified |Data e hora em que o estado do alerta foi alterado pela última vez. |


## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre [alertas](log-analytics-alerts.md) na análise de registos.
* Saiba mais sobre [pesquisas de registo](log-analytics-log-searches.md) para analisar os dados recolhidos a partir de origens de dados e soluções. 

---
title: "Recolher dados de CollectD na análise de registos do OMS | Microsoft Docs"
description: "CollectD é um daemon de Linux de código aberto periodicamente recolhe dados das aplicações e informações de nível do sistema.  Este artigo fornece informações sobre a recolha de dados de CollectD na análise de registos."
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
ms.date: 05/02/2017
ms.author: magoedte
ms.openlocfilehash: a63b15ca5126b45451f0694c9ee75d7b67b1ceaf
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="collect-data-from-collectd-on-linux-agents-in-log-analytics"></a>Recolher dados de CollectD nos agentes Linux na análise de registos
[CollectD](https://collectd.org/) é um daemon de Linux de código aberto periodicamente recolhe métricas de desempenho de aplicações e informações de nível do sistema. Aplicações de exemplo incluem a Máquina Virtual de Java (JVM), servidor MySQL e Nginx. Este artigo fornece informações sobre a recolha de dados de desempenho de CollectD na análise de registos.

Uma lista completa de plug-ins disponíveis pode ser encontrada em [tabela de plug-ins](https://collectd.org/wiki/index.php/Table_of_Plugins).

![Descrição geral de CollectD](media/log-analytics-data-sources-collectd/overview.png)

A seguinte configuração de CollectD está incluída no agente do OMS do Linux, a rota CollectD dados para o agente do OMS para Linux.

    LoadPlugin write_http

    <Plugin write_http>
         <Node "oms">
         URL "127.0.0.1:26000/oms.collectd"
         Format "JSON"
         StoreRates true
         </Node>
    </Plugin>

Além disso, se utilizar um versões do collectD antes 5.5 em alternativa, utilize a seguinte configuração.

    LoadPlugin write_http

    <Plugin write_http>
       <URL "127.0.0.1:26000/oms.collectd">
        Format "JSON"
         StoreRates true
       </URL>
    </Plugin>

A configuração de CollectD utiliza a predefinição`write_http` Plug-in para enviar dados de métrica de desempenho através da porta 26000 para o agente do OMS para Linux. 

> [!NOTE]
> Esta porta pode ser configurada para uma porta personalizada definida se for necessário.

O agente do OMS para Linux também escuta na porta 26000 CollectD com base nas métricas e, em seguida, converte-os para as métricas de esquema do OMS. Segue-se o agente do OMS para a configuração do Linux `collectd.conf`.

    <source>
      type http
      port 26000
      bind 127.0.0.1
    </source>

    <filter oms.collectd>
      type filter_collectd
    </filter>


## <a name="versions-supported"></a>Versões suportadas
- Análise de registos suporta atualmente CollectD versão 4.8 e superior.
- Agente do OMS para Linux v1.1.0-217 ou superior é necessário para a coleção de métrica de CollectD.


## <a name="configuration"></a>Configuração
Seguem-se os passos básicos para configurar a recolha de dados de CollectD na análise de registos.

1. Configure CollectD para enviar dados para o agente do OMS para Linux utilizando o plug-in de write_http.  
2. Configure o agente do OMS para Linux escutar os dados de CollectD na porta adequada.
3. Reinicie CollectD e o agente do OMS para Linux.

### <a name="configure-collectd-to-forward-data"></a>Configurar CollectD para reencaminhar dados 

1. A rota CollectD dados para o agente do OMS para Linux `oms.conf` tem de ser adicionado ao diretório de configuração do CollectD. O destino deste ficheiro depende do distro Linux da sua máquina.

    Se o diretório de configuração CollectD está localizado na /etc/collectd.d/:

        sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.d/oms.conf /etc/collectd.d/oms.conf

    Se o diretório de configuração CollectD está localizado na /etc/collectd/collectd.conf.d/:

        sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.d/oms.conf /etc/collectd/collectd.conf.d/oms.conf

    >[!NOTE]
    >Para versões de CollectD antes 5.5 terá de modificar as etiquetas na `oms.conf` conforme mostrado acima.
    >

2. Copie collectd.conf para o diretório de configuração de omsagent da área de trabalho pretendida.

        sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.d/collectd.conf /etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/
        sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/collectd.conf

3. Reinicie CollectD e o agente do OMS para Linux com os seguintes comandos.

    sudo collectd reinício sudo /opt/microsoft/omsagent/bin/service_control reiniciar o serviço do

## <a name="collectd-metrics-to-log-analytics-schema-conversion"></a>Métricas de CollectD para conversão de esquema de análise de registos
Para manter um modelo familiar entre as métricas de infraestrutura recolhidas pelo agente do OMS para Linux e a nova métrica recolhidos pelo CollectD o seguinte mapeamento de esquema é utilizado:

| Campo de métrica de CollectD | Campo de análise do registo |
|:--|:--|
| anfitrião | Computador |
| Plug-in | Nenhuma |
| plugin_instance | Nome da instância<br>Se **plugin_instance** é *nulo* , em seguida, InstanceName = "*total*" |
| tipo | ObjectName |
| type_instance | CounterName<br>Se **type_instance** é *nulo* , em seguida, CounterName =**em branco** |
| dsnames [] | CounterName |
| dstypes | Nenhuma |
| os valores] | CounterValue |

## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre [pesquisas de registo](log-analytics-log-searches.md) para analisar os dados recolhidos a partir de origens de dados e soluções. 
* Utilize [campos personalizados](log-analytics-custom-fields.md) para analisar dados de registos de syslog em campos individuais.


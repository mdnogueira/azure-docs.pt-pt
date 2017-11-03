---
title: "Tipos de recursos através de estado de funcionamento de recursos do Azure suportados | Microsoft Docs"
description: "Tipos de recurso suportados através do Estado de funcionamento de recursos do Azure"
services: Resource health
documentationcenter: 
author: BernardoAMunoz
manager: 
editor: 
ms.assetid: 85cc88a4-80fd-4b9b-a30a-34ff3782855f
ms.service: service-health
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Supportability
ms.date: 10/09/2017
ms.author: BernardoAMunoz
ms.openlocfilehash: 22e1768257524400f341cf881dfebfb35115a43b
ms.sourcegitcommit: 6acb46cfc07f8fade42aff1e3f1c578aa9150c73
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/18/2017
---
# <a name="resource-types-and-health-checks-in-azure-resource-health"></a>Tipos de recursos e o estado de funcionamento verifica-se no estado de funcionamento de recursos do Azure
Segue-se uma lista completa de todas as verificações executadas através do Estado de funcionamento de recursos por tipos de recursos.

## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service
|Verificações executadas|
|---|
|<ul><li>O serviço de Api Management do agregador estiver e em execução?</li></ul>|

## <a name="microsoftcacheredisredis"></a>Microsoft.CacheRedis/Redis
|Verificações executadas|
|---|
|<ul><li>Estão todos os nós de Cache em execução?</li><li>A Cache acessível no Centro de dados?</li><li>A Cache atingiu o número máximo de ligações?</li><li> A cache esgotou a memória disponível? </li><li>A Cache tiver um número elevado de falhas de paginação?</li><li>Se a Cache com muita carga?</li></ul>|

## <a name="microsoftcdnprofile"></a>Microsoft.CDN/profile
|Verificações executadas|
|---|
|<ul> <li>Qualquer um dos pontos finais foi parado, removidas ou mal configurada?</li><li>Portal suplementar está acessível para operações de configuração da CDN?</li><li>Existem problemas de entrega em curso com os pontos finais da CDN?</li><li>Podem utilizadores alterar a configuração dos seus recursos CDN?</li><li>As alterações de configuração são propagar a taxa esperada?</li><li>Podem aos utilizadores gerir a configuração de CDN através do portal do Azure, PowerShell ou a API?</li> </ul>|

## <a name="microsoftclassiccomputevirtualmachines"></a>Microsoft.classiccompute/virtualmachines
|Verificações executadas|
|---|
|<ul><li>O servidor de anfitrião se encontra em execução?</li><li>O arranque do anfitrião SO concluída?</li><li>O contentor de máquina virtual é aprovisionado e ligados à corrente cópias de segurança?</li><li>Existe conectividade de rede entre o anfitrião e a conta de armazenamento?</li><li>O arranque do SO convidado concluiu?</li><li>Existe a manutenção planeada em curso?</li></ul>|

## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft.cognitiveservices/Accounts
|Verificações executadas|
|---|
|<ul><li>A conta acessível no Centro de dados?</li><li>O fornecedor de recursos de serviços cognitivos está disponível?</li><li>O serviço cognitivos está disponível na região adequada?</li><li>Pode ler possível efetuar as operações na conta de armazenamento que contém os metadados do recurso?</li><li>Foi atingida a quota de chamada de API?</li><li>Foi atingido o limite de leitura de chamada de API?</li></ul>|

## <a name="microsoftcomputevirtualmachines"></a>Microsoft.Compute/virtualmachines
|Verificações executadas|
|---|
|<ul><li>É o servidor que aloja esta máquina virtual a cópia de segurança e em execução?</li><li>O arranque do anfitrião SO concluída?</li><li>O contentor de máquina virtual é aprovisionado e ligados à corrente cópias de segurança?</li><li>Existe conectividade de rede entre o anfitrião e a conta de armazenamento?</li><li>O arranque do SO convidado concluiu?</li><li>Existe a manutenção planeada em curso?</li></ul>|

## <a name="microsoftdatalakeanalyticsaccounts"></a>Microsoft.datalakeanalytics/Accounts
|Verificações executadas|
|---|
|<ul><li>Pode tarefas de envio de utilizadores ao Data Lake Analytics na região?</li><li>Não executar as tarefas básicas e concluir com êxito na região?</li><li>Podem utilizadores listar itens de catálogo na região?</li>|


## <a name="microsoftdatalakestoreaccounts"></a>Microsoft.datalakestore/Accounts
|Verificações executadas|
|---|
|<ul><li>Os utilizadores carregar dados ao Data Lake Store na região</li><li>Os utilizadores transferem dados do Data Lake Store na região</li></ul>|

## <a name="microsoftdevicesiothubs"></a>Microsoft.Devices/iothubs

|Verificações executadas|
|---|
|<ul><li>O IoT hub se encontra em execução?</li></ul>|

## <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.documentdb/databaseAccounts
|Verificações executadas|
|---|
|<ul><li>Ter ocorrido quaisquer pedidos de base de dados ou de coleção não servidos devido a uma indisponibilidade de serviço do DocumentDB?</li><li>Ter ocorrido quaisquer pedidos de documento não servidos devido a uma indisponibilidade de serviço do DocumentDB?</li></ul>|

## <a name="microsoftnetworkconnections"></a>Microsoft.Network/Connections
|Verificações executadas|
|---|
|<ul><li>O túnel VPN está ligado?</li><li>Existem conflitos de configuração na ligação?</li><li>As chaves pré-partilhadas estão corretamente configuradas?</li><li>Dispositivo VPN no local é acessível?</li><li>Existem correspondência na política de segurança IPSec/IKE?</li><li>Corretamente aprovisionado ou em estado de falha, é a ligação S2S VPN?</li><li>Corretamente aprovisionado ou em estado de falha, é a ligação VNET a VNET?</li></ul>|

## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft.network/virtualNetworkGateways
|Verificações executadas|
|---|
|<ul><li>O gateway VPN é acessível a partir da internet?</li><li>É o Gateway de VPN no modo de reserva dinâmica?</li><li>O serviço VPN em execução no gateway?</li></ul>|

## <a name="microsoftnotificationhubsnamespace"></a>Microsoft.NotificationHubs/namespace
|Verificações executadas|
|---|
|<ul><li> Podem ser efetuadas operações de tempo de execução como o registo, a instalação ou a enviar no espaço de nomes?</li></ul>|

## <a name="microsoftpowerbiworkspacecollections"></a>Microsoft.PowerBI/workspaceCollections
|Verificações executadas|
|---|
|<ul><li>O sistema operativo do anfitrião se encontra em execução?</li><li>O workspaceCollection é acessível a partir de fora do datacenter?</li><li>O fornecedor de recursos do Power BI está disponível?</li><li>O serviço PowerBI está disponível na região adequada?</li></ul>|

## <a name="microsoftsearchsearchservices"></a>Microsoft.search/searchServices
|Verificações executadas|
|---|
|<ul><li>Operações de diagnóstico podem ser executadas num cluster?</li></ul>|

## <a name="microsoftsqlserverdatabase"></a>Microsoft.SQL/Server/database
|Verificações executadas|
|---|
|<ul><li> Não existe ter sido inícios de sessão na base de dados?</li></ul>|

## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft.StreamAnalytics/streamingjobs
|Verificações executadas|
|---|
|<ul><li>São todos os anfitriões em que a tarefa está a executar cópias de segurança e em execução?</li><li>A tarefa não foi possível iniciar?</li><li>Existem atualizações de tempo de execução em curso?</li><li>A tarefa num estado esperado (por exemplo em execução ou parada pelo cliente)?</li><li>A tarefa encontrou saída de exceções de memória?</li><li>Existem atualizações em curso computação agendada?</li><li>O Gestor de execução (plano de controlo) está disponível?</li></ul>|

## <a name="microsoftwebserverfarms"></a>Microsoft.web/serverFarms
|Verificações executadas|
|---|
|<ul><li>O servidor de anfitrião se encontra em execução?</li><li>Serviços de informação Internet está em execução?</li><li>O Balanceador de carga está em execução?</li><li>O plano de serviço Web acessível no Centro de dados?</li><li>A conta de armazenamento aloja o conteúdo de sites para serverFarm disponível??</li></ul>|

## <a name="microsoftwebsites"></a>Microsoft.Web/sites
|Verificações executadas|
|---|
|<ul><li>O servidor de anfitrião se encontra em execução?</li><li>Servidor de informação Internet está em execução?</li><li>O Balanceador de carga está em execução?</li><li>A aplicação Web acessível no Centro de dados?</li><li>A conta de armazenamento aloja o conteúdo do site disponível?</li></ul>|

# <a name="next-steps"></a>Passos Seguintes
-  Consulte [introdução ao estado de funcionamento de serviço de Azure](service-health-overview.md) e [introdução ao estado de funcionamento de recursos de Azure](resource-health-overview.md) para saber mais sobre os mesmos. 
-  [Perguntas mais frequentes sobre o estado de funcionamento de recursos de Azure](resource-health-faq.md)
- Configure alertas para que seja notificado dos problemas de estado de funcionamento. Para obter mais informações, consulte [configurar alertas de estado de funcionamento do serviço](../monitoring-and-diagnostics/monitoring-activity-log-alerts-on-service-notifications.md). 
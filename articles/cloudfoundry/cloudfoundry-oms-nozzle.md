---
title: "Implementar Nozzle de análise de registos do Azure para a monitorização de nuvem Foundry | Microsoft Docs"
description: "Orientações passo a passo sobre como implementar o loggregator nuvem Foundry Nozzle para Log Analytics do Azure. Utilize o Nozzle para monitorizar as métricas de desempenho e estado de funcionamento de sistema do Foundry de nuvem."
services: virtual-machines-linux
documentationcenter: 
author: ningk
manager: timlt
editor: 
tags: Cloud-Foundry
ms.assetid: 00c76c49-3738-494b-b70d-344d8efc0853
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/22/2017
ms.author: ningk
ms.openlocfilehash: 0d13d39d2921c51c537534a5b000564a9df91880
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="deploy-azure-log-analytics-nozzle-for-cloud-foundry-system-monitoring"></a>Implementar Nozzle de análise de registos do Azure para a monitorização de sistema de nuvem Foundry

[Análise de registos do Azure](https://azure.microsoft.com/services/log-analytics/) é um serviço no Microsoft [Operations Management Suite](https://docs.microsoft.com/azure/operations-management-suite/) (OMS). Ajuda a recolher e analisar os dados que são gerados a partir da nuvem e no local ambientes.

O Nozzle de análise do registo (o Nozzle) é um componente de nuvem Foundry (CF), que reencaminha as métricas do [nuvem Foundry loggregator](https://docs.cloudfoundry.org/loggregator/architecture.html) firehose à análise de registos. Com o Nozzle, pode recolher, ver e analisar CF sistema estado de funcionamento e desempenho métricas, através de múltiplas implementações.

Neste documento, irá aprender a implementar o Nozzle ao seu ambiente CF e, em seguida, aceder os dados a partir da consola de análise de registos OMS.

## <a name="prerequisites"></a>Pré-requisitos

Os seguintes passos são pré-requisitos para implementar o Nozzle.

### <a name="1-deploy-a-cf-or-pivotal-cloud-foundry-environment-in-azure"></a>1. Implementar um ambiente CF ou Pivotal Foundry de nuvem no Azure

Pode utilizar o Nozzle com um código aberto CF de implementação ou uma implementação de Pivotal nuvem Foundry (PCF).

* [Implementar Foundry de nuvem no Azure](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/blob/master/docs/guidance.md)

* [Implementar Foundry Pivotal nuvem no Azure](https://docs.pivotal.io/pivotalcf/1-11/customizing/azure.html)

### <a name="2-install-the-cf-command-line-tools-for-deploying-the-nozzle"></a>2. Instalar as ferramentas de linha de comandos CF para implementar o Nozzle

O Nozzle é executado como uma aplicação num ambiente de CF. É necessário CF CLI para implementar a aplicação.

O Nozzle também necessita da permissão de acesso para o firehose loggregator e o controlador de nuvem. Para criar e configurar o utilizador, terá do serviço de conta de utilizador e de autenticação (UAA).

* [Instalar nuvem Foundry CLI](https://docs.cloudfoundry.org/cf-cli/install-go-cli.html)

* [Instalar o cliente da linha de comandos da nuvem Foundry UAA](https://github.com/cloudfoundry/cf-uaac/blob/master/README.md)

Antes de configurar o cliente de linha de comandos UAA, certifique-se de que o Rubygems está instalada.

### <a name="3-create-an-oms-workspace-in-azure"></a>3. Criar uma área de trabalho do OMS no Azure

Pode criar a área de trabalho do OMS manualmente ou utilizando um modelo. Carregar a alertas e vistas OMS previamente configuradas depois de concluir a implementação de Nozzle.

Para criar manualmente a área de trabalho:

1. No portal do Azure, pesquise a lista de serviços no Azure Marketplace e, em seguida, selecione de análise de registos.
2. Selecione **criar**e, em seguida, selecione as opções para os seguintes itens:

   * **Área de trabalho OMS**: escreva um nome para a sua área de trabalho.
   * **Subscrição**: Se tiver várias subscrições, escolha a que é o mesmo que a implementação de CF.
   * **Grupo de recursos**: pode criar um novo grupo de recursos ou utilizar o mesmo com a sua implementação CF.
   * **Localização**: introduza a localização.
   * **Escalão de preço**: selecione **OK** para concluir.

Para obter mais informações, consulte [introdução à análise de registos](https://docs.microsoft.com/azure/log-analytics/log-analytics-get-started).

Em alternativa, pode criar a área de trabalho do OMS através do modelo do OMS. Com este método, o modelo é carregado previamente configuradas vistas do OMS e alertas automaticamente. Para obter mais informações, consulte o [solução de análise de registos do Azure OMS para nuvem Foundry](https://github.com/Azure/azure-quickstart-templates/tree/master/oms-cloudfoundry-solution).

## <a name="deploy-the-nozzle"></a>Implementar o Nozzle

Existem duas formas diferentes de implementar o Nozzle: como um mosaico PCF ou como uma aplicação CF.

### <a name="deploy-the-nozzle-as-a-pcf-ops-manager-tile"></a>Implementar o Nozzle como um mosaico PCF Ops Manager

Se tiver implementado PCF utilizando o Gestor de operações, siga os passos para [instalar e configurar o Nozzle para PCF](http://docs.pivotal.io/partners/azure-log-analytics-nozzle/installing.html). O Nozzle é instalado como um mosaico com o Gestor de operações.

### <a name="deploy-the-nozzle-as-a-cf-application"></a>Implementar o Nozzle como uma aplicação de CF

Se não estiver a utilizar o Gestor de Ops PCF, implemente o Nozzle como uma aplicação. As secções seguintes descrevem este processo.

#### <a name="sign-in-to-your-cf-deployment-as-an-admin-through-cf-cli"></a>Inicie sessão na sua implementação CF como um administrador através de CF CLI

Execute o seguinte comando:
```
cf login -a https://api.${SYSTEM_DOMAIN} -u ${CF_USER} --skip-ssl-validation
```

"SYSTEM_DOMAIN" é o nome de domínio CF. Pode obtê-lo ao pesquisar "SYSTEM_DOMAIN" no ficheiro de manifesto de implementação de CF. 

"CF_User" é o nome de administrador de CF. Pode obter o nome e a palavra-passe por pesquisar a secção "scim", o nome e a "cf_admin_password" no ficheiro de manifesto de implementação de CF a procurar.

#### <a name="create-a-cf-user-and-grant-required-privileges"></a>Criar um utilizador CF e conceder privilégios necessários

Execute os seguintes comandos:
```
uaac target https://uaa.${SYSTEM_DOMAIN} --skip-ssl-validation
uaac token client get admin
cf create-user ${FIREHOSE_USER} ${FIREHOSE_USER_PASSWORD}
uaac member add cloud_controller.admin ${FIREHOSE_USER}
uaac member add doppler.firehose ${FIREHOSE_USER}
```

"SYSTEM_DOMAIN" é o nome de domínio CF. Pode obtê-lo ao pesquisar "SYSTEM_DOMAIN" no ficheiro de manifesto de implementação de CF.

#### <a name="download-the-latest-log-analytics-nozzle-release"></a>Transferir a versão mais recente do Nozzle de análise do registo

Execute o seguinte comando:
```
git clone https://github.com/Azure/oms-log-analytics-firehose-nozzle.git
cd oms-log-analytics-firehose-nozzle
```

#### <a name="set-environment-variables"></a>Variáveis de ambiente de conjunto

Agora pode definir variáveis de ambiente no ficheiro manifest.yml no seu diretório atual. O seguinte mostra o manifesto da aplicação para o Nozzle. Substitua os valores com informações da sua área de trabalho OMS específicas.

```
OMS_WORKSPACE             : OMS workspace ID: open OMS portal from your OMS workspace, select Settings, and select connected sources.
OMS_KEY                   : OMS key: open OMS portal from your OMS workspace, select Settings, and select connected sources.
OMS_POST_TIMEOUT          : HTTP post timeout for sending events to OMS Log Analytics. The default is 10 seconds.
OMS_BATCH_TIME            : Interval for posting a batch to OMS Log Analytics. The default is 10 seconds.
OMS_MAX_MSG_NUM_PER_BATCH : The maximum number of messages in a batch to OMS Log Analytics. The default is 1000.
API_ADDR                  : The API URL of the CF environment. For more information, see the preceding section, "Sign in to your CF deployment as an admin through CF CLI."
DOPPLER_ADDR              : Loggregator's traffic controller URL. For more information, see the preceding section, "Sign in to your CF deployment as an admin through CF CLI."
FIREHOSE_USER             : CF user you created in the preceding section, "Create a CF user and grant required privileges." This user has firehose and Cloud Controller admin access.
FIREHOSE_USER_PASSWORD    : Password of the CF user above.
EVENT_FILTER              : Event types to be filtered out. The format is a comma-separated list. Valid event types are METRIC, LOG, and HTTP.
SKIP_SSL_VALIDATION       : If true, allows insecure connections to the UAA and the traffic controller.
CF_ENVIRONMENT            : Enter any string value for identifying logs and metrics from different CF environments.
IDLE_TIMEOUT              : The Keep Alive duration for the firehose consumer. The default is 60 seconds.
LOG_LEVEL                 : The logging level of the Nozzle. Valid levels are DEBUG, INFO, and ERROR.
LOG_EVENT_COUNT           : If true, the total count of events that the Nozzle has received and sent are logged to OMS Log Analytics as CounterEvents.
LOG_EVENT_COUNT_INTERVAL  : The time interval of the logging event count to OMS Log Analytics. The default is 60 seconds.
```

### <a name="push-the-application-from-your-development-computer"></a>Push da aplicação a partir do seu computador de desenvolvimento

Certifique-se de que está sob a pasta do oms-registo-análise-firehose-nozzle. Execute o seguinte comando:
```
cf push
```

## <a name="validate-the-nozzle-installation"></a>Validar a instalação de Nozzle

### <a name="from-apps-manager-for-pcf"></a>A partir do Gestor de aplicações (para PCF)

1. Inicie sessão no Gestor de operações e certifique-se de que é apresentado o mosaico no dashboard de instalação.
2. Inicie sessão no Gestor de aplicações, certifique-se de que o espaço que criou para o Nozzle está listado no relatório de utilização e confirme que o estado é normal.

### <a name="from-your-development-computer"></a>Do seu computador de desenvolvimento

Na janela CF CLI, escreva:
```
cf apps
```
Certifique-se que a aplicação da OMS Nozzle está em execução.

## <a name="view-the-data-in-the-oms-portal"></a>Ver os dados no portal do OMS

### <a name="1-import-the-oms-view"></a>1. Importar a vista do OMS

No portal do OMS, navegue até à **estruturador de vistas** > **importação** > **procurar**e selecione um dos ficheiros omsview. Por exemplo, seleccione *nuvem Foundry.omsview*e guarde a vista. Agora é apresentado um mosaico no OMS **descrição geral** página. Selecione para ver as métricas visualizadas.

Pode personalizar estas vistas ou criar novas vistas através de **estruturador de vistas**.

O *"Nuvem Foundry.omsview"* é uma versão de pré-visualização do modelo de vista de nuvem Foundry OMS. Este é um modelo predefinido totalmente configurado. Se tiver sugestões ou comentários sobre o modelo, envie-os para o [emitir secção](https://github.com/Azure/oms-log-analytics-firehose-nozzle/issues).

### <a name="2-create-alert-rules"></a>2. Criar regras de alertas

Pode [criar os alertas](https://docs.microsoft.com/azure/log-analytics/log-analytics-alerts)e personalizar os valores de limiar de consultas e conforme necessário. São recomendados alertas:

| Consulta de pesquisa                                                                  | Gerar alerta com base no | Descrição                                                                       |
| ----------------------------------------------------------------------------- | ----------------------- | --------------------------------------------------------------------------------- |
| Tipo = CF_ValueMetric_CL Origin_s = bbs Name_s = "Domain.cf-aplicações"                   | Número de resultados < 1   | **BBS. Aplicações Domain.cf** indica se o domínio de aplicações cf está atualizado. Isto significa que os pedidos de aplicação CF do controlador de nuvem são sincronizados com bbs. LRPsDesired (AIs pretendido de Diego) para execução. Não foram recebidos dados significa que Cf aplicações domínio não está atualizado na janela de tempo especificado. |
| Tipo = CF_ValueMetric_CL Origin_s = rep Name_s = UnhealthyCell Value_d > 1            | Número de resultados > 0   | Para Diego células, 0 significa bom estado de funcionamento e 1 significa mau estado de funcionamento. Defina o alerta se forem detetadas várias células Diego mau estado de funcionamento na janela de tempo especificado. |
| Tipo = CF_ValueMetric_CL Origin_s = "bosh-hm-reencaminhador" Name_s="system.healthy" Value_d = 0 | Número de resultados > 0 | 1 significa que o sistema está em bom estado, e 0 significa que o sistema não está em bom estado. |
| Tipo = CF_ValueMetric_CL Origin_s = route_emitter Name_s = ConsulDownMode Value_d > 0 | Número de resultados > 0   | Consul emite periodicamente o estado de funcionamento. 0 significa que o sistema está em bom estado e 1 significa que o emitter rota Deteta que Consul está desativado. |
| Tipo = CF_CounterEvent_CL Origin_s = Delta_d DopplerServer (Name_s="TruncatingBuffer.DroppedMessages" ou Name_s="doppler.shedEnvelopes)" > 0 | Número de resultados > 0 | O número de diferenças de mensagens, ignorada intencionalmente pelo Doppler devido a pressão de back. |
| Tipo = CF_LogMessage_CL SourceType_s = LGR MessageType_s = ERR                      | Número de resultados > 0   | Loggregator emite **LGR** para indicar problemas com o processo de registo. Um exemplo deste tipo um problema é quando o resultado de mensagem do registo é demasiado elevado. |
| Tipo = CF_ValueMetric_CL Name_s = slowConsumerAlert                               | Número de resultados > 0   | Quando o Nozzle recebe um alerta de consumidor lenta de loggregator, envia o **slowConsumerAlert** ValueMetric OMS. |
| Tipo = CF_CounterEvent_CL Job_s = nozzle Name_s = eventsLost Delta_d > 0              | Número de resultados > 0   | Se o número de diferenças de perder eventos atingir um limiar, significa que o Nozzle pode ter um problema ao executar. |

## <a name="scale"></a>Escala

Pode dimensionar o Nozzle e o loggregator.

### <a name="scale-the-nozzle"></a>Dimensionar o Nozzle

Deve começar com, pelo menos, duas instâncias do Nozzle. O firehose distribui a carga de trabalho por todas as instâncias do Nozzle.
Para certificar-se de que o Nozzle pode manter-se com o tráfego de dados do firehose, configure o **slowConsumerAlert** alerta (listada na secção anterior, "Criar regras de alerta"). Depois do utilizador ter sido alertado, siga o [orientações para Nozzle lenta](https://docs.pivotal.io/pivotalcf/1-11/loggregator/log-ops-guide.html#slow-noz) para determinar se o dimensionamento é necessária.
Para aumentar verticalmente o Nozzle, utilize o Gestor de aplicações ou a CLI CF para aumentar os números de instância ou os recursos de memória ou disco para o Nozzle.

### <a name="scale-the-loggregator"></a>Dimensionar o loggregator

Loggregator envia um **LGR** mensagem do registo para indicar problemas com o processo de registo. Pode monitorizar o alerta para determinar se o loggregator tem de ser escalado para cima.
Para aumentar verticalmente o loggregator, aumente o tamanho da memória intermédia Doppler ou adicionar as instâncias de servidor Doppler adicional no manifesto CF. Para obter mais informações, consulte [a orientação para o dimensionamento a loggregator](https://docs.cloudfoundry.org/running/managing-cf/logging-config.html#scaling).

## <a name="update"></a>Atualizar

Para atualizar o Nozzle com uma versão mais recente, transferir a nova versão de Nozzle, siga os passos na secção "Implementar o Nozzle" anterior e push novamente a aplicação.

### <a name="remove-the-nozzle-from-ops-manager"></a>Remova o Nozzle do Ops Manager

1. Inicie sessão no Gestor de operações.
2. Localize o **Nozzle de análise do Microsoft Azure registo para PCF** mosaico.
3. Selecione o ícone de libertação da memória e confirmar a eliminação.

### <a name="remove-the-nozzle-from-your-development-computer"></a>Remova o Nozzle do seu computador de desenvolvimento

Na janela do CF CLI, escreva:
```
cf delete <App Name> -r
```

Se remover o Nozzle, os dados no portal do OMS não são removidos automaticamente. Expirar com base nas suas definições de retenção de análise de registos do OMS.

## <a name="support-and-feedback"></a>Suporte e comentários

Azure Nozzle de análise do registo é abrir sourced. Envie as perguntas e comentários para o [GitHub secção](https://github.com/Azure/oms-log-analytics-firehose-nozzle/issues). Para abrir um pedido de suporte do Azure, escolha "Máquina Virtual em execução na nuvem Foundry" como a categoria de serviço. 

## <a name="next-step"></a>Passo seguinte

Além das métricas de nuvem Foundry abrangidas o Nozzle, pode utilizar o agente do OMS para obter informações acerca de dados operacionais ao nível da VM (por exemplo, Syslog, desempenho, alertas, inventário). O agente do OMS é instalado como um suplemento de Bosh para as suas VMs CF.

Para obter mais informações, consulte [agente do OMS implementar para a implementação de nuvem Foundry](https://github.com/Azure/oms-agent-for-linux-boshrelease).

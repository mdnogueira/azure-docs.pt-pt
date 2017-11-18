---
title: "Azure IoT Hub Glossário de termos | Microsoft Docs"
description: "Guia para programadores - obter um glossário dos termos comuns relacionadas com o IoT Hub do Azure."
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 16ef29ea-a185-48c3-ba13-329325dc6716
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/08/2017
ms.author: dobett
ms.openlocfilehash: 0a780461bb934b4766f8050fba825e1d7503f4fd
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/18/2017
---
# <a name="glossary-of-iot-hub-terms"></a>Glossário de termos de IoT Hub
Este artigo apresenta uma lista alguns dos termos comuns utilizados nos artigos IoT Hub.

## <a name="advanced-message-queueing-protocol"></a>Protocolo de colocação de mensagens avançadas
[Avançadas de protocolo de colocação de mensagens (AMQP)](https://www.amqp.org/) é uma da mensagens protocolos que [IoT Hub](#iot-hub) suporta para comunicar com os dispositivos. Para obter mais informações sobre os protocolos de mensagens que suporta o IoT Hub, consulte [enviar e receber mensagens com o IoT Hub](iot-hub-devguide-messaging.md).

## <a name="azure-cli"></a>CLI do Azure
O [CLI do Azure](../cli-install-nodejs.md) é uma ferramenta de plataforma, open source, baseada na shell de comandos para criar e gerir recursos no Microsoft Azure. Esta versão do CLI está implementado com o Node.js.

## <a name="azure-cli-20"></a>CLI 2.0 do Azure
O [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-az-cli2) é uma ferramenta de plataforma, open source, baseada na shell de comandos para criar e gerir recursos no Microsoft Azure. Esta versão de pré-visualização do CLI está implementado com o Python.


## <a name="azure-iot-device-sdks"></a>SDKs do Azure do dispositivo IoT
Existem _SDKs do dispositivo_ disponível para vários idiomas que permitem-lhe criar [aplicações de dispositivos](#device-app) que interagem com um IoT hub. Os tutoriais do IoT Hub mostram-lhe como utilizar estes SDKs do dispositivo. Pode encontrar o código de origem e obter mais informações sobre os SDKs do dispositivo no GitHub, [repositório](https://github.com/Azure/azure-iot-sdks).

## <a name="azure-iot-edge"></a>Azure IoT Edge
Limite de IoT do Azure move a análise de nuvem e a lógica de negócio personalizadas para dispositivos, para que a sua organização pode concentrar-se na informações empresariais em vez de gestão de dados. Ative a sua solução verdadeiramente Dimensionar ao configurar o seu software de IoT, implementar em dispositivos através de contentores padrão e monitorização-tudo a partir da nuvem. Começar com um tutorial que mostra como instalar e utilizar o Azure IoT Edge num [Linux](../iot-edge/tutorial-simulate-device-linux.md) ou [Windows](../iot-edge/tutorial-simulate-device-windows.md) dispositivo.

## <a name="azure-iot-service-sdks"></a>SDKs de serviço do IoT do Azure
Existem _service SDKs_ disponível para vários idiomas que permitem-lhe criar [aplicações de back-end](#back-end-app) que interagem com um IoT hub. Os tutoriais do IoT Hub mostram-lhe como utilizar estes SDKs de serviço. Pode encontrar o código de origem e obter mais informações sobre os SDKs de serviço no GitHub, [repositório](https://github.com/Azure/azure-iot-sdks).

## <a name="azure-portal"></a>Portal do Azure
O [portal do Microsoft Azure](https://portal.azure.com) é um local central onde pode aprovisionar e gerir os recursos do Azure. Organiza os respetivos conteúdos através de _painéis_.

## <a name="azure-powershell"></a>Azure PowerShell
[O Azure PowerShell](/powershell/azure/overview) é uma coleção de cmdlets que pode utilizar para gerir o Azure com o Windows PowerShell. Pode utilizar os cmdlets para criar, testar, implementar e gerir soluções e serviços entregues através de plataforma do Azure.

## <a name="azure-resource-manager"></a>Azure Resource Manager
[O Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) permite-lhe trabalhar com os recursos na sua solução como um grupo. Pode implementar, atualizar ou eliminar os recursos para a sua solução numa operação única e coordenada.

## <a name="azure-service-bus"></a>Service Bus do Azure
[Barramento de serviço](../service-bus/index.md) fornece comunicação ativado para a nuvem com mensagens empresariais e comunicação retransmitida que o ajuda a estabelecer ligação soluções no local com a nuvem. Tutoriais do IoT Hub se utilizar o Service Bus [filas](../service-bus-messaging/service-bus-messaging-overview.md).

## <a name="azure-storage"></a>Storage do Azure
[Armazenamento do Azure](../storage/common/storage-introduction.md) é uma solução de armazenamento de nuvem. Inclui o serviço de armazenamento de BLOBs que pode utilizar para armazenar dados de objetos não estruturados. Tutoriais do IoT Hub utilizam o blob storage.

## <a name="back-end-app"></a>Aplicação de back-end
No contexto de [IoT Hub](#iot-hub), uma aplicação de back-end é uma aplicação que liga a um dos pontos finais orientado para o serviço num IoT hub. Por exemplo, uma aplicação de back-end poderá obter [dispositivo para nuvem](#device-to-cloud)as mensagens ou gerir o [registo de identidade](#identity-registry). Normalmente, uma aplicação de back-end é executado na nuvem, mas muitos dos tutoriais as aplicações de back-end são aplicações de consola em execução no computador de desenvolvimento local.

## <a name="built-in-endpoints"></a>Pontos finais incorporados
Cada IoT hub inclui um incorporado [endpoint](iot-hub-devguide-endpoints.md) que Hub compatível com o Event. Pode utilizar qualquer mecanismo que funciona com os Hubs de eventos para ler mensagens do dispositivo para nuvem a partir deste ponto final.

## <a name="cloud-gateway"></a>Gateway de nuvem
Um gateway de nuvem ativa a conectividade para dispositivos que não é possível ligar diretamente à [IoT Hub](#iot-hub). Um gateway de nuvem está alojado na nuvem contrast para um [gateway de campo](#field-gateway) que executa o local para os seus dispositivos. Um caso de utilização típica para um gateway de nuvem consiste em implementar tradução de protocolos para os seus dispositivos.

## <a name="cloud-to-device"></a>Nuvem para o dispositivo
Refere-se às mensagens enviadas a partir de um hub IoT para um dispositivo ligado. Muitas vezes, estas mensagens são comandos que instrui o dispositivo execute uma ação. Para obter mais informações, consulte [enviar e receber mensagens com o IoT Hub](iot-hub-devguide-messaging.md).

## <a name="connection-string"></a>Cadeia de ligação
Utilize as cadeias de ligação no código da aplicação para encapsular as informações necessárias para ligar a um ponto final. Uma cadeia de ligação, incluem normalmente o endereço do ponto final e informações de segurança, mas a cadeia de ligação formatos variem em todos os serviços. Existem dois tipos de cadeia de ligação associado ao serviço IoT Hub:
- *Cadeias de ligação do dispositivo* permitir que os dispositivos liguem aos pontos finais do orientado para o dispositivo num IoT hub.
- *Cadeias de ligação do IoT Hub* ativar aplicações de back-end para se ligarem aos pontos finais do serviço com acesso num IoT hub.

## <a name="custom-endpoints"></a>Pontos finais personalizados
Pode criar personalizado [pontos finais](iot-hub-devguide-endpoints.md) num IoT hub para entregar mensagens distribuídas por um [regra encaminhamento](#routing-rules). Os pontos finais personalizados ligam diretamente para um hub de eventos, uma fila de barramento de serviço ou um tópico de barramento de serviço.

## <a name="custom-gateway"></a>Gateway personalizado
Um gateway ativa a conectividade para dispositivos que não é possível ligar diretamente à [IoT Hub](#iot-hub). Pode utilizar [Azure IoT Edge](#azure-iot-edge) criar gateways personalizados que implementa lógica personalizada para processar mensagens, conversões de protocolo personalizado e processamento adicional no limite.

## <a name="data-point-message"></a>Mensagem de ponto de dados
Uma mensagem de ponto de dados é um [dispositivo para nuvem](#device-to-cloud) mensagem que contém [telemetria](#telemetry) dados, tais como velocidade do vento ou temperatura.

## <a name="desired-configuration"></a>Configuração pretendida
No contexto de um [dispositivo duplo](iot-hub-devguide-device-twins.md), assim o desejar configuração refere-se para o conjunto completo de propriedades e os metadados no dispositivo duplo que deve ser sincronizado com o dispositivo.

## <a name="desired-properties"></a>Propriedades pretendidas
No contexto de um [dispositivo duplo](iot-hub-devguide-device-twins.md), pretendido propriedades é uma subsecção do dispositivo duplo que é utilizado com [comunicadas propriedades](#reported-properties) para sincronizar a configuração do dispositivo ou condição. Propriedades pretendidas só podem ser definidas uma [aplicações back-end](#back-end-app) e são respeitadas pelo [aplicação de dispositivo](#device-app).

## <a name="device-to-cloud"></a>Dispositivo-nuvem
Refere-se às mensagens enviadas a partir de um dispositivo ligado para [IoT Hub](#iot-hub). Estas mensagens podem ser [ponto de dados](#data-point-message) ou [interativa](#interactive-message) mensagens. Para obter mais informações, consulte [enviar e receber mensagens com o IoT Hub](iot-hub-devguide-messaging.md).

## <a name="device"></a>Dispositivo
No contexto do IoT, um dispositivo é, normalmente, um dispositivo de informática do autónomo em pequena escala, que pode recolher dados ou outros dispositivos de controlo. Por exemplo, um dispositivo pode ser um dispositivo de monitorização ambiental, ou um controlador para os sistemas de watering e ventilação um greenhouse. O [catálogo dispositivo](https://catalog.azureiotsuite.com/) fornece uma lista de dispositivos de hardware certificadas para funcionar com [IoT Hub](#iot-hub).

## <a name="device-app"></a>Aplicação de dispositivo
Uma aplicação de dispositivo é executado no seu [dispositivo](#device) e processa a comunicação com o seu [IoT hub](#iot-hub). Normalmente, utilize um do [SDKs do Azure IoT device](#azure-iot-device-sdks) quando implementar uma aplicação de dispositivo. Em muitos dos tutoriais IoT, utilize um [dispositivo simulado](#simulated-device) para sua comodidade.

## <a name="device-condition"></a>Condição de dispositivo
Refere-se a informações de estado do dispositivo, tal como o método de conectividade atualmente em utilização, conforme comunicado por um [aplicação de dispositivo](#device-app). [Aplicações de dispositivos](#device-app) também pode comunicar as respetivas capacidades. Pode consultar as informações de condição e a capacidade de utilizar dispositivos duplos.

## <a name="device-data"></a>Dados de dispositivo
Dados de dispositivo refere-se aos dados armazenados no IoT Hub por dispositivo [registo de identidade](#identity-registry). É possível importar e exportar estes dados.

## <a name="device-explorer"></a>Explorador de dispositivo
O [Explorador de dispositivo](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer) é uma ferramenta que é executado no Windows e permite-lhe gerir os seus dispositivos no [registo de identidade](#identity-registry). A ferramenta também pode enviar e receber mensagens nos seus dispositivos.

## <a name="device-identities-rest-api"></a>API REST de Identidades do Dispositivo
O [API de REST de identidades de dispositivo](https://docs.microsoft.com/rest/api/iothub/iothubresource) permite-lhe gerir os seus dispositivos registados no [registo de identidade](#identity-registry) utilizando uma API REST. Normalmente, deve utilizar um do nível mais elevado [service SDKs](#azure-iot-service-sdks) conforme mostrado nos tutoriais do IoT Hub.

## <a name="device-identity"></a>Identidade do dispositivo
A identidade de dispositivo é o identificador exclusivo atribuído a cada dispositivo registado no [registo de identidade](#identity-registry).

## <a name="device-management"></a>Gestão de dispositivos
Gestão de dispositivos abrange o ciclo de vida completo associado à gestão de dispositivos na sua solução de IoT, incluindo o planeamento, aprovisionamento, configurar, monitorizar e extinção.

## <a name="device-management-patterns"></a>Padrões da gestão de dispositivos
[IoT hub](#iot-hub) permite padrões de gestão comuns do dispositivo, incluindo a ser reiniciado, efetuar reposições de fábrica e efetuar atualizações de firmware nos seus dispositivos.

## <a name="device-messaging-rest-api"></a>API REST de Mensagens do Dispositivo
Pode utilizar o [API REST de mensagens do dispositivo](https://docs.microsoft.com/rest/api/iothub/httpruntime) de um dispositivo para enviar mensagens do dispositivo para nuvem a um IoT hub e receber [nuvem para o dispositivo](#cloud-to-device) mensagens a partir de um hub IoT. Normalmente, deve utilizar um do nível mais elevado [SDKs do dispositivo](#azure-iot-device-sdks) conforme mostrado nos tutoriais do IoT Hub.

## <a name="device-provisioning"></a>Aprovisionamento de dispositivos
Aprovisionamento de dispositivos é o processo de adicionar iniciais [dados de dispositivo](#device-data) para os arquivos na sua solução. Para ativar um novo dispositivo ligar ao seu hub, tem de adicionar um ID de dispositivo e as chaves para o IoT Hub [registo de identidade](#identity-registry). Como parte do processo de aprovisionamento, poderá ter de inicializar dados específicos do dispositivo nos outros arquivos de solução.

## <a name="device-twin"></a>Dispositivo duplo
A [dispositivo duplo](iot-hub-devguide-device-twins.md) é o documento JSON que armazena informações de estado do dispositivo como metadados, configurações e condições. [IoT Hub](#iot-hub) persistir um dispositivo duplo para cada dispositivo que for aprovisionado no seu IoT hub. Dispositivos duplos permitem-lhe sincronizar [condições de dispositivo](#device-condition) e configurações entre o dispositivo e a solução de back-end. Pode consultar dispositivos duplos localizar dispositivos específicos e consultar o estado das operações de execução longa.

## <a name="device-twin-queries"></a>Consultas do dispositivo duplo
[As consultas do dispositivo duplo](iot-hub-devguide-query-language.md) utilizar o idioma de consulta do SQL Server como o IoT Hub para obter informações do seu dispositivos duplos. Pode utilizar a mesma linguagem de consulta do IoT Hub para obter informações sobre [tarefas](#job) em execução no seu IoT hub.

## <a name="device-twin-rest-api"></a>API de REST do dispositivo duplo
Pode utilizar o [API de REST do dispositivo duplo](https://docs.microsoft.com/rest/api/iothub/devicetwinapi) da solução de back-end para gerir os dispositivos duplos. A API permite-lhe obter e atualizar [dispositivo duplo](#device-twin) propriedades e invocar [direcionar métodos](#direct-method). Normalmente, deve utilizar um do nível mais elevado [service SDKs](#azure-iot-service-sdks) conforme mostrado nos tutoriais do IoT Hub.

## <a name="device-twin-synchronization"></a>Sincronização do dispositivo duplo
Sincronização do dispositivo duplo utiliza o [pretendido propriedades](#desired-properties) nos seus dispositivos duplos para configurar os seus dispositivos e obter [comunicadas propriedades](#reported-properties) dos seus dispositivos para armazenar no dispositivo duplo.

## <a name="direct-method"></a>Método direto
A [método direto](iot-hub-devguide-direct-methods.md) é uma forma para que possa acionar um método a executar num dispositivo invocando uma API no seu IoT hub.

## <a name="endpoint"></a>Ponto Final
Um IoT hub expõe vários [pontos finais](iot-hub-devguide-endpoints.md) que permitem que as suas aplicações para estabelecer ligação ao IoT hub. Existem pontos finais de orientado para o dispositivo que permitem que os dispositivos efetuar operações como enviar [dispositivo para nuvem](#device-to-cloud) receção e as mensagens [nuvem para o dispositivo](#cloud-to-device) mensagens. Existem pontos finais de gestão de serviço com acesso à ativarem [aplicações de back-end](#back-end-app) para efetuar operações como [identidade de dispositivo](#device-identity) gestão e a gestão do dispositivo duplo. Existem orientado para o serviço [pontos finais incorporados](#built-in-endpoints) para ler mensagens do dispositivo para a nuvem. Pode criar [pontos finais personalizados](#custom-endpoints) para receber mensagens dispositivo-nuvem distribuídas por um [regra encaminhamento](#routing-rules).

## <a name="event-hubs-service"></a>Serviço de Hubs de eventos
[Os Event Hubs](../event-hubs/event-hubs-what-is-event-hubs.md) é um serviço de entrada de dados altamente dimensionável que pode ingerir milhões de eventos por segundo. O serviço permite-lhe processar e analisar as quantidades enormes de dados produzidos pelos seus dispositivos e aplicações ligados. Para ver uma comparação com o serviço do IoT Hub, consulte [comparação do IoT Hub do Azure e Event Hubs do Azure](iot-hub-compare-event-hubs.md).

## <a name="event-hub-compatible-endpoint"></a>Ponto final compatível com o Hub de eventos
Ler [dispositivo para nuvem](#device-to-cloud) as mensagens enviadas ao seu IoT hub, pode ligar a um ponto final no seu hub e utilizar qualquer método compatível com o Event Hub para ler as mensagens. Métodos de compatível com o Hub de eventos incluem a utilização de [SDKs de Hubs de eventos](../event-hubs/event-hubs-programming-guide.md) e [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md).

## <a name="field-gateway"></a>Gateway de campo
Um gateway de campo ativa a conectividade para dispositivos que não é possível ligar diretamente à [IoT Hub](#iot-hub) e é normalmente implementado localmente nos seus dispositivos. Para obter mais informações, consulte [que é o IoT Hub do Azure?](iot-hub-what-is-iot-hub.md)

## <a name="free-account"></a>Conta gratuita
Pode criar um [conta do Azure gratuita](https://azure.microsoft.com/pricing/free-trial/) para concluir os tutoriais do IoT Hub e experimentar o serviço de IoT Hub (e outros serviços do Azure).

## <a name="gateway"></a>Gateway
Um gateway ativa a conectividade para dispositivos que não é possível ligar diretamente à [IoT Hub](#iot-hub). Consulte também [Gateway de campo](#field-gateway), [Gateway de nuvem](#cloud-gateway), e [Gateway personalizado](#custom-gateway).

## <a name="identity-registry"></a>Registo de identidade
O [registo de identidade](iot-hub-devguide-identity-registry.md) é o componente incorporado de um hub IoT que armazena informações sobre os dispositivos individuais autorizado a ligar a um IoT hub.

## <a name="interactive-message"></a>Mensagem interativa
É uma mensagem interativa um [nuvem para o dispositivo](#cloud-to-device) mensagem que aciona uma ação imediata a solução de back-end. Por exemplo, um dispositivo pode enviar um alarme sobre uma falha que deve ser automaticamente a sessão para um sistema CRM.

## <a name="iot-hub"></a>IoT Hub
IoT Hub é um serviço completamente gerido do Azure que permite comunicações bidirecionais fiáveis e seguras entre milhões de dispositivos e uma solução de back-end. Para obter mais informações, consulte [que é o IoT Hub do Azure?](iot-hub-what-is-iot-hub.md) Utilizar o [subscrição do Azure](#subscription), pode criar os hubs IoT para processar o seu IoT mensagens cargas de trabalho.

## <a name="iot-hub-metrics"></a>Métricas de IoT Hub
[Métricas de IoT Hub](iot-hub-metrics.md) dão-lhe dados sobre o estado dos hubs IoT no seu [subscrição do Azure](#subscription). Métricas de IoT Hub permitem-lhe avaliar o estado de funcionamento geral do serviço e os dispositivos ligados ao mesmo. Métricas de IoT Hub podem ajudar a ver o que se passa seu IoT hub e investigar problemas de causa raiz sem ser necessário contactar o suporte do Azure.

## <a name="iot-hub-query-language"></a>Linguagem de consulta do IoT Hub
O [idioma de consulta do IoT Hub](iot-hub-devguide-query-language.md) é uma linguagem semelhante a SQL que lhe permite consultar o [tarefas](#job) e dispositivos duplos.

## <a name="iot-hub-resource-provider-rest-api"></a>Fornecedor de recursos do IoT Hub REST API
Pode utilizar o [API de REST do fornecedor de recursos do IoT Hub](https://docs.microsoft.com/rest/api/iothub/resourceprovider/iot-hub-resource-provider-rest) para gerir os hubs IoT no seu [subscrição do Azure](#subscription) efetuar operações como criar, atualizar e eliminar hubs.

## <a name="iot-suite"></a>Suite IoT
Azure IoT Suite reúne vários serviços do Azure com soluções pré-configuradas. Estas soluções pré-configuradas permitem-lhe começar a trabalhar rapidamente com implementações ponto-a-ponto dos cenários comuns do IoT. Para obter mais informações, consulte [que é o Azure IoT Suite?](../iot-suite/iot-suite-overview.md)

## <a name="iothub-explorer"></a>Explorador de iothub
O [iothub explorer](https://github.com/azure/iothub-explorer) é uma ferramenta de linha de comandos, de plataforma. A ferramenta permite-lhe gerir os seus dispositivos no [registo de identidade](#identity-registry), enviar e receber mensagens e os ficheiros dos seus dispositivos e monitorizar as operações de hub IoT.

## <a name="job"></a>Tarefa
Pode utilizar a sua solução de back-end [tarefas](iot-hub-devguide-jobs.md) para agendar e controlar as atividades num conjunto de dispositivos registados com o seu IoT hub. As atividades incluem a atualizar o dispositivo duplo [pretendido propriedades](#desired-properties), atualizar dispositivo duplo [etiquetas](#tags)e invocar [direcionar métodos](#direct-method). [IoT Hub](#iot-hub) também utiliza as tarefas [importar para e exportar](iot-hub-devguide-identity-registry.md#import-and-export-device-identities) do [registo de identidade](#identity-registry).

## <a name="jobs-rest-api"></a>Tarefas de REST API
O [API de REST de tarefas](https://docs.microsoft.com/rest/api/iothub/jobapi) permite-lhe gerir [tarefas](#job) em execução no seu IoT hub.

## <a name="module"></a>Módulo
No [Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md), um [módulo](../iot-edge/iot-edge-modules.md) é um componente que executa uma tarefa específica. As tarefas podem incluir uma mensagem a partir de um dispositivo de ingestão de relacionadas, transformar uma mensagem ou enviar uma mensagem para um hub IoT. Um mediador é responsável por reencaminhamento de mensagens entre módulos. Limite de IoT do Azure inclui um conjunto de módulos de exemplo. Também pode criar os seus próprios módulos personalizados.

## <a name="mqtt"></a>MQTT
[MQTT](http://mqtt.org/) é uma da mensagens protocolos que [IoT Hub](#iot-hub) suporta para comunicar com os dispositivos. Para obter mais informações sobre os protocolos de mensagens que suporta o IoT Hub, consulte [enviar e receber mensagens com o IoT Hub](iot-hub-devguide-messaging.md).

## <a name="operations-monitoring"></a>Monitorização de operações
IoT Hub [operações de monitorização](iot-hub-operations-monitoring.md) permite-lhe monitorizar o estado das operações no seu IoT hub em tempo real. [IoT Hub](#iot-hub) controla os eventos em várias categorias de operações. Pode participar num envio de eventos de uma ou mais categorias para um ponto final de IoT Hub para processamento. Pode monitorizar os dados de erros ou configurar o processamento de mais complexo com base nos padrões de dados.

## <a name="physical-device"></a>Dispositivo físico
Um dispositivo físico é um dispositivo real, tais como um Raspberry Pi que liga a um IoT hub. Para sua comodidade, muitos dos tutoriais do IoT Hub utilizam [dispositivos simulados](#simulated-device) para ativar a execução de amostras no seu computador local.

## <a name="primary-and-secondary-keys"></a>Chaves primárias e secundárias
Quando liga a um ponto de final orientado para o dispositivo ou orientado para o serviço num IoT hub, o [cadeia de ligação](#connection-string) incluem a chave para conceder a aceder. Ao adicionar um dispositivo para o [registo de identidade](#identity-registry) ou adicione um [partilhado a política de acesso](#shared-access-policy) ao seu hub, o serviço gera uma chave primária e secundária. Ter duas chaves permite-lhe rollover da chave de um para outro quando atualizar uma chave sem perderem o acesso ao IoT hub.

## <a name="protocol-gateway"></a>Gateway de protocolo
Um gateway de protocolo normalmente é implementado na nuvem e fornece serviços de tradução para dispositivos que se ligam de protocolo [IoT Hub](#iot-hub). Para obter mais informações, consulte [que é o IoT Hub do Azure?](iot-hub-what-is-iot-hub.md)

## <a name="quotas-and-throttling"></a>Quotas e limitação
Existem várias [quotas](iot-hub-devguide-quotas-throttling.md) que se aplicam à sua utilização [IoT Hub](#iot-hub), muitos das quotas variam com base na camada do IoT hub. [IoT Hub](#iot-hub) também se aplica [acelera](iot-hub-devguide-quotas-throttling.md) à sua utilização do serviço em tempo de execução.

## <a name="reported-configuration"></a>Configuração comunicada
No contexto de um [dispositivo duplo](iot-hub-devguide-device-twins.md), comunicados configuração refere-se para o conjunto completo de propriedades e os metadados no dispositivo duplo que deve ser reportado para o solução de back-end.

## <a name="reported-properties"></a>Propriedades comunicadas
No contexto de um [dispositivo duplo](iot-hub-devguide-device-twins.md), reportou propriedades é uma subsecção do dispositivo duplo utilizado com [pretendido propriedades](#desired-properties) para sincronizar a configuração do dispositivo ou condição. Só é possível definir propriedades comunicadas [aplicação de dispositivo](#device-app) e podem ser de leitura e consultadas a um [aplicações back-end](#back-end-app).

## <a name="resource-group"></a>Grupo de recursos
[O Azure Resource Manager](#azure-resource-manager) utiliza grupos de recursos para agrupar os recursos relacionados. Pode utilizar um grupo de recursos para efetuar operações em todos os recursos no grupo de em simultâneo.

## <a name="retry-policy"></a>Política de repetição
Utilizar uma política de repetição para lidar com [erros transitórios](https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx) quando liga a um serviço em nuvem.

## <a name="routing-rules"></a>Regras de encaminhamento
Configurar [regras de encaminhamento](iot-hub-devguide-messages-read-custom.md) no seu IoT hub para encaminhar mensagens do dispositivo para a nuvem para um [ponto final incorporado](#built-in-endpoints) ou [pontos finais personalizados](#custom-endpoints) para processamento através da sua solução de back-end .

## <a name="sasl-plain"></a>SASL SIMPLES
SASL simples é um protocolo que os [AMQP](#advanced-message-queue-protocol) utiliza o protocolo para transferência de tokens de segurança.

## <a name="shared-access-signature"></a>Assinatura de acesso partilhado
Assinaturas de acesso partilhado (SAS) são um mecanismo de autenticação com base nos hashes segurados SHA-256 ou URI. Autenticação de SAS tem dois componentes: um _política de acesso partilhado_ e um _assinatura de acesso partilhado_ (frequentemente designado por um token). Um dispositivo utiliza SAS para autenticar com um IoT hub. [Aplicações de back-end](#back-end-app) também utilizar SAS para autenticar com os pontos finais de orientado para o serviço num IoT hub. Normalmente, incluem o token SAS no [cadeia de ligação](#connection-string) que uma aplicação utiliza para estabelecer uma ligação a um IoT hub.

## <a name="shared-access-policy"></a>Política de acesso partilhado
Uma política de acesso partilhado define as permissões concedidas a qualquer pessoa que tenha um [chave primária ou secundária](#primary-and-secondary-keys) associados a essa política. Pode gerir as políticas de acesso partilhado e as chaves para o seu hub no [portal](#azure-portal).

## <a name="simulated-device"></a>Dispositivo simulado
Para sua comodidade, muitos dos tutoriais do IoT Hub utilizarem dispositivos simulados para ativar a execução de amostras no seu computador local. Em contrapartida, uma [dispositivo físico](#physical-device) é um dispositivo real, tais como um Raspberry Pi que liga a um IoT hub.

## <a name="solution"></a>Solução
A _solução_ pode fazer referência a uma solução do Visual Studio que inclui os projetos de um ou mais. A _solução_ também pode referir-se a uma solução de IoT que inclui elementos, tais como dispositivos, [aplicações de dispositivos](#device-app), um hub IoT, outros serviços do Azure, e [aplicações de back-end](#back-end-app).

## <a name="subscription"></a>Subscrição
Uma subscrição do Azure é onde ocorre faturação. Cada recurso do Azure cria ou serviço do Azure que utiliza está associado uma única subscrição. Quotas muitos também se aplicam a nível de uma subscrição.

## <a name="system-properties"></a>Propriedades do sistema
No contexto de um [dispositivo duplo](iot-hub-devguide-device-twins.md), as propriedades do sistema são só de leitura e incluir informações sobre a utilização de dispositivos, tais como o último Estado de ligação e tempo de atividade.

## <a name="tags"></a>Etiquetas
No contexto de um [dispositivo duplo](iot-hub-devguide-device-twins.md), as etiquetas são os metadados do dispositivo armazenada e obtida através do solução de back-end sob a forma de um documento JSON. As etiquetas não estão visíveis para as aplicações num dispositivo.

## <a name="telemetry"></a>Telemetria
Dispositivos recolher dados de telemetria, tais como velocidade do vento ou temperatura e utilizar [mensagens de ponto de dados](#data-point-messages) para enviar a telemetria para um hub IoT.

## <a name="token-service"></a>Serviço de tokens
Pode utilizar um serviço de tokens para implementar um mecanismo de autenticação para os seus dispositivos. Utiliza um IoT Hub [partilhado a política de acesso](#shared-access-policy) com **DeviceConnect** permissões para criar *no âmbito do dispositivo* tokens. Estes tokens permitem que um dispositivo ligar ao seu IoT hub. Um dispositivo utiliza um mecanismo de autenticação personalizado para autenticar com o serviço de tokens. Se o dispositivo efetua a autenticação com êxito, o serviço de token emite um token SAS para o dispositivo utilize para aceder ao seu IoT hub.

## <a name="x509-client-certificate"></a>Certificado de cliente de x. 509
Um dispositivo pode utilizar um certificado x. 509 para autenticar com [IoT Hub](#iot-hub). Utilizar um certificado x. 509 é uma alternativa à utilização de um [SAS token](#shared-access-signature).

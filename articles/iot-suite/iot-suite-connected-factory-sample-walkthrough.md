---
title: "Instruções da solução de fábrica ligada - Azure | Microsoft Docs"
description: "Uma descrição da solução pré-configurada de fábrica ligada do Azure IoT e respetiva arquitetura."
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 31fe13af-0482-47be-b4c8-e98e36625855
ms.service: iot-suite
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/14/2017
ms.author: dobett
ms.openlocfilehash: 237ca28c699984e89127a95b2141fe9131ad868c
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2017
---
# <a name="connected-factory-preconfigured-solution-walkthrough"></a>Instruções da solução pré-configurada de fábrica ligada

A [solução pré-configurada][lnk-preconfigured-solutions] de fábrica ligada do IoT Suite é uma implementação de uma solução industrial ponta a ponta que:

* Se liga quer a dispositivos industriais simulados que executem servidores OPC UA em linhas de produção de fábricas simuladas, quer a dispositivos de servidor OPC UA reais. Para obter mais informações sobre OPC UA, veja as [FAQ sobre a fábrica ligada](iot-suite-faq-cf.md).
* Mostra KPIs e OEE operacionais relativos a esses dispositivos e linhas de produção.
* Demonstra como utilizar uma aplicação baseada na cloud para interagir com sistemas de servidores OPC UA.
* Permite-lhe ligar os seus próprios dispositivos de servidor OPC UA.
* Permite-lhe procurar e modificar os dados do servidor OPC UA.
* Integra-se com o serviço Azure Time Series Insights (TSI), para proporcionar vistas personalizadas dos dados a partir dos servidores OPC UA.

Pode utilizar a solução como um ponto de partida para a sua própria implementação e [personalizá-la][lnk-customize] para satisfazer os seus requisitos comerciais específicos.

Este artigo acompanha-o através de alguns dos elementos-chave da solução de fábrica ligada, para que possa compreender como funciona. O artigo também descreve como os dados fluem através da solução. Estes conhecimentos ajudam a:

* Resolver problemas na solução.
* Planear a forma de personalizar a solução para satisfazer os seus próprios requisitos específicos.
* Estruturar a sua própria solução de IoT que utiliza os serviços do Azure.

Para obter mais informações , veja as [FAQ sobre a fábrica ligada](iot-suite-faq-cf.md).

## <a name="logical-architecture"></a>Arquitetura lógica

O diagrama que se segue descreve os componentes lógicos da solução pré-configurada:

![Arquitetura lógica da fábrica ligada][connected-factory-logical]

## <a name="communication-patterns"></a>Padrões de comunicação

A solução utiliza a [Especificação OPC UA Pub/Sub](https://opcfoundation.org/news/opc-foundation-news/opc-foundation-announces-support-of-publish-subscribe-for-opc-ua/) para enviar dados de telemetria de OPC UA para o Hub IoT no formato JSON. A solução utiliza o módulo do IoT Edge do [Publicador OPC](https://github.com/Azure/iot-edge-opc-publisher) para esta finalidade.

A solução também tem um cliente de OPC UA integrado numa aplicação Web que pode estabelecer ligações com servidores OPC UA no local. O cliente utiliza um [proxy inverso](https://wikipedia.org/wiki/Reverse_proxy) e recebe ajuda a partir do Hub IoT para fazer a ligação sem necessidade de abrir portas na firewall no local. Este padrão de comunicação é denominado [comunicação auxiliada](https://blogs.msdn.microsoft.com/clemensv/2014/02/09/service-assisted-communication-for-connected-devices/). A solução utiliza o módulo de IoT Edge do [OPC Proxy](https://github.com/Azure/iot-edge-opc-proxy/) para esta finalidade.


## <a name="simulation"></a>Simulação

As estações simuladas e os sistemas de execução de fabrico simulados (MES, Manufacturing Execution Systems) são compostos por linhas de produção de fábrica. Os dispositivos simulados e o Módulo de Publicador OPC baseiam-se na [Normal OPC UA .NET][lnk-OPC-UA-NET-Standard] publicada pela OPC Foundation.

O Proxy OPC e o Publicador OPC são implementados como módulos baseados no [Azure IoT Edge][lnk-Azure-IoT-Gateway]. Cada linha de produção simulada tem um gateway designado anexado.

Todos os componentes da simulação são executados em contentores do Docker alojados numa VM do Linux do Azure. A simulação está configurada para executar oito linhas de produção simuladas por predefinição.

## <a name="simulated-production-line"></a>Linha de produção simulada

Uma linha de produção fabrica peças. É composta por diferentes estações: uma estação de montagem, uma de teste e outra de empacotamento.

A simulação é executada e atualiza os dados que são expostos através dos nós de OPC UA. Todas as estações da linha de produção simulada são orquestradas pelo MES através do OPC UA.

## <a name="simulated-manufacturing-execution-system"></a>Sistema de execução de fabrico simulado

O MES monitoriza cada estação da linha de produção através de OPC UA para detetar alterações ao estado das estações. Chama métodos de OPC UA para controlar as estações e envia produtos de uma estação para outra até que estejam concluídos.

## <a name="gateway-opc-publisher-module"></a>Módulo de publicador OPC do gateway

O Módulo de Publicador do OPC liga-se aos serviços OPC UA da estação e subscreve os nós do OPC que vão ser publicados. O módulo converte os dados dos nós no formato JSON, encripta-os e envia-os para o Hub IoT como mensagens Pub/Sub de OPC UA.

O Módulo de Publicador OPC só precisa de uma porta https de saída (443) e funciona com a infraestrutura empresarial existente.

## <a name="gateway-opc-proxy-module"></a>Módulo de proxy OPC do gateway

O Módulo de Proxy OPC UA do Gateway faz o túnel de mensagens de comando e controlo binárias de OPC UA e só precisa de uma porta https de saída (443). Funciona com a infraestrutura empresarial existente, incluindo Proxies Web.

Utiliza métodos de Dispositivo Hub IoT para transferir dados TCP/IP empacotados na camada da aplicação e, assim, garante a fiabilidade do ponto final, a encriptação dos dados e a integridade através da utilização de SSL/TLS.

O protocolo binário OPC UA transmitido através do próprio proxy utiliza a autenticação e a encriptação UA.

## <a name="azure-time-series-insights"></a>Azure Time Series Insights

O Módulo de Publicador OPC do Gateway subscreve os nós dos servidores OPC UA para detetar alterações aos valores dos dados. Se for detetada uma alteração aos dados num dos nós, este módulo envia, então, mensagens para o Hub IoT do Azure.

O Hub IoT disponibiliza uma origem de eventos para o Azure TSI. O TSI armazena dados durante 30 dias com base nos carimbos de data/hora anexados às mensagens. Estes dados incluem:

* O ApplicationUri de OPC UA
* O NodeId de OPC UA
* O valor do nó
* O Carimbo de data/hora da origem
* O DisplayName de OPC UA

Atualmente, o TSI não permite aos clientes personalizar o período de tempo durante o qual pretendem manter os dados.

O TSI consulta os dados dos nós através de um **SearchSpan** [InvervalodePesquisa] \(**Time.From** [Hora.De], **Time.To** [Hora.Até]) e agrega por **OPC UA ApplicationUri** [UriAplicação OPC UA], **OPC UA NodeId** [IdNó OPC UA] ou **OPC UA DisplayName** [NomeaApresentar OPC UA].

Para obter os dados relativos aos medidores OEE e KPI, bem como os gráficos de séries temporais, os dados são agregados por contagem de eventos, Soma, Média, Mín e Máx.

As séries temporais são criadas com outro processo. O OEE e os KPIs são calculados a partir dos dados base das estações e agregados para a topologia (linhas de produção, fábricas, empresas) da aplicação.

Além disso, as séries temporais para a topologia de OEE e KPI são calculadas na aplicação, sempre que um intervalo de tempo apresentado estiver pronto. Por exemplo, a vista diária é atualizada a cada hora completa.

A vista de série temporal dos dados de nós vêm diretamente do TSI através de uma agregação para o intervalo de tempo.

## <a name="iot-hub"></a>IoT Hub
O [hub IoT][lnk-IoT Hub] recebe dados enviados a partir do Módulo de Publicador OPC para a cloud e disponibiliza-os para o serviço Azure TSI. 

O Hub IoT na solução também:
- Mantém um registo de identidades que armazena os IDs de todos os Módulos de Publicador OPC e de todos os Módulos de Proxy OPC.
- É utilizado como canal de transporte para comunicação bidirecional do Módulo de Proxy OPC.

## <a name="azure-storage"></a>Storage do Azure
A solução utiliza o Armazenamento de blobs do Azure como armazenamento de discos para a VM e para armazenar dados de implementação.

## <a name="web-app"></a>Aplicação Web
A aplicação Web implementada como parte da solução pré-configurada inclui um cliente OPC UA integrado processamento de alertas e visualização de telemetria.

## <a name="telemetry-data-flow"></a>Fluxo de dados de telemetria

![Fluxo de dados de telemetria](media/iot-suite-connected-factory-walkthrough/telemetry_dataflow.png)

### <a name="flow-steps"></a>Passos do fluxo

1. O Publicador OPC lê os certificados OPC UA X509 necessários e as credenciais de segurança do Hub IoT do arquivo de certificados local.
    - Se necessário, o Publicador OPC cria e armazena quaisquer certificados ou credenciais em falta no arquivo de certificados.

2. O Publicador OPC efetua o próprio registo no Hub IoT.
    - Utiliza o protocolo configurado. Pode utilizar qualquer protocolo suportado pelo SDK de cliente do Hub IoT. A predefinição é MQTT.
    - A comunicação do protocolo está protegida por TLS.

3. O Publicador OPC lê o ficheiro de configuração.

4. O Publicador OPC cria uma Sessão OPC com cada servidor OPC UA configurado.
    - Utiliza a ligação TCP.
    - O Publicador OPC e o servidor OPC UA autenticam-se mutuamente através de certificados X509.
    - Todo o tráfego OPC UA adicional é encriptado pelo mecanismo de encriptação OPC UA configurado.
    - O Publicador OPC cria, na Sessão OPC para cada intervalo de publicação configurado, uma Subscrição OPC.
    - Cria itens OPC Monitorizados para os Nós OPC a publicar na Subscrição OPC.

5. Se um valor do Nó OPC monitorizado for alterado, o Servidor OPC UA envia atualizações para o Publicador OPC.

6. O Publicador OPC transcodifica o novo valor.
    - Se a criação de batches estiver ativada, cria batches das várias alterações.
    - Cria uma mensagem do Hub IoT.

7. O Publicador OPC envia uma mensagem para o Hub IoT.
    - Utiliza o protocolo configurado.
    - A comunicação está protegida por TLS.

8. O Time Series Insights (TSI) lê as mensagens do Hub IoT.
    - Utiliza o AMQP através de TCP/TLS.
    - Este passo é interno para o datacenter.

9. Dados inativos no TSI.

10. A WebApp de fábrica ligada no Azure AppService consulta os dados necessários a partir do TSI.
    - Utiliza a comunicação protegida por TCP/TLS.
    - Este passo é interno para o datacenter.

11. O browser liga à WebApp de fábrica ligada.
    - Compõe o dashboard de fábrica ligada.
    - Liga por HTTPS.
    - O acesso à Aplicação de fábrica ligada requer autenticação do utilizador através do Azure Active Directory.
    - Todas as chamadas WebApi na aplicação de fábrica ligada são protegidas por Tokens Antifalsificação.

12. Nas atualizações de dados, a WebApp de fábrica ligada envia os dados atualizados para o browser.
    - Utiliza o protocolo SignalR.
    - Protegida por TCP/TLS.

## <a name="browsing-data-flow"></a>Fluxo de dados de navegação

![Fluxo de dados de navegação](media/iot-suite-connected-factory-walkthrough/browsing_dataflow.png)

### <a name="flow-steps"></a>Passos do fluxo

1. O Proxy OPC (componente de servidor) é iniciado.
    - Lê as chaves de acesso partilhado a partir de um arquivo local.
    - Se necessário, armazena as chaves de acesso em falta no arquivo.

2. O Proxy OPC (componente de servidor) efetua o próprio registo no Hub IoT.
    - Lê todos os dispositivos conhecidos a partir do Hub IoT.
    - Utiliza MQTT por TLS através de Socket ou Websocket Seguro.

3. O browser liga à WebApp de fábrica ligada e compõe o dashboard de fábrica ligada.
    - Utiliza HTTPS.
    - Um utilizador seleciona um servidor OPC UA ao qual ligar.

4. A WebApp de fábrica ligada estabelece uma Sessão OPC UA no servidor OPC UA selecionado.
    - Utiliza a pilha OPC UA.

5. O transporte do Proxy OPC recebe um pedido da pilha OPC UA para estabelecer uma ligação de socket TCP com o servidor OPC UA.
    - Obtém o payload TCP e utiliza-o inalterado.
    - Este passo é interno para a WebApp de fábrica ligada.

6. O Proxy OPC (componente de cliente) procura o dispositivo Proxy OPC (componente de servidor) no registo de dispositivos do Hub IoT. Em seguida, chama um método de dispositivo do dispositivo Proxy OPC (componente de servidor) no Hub IoT.
    - Utiliza HTTPS por TCP/TLS para procurar o Proxy OPC.
    - Utiliza HTTPS por TCP/TLS para estabelecer uma ligação de socket TCP com o servidor OPC UA.
    - Este passo é interno para o datacenter.

7. O Hub IoT chama um método de dispositivo no dispositivo Proxy OPC (componente de servidor).
    - Utiliza um MQTT estabelecido por TLS através de uma ligação de Socket ou Websocket Seguro para estabelecer uma ligação de socket TCP com o servidor OPC UA.

8. O Proxy OPC (componente de servidor) envia o payload TCP para a rede shopfloor.

9. O servidor OPC UA processa o payload e envia a resposta.

10. A resposta é recebida pelo socket do Proxy OPC (componente de servidor).
    - O Proxy OPC envia os dados como valor de retorno do método de dispositivo para o Hub IoT e o Proxy OPC (componente de cliente).
    - Estes dados são entregues na pilha OPC UA na aplicação de fábrica ligada.

11. A WebApp de fábrica ligada devolve o OPC Browser UX enriquecido com as informações específicas do OPC UA que recebeu do servidor OPC UA para composição pelo Browser.
    - Ao navegar pelo espaço de endereços OPC e ao aplicar as funções nos nós do espaço de endereços OPC, o cliente OPC Browser UX utiliza as chamadas AJAX por HTTPS seguro com Tokens Antifalsificação para obter dados da WebApp de fábrica ligada.
    - Se necessário, o cliente utiliza a comunicação explicada nos passos 4 a 10 para trocar informações com o servidor OPC UA.

> [!NOTE]
> O Proxy OPC (componente de servidor) e o Proxy OPC (componente de cliente) concluem os passos 4 a 10 para todo o tráfego TCP relacionado com a comunicação OPC UA.

> [!NOTE]
> Para o servidor OPC UA e a pilha OPC UA na WebApp de fábrica ligada, a comunicação do Proxy OPC é transparente e aplicam-se todas as funcionalidades de segurança OPC UA para autenticação e encriptação.

## <a name="next-steps"></a>Passos seguintes

Pode continuar a introdução ao IoT Suite ao ler os artigos seguintes:

* [Permissões no site azureiotsuite.com][lnk-permissions]
* [Implementar um gateway no Windows ou Linux para a solução de fábrica ligada pré-configurada](iot-suite-connected-factory-gateway-deployment.md)
* [Implementação de referência do Publicador OPC](iot-suite-connected-factory-publisher.md).

[connected-factory-logical]:media/iot-suite-connected-factory-walkthrough/cf-logical-architecture.png

[lnk-preconfigured-solutions]: iot-suite-what-are-preconfigured-solutions.md
[lnk-customize]: iot-suite-v1-guidance-on-customizing-preconfigured-solutions.md
[lnk-IoT Hub]: https://azure.microsoft.com/documentation/services/iot-hub/
[lnk-direct-methods]: ../iot-hub/iot-hub-devguide-direct-methods.md
[lnk-OPC-UA-NET-Standard]:https://github.com/OPCFoundation/UA-.NETStandardLibrary
[lnk-Azure-IoT-Gateway]: https://github.com/azure/iot-edge
[lnk-permissions]: iot-suite-v1-permissions.md

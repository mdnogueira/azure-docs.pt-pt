---
title: "Compreender o tempo de execução do limite do Azure IoT | Microsoft Docs"
description: "Saiba mais sobre o tempo de execução do limite de IoT do Azure e como garante os dispositivos de limite"
services: iot-edge
keywords: 
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 10/05/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 7b37f9e103644d2492f69f4a4cc80d3fd57d4aa4
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/15/2017
---
# <a name="understand-the-azure-iot-edge-runtime-and-its-architecture---preview"></a>Compreender o tempo de execução do limite do Azure IoT e respetiva arquitetura – pré-visualização

O tempo de execução do limite de IoT é uma coleção de programas que precisam de ser instalada num dispositivo para o mesmo ser considerado um dispositivo de limite de IoT. Coletivamente, os componentes do tempo de execução de limite de IoT permitem que os dispositivos IoT Edge receber o código para executar no limite e comunicam os resultados. 

O tempo de execução do limite de IoT efetua as seguintes funções nos dispositivos de limite de IoT:

* Instala e atualiza as cargas de trabalho no dispositivo.
* Mantém as normas de segurança do Azure IoT Edge no dispositivo.
* Garante que [módulos de limite de IoT][lnk-módulos] sempre em execução.
* Relatórios de estado de funcionamento do módulo para a nuvem para a monitorização remota.
* Facilita a comunicação entre dispositivos de folha a jusante e o dispositivo de limite de IoT.
* Facilita a comunicação entre os módulos no dispositivo de limite de IoT.
* Facilita a comunicação entre o dispositivo de limite de IoT e na nuvem.

![Tempo de execução do limite de IoT comunica insights e o estado de funcionamento do módulo ao IoT Hub][1]

Responsabilidades do tempo de execução do limite de IoT enquadram-se em duas categorias: módulo Gestão e de comunicação. Estas duas funções são efetuadas por dois componentes que compõem o tempo de execução do limite de IoT. O hub IoT Edge é responsável pela comunicação, enquanto o agente de limite de IoT gere a implementação e os módulos de monitorização. 

O agente de limite e o hub de limite são módulos, tal como qualquer outro módulo em execução num dispositivo de limite de IoT. Para obter mais informações sobre como funcionam os módulos, consulte [lnk-módulos]. 

## <a name="iot-edge-hub"></a>Hub IoT Edge

O hub de limite é um dos dois módulos que constituem o tempo de execução do limite de IoT do Azure. Atua como um proxy local para o IoT Hub resultariam da exposição pontos finais de protocolo mesmo como o IoT Hub. Este consistência significa que os clientes (se dispositivos ou os módulos) pode ligar para o tempo de execução do IoT Edge como fariam ao IoT Hub. 

>[!NOTE]
> Durante a pré-visualização pública Edge Hub só suporta clientes que se ligam utilizando MQTT.

O limite do hub não é uma versão completa do IoT Hub executar localmente. Existem alguns aspetos silenciosamente delega o hub de limite ao IoT Hub. Por exemplo, o Edge hub reencaminha os pedidos de autenticação ao IoT Hub quando um dispositivo primeiro tenta ligar. Após a primeira ligação for estabelecida, as informações de segurança é colocado em cache localmente pelo Edge hub. As ligações subsequentes partir desse dispositivo são permitidas sem terem de autenticar para a nuvem. 

>[!NOTE]
> Durante a pré-visualização pública o tempo de execução deve estar ligado sempre que tentar autenticar um dispositivo.

Para reduzir a largura de banda da sua solução de IoT Edge utiliza, o hub de limite otimiza o número de ligações efetivas são efetuadas para a nuvem. Hub de contorno leva lógicas ligações de clientes, como módulos ou dispositivos de folha e combina-los para uma única ligação física para a nuvem. Os detalhes deste processo são transparentes para o resto da solução. Os clientes pensar têm as seus próprios ligação para a nuvem, apesar de serem todas a ser enviados através da ligação do mesma. 

![Hub de limite atua como um gateway entre vários dispositivos físicos e a nuvem][2]

Hub de limite pode determinar se está ligado ao IoT Hub. Se se perder a ligação, o hub de limite guarda mensagens ou as atualizações de duplo localmente. Assim que for restabelecer uma ligação, sincroniza-se todos os dados. A localização utilizada para esta cache temporário é determinada por uma propriedade do duplo de módulo do hub Edge. O tamanho da cache não é limitado e irá aumentar desde que o dispositivo tem capacidade de armazenamento. 

>[!NOTE]
>Adicionar controlo sobre os parâmetros de colocação em cache adicionais será adicionada ao produto, antes de ser disponibilidade geral.

### <a name="module-communication"></a>Comunicação de módulo

Hub de limite facilita a comunicação de módulo para o módulo. Utilizar Edge Hub como um mediador de mensagens mantém módulos independentes entre si. Módulos só tem de especificar as entradas no qual aceitar as saídas para que estes escrever mensagens e as mensagens. Em seguida, um programador de solução stitches estas entradas e saídas em conjunto para que os módulos processam os dados na ordem específica para essa solução. 

![Hub de limite facilita a comunicação de módulo de módulo][3]

Para enviar dados para o hub de limite, um módulo chama o método de SendEventAsync. O primeiro argumento especifica em qual saída para enviar a mensagem. A seguinte pseudocode envia uma mensagem no output1:

    DeviceClient client = new DeviceClient.CreateFromConnectionString(moduleConnectionString, settings); 
    await client.OpenAsync(); 
    await client.SendEventAsync(“output1”, message); 

Para receber uma mensagem, registe-se uma chamada de retorno que processa mensagens futuras numa introdução específica. A seguinte pseudocode regista messageProcessor a função a ser utilizada para processar todas as mensagens recebidas em input1:

    await client.SetEventHandlerAsync(“input1”, messageProcessor, userContext);
    
O Programador de solução é responsável por especificando as regras que determinam como o hub de limite passa mensagens entre módulos. As regras de encaminhamento estão definidas na nuvem e enviadas para baixo para hub Edge no respetivo dispositivo duplo. A mesma sintaxe para rotas de IoT Hub é utilizada para definir as rotas entre os módulos no limite de IoT do Azure. 

<!--- For more info on how to declare routes between modules, see []. --->   

![Rotas entre os módulos][4]

## <a name="iot-edge-agent"></a>Agente de limite de IoT

O agente de limite de IoT é o módulo que constitui o tempo de execução do limite de IoT do Azure. É responsável pela instanciar módulos, garantindo que continuam em execução e reportar o estado dos módulos do IoT Hub. Tal como qualquer outro módulo, o agente de limite utiliza duplo respetivo módulo para armazenar estes dados de configuração. 

Para iniciar a execução do agente Edge, execute o comando de início do azure-iot-limite-tempo de execução-ctl.py. O agente obtém o respetivo duplo de módulo a partir do IoT Hub e inspeciona o dicionário de módulos. O dicionário de módulos é uma coleção de módulos que têm de ser iniciados. 

Cada item no dicionário de módulos contém informações específicas sobre um módulo e é utilizado pelo agente de contorno para controlar o ciclo de vida do módulo. Algumas das propriedades mais interessantes são: 

* **Settings.Image** – a imagem de contentor que o agente de limite utiliza para iniciar o módulo. O agente de limite deve ser configurado com as credenciais para o registo de contentor, se a imagem estiver protegida por uma palavra-passe. Para configurar o agente de limite, utilize o seguinte comando:`azure-iot-edge-runtime-ctl.py –configure`
* **settings.createOptions** – uma cadeia que é transmitida diretamente para o daemon de Docker ao iniciar o contentor de um módulo. A adição de opções de Docker esta propriedade permite opções avançadas, como a porta de reencaminhamento ou montar os volumes num contentor de um módulo.  
* **estado** – o estado em que o agente de Edge coloca o módulo. Este valor, normalmente, é definido como *executar* como a maioria das pessoas pretende que o agente de contorno para iniciar imediatamente a todos os módulos no dispositivo. No entanto, pode especificar o estado inicial de um módulo para ser interrompida e aguarde uma hora no futuro saber se o agente de contorno para iniciar um módulo. O agente de limite reporta o estado de cada módulo novamente para a nuvem nas propriedades que relatados. Uma diferença entre a propriedade pretendida e a propriedade comunicada é um indicador ou um dispositivo funcionar incorretamente. Os Estados suportados são:
   * A transferir
   * Em Execução
   * Estado de funcionamento incorreto
   * Com Falhas
   * Parada
* **restartPolicy** – como o agente de limite reinicia um módulo. Os valores possíveis incluem:
   * Nunca – o agente de limite nunca reinicia o módulo.
   * o agente de limite de onFailure - se o módulo de falhas, reinicia-lo. Se o módulo foi encerrado corretamente, o agente de limite não reiniciá-lo.
   * Mau estado de funcionamento - se o módulo de falhas ou é considerado mau estado de funcionamento, o agente de limite reinicia-lo.
   * -Se o módulo falha, é considerado em mau estado de funcionamento ou encerramento de qualquer forma, o agente de limite reinicia sempre-lo. 
   
### <a name="security"></a>Segurança

O agente de limite de IoT desempenha um papel fundamental na segurança de um dispositivo de limite de IoT. Por exemplo, executa ações como verificar a imagem de um módulo antes de iniciá-lo. Estas funcionalidades serão adicionadas na disponibilidade geral das funcionalidades de V2. 

<!-- For more information about the Azure IoT Edge security framework, see []. -->

## <a name="next-steps"></a>Passos seguintes

- [Compreender os módulos do Azure IoT Edge][lnk-módulos]

<!-- Images -->
[1]: ./media/iot-edge-runtime/pipeline.png
[2]: ./media/iot-edge-runtime/gateway.png
[3]: ./media/iot-edge-runtime/ModuleEndpoints.png
[4]: ./media/iot-edge-runtime/ModuleEndpointsWithRoutes.png

<!-- Links -->
[lnk-módulos]: iot-edge-modules.md
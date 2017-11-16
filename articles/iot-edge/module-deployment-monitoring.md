---
title: "Implementar módulos para o limite do Azure IoT | Microsoft Docs"
description: "Saiba mais sobre a forma como os módulos obterem implementados em dispositivos de limite"
services: iot-edge
keywords: 
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 10/05/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: d8688ab2daefd400e9c0948853459dd238fa0d43
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/15/2017
---
# <a name="understand-iot-edge-deployments-for-single-devices-or-at-scale---preview"></a>Compreender as implementações de IoT Edge para dispositivos único ou na escala – pré-visualização

Dispositivos de limite de IoT do Azure siga uma [ciclo de vida do dispositivo] [ lnk-lifecycle] que é semelhante de outros tipos de dispositivos IoT:

1. Dispositivos de limite de IoT são aprovisionados, que envolve um dispositivo com um SO de processamento de imagens e instalar o [IoT Edge runtime][lnk-runtime].
1. Os dispositivos estejam configurados para executar [módulos de limite de IoT][lnk-modules]e, em seguida, monitorizar estado de funcionamento. 
1. Por fim, os dispositivos poderão ser extinguidos quando são substituídas ou tornar-se obsoleto.  

Limite de IoT do Azure fornece duas formas de configurar os módulos para executar em dispositivos de limite de IoT: uma para desenvolvimento e rápidas iterações num único dispositivo (que é utilizado nos tutoriais do Azure IoT limite) e uma gestão fleets grande de dispositivos de limite de IoT. Ambos estes abordagens estão disponíveis no Portal do Azure e através de programação.

Este artigo foca-se na configuração e monitorização fases para fleets dos dispositivos, coletivamente referidas como implementações de limite de IoT. Os passos de implementação geral são os seguintes:   

1. Um operador define uma implementação que descreve um conjunto de módulos, bem como os dispositivos de destino. Cada implementação tem um manifesto de implementação que reflete esta informação. 
1. O serviço de IoT Hub comunica com todos os dispositivos direcionados para configurá-los com os módulos pretendidos. 
1. O serviço de IoT Hub obtém o estado dos dispositivos IoT Edge e analisa-os para o operador monitorizar.  Por exemplo, um operador pode ver quando um dispositivo de limite não está configurado com êxito ou se um módulo ocorre uma falha durante o tempo de execução. 
1. Em qualquer altura, os novos dispositivos de limite de IoT que cumprem as condições de destino estão configurados para a implementação. Por exemplo, uma implementação que vise automaticamente todos os dispositivos de IoT Edge no estado de Washington configura um novo dispositivo de limite de IoT, assim que está aprovisionado e adicionada ao grupo de dispositivos de estado de Washington. 
 
Este artigo explica cada componente envolvido numa configuração e monitorização de uma implementação. Para obter instruções sobre criar e atualizar uma implementação, consulte [implementar e monitorizar os módulos de limite de IoT à escala][lnk-howto].

## <a name="deployment"></a>Implementação

Uma implementação atribui IoT Edge imagens do módulo para ser executado como instâncias num conjunto de dispositivos de IoT limite de destino. Funciona ao configurar um manifesto de implementação de limite de IoT para incluir uma lista de módulos com os parâmetros de inicialização correspondente. Uma implementação pode ser atribuída a um único dispositivo (normalmente, com base no Id de dispositivo) ou a um grupo de dispositivos (com base em tags). Depois de um dispositivo de limite de IoT recebe um manifesto de implementação, transfere e instala as imagens de contentor do módulo a partir do repositórios do respetivo contentor e configura-as em conformidade. Depois de uma implementação é criada, um operador pode monitorizar o estado de implementação para ver se os dispositivos direcionados estão corretamente configurados.   

Dispositivos têm de ser aprovisionado como dispositivos de limite de IoT para estar configurado com uma implementação. Os seguintes pré-requisitos e não estão incluídas na implementação:
* O sistema operativo base
* Docker 
* Aprovisionamento do tempo de execução IoT Edge 

### <a name="deployment-manifest"></a>Manifesto de implementação

O manifesto de implementação é um documento JSON que descreve os módulos a ser configurado nos dispositivos de IoT Edge visados. Contém os metadados de configuração para todos os módulos, incluindo os módulos de sistema necessário (especificamente o agente de limite de IoT e hub IoT Edge).  

Os metadados de configuração para cada módulo incluem: 
* Versão 
* Tipo 
* Estado (por exemplo, em execução ou parado) 
* Política de voltar a iniciar 
* Repositório de imagens e contentor 
* Rotas para dados de entrada e saída 

### <a name="target-condition"></a>Condição de destino

Condições de filtragem de especificar se um dispositivo de limite de IoT deve estar sob o âmbito de uma implementação. Condições de filtragem são baseadas em etiquetas do dispositivo duplo. 

### <a name="priority"></a>Prioridade

Uma prioridade define se uma implementação deve ser aplicada ao dispositivo relativo outras implementações. Uma prioridade de implementação é um número inteiro positivo, com um número maior que indica a prioridade mais alta. Se um dispositivo de limite de IoT é direcionado por mais de uma implementação, aplica-se a implementação com a prioridade mais elevada.  Implementações com prioridades inferiores não são aplicadas, nem são intercaladas.  Se um dispositivo é direcionado com dois ou mais implementações com igual prioridade, aplica-se a implementação mais recentemente criada (determinada pelo carimbo de criação).

### <a name="labels"></a>Etiquetas 

As etiquetas são pares de chave/valor de cadeia que pode utilizar para filtrar e grupo de implementações. Uma implementação pode ter múltiplas etiquetas. As etiquetas são opcionais e fazer sem afetar a configuração de dispositivos de IoT Edge real. 

### <a name="deployment-status"></a>Estado da implementação

Uma implementação pode ser monitorizada para determinar se aplicada com êxito para qualquer dispositivo de limite de IoT visado.  Um dispositivo de limite de destino será apresentado em uma ou mais das seguintes categorias de estado: 
* **Destino** mostra o limite de IoT dispositivos que correspondem a condição de filtragem de implementação.
* **Real** mostra o limite de IoT visado dispositivos que não são visados por outra implementação de maior prioridade.
* **Bom estado de funcionamento** mostra o limite de IoT dispositivos que relataram novamente para o serviço que os módulos que foram implementados com êxito. 
* **Mau estado de funcionamento** mostra o limite de IoT dispositivos relataram novamente para o serviço que um ou módulos não foram implementados com êxito. Para continuar a investigar o erro, ligar remotamente à qual os dispositivos e ver os ficheiros de registo.
* **Desconhecido** mostra o limite de IoT dispositivos que não comunicaram qualquer Estado relativas esta implementação. Para continuar a investigar, ver ficheiros de registo e as informações de serviço.

## <a name="phased-rollout"></a>Implementação faseada 

Uma implementação faseada é um processo geral num operador implementa as alterações a um conjunto broadening de dispositivos de limite de IoT. O objetivo é efetuar alterações gradualmente para reduzir o risco de efetuar o dimensionamento wide interrompendo as alterações.  

Uma implementação faseada é executada nas seguintes fases e passos: 
1. Estabelecer um ambiente de teste de dispositivos de IoT Edge ao aprovisionamento-los e definir uma etiqueta de duplo de dispositivo como `tag.environment='test'`. O ambiente de teste deverá ser espelhada o ambiente de produção, eventualmente, serão direcionadas para a implementação. 
1. Crie uma implementação, incluindo o pretendido módulos e configurações. A condição de destino deve ter como destino o teste de ambiente de dispositivo de limite de IoT.   
1. Valide a configuração do módulo nova no ambiente de teste.
1. Atualize a implementação para incluir um subconjunto de dispositivos de limite de IoT de produção ao adicionar uma nova etiqueta para a condição de filtragem. Além disso, certifique-se de que a prioridade para a implementação é superior ao outras implementações atualmente visadas para esses dispositivos 
1. Certifique-se de que a implementação concluída com êxito nos dispositivos de IoT visados verificando o estado de implementação.
1. Atualize a implementação para todos os restantes dispositivos de IoT limite de produção de destino.

## <a name="rollback"></a>Reversão

Implementações podem ser revertidas em caso de erros ou configurações incorretas.  Porque uma implementação define a configuração do módulo absoluto de um dispositivo de limite de IoT, uma implementação adicional deve ser também visada ao mesmo dispositivo, uma prioridade mais baixa, mesmo se o objetivo consiste em remover todos os módulos.  

Execute reverte na seguinte sequência: 
1. Certifique-se de que uma segunda implementação visa também o mesmo conjunto de dispositivos. Se o objetivo de reversão remover todos os módulos, a segunda implementação não deve incluir quaisquer módulos. 
1. Modificar ou remover a expressão de condição de destino da implementação que pretende implementar, para que os dispositivos já não satisfazem a condição de filtragem.
1. Certifique-se de que a reversão concluída com êxito ao visualizar o estado de implementação.
   * A implementação de back revertida deve deixar de Mostrar estado para os dispositivos que foram revertidas.
   * A segunda implementação agora deve incluir o estado de implementação para os dispositivos que foram revertidas.


## <a name="next-steps"></a>Passos seguintes

* Siga os passos para criar, atualizar ou eliminar uma implementação no [implementar e monitorizar os módulos de limite de IoT à escala][lnk-howto].
* Saiba mais sobre os outros conceitos de IoT Edge como o [IoT Edge runtime] [ lnk-runtime] e [módulos de limite de IoT][lnk-modules].

<!-- Links -->
[lnk-lifecycle]: ../iot-hub/iot-hub-device-management-overview.md
[lnk-runtime]: iot-edge-runtime.md
[lnk-modules]: iot-edge-modules.md
[lnk-howto]: how-to-deploy-monitor.md
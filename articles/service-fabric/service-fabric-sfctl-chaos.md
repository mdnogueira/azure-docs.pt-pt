---
title: Azure Service Fabric CLI - sfctl choas | Microsoft Docs
description: "Descreve os comandos de chaos sfctl CLI de recursos de infraestrutura de serviço."
services: service-fabric
documentationcenter: na
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: cli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 09/22/2017
ms.author: ryanwi
ms.openlocfilehash: 336e74d8f69cb04e6bd0e85fc68ba38b218fabae
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="sfctl-chaos"></a>sfctl chaos
Iniciar, parar e relatórios sobre o serviço de teste chaos.

## <a name="commands"></a>Comandos

|Comando|Descrição|
| --- | --- |
|    relatório| Obtém o segmento seguinte do relatório Chaos baseado no token de continuação transmitido na ou o transmitido no-intervalo de tempo.|
|    start | Se Chaos não está já em execução no cluster, começa a ser executada Chaos com especificado nos parâmetros de Chaos.|
|    Parar  | Interrompe Chaos do cluster se já está em execução, caso contrário faz nada.|


## <a name="sfctl-chaos-report"></a>relatório de chaos sfctl
Obtém o segmento seguinte do relatório Chaos baseado no token de continuação transmitido na ou o transmitido no-intervalo de tempo.

Pode especificar o ContinuationToken para obter o segmento seguinte do relatório Chaos ou pode especificar o intervalo de tempo através de StartTimeUtc e EndTimeUtc, mas não é possível especificar o ContinuationToken e o intervalo de tempo na mesma chamada. Quando existe mais do que 100 eventos de Chaos, é devolvido o relatório de Chaos em segmentos em que um segmento contém mais do que 100 eventos de Chaos. 

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| -token de continuação| O parâmetro de token de continuação é utilizado para obter o seguinte conjunto de resultados. Um token de continuação com um valor não vazio está incluído na resposta da API de quando os resultados do sistema não se enquadram numa única resposta. Quando este valor é transmitido para a próxima chamada de API, a API devolve o seguinte conjunto de resultados. Se existirem resultados adicionais, em seguida, o token de continuação não contém um valor. O valor deste parâmetro não deve ser o URL, codificado.|
| – hora de fim-utc   | A contagem de ticks que representa a hora de fim do intervalo de tempo para o qual um relatório de Chaos é para ser gerado. Consulte [DateTime.Ticks propriedade](https://msdn.microsoft.com/en-us/library/system.datetime.ticks%28v=vs.110%29) para obter detalhes sobre marcas de escala.|
| -utc de hora de início | A contagem de ticks que representa a hora de início do intervalo de tempo para o qual um relatório de Chaos é para ser gerado. Consulte [DateTime.Ticks propriedade](https://msdn.microsoft.com/en-us/library/system.datetime.ticks%28v=vs.110%29) para obter detalhes sobre marcas de escala.|
| tempo limite – -t     | Tempo limite do servidor em segundos.  Predefinição: 60.|

### <a name="global-arguments"></a>Argumentos global

|Argumento|Descrição|
| --- | --- |
| -debug          | Aumente a verbosidade do registo para mostrar que todos os registos de depuração.|
| -ajudar -h        | Mostra esta mensagem de ajuda e saída.|
| --o de saída      | Formato de saída.  Valores permitidos: json, jsonc, tabela, tsv.  Predefinição: json.|
| – consulta          | Cadeia de consulta JMESPath. Consulte http://jmespath.org/ para obter mais informações e exemplos.|
| -verbose        | Aumente a verbosidade do registo. Utilize - a depuração para os registos de depuração completas.|

## <a name="sfctl-chaos-start"></a>início de chaos sfctl
Se Chaos não está já em execução no cluster, começa a ser executada Chaos com especificado nos parâmetros de Chaos.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| aplicações--tipo-estado de funcionamento-política-mapa  | JSON codificado lista com aplicações mau estado de funcionamento da percentagem de máximo para tipos de aplicação específica. Cada entrada especifica como uma chave de nome de tipo de aplicação e como um valor de número inteiro que representa a percentagem de MaxPercentUnhealthyApplications utilizada para avaliar as aplicações do tipo de aplicação especificado.|
| -disable-move-réplica-lento | Desativa o principal de mover e mover falhas secundárias.|
| -estabilização de cluster máx.| A quantidade máxima de tempo de espera para todas as entidades para se tornam estáveis e em bom estado de cluster.  Predefinição: 60.|
| -falhas de em simultâneo de máx.    | O número máximo de falhas em simultâneo induzida por iteração.           Predefinição: 1.|
| – máximo-por cento-mau estado de funcionamento-aplicações  | Quando avaliar o estado de funcionamento do cluster durante a Chaos, o máximo permitido percentagem das aplicações mau estado de funcionamento antes de o comunicar um erro.|
| – máximo-por cento-mau estado de funcionamento-nós | Quando avaliar o estado de funcionamento do cluster durante a Chaos, o máximo permitido percentagem de nós mau estado de funcionamento antes de o comunicar um erro.|
| -tempo para execução              | Total de tempo (em segundos) para o qual Chaos será executado antes de parar automaticamente. O valor máximo permitido é 4.294.967.295 (System.UInt32.MaxValue).  Predefinição: 4294967295.|
| tempo limite – -t               | Tempo limite do servidor em segundos.  Predefinição: 60.|
| -espera tempo entre falhas | De tempo de espera (em segundos) entre falhas consecutivas dentro de uma único iteração.  Predefinição: 20.|
| -espera tempo entre iterações| Tempo-separação (em segundos) entre duas iterações consecutivas de Chaos.  Predefinição: 30.|
| -aviso como erro         | Quando avaliar o estado de funcionamento do cluster durante a Chaos, processe avisos com a mesma gravidade como erros.|

### <a name="global-arguments"></a>Argumentos global

|Argumento|Descrição|
| --- | --- |
| -debug                    | Aumente a verbosidade do registo para mostrar que todos os registos de depuração.|
| -ajudar -h                  | Mostra esta mensagem de ajuda e saída.|
| --o de saída                | Formato de saída.  Valores permitidos: json, jsonc, tabela, tsv.           Predefinição: json.|
| – consulta                    | Cadeia de consulta JMESPath. Consulte http://jmespath.org/ para obter mais informações e exemplos.|
| -verbose                  | Aumente a verbosidade do registo. Utilize - a depuração para os registos de depuração completas.|

## <a name="sfctl-chaos-stop"></a>parar de chaos sfctl
Interrompe Chaos do cluster se já está em execução, caso contrário faz nada.

Chaos interrompe o processo de agendamento mais falhas; No entanto, falhas em trânsito não são afetadas.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| tempo limite – -t| Tempo limite do servidor em segundos.  Predefinição: 60.|

### <a name="global-arguments"></a>Argumentos global

|Argumento|Descrição|
| --- | --- |
| -debug  | Aumente a verbosidade do registo para mostrar que todos os registos de depuração.|
| -ajudar -h| Mostra esta mensagem de ajuda e saída.|
| --o de saída | Formato de saída.  Valores permitidos: json, jsonc, tabela, tsv.  Predefinição: json.|
| – consulta  | Cadeia de consulta JMESPath. Consulte http://jmespath.org/ para obter mais informações e exemplos.|
| -verbose| Aumente a verbosidade do registo. Utilize - a depuração para os registos de depuração completas.|

## <a name="next-steps"></a>Passos seguintes
- [A configuração](service-fabric-cli.md) os recursos de infraestrutura do serviço CLI.
- Saiba como utilizar a CLI de recursos de infraestrutura de serviço utilizando o [scripts de exemplo](/azure/service-fabric/scripts/sfctl-upgrade-application).
---
title: "Azure Service Fabric CLI sfctl partição | Microsoft Docs"
description: "Descreve os comandos de partição de sfctl CLI de recursos de infraestrutura de serviço."
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
ms.openlocfilehash: 99756378f2106707b4f6d634a1183d5c32243ee2
ms.sourcegitcommit: ccb84f6b1d445d88b9870041c84cebd64fbdbc72
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/14/2017
---
# <a name="sfctl-partition"></a>partição de sfctl
Consultar e gerir partições para qualquer serviço.

## <a name="commands"></a>Comandos

|Comando|Descrição|
| --- | --- |
|    perda de dados      | Esta API induces perda de dados para a partição especificada.|
|    Estado de perda de dados  | Obtém o progresso de uma operação de perda de dados de partição iniciado utilizando a API de StartDataLoss.|
|    Estado de funcionamento         | Obtém o estado de funcionamento a partição de serviço recursos de infraestrutura especificada.|
|    informações           | Obtém as informações sobre uma partição de Service Fabric.|
|    lista           | Obtém a lista de partições de um serviço do Service Fabric.|
|    carregar           | Obtém a carga da partição de serviço recursos de infraestrutura especificada.|
|    reposição de carga     | Repõe a atual carga de uma partição de Service Fabric.|
|    perda de quórum    | Induces perda de quórum para uma partição de serviço com estado indicado.|
|    Estado de perda de quórum| Obtém o progresso de uma operação de perda de quórum numa partição iniciada utilizando a API de StartQuorumLoss.|
|    Recuperar        | Indica, para o cluster do Service Fabric, o que deve tentar recuperar uma partição específica, que está atualmente bloqueada na perda de quórum.|
|    recuperar-all    | Indica, para o cluster do Service Fabric, o que deve tentar recuperar quaisquer serviços (incluindo os serviços do sistema), que atualmente estão bloqueados na perda de quórum.|
|    Estado de funcionamento de relatório  | Envia um relatório de estado de funcionamento na partição de Service Fabric.|
|    Reiniciar        | Esta API reinicia algumas ou todas as réplicas ou instâncias da partição especificada.|
|    Estado de reinício | Obtém o progresso de uma operação de PartitionRestart iniciado utilizando StartPartitionRestart.|
|    nome-SVC       | Obtém o nome do serviço Service Fabric para uma partição.|


## <a name="sfctl-partition-health"></a>o estado de funcionamento do sfctl partição
Obtém o estado de funcionamento a partição de serviço recursos de infraestrutura especificada.

Obtém as informações de estado de funcionamento da partição especificada. Utilize EventsHealthStateFilter para filtrar a recolha de eventos de estado de funcionamento comunicadas falhas no serviço, com base no estado de funcionamento.
Utilize ReplicasHealthStateFilter para filtrar a coleção de objetos de ReplicaHealthState na partição. Se especificar uma partição que não existe no arquivo de estado de funcionamento, este cmdlet devolve um erro. .

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| – id de partição [necessário]| A identidade da partição.|
| – Estado-filtro de eventos-estado de funcionamento  | Permite a filtragem na coleção de objetos de HealthEvent devolvido com base no estado de funcionamento. Os valores possíveis para este parâmetro incluem o valor de número inteiro de um dos seguintes Estados de funcionamento.                Apenas os eventos que correspondem ao filtro são devolvidos. Todos os eventos são utilizados para avaliar o estado de funcionamento agregada. Se não for especificado, são devolvidas todas as entradas. Os valores de estado são enumeração baseada no sinalizador de, pelo que o valor pode ser uma combinação destes valores obtidos através de operador de 'Ou' bit a bit. Por exemplo, se o valor fornecido é 6, em seguida, todos os eventos com o valor HealthState OK (2) e de aviso (4) são devolvidos. -Predefinição - valor predefinido. Corresponde a qualquer HealthState. O valor é zero. -Nenhuma - filtro que não corresponde a qualquer valor HealthState. Utilizado para não devolver resultados numa coleção especificada dos Estados. O valor é 1. -Ok - filtre que corresponde de entrada com o valor de HealthState Ok. O valor é 2. -Aviso - filtro que corresponde à entrada com HealthState valor aviso. O valor é 4. -Erro - filtro que corresponda à entrada com o valor de HealthState erro. O valor é 8.                -Todos os - filtro que corresponda à entrada com qualquer valor HealthState.                O valor é a 65535.|
|– estatísticas de estado de funcionamento de exclusão   | Indica se as estatísticas de estado de funcionamento devem ser devolvidas como parte do resultado da consulta. FALSO por predefinição. As estatísticas mostram o número de elementos subordinados entidades no estado de funcionamento Ok, aviso e erro.|
| -réplicas-estado de funcionamento do Estado de filtro| Permite a filtragem da coleção de objetos de ReplicaHealthState na partição. Os membros ou operações bit a bit em membros de HealthStateFilter pode obter o valor. Apenas as réplicas que correspondem ao filtro são devolvidas. Todas as réplicas são utilizadas para avaliar o estado de funcionamento agregada. Se não for especificado, são devolvidas todas as entradas. Os valores de estado são enumeração baseada no sinalizador de, pelo que o valor pode ser uma combinação destes valores obtidos através de operador de 'Ou' bit a bit. Por exemplo, se o valor fornecido é 6, em seguida, todos os eventos com o valor HealthState OK (2) e de aviso (4) são devolvidos. Os valores possíveis para este parâmetro incluem o valor de número inteiro de um dos seguintes Estados de funcionamento. -Predefinição - valor predefinido. Corresponde a qualquer HealthState. O valor é zero. -Nenhuma - filtro que não corresponde a qualquer valor HealthState. Utilizado para não devolver resultados numa coleção especificada dos Estados. O valor é 1. -Ok - filtre que corresponde de entrada com o valor de HealthState Ok. O valor é 2. -Aviso - filtro que corresponde à entrada com HealthState valor aviso. O valor é 4. -Erro - filtro que corresponda à entrada com o valor de HealthState erro. O valor é 8. -Todos os - filtro que corresponda à entrada com qualquer valor HealthState. O valor é a 65535.|
| tempo limite – -t               | Tempo limite do servidor em segundos.  Predefinição: 60.|

### <a name="global-arguments"></a>Argumentos global

|Argumento|Descrição|
| --- | --- |
| -debug                    | Aumente a verbosidade do registo para mostrar que todos os registos de depuração.|
| -ajudar -h                  | Mostra esta mensagem de ajuda e saída.|
| --o de saída                | Formato de saída.  Valores permitidos: json, jsonc, tabela, tsv.                Predefinição: json.|
| – consulta                    | Cadeia de consulta JMESPath. Para obter mais informações e exemplos, consulte http://jmespath.org/. |
| -verbose                  | Aumente a verbosidade do registo. Utilize - a depuração para os registos de depuração completas.|

## <a name="sfctl-partition-info"></a>informações de partição sfctl
Obtém as informações sobre uma partição de Service Fabric.

O ponto final de partições devolve informações sobre a partição especificada. A resposta inclui o ID de partição, criação de partições informações de esquema, suportadas por partição, o estado, o estado de funcionamento e outros detalhes sobre a partição de chaves.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| – id de partição [necessário]| A identidade da partição.|
| tempo limite – -t          | Tempo limite do servidor em segundos.  Predefinição: 60.|

### <a name="global-arguments"></a>Argumentos global

|Argumento|Descrição|
| --- | --- |
| -debug               | Aumente a verbosidade do registo para mostrar que todos os registos de depuração.|
| -ajudar -h             | Mostra esta mensagem de ajuda e saída.|
| --o de saída           | Formato de saída.  Valores permitidos: json, jsonc, tabela, tsv.  Predefinição: json.|
| – consulta               | Cadeia de consulta JMESPath. Para obter mais informações e exemplos, consulte http://jmespath.org/.|
| -verbose             | Aumente a verbosidade do registo. Utilize - a depuração para os registos de depuração completas.|

## <a name="sfctl-partition-list"></a>lista de partição sfctl
Obtém a lista de partições de um serviço do Service Fabric.

Obtém a lista de partições de um serviço do Service Fabric. O s o ID de partição, criação de partições informações de esquema, chaves suportadas por partição, o estado, o estado de funcionamento e outros detalhes sobre a partição.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| – id de serviço [necessário]| A identidade do serviço. Isto é, geralmente, o nome completo do serviço sem o ' recursos de infraestrutura:' esquema de URI. A partir da versão 6.0, nomes hierárquicos são delimitados por com o "~" carateres. Por exemplo, se o nome do serviço é "fabric://myapp/app1/svc1", a identidade de serviço será "myapp ~ app1 ~ svc1" 6.0 + e "myapp/app1/svc1" em versões anteriores.|
| -token de continuação| O parâmetro de token de continuação é utilizado para obter o seguinte conjunto de resultados.         Um token de continuação com um valor não vazio está incluído na resposta da API de quando os resultados do sistema não se enquadram numa única resposta. Quando este valor é transmitido para a próxima chamada de API, a API devolve o seguinte conjunto de resultados. Se não existirem resultados adicionais, em seguida, o token de continuação não contém um valor. O valor deste parâmetro não deve ser o URL, codificado.|
| tempo limite – -t        | Tempo limite do servidor em segundos.  Predefinição: 60.|

### <a name="global-arguments"></a>Argumentos global

|Argumento|Descrição|
| --- | --- |
| -debug             | Aumente a verbosidade do registo para mostrar que todos os registos de depuração.|
| -ajudar -h           | Mostra esta mensagem de ajuda e saída.|
| --o de saída         | Formato de saída.  Valores permitidos: json, jsonc, tabela, tsv.  Predefinição: json.|
| – consulta             | Cadeia de consulta JMESPath. Para obter mais informações e exemplos, consulte http://jmespath.org/.|
| -verbose           | Aumente a verbosidade do registo. Utilize - a depuração para os registos de depuração completas.|

## <a name="sfctl-partition-load"></a>sfctl carga da partição
Obtém a carga da partição de serviço recursos de infraestrutura especificada.

Devolve informações sobre a partição especificada. A resposta inclui uma lista de informações de carga. Cada informações incluem o nome da métrica de carga, o valor e o último tempo comunicado em UTC.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| – id de partição [necessário]| A identidade da partição.|
| tempo limite – -t          | Tempo limite do servidor em segundos.  Predefinição: 60.|

### <a name="global-arguments"></a>Argumentos global

|Argumento|Descrição|
| --- | --- |
| -debug               | Aumente a verbosidade do registo para mostrar que todos os registos de depuração.|
| -ajudar -h             | Mostra esta mensagem de ajuda e saída.|
| --o de saída           | Formato de saída.  Valores permitidos: json, jsonc, tabela, tsv.  Predefinição: json.|
| – consulta               | Cadeia de consulta JMESPath. Para obter mais informações e exemplos, consulte http://jmespath.org/.|
| -verbose             | Aumente a verbosidade do registo. Utilize - a depuração para os registos de depuração completas.|

## <a name="sfctl-partition-recover"></a>recuperar sfctl partição
Indica, para o cluster do Service Fabric, o que deve tentar recuperar uma partição específica que está atualmente bloqueada na perda de quórum.

Indica, para o cluster do Service Fabric, o que deve tentar recuperar uma partição específica que está atualmente bloqueada na perda de quórum. Esta operação só deve ser efetuada se sabe-se que não não possível recuperar as réplicas que estão em baixo. Utilização incorreta desta API pode causar a potencial perda de dados.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| – id de partição [necessário]| A identidade da partição.|
| tempo limite – -t          | Tempo limite do servidor em segundos.  Predefinição: 60.|

### <a name="global-arguments"></a>Argumentos global

|Argumento|Descrição|
| --- | --- |
| -debug               | Aumente a verbosidade do registo para mostrar que todos os registos de depuração.|
| -ajudar -h             | Mostra esta mensagem de ajuda e saída.|
| --o de saída           | Formato de saída.  Valores permitidos: json, jsonc, tabela, tsv.  Predefinição: json.|
| – consulta               | Cadeia de consulta JMESPath. Para obter mais informações e exemplos, consulte http://jmespath.org/.|
| -verbose             | Aumente a verbosidade do registo. Utilize - a depuração para os registos de depuração completas.|

## <a name="sfctl-partition-restart"></a>reinício de partição sfctl
Esta API reinicia algumas ou todas as réplicas ou instâncias da partição especificada.

Esta API é útil para testar a ativação pós-falha. Se utilizados para visar uma partição de serviço sem monitorização de estado, RestartPartitionMode tem de ser AllReplicasOrInstances. Chame a API de GetPartitionRestartProgress utilizando o mesmo OperationId para obter o progresso. .

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| – id de operação [necessário]| Um GUID que identifica uma chamada desta API.  Isto é transmitido para a API de GetProgress correspondente.|
| – id de partição [necessário]| A identidade da partição.|
| -modo de partição de reinício [necessário]| -Inválido - reservado.  Não são transmitidas para a API. -AllReplicasOrInstances - todas as réplicas ou instâncias na partição são reiniciadas em simultâneo. -OnlyActiveSecondaries - apenas secundário réplicas são reiniciadas. .|
| – id de serviço [necessário]| A identidade do serviço. Isto é, geralmente, o nome completo do serviço sem o ' recursos de infraestrutura:' esquema de URI. A partir da versão 6.0, nomes hierárquicos são delimitados por com o "~" carateres. Por exemplo, se o nome do serviço é "fabric://myapp/app1/svc1", a identidade de serviço será "myapp ~ app1 ~ svc1" 6.0 + e "myapp/app1/svc1" no v anterior ersions.|
| tempo limite – -t                    | Tempo limite do servidor em segundos.  Predefinição: 60.|

### <a name="global-arguments"></a>Argumentos global

|Argumento|Descrição|
| --- | --- |
| -debug                         | Aumente a verbosidade do registo para mostrar que todos os registos de depuração.|
| -ajudar -h                       | Mostra esta mensagem de ajuda e saída.|
| --o de saída                     | Formato de saída.  Valores permitidos: json, jsonc, tabela, tsv.                     Predefinição: json.|
| – consulta                         | Cadeia de consulta JMESPath. Para obter mais informações e exemplos, consulte http://jmespath.org/.|
| -verbose                       | Aumente a verbosidade do registo. Utilize - a depuração para os registos de depuração completas.|

## <a name="next-steps"></a>Passos seguintes
- [A configuração](service-fabric-cli.md) os recursos de infraestrutura do serviço CLI.
- Saiba como utilizar a CLI de recursos de infraestrutura de serviço utilizando o [scripts de exemplo](/azure/service-fabric/scripts/sfctl-upgrade-application).

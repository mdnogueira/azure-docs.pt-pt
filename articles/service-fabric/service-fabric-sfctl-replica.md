---
title: "Réplica do Azure Service Fabric CLI - sfctl | Microsoft Docs"
description: "Descreve os comandos de réplica de sfctl CLI de recursos de infraestrutura de serviço."
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
ms.openlocfilehash: bd16dd889cbe0f05d7e60f444c6c5fa2e65f64a4
ms.sourcegitcommit: 5bced5b36f6172a3c20dbfdf311b1ad38de6176a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/27/2017
---
# <a name="sfctl-replica"></a>réplica sfctl
Gerir as réplicas que pertencem a partições de serviço.

## <a name="commands"></a>Comandos

|Comando|Descrição|
| --- | --- |
|    Implementado  | Obtém os detalhes da réplica implementada num nó de Service Fabric.|
|    lista implementado| Obtém a lista de réplicas implementado num nó de Service Fabric.|
|    estado de funcionamento    | Obtém o estado de funcionamento de uma réplica de monitorização de estado do serviço do Service Fabric ou instância de serviço sem estado.|
|    informações      | Obtém as informações sobre uma réplica de uma partição de Service Fabric.|
|    lista      | Obtém as informações acerca de réplicas de uma partição de serviço do Service Fabric.|
|    Remover    | Remove uma réplica de serviço em execução num nó.|
|    Estado de funcionamento de relatório| Envia um relatório de estado de funcionamento da réplica de Service Fabric.|
|    Reiniciar   | Reinicia uma réplica de serviço de um serviço persistente em execução num nó.|


## <a name="sfctl-replica-deployed"></a>réplica sfctl implementada
Obtém os detalhes da réplica implementada num nó de Service Fabric.

Obtém os detalhes da réplica implementada num nó de Service Fabric. As informações incluem o tipo de serviço, o nome do serviço, operação de serviço atual, iniciar a operação de serviço atual data hora, ID de partição, ID de réplica/instância, carga comunicada e outras informações.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --nome do nó [necessário]| O nome do nó.|
| – id de partição [necessário]| A identidade da partição.|
| – id de réplica [necessário]| O identificador da réplica.|
| tempo limite – -t          | Tempo limite do servidor em segundos.  Predefinição: 60.|

### <a name="global-arguments"></a>Argumentos global

|Argumento|Descrição|
| --- | --- |
| -debug               | Aumente a verbosidade do registo para mostrar que todos os registos de depuração.|
| -ajudar -h             | Mostra esta mensagem de ajuda e saída.|
| --o de saída           | Formato de saída.  Valores permitidos: json, jsonc, tabela, tsv.  Predefinição: json.|
| – consulta               | Cadeia de consulta JMESPath. Para obter mais informações e exemplos, consulte http://jmespath.org/.|
| -verbose             | Aumente a verbosidade do registo. Utilize - a depuração para os registos de depuração completas.|

## <a name="sfctl-replica-health"></a>o estado de funcionamento do sfctl réplica
Obtém o estado de funcionamento de uma réplica de monitorização de estado do serviço do Service Fabric ou instância de serviço sem estado.

Obtém o estado de funcionamento de uma réplica de Service Fabric. Utilize EventsHealthStateFilter para filtrar a recolha de eventos de estado de funcionamento comunicadas na réplica com base no estado de funcionamento. .

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| – id de partição [necessário]| A identidade da partição.|
| – id de réplica [necessário]| O identificador da réplica.|
| – Estado-filtro de eventos-estado de funcionamento| Permite a filtragem na coleção de objetos de HealthEvent devolvido com base no estado de funcionamento. Os valores possíveis para este parâmetro incluem o valor de número inteiro de um dos seguintes Estados de funcionamento. Apenas os eventos que correspondem ao filtro são devolvidos. Todos os eventos são utilizados para avaliar o estado de funcionamento agregada. Se não for especificado, são devolvidas todas as entradas. Os valores de estado são enumeração baseada no sinalizador de, pelo que o valor pode ser uma combinação destas valor obtido utilizando o operador de 'Ou' bit a bit. Por exemplo, se o valor fornecido é 6, em seguida, todos os eventos com o valor HealthState OK (2) e de aviso (4) são devolvidos. -Predefinição - valor predefinido. Corresponde a qualquer HealthState. O valor é zero. -Nenhuma - filtro que não corresponde a qualquer valor HealthState. Utilizado para não devolver resultados numa coleção especificada dos Estados. O valor é 1. -Ok - filtre que corresponde de entrada com o valor de HealthState Ok. O valor é 2. -Aviso - filtro que corresponde à entrada com HealthState valor aviso. O valor é 4. -Erro - filtro que corresponda à entrada com o valor de HealthState erro. O valor é 8. -Todos os - filtro que corresponda à entrada com qualquer valor HealthState. O valor é a 65535.|
| tempo limite – -t             | Tempo limite do servidor em segundos.  Predefinição: 60.|

### <a name="global-arguments"></a>Argumentos global

|Argumento|Descrição|
| --- | --- |
| -debug                  | Aumente a verbosidade do registo para mostrar que todos os registos de depuração.|
| -ajudar -h                | Mostra esta mensagem de ajuda e saída.|
| --o de saída              | Formato de saída.  Valores permitidos: json, jsonc, tabela, tsv.  Predefinição: json.|
| – consulta                  | Cadeia de consulta JMESPath. Consulte http://jmespath.org/ para obter mais informações e exemplos.|
| -verbose                | Aumente a verbosidade do registo. Utilize - a depuração para os registos de depuração completas.|

## <a name="sfctl-replica-info"></a>informações de réplica sfctl
Obtém as informações sobre uma réplica de uma partição de Service Fabric.

Os respons incluem o ID, função, o estado, estado de funcionamento, o nome do nó, tempo de atividade e outros detalhes sobre a réplica.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| – id de partição [necessário]| A identidade da partição.|
| – id de réplica [necessário]| O identificador da réplica.|
| -token de continuação  | O parâmetro de token de continuação é utilizado para obter o seguinte conjunto de resultados. Um token de continuação com um valor não vazio está incluído na resposta da API de quando os resultados do sistema não se enquadram numa única resposta. Quando este valor é transmitido para a próxima chamada de API, a API devolve o seguinte conjunto de resultados. Se existirem resultados adicionais, em seguida, o token de continuação não contém um valor. O valor deste parâmetro não deve ser o URL, codificado.|
| tempo limite – -t          | Tempo limite do servidor em segundos.  Predefinição: 60.|

### <a name="global-arguments"></a>Argumentos global

|Argumento|Descrição|
| --- | --- |
| -debug               | Aumente a verbosidade do registo para mostrar que todos os registos de depuração.|
| -ajudar -h             | Mostra esta mensagem de ajuda e saída.|
| --o de saída           | Formato de saída.  Valores permitidos: json, jsonc, tabela, tsv.  Predefinição: json.|
| – consulta               | Cadeia de consulta JMESPath. Consulte http://jmespath.org/ para obter mais informações e exemplos.|
| -verbose             | Aumente a verbosidade do registo. Utilize - a depuração para os registos de depuração completas.|

## <a name="sfctl-replica-list"></a>lista de réplicas sfctl
Obtém as informações acerca de réplicas de uma partição de serviço do Service Fabric.

O ponto final GetReplicas devolve informações sobre as réplicas de partição especificada.
Os respons incluem o ID, função, o estado, estado de funcionamento, o nome do nó, tempo de atividade e outros detalhes sobre a réplica.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| – id de partição [necessário]| A identidade da partição.|
| -token de continuação  | O parâmetro de token de continuação é utilizado para obter o seguinte conjunto de resultados. Um token de continuação com um valor não vazio está incluído na resposta da API de quando os resultados do sistema não se enquadram numa única resposta. Quando este valor é transmitido para a próxima chamada de API, a API devolve o seguinte conjunto de resultados. Se existirem resultados adicionais, em seguida, o token de continuação não contém um valor. O valor deste parâmetro não deve ser o URL, codificado.|
| tempo limite – -t          | Tempo limite do servidor em segundos.  Predefinição: 60.|

### <a name="global-arguments"></a>Argumentos global

|Argumento|Descrição|
| --- | --- |
| -debug               | Aumente a verbosidade do registo para mostrar que todos os registos de depuração.|
| -ajudar -h             | Mostra esta mensagem de ajuda e saída.|
| --o de saída           | Formato de saída.  Valores permitidos: json, jsonc, tabela, tsv.  Predefinição: json.|
| – consulta               | Cadeia de consulta JMESPath. Consulte http://jmespath.org/ para obter mais informações e exemplos.|
| -verbose             | Aumente a verbosidade do registo. Utilize - a depuração para os registos de depuração completas.|

## <a name="sfctl-replica-remove"></a>remover da réplica de sfctl
Remove uma réplica de serviço em execução num nó.

Esta API simula uma falha de réplica do Service Fabric ao remover uma réplica de um cluster do Service Fabric. A remoção fecha a réplica, passa a réplica para a função None e, em seguida, remove todas as informações de estado da réplica do cluster. Esta API testa o caminho de remoção de estado da réplica e simula o caminho de permanente de falhas de relatório através de APIs do cliente. Aviso - daí serão efetuadas verificações de segurança efetuadas quando esta API é utilizada. Utilização incorreta desta API pode levar a perda de dados para os serviços com monitorização de estado. Além disso, o sinalizador de forceRemove afeta todos os outras réplicas alojadas no mesmo processo.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --nome do nó [necessário]| O nome do nó.|
| – id de partição [necessário]| A identidade da partição.|
| – id de réplica [necessário]| O identificador da réplica.|
| -force-remover        | Remova uma aplicação de Service Fabric ou o serviço forçadamente sem passar a sequência de encerramento correto. Este parâmetro pode ser utilizado para forçadamente eliminar uma aplicação ou serviço para o eliminar é exceder o tempo limite devido a problemas com o código do serviço que impede o correto fechar das réplicas.|
| tempo limite – -t          | Tempo limite do servidor em segundos.  Predefinição: 60.|

### <a name="global-arguments"></a>Argumentos global

|Argumento|Descrição|
| --- | --- |
| -debug               | Aumente a verbosidade do registo para mostrar que todos os registos de depuração.|
| -ajudar -h             | Mostra esta mensagem de ajuda e saída.|
| --o de saída           | Formato de saída.  Valores permitidos: json, jsonc, tabela, tsv.  Predefinição: json.|
| – consulta               | Cadeia de consulta JMESPath. Consulte http://jmespath.org/ para obter mais informações e exemplos.|
| -verbose             | Aumente a verbosidade do registo. Utilize - a depuração para os registos de depuração completas.|

## <a name="sfctl-replica-restart"></a>reinício de réplica sfctl
Reinicia uma réplica de serviço de um serviço persistente em execução num nó.

Reinicia uma réplica de serviço de um serviço persistente em execução num nó. Aviso - daí serão efetuadas verificações de segurança efetuadas quando esta API é utilizada. Utilização incorreta desta API pode levar a perda de disponibilidade para serviços com monitorização de estado.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --nome do nó [necessário]| O nome do nó.|
| – id de partição [necessário]| A identidade da partição.|
| – id de réplica [necessário]| O identificador da réplica.|
| tempo limite – -t          | Tempo limite do servidor em segundos.  Predefinição: 60.|

### <a name="global-arguments"></a>Argumentos global

|Argumento|Descrição|
| --- | --- |
| -debug               | Aumente a verbosidade do registo para mostrar que todos os registos de depuração.|
| -ajudar -h             | Mostra esta mensagem de ajuda e saída.|
| --o de saída           | Formato de saída.  Valores permitidos: json, jsonc, tabela, tsv.  Predefinição: json.|
| – consulta               | Cadeia de consulta JMESPath. Consulte http://jmespath.org/ para obter mais informações e exemplos.|
| -verbose             | Aumente a verbosidade do registo. Utilize - a depuração para os registos de depuração completas.|

## <a name="next-steps"></a>Passos seguintes
- [A configuração](service-fabric-cli.md) os recursos de infraestrutura do serviço CLI.
- Saiba como utilizar a CLI de recursos de infraestrutura de serviço utilizando o [scripts de exemplo](/azure/service-fabric/scripts/sfctl-upgrade-application).

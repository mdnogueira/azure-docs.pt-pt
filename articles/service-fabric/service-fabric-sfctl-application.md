---
title: "Aplicação Azure Service Fabric CLI - sfctl | Microsoft Docs"
description: "Descreve os comandos de aplicação de sfctl CLI de recursos de infraestrutura de serviço."
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
ms.openlocfilehash: 82d2024f567768e784d9d8697784d06b56bc08ed
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2017
---
# <a name="sfctl-application"></a>aplicação de sfctl
Criar, eliminar e gerir aplicações e tipos de aplicações.

## <a name="commands"></a>Comandos

|Comando|Descrição|
| --- | --- |
| criar       | Cria uma aplicação de Service Fabric utilizando a descrição especificada.|
| eliminar       | Elimina uma aplicação de Service Fabric existente.|
| Implementado     | Obtém as informações sobre uma aplicação implementada num nó de Service Fabric.|
| Estado de funcionamento implementado | Obtém as informações sobre o estado de funcionamento de uma aplicação implementada num nó de Service Fabric.|
| lista implementado| Obtém a lista de aplicações implementadas num nó de Service Fabric.|
| Estado de funcionamento       | Obtém o estado de funcionamento da aplicação de recursos de infraestrutura de serviço.|
| informações         | Obtém informações sobre uma aplicação de Service Fabric.|
| lista         | Obtém a lista de aplicações criado no cluster de Service Fabric que correspondem aos filtros especificados como parâmetro.|
| carregar | Obtém informações sobre uma aplicação de Service Fabric de carga. |
| Manifesto     | Obtém o manifesto que descreve um tipo de aplicação.|
| Aprovisionar    | Aprovisiona ou regista escrever uma aplicação de Service Fabric com o cluster.|
| Estado de funcionamento de relatório| Envia um relatório de estado de funcionamento da aplicação de Service Fabric.|
| tipo         | Obtém a lista de tipos de aplicações no cluster de Service Fabric correspondente exatamente o nome especificado.|
| lista de tipos    | Obtém a lista de tipos de aplicações no cluster de Service Fabric.|
| Não aprovisionamento  | Remove ou anula o registo de um tipo de aplicação de Service Fabric do cluster.|
| atualização      | Inicia a atualizar uma aplicação no cluster de Service Fabric.|
| retoma de atualização  | Retoma a atualizar uma aplicação no cluster de Service Fabric.|
| reversão de atualização| Inicia a reverter a atualização atualmente em curso de uma aplicação no cluster de Service Fabric.|
| Estado de atualização  | Obtém os detalhes para a atualização mais recente efetuada nesta aplicação.|
| Carregar       | Copie um pacote de aplicação de Service Fabric para o arquivo de imagens.|

## <a name="sfctl-application-create"></a>Criar aplicação sfctl
Cria uma aplicação de Service Fabric utilizando a descrição especificada.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --nome da aplicação [necessário]| O nome da aplicação, incluindo o ' recursos de infraestrutura:' esquema de URI.|
| --tipo de aplicação [necessário]| O nome do tipo de aplicação foi encontrado no manifesto da aplicação.|
| versão de – aplicação [necessária]| A versão do tipo de aplicação conforme definido no manifesto da aplicação.|
| – contagem máxima de nó     | O número máximo de nós que o Service Fabric reserva-se a capacidade para esta aplicação. Isto significa que os serviços desta aplicação são colocados em todos os nós.|
| -as métricas            | Um JSON codificado lista de descrições de métrica de capacidade de aplicação. Uma métrica é definida como um nome, associado um conjunto das capacidades para cada nó que a aplicação existe na.|
| número de nós – mín.     | O número mínimo de nós que o Service Fabric reserva-se a capacidade para esta aplicação. Isto significa que os serviços desta aplicação são colocados em todos os nós.|
| -parâmetros         | Uma lista JSON codificado de parâmetro de aplicação as substituições aplicadas ao criar a aplicação.|
| tempo limite – -t         | Tempo limite do servidor em segundos.  Predefinição: 60.|

### <a name="global-arguments"></a>Argumentos global

|Argumento|Descrição|
| --- | --- |
| -debug              | Aumente a verbosidade do registo para mostrar que todos os registos de depuração.|
| -ajudar -h            | Mostra esta mensagem de ajuda e saída.|
| --o de saída          | Formato de saída.  Valores permitidos: json, jsonc, tabela, tsv.  Predefinição: json.|
| – consulta              | Cadeia de consulta JMESPath. Consulte http://jmespath.org/ para obter mais informações e exemplos.|
| -verbose            | Aumente a verbosidade do registo. Utilize - a depuração para os registos de depuração completas.|

## <a name="sfctl-application-delete"></a>sfctl aplicações eliminar
Elimina uma aplicação de Service Fabric existente.

Elimina uma aplicação de Service Fabric existente. Tem de ser criada uma aplicação poderá ser eliminado. Eliminar uma aplicação elimina todos os serviços que fazem parte dessa aplicação. Por predefinição o Service Fabric tenta fechar as réplicas do serviço de forma correto e, em seguida, elimine o serviço. No entanto se o serviço está com dificuldades em Fechar corretamente a réplica, a operação de eliminação pode demorar muito tempo ou ficar bloqueado. Utilize o sinalizador de ForceRemove opcional para ignorar a sequência de fechada correto e forçadamente eliminar a aplicação e todos os respetivos serviços.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --id da aplicação [necessário]| A identidade da aplicação. Isto é, geralmente, o nome completo da aplicação sem a ' recursos de infraestrutura:' esquema de URI. A partir da versão 6.0, nomes hierárquicos são delimitados por com o "~" carateres. Por exemplo, se o nome da aplicação é "fabric://myapp/app1", a identidade da aplicação será "myapp ~ app1" 6.0 + e "myapp/app1" em versões anteriores.|
| -force-remover          | Remova uma aplicação de Service Fabric ou o serviço forçadamente sem passar a sequência de encerramento correto. Este parâmetro pode ser utilizado para forçadamente eliminar uma aplicação ou serviço para o eliminar é exceder o tempo limite devido a problemas com o código do serviço que impede o correto fechar das réplicas.|
| tempo limite – -t            | Tempo limite do servidor em segundos.  Predefinição: 60.|

### <a name="global-arguments"></a>Argumentos global

|Argumento|Descrição|
| --- | --- |
| -debug                 | Aumente a verbosidade do registo para mostrar que todos os registos de depuração.|
| -ajudar -h               | Mostra esta mensagem de ajuda e saída.|
| --o de saída             | Formato de saída.  Valores permitidos: json, jsonc, tabela, tsv.  Predefinição: json.|
| – consulta                 | Cadeia de consulta JMESPath. Consulte http://jmespath.org/ para obter mais informações e exemplos.|
| -verbose               | Aumente a verbosidade do registo. Utilize - a depuração para os registos de depuração completas.|

## <a name="sfctl-application-deployed"></a>sfctl aplicação implementada
Obtém as informações sobre uma aplicação implementada num nó de Service Fabric.
     
### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --id da aplicação [necessário]| A identidade da aplicação. Isto é, geralmente, o nome completo da aplicação sem a ' recursos de infraestrutura:' esquema de URI. A partir da versão 6.0, nomes hierárquicos são delimitados por com o "~" carateres. Por exemplo, se o nome da aplicação é "fabric://myapp/app1", a identidade da aplicação será "myapp ~ app1" 6.0 + e "myapp/app1" em versões anteriores.|
| --nome do nó [necessário]| O nome do nó.|
| tempo limite – -t            | Tempo limite do servidor em segundos.  Predefinição: 60.|

### <a name="global-arguments"></a>Argumentos global

|Argumento|Descrição|
| --- | --- |
| -debug                 | Aumente a verbosidade do registo para mostrar que todos os registos de depuração.|
| -ajudar -h               | Mostra esta mensagem de ajuda e saída.|
| --o de saída             | Formato de saída.  Valores permitidos: json, jsonc, tabela, tsv.  Predefinição: json.|
| – consulta                 | Cadeia de consulta JMESPath. Consulte http://jmespath.org/ para obter mais informações e exemplos.|
| -verbose               | Aumente a verbosidade do registo. Utilize - a depuração para os registos de depuração completas.|

## <a name="sfctl-application-health"></a>o estado de funcionamento do sfctl aplicação
Obtém o estado de funcionamento da aplicação de recursos de infraestrutura de serviço.

Devolve o estado de estado de funcionamento da aplicação de recursos de infraestrutura de serviço. A resposta relatórios de estado de funcionamento Ok, erro ou de aviso. Se a entidade não foi encontrada no arquivo de estado de funcionamento, devolve o erro.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --id da aplicação [necessário]| A identidade da aplicação. Isto é, geralmente, o nome completo da aplicação sem a ' recursos de infraestrutura:' esquema de URI. A partir da versão 6.0, nomes hierárquicos são delimitados por com o "~" carateres. Por exemplo, se o nome da aplicação é "fabric://myapp/app1", a identidade da aplicação será "myapp ~ app1" 6.0 + e "myapp/app1" em versões anteriores.|
| -implementar-aplicações-estado de funcionamento--filtro de estado| Permite a filtragem dos objetos de estado de funcionamento de aplicações implementadas devolvido nos resultados da consulta de estado de funcionamento de aplicação com base no respetivo estado de funcionamento. Os valores possíveis para este parâmetro incluem o valor de número inteiro de um dos seguintes Estados de funcionamento. Apenas as aplicações implementadas que correspondem ao filtro vai ser devolvidas. Todas as aplicações implementadas são utilizadas para avaliar o estado de funcionamento agregada. Se não for especificado, são devolvidas todas as entradas. Os valores de estado são enumeração baseada no sinalizador de, pelo que o valor pode ser uma combinação destes valores obtidos através de operador de 'Ou' bit a bit. Por exemplo, se o valor fornecido é 6, em seguida, estado de funcionamento de aplicações implementadas com o valor HealthState OK (2) e de aviso (4) são devolvidas. -Predefinição - valor predefinido. Corresponde a qualquer HealthState. O valor é zero. -Nenhuma - filtro que não corresponde a qualquer valor HealthState. Utilizado para não devolver resultados numa coleção especificada dos Estados. O valor é 1. -Ok - filtre que corresponde de entrada com o valor de HealthState Ok. O valor é 2. -Aviso - filtro que corresponde à entrada com HealthState valor aviso. O valor é 4. -Erro - filtro que corresponda à entrada com o valor de HealthState erro. O valor é 8. -Todos os - filtro que corresponda à entrada com qualquer valor HealthState. O valor é a 65535.|
| – Estado-filtro de eventos-estado de funcionamento            | Permite a filtragem na coleção de objetos de HealthEvent devolvido com base no estado de funcionamento. Os valores possíveis para este parâmetro incluem o valor de número inteiro de um dos seguintes Estados de funcionamento. Apenas os eventos que correspondem ao filtro são devolvidos. Todos os eventos são utilizados para avaliar o estado de funcionamento agregada. Se não for especificado, são devolvidas todas as entradas. Os valores de estado são enumeração baseada no sinalizador de, pelo que o valor pode ser uma combinação destes valores obtidos através de operador de 'Ou' bit a bit. Por exemplo, se o valor fornecido é 6, em seguida, todos os eventos com o valor HealthState OK (2) e de aviso (4) são devolvidos. -Predefinição - valor predefinido. Corresponde a qualquer HealthState. O valor é zero. -Nenhuma - filtro que não corresponde a qualquer valor HealthState. Utilizado para não devolver resultados numa coleção especificada dos Estados. O valor é 1. -Ok - filtre que corresponde de entrada com o valor de HealthState Ok. O valor é 2. -Aviso - filtro que corresponde à entrada com HealthState valor aviso. O valor é 4. -Erro - filtro que corresponda à entrada com o valor de HealthState erro. O valor é 8. -Todos os - filtro que corresponda à entrada com qualquer valor HealthState. O valor é a 65535.|
| – estatísticas de estado de funcionamento de exclusão | Indica se as estatísticas de estado de funcionamento devem ser devolvidas como parte do resultado da consulta. FALSO por predefinição. As estatísticas mostram o número de elementos subordinados entidades no estado de funcionamento Ok, aviso e erro.|
| -Serviços de estado de funcionamento-estado do filtro          | Permite a filtragem de objetos de estado de funcionamento dos serviços devolvido nos resultados da consulta de estado de funcionamento de serviços com base no respetivo estado de funcionamento. Os valores possíveis para este parâmetro incluem o valor de número inteiro de um dos seguintes Estados de funcionamento. Apenas os serviços que correspondem ao filtro são devolvidos. Todos os serviços são utilizados para avaliar o estado de funcionamento agregada. Se não for especificado, são devolvidas todas as entradas. Os valores de estado são enumeração baseada no sinalizador de, pelo que o valor pode ser uma combinação destes valores obtidos através de operador de 'Ou' bit a bit. Por exemplo, se o valor fornecido é 6, em seguida, estado de funcionamento de serviços com o valor HealthState OK (2) e de aviso (4) vai ser devolvido. -Predefinição - valor predefinido. Corresponde a qualquer HealthState. O valor é zero. -Nenhuma - filtro que não corresponde a qualquer valor HealthState. Utilizado para não devolver resultados numa coleção especificada dos Estados. O valor é 1. -Ok - filtre que corresponde de entrada com o valor de HealthState Ok. O valor é 2. -Aviso - filtro que corresponde à entrada com HealthState valor aviso. O valor é 4. -Erro - filtro que corresponda à entrada com o valor de HealthState erro. O valor é 8. -Todos os - filtro que corresponda à entrada com qualquer valor HealthState. O valor é a 65535.|
| tempo limite – -t                            | Tempo limite do servidor em segundos.  Predefinição: 60.|

### <a name="global-arguments"></a>Argumentos global

|Argumento|Descrição|
| --- | --- |
| -debug                                 | Aumente a verbosidade do registo para mostrar que todos os registos de depuração.|
| -ajudar -h                               | Mostra esta mensagem de ajuda e saída.|
| --o de saída                             | Formato de saída.  Valores permitidos: json, jsonc, tabela, tsv.  Predefinição: json.|
| – consulta                                 | Cadeia de consulta JMESPath. Consulte http://jmespath.org/ para obter mais informações e exemplos.|
| -verbose                               | Aumente a verbosidade do registo. Utilize - a depuração para os registos de depuração completas.|

## <a name="sfctl-application-info"></a>informações de aplicação sfctl
Obtém informações sobre uma aplicação de Service Fabric.

Devolve as informações sobre a aplicação que foi criada ou no processo de que está a ser criado no cluster de Service Fabric e cujo nome corresponde à especificado como parâmetro. A resposta inclui o nome, tipo, estado, parâmetros e outros detalhes sobre a aplicação.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --id da aplicação [necessário]| A identidade da aplicação. Isto é, geralmente, o nome completo da aplicação sem a ' recursos de infraestrutura:' esquema de URI. A partir da versão 6.0, nomes hierárquicos são delimitados por com o "~" carateres. Por exemplo, se o nome da aplicação é "fabric://myapp/app1", a identidade da aplicação será "myapp ~ app1" 6.0 + e "myapp/app1" em versões anteriores.|
| -parâmetros da aplicação de exclusão| O sinalizador que especifica se os parâmetros da aplicação serão excluídos do resultado.|
| tempo limite – -t                 | Tempo limite do servidor em segundos.  Predefinição: 60.|

### <a name="global-arguments"></a>Argumentos global

|Argumento|Descrição|
| --- | --- |
| -debug                      | Aumente a verbosidade do registo para mostrar que todos os registos de depuração.|
| -ajudar -h                    | Mostra esta mensagem de ajuda e saída.|
| --o de saída                  | Formato de saída.  Valores permitidos: json, jsonc, tabela, tsv.             Predefinição: json.|
| – consulta                      | Cadeia de consulta JMESPath. Consulte http://jmespath.org/ para obter mais informações e exemplos.|
| -verbose                    | Aumente a verbosidade do registo. Utilize - a depuração para os registos de depuração completas.|

## <a name="sfctl-application-list"></a>lista de aplicações sfctl
Obtém a lista de aplicações criado no cluster de Service Fabric que correspondem aos filtros especificados como parâmetro.

Obtém as informações sobre as aplicações que foram criados ou no processo de ser criados nos recursos de infraestrutura de serviço de cluster e correspondem aos filtros especificados como parâmetro. A resposta inclui o nome, tipo, estado, parâmetros e outros detalhes sobre a aplicação. Se as aplicações não se enquadram numa página, é devolvida uma página de resultados, bem como um token de continuação, que pode ser utilizado para obter a página seguinte.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
|– aplicação-definição de tipo de filtro| Utilizado para filtrar ApplicationDefinitionKind para operações de consulta de aplicações. -Predefinição - valor predefinido. Filtro que corresponda à entrada com qualquer valor ApplicationDefinitionKind. O valor é 0. -Todos os - filtro que corresponda à entrada com qualquer valor ApplicationDefinitionKind. O valor é a 65535. -ServiceFabricApplicationDescription - filtro que corresponda à entrada com o valor de ApplicationDefinitionKind ServiceFabricApplicationDescription. O valor é 1. -Compose - filtro que corresponda à entrada com o valor de ApplicationDefinitionKind Compose. O valor é 2. Predefinição: 65535.|
| – o nome de tipo de aplicação      | O nome do tipo de aplicação utilizado para filtrar as aplicações a consultar. Este valor não deve conter a versão de tipo de aplicação.|
| -token de continuação         | O parâmetro de token de continuação é utilizado para obter o seguinte conjunto de resultados. Um token de continuação com um valor não vazio está incluído na resposta da API de quando os resultados do sistema não se enquadram numa única resposta. Quando este valor é transmitido para a próxima chamada de API, a API devolve o seguinte conjunto de resultados. Se não existirem resultados adicionais, em seguida, o token de continuação não contém um valor. O valor deste parâmetro não deve ser o URL, codificado.|
| -parâmetros da aplicação de exclusão| O sinalizador que especifica se os parâmetros da aplicação são excluídos do resultado.|
| tempo limite – -t                 | Tempo limite do servidor em segundos.  Predefinição: 60.|

### <a name="global-arguments"></a>Argumentos global

|Argumento|Descrição|
| --- | --- |
| -debug                      | Aumente a verbosidade do registo para mostrar que todos os registos de depuração.|
| -ajudar -h                    | Mostra esta mensagem de ajuda e saída.|
| --o de saída                  | Formato de saída.  Valores permitidos: json, jsonc, tabela, tsv.             Predefinição: json.|
| – consulta                      | Cadeia de consulta JMESPath. Consulte http://jmespath.org/ para obter mais informações e exemplos.|
| -verbose                    | Aumente a verbosidade do registo. Utilize - a depuração para os registos de depuração completas.|

## <a name="sfctl-application-load"></a>sfctl carga da aplicação
Obtém informações sobre uma aplicação de Service Fabric de carga.

Devolve as carregar as informações sobre a aplicação que foi criada ou no processo de que está a ser criado no cluster de Service Fabric e cujo nome corresponde à especificado como parâmetro. A resposta inclui o nome, nós mínimo, máximo de nós, o número de nós que a aplicação está atualmente occupying e informações de métrica de carga de aplicação sobre a aplicação.

### <a name="arguments"></a>Argumentos
|Argumento|Descrição|
| --- | --- |
|--id da aplicação [necessário]| A identidade da aplicação. Isto é, geralmente, o nome completo da aplicação sem a ' recursos de infraestrutura:' esquema de URI. A partir da versão 6.0, nomes hierárquicos são delimitados por com o "~" carateres. Por exemplo, se o nome da aplicação é "fabric://myapp/app1", a identidade da aplicação será "myapp ~ app1" 6.0 + e "myapp/app1" em versões anteriores. |
| tempo limite – -t               | Tempo limite do servidor em segundos.  Predefinição: 60.|

### <a name="global-arguments"></a>Argumentos global
|Argumento|Descrição|
| --- | --- |
|-debug                    | Aumente a verbosidade do registo para mostrar que todos os registos de depuração.|
    -ajudar -h                  | Mostra esta mensagem de ajuda e saída.|
    --o de saída                | Formato de saída.  Valores permitidos: json, jsonc, tabela, tsv.  Predefinição: json.|
    – consulta                    | Cadeia de consulta JMESPath. Consulte http://jmespath.org/ para obter mais informações e exemplos.|
    -verbose                  | Aumente a verbosidade do registo. Utilize - a depuração para os registos de depuração completas.|

## <a name="sfctl-application-manifest"></a>manifesto da aplicação sfctl
Obtém o manifesto que descreve um tipo de aplicação.
        
Obtém o manifesto que descreve um tipo de aplicação. A resposta contém o manifesto da aplicação XML como uma cadeia.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| -nome de tipo de aplicação [necessário]| O nome do tipo de aplicação.|
| --tipo-versão da aplicação [necessário]| A versão do tipo de aplicação.|
| tempo limite – -t                      | Tempo limite do servidor em segundos.  Predefinição: 60.|

### <a name="global-arguments"></a>Argumentos global

|Argumento|Descrição|
| --- | --- |
| -debug                           | Aumente a verbosidade do registo para mostrar que todos os registos de depuração.|
| -ajudar -h                         | Mostra esta mensagem de ajuda e saída.|
| --o de saída                       | Formato de saída.  Valores permitidos: json, jsonc, tabela, tsv.                  Predefinição: json.|
| – consulta                           | Cadeia de consulta JMESPath. Consulte http://jmespath.org/ para obter mais informações e exemplos.|
| -verbose                         | Aumente a verbosidade do registo. Utilize - a depuração para os registos de depuração completas.|

## <a name="sfctl-application-provision"></a>Aprovisionamento de aplicações sfctl
Aprovisiona ou regista escrever uma aplicação de Service Fabric com o cluster.
        
Aprovisiona ou regista escrever uma aplicação de Service Fabric com o cluster. Isto é necessário antes de quaisquer novas aplicações podem ser instanciadas.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --tipo-compilação-caminho da aplicação [necessário]| O caminho de arquivo de imagens relativo ao pacote de aplicações.|
| tempo limite – -t                         | Tempo limite do servidor em segundos.  Predefinição: 60.|

### <a name="global-arguments"></a>Argumentos global

|Argumento|Descrição|
| --- | --- |
| -debug                              | Aumente a verbosidade do registo para mostrar que todos os registos de depuração.|
| -ajudar -h                            | Mostra esta mensagem de ajuda e saída.|
| --o de saída                          | Formato de saída.  Valores permitidos: json, jsonc, tabela, tsv.  Predefinição: json.|
| – consulta                              | Cadeia de consulta JMESPath. Consulte http://jmespath.org/ para obter mais informações e exemplos.|
| -verbose                            | Aumente a verbosidade do registo. Utilize - a depuração para os registos de depuração completas.|

## <a name="sfctl-application-type"></a>tipo de aplicação sfctl

Obtém a lista de tipos de aplicações no cluster de Service Fabric correspondente exatamente o nome especificado.

Devolve as informações sobre os tipos de aplicação que estão aprovisionados ou no processo de aprovisionamento do cluster do Service Fabric. Estes resultados são dos tipos de aplicação cujo nome corresponde ao exatamente especificado como o parâmetro e que estão em conformidade com os parâmetros de consulta especificada. Todas as versões do tipo de aplicação correspondente ao nome de tipo de aplicação são devolvidas, com cada versão devolvida como um tipo de aplicação. A resposta inclui o nome, versão, estado e outros detalhes sobre o tipo de aplicação. Esta é uma consulta paginada, o que significa que se não for todos os tipos de aplicação cabem numa página, é devolvida uma página de resultados, bem como um token de continuação, que pode ser utilizado para obter a página seguinte. Por exemplo, se existirem 10 tipos de aplicação, mas uma página só se adequa os primeira tipos de aplicações de 3, ou se resultados máximas for definido para 3, em seguida, 3 é devolvido. Para aceder ao resto dos resultados, obter as páginas subsequentes, utilizando o token de continuação devolvido na consulta seguinte. Um token de continuação vazio é devolvido se não houver nenhuma das páginas subsequente.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| -nome de tipo de aplicação [necessário]| O nome do tipo de aplicação.|
| -token de continuação           | O parâmetro de token de continuação é utilizado para obter o seguinte conjunto de resultados. Um token de continuação com um valor não vazio está incluído na resposta da API de quando os resultados do sistema não se enquadram numa única resposta. Quando este valor é transmitido para a próxima chamada de API, a API devolve o seguinte conjunto de resultados. Se não existirem resultados adicionais, em seguida, o token de continuação não contém um valor. O valor deste parâmetro não deve ser o URL, codificado.|
| -parâmetros da aplicação de exclusão  | O sinalizador que especifica se os parâmetros da aplicação serão excluídos do resultado.|
| -resultados máx.                  | O número máximo de resultados a ser devolvido como parte das consultas paginadas. Este parâmetro define o limite superior no número de resultados devolvidos. Os resultados devolvidos pode ser menor que o especificado máximo de resultados se estes não se enquadram na mensagem de acordo com as restrições de tamanho máximo de mensagem definida na configuração. Se este parâmetro for zero ou não especificado, a consulta paginada inclui o mesmo resultados possível ajustar na mensagem de retorno.|
| tempo limite – -t                   | Tempo limite do servidor em segundos.  Predefinição: 60.|

### <a name="global-arguments"></a>Argumentos global

|Argumento|Descrição|
| --- | --- |
| -debug                        | Aumente a verbosidade do registo para mostrar que todos os registos de depuração.|
| -ajudar -h                      | Mostra esta mensagem de ajuda e saída.|
| --o de saída                    | Formato de saída.  Valores permitidos: json, jsonc, tabela, tsv.               Predefinição: json.|
| – consulta                        | Cadeia de consulta JMESPath. Consulte http://jmespath.org/ para obter mais informações e exemplos.|
| -verbose                      | Aumente a verbosidade do registo. Utilize - a depuração para os registos de depuração completas.|

## <a name="sfctl-application-unprovision"></a>não aprovisionamento de aplicações sfctl
Remove ou anula o registo de um tipo de aplicação de Service Fabric do cluster.

Remove ou anula o registo de um tipo de aplicação de Service Fabric do cluster. Esta operação só pode ser efetuada se todas as instâncias de aplicação do tipo de aplicação foi eliminada. Depois do tipo de aplicação não está registado, nenhuma instância nova da aplicação pode ser criada para este tipo de aplicação em particular.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| -nome de tipo de aplicação [necessário]| O nome do tipo de aplicação.|
| --tipo-versão da aplicação [necessário]| A versão de tipo de aplicação.|
| tempo limite – -t                      | Tempo limite do servidor em segundos.  Predefinição: 60.|

### <a name="global-arguments"></a>Argumentos global

|Argumento|Descrição|
| --- | --- |
| -debug                           | Aumente a verbosidade do registo para mostrar que todos os registos de depuração.|
| -ajudar -h                         | Mostra esta mensagem de ajuda e saída.|
| --o de saída                       | Formato de saída.  Valores permitidos: json, jsonc, tabela, tsv.                  Predefinição: json.|
| – consulta                           | Cadeia de consulta JMESPath. Consulte http://jmespath.org/ para obter mais informações e exemplos.|
| -verbose                         | Aumente a verbosidade do registo. Utilize - a depuração para os registos de depuração completas.|

## <a name="sfctl-application-upgrade"></a>atualização da aplicação sfctl
Inicia a atualizar uma aplicação no cluster de Service Fabric.

Valida os parâmetros de atualização da aplicação fornecida e começa a efetuar a atualização da aplicação, se os parâmetros são válidos. Tenha em atenção que a atualização da descrição substitui a descrição da aplicação existente. Isto significa que, se os parâmetros não forem especificados, os parâmetros nas aplicações existentes serão substituídos com a lista de parâmetros vazio. Isto resulta na aplicação utilizando o valor predefinido dos parâmetros do manifesto da aplicação.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| id da aplicação – [necessário]| A identidade da aplicação. Isto é, geralmente, o nome completo da aplicação sem a ' recursos de infraestrutura:' esquema de URI. A partir da versão 6.0, nomes hierárquicos são delimitados por com o ' ~' carateres. Por exemplo, se o nome da aplicação é 'fabric://myapp/app1', a identidade da aplicação será ' myapp ~ app1' 6.0 + e ' myapp/app1' em versões anteriores.|
| versão de – aplicação [necessária]| Versão da aplicação de destino.|
| -parâmetros [necessários]| Uma lista JSON codificado de parâmetro de aplicação as substituições aplicadas ao atualizar a aplicação.|
| -predefinição serviço-estado de funcionamento da política| JSON codificado especificação da política de estado de funcionamento utilizada por predefinição para avaliar o estado de funcionamento de um tipo de serviço.|
| -Falha de ação            | A ação a executar quando uma atualização monitorizada encontra monitorização violações de políticas ou de estado de funcionamento da política.|
| -force-reinício             | Reinicie forçadamente processos durante a atualização, mesmo quando a versão de código não foi alterada.|
| -Estado de funcionamento verificação-repetição-tempo limite| A quantidade de tempo para repetir avaliações do Estado de funcionamento quando a aplicação ou o cluster mau estado de funcionamento antes da ação de falha é executada. Medido em milissegundos.  Predefinição: PT0H10M0S.|
| -Estado de funcionamento-verifique-estável-duração | A quantidade de tempo que a aplicação ou o cluster tem de permanecer bom estado de funcionamento antes de continua a atualização para o domínio de atualização seguinte.            Medido em milissegundos.  Predefinição: PT0H2M0S.|
| -Estado de funcionamento-verificação espera duração| A quantidade de tempo de espera após a conclusão de um domínio de atualização antes de aplicar políticas de estado de funcionamento. Medido em milissegundos.            Predefinição: 0.|
| – máximo aplicações mau estado de funcionamento        | O máximo permitido de percentagem de mau estado de funcionamento aplicações implementadas. Representado como um número entre 0 e 100.|
| -modo                      | O modo utilizado para monitorizar o estado de funcionamento durante uma atualização sem interrupção.            Predefinição: UnmonitoredAuto.|
| -réplica set-verifique-tempo limite | A quantidade máxima de tempo para bloquear o processamento de um domínio de atualização e evitar a perda de disponibilidade quando existirem problemas inesperados. Medido em segundos.|
| – política de estado de funcionamento de serviço     | JSON codificado mapa com a política de estado de funcionamento do tipo de serviço por nome de tipo de serviço. O mapa está vazio ser predefinido.|
| tempo limite – -t                | Tempo limite do servidor em segundos.  Predefinição: 60.|
| -atualização--tempo limite do domínio    | A quantidade de tempo cada domínio de atualização tem de concluir antes de FailureAction é executada. Medido em milissegundos.  Predefinição: P10675199DT02H48M05.4775807S.|
| -tempo limite de atualização           | A quantidade de tempo de atualização global tem de concluir antes de FailureAction é executada. Medido em milissegundos.  Predefinição: P10675199DT02H48M05.4775807S.|
| -aviso como erro          | Processe avisos de avaliação do Estado de funcionamento com o mesmo gravidade como erros.|

### <a name="global-arguments"></a>Argumentos global

|Argumento|Descrição|
| --- | --- |
| -debug                     | Aumente a verbosidade do registo para mostrar que todos os registos de depuração.|
| -ajudar -h                   | Mostra esta mensagem de ajuda e saída.|
| --o de saída                 | Formato de saída.  Valores permitidos: json, jsonc, tabela, tsv.            Predefinição: json.|
| – consulta                     | Cadeia de consulta JMESPath. Consulte http://jmespath.org/ para obter mais informações e exemplos.|
| -verbose                   | Aumente a verbosidade do registo. Utilize - a depuração para os registos de depuração completas.|

## <a name="sfctl-application-upload"></a>carregamento de aplicação sfctl
Copie um pacote de aplicação de Service Fabric para o arquivo de imagens.

Apresenta opcionalmente o progresso do carregamento para cada ficheiro no pacote. Carregar progresso é enviado para `stderr`.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| – o caminho [necessário]| Caminho para o pacote de aplicação local.|
|cadeia de – o arquivo de imagens| Armazenar a imagem de destino para carregar o pacote de aplicação.  Predefinição: recursos de infraestrutura: arquivo de imagens.|
| – Mostrar progresso  | Mostra progresso de carregamento de ficheiros para pacotes grandes.|

### <a name="global-arguments"></a>Argumentos global

|Argumento|Descrição|
| --- | --- |
| -debug       | Aumente a verbosidade do registo para mostrar que todos os registos de depuração.|
| -ajudar -h     | Mostra esta mensagem de ajuda e saída.|
| --o de saída   | Formato de saída.  Valores permitidos: json, jsonc, tabela, tsv.  Predefinição: json.|
| – consulta       | Cadeia de consulta JMESPath. Consulte http://jmespath.org/ para obter mais informações e exemplos.|
| -verbose     | Aumente a verbosidade do registo. Utilize - a depuração para os registos de depuração completas.|

## <a name="next-steps"></a>Passos seguintes
- [A configuração](service-fabric-cli.md) os recursos de infraestrutura do serviço CLI.
- Saiba como utilizar a CLI de recursos de infraestrutura de serviço utilizando o [scripts de exemplo](/azure/service-fabric/scripts/sfctl-upgrade-application).
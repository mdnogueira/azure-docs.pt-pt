---
title: "Serviço do Azure Service Fabric CLI - sfctl | Microsoft Docs"
description: "Descreve os comandos de serviço do Service Fabric CLI sfctl."
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
ms.openlocfilehash: 66649bb6ae317eb227dcdf45aa084905967c117f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="sfctl-service"></a>serviço de sfctl
Criar, eliminar e gerir o serviço, tipos de serviço e pacotes de serviços.

## <a name="commands"></a>Comandos

|Comando|Descrição|
| --- | --- |
|    nome da aplicação       | Obtém o nome da aplicação de Service Fabric para um serviço.|
|    lista do pacote do código | Obtém a lista de pacotes de código implementado num nó de Service Fabric.|
|    criar         | Cria o serviço do Service Fabric especificado a partir da descrição.|
|    eliminar         | Elimina um serviço de recursos de infraestrutura de serviço existente.|
|    tipo de implementação  | Obtém as informações sobre um tipo de serviço especificado da aplicação implementada num nó num cluster de Service Fabric.|
|    implementação tipo-lista| Obtém a lista que contém as informações sobre os tipos de serviço de aplicações implementadas num nó num cluster de Service Fabric.|
|    descrição    | Obtém a descrição de um serviço de recursos de infraestrutura de serviço existente.|
|    Estado de funcionamento         | Obtém o estado de funcionamento do serviço do Service Fabric especificado.|
|    informações           | Obtém as informações sobre o serviço específico que pertencem a uma aplicação de Service Fabric.|
|    lista           | Obtém as informações sobre todos os serviços que pertencem à aplicação especificada pelo ID da aplicação.|
|    Manifesto       | Obtém o manifesto que descreve um tipo de serviço.|
|    implementar o pacote | Transfere os pacotes associados à manifesto do serviço especificado para a cache de imagem no nó especificado.|
|    Estado de funcionamento de pacote | Obtém as informações sobre o estado de funcionamento de um pacote de serviço para uma aplicação específica implementada para um nó de Service Fabric e da aplicação.|
|    informações de pacote   | Obtém a lista de pacotes de serviços implementado num nó de Service Fabric correspondente exatamente o nome especificado.|
|    lista de pacotes   | Obtém a lista de pacotes de serviços implementado num nó de Service Fabric.|
|    Recuperar        | Indica, para o cluster do Service Fabric, o que deve tentar recuperar o serviço especificado, o que está atualmente bloqueado na perda de quórum.|
|    Estado de funcionamento de relatório  | Envia um relatório de estado de funcionamento do serviço do Service Fabric.|
|    Resolver        | Resolva uma partição de Service Fabric.|
|    lista de tipos      | Obtém a lista que contém as informações sobre tipos de serviço que são suportados por um tipo de aplicação aprovisionados num cluster de Service Fabric.|
|    Atualização         | Atualiza o serviço especificado utilizando a descrição da atualização indicado.|


## <a name="sfctl-service-create"></a>criar serviço sfctl
Cria o serviço do Service Fabric especificado a partir da descrição.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| id da aplicação – [necessário]| A identidade da aplicação principal. Isto é, geralmente, o ID completo da aplicação sem a ' recursos de infraestrutura:' esquema de URI. A partir da versão 6.0, nomes hierárquicos são delimitados por com o ' ~' carateres. Por exemplo, se o nome da aplicação é 'fabric://myapp/app1', a identidade da aplicação será ' myapp ~ app1' 6.0 + e ' myapp/app1' em versões anteriores.|
| – o nome [necessário]| Nome do serviço. Isto deve ser um subordinado do ID de aplicação.           Este é o nome, incluindo o `fabric:` URI. Por exemplo service `fabric:/A/B` é um elemento subordinado da aplicação `fabric:/A`.|
| --tipo de serviço [necessário]| Nome do tipo de serviço.|
| -modo de ativação     | O modo de ativação para o pacote de serviço.|
| -restrições         | As restrições de posicionamento como uma cadeia. Restrições de posicionamento estão expressões nas propriedades de nó e permitem restringir um serviço a nós determinado com base nos requisitos de serviço. Por exemplo, para colocar um serviço em nós em que é azul NodeType especifique o seguinte: "NodeColor = = blue".|
| -serviço correlacionado  | Nome do serviço de destino para correlacionar com.|
| -correlação         | Correlacionar o serviço com um serviço existente utilizando uma afinidade de alinhamento.|
| nome – dns            | O nome DNS do serviço a ser criado. O serviço de sistema de DNS de recursos de infraestrutura de serviço tem de estar ativado para esta definição.|
| -Contagem de instâncias      | A contagem de instâncias. Isto aplica-se apenas a serviços sem monitorização de estado.|
| esquema de – int          | Indica que o serviço deve ser particionado uniformemente através de um intervalo de números inteiros não assinados.|
| Contagem de esquema – int    | O número de partições dentro de número inteiro de chave de intervalo (para um esquema de partição de número inteiro uniforme) para criar.|
| – int esquema-alto     | O fim do intervalo de número inteiro de chave, se utilizar um esquema de partição de número inteiro uniforme.|
| – int-esquema-baixa      | O início do intervalo de número inteiro de chave, se utilizar um esquema de partição de número inteiro uniforme.|
| -as métricas de carga        | Lista JSON codificado de métricas utilizadas quando o balanceamento de carga services entre nós.|
| Mín.---tamanho conjunto de réplicas-| A réplica mínimo definir tamanho como um número. Isto aplica-se apenas a serviços com monitorização de estado.|
| -custo de movimentação           | Especifica o custo de mover para o serviço. Os valores possíveis são: 'Zero', 'Baixa', 'Média', 'Alto'.|
| -esquema com o nome        | Indica que o serviço deve ter várias partições com nome.|
| -com o nome esquema-lista   | JSON codificado lista de nomes para particionar o serviço entre, se utilizar o esquema de partição com nome.|
| – Estado não persistente  | Se for VERDADEIRO, isto indica que o serviço não tem nenhum estado persistente armazenado no disco local, ou apenas armazena estado na memória.|
| – lista de políticas de colocação  | JSON codificado lista de políticas de colocação para o serviço e quaisquer associados nomes de domínio. As políticas podem ser um ou mais dos: `NonPartiallyPlaceService`, `PreferPrimaryDomain`, `RequireDomain`, `RequireDomainDistribution`.|
| -espera de perda de quórum    | A duração máxima, em segundos, para que uma partição pode estar num Estado de perda de quórum. Isto aplica-se apenas a serviços com monitorização de estado.|
| -espera de reinício de réplica| A duração, em segundos, entre quando uma réplica fica inativo e quando é criada uma nova réplica. Isto aplica-se apenas a serviços com monitorização de estado.|
| -singleton esquema    | Indica o serviço deve ter uma única partição ou ser um serviço particionado sem.|
| -modo de espera pela réplica keep  | A duração máxima, em segundos, para o modo de espera réplicas são mantidas antes de a ser removido. Isto aplica-se apenas a serviços com monitorização de estado.|
| -monitorização de estado            | Indica que o serviço é um serviço com monitorização de estado.|
| – sem monitorização de estado           | Indica que o serviço é um serviço sem estado.|
| -set-tamanho de destino-réplica| A réplica de destino definir tamanho como um número. Isto aplica-se apenas a serviços com monitorização de estado.|
| tempo limite – -t          | Tempo limite do servidor em segundos.  Predefinição: 60.|

### <a name="global-arguments"></a>Argumentos global

|Argumento|Descrição|
| --- | --- |
| -debug               | Aumente a verbosidade do registo para mostrar que todos os registos de depuração.|
| -ajudar -h             | Mostra esta mensagem de ajuda e saída.|
| --o de saída           | Formato de saída.  Valores permitidos: json, jsonc, tabela, tsv.  Predefinição: json.|
| – consulta               | Cadeia de consulta JMESPath. Para obter mais informações e exemplos, consulte http://jmespath.org/.|
| -verbose             | Aumente a verbosidade do registo. Utilize - a depuração para os registos de depuração completas.|

## <a name="sfctl-service-delete"></a>eliminação do serviço de sfctl
Elimina um serviço de recursos de infraestrutura de serviço existente.

Elimina um serviço de recursos de infraestrutura de serviço existente. Um serviço tem de ser criado antes de ser eliminado. Por predefinição o Service Fabric tenta fechar as réplicas do serviço de forma correto e, em seguida, elimine o serviço. No entanto se o serviço está com dificuldades em Fechar corretamente a réplica, a operação de eliminação pode demorar muito tempo ou ficar bloqueado. Utilize o sinalizador de ForceRemove opcional para ignorar a sequência de fechada correto e forçadamente eliminar o serviço.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| – id de serviço [necessário]| A identidade do serviço. Isto é, geralmente, o nome completo do serviço sem o ' recursos de infraestrutura:' esquema de URI. A partir da versão 6.0, nomes hierárquicos são delimitados por com o "~" carateres. Por exemplo, se o nome do serviço é fabric://myapp/app1/svc1 ", a identidade de serviço seria" myapp ~ app1 ~ svc1 "6.0 + e" myapp/app1/svc1"em versões anteriores.|
| -force-remover      | Remova uma aplicação de Service Fabric ou o serviço forçadamente sem passar a sequência de encerramento correto. Este parâmetro pode ser utilizado para forçadamente eliminar uma aplicação ou serviço para o eliminar é exceder o tempo limite devido a problemas com o código do serviço que impede o correto fechar das réplicas.|
| tempo limite – -t        | Tempo limite do servidor em segundos.  Predefinição: 60.|

### <a name="global-arguments"></a>Argumentos global

|Argumento|Descrição|
| --- | --- |
| -debug             | Aumente a verbosidade do registo para mostrar que todos os registos de depuração.|
| -ajudar -h           | Mostra esta mensagem de ajuda e saída.|
| --o de saída         | Formato de saída.  Valores permitidos: json, jsonc, tabela, tsv.  Predefinição: json.|
| – consulta             | Cadeia de consulta JMESPath. Para obter mais informações e exemplos, consulte http://jmespath.org/.|
| -verbose           | Aumente a verbosidade do registo. Utilize - a depuração para os registos de depuração completas.|

## <a name="sfctl-service-description"></a>Descrição do serviço sfctl
Obtém a descrição de um serviço de recursos de infraestrutura de serviço existente.

Obtém a descrição de um serviço de recursos de infraestrutura de serviço existente. Tem de ser criado um serviço pode ser obtida a respetiva descrição.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| – id de serviço [necessário]| A identidade do serviço. Isto é, geralmente, o nome completo do serviço sem o ' recursos de infraestrutura:' esquema de URI. A partir da versão 6.0, nomes hierárquicos são delimitados por com o "~" carateres. Por exemplo, se o nome do serviço é "fabric://myapp/app1/svc1", a identidade de serviço será "myapp ~ app1 ~ svc1" 6.0 + e "myapp/app1/svc1" em versões anteriores.|
| tempo limite – -t        | Tempo limite do servidor em segundos.  Predefinição: 60.|

### <a name="global-arguments"></a>Argumentos global

|Argumento|Descrição|
| --- | --- |
| -debug             | Aumente a verbosidade do registo para mostrar que todos os registos de depuração.|
| -ajudar -h           | Mostra esta mensagem de ajuda e saída.|
| --o de saída         | Formato de saída.  Valores permitidos: json, jsonc, tabela, tsv.  Predefinição: json.|
| – consulta             | Cadeia de consulta JMESPath. Para obter mais informações e exemplos, consulte http://jmespath.org/.|
| -verbose           | Aumente a verbosidade do registo. Utilize - a depuração para os registos de depuração completas.|

## <a name="sfctl-service-health"></a>Estado de funcionamento de serviço de sfctl
Obtém o estado de funcionamento do serviço do Service Fabric especificado.

Obtém as informações de estado de funcionamento do serviço especificado. Utilize EventsHealthStateFilter para filtrar a recolha de eventos de estado de funcionamento comunicadas falhas no serviço, com base no estado de funcionamento. PartitionsHealthStateFilter de utilização para filtrar a coleção de partições devolvido. Se especificar um serviço que não existe no arquivo de estado de funcionamento, este cmdlet devolve um erro. .

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| – id de serviço [necessário]| A identidade do serviço. Isto é, geralmente, o nome completo do serviço sem o ' recursos de infraestrutura:' esquema de URI. A partir da versão 6.0, nomes hierárquicos são delimitados por com o "~" carateres. Por exemplo, se o nome do serviço é "fabric://myapp/app1/svc1", a identidade de serviço será "myapp ~ app1 ~ svc1" 6.0 + e "myapp/app1/svc1" em versões anteriores.|
| – Estado-filtro de eventos-estado de funcionamento | Permite a filtragem na coleção de objetos de HealthEvent devolvido com base no estado de funcionamento. Os valores possíveis para este parâmetro incluem o valor de número inteiro de um dos seguintes Estados de funcionamento. Apenas os eventos que correspondem ao filtro são devolvidos. Todos os eventos são utilizados para avaliar o estado de funcionamento agregada. Se não for especificado, são devolvidas todas as entradas. Os valores de estado são enumeração baseada no sinalizador de, pelo que o valor pode ser uma combinação destes valores obtidos através de operador de 'Ou' bit a bit. Por exemplo, se o valor fornecido é 6, em seguida, todos os eventos com o valor HealthState OK (2) e de aviso (4) são devolvidos. -Predefinição - valor predefinido. Corresponde a qualquer HealthState. O valor é zero. -Nenhuma - filtro que não corresponde a qualquer valor HealthState. Utilizado para não devolver resultados numa coleção especificada dos Estados. O valor é 1. -Ok - filtre que corresponde de entrada com o valor de HealthState Ok. O valor é 2. -Aviso - filtro que corresponde à entrada com HealthState valor aviso. O valor é 4. -Erro - filtro que corresponda à entrada com o valor de HealthState erro. O valor é 8. -Todos os - filtro que corresponda à entrada com qualquer valor HealthState. O valor é a 65535.|
|– estatísticas de estado de funcionamento de exclusão     | Indica se as estatísticas de estado de funcionamento devem ser devolvidas como parte do resultado da consulta. FALSO por predefinição. As estatísticas mostram o número de elementos subordinados entidades no estado de funcionamento Ok, aviso e erro.|
| – partições-estado de funcionamento do Estado do filtro| Permite a filtragem de objetos de estado de funcionamento partições devolvido nos resultados da consulta de estado de funcionamento de serviço com base no respetivo estado de funcionamento. Os valores possíveis para este parâmetro incluem o valor de número inteiro de um dos seguintes Estados de funcionamento. Apenas as partições que correspondem ao filtro são devolvidas. Todas as partições são utilizadas para avaliar o estado de funcionamento agregada. Se não for especificado, são devolvidas todas as entradas. Os valores de estado são enumeração baseada no sinalizador de, pelo que o valor pode ser uma combinação destes valores obtidos através de operador de 'Ou' bit a bit. Por exemplo, se o valor fornecido é "6", em seguida, estado de funcionamento de partições com o valor HealthState OK (2) e de aviso (4) são devolvidas. -Predefinição - valor predefinido. Corresponde a qualquer HealthState.                  O valor é zero. -Nenhuma - filtro que não corresponde a qualquer valor HealthState. Utilizado para não devolver resultados numa coleção especificada dos Estados. O valor é 1. -Ok - filtre que corresponde de entrada com o valor de HealthState Ok. O valor é 2. -Aviso - filtro que corresponde à entrada com HealthState valor aviso. O valor é 4. -Erro - filtro que corresponda à entrada com o valor de HealthState erro. O valor é 8. -Todos os - filtro que corresponda à entrada com qualquer valor HealthState. O valor é a 65535.|
| tempo limite – -t                 | Tempo limite do servidor em segundos.  Predefinição: 60.|

### <a name="global-arguments"></a>Argumentos global

|Argumento|Descrição|
| --- | --- |
| -debug                      | Aumente a verbosidade do registo para mostrar que todos os registos de depuração.|
| -ajudar -h                    | Mostra esta mensagem de ajuda e saída.|
| --o de saída                  | Formato de saída.  Valores permitidos: json, jsonc, tabela, tsv.                  Predefinição: json.|
| – consulta                      | Cadeia de consulta JMESPath. Consulte http://jmespath.org/ para obter mais informações e exemplos.|
| -verbose                    | Aumente a verbosidade do registo. Utilize - a depuração para os registos de depuração completas.|

## <a name="sfctl-service-info"></a>informações do serviço de sfctl
Obtém as informações sobre o serviço específico que pertencem a uma aplicação de Service Fabric.

Devolve as informações sobre o serviço especificado que pertencem à aplicação de Service Fabric especificada.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --id da aplicação [necessário]| A identidade da aplicação. Isto é, geralmente, o nome completo da aplicação sem a ' recursos de infraestrutura:' esquema de URI. A partir da versão 6.0, nomes hierárquicos são delimitados por com o "~" carateres. Por exemplo, se o nome da aplicação é "fabric://myapp/app1", a identidade da aplicação será "myapp ~ app1" 6.0 + e "myapp/app1" em versões anteriores.|
| – id de serviço [necessário]| A identidade do serviço. Isto é, geralmente, o nome completo do serviço sem o ' recursos de infraestrutura:' esquema de URI. A partir da versão 6.0, nomes hierárquicos são delimitados por com o "~" carateres. Por exemplo, se o nome do serviço é "fabric://myapp/app1/svc1", a identidade de serviço será "myapp ~ app1 ~ svc1" 6.0 + e "myapp/app1/svc1" em versões anteriores.|
| tempo limite – -t            | Tempo limite do servidor em segundos.  Predefinição: 60.|

### <a name="global-arguments"></a>Argumentos global

|Argumento|Descrição|
| --- | --- |
| -debug                 | Aumente a verbosidade do registo para mostrar que todos os registos de depuração.|
| -ajudar -h               | Mostra esta mensagem de ajuda e saída.|
| --o de saída             | Formato de saída.  Valores permitidos: json, jsonc, tabela, tsv.  Predefinição: json.|
| – consulta                 | Cadeia de consulta JMESPath. Para obter mais informações e exemplos, consulte http://jmespath.org/.|
| -verbose               | Aumente a verbosidade do registo. Utilize - a depuração para os registos de depuração completas.|

## <a name="sfctl-service-list"></a>lista de serviço sfctl
Obtém as informações sobre todos os serviços que pertencem à aplicação especificada pelo ID da aplicação.

Devolve as informações sobre todos os serviços que pertencem à aplicação especificada pelo ID da aplicação.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --id da aplicação [necessário]| A identidade da aplicação. Isto é, geralmente, o nome completo da aplicação sem a ' recursos de infraestrutura:' esquema de URI. A partir da versão 6.0, nomes hierárquicos são delimitados por com o "~" carateres. Por exemplo, se o nome da aplicação é "fabric://myapp/app1", a identidade da aplicação será "myapp ~ app1" 6.0 + e "myapp/app1" em versões anteriores.|
| -token de continuação    | O parâmetro de token de continuação é utilizado para obter o seguinte conjunto de resultados. Um token de continuação com um valor não vazio está incluído na resposta da API de quando os resultados do sistema não se enquadram numa única resposta. Quando este valor é transmitido para a próxima chamada de API, a API devolve o seguinte conjunto de resultados. Se não existirem resultados adicionais, em seguida, o token de continuação não contém um valor. O valor deste parâmetro não deve ser o URL, codificado.|
| -nome do tipo de serviço     | O nome do tipo de serviço utilizado para filtrar os serviços a consultar.|
| tempo limite – -t            | Tempo limite do servidor em segundos.  Predefinição: 60.|

### <a name="global-arguments"></a>Argumentos global

|Argumento|Descrição|
| --- | --- |
| -debug                 | Aumente a verbosidade do registo para mostrar que todos os registos de depuração.|
| -ajudar -h               | Mostra esta mensagem de ajuda e saída.|
| --o de saída             | Formato de saída.  Valores permitidos: json, jsonc, tabela, tsv.  Predefinição: json.|
| – consulta                 | Cadeia de consulta JMESPath. Para obter mais informações e exemplos, consulte http://jmespath.org/.|
| -verbose               | Aumente a verbosidade do registo. Utilize - a depuração para os registos de depuração completas.|

## <a name="sfctl-service-manifest"></a>manifesto de serviço sfctl
Obtém o manifesto que descreve um tipo de serviço.

Obtém o manifesto que descreve um tipo de serviço. A resposta contém o manifesto do serviço XML como uma cadeia.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| -nome de tipo de aplicação [necessário]| O nome do tipo de aplicação.|
| --tipo-versão da aplicação [necessário]| A versão do tipo de aplicação.|
| --manifesto-nome do serviço [necessário]| O nome de um manifesto de serviço registado como parte de um tipo de aplicação de um cluster do Service Fabric.|
| tempo limite – -t                      | Tempo limite do servidor em segundos.  Predefinição: 60.|

### <a name="global-arguments"></a>Argumentos global

|Argumento|Descrição|
| --- | --- |
| -debug                           | Aumente a verbosidade do registo para mostrar que todos os registos de depuração.|
| -ajudar -h                         | Mostra esta mensagem de ajuda e saída.|
| --o de saída                       | Formato de saída.  Valores permitidos: json, jsonc, tabela, tsv.                       Predefinição: json.|
| – consulta                           | Cadeia de consulta JMESPath. Consulte http://jmespath.org/ para obter mais informações e exemplos.|
| -verbose                         | Aumente a verbosidade do registo. Utilize - a depuração para os registos de depuração completas.|

## <a name="sfctl-service-recover"></a>sfctl recuperar de serviço
Indica, para o cluster do Service Fabric, o que deve tentar recuperar o serviço especificado, o que está atualmente bloqueado na perda de quórum.

Indica, para o cluster do Service Fabric, o que deve tentar recuperar o serviço especificado, o que está atualmente bloqueado na perda de quórum. Esta operação só deve ser efetuada se não não possível recuperar as réplicas que estão em baixo. Utilização incorreta desta API pode causar a potencial perda de dados.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| – id de serviço [necessário]| A identidade do serviço. Isto é, geralmente, o nome completo do serviço sem o ' recursos de infraestrutura:' esquema de URI. A partir da versão 6.0, nomes hierárquicos são delimitados por com o "~" carateres. Por exemplo, se o nome do serviço é fabric://myapp/app1/svc1 ", a identidade de serviço seria" myapp ~ app1 ~ svc1 "6.0 + e" myapp/app1/svc1"em versões anteriores.|
| tempo limite – -t        | Tempo limite do servidor em segundos.  Predefinição: 60.|

### <a name="global-arguments"></a>Argumentos global

|Argumento|Descrição|
| --- | --- |
| -debug             | Aumente a verbosidade do registo para mostrar que todos os registos de depuração.|
| -ajudar -h           | Mostra esta mensagem de ajuda e saída.|
| --o de saída         | Formato de saída.  Valores permitidos: json, jsonc, tabela, tsv.  Predefinição: json.|
| – consulta             | Cadeia de consulta JMESPath. Para obter mais informações e exemplos, consulte http://jmespath.org/.|
| -verbose           | Aumente a verbosidade do registo. Utilize - a depuração para os registos de depuração completas.|

## <a name="sfctl-service-resolve"></a>resolução de serviço sfctl
Resolva uma partição de Service Fabric.

Resolva uma partição de serviço do Service Fabric, para obter os pontos finais das réplicas do serviço.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| – id de serviço [necessário]| A identidade do serviço. Isto é, geralmente, o nome completo do serviço sem o ' recursos de infraestrutura:' esquema de URI. A partir da versão 6.0, nomes hierárquicos são delimitados por com o "~" carateres. Por exemplo, se o nome do serviço é "fabric://myapp/app1/svc1", a identidade de serviço será "myapp ~ app1 ~ svc1" 6.0 + e "myapp/app1/svc1" em versões anteriores.|
| – o tipo de chave de partição| Tipo de chave para a partição. Este parâmetro é necessário se o esquema de partição para o serviço é Int64Range ou nomeado. Os valores possíveis são seguintes. -Nenhum (1) - indica que o parâmetro PartitionKeyValue não está especificado. Isto é válido para partições com a criação de partições de esquema como Singleton. Este é o valor predefinido. O valor é 1. -Int64Range (2) - indica que o parâmetro de PartitionKeyValue é uma chave de partição int64. Isto é válido para partições com a criação de partições de esquema como Int64Range. O valor é 2. -Com o nome (3) - indica que o parâmetro PartitionKeyValue é um nome da partição. Isto é válido para partições com a criação de partições de esquema como nomeado. O valor é 3.|
| – o valor de chave de partição  | Chave de partição. Isto é necessário se o esquema de partição para o serviço é Int64Range ou nomeado.|
| -versão anterior rsp | O valor no campo versão da resposta recebida anteriormente. Isto é necessário se o utilizador sabe que o resultado que foi obtido anteriormente está obsoleto.|
| tempo limite – -t        | Tempo limite do servidor em segundos.  Predefinição: 60.|

### <a name="global-arguments"></a>Argumentos global

|Argumento|Descrição|
| --- | --- |
| -debug             | Aumente a verbosidade do registo para mostrar que todos os registos de depuração.|
| -ajudar -h           | Mostra esta mensagem de ajuda e saída.|
| --o de saída         | Formato de saída.  Valores permitidos: json, jsonc, tabela, tsv.  Predefinição: json.|
| – consulta             | Cadeia de consulta JMESPath. Para obter mais informações e exemplos, consulte http://jmespath.org/.|
| -verbose           | Aumente a verbosidade do registo. Utilize - a depuração para os registos de depuração completas.|

## <a name="sfctl-service-update"></a>atualização do serviço sfctl
Atualiza o serviço especificado utilizando a descrição da atualização indicado.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| – id de serviço [necessário]| Serviço de destino para atualizar. Isto é, geralmente, o ID completo do serviço sem o ' recursos de infraestrutura:' esquema de URI. A partir da versão 6.0, nomes hierárquicos são delimitados por com o "~" carateres. Por exemplo, se o nome do serviço é 'fabric://myapp/app1/svc1', a identidade de serviço será ' myapp ~ app1 ~ svc1' 6.0 + e ' myapp/app1/svc1' em versões anteriores.|
| -restrições         | As restrições de posicionamento como uma cadeia. Restrições de posicionamento estão expressões nas propriedades de nó e permitem restringir um serviço a nós determinado com base nos requisitos de serviço. Por exemplo, para colocar um serviço em nós em que é azul NodeType especifique o seguinte: "NodeColor = = blue".|
| -serviço correlacionado  | Nome do serviço de destino para correlacionar com.|
| -correlação         | Correlacionar o serviço com um serviço existente utilizando uma afinidade de alinhamento.|
| -Contagem de instâncias      | A contagem de instâncias. Isto aplica-se apenas a serviços sem monitorização de estado.|
| -as métricas de carga        | Lista JSON codificado de métricas utilizado quando balanceamento em nós de carga.|
| Mín.---tamanho conjunto de réplicas-| A réplica mínimo definir tamanho como um número. Isto aplica-se apenas a serviços com monitorização de estado.|
| -custo de movimentação           | Especifica o custo de mover para o serviço. Os valores possíveis são: 'Zero', 'Baixa', 'Média', 'Alto'.|
| – lista de políticas de colocação  | JSON codificado lista de políticas de colocação para o serviço e quaisquer associados nomes de domínio. As políticas podem ser um ou mais dos: `NonPartiallyPlaceService`, `PreferPrimaryDomain`, `RequireDomain`, `RequireDomainDistribution`.|
| -espera de perda de quórum    | A duração máxima, em segundos, para que uma partição pode estar num Estado de perda de quórum. Isto aplica-se apenas a serviços com monitorização de estado.|
| -espera de reinício de réplica| A duração, em segundos, entre quando uma réplica fica inativo e quando é criada uma nova réplica. Isto aplica-se apenas a serviços com monitorização de estado.|
| -modo de espera pela réplica keep  | A duração máxima, em segundos, para o modo de espera réplicas são mantidas antes de a ser removido. Isto aplica-se apenas a serviços com monitorização de estado.|
| -monitorização de estado            | Indica que o serviço de destino é um serviço com monitorização de estado.|
| – sem monitorização de estado           | Indica que o serviço de destino é um serviço sem estado.|
| -set-tamanho de destino-réplica| A réplica de destino definir tamanho como um número. Isto aplica-se apenas a serviços com monitorização de estado.|
| tempo limite – -t          | Tempo limite do servidor em segundos.  Predefinição: 60.|

### <a name="global-arguments"></a>Argumentos global

|Argumento|Descrição|
| --- | --- |
| -debug               | Aumente a verbosidade do registo para mostrar que todos os registos de depuração.|
| -ajudar -h             | Mostra esta mensagem de ajuda e saída.|
| --o de saída           | Formato de saída.  Valores permitidos: json, jsonc, tabela, tsv.  Predefinição: json.|
| – consulta               | Cadeia de consulta JMESPath. Para obter mais informações e exemplos, consulte http://jmespath.org/.|
| -verbose             | Aumente a verbosidade do registo. Utilize - a depuração para os registos de depuração completas.|

## <a name="next-steps"></a>Passos seguintes
- [Configurar](service-fabric-cli.md) a CLI de recursos de infraestrutura de serviço.
- Saiba como utilizar a CLI de recursos de infraestrutura de serviço utilizando o [scripts de exemplo](/azure/service-fabric/scripts/sfctl-upgrade-application).
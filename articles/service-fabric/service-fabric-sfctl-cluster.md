---
title: Cluster do Azure Service Fabric CLI - sfctl | Microsoft Docs
description: Descreve os comandos de cluster do Service Fabric CLI sfctl.
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
ms.openlocfilehash: dc2ed59d6adaca97b23dddcb7ec968d90171b483
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="sfctl-cluster"></a>sfctl cluster
Selecione, gerir e operar clusters de Service Fabric.

## <a name="commands"></a>Comandos

|Comando|Descrição|
| --- | --- |
|    versões de código| Obtém uma lista dos recursos de infraestrutura versões de código aprovisionados num cluster de Service Fabric.|
|    versões de configuração | Obtém uma lista dos recursos de infraestrutura versões de configuração que são aprovisionadas num cluster de Service Fabric.|
|    Estado de funcionamento       | Obtém o estado de funcionamento de um cluster do Service Fabric.|
|    Manifesto     | Obter o manifesto do cluster de Service Fabric.|
|    cancelamento da operação| Cancela uma operação do índice de falhas induzida pelo utilizador.|
|    operationgit | Obtém uma lista de operações do índice de falhas induzida pelo utilizador filtrados pela entrada fornecida.|
|    Aprovisionar     | Aprovisione os pacotes de código ou de configuração de um cluster do Service Fabric.|
|    sistema de recuperação  | Indica, para o cluster do Service Fabric, o que deve tentar recuperar os serviços do sistema que atualmente estão bloqueados na perda de quórum.|
|Estado de funcionamento de relatório   | Envia um relatório de estado de funcionamento no cluster de Service Fabric.|
|    Selecione       | Liga a um ponto final de cluster de Service Fabric.|
| Não aprovisionamento     | Anular o provisionamento os pacotes de código ou de configuração de um cluster do Service Fabric.|
|    atualização         | Comece a atualizar a versão de código ou de configuração de um cluster do Service Fabric.|
|    retoma de atualização  | Efetuar a atualização do cluster avançar para o domínio de atualização seguinte.|
|    reversão de atualização| Reverta a atualização de um cluster do Service Fabric.|
|    Estado de atualização  | Obtém o progresso da atualização do cluster atual.|
|atualização-atualização  | Atualize os parâmetros de atualização de uma atualização de cluster do Service Fabric.|


## <a name="sfctl-cluster-health"></a>o estado de funcionamento do sfctl cluster
Obtém o estado de funcionamento de um cluster do Service Fabric.

Obtém o estado de funcionamento de um cluster do Service Fabric. Utilize EventsHealthStateFilter para filtrar a recolha de eventos de estado de funcionamento comunicado no cluster com base no estado de funcionamento. Da mesma forma, utilize NodesHealthStateFilter e ApplicationsHealthStateFilter para filtrar a coleção de nós e aplicações devolvidas com base no respetivo estado de funcionamento agregada. 

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| – aplicações-estado de funcionamento do Estado do filtro| Permite a filtragem de estado de funcionamento do Estado da aplicação
                                                    objects returned in the result of cluster health
                                                    query based on their health state. The possible
                                                    values for this parameter include integer value
                                                    obtained from members or bitwise operations on
                                                    members of HealthStateFilter enumeration. Only
                                                    applications that match the filter are returned.
                                                    All applications are used to evaluate the
                                                    aggregated health state. If not specified, all
                                                    entries are returned. The state values are flag
                                                    based enumeration, so the value could be a
                                                    combination of these values obtained using
                                                    bitwise 'OR' operator. For example, if the
                                                    provided value is 6 then health state of
                                                    applications with HealthState value of OK (2)
                                                    and Warning (4) are returned. - Default -
                                                    Default value. Matches any HealthState. The
                                                    value is zero. - None - Filter that doesn't
                                                    match any HealthState value. Used in order to
                                                    return no results on a given collection of
                                                    states. The value is 1. - Ok - Filter that
                                                    matches input with HealthState value Ok. The
                                                    value is 2. - Warning - Filter that matches
                                                    input with HealthState value Warning. The value
                                                    is 4. - Error - Filter that matches input with
                                                    HealthState value Error. The value is 8. - All -
                                                    Filter that matches input with any HealthState value. The value is 65535.|
| – Estado-filtro de eventos-estado de funcionamento | Permite a filtragem na coleção de objetos de HealthEvent devolvido com base no estado de funcionamento. Os valores possíveis para este parâmetro incluem o valor de número inteiro de um dos seguintes Estados de funcionamento. Apenas os eventos que correspondem ao filtro são devolvidos. Todos os eventos são utilizados para avaliar o estado de funcionamento agregada. Se não for especificado, são devolvidas todas as entradas. Os valores de estado são sinalizador com base na enumeração, pelo que o valor pode ser uma combinação destes valores obtidos através de operador de 'Ou' bit a bit. Por exemplo, se o valor fornecido é 6, em seguida, todos os eventos com o valor HealthState OK (2) e de aviso (4) são devolvidos. -Predefinição - valor predefinido. Corresponde a qualquer HealthState. O valor é zero. -Nenhuma - filtro que não corresponde a qualquer valor HealthState. Utilizado para não devolver resultados numa coleção especificada dos Estados. O valor é 1. -Ok - filtre que corresponde de entrada com o valor de HealthState Ok. O valor é 2. -Aviso - filtro que corresponde à entrada com HealthState valor aviso.
O valor é 4. -Erro - filtro que corresponda à entrada com o valor de HealthState erro. O valor é 8. -Todos os - filtro que corresponda à entrada com qualquer valor HealthState. O valor é a 65535. | | – estatísticas de estado de funcionamento de exclusão | Indica se as estatísticas de estado de funcionamento devem ser devolvidas como parte do resultado da consulta. FALSO por predefinição. As estatísticas mostram o número de elementos subordinados entidades no estado de funcionamento Ok, aviso e erro. | |   – incluir-sistema--estado de funcionamento-das estatísticas da aplicação | Indica se as estatísticas de estado de funcionamento devem incluir o fabric: / estatísticas de estado de funcionamento da aplicação de sistema. FALSO por predefinição. Se IncludeSystemApplicationHealthStatistics estiver definido como VERDADEIRO, o estado de funcionamento estatísticas incluem as entidades que pertencem aos recursos de infraestrutura: / aplicação de sistema. Caso contrário, o resultado da consulta inclui estatísticas de estado de funcionamento apenas para aplicações do utilizador. As estatísticas de estado de funcionamento tem de ser incluídas no resultado da consulta para este parâmetro seja aplicada. | | – nós-estado de funcionamento do Estado do filtro | Permite a filtragem de objetos de estado de funcionamento nó devolvidos nos resultados da consulta de estado de funcionamento de cluster com base no respetivo estado de funcionamento. Os valores possíveis para este parâmetro incluem o valor de número inteiro de um dos seguintes Estados de funcionamento. Apenas nós que correspondem ao filtro são devolvidos. Todos os nós são utilizados para avaliar o estado de funcionamento agregada. Se não for especificado, são devolvidas todas as entradas. Os valores de estado são enumeração baseada no sinalizador de, pelo que o valor pode ser uma combinação destes valores obtidos através de operador de 'Ou' bit a bit. Por exemplo, se o valor fornecido é "6", em seguida, estado de funcionamento de nós com o valor HealthState OK (2) e de aviso (4) são devolvidas. -Predefinição - valor predefinido. Corresponde a qualquer HealthState. O valor é zero. -Nenhuma - filtro que não corresponde a qualquer valor HealthState. Utilizado para não devolver resultados numa coleção especificada dos Estados. O valor é 1. -Ok - filtre que corresponde de entrada com o valor de HealthState Ok. O valor é 2. -Aviso - filtro que corresponde à entrada com HealthState valor aviso.
O valor é 4. -Erro - filtro que corresponda à entrada com o valor de HealthState erro. O valor é 8. -Todos os - filtro que corresponda à entrada com qualquer valor HealthState. O valor é a 65535. | | tempo limite – -t | Tempo limite do servidor em segundos.  Predefinição: 60. |

### <a name="global-arguments"></a>Argumentos global

|Argumento|Descrição|
| --- | --- |
| -debug                        | Aumente a verbosidade do registo para mostrar que todos os registos de depuração.|
| -ajudar -h                      | Mostra esta mensagem de ajuda e saída.|
| --o de saída                    | Formato de saída.  Valores permitidos: json, jsonc, tabela, tsv.                    Predefinição: json.|
| – consulta                        | Cadeia de consulta JMESPath. Consulte http://jmespath.org/ para obter mais informações e exemplos.|
| -verbose                      | Aumente a verbosidade do registo. Utilize - a depuração para os registos de depuração completas.|

## <a name="sfctl-cluster-manifest"></a>manifesto do cluster sfctl
Obter o manifesto do cluster de Service Fabric.

Obter o manifesto do cluster de Service Fabric. O manifesto do cluster contém as propriedades do cluster, que incluem os tipos de outro nó no cluster, configurações de segurança, falhas e topologias de domínio de atualização etc. Estas propriedades são especificadas como parte do ficheiro Clusterconfig durante a implementação de um cluster autónomo. No entanto, a maioria das informações no manifesto do cluster é gerado internamente pelo recurso de infraestrutura de serviço durante a implementação de cluster nos outros cenários de implementação (por exemplo, quando utilizar o [portal do Azure](https://portal.azure.com)). O conteúdo do manifesto do cluster é apenas para fins informativos e os utilizadores não são esperados para colocar uma dependência no formato do conteúdo do ficheiro ou a interpretação. 

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

## <a name="sfctl-cluster-provision"></a>Aprovisionamento de cluster sfctl
Aprovisione os pacotes de código ou de configuração de um cluster do Service Fabric.

        Validate and provision the code or configuration packages of a Service Fabric cluster.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
|-cluster-manifesto--caminho do ficheiro| O caminho de ficheiro de manifesto do cluster.|
|    – o caminho de ficheiro de código            | Caminho de ficheiro de pacote do código de cluster.|
|    tempo limite – -t                | Tempo limite do servidor em segundos.  Predefinição: 60.|

### <a name="global-arguments"></a>Argumentos global

|Argumento|Descrição|
| --- | --- |
| -debug | Aumente a verbosidade do registo para mostrar que todos os registos de depuração.|
| -ajudar -h  | Mostra esta mensagem de ajuda e saída.|
| --o de saída| Formato de saída.  Valores permitidos: json, jsonc, tabela, tsv.  Predefinição: json.|
| – consulta | Cadeia de consulta JMESPath. Consulte http://jmespath.org/ para obter mais informações e exemplos.|
| -verbose  | Aumente a verbosidade do registo. Utilize - a depuração para os registos de depuração completas.|

## <a name="sfctl-cluster-select"></a>Selecione de cluster sfctl
Liga a um ponto final de cluster de Service Fabric.

Se ligar ao cluster segura, especifique um certificado (. crt) e o ficheiro de chave (.key) ou um único ficheiro com ambos os (. pem). Não especifique ambos. Opcionalmente, de se ligar a um cluster seguro, também especificar um caminho para um ficheiro de pacote de AC ou o diretório de certificados de AC fidedignos.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| -ponto final [necessário]| URL de ponto final, incluindo a porta e o prefixo HTTP ou HTTPS de cluster.|
| – aad             | Utilize o Azure Active Directory para autenticação.|
| – a AC              | Caminho para o diretório de certificados de AC para tratar como válido ou um ficheiro de pacote de AC.|
| -certificados            | Caminho para um ficheiro de certificado de cliente.|
| -chave             | Caminho do ficheiro de chave do certificado de cliente.|
| -Certifique-se de não       | Desativar a verificação de certificados quando utilizar HTTPS, tenha em atenção: Esta é uma opção inseguras e não deve ser utilizada para ambientes de produção.|
| -pem             | Caminho do certificado de cliente, como um ficheiro. pem.|

### <a name="global-arguments"></a>Argumentos global

|Argumento|Descrição|
| --- | --- |
| -debug           | Aumente a verbosidade do registo para mostrar que todos os registos de depuração.|
| -ajudar -h         | Mostra esta mensagem de ajuda e saída.|
| --o de saída       | Formato de saída.  Valores permitidos: json, jsonc, tabela, tsv.  Predefinição: json.|
| – consulta           | Cadeia de consulta JMESPath. Consulte http://jmespath.org/ para obter mais informações e exemplos.|
| -verbose         | Aumente a verbosidade do registo. Utilize - a depuração para os registos de depuração completas.|

## <a name="sfctl-cluster-unprovision"></a>não aprovisionamento de cluster sfctl
Anular o provisionamento os pacotes de código ou de configuração de um cluster do Service Fabric.

        Unprovision the code or configuration packages of a Service Fabric cluster.

### <a name="arguments"></a>Argumentos
|Argumento|Descrição|
| --- | --- |
|-versão de código  | A versão de pacote do código de cluster.|
|    -config-versão| O versão do manifesto do cluster.|
|    tempo limite – -t    | Tempo limite do servidor em segundos.  Predefinição: 60.|

### <a name="global-arguments"></a>Argumentos global
|Argumento|Descrição|
| --- | --- |
|-debug         | Aumente a verbosidade do registo para mostrar que todos os registos de depuração.|
 |   -ajudar -h       | Mostra esta mensagem de ajuda e saída.|
 |   --o de saída     | Formato de saída.  Valores permitidos: json, jsonc, tabela, tsv.  Predefinição: json.|
 |   – consulta         | Cadeia de consulta JMESPath. Consulte http://jmespath.org/ para obter mais informações e
                      exemplos.|
 |   -verbose       | Aumente a verbosidade do registo. Utilize - a depuração para os registos de depuração completas.|


## <a name="sfctl-cluster-upgrade"></a>atualização do cluster sfctl
Comece a atualizar a versão de código ou de configuração de um cluster do Service Fabric.

        Validate the supplied upgrade parameters and start upgrading the code or configuration
        version of a Service Fabric cluster if the parameters are valid.

### <a name="arguments"></a>Argumentos
|Argumento|Descrição|
| --- | --- |
|    mapa de estado de funcionamento – a aplicação                      | Dicionário JSON codificado de pares de nome de aplicação e
                                            percentagem de máxima mau estado de funcionamento antes de gerar o erro.|
 |   aplicações--tipo-estado de funcionamento-mapa                 | JSON codificado dicionário de pares de tipo de aplicação
                                            name and maximum percentage unhealthy before raising
                                            error.|
 |   -versão de código | A versão de código de cluster. | |   versão – configuração | A versão de configuração de cluster. | |   -avaliação de estado de funcionamento de diferenças | Permite a avaliação de estado de funcionamento de diferenças em vez de avaliação de estado de funcionamento absoluto após a conclusão de cada domínio de atualização. | |   -delta mau estado de funcionamento nós | O máximo permitido percentagem de nós degradação de estado de funcionamento permitida durante as atualizações de cluster.  Predefinição: 10.
O delta é medido entre o estado de nós no início da atualização e o estado de nós no momento da avaliação de estado de funcionamento. A verificação é efetuada após a conclusão de atualização cada domínio de atualização para se certificar de que o estado global do cluster está dentro dos limites tolerated. | |   -Falha de ação | Os valores possíveis incluem: 'Inválido,' 'Reversão', 'Manual'. | |   -force-reinício | Forçar o reinício. | |   – tentativas de verificação de estado de funcionamento | Tempo limite de tentativas de verificação do Estado de funcionamento, medido em milissegundos. | |   -Estado de funcionamento-verifique-estável | Verificação de estado de funcionamento duração estável medida em milissegundos. | |  -espera de verificação de estado de funcionamento | Verificação de estado de funcionamento Aguarde duração medida em milissegundos. | |  -réplica set-verifique-tempo limite da | Réplica atualização definir um tempo limite de verificação medido em segundos. | |   -implementar atualização-modo | Os valores possíveis incluem: 'Inválido', 'UnmonitoredAuto', 'UnmonitoredManual', 'Monitorizado'.  Predefinição: UnmonitoredAuto. | |  tempo limite – -t | Tempo limite do servidor em segundos.  Predefinição: 60. | |  -mau estado de funcionamento de aplicações | O máximo permitido percentagem das aplicações mau estado de funcionamento antes de o comunicar um erro.
Por exemplo, para permitir a 10% de aplicações para ser mau estado de funcionamento, este valor será 10. A percentagem representa a percentagem máxima de tolerated das aplicações que podem ser mau estado de funcionamento antes do cluster for considerado no erro. Se a percentagem é respeitada, mas existir pelo menos uma aplicação de mau estado de funcionamento, o estado de funcionamento é avaliado como aviso. Esta é calculada dividindo o número de aplicações mau estado de funcionamento e o número total de instâncias de aplicações no cluster, excluindo as aplicações de tipos de aplicação que estão incluídas no ApplicationTypeHealthPolicyMap. O cálculo Arredonda por excesso para tolerar uma falha num pequeno número de aplicações. | |   -mau estado de funcionamento de nós | O máximo permitido de percentagem de nós mau estado de funcionamento antes de o comunicar um erro.
Por exemplo, para permitir a 10% de nós incorreto, este valor será 10. A percentagem representa a percentagem máxima de tolerated de nós que podem ser mau estado de funcionamento antes do cluster for considerado no erro. Se a percentagem é respeitada, mas existir pelo menos um nó de mau estado de funcionamento, o estado de funcionamento é avaliado como aviso. A percentagem é calculada dividindo o número de nós mau estado de funcionamento e o número total de nós no cluster. O cálculo Arredonda por excesso para tolerar uma falha num pequeno número de nós. Em grandes clusters, alguns nós sempre será baixo ou horizontalmente para reparações, pelo que esta percentagem deve ser configurada para tolerar que. | |   -atualização-domínio-delta-mau estado de funcionamento-nós | O máximo permitido percentagem de nós de domínio de atualização de degradação de estado de funcionamento permitida durante as atualizações de cluster.
Predefinição: 15.
O delta é medido entre o estado de nós do domínio de atualização no início da atualização e o estado de nós do domínio de atualização no momento da avaliação de estado de funcionamento. A verificação é efetuada após a conclusão de atualização cada domínio de atualização para concluir todos os domínios de atualização para se certificar de que o estado dos domínios de atualização está dentro dos limites tolerated. | |   -atualização--tempo limite do domínio | Tempo limite do domínio de atualização, medido em milissegundos. | |   -tempo limite de atualização | Tempo limite de atualização, medido em milissegundos. | |   -aviso como erro | Avisos são tratados com a mesma gravidade como erros. |

### <a name="global-arguments"></a>Argumentos global
    |Argumento|Descrição|
| --- | --- |
|-debug                               | Aumente a verbosidade do registo para mostrar que todos os registos de depuração.|
|    -ajudar -h                             | Mostra esta mensagem de ajuda e saída.|
|    --o de saída                           | Formato de saída.  Valores permitidos: json, jsonc, tabela, tsv.
                                            Predefinição: json.|
|    – consulta                               | Cadeia de consulta JMESPath. Consulte http://jmespath.org/ para obter mais informações
                                            informações e exemplos.|
|    -verbose                             | Aumente a verbosidade do registo. Utilize - a depuração para depuração completa
                                            registos.|

## <a name="next-steps"></a>Passos seguintes
- [A configuração](service-fabric-cli.md) os recursos de infraestrutura do serviço CLI.
- Saiba como utilizar a CLI de recursos de infraestrutura de serviço utilizando o [scripts de exemplo](/azure/service-fabric/scripts/sfctl-upgrade-application).
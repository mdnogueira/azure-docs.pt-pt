---
title: "Regra de firewall de aplicações do Azure web de Gateway de aplicação CR grupos e as regras | Microsoft Docs"
description: "Esta página fornece informações sobre regras e grupos de regras de CR de firewall de aplicação de web."
documentationcenter: na
services: application-gateway
author: davidmu1
manager: timlt
editor: tysonn
ms.assetid: e5ea5cf9-3b41-4b85-a12c-e758bff7f3ec
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.custom: 
ms.workload: infrastructure-services
ms.date: 03/28/2017
ms.author: davidmu
ms.openlocfilehash: 9265be4ac4258115c9302189d84b20e4894d42bb
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="list-of-web-application-firewall-crs-rule-groups-and-rules-offered"></a>Lista de grupos de regras de CR de firewall de aplicação de web e de regras fornecidos

Firewall de aplicação da web de Gateway de aplicação (WAF) protege as aplicações web de vulnerabilidades comuns e exploits. Isto é feito através de regras que são definidas com base nos conjuntos de regras do núcleo OWASP 2.2.9 ou 3.0. Estas regras podem ser desativadas numa base por regra. Este artigo contém as regras atuais e rulesets oferecidos.

As tabelas seguintes são os grupos de regras e as regras que estão disponíveis ao utilizar o Gateway de aplicação com firewall de aplicações web.  Cada tabela representa as regras encontradas no grupo de regras para uma versão de CR específica.

##<a name="owasp30"></a>OWASP_3.0

### <a name="crs910"></a>  <p x-ms-format-detection="none">IP-REPUTAÇÃO DE PEDIDO-910</p>

|ruleId|Descrição|
|---|---|
|910011|Regra 910011|
|910012|Regra 910012|
|910000|O pedido de cliente malicioso conhecido (com base no violações de tráfego anterior).|
|910100|Cliente IP é a partir de uma localização de país de risco elevado.|
|910120|Regra 910120|
|910130|Regra 910130|
|910150|HTTP Blacklist corresponde do IP do motor de pesquisa|
|910160|Correspondência de HTTP Blacklist para spammer IP|
|910170|Correspondência de bloqueados de HTTP para o IP suspeita|
|910180|Correspondência de HTTP Blacklist para harvester IP|
|910013|Regra 910013|
|910014|Regra 910014|
|910015|Regra 910015|
|910016|Regra 910016|
|910017|Regra 910017|
|910018|Regra 910018|

### <a name="crs911"></a> <p x-ms-format-detection="none">PEDIDO 911-MÉTODO DE IMPOSIÇÃO</p>

|ruleId|Descrição|
|---|---|
|911011|Regra 911011|
|911012|Regra 911012|
|911100|Método não é permitido pela política|
|911013|Regra 911013|
|911014|Regra 911014|
|911015|Regra 911015|
|911016|Regra 911016|
|911017|Regra 911017|
|911018|Regra 911018|

### <a name="crs912"></a> <p x-ms-format-detection="none">PEDIDO 912-DOS PROTEÇÃO</p>

|ruleId|Descrição|
|---|---|
|912100|Regra 912100|
|912012|Regra 912012|
|912120|Negação de ataque Service (DoS) identificada a partir das % @{tx.real_ip} (% @{tx.dos_block_counter} pedidos desde o último alerta)|
|912130|Regra 912130|
|912140|Regra 912140|
|912150|Regra 912150|
|912160|Regra 912160|
|912170|Potenciais ataques Denial of Service (DoS) de % @{tx.real_ip} - n. º de pedido Bursts = % @{ip.dos_burst_counter}|
|912013|Regra 912013|
|912014|Regra 912014|
|912019|Regra 912019|
|912171|Potenciais ataques Denial of Service (DoS) de % @{tx.real_ip} - n. º de pedido Bursts = % @{ip.dos_burst_counter}|
|912015|Regra 912015|
|912016|Regra 912016|
|912017|Regra 912017|
|912018|Regra 912018|

### <a name="crs913"></a> <p x-ms-format-detection="none">PEDIDO 913-SCANNER DETEÇÃO</p>

|ruleId|Descrição|
|---|---|
|913011|Regra 913011|
|913012|Regra 913012|
|913100|Foi encontrado o agente de utilizador associado o Verificador de segurança|
|913110|Foi encontrado o cabeçalho do pedido associado com o Verificador de segurança|
|913120|Foi encontrado um pedido. o nome de ficheiro/argumento associado o Verificador de segurança|
|913013|Regra 913013|
|913014|Regra 913014|
|913101|Foi encontrado o agente de utilizador associado com o cliente HTTP de scripting/genérico|
|913102|Foi encontrado o agente de utilizador associado web crawler/bot|
|913015|Regra 913015|
|913016|Regra 913016|
|913017|Regra 913017|
|913018|Regra 913018|

### <a name="crs920"></a> <p x-ms-format-detection="none">PEDIDO 920-PROTOCOLO DE IMPOSIÇÃO</p>

|ruleId|Descrição|
|---|---|
|920011|Regra 920011|
|920012|Regra 920012|
|920100|Linha de pedido de HTTP inválido|
|920130|Falha ao analisar o corpo do pedido.|
|920140|Pedido com várias partes corpo falhou a validação strict = do PE %@{REQBODY_PROCESSOR_ERROR} BQ %@{MULTIPART_BOUNDARY_QUOTED} BW %@{MULTIPART_BOUNDARY_WHITESPACE} DB %@{MULTIPART_DATA_BEFORE} DA %@{MULTIPART_DATA_AFTER} HF %@{MULTIPART_HEADER_FOLDING} LF % @ {MULTIPART_LF_LINE}     SM %@{MULTIPART_SEMICOLON_MISSING} IQ %@{MULTIPART_INVALID_QUOTING} IH %@{MULTIPART_INVALID_HEADER_FOLDING} FLE %@{MULTIPART_FILE_LIMIT_EXCEEDED}|
|920160|Cabeçalho Content-Length de HTTP não é um valor numérico.|
|920170|O pedido GET ou HEAD com conteúdo do corpo.|
|920180|Pedido POST em falta o cabeçalho Content-Length.|
|920190|Intervalo = o último valor de Byte inválido.|
|920210|Múltiplo/em conflito foram encontrados dados de cabeçalho de ligação.|
|920220|Tentativa de ataque de abuso codificação URL|
|920240|Tentativa de ataque de abuso codificação URL|
|920250|UTF8 Tentativa de ataque de abuso codificação|
|920260|Tentativa de ataque do Unicode completo/meio largura abuso|
|920270|Caráter inválido no pedido (carácter de nulo)|
|920280|Falta um cabeçalho de anfitrião de pedido|
|920290|Cabeçalho de anfitrião em branco|
|920310|Tem de pedido vazio aceitar cabeçalho|
|920311|Tem de pedido vazio aceitar cabeçalho|
|920330|Cabeçalho de agente de utilizador vazio|
|920340|Pedido que contém conteúdo mas cabeçalho Content-Type em falta|
|920350|Cabeçalho de anfitrião é um endereço IP numérico|
|920380|Demasiados argumentos no pedido|
|920360|Nome do argumento é demasiado longo|
|920370|Valor do argumento é demasiado longo|
|920390|Tamanho total de argumentos excedido|
|920400|Tamanho do ficheiro carregado demasiado grande|
|920410|Tamanho total de ficheiros carregados demasiado grande|
|920420|Tipo de conteúdo do pedido não é permitido pela política|
|920430|Versão do protocolo HTTP não é permitida pela política|
|920440|Extensão de ficheiro do URL está restringido por política|
|920450|Cabeçalho de HTTP está restringido pela política (%@{MATCHED_VAR})|
|920013|Regra 920013|
|920014|Regra 920014|
|920200|Intervalo = demasiados muitos dos campos (6 ou mais)|
|920201|Intervalo = demasiados muitos dos campos para o pedido de pdf (35 ou mais)|
|920230|Codificação do URL vários detetado|
|920300|Pedido em falta um cabeçalho de aceitar|
|920271|Caráter inválido no pedido (carateres não imprimível)|
|920320|Cabeçalho de agente de utilizador em falta|
|920015|Regra 920015|
|920016|Regra 920016|
|920272|Caráter inválido no pedido (fora imprimível carateres abaixo ascii 127)|
|920017|Regra 920017|
|920018|Regra 920018|
|920202|Intervalo = demasiados muitos dos campos para o pedido de pdf (6 ou mais)|
|920273|Caráter inválido no pedido (fora muito restrito conjunto)|
|920274|Caráter inválido nos cabeçalhos de pedido (fora muito restrito conjunto)|
|920460|Regra 920460|

### <a name="crs921"></a> <p x-ms-format-detection="none">PEDIDO 921-PROTOCOLO DE ATAQUE</p>

|ruleId|Descrição|
|---|---|
|921011|Regra 921011|
|921012|Regra 921012|
|921100|Pedido de HTTP Smuggling ataque.|
|921110|Ataque de Smuggling de pedido de HTTP|
|921120|Ataque de divisão de resposta de HTTP|
|921130|Ataque de divisão de resposta de HTTP|
|921140|Ataque de Injeção de cabeçalho HTTP através de cabeçalhos|
|921150|Ataque de Injeção de cabeçalho HTTP através do payload (CR/LF detetado)|
|921160|Ataque de Injeção de cabeçalho HTTP através do payload (CR/LF e nome de cabeçalho detetado)|
|921013|Regra 921013|
|921014|Regra 921014|
|921151|Ataque de Injeção de cabeçalho HTTP através do payload (CR/LF detetado)|
|921015|Regra 921015|
|921016|Regra 921016|
|921170|Regra 921170|
|921180|Pollution de parâmetro de HTTP (% @{TX.1})|
|921017|Regra 921017|
|921018|Regra 921018|

### <a name="crs930"></a> <p x-ms-format-detection="none">PEDIDO-930-APLICAÇÃO-ATAQUE-LFI</p>

|ruleId|Descrição|
|---|---|
|930011|Regra 930011|
|930012|Regra 930012|
|930100|Caminho transversal ataque (/.. /)|
|930110|Caminho transversal ataque (/.. /)|
|930120|Tentativa de acesso do ficheiro do SO|
|930130|Tentativa de acesso de ficheiro restritas|
|930013|Regra 930013|
|930014|Regra 930014|
|930015|Regra 930015|
|930016|Regra 930016|
|930017|Regra 930017|
|930018|Regra 930018|

### <a name="crs931"></a> <p x-ms-format-detection="none">PEDIDO-931-APLICAÇÃO-ATAQUE-RFI</p>

|ruleId|Descrição|
|---|---|
|931011|Regra 931011|
|931012|Regra 931012|
|931100|Possíveis ataques de inclusão de ficheiros remoto (RFI) = utilizando o endereço IP de parâmetro de URL|
|931110|Possíveis ataques de inclusão de ficheiros remoto (RFI) = RFI vulnerável parâmetro nome comum utilizado w/URL Payload|
|931120|Ataque de inclusão (RFI) de ficheiros remoto possíveis = URL Payload utilizado à direita w/pergunta marca caráter (?)|
|931013|Regra 931013|
|931014|Regra 931014|
|931130|Ataque de inclusão (RFI) de ficheiros remoto possíveis = fora do domínio/ligação de referência|
|931015|Regra 931015|
|931016|Regra 931016|
|931017|Regra 931017|
|931018|Regra 931018|

### <a name="crs932"></a> <p x-ms-format-detection="none">PEDIDO-932-APLICAÇÃO-ATAQUE-RCE</p>

|ruleId|Descrição|
|---|---|
|932011|Regra 932011|
|932012|Regra 932012|
|932120|Execução do comando remoto = encontrado de comando do Windows PowerShell|
|932130|Execução do comando remoto = expressão da Shell de Unix encontrado|
|932140|Execução do comando remoto = Windows para / se o comando foi encontrado|
|932160|Execução do comando remoto = o código de Shell de Unix encontrado|
|932170|Execução do comando remoto = Shellshock (Exposures-2014-6271)|
|932171|Execução do comando remoto = Shellshock (Exposures-2014-6271)|
|932013|Regra 932013|
|932014|Regra 932014|
|932015|Regra 932015|
|932016|Regra 932016|
|932017|Regra 932017|
|932018|Regra 932018|

### <a name="crs933"></a> <p x-ms-format-detection="none">PEDIDO-933-APLICAÇÃO-ATAQUE-PHP</p>

|ruleId|Descrição|
|---|---|
|933011|Regra 933011|
|933012|Regra 933012|
|933100|Ataque de Injeção de PHP = foi encontrada uma Tag de abertura/fecho|
|933110|Ataque de Injeção de PHP = PHP carregamento de ficheiros de Script encontrado|
|933120|Ataque de Injeção de PHP = diretiva de configuração encontrada|
|933130|Ataque de Injeção de PHP = encontradas variáveis|
|933150|Ataque de Injeção de PHP = nome de função de alto risco PHP encontrado|
|933160|Ataque de Injeção de PHP = chamada de função de alto risco PHP encontrada|
|933180|Ataque de Injeção de PHP = chamada de função variável encontrada|
|933013|Regra 933013|
|933014|Regra 933014|
|933151|Ataque de Injeção de PHP = nome de função de risco média PHP encontrado|
|933015|Regra 933015|
|933016|Regra 933016|
|933131|Ataque de Injeção de PHP = encontradas variáveis|
|933161|Ataque de Injeção de PHP = chamada de função de valor baixo PHP encontrada|
|933111|Ataque de Injeção de PHP = PHP carregamento de ficheiros de Script encontrado|
|933017|Regra 933017|
|933018|Regra 933018|

### <a name="crs941"></a> <p x-ms-format-detection="none">PEDIDO-941-APLICAÇÃO-ATAQUE-XSS</p>

|ruleId|Descrição|
|---|---|
|941011|Regra 941011|
|941012|Regra 941012|
|941100|XSS ataque detetado através de libinjection|
|941110|Filtro XSS - categoria 1 = Vetor de Tag de Script|
|941130|Filtro XSS - categoria 3 = Vetor de atributo|
|941140|Filtro XSS - categoria 4 = Vetor de URI de Javascript|
|941150|Filtro XSS - categoria 5 = os atributos HTML não permitido|
|941180|Palavras-chave do nó validador Blacklist|
|941190|Filtros de i/e XSS - ataque detetado.|
|941200|Filtros de i/e XSS - ataque detetado.|
|941210|Filtros de i/e XSS - ataque detetado.|
|941220|Filtros de i/e XSS - ataque detetado.|
|941230|Filtros de i/e XSS - ataque detetado.|
|941240|Filtros de i/e XSS - ataque detetado.|
|941260|Filtros de i/e XSS - ataque detetado.|
|941270|Filtros de i/e XSS - ataque detetado.|
|941280|Filtros de i/e XSS - ataque detetado.|
|941290|Filtros de i/e XSS - ataque detetado.|
|941300|Filtros de i/e XSS - ataque detetado.|
|941310|US-ASCII com formato incorreto codificação XSS filtro - ataque detetado.|
|941350|UTF-7 codificação IE XSS - ataque detetado.|
|941013|Regra 941013|
|941014|Regra 941014|
|941320|Ataque possíveis XSS detetado - processador de HTML Tag|
|941015|Regra 941015|
|941016|Regra 941016|
|941017|Regra 941017|
|941018|Regra 941018|

### <a name="crs942"></a> <p x-ms-format-detection="none">PEDIDO-942-APLICAÇÃO-ATAQUE-SQLI</p>

|ruleId|Descrição|
|---|---|
|942011|Regra 942011|
|942012|Regra 942012|
|942100|SQL detetado de ataque de Injeção através de libinjection|
|942140|Ataque de Injeção de SQL = nomes comuns da base de dados detetados|
|942160|Deteta os testes de sqli oculta utilizando sleep() ou benchmark().|
|942170|Deteta tentativas de injeção de SQL benchmark e modo de suspensão, incluindo consultas condicionais|
|942230|Deteta tentativas de injeção de SQL condicionais|
|942270|Está à procura de injeção de sql básica. Cadeia de ataque comuns para mysql oracle e outros.|
|942290|Localiza tentativas de injeção de SQL básica do MongoDB|
|942320|Deteta o MySQL e PostgreSQL armazenados injections procedimento/função|
|942350|Deteta a inserção de MySQL UDF e outra manipulação de dados/estrutura tenta|
|942013|Regra 942013|
|942014|Regra 942014|
|942150|Ataque de Injeção de SQL|
|942410|Ataque de Injeção de SQL|
|942440|Sequência de comentário do SQL Server detetada.|
|942450|SQL Server hexadecimais codificação identificado|
|942015|Regra 942015|
|942016|Regra 942016|
|942251|Deteta HAVING injections|
|942460|Alerta de deteção de anomalias meta-caráter - repetitivas não Word carateres|
|942017|Regra 942017|
|942018|Regra 942018|

### <a name="crs943"></a> <p x-ms-format-detection="none">REQUEST-943-APPLICATION-ATTACK-SESSION-FIXATION</p>

|ruleId|Descrição|
|---|---|
|943011|Regra 943011|
|943012|Regra 943012|
|943100|Sessão possíveis Fixation ataque = definir valores de Cookie em HTML|
|943110|Sessão possíveis Fixation ataque = o nome do parâmetro de SessionID com referência fora do domínio|
|943120|Sessão possíveis Fixation ataque = o nome do parâmetro de SessionID com nenhuma referência|
|943013|Regra 943013|
|943014|Regra 943014|
|943015|Regra 943015|
|943016|Regra 943016|
|943017|Regra 943017|
|943018|Regra 943018|

##<a name="owasp229"></a>OWASP_2.2.9

### <a name="crs20"></a>crs_20_protocol_violations

|ruleId|Descrição|
|---|---|
|960911|Linha de pedido de HTTP inválido|
|981227|Erro de Apache = URI inválido no pedido.|
|960912|Falha ao analisar o corpo do pedido.|
|960914|Pedido com várias partes corpo falhou a validação strict = do PE %@{REQBODY_PROCESSOR_ERROR} BQ %@{MULTIPART_BOUNDARY_QUOTED} BW %@{MULTIPART_BOUNDARY_WHITESPACE} DB %@{MULTIPART_DATA_BEFORE} DA %@{MULTIPART_DATA_AFTER} HF %@{MULTIPART_HEADER_FOLDING} LF % @ {MULTIPART_LF_LINE}     SM %@{MULTIPART_SEMICOLON_MISSING} IQ %@{MULTIPART_INVALID_QUOTING} IH %@{MULTIPART_INVALID_HEADER_FOLDING} FLE %@{MULTIPART_FILE_LIMIT_EXCEEDED}|
|960915|Com várias partes parser detetou um limite de sem correspondência possíveis.|
|960016|Cabeçalho Content-Length de HTTP não é um valor numérico.|
|960011|O pedido GET ou HEAD com conteúdo do corpo.|
|960012|Pedido POST em falta o cabeçalho Content-Length.|
|960902|Utilização inválida de codificação de identidade.|
|960022|Espere cabeçalho não é permitido para HTTP 1.0.|
|960020|Cabeçalho de pragma necessita de Cache-Control Header para pedidos HTTP/1.1.|
|958291|Intervalo = campo existe e que começa com 0.|
|958230|Intervalo = o último valor de Byte inválido.|
|958295|Múltiplo/em conflito foram encontrados dados de cabeçalho de ligação.|
|950107|Tentativa de ataque de abuso codificação URL|
|950109|Codificação do URL vários detetado|
|950108|Tentativa de ataque de abuso codificação URL|
|950801|UTF8 Tentativa de ataque de abuso codificação|
|950116|Tentativa de ataque do Unicode completo/meio largura abuso|
|960901|Caráter inválido no pedido|
|960018|Caráter inválido no pedido|

### <a name="crs21"></a>crs_21_protocol_anomalies

|ruleId|Descrição|
|---|---|
|960008|Falta um cabeçalho de anfitrião de pedido|
|960007|Cabeçalho de anfitrião em branco|
|960015|Pedido em falta um cabeçalho de aceitar|
|960021|Tem de pedido vazio aceitar cabeçalho|
|960009|Pedido em falta um cabeçalho de agente do utilizador|
|960006|Cabeçalho de agente de utilizador vazio|
|960904|Pedido que contém conteúdo mas cabeçalho Content-Type em falta|
|960017|Cabeçalho de anfitrião é um endereço IP numérico|

### <a name="crs23"></a>crs_23_request_limits

|ruleId|Descrição|
|---|---|
|960209|Nome do argumento é demasiado longo|
|960208|Valor do argumento é demasiado longo|
|960335|Demasiados argumentos no pedido|
|960341|Tamanho total de argumentos excedido|
|960342|Tamanho do ficheiro carregado demasiado grande|
|960343|Tamanho total de ficheiros carregados demasiado grande|

### <a name="crs30"></a>crs_30_http_policy

|ruleId|Descrição|
|---|---|
|960032|Método não é permitido pela política|
|960010|Tipo de conteúdo do pedido não é permitido pela política|
|960034|Versão do protocolo HTTP não é permitida pela política|
|960035|Extensão de ficheiro do URL está restringido por política|
|960038|Cabeçalho de HTTP está restringido por política|

### <a name="crs35"></a>crs_35_bad_robots

|ruleId|Descrição|
|---|---|
|990002|Pedido indica que uma analista de segurança analisados o Site|
|990901|Pedido indica que uma analista de segurança analisados o Site|
|990902|Pedido indica que uma analista de segurança analisados o Site|
|990012|Crawler de web site Rogue|

### <a name="crs40"></a>crs_40_generic_attacks

|ruleId|Descrição|
|---|---|
|960024|Alerta de deteção de anomalias meta-caráter - repetitivas não Word carateres|
|950008|Inserção não documentado ColdFusion etiquetas|
|950010|Ataque de Injeção LDAP|
|950011|Ataque de injeção de SSI|
|950018|URL de universal PDF XSS detetado.|
|950019|Ataque de Injeção de e-mail|
|950012|Pedido de HTTP Smuggling ataque.|
|950910|Ataque de divisão de resposta de HTTP|
|950911|Ataque de divisão de resposta de HTTP|
|950117|Ataque de inclusão de ficheiros remoto|
|950118|Ataque de inclusão de ficheiros remoto|
|950119|Ataque de inclusão de ficheiros remoto|
|950120|Ataque de inclusão (RFI) de ficheiros remoto possíveis = fora do domínio/ligação de referência|
|981133|Regra 981133|
|981134|Regra 981134|
|950009|Ataque de Fixation de sessão|
|950003|Fixation de sessão|
|950000|Fixation de sessão|
|950005|Tentativa de acesso remoto de ficheiros|
|950002|Acesso de comando de sistema|
|950006|Inserção de comando do sistema|
|959151|Ataque de Injeção de PHP|
|958976|Ataque de Injeção de PHP|
|958977|Ataque de Injeção de PHP|

### <a name="crs41sql"></a>crs_41_sql_injection_attacks

|ruleId|Descrição|
|---|---|
|981231|Sequência de comentário do SQL Server detetada.|
|981260|SQL Server hexadecimais codificação identificado|
|981320|Ataque de Injeção de SQL = nomes comuns da base de dados detetados|
|981300|Regra 981300|
|981301|Regra 981301|
|981302|Regra 981302|
|981303|Regra 981303|
|981304|Regra 981304|
|981305|Regra 981305|
|981306|Regra 981306|
|981307|Regra 981307|
|981308|Regra 981308|
|981309|Regra 981309|
|981310|Regra 981310|
|981311|Regra 981311|
|981312|Regra 981312|
|981313|Regra 981313|
|981314|Regra 981314|
|981315|Regra 981315|
|981316|Regra 981316|
|981317|Alerta de deteção de anomalias de instrução SELECT SQL|
|950007|Ataque de Injeção de SQL oculta|
|950001|Ataque de Injeção de SQL|
|950908|Ataque de Injeção de SQL.|
|959073|Ataque de Injeção de SQL|
|981272|Deteta os testes de sqli oculta utilizando sleep() ou benchmark().|
|981250|Deteta tentativas de injeção de SQL benchmark e modo de suspensão, incluindo consultas condicionais|
|981241|Deteta tentativas de injeção de SQL condicionais|
|981276|Está à procura de injeção de sql básica. Cadeia de ataque comuns para mysql oracle e outros.|
|981270|Localiza tentativas de injeção de SQL básica do MongoDB|
|981253|Deteta o MySQL e PostgreSQL armazenados injections procedimento/função|
|981251|Deteta a inserção de MySQL UDF e outra manipulação de dados/estrutura tenta|

### <a name="crs41xss"></a>crs_41_xss_attacks

|ruleId|Descrição|
|---|---|
|973336|Filtro XSS - categoria 1 = Vetor de Tag de Script|
|973338|Filtro XSS - categoria 3 = Vetor de URI de Javascript|
|981136|Regra 981136|
|981018|Regra 981018|
|958016|Ataque de processamento de scripts entre sites (XSS)|
|958414|Ataque de processamento de scripts entre sites (XSS)|
|958032|Ataque de processamento de scripts entre sites (XSS)|
|958026|Ataque de processamento de scripts entre sites (XSS)|
|958027|Ataque de processamento de scripts entre sites (XSS)|
|958054|Ataque de processamento de scripts entre sites (XSS)|
|958418|Ataque de processamento de scripts entre sites (XSS)|
|958034|Ataque de processamento de scripts entre sites (XSS)|
|958019|Ataque de processamento de scripts entre sites (XSS)|
|958013|Ataque de processamento de scripts entre sites (XSS)|
|958408|Ataque de processamento de scripts entre sites (XSS)|
|958012|Ataque de processamento de scripts entre sites (XSS)|
|958423|Ataque de processamento de scripts entre sites (XSS)|
|958002|Ataque de processamento de scripts entre sites (XSS)|
|958017|Ataque de processamento de scripts entre sites (XSS)|
|958007|Ataque de processamento de scripts entre sites (XSS)|
|958047|Ataque de processamento de scripts entre sites (XSS)|
|958410|Ataque de processamento de scripts entre sites (XSS)|
|958415|Ataque de processamento de scripts entre sites (XSS)|
|958022|Ataque de processamento de scripts entre sites (XSS)|
|958405|Ataque de processamento de scripts entre sites (XSS)|
|958419|Ataque de processamento de scripts entre sites (XSS)|
|958028|Ataque de processamento de scripts entre sites (XSS)|
|958057|Ataque de processamento de scripts entre sites (XSS)|
|958031|Ataque de processamento de scripts entre sites (XSS)|
|958006|Ataque de processamento de scripts entre sites (XSS)|
|958033|Ataque de processamento de scripts entre sites (XSS)|
|958038|Ataque de processamento de scripts entre sites (XSS)|
|958409|Ataque de processamento de scripts entre sites (XSS)|
|958001|Ataque de processamento de scripts entre sites (XSS)|
|958005|Ataque de processamento de scripts entre sites (XSS)|
|958404|Ataque de processamento de scripts entre sites (XSS)|
|958023|Ataque de processamento de scripts entre sites (XSS)|
|958010|Ataque de processamento de scripts entre sites (XSS)|
|958411|Ataque de processamento de scripts entre sites (XSS)|
|958422|Ataque de processamento de scripts entre sites (XSS)|
|958036|Ataque de processamento de scripts entre sites (XSS)|
|958000|Ataque de processamento de scripts entre sites (XSS)|
|958018|Ataque de processamento de scripts entre sites (XSS)|
|958406|Ataque de processamento de scripts entre sites (XSS)|
|958040|Ataque de processamento de scripts entre sites (XSS)|
|958052|Ataque de processamento de scripts entre sites (XSS)|
|958037|Ataque de processamento de scripts entre sites (XSS)|
|958049|Ataque de processamento de scripts entre sites (XSS)|
|958030|Ataque de processamento de scripts entre sites (XSS)|
|958041|Ataque de processamento de scripts entre sites (XSS)|
|958416|Ataque de processamento de scripts entre sites (XSS)|
|958024|Ataque de processamento de scripts entre sites (XSS)|
|958059|Ataque de processamento de scripts entre sites (XSS)|
|958417|Ataque de processamento de scripts entre sites (XSS)|
|958020|Ataque de processamento de scripts entre sites (XSS)|
|958045|Ataque de processamento de scripts entre sites (XSS)|
|958004|Ataque de processamento de scripts entre sites (XSS)|
|958421|Ataque de processamento de scripts entre sites (XSS)|
|958009|Ataque de processamento de scripts entre sites (XSS)|
|958025|Ataque de processamento de scripts entre sites (XSS)|
|958413|Ataque de processamento de scripts entre sites (XSS)|
|958051|Ataque de processamento de scripts entre sites (XSS)|
|958420|Ataque de processamento de scripts entre sites (XSS)|
|958407|Ataque de processamento de scripts entre sites (XSS)|
|958056|Ataque de processamento de scripts entre sites (XSS)|
|958011|Ataque de processamento de scripts entre sites (XSS)|
|958412|Ataque de processamento de scripts entre sites (XSS)|
|958008|Ataque de processamento de scripts entre sites (XSS)|
|958046|Ataque de processamento de scripts entre sites (XSS)|
|958039|Ataque de processamento de scripts entre sites (XSS)|
|958003|Ataque de processamento de scripts entre sites (XSS)|
|973300|Ataque possíveis XSS detetado - processador de HTML Tag|
|973301|Ataque XSS detetado|
|973302|Ataque XSS detetado|
|973303|Ataque XSS detetado|
|973304|Ataque XSS detetado|
|973305|Ataque XSS detetado|
|973306|Ataque XSS detetado|
|973307|Ataque XSS detetado|
|973308|Ataque XSS detetado|
|973309|Ataque XSS detetado|
|973311|Ataque XSS detetado|
|973313|Ataque XSS detetado|
|973314|Ataque XSS detetado|
|973331|Filtros de i/e XSS - ataque detetado.|
|973315|Filtros de i/e XSS - ataque detetado.|
|973330|Filtros de i/e XSS - ataque detetado.|
|973327|Filtros de i/e XSS - ataque detetado.|
|973326|Filtros de i/e XSS - ataque detetado.|
|973346|Filtros de i/e XSS - ataque detetado.|
|973345|Filtros de i/e XSS - ataque detetado.|
|973324|Filtros de i/e XSS - ataque detetado.|
|973323|Filtros de i/e XSS - ataque detetado.|
|973348|Filtros de i/e XSS - ataque detetado.|
|973321|Filtros de i/e XSS - ataque detetado.|
|973320|Filtros de i/e XSS - ataque detetado.|
|973318|Filtros de i/e XSS - ataque detetado.|
|973317|Filtros de i/e XSS - ataque detetado.|
|973329|Filtros de i/e XSS - ataque detetado.|
|973328|Filtros de i/e XSS - ataque detetado.|

### <a name="crs42"></a>crs_42_tight_security

|ruleId|Descrição|
|---|---|
|950103|Caminho transversal ataque|

### <a name="crs45"></a>crs_45_trojans

|ruleId|Descrição|
|---|---|
|950110|Acesso Backdoor|
|950921|Acesso Backdoor|
|950922|Acesso Backdoor|

## <a name="next-steps"></a>Passos seguintes

Saiba como desativar regras WAF, visitando: [regras personalizar WAF](application-gateway-customize-waf-rules-portal.md)

[1]: ./media/application-gateway-integration-security-center/figure1.png
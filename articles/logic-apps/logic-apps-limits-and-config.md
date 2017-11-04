---
title: "Limites e de configuração - Azure Logic Apps | Microsoft Docs"
description: "Os limites de serviços e valores de configuração para o Azure Logic Apps"
services: logic-apps
documentationcenter: .net,nodejs,java
author: jeffhollan
manager: anneta
editor: 
ms.assetid: 75b52eeb-23a7-47dd-a42f-1351c6dfebdc
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: LADocs; jehollan
ms.openlocfilehash: 4babb3033e75edc5c85ce89dac569b9f2beae9f7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="logic-apps-limits-and-configuration"></a>Configuração e os limites de aplicações lógicas

Este tópico descreve os limites atuais e detalhes de configuração para o Azure Logic Apps.

## <a name="limits"></a>Limites

### <a name="http-request-limits"></a>Limites de pedidos HTTP

Estes limites aplicam-se um único pedido HTTP ou uma chamada de conector.

#### <a name="timeout"></a>Tempo limite

| Nome | Limite | Notas | 
| ---- | ----- | ----- | 
| Tempo limite do pedido | por 120 segundos | Um [padrão assíncrono](../logic-apps/logic-apps-create-api-app.md) ou [até ciclo](logic-apps-loops-and-scopes.md) pode compensar conforme necessário |
|||| 

#### <a name="message-size"></a>Tamanho da mensagem

| Nome | Limite | Notas | 
| ---- | ----- | ----- | 
| Tamanho da mensagem | 100 MB | Algumas APIs e conectores não suportem 100 MB. | 
| Limite de avaliação da expressão | 131,072 carateres | `@concat()`, `@base64()`, `string` não pode ser superior a este limite. | 
|||| 

#### <a name="retry-policy"></a>Política de repetição

| Nome | Limite | Notas | 
| ---- | ----- | ----- | 
| Tentativas de repetição | 90 | A predefinição é 4. Pode configurar com a [Repita parâmetro política](../logic-apps/logic-apps-workflow-actions-triggers.md). | 
| Intervalo máximo de repetição | 1 dia | Pode configurar com a [Repita parâmetro política](../logic-apps/logic-apps-workflow-actions-triggers.md). | 
| Mín. intervalo de repetição | seg 5 | Pode configurar com a [Repita parâmetro política](../logic-apps/logic-apps-workflow-actions-triggers.md). |
|||| 

### <a name="run-duration-and-retention"></a>Duração de execução e retenção

Estes limites aplicam-se para uma aplicação lógica única de executar.

| Nome | Limite | 
| ---- | ----- | 
| Duração de execução | 90 dias | 
| Retenção de armazenamento | hora de início de 90 dias da execução | 
| Intervalo de periodicidade mín. | 1 segundo </br>Para aplicações lógicas com um plano do App Service: 15 segundos | 
| Intervalo de periodicidade máximo | dias de 500 | 
||| 

Exceder os limites para a duração de execução ou de retenção de armazenamento no seu fluxo de processamento normal, [contacte-nos](mailto://logicappsemail@microsoft.com) , de modo a que o possamos ajudar com as suas necessidades.

### <a name="looping-and-debatching-limits"></a>Criar ciclos e debatching limites

Estes limites aplicam-se para uma aplicação lógica única de executar.

| Nome | Limite | Notas | 
| ---- | ----- | ----- | 
| ForEach itens | 100,000 | Pode utilizar o [ação de consulta](../connectors/connectors-native-query.md) para filtrar matrizes maior, conforme necessário. | 
| Até iterações | 5,000 | | 
| Itens de SplitOn | 100,000 | | 
| ForEach paralelismo | 50 | A predefinição é 20. <p>Para definir um nível específico de paralelismo num ciclo ForEach, defina o `runtimeConfiguration` propriedade no `foreach` ação. <p>Para executar sequencialmente um ciclo de ForEach, defina o `operationOptions` propriedade como "Sequencial" no `foreach` ação. | 
|||| 

### <a name="throughput-limits"></a>Limites de débito

Estes limites aplicam-se a uma instância de aplicação lógica única.

| Nome | Limite | Notas | 
| ---- | ----- | ----- | 
| Execuções de ações por 5 minutos | 100,000 | Pode distribuir a carga de trabalho por várias aplicações conforme necessário. | 
| Chamadas de saída em simultâneo de ações | ~2,500 | Reduzir o número de pedidos simultâneos ou reduzir a duração, conforme necessário. | 
| Ponto final de Runtime: entradas de chamadas em simultâneo | ~1,000 | Reduzir o número de pedidos simultâneos ou reduzir a duração, conforme necessário. | 
| Ponto final de Runtime: ler chamadas por 5 minutos | 60,000 | Pode distribuir a carga de trabalho por várias aplicações conforme necessário. | 
| Ponto final de Runtime: invocar chamadas por 5 minutos | 45,000 | Pode distribuir a carga de trabalho por várias aplicações conforme necessário. | 
|||| 

Exceda estes limites de processamento normal ou teste de carga de execução que poderão exceder estes limites [contacte-nos](mailto://logicappsemail@microsoft.com) , de modo a que o possamos ajudar com as suas necessidades.

### <a name="logic-app-definition-limits"></a>Limites de definição de aplicação lógica

Estes limites aplicam-se a uma definição de aplicação lógica única.

| Nome | Limite | Notas | 
| ---- | ----- | ----- | 
| Ações por fluxo de trabalho | 500 | Para expandir este limite, pode adicionar fluxos de trabalho aninhados conforme necessário. |
| Profundidade de aninhamento de ação de permissão | 8 | Para expandir este limite, pode adicionar fluxos de trabalho aninhados conforme necessário. | 
| Fluxos de trabalho por região por subscrição | 1000 | | 
| Acionadores por fluxo de trabalho | 10 | | 
| Limite de casos de âmbito do comutador | 25 | | 
| Número de variáveis por fluxo de trabalho | 250 | | 
| Carateres máx. por expressão | 8,192 | | 
| Máx. `trackedProperties` tamanho em carateres | 16,000 | 
| `action`/`trigger`limite de nome | 80 | | 
| `description`limite de comprimento | 256 | | 
| `parameters`limite | 50 | | 
| `outputs`limite | 10 | | 
|||| 

<a name="custom-connector-limits"></a>

### <a name="custom-connector-limits"></a>Limites de conetor personalizado

Aplicam estes limites conectores personalizado que pode criar a partir de web APIs.

| Nome | Limite | 
| ---- | ----- | 
| Vários conectores personalizados que pode criar | 1000 por subscrição do Azure | 
| Número de pedidos por minuto para cada ligação criada por um conetor personalizado | 500 pedidos para cada ligação criada pelo conector do |
||| 

### <a name="integration-account-limits"></a>Limites de conta de integração

Estes limites se aplicam a artefactos que pode adicionar a uma conta de integração.

| Nome | Limite | Notas | 
| ---- | ----- | ----- | 
| Esquema | 8 MB | Pode utilizar [URI de blob](../logic-apps/logic-apps-enterprise-integration-schemas.md) para carregar ficheiros maiores do que 2 MB. | 
| Mapa (ficheiro XSLT) | 2 MB | | 
| Ponto final de Runtime: ler chamadas por 5 minutos | 60,000 | Pode distribuir a carga de trabalho em várias contas, conforme necessário. | 
| Ponto final de Runtime: invocar chamadas por 5 minutos | 45,000 | Pode distribuir a carga de trabalho em várias contas, conforme necessário. | 
| Ponto final de Runtime: controlo de chamadas por 5 minutos | 45,000 | Pode distribuir a carga de trabalho em várias contas, conforme necessário. | 
| Ponto final de Runtime: bloquear chamadas simultâneas | ~1,000 | Reduzir o número de pedidos simultâneos ou reduzir a duração, conforme necessário. | 
|||| 

Estes limites aplicam-se ao número de artefactos que pode adicionar a uma conta de integração.

#### <a name="free-pricing-tier"></a>Escalão de preço livres

| Nome | Limite | Notas | 
| ---- | ----- | ----- | 
| Contratos | 10 | | 
| Outros tipos de artefactos | 25 |Os tipos de artefacto incluem parceiros, esquemas, certificados e mapas. Cada tipo pode ter cópias de segurança para o número máximo de artefactos. | 
|||| 

#### <a name="standard-pricing-tier"></a>Escalão de preço padrão

| Nome | Limite | Notas | 
| ---- | ----- | ----- | 
| Qualquer tipo de artefactos | 500 | Os tipos de artefacto incluem contratos, parceiros, esquemas, certificados e mapas. Cada tipo pode ter cópias de segurança para o número máximo de artefactos. | 
|||| 

### <a name="b2b-protocols-as2-x12-edifact-message-size"></a>Tamanho da mensagem de protocolos de B2B (AS2, X12, EDIFACT)

Estes limites aplicam-se para protocolos de B2B.

| Nome | Limite | Notas | 
| ---- | ----- | ----- | 
| AS2 | 50 MB | Aplica-se ao descodificar e codificar | 
| X12 | 50 MB | Aplica-se ao descodificar e codificar | 
| EDIFACT | 50 MB | Aplica-se ao descodificar e codificar | 
|||| 

<a name="configuration"></a>

## <a name="configuration-ip-addresses"></a>Configuração: Endereços IP

### <a name="logic-apps-service"></a>Serviço de aplicações lógicas

As chamadas de uma aplicação lógica diretamente que faz com que, ou seja, através de [HTTP](../connectors/connectors-native-http.md) ou [HTTP + Swagger](../connectors/connectors-native-http-swagger.md) ou outros pedidos HTTP, provenientes de endereços IP nesta lista.

|Região do Logic Apps|IP de saída|
|-----------------|-----------|
|Leste da Austrália|13.75.149.4, 104.210.91.55, 104.210.90.241|
|Sudeste da Austrália|13.73.114.207, 13.77.3.139, 13.70.159.205|
|Sul do Brasil|191.235.82.221, 191.235.91.7, 191.234.182.26|
|Canadá Central|52.233.29.92, 52.228.39.241, 52.228.39.244|
|Leste do Canadá|52.232.128.155, 52.229.120.45, 52.229.126.25|
|Índia Central|52.172.154.168, 52.172.186.159, 52.172.185.79|
|EUA Central|13.67.236.125, 104.208.25.27, 40.122.170.198|
|Ásia Oriental|13.75.94.173, 40.83.127.19, 52.175.33.254|
|EUA Leste|13.92.98.111, 40.121.91.41, 40.114.82.191|
|E.U.A. Leste 2|40.84.30.147, 104.208.155.200, 104.208.158.174|
|Leste do Japão|13.71.158.3, 13.73.4.207, 13.71.158.120|
|Oeste do Japão|40.74.140.4, 104.214.137.243, 138.91.26.45|
|EUA Centro-Norte|168.62.248.37, 157.55.210.61, 157.55.212.238|
|Europa do Norte|40.113.12.95, 52.178.165.215, 52.178.166.21|
|EUA Centro-Sul|104.210.144.48, 13.65.82.17, 13.66.52.232|
|Sudeste Asiático|13.76.133.155, 52.163.228.93, 52.163.230.166|
|Sul da Índia|52.172.50.24, 52.172.55.231, 52.172.52.0|
|EUA Centro-Oeste|52.161.27.190, 52.161.18.218, 52.161.9.108|
|Europa Ocidental|40.68.222.65, 40.68.209.23, 13.95.147.65|
|Índia Ocidental|104.211.164.80, 104.211.162.205, 104.211.164.136|
|EUA Oeste|52.160.92.112, 40.118.244.241, 40.118.241.243|
|Oeste dos E.U.A 2|13.66.210.167, 52.183.30.169, 52.183.29.132|
|Reino Unido Sul|51.140.74.14, 51.140.73.85, 51.140.78.44|
|Reino Unido Oeste|51.141.54.185, 51.141.45.238, 51.141.47.136|
| | |

### <a name="connectors"></a>Conectores

As chamadas que [conectores](../connectors/apis-list.md) tornar provenientes de endereços IP nesta lista.

|Região do Logic Apps|IP de saída|
|-----------------|-----------|
|Leste da Austrália|40.126.251.213|
|Sudeste da Austrália|40.127.80.34|
|Sul do Brasil|191.232.38.129|
|Canadá Central|52.233.31.197, 52.228.42.205, 52.228.33.76, 52.228.34.13|
|Leste do Canadá|52.229.123.98, 52.229.120.178, 52.229.126.202, 52.229.120.52|
|Índia Central|104.211.98.164|
|EUA Central|40.122.49.51|
|Ásia Oriental|23.99.116.181|
|EUA Leste|191.237.41.52|
|E.U.A. Leste 2|104.208.233.100|
|Leste do Japão|40.115.186.96|
|Oeste do Japão|40.74.130.77|
|EUA Centro-Norte|65.52.218.230|
|Europa do Norte|104.45.93.9|
|EUA Centro-Sul|104.214.70.191|
|Sudeste Asiático|13.76.231.68|
|Sul da Índia|104.211.227.225|
|Europa Ocidental|40.115.50.13|
|Índia Ocidental|104.211.161.203|
|EUA Oeste|104.40.51.248|
|Reino Unido Sul|51.140.80.51|
|Reino Unido Oeste|51.141.47.105|
| | | 

## <a name="next-steps"></a>Passos seguintes  

* [Criar a sua primeira aplicação lógica](../logic-apps/logic-apps-create-a-logic-app.md)  
* [Exemplos e cenários comuns](../logic-apps/logic-apps-examples-and-scenarios.md)
* [Vídeo: Automatizar os processos de negócios com Logic Apps](http://channel9.msdn.com/Events/Build/2016/T694) 
* [Vídeo: Integrar os seus sistemas com Logic Apps](http://channel9.msdn.com/Events/Build/2016/P462)

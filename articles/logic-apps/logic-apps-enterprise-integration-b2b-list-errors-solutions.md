---
title: "Lista de aplicações B2B de lógica de erros e soluções: App Service do Azure | Microsoft Docs"
description: "Lista de aplicações B2B de lógica de erros e soluções"
services: logic-apps
documentationcenter: .net,nodejs,java
author: padmavc
manager: anneta
editor: 
ms.assetid: cf44af18-1fe5-41d5-9e06-cc57a968207c
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/02/2017
ms.author: LADocs; padmavc
ms.openlocfilehash: 1865d75f1b4c2aa18d5a3130f639572d19563b3e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="logic-apps-b2b-list-of-errors-and-solutions"></a>Lista de aplicações B2B de lógica de erros e soluções  
Este artigo ajuda-o a resolver erros que poderão acontecer em Logic Apps B2B cenários e sugere as ações adequadas para corrigir os erros.


## <a name="agreement-resolution"></a>Resolução de contrato

### <a name="no-agreement-found"></a>* Qualquer contrato encontrado 

|   |   |  
|---|---|
| Descrição de erro | Nenhum contrato encontrado com parâmetros de resolução de contrato|    
| Ação do utilizador | O contrato deve ser adicionado à conta de integração com identidades de negócio definido.</br> As identidades de negócio devem corresponder aos ids de mensagem de entrada|  
|   |   |

### <a name="-no-agreement-found-with-identities"></a>* Qualquer contrato encontrado com identidades

|   |   | 
|---|---|
| Descrição de erro | Nenhum contrato encontrado com identidades: 'AS2Identity':: 'Partner1' e 'AS2Identity':: 'Partner3'| 
| Ação do utilizador | AS2 inválido-do ou AS2-a configuradas para o contrato. </br> Mensagem de AS2 correta AS2-do ou AS2-cabeçalhos ou contrato de correspondentes aos ids AS2 no AS2 cabeçalhos com configurações de contrato de mensagem |
|   |   |     

## <a name="as2"></a>AS2

### <a name="-missing-as2-message-headers"></a>* Cabeçalhos de mensagens AS2 em falta  

|   |   |  
|---|---|
| Descrição de erro| Cabeçalhos de AS2 inválidos. Um dos ' AS2-para ' ou ' AS2-de ' cabeçalhos estão vazios| 
| Ação do utilizador | Foi recebida uma mensagem de AS2 que não continha o AS2-do ou AS2-a ou ambos os cabeçalhos. </br> Consulte a mensagem de AS2 AS2-do e AS2-cabeçalhos e correto-los com base na configuração de contrato |
|  |  | 


### <a name="-missing-as2-message-body-and-headers"></a>* Em falta AS2 corpo da mensagem e os cabeçalhos    

|   |   |  
|---|---|
| Descrição de erro| O conteúdo do pedido é nulo ou está vazio | 
| Ação do utilizador | Foi recebida uma mensagem de AS2 que não continha o corpo da mensagem |
|  |  | 

### <a name="-as2-message-decryption-failure"></a>* Falha de desencriptação de mensagem AS2

|   |   | 
|---|---|
| Descrição de erro |  [processados/erro: falha de desencriptação] | 
| Ação do utilizador | Adicionar @base64ToBinary para AS2Message antes de enviar para o parceiro 
```java
            "HTTP": {
                "inputs": {
                    "body": "@base64ToBinary(body('Encode_to_AS2_message')?['AS2Message']?['Content'])",
                    "headers": "@body('Encode_to_AS2_message')?['AS2Message']?['OutboundHeaders']",
                    "method": "POST",
                    "uri": "xxxxx.xxx"
                },
                
``` 

### <a name="-mdn-decryption-failure"></a>* Falha de desencriptação MDN

|   |   | 
|---|---|
| Descrição de erro |  [processados/erro: falha de desencriptação] | 
| Ação do utilizador | Adicionar @base64ToBinary para MDN antes de enviar para o parceiro 
```java
            "Response": {
                "inputs": {
                    "body": "@base64ToBinary(body('Decode_AS2_message')?['OutgoingMDN']?['Content'])",
                    "headers": "@body('Decode_AS2_message')?['OutgoingMDN']?['OutboundHeaders']",
                    "statusCode": 200
                },
                
``` 

### <a name="-missing-signing-certificate"></a>* Certificado de assinatura em falta

|   |   |  
|---|---|
| Descrição de erro| O certificado de assinatura não foi configurado para terceiros AS2. </br> AS2-do: partner1 AS2-a: partner2 | 
| Ação do utilizador | Configurar definições de contratos AS2 com o certificado correto para a assinatura |
|  |  | 

## <a name="x12-and-edifact"></a>X12 e EDIFACT

### <a name="-leading-or-trailing-space-found"></a>* Esquerda ou à direita espaço encontrado    
    
|   |   | 
|---|---|
| Descrição de erro | Ocorreu um erro durante a análise. A transação Edifact definido com o id ' 123456 'contidas no intercâmbio (sem grupo) com o id ' 987654', com o id do remetente 'Partner1', id de recetor 'Partner2' está a ser suspensa com os seguintes erros: separador à esquerda à direita encontrado |
| Ação do utilizador | As definições de contrato de ser configurada para permitir espaço à direita e à esquerda. </br> Editar definições de contrato para permitir espaço à direita e à esquerda |
|   |   |

![permitir espaço](./media/logic-apps-enterprise-integration-b2b-list-errors-solutions/leadingandtrailing.png)

### <a name="-duplicate-check-has-enabled-in-the-agreement"></a>* Ativou a verificação duplicada no contrato de

|   |   | 
|---|---| 
| Descrição de erro | Número de controlo duplicado |
| Ação do utilizador | Este erro indica que a mensagem recebida tem controlo duplicado números. </br> Corrija o número de controlo e volte a enviar a mensagem |
|   |   |

### <a name="-missing-schema-in-the-agreement"></a>* Esquema em falta no contrato de

|   |   | 
|---|---| 
| Descrição de erro | Ocorreu um erro durante a análise. X12 conjunto de transação com o id '564220001' contidos no grupo de funcional com o id '56422', no intercâmbio com o id '000056422', com o id do remetente ' 12345678', id de recetor ' 87654321' está a ser suspensa com os seguintes erros "a mensagem tem um tipo de documento desconhecido e não foi possível resolver a nenhum dos esquemas existentes configurados no contrato de" |
| Ação do utilizador | Configurar esquema nas definições do contrato  |
|   |   |

### <a name="-incorrect-schema-in-the-agreement"></a>* Esquema incorreto no contrato de

|   |   | 
|---|---| 
| Descrição de erro | A mensagem tem um tipo de documento desconhecido e não foi possível resolver a nenhum dos esquemas existentes configurados no contrato de. |
| Ação do utilizador | Configurar esquema correta nas definições do contrato  |
|   |   |

## <a name="flat-file"></a>Ficheiro simples

### <a name="-input-message-with-no-body"></a>* Mensagem de entrada com nenhum corpo

|   |   | 
|---|---|
| Descrição de erro | InvalidTemplate. Não é possível expressões de idioma do modelo de processo em entradas de 'Flat_File_Decoding' de ação na linha '1' e a coluna '1902': ' necessária propriedade 'content' espera um valor, mas obteve nulo. Caminho '.'. |
| Ação do utilizador | Este erro indica que a mensagem de entrada não contém um corpo |
|   |   | 

## <a name="learn-more"></a>Saiba mais
[Saiba mais sobre o pacote de integração do Enterprise](logic-apps-enterprise-integration-overview.md)
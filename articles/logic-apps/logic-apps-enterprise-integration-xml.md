---
title: Trabalhar com mensagens XML em fluxos de trabalho - Azure Logic Apps | Microsoft Docs
description: "Processar, validar, transformar e enriquecer a mensagens de XML as logic apps e empresas-para cenários utilizando o pacote de integração do Enterprise"
services: logic-apps
documentationcenter: .net,nodejs,java
author: MandiOhlinger
manager: anneta
editor: 
ms.assetid: 47672dc4-1caa-44e5-b8cb-68ec3a76b7dc
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: H1Hack27Feb2017
ms.date: 02/27/2017
ms.author: LADocs; mandia
ms.openlocfilehash: 3fec4935f5317be4bf8c9e05f1c24a7c05381b1e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="validate-and-transform-xml-encode-and-decode-flat-files-and-enrich-messages-features-in-logic-apps"></a>Validar transformação XML, codificar e descodificar ficheiros simples e enriquecer a funcionalidades de mensagens nas logic apps

Utilizar as logic apps, tem a capacidade para processar mensagens de XML que enviar e receber. Esta funcionalidade está incluída no pacote de integração empresarial. Para os utilizadores com um fundo do BizTalk Server, Enterprise Integration Pack fornece capacidades semelhantes para transformar e validar mensagens, trabalhar com ficheiros simples e mesmo utilizar o XPath para enriqueça ou extrair propriedades específicas de uma mensagem. 

Para os utilizadores que são novos para este espaço de mensagens em fila, estas funcionalidades expanda como processar mensagens dentro do seu fluxo de trabalho. Por exemplo, se estiver num cenário de empresa-empresa e a trabalhar com esquemas XML específicas, em seguida, pode utilizar o pacote de integração de empresa para melhorar como da sua empresa processa estas mensagens. 

O pacote de integração de Enterprise inclui: 

* [Validação XML](logic-apps-enterprise-integration-xml-validation.md "Saiba mais sobre a validação de mensagem XML") -validar uma mensagem XML de entrada ou saída com um esquema específico.
* [Transformação XML](../logic-apps/logic-apps-enterprise-integration-transform.md "Saiba mais sobre transformações de mensagem XML e mapas") - converter ou personalizar uma mensagem XML com base nos seus requisitos ou os requisitos de um parceiro.
* [Simples e ficheiro codificação e descodificação de ficheiro simples](logic-apps-enterprise-integration-flatfile.md "Saiba mais sobre o ficheiro simples codificação/descodificação") - codificar ou descodificar um ficheiro simples. Por exemplo, SAP aceita e envia ficheiros IDOC no formato de ficheiro simples. Muitas plataformas de integração criar mensagens XML, incluindo as Logic Apps. Por isso, pode criar uma aplicação lógica que utiliza o codificador de ficheiro simples "converter" ficheiros XML para ficheiros simples. 
* [XPath](https://msdn.microsoft.com/library/mt643789.aspx) - enriquecer a uma mensagem e extrair propriedades específicas da mensagem. Em seguida, pode utilizar as propriedades extraídas para encaminhar a mensagem para um destino ou um ponto de final intermediário.

## <a name="try-it-out"></a>Experimentar
[Implementar uma aplicação lógica totalmente operacional ](https://github.com/Azure/azure-quickstart-templates/tree/master/201-logic-app-veter-pipeline) (exemplo de GitHub), utilizando as funcionalidades XML no Azure Logic Apps.

## <a name="learn-more"></a>Saiba mais
[Saiba mais sobre o Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md "Saiba mais sobre o pacote de integração do Enterprise")

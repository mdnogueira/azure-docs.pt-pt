---
title: Certificar a sua conectores personalizados - Azure Logic Apps | Microsoft Docs
description: Disponibilizar conectores para todos os utilizadores no Azure Logic Apps, Microsoft Flow e Microsoft PowerApps
author: ecfan
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: 
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/22/2017
ms.author: LADocs; estfan
ms.openlocfilehash: 9bcb138c181df0c12548cc5dc721542fd9c9ba68
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="submit-your-connectors-for-microsoft-certification"></a>Submeter os conectores de certificação da Microsoft

Para tornar os conectores personalizados publicamente disponíveis para todos os utilizadores no Azure Logic Apps, PowerApps Microsoft e Microsoft Flow, submeter o seu conector à Microsoft para rever, validação e aprovação para publicação. 

## <a name="certification-criteria"></a>Critérios de certificação

| Capacidade | Detalhes | Necessários ou recomendados |
|------------|---------|-------------------------|
| Aplicação de software-como-um-serviço (SaaS) | Cumpre um cenário de utilizador que se adequa bem com Logic Apps, PowerApps e fluxo. | Necessário |
| Tipo de autenticação | A API tem de suportar OAuth2, a chave de API ou a autenticação básica. | Necessário | 
| Suporte | Tem de fornecer um contacto de suporte para que os clientes podem obter ajuda. | Necessário | 
| Disponibilidade e o tempo de atividade | A aplicação tiver, pelo menos, 99,9% tempo de atividade. | Recomendado | 
|||| 

Além disso, se não tiver um parceiro da Microsoft ou independentes de Software (ISV) e pretender certificar e um conector de versão publicamente, ou tem proprietário do serviço subjacente ou direitos explícitos presentes ao utilizar a API.

## <a name="validation-phases"></a>Fases de validação

A Microsoft utiliza estas fases de validação para avaliar o seu conector:

| Fase de validação | Descrição | 
| ----- | ----------- |
| Funcionalidade | Microsoft testa o conector com Logic Apps, PowerApps e fluxo, estes erros: <p>-Inválido OpenAPI (Swagger) ou JSON erros que são apresentados na secção de definição do Assistente do conector personalizado <p>-O tempo de execução e o esquema de erros de validação que aparecem na secção de teste do Assistente do conector personalizado | 
| Conteúdo | Microsoft verifica que o conector utiliza nomes amigáveis e as descrições para cada entidade. Certifique-se de que cada operação, o parâmetro de entrada e o atributo de resposta no Swagger seu conector tem estes elementos: <p>- [Resumo](../logic-apps/custom-connector-openapi-extensions.md#summary) <br>- [Descrição](../logic-apps/custom-connector-openapi-extensions.md#description) </br>- [Informações de visibilidade](../logic-apps/custom-connector-openapi-extensions.md#visibility) | 
||| 

## <a name="nominate-and-submit-your-connector-to-microsoft-for-certification"></a>Para indicar e submeter o seu conector à Microsoft para certificação

Para se candidatar a certificação, siga estes passos:

1. **Para indicar**

   1. [Submeter o nomination](https://go.microsoft.com/fwlink/?linkid=848754).

      1. Na parte inferior da página, escolha **contacte-nos**.
      2. Preencher o formulário e selecione **perguntas sobre o programa de conector de ISV e marketing conjunta**.
      3. Escolha **submeter**.

   2. Assine o mútua não divulgação contrato e contrato para parceiros que recebe. 

      Microsoft requer estes contratos assinados antes de continuar. 
      Vamos, em seguida, pode verificar se o seu conector cumpre os critérios de certificação. 
   
   3. Se o seu conector for aprovada, o Microsoft notifica-o com instruções para a integração.
    
2. **Rever**

   1. Envie estas informações para o seu contacto nomination para revisão:

      * O ficheiro de OpenAPI que descreve a API
      * O ficheiro de ícone (. png ou. jpg) que representa o conector 
      
        O ícone deve ter um logótipo de pixel ~ 160 dentro de um quadrado 230 pixel. 
        Um logótipo branco sobre um fundo colorido é preferencial.
      
      * Marca o ícone cor em formato hexadecimal, o que deve corresponder à imagem de fundo colorida no ficheiro de ícone

      * Uma conta de teste de validação
      * Um contacto de suporte

   2. Se é necessário obter mais informações, iremos irá contactá-lo.

3. **Publicar**

    Depois de validamos funcionalidade e conteúdo do seu conector, iremos testar o conector para implementação em todos os produtos e regiões.
    
    Por predefinição, todos os conectores são publicados como conectores "premium". 
    Se criou a aplicação com o Azure, pode aplicar para listar o seu conector como "padrão" conector que está disponível para todos os utilizadores com o Office 365 Enterprise planos. 
    Para obter mais detalhes, peça ao seu contacto nomination.

## <a name="next-steps"></a>Passos seguintes

* [FAQ sobre o conetor personalizado](../logic-apps/custom-connector-faq.md)
* [Descrição geral do conetor personalizado](../logic-apps/custom-connector-overview.md)
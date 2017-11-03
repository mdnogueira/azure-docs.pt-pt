---
title: "B2C do Azure Active Directory: Desativar a verificação de correio eletrónico durante a inscrição do consumidor | Microsoft Docs"
description: "Um tópico demonstrar como desativar a verificação de correio eletrónico durante a inscrição no Azure Active Directory B2C de consumidor"
services: active-directory-b2c
documentationcenter: 
author: parakhj
manager: krassk
editor: parakhj
ms.assetid: 433f32b8-96d2-4113-aa82-efcf42fa9827
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 2/06/2017
ms.author: parakhj
ms.openlocfilehash: d8e44a8aade60d21734477d60bccc2bd5194436e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-b2c-disable-email-verification-during-consumer-sign-up"></a>O Azure Active Directory B2C: Verificação de correio eletrónico de desativar durante a inscrição de consumidor
Quando ativada, o Azure Active Directory (Azure AD) B2C fornece um consumidor a capacidade para se inscrever para aplicações, ao fornecer um endereço de e-mail e criar uma conta local. O Azure AD B2C garante endereços de correio eletrónico, exigindo que os consumidores Certifique-se durante o processo de inscrição. Também impede que um processo automatizado malicioso gerar contas falsificadas para as aplicações.

Os programadores de algumas aplicações preferem ignorar a verificação de correio eletrónico durante o processo de inscrição e, em vez disso tem consumidor validar o endereço de correio eletrónico mais tarde. Para suportar isto, Azure AD B2C pode ser configurado para desativar a verificação de correio eletrónico. Se o fizer, cria um processo de inscrição smoother e fornece os programadores a flexibilidade para diferenciar os consumidores que tem verificado o respetivo endereço de e-mail a partir desses consumidores que não têm.

Por predefinição, as políticas de inscrição têm de verificação de correio eletrónico ativada. Utilize os seguintes passos para desativá-la:

1. [Siga estes passos para navegar para o painel de funcionalidades do B2C no portal do Azure](active-directory-b2c-app-registration.md#navigate-to-b2c-settings).
2. Clique em **políticas de inscrição** ou **políticas de inscrição ou início de sessão** dependendo da configuração para se inscrever.
3. Clique em sua política (por exemplo, "B2C_1_SiUp") para abri-lo. Clique em **editar** na parte superior do painel.
4. Clique em **personalização de página IU**.
5. Clique em **página de inscrição de conta Local**.
6. Clique em **endereço de correio eletrónico** no **nome** coluna sob o **atributos de inscrição** secção.
7. Ativar/desativar a **exigir verificação** opção para **não**.
8. Clique em **OK** na parte inferior até chegar a **Editar política** painel.
9. Clique em **guardar** na parte superior do painel. Terminar!

> [!NOTE]
> Desativar a verificação de e-mail no processo de inscrição pode levar para lhe enviar spam. Se desativar a predefinição, é recomendado adicionar o seu próprio sistema de verificação.
> 
> 

Podemos sempre estão abertos para comentários e sugestões! Se tiver dificuldades em causa com este tópico ou tiver recomendações para melhorar este conteúdo, Agradecemos os seus comentários na parte inferior da página. Para pedidos de funcionalidades, adicioná-los ao [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160596-b2c).
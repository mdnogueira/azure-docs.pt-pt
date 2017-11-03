---
title: 'Azure Active Directory B2C: Os atributos personalizados | Microsoft Docs'
description: "Como utilizar atributos personalizados no Azure Active Directory B2C para recolher informações sobre os consumidores"
services: active-directory-b2c
documentationcenter: 
author: swkrish
manager: mbaldwin
editor: bryanla
ms.assetid: 055ffb0a-197b-4716-8dad-1fd8a01e174f
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/06/2016
ms.author: swkrish
ms.openlocfilehash: 356aaeff3a78fc7b682d621e8e0de9312582b2fe
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-b2c-use-custom-attributes-to-collect-information-about-your-consumers"></a>O Azure Active Directory B2C: Utilizar atributos personalizados para recolher informações sobre os consumidores
Diretório do Azure Active Directory (Azure AD) B2C é fornecido com um conjunto de informações (atributos) incorporado: nome próprio, apelido, cidade, Código Postal e outros atributos. No entanto, todas as aplicações direcionadas para o consumidor tem requisitos exclusivos nos quais atributos para recolher dos consumidores. Com o Azure AD B2C, pode expandir o conjunto de atributos armazenados em cada conta de consumidor. Pode criar atributos personalizados no [portal do Azure](https://portal.azure.com/) e utilizá-lo nas políticas de inscrição, conforme mostrado abaixo. Também pode ler e escrever estes atributos utilizando o [AD Graph API do Azure](active-directory-b2c-devquickstarts-graph-dotnet.md).

> [!NOTE]
> Utilização de atributos personalizados [extensões do Azure AD Graph API do esquema](https://msdn.microsoft.com/library/azure/dn720459.aspx).
> 
> 

## <a name="create-a-custom-attribute"></a>Criar um atributo personalizado
1. [Siga estes passos para navegar para o painel de funcionalidades do B2C no portal do Azure](active-directory-b2c-app-registration.md#navigate-to-b2c-settings).
2. Clique em **atributos de utilizador**.
3. Clique em **+ Adicionar** na parte superior do painel.
4. Forneça um **nome** para o atributo personalizado (por exemplo, "ShoeSize") e, opcionalmente, um **Descrição**. Clique em **Criar**.
   
   > [!NOTE]
   > Apenas a "cadeia" **tipo de dados** está atualmente disponível.
   > 
   > 

O atributo personalizado está agora disponível na lista de **atributos de utilizador**e para utilização nas suas políticas de inscrição.

## <a name="use-a-custom-attribute-in-your-sign-up-policy"></a>Utilize um atributo personalizado na política de inscrição
1. [Siga estes passos para navegar para o painel de funcionalidades do B2C no portal do Azure](active-directory-b2c-app-registration.md#navigate-to-b2c-settings).
2. Clique em **Políticas de inscrição**.
3. Clique em política de inscrição (por exemplo, "B2C_1_SiUp") para abri-lo. Clique em **editar** na parte superior do painel.
4. Clique em **atributos de inscrição** e selecione o atributo personalizado (por exemplo, "ShoeSize"). Clique em **OK**.
5. Clique em **afirmações de aplicação** e selecione o atributo personalizado. Clique em **OK**.
6. Clique em **guardar** na parte superior do painel.

Pode utilizar a funcionalidade de "Executar agora" na política para verificar a experiência de consumidor. Deve agora ver "ShoeSize" na lista de atributos recolhidos durante a inscrição de consumidor e vê-lo no token enviado para a sua aplicação.

## <a name="notes"></a>Notas
* Juntamente com as políticas de inscrição, os atributos personalizados também podem ser utilizados nas políticas de inscrição ou início de sessão e as políticas de edição de perfis.
* Há uma limitação conhecida de atributos personalizados. É apenas criado na primeira vez que é utilizado em qualquer política e não quando a adicionar à lista de **atributos de utilizador**.


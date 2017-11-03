---
title: "Do Azure Active Directory B2C: Reposição de palavra-passe self-service | Microsoft Docs"
description: "Um tópico demonstrar como configurar o self-service reposição palavra-passe para os consumidores no Azure Active Directory B2C"
services: active-directory-b2c
documentationcenter: 
author: swkrish
manager: mbaldwin
editor: curtand
ms.assetid: c87ed86e-1520-42b1-8c31-46cd44ed5310
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/06/2016
ms.author: swkrish
ms.openlocfilehash: 0508868e3b00c5771cc26038a3dd71fde6625a84
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-b2c-set-up-self-service-password-reset-for-your-consumers"></a>O Azure Active Directory B2C: Configurar o self-service reposição palavra-passe para os consumidores
Com a funcionalidade de reposição de palavra-passe self-service, os consumidores (que tenham efetuado a inscrição para contas locais) podem repor as palavras-passe por si próprios. Esta opção reduz significativamente a carga sobre a sua equipa de suporte, especialmente se a sua aplicação tiver milhões de consumidores utilizá-lo regularmente. Atualmente, é apenas suportado utilizando um endereço de correio eletrónico verificado como um método de recuperação. Iremos adicionar métodos de recuperação adicionais (número de telefone verificado, perguntas de segurança, etc.) no futuro.

> [!NOTE]
> Este artigo aplica-se a palavra-passe self-service reposição utilizada no contexto de uma política de início de sessão. Se precisar de políticas de reposição de palavra-passe totalmente personalizável invocadas a partir da sua aplicação, consulte [neste artigo](active-directory-b2c-reference-policies.md#create-a-password-reset-policy).
> 
> 

Por predefinição, o diretório não tem palavra-passe self-service reposição ativada. Utilize os seguintes passos para ativá-la:

1. Inicie sessão no [Portal Clássico do Azure](https://manage.windowsazure.com/) como Administrador da Subscrição. Este é o mesmo ou conta profissional ou a mesma conta Microsoft que utilizou para criar o seu diretório.
2. Navegue para a extensão do Active Directory na barra de navegação no lado esquerdo.
3. Localizar o diretório sob o **diretório** separador e clique no mesmo.
4. Clique no separador **Configurar**.
5. Desloque para baixo até o **política de reposição de palavra-passe do utilizador** secção e ativar/desativar a **utilizadores ativados para a reposição de palavra-passe** opção para **Sim**. Tenha em atenção que o **endereço de correio eletrónico alternativo** opção for selecionada; deixá-lo tal como está.
   
    ![Reposição personalizada de palavra-passe](./media/active-directory-b2c-reference-sspr/sspr.png)
6. Clique em **Guardar** na parte inferior da página. Terminar!

Para testar, utilize a funcionalidade "Executar agora" em qualquer política de início de sessão que tenha contas locais como um fornecedor de identidade. No início de sessão-in de conta local página (onde introduziu um endereço de e-mail e a palavra-passe, ou um nome de utilizador e a palavra-passe), clique em **não é possível aceder à sua conta?** para verificar a experiência de consumidor.

> [!NOTE]
> As páginas de reposição de palavra-passe self-service podem ser personalizadas utilizando a [corporativa funcionalidade](../active-directory/active-directory-add-company-branding.md).
> 
> 


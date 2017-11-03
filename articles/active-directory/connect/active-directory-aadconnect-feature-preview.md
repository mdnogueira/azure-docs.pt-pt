---
title: "Do Azure AD Connect: As funcionalidades pré-visualização | Microsoft Docs"
description: "Este tópico descreve em mais funcionalidades de detalhe que estão na pré-visualização no Azure AD Connect."
services: active-directory
documentationcenter: 
author: andkjell
manager: femila
editor: 
ms.assetid: c75cd8cf-3eff-4619-bbca-66276757cc07
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.author: billmath
ms.openlocfilehash: cbf8f729d0ebfb271bb0d8702ac043442b42c262
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="more-details-about-features-in-preview"></a>Obter mais detalhes sobre as funcionalidades em pré-visualização
Este tópico descreve como utilizar as funcionalidades atualmente em pré-visualização.

## <a name="group-writeback"></a>Repetição de escrita do grupo
A opção para repetição de escrita do grupo em funcionalidades opcionais permite-lhe a repetição de escrita **grupos do Office 365** a uma floresta com o Exchange instalado. Este é um grupo que é controlado sempre na nuvem. Se tiver o Exchange no local, em seguida, pode escrever novamente estes grupos no local para que os utilizadores com uma caixa de correio do Exchange no local podem enviar e receber e-mails destes grupos.

Podem encontrar mais informações sobre grupos do Office 365 e como utilizá-los [aqui](http://aka.ms/O365g).

Um grupo do Office 365 é representado como um grupo de distribuição no local do AD DS. O servidor do Exchange no local tem de estar na atualização cumulativa do Exchange 2013 8 (lançada em Março de 2015) ou o Exchange 2016 para reconhecer este novo tipo de grupo.

**Notas durante a pré-visualização**

* O atributo de livro de endereços não for preenchido atualmente na pré-visualização. Sem este atributo, o grupo não é visível na GAL. A forma mais fácil para preencher este atributo está a utilizar o cmdlet do PowerShell do Exchange `update-recipient`.
* Só é florestas com o esquema do Exchange são destinos válidos para grupos. Não se foi detetada nenhuma Exchange, repetição de escrita do grupo não é possível ativar.
* Apenas floresta única organização implementações do Exchange são atualmente suportadas. Se tiver mais do que um Exchange organização no local, em seguida, é necessário uma solução de GALSync no local para estes grupos a aparecer na sua noutras florestas.
* A funcionalidade de repetição de escrita do grupo não processa os grupos de segurança ou grupos de distribuição.

> [!NOTE]
> Uma subscrição do Azure AD Premium é necessária para a repetição de escrita do grupo.
> 
>

## <a name="user-writeback"></a>Repetição de escrita do utilizador
> [!IMPORTANT]
> A funcionalidade de pré-visualização de repetição de escrita do utilizador foi removida na atualização de Agosto de 2015 para o Azure AD Connect. Se tiver ativado o-lo, deve desativar esta funcionalidade.
>
>

## <a name="next-steps"></a>Passos seguintes
Continuar a [instalação personalizada do Azure AD Connect](active-directory-aadconnect-get-started-custom.md).

Saiba mais sobre como [Integrar as identidades no local ao Azure Active Directory](active-directory-aadconnect.md).

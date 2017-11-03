---
title: "Sincronização do Azure AD Connect: extensões de diretórios | Microsoft Docs"
description: "Este tópico descreve a funcionalidade de extensões de diretório no Azure AD Connect."
services: active-directory
documentationcenter: 
author: AndKjell
manager: femila
editor: 
ms.assetid: 995ee876-4415-4bb0-a258-cca3cbb02193
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/12/2017
ms.author: billmath
ms.openlocfilehash: 16ce7da9321a4a36e892bad75819d1c30ab5197a
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/31/2017
---
# <a name="azure-ad-connect-sync-directory-extensions"></a>Sincronização do Azure AD Connect: extensões de diretórios
Extensões de diretórios permite-lhe expandir o esquema no Azure AD com os seus próprios atributos do Active Directory no local. Esta funcionalidade permite-lhe criar aplicações LOB consumir atributos que continua a gerir no local. Estes atributos podem ser consumidos através de [extensões de diretórios do Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-directory-schema-extensions) ou [Microsoft Graph](https://graph.microsoft.io/). Pode ver os atributos disponíveis utilizar [Explorador do Azure AD Graph](https://graphexplorer.azurewebsites.net/) e [explorer Microsoft Graph](https://developer.microsoft.com/en-us/graph/graph-explorer) respetivamente.

No presente nenhuma carga de trabalho do Office 365 consome estes atributos.

Configurar os atributos adicionais que pretende sincronizar no caminho de definições personalizadas no Assistente de instalação.
![Assistente de extensão de esquema](./media/active-directory-aadconnectsync-feature-directory-extensions/extension2.png)  
A instalação mostra os seguintes atributos, são candidatos válidos:

* Tipos de objeto de utilizador e grupo
* Atributos de valor único: String, booleano, número inteiro, binário
* Atributos com múltiplos valores: cadeia, binário


>[!NOTE]
> Enquanto o Azure AD Connect suporta sincronizar atributos com múltiplos valores de AD para o Azure AD como extensões de diretórios com múltiplos valores, existem atualmente não existem funcionalidades no Azure AD que suportam a utilização de extensões de diretórios com múltiplos valores.

A lista de atributos é lido a partir da cache do esquema criada durante a instalação do Azure AD Connect. Se tiver expandido o esquema do Active Directory com atributos adicionais, em seguida, a [esquema têm de ser atualizado](active-directory-aadconnectsync-installation-wizard.md#refresh-directory-schema) antes destes novos atributos estão visíveis.

Um objeto no Azure AD pode ter até 100 atributos de extensões de diretórios. O comprimento máximo é 250 caracteres. Um valor de atributo é maior, em seguida, será truncado se o motor de sincronização.

Durante a instalação do Azure AD Connect, uma aplicação está registada em que estes atributos estão disponíveis. Pode ver esta aplicação no portal do Azure.  
![Aplicação de extensões de esquema](./media/active-directory-aadconnectsync-feature-directory-extensions/extension3new.png)

Estes atributos estão agora disponíveis através do gráfico:  
![Graph](./media/active-directory-aadconnectsync-feature-directory-extensions/extension4.png)

Os atributos têm o prefixo extensão\_{AppClientId}\_. O AppClientId tem o mesmo valor para todos os atributos no inquilino do Azure AD.

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre o [sincronização do Azure AD Connect](active-directory-aadconnectsync-whatis.md) configuração.

Saiba mais sobre como [Integrar as identidades no local ao Azure Active Directory](active-directory-aadconnect.md).

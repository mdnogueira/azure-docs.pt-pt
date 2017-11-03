---
title: "Do Azure AD Connect: Instâncias do serviço de sincronização | Microsoft Docs"
description: "Esta página documentos considerações especiais para instâncias do Azure AD."
services: active-directory
documentationcenter: 
author: andkjell
manager: femila
editor: 
ms.assetid: f340ea11-8ff5-4ae6-b09d-e939c76355a3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/26/2017
ms.author: billmath
ms.openlocfilehash: abf234caa4c26cf3554911aabb839c696b1ba8cb
ms.sourcegitcommit: c50171c9f28881ed3ac33100c2ea82a17bfedbff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/26/2017
---
# <a name="azure-ad-connect-special-considerations-for-instances"></a>O Azure AD Connect: Considerações especiais para instâncias
O Azure AD Connect é normalmente utilizado com a instância em todo o mundo do Azure AD e o Office 365. Mas também existem outras instâncias e estes têm diferentes requisitos para os URLs e outras considerações especiais.

## <a name="microsoft-cloud-germany"></a>Microsoft Cloud Alemanha
O [Datacenters da Microsoft Cloud](http://www.microsoft.de/cloud-deutschland) é uma nuvem sovereign operada por uma trustee de dados em alemão.

| URLs para abrir o servidor proxy |
| --- |
| \*. microsoftonline.de |
| \*.windows.net |
| + Listas de revogação de certificados |

Quando inicia sessão seu inquilino do Azure AD, tem de utilizar uma conta no domínio onmicrosoft.de.

Funcionalidades atualmente não estão presentes na Alemanha Microsoft Cloud:

* **O Azure AD Connect Health** não está disponível.
* **As atualizações automáticas** não está disponível.
* **Repetição de escrita de palavras-passe** está disponível para pré-visualização com a versão do Azure AD Connect 1.1.570.0 e depois.
* Não estão disponíveis outros serviços do Azure AD Premium.

## <a name="microsoft-azure-government-cloud"></a>Nuvem do Microsoft Azure Government
O [cloud do Microsoft Azure Government](https://azure.microsoft.com/features/gov/) é uma nuvem para dos EUA.

Esta nuvem é suportada por versões anteriores do DirSync. A partir de compilação 1.1.180 do Azure AD Connect, a próxima geração da nuvem é suportada. Esta geração está a utilizar apenas de E.U.A. baseada em pontos finais e ter uma lista de URLs para abrir no seu servidor proxy diferente.

| URLs para abrir o servidor proxy |
| --- |
| \*.microsoftonline.com |
| \*. microsoftonline.us |
| \*. windows.net (necessário para automático deteção de inquilino do Azure AD government) |
| \*. gov.us.microsoftonline.com |
| + Listas de revogação de certificados |

> [!NOTE]
> A partir do AAD Connect versão 1.1.647.0, definindo o valor de AzureInstance no registo já não é necessário que fornecido *. windows.net está aberta no seu servidor de proxy (es).

Funcionalidades atualmente não está presentes na nuvem do Microsoft Azure Government:

* **O Azure AD Connect Health** não está disponível.
* **As atualizações automáticas** não está disponível.
* **Repetição de escrita de palavras-passe** está disponível para pré-visualização com a versão do Azure AD Connect 1.1.570.0 e depois.
* Não estão disponíveis outros serviços do Azure AD Premium.

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre como [Integrar as identidades no local ao Azure Active Directory](active-directory-aadconnect.md).

---
title: Azure AD Connect no Microsoft Cloud Alemanha
description: "O Azure AD Connect irá integrar os diretórios no local ao Azure Active Directory. Isto permite-lhe fornecer uma identidade comum para as aplicações do Office 365, do Azure e do SaaS integradas com o Azure AD."
keywords: "introdução ao Azure AD Connect, descrição geral do Azure AD Connect, o que é o Azure AD Connect, instalar o Active Directory, Alemanha, Floresta Negra"
services: active-directory
documentationcenter: 
author: billmath
manager: femila
editor: 
ms.assetid: 2bcb0caf-5d97-46cb-8c32-bda66cc22dad
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/12/2017
ms.author: billmath
ms.openlocfilehash: 4c55b116c0dc080ae316caca873a7b693caa793b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-ad-connect-in-microsoft-cloud-germany---public-preview"></a>Azure AD Connect no Microsoft Cloud Alemanha - Pré-visualização Pública
## <a name="introduction"></a>Introdução
O Azure AD Connect fornece sincronização entre o Active Directory no local e o Azure Active Directory.
Atualmente, muitos dos cenários no [Microsoft Cloud Alemanha](https://www.microsoft.com/de-de/cloud/deutschland/default.aspx) têm de ser executados pelo operador. Ao utilizar o Microsoft Cloud Alemanha, tem de ter em atenção o seguinte:

* Os seguintes URLs têm de ser abertos num servidor proxy para que a sincronização seja feita com êxito:
  
  * *.microsoftonline.de
  * *.windows.net
  * * Listas de Revogação de Certificados
* Quando inicia sessão no seu diretório do Azure AD, tem de utilizar uma conta no domínio onmicrosoft.de.
* As seguintes funcionalidades não estão disponíveis:
  * Azure AD Connect Health
  * Atualizações automáticas
 
## <a name="download"></a>Transferência
Pode transferir o Azure AD Connect a partir do painel do Azure AD Connect no portal.  Utilize as instruções abaixo para localizar o painel do Azure AD Connect.

### <a name="the-azure-ad-connect-blade"></a>O Painel do Azure AD Connect
Depois de iniciar sessão no portal do Azure, faça o seguinte:

1. Vá para Procurar
2. Selecione Azure Active Directory
3. Em seguida, selecione Azure AD Connect

Deverá ver o seguinte:

![Painel do Azure AD Connect](media/active-directory-aadconnect-germany/germany1.png)

A tabela seguinte descreve as funcionalidades apresentadas no painel.

| Título | Descrição |
| --- | --- |
| ESTADO DE SINCRONIZAÇÃO |Permite-lhe saber se a sincronização está ativada ou desativada. |
| ÚLTIMA SINCRONIZAÇÃO |A última vez que uma sincronização bem-sucedida foi concluída. |
| DOMÍNIOS FEDERADOS |Mostra o número de domínios federados atualmente configurados. |

## <a name="installation"></a>Instalação
Para instalar o Azure AD Connect, pode utilizar a documentação [aqui](active-directory-aadconnect.md#install-azure-ad-connect).

## <a name="advanced-features-and-additional-information"></a>Funcionalidades avançadas e Informações Adicionais
Para obter informações adicionais e orientações sobre definições personalizadas ou configurações avançadas, comece por [Integrar as identidades no local no Azure Active Directory](active-directory-aadconnect.md).  Esta página fornece informações e hiperligações para orientações adicionais.


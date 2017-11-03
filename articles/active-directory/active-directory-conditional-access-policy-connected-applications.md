---
title: "Configurar políticas de acesso condicional baseado no dispositivo do Azure Active Directory | Microsoft Docs"
description: "Saiba como configurar políticas de acesso condicional baseado no dispositivo do Azure Active Directory."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: a27862a6-d513-43ba-97c1-1c0d400bf243
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/22/2017
ms.author: markvi
ms.reviewer: jairoc
ms.openlocfilehash: a57edd30975ec0e943fd84b2c66137d328a89b8b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="configure-azure-active-directory-device-based-conditional-access-policies"></a>Configurar políticas de acesso condicional baseado no dispositivo do Azure Active Directory

Com [acesso condicional do Azure Active Directory (Azure AD)](active-directory-conditional-access-azure-portal.md), pode ajustar a forma como os utilizadores autorizados podem aceder aos seus recursos. Por exemplo, limitar o acesso a alguns recursos para dispositivos fidedignos. Uma política de acesso condicional que requer um dispositivo fidedigno também é conhecido como política de acesso condicional baseado no dispositivo.

Este tópico fornece informações sobre como configurar políticas de acesso condicional baseado no dispositivo para aplicações do Azure AD ligados. 


## <a name="before-you-begin"></a>Antes de começar

Ligações de acesso condicional baseado no dispositivo **acesso condicional do Azure AD** e **em conjunto da gestão de dispositivos do Azure AD**. Se não estiver familiarizado com uma das seguintes áreas ainda, leia os tópicos seguintes, pela primeira vez:

- **[Acesso condicional no Azure Active Directory](active-directory-conditional-access-azure-portal.md)**  -este tópico fornece uma descrição geral conceptual do acesso condicional e a terminologia relacionada.

- **[Introdução à gestão de dispositivos no Azure Active Directory](device-management-introduction.md)**  -este tópico fornece uma descrição geral das várias opções de tem de ligar os dispositivos com o Azure AD. 


## <a name="trusted-devices"></a>Dispositivos fidedignos

No mundo mobile-primeiro, primeiro de nuvem, o Azure Active Directory permite início de sessão único para dispositivos, aplicações e serviços em qualquer lugar. Alguns recursos no seu ambiente, conceder acesso para os utilizadores certos poderão não ser suficientemente boas. Para além dos utilizadores certos, também poderá necessitar de um dispositivo fidedigno para ser utilizado para aceder a um recurso. No seu ambiente, pode definir o que um dispositivo fidedigno baseia-se nos seguintes componentes:

- O [plataformas de dispositivos](active-directory-conditional-access-azure-portal.md#device-platforms) num dispositivo
- Se um dispositivo é compatível
- Se um dispositivo se encontra associado a um domínio 

O [plataformas de dispositivos](active-directory-conditional-access-azure-portal.md#device-platforms) é caracterizada pelo sistema operativo que está a executar no seu dispositivo. Na sua política de acesso condicional baseado no dispositivo, pode limitar o acesso a alguns recursos para plataformas de dispositivos específicos.



Numa política de acesso condicional baseado no dispositivo, pode exigir a dispositivos fidedignos ser assinalado como estando em conformidade.

![Aplicações na nuvem](./media/active-directory-conditional-access-policy-connected-applications/24.png)

Dispositivos podem ser marcados como compatível no diretório por:

- Intune 
- Um dispositivo móvel de terceiros geridos sistema que gere os dispositivos Windows 10 através da integração do Azure AD 
 
  

Apenas os dispositivos que estão ligados ao Azure AD podem ser marcados como compatíveis. Para ligar um dispositivo ao Azure Active Directory, tem as seguintes opções: 

- Azure AD registado
- Azure AD associado
- Híbrida do Azure AD associada

    ![Aplicações na nuvem](./media/active-directory-conditional-access-policy-connected-applications/26.png)

Se tiver um local do Active Directory (AD) os requisitos de espaço, poderá considerar os dispositivos que não são ligados com o Azure AD mas associados ao seu AD para ser considerado confiável.

![Aplicações na nuvem](./media/active-directory-conditional-access-policy-connected-applications/25.png)


## <a name="next-steps"></a>Passos seguintes

Antes de configurar uma política de acesso condicional baseado no dispositivo no seu ambiente, deve ter uma vista de olhos a [melhores práticas para acesso condicional no Azure Active Directory](active-directory-conditional-access-best-practices.md).


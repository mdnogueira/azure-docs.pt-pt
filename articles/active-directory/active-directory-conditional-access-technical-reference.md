---
title: "Referência técnica do Azure Active Directory acesso condicional | Microsoft Docs"
description: "Saiba como utilizar o controlo de acesso condicional no Azure Active Directory. Especifique as condições para autenticar utilizadores e controlar o acesso à sua aplicação. Quando são satisfeitas as condições especificadas, os utilizadores são autenticados e conceder acesso à sua aplicação."
services: active-directory.
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: 56a5bade-7dcc-4dcf-8092-a7d4bf5df3c1
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/01/2017
ms.author: markvi
ms.reviewer: spunukol
ms.openlocfilehash: 8cde8e10b6789e930ea1deda2f4aa0d4a5e7ea97
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2017
---
# <a name="azure-active-directory-conditional-access-technical-reference"></a>Referência técnica do Azure Active Directory acesso condicional

Pode utilizar [acesso condicional do Azure Active Directory (Azure AD)](active-directory-conditional-access-azure-portal.md) para otimizar a forma como os utilizadores autorizados podem aceder aos seus recursos.  

Este tópico fornece informações de suporte para as seguintes opções de configuração para uma política de acesso condicional: 

- Atribuições de aplicações em nuvem

- Condição de plataforma do dispositivo 

- Condição de aplicações de cliente

- Requisito da aplicação cliente aprovada



## <a name="cloud-apps-assignments"></a>Atribuições de aplicações em nuvem

Quando configura uma política de acesso condicional, terá de [Selecione as aplicações em nuvem que utilizam a política](active-directory-conditional-access-azure-portal.md#who). 

![Selecione as aplicações em nuvem para a sua política](./media/active-directory-conditional-access-technical-reference/09.png)


### <a name="microsoft-cloud-applications"></a>Aplicações em nuvem da Microsoft

Pode atribuir uma política de acesso condicional para as seguintes aplicações em nuvem da Microsoft:

- O Azure Information Protection - [Saiba mais](https://docs.microsoft.com/information-protection/get-started/faqs#i-see-azure-information-protection-is-listed-as-an-available-cloud-app-for-conditional-accesshow-does-this-work)
- Azure RemoteApp

- Microsoft Dynamics 365

- Microsoft Office 365 Yammer

- Microsoft Office 365 Exchange Online

- Microsoft Office 365 SharePoint Online (inclui o OneDrive para empresas)

- Microsoft Power BI 

- Microsoft Visual Studio Team Services

- Microsoft Teams


### <a name="other-applications"></a>Outras aplicações 

Para além das aplicações em nuvem da Microsoft, pode atribuir uma política de acesso condicional para os seguintes tipos de aplicações na nuvem:

- Aplicações do Azure AD ligados

- Pré-integrado federado software como uma aplicação de serviço (SaaS)

- Aplicações que utilizam a palavra-passe-início de sessão único (SSO)

- Aplicações de linha de negócio

- Aplicações que utilizam o Proxy de aplicações do Azure AD


## <a name="device-platform-condition"></a>Condição de plataforma do dispositivo

Uma política de acesso condicional, pode configurar a condição de plataforma de dispositivo para associar a política para o sistema operativo num cliente.

![Associar a política de acesso para o SO de cliente](./media/active-directory-conditional-access-technical-reference/41.png)

Acesso condicional do Azure AD suporta as seguintes plataformas de dispositivos:

- Android

- iOS

- Windows Phone

- Windows

- macOS (pré-visualização)



## <a name="client-apps-condition"></a>Condição de aplicações de cliente 

Quando configurar uma política de acesso condicional, pode [Selecione as aplicações cliente](active-directory-conditional-access-azure-portal.md#client-apps) para a condição de aplicação de cliente. Defina a condição de aplicações para conceder ou bloquear o acesso quando é efetuada uma tentativa de acesso a partir os seguintes tipos de aplicações de cliente:

- Browser
- Aplicações móveis e aplicações de ambiente de trabalho

![Controlo de acesso para aplicações de cliente](./media/active-directory-conditional-access-technical-reference/03.png)

### <a name="supported-browsers"></a>Browsers suportados 

Controlar o acesso ao browser utilizando o **Browser** opção na sua política de acesso condicional. É concedido o acesso apenas se a tentativa de acesso for feita por um browser suportado. Quando é efetuada uma tentativa de acesso por um browser suportado, a tentativa de está bloqueada.

![Controlo de acesso para browsers suportados](./media/active-directory-conditional-access-technical-reference/05.png)

Na sua política de acesso condicional, os seguintes browsers são suportados: 


| SO                     | Browsers                    | Suporte     |
| :--                    | :--                         | :-:         |
| Windows 10             | Limite do Internet Explorer     | ![Marcar][1] |
| Windows 10             | Chrome                      | ![Marcar][1] |
| Windows 8 / 8.1        | Internet Explorer, Chrome   | ![Marcar][1] |
| Windows 7              | Internet Explorer, Chrome   | ![Marcar][1] |
| iOS                    | Safari, Browser gerido do Intune                      | ![Marcar][1] |
| Android                | Chrome, Browser gerido do Intune                      | ![Marcar][1] |
| Windows Phone          | Limite do Internet Explorer     | ![Marcar][1] |
| Windows Server 2016    | Limite do Internet Explorer     | ![Marcar][1] |
| Windows Server 2016    | Chrome                      | Brevemente |
| Windows Server 2012 R2 | Internet Explorer, Chrome   | ![Marcar][1] |
| Windows Server 2008 R2 | Internet Explorer, Chrome   | ![Marcar][1] |
| macOS                  | Safari                      | ![Marcar][1] |
| macOS                  | Chrome                      | Brevemente |

> [!NOTE]
> Para obter suporte do Chrome, tem de utilizar o Windows 10 criadores Update (versão 1703) ou posterior.<br>
> Pode instalar [esta extensão](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji).

### <a name="supported-mobile-applications-and-desktop-clients"></a>Clientes de ambiente de trabalho e aplicações móveis suportadas

Controlar o acesso de aplicação e o cliente utilizando o **clientes de ambiente de trabalho e aplicações móveis** opção na sua política de acesso condicional. É concedido o acesso apenas se a tentativa de acesso for feita por um cliente de ambiente de trabalho ou aplicação móvel suportado. Quando é efetuada uma tentativa de acesso por uma aplicação não suportada ou um cliente, a tentativa de está bloqueada.

![Controlo de acesso para aplicações móveis suportadas ou clientes de ambiente de trabalho](./media/active-directory-conditional-access-technical-reference/06.png)

Os clientes de ambiente de trabalho e as seguintes aplicações móveis suportam o acesso condicional para o Office 365 e outras aplicações de serviço ligado do AD do Azure:


| Aplicações de cliente| Serviço de destino| Plataforma |
| --- | --- | --- |
| Aplicação remota do Azure| Remoto App service do Azure| Windows 10, Windows 8.1, Windows 7, iOS, Android e Mac OS X|
| Aplicação do Dynamics CRM| Dynamics CRM| Windows 10, Windows 8.1, Windows 7, iOS e Android|
| Aplicação de correio/calendário/pessoas, Outlook 2016, Outlook 2013 (com a autenticação moderna)| Office 365 Exchange Online| Windows 10|
| Política de MFA e a localização para aplicações. Não são suportadas políticas de dispositivo baseada-se.| Qualquer serviço de aplicações de aplicações My| Android e iOS|
| Serviços de equipas da Microsoft - Isto controla todos os serviços que suportam Teams da Microsoft e todas as respetivas aplicações de cliente - ambiente de trabalho do Windows, iOS, Android, WP e cliente web| Microsoft Teams| Windows 10, Windows 8.1, Windows 7, iOS e Android|
| Cliente de sincronização de aplicações do Office 2016, Office 2013 (com a autenticação moderna), no OneDrive (consulte [notas](https://support.office.com/en-US/article/Azure-Active-Directory-conditional-access-with-the-OneDrive-sync-client-on-Windows-028d73d7-4b86-4ee0-8fb7-9a209434b04e))| Office 365 SharePoint Online| Windows 8.1, Windows 7|
| Aplicações do Office 2016, Universal Office aplicações, Office 2013 (com a autenticação moderna), cliente de sincronização do OneDrive (consulte [notas](https://support.office.com/en-US/article/Azure-Active-Directory-conditional-access-with-the-OneDrive-sync-client-on-Windows-028d73d7-4b86-4ee0-8fb7-9a209434b04e)), o suporte de grupos do Office é planeado para o futuro, suporte para aplicações do SharePoint é planeada para o futuro| Office 365 SharePoint Online| Windows 10|
| Office 2016 para macOS (Word, Excel, PowerPoint, OneNote apenas). OneDrive para empresas o suporte para o futuro| Office 365 SharePoint Online| Mac OS X|
| Aplicações móveis do Office| Office 365 SharePoint Online| iOS, Android|
| Aplicação do Office Yammer| Yammer do Office 365| Windows 10, iOS, Android|
| Outlook 2016 (Office para macOS)| Office 365 Exchange Online| Mac OS X|
| Outlook 2016, o Outlook 2013 (com a autenticação moderna), o Skype para empresas (com a autenticação moderna)| Office 365 Exchange Online| Windows 8.1, Windows 7|
| Aplicação móvel do Outlook| Office 365 Exchange Online| iOS|
| Aplicação do Power BI. A aplicação Power BI para Android não suporta acesso condicional baseado no dispositivo.| Serviço do Power BI| Windows 10, Windows 8.1, Windows 7 e iOS|
| Skype para empresas| Office 365 Exchange Online| Android, IOS|
| Visual Studio Team Services aplicação| Visual Studio Team Services| Windows 10, Windows 8.1, Windows 7, iOS e Android|



## <a name="approved-client-app-requirement"></a>Requisito da aplicação de cliente aprovada 

Controlar as ligações de cliente utilizando o **requerem a aplicação de cliente aprovada** opção na sua política de acesso condicional. É concedido o acesso apenas quando é efetuada uma tentativa de ligação por uma aplicação de cliente aprovada.

![Controlo de acesso para aplicações de cliente aprovada](./media/active-directory-conditional-access-technical-reference/21.png)

As seguintes aplicações de cliente podem ser utilizadas com o requisito da aplicação cliente aprovada:

- Microsoft Excel

- Microsoft OneDrive

- Microsoft Outlook

- Microsoft OneNote

- Microsoft PowerPoint

- Microsoft SharePoint

- Microsoft Skype para empresas

- Microsoft Teams

- Microsoft Visio

- Microsoft Word


**Observações**

- As aplicações de cliente aprovada suportam a funcionalidade de gestão de aplicações móveis do Intune.

- O **requerem a aplicação de cliente aprovada** requisito:

    - Só suporta o iOS e Android para [condição de plataforma de dispositivo](#device-platforms-condition).

    - Não suporta o **Browser** opção para o [condição de aplicações de cliente](#supported-browsers).
    
    - Substitui o **clientes de ambiente de trabalho e aplicações móveis** opção para o [condição de aplicações de cliente](#supported-mobile-apps-and-desktop-clients) quando esta opção está selecionada.


## <a name="next-steps"></a>Passos seguintes

- Para obter uma descrição geral do acesso condicional, consulte [acesso condicional no Azure Active Directory](active-directory-conditional-access-azure-portal.md).
- Se estiver pronto para configurar políticas de acesso condicional no seu ambiente, consulte o [práticas de acesso condicional no Azure Active Directory recomendadas](active-directory-conditional-access-best-practices.md).



<!--Image references-->
[1]: ./media/active-directory-conditional-access-technical-reference/01.png



---
title: "Inscrição self-service ou viral no Azure Active Directory | Microsoft Docs"
description: "Utilize a inscrição self-service num inquilino do Azure Active Directory (Azure AD)"
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: b9f01876-29d1-4ab8-8b74-04d43d532f4b
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/03/2017
ms.author: curtand
ms.reviewer: elkuzmen
ms.custom: it-pro
ms.openlocfilehash: 762627a1d743922d680d90d1065ae80fbd3f4420
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2017
---
# <a name="what-is-self-service-signup-for-azure-active-directory"></a>O que é a inscrição self-service do Azure Active Directory?
Este artigo explica inscrição self-service e como o suportam no Azure Active Directory (Azure AD). Se pretender assumir um nome de domínio de um Azure AD não gerido de inquilino, consulte [assumir um diretório não gerido como administrador](domains-admin-takeover.md).

## <a name="why-use-self-service-signup"></a>Porquê utilizar a inscrição self-service?
* Obter os clientes que pretendem mais rápida nos serviços
* Criar ofertas baseados em e-mail para um serviço
* Criar fluxos de inscrição baseados em e-mail que rapidamente permitem aos utilizadores criar identidades utilizando os aliases de e-mail do trabalho fácil de lembrar
* Um self-service criadas diretório do Azure AD pode ser ativado para um diretório gerido que pode ser utilizado para outros serviços

## <a name="terms-and-definitions"></a>Termos e definições
* **Inscrição self-service**: Este é o método pelo qual um utilizador se inscreve num serviço em nuvem e tem uma identidade criada automaticamente para os mesmos no Azure AD com base no respetivo domínio de correio eletrónico.
* **Não gerido diretório do Azure AD**: Este é o diretório onde essa identidade é criada. Um diretório não gerido é um diretório com nenhum administrador global.
* **Verificar o e-mail de utilizador**: Este é um tipo de conta de utilizador no Azure AD. Um utilizador que tem uma identidade criada automaticamente depois de inscrever-se para uma oferta de self-service é conhecido como um utilizador verificados por e-mail. Um utilizador verificado de e-mail é um membro de um diretório etiquetado com creationmethod regular = EmailVerified.

## <a name="how-do-i-control-self-service-settings"></a>Como controlar o self-service definições?
Administradores tem dois controlos self-service hoje. Podem controlar o se:

* Os utilizadores podem associar o diretório através de e-mail.
* Os utilizadores podem licenças si próprios para aplicações e serviços.

### <a name="how-can-i-control-these-capabilities"></a>Como posso controlar estas capacidades?
Um administrador pode configurar estas capacidades utilizando os seguintes parâmetros do cmdlet Set-MsolCompanySettings do Azure AD:

* **AllowEmailVerifiedUsers** controla se um utilizador pode criar ou associar um diretório de não gerido. Se definir esse parâmetro para $false, nenhum utilizador verificado de e-mail pode associar o diretório.
* **AllowAdHocSubscriptions** controla a capacidade dos utilizadores efetuar a inscrição self-service. Se definir esse parâmetro para $false, nenhum utilizador pode efetuar a inscrição self-service.

### <a name="how-do-the-controls-work-together"></a>Como os controlos funcionam em conjunto?
Estes dois parâmetros podem ser utilizados em conjunto para definir um controlo mais preciso sobre inscrição self-service. Por exemplo, o comando seguinte irá permitir aos utilizadores efetuar a inscrição self-service, mas apenas se os utilizadores já tiverem uma conta no Azure AD (por outras palavras, os utilizadores que teriam uma conta de e-mail verificada a ser criado pela primeira vez não é possível efetuar a inscrição self-service):

````
    Set-MsolCompanySettings -AllowEmailVerifiedUsers $false -AllowAdHocSubscriptions $true
````
O fluxograma a seguir explica diferentes combinações para estes parâmetros e as condições resultantes para o diretório e a inscrição self-service.

![][1]

Para obter mais informações e exemplos de como utilizar estes parâmetros, consulte [Set-MsolCompanySettings](/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0).

## <a name="next-steps"></a>Passos seguintes
* [Adicionar um nome de domínio personalizado ao Azure AD](add-custom-domain.md)
* [Como instalar e configurar o Azure PowerShell](/powershell/azure/overview)
* [Azure PowerShell](/powershell/azure/overview)
* [Referência de Cmdlet do Azure](/powershell/azure/get-started-azureps)
* [Set-MsolCompanySettings](/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0)

<!--Image references-->
[1]: ./media/active-directory-self-service-signup/SelfServiceSignUpControls.png

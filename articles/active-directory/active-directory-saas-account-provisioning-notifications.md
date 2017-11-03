---
title: "Conta de notificações de aprovisionamento | Microsoft Docs"
description: "Saiba como Certifique-se de que são notificados de problemas relacionados com o aprovisionamento de utilizadores que necessitam da sua atenção ao ativar as notificações de aprovisionamento da conta."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: a637aac7-f06b-48ef-a66d-639835a8edec
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/17/2017
ms.author: markvi
ms.openlocfilehash: a59e9805bd4b694a44a6c83382cce2872530cde3
ms.sourcegitcommit: 6acb46cfc07f8fade42aff1e3f1c578aa9150c73
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/18/2017
---
# <a name="account-provisioning-notifications"></a>Notificações do Aprovisionamento de Contas
Com o aprovisionamento de utilizadores, pode automatizar o processo de gestão de utilizadores em aplicações de SaaS de terceiros. <br>
Apesar de ser um processo automatizado, ocasionalmente é necessária a interação com este processo. <br>
Isto é, por exemplo o caso, quando a palavra-passe da conta que configurou para trocar dados com uma terceira SaaS expiraram aplicações de terceiros. 

Ao ativar as notificações de aprovisionamento da conta, pode certificar-se de que são notificados de problemas relacionados com o aprovisionamento de utilizadores que necessitam da sua atenção.

Ativar ou desativar notificações de aprovisionamento como parte da sua configuração para uma aplicação SaaS de terceiros de aprovisionamento de utilizador da conta.

![Aprovisionamento de utilizadores][1] 

Para ativar notificações de aprovisionamento da conta, selecione a caixa de verificação relacionada no **confirmação** página da caixa de diálogo e, em seguida, escreva o alias de e-mail do destinatário.

![Notificações do Aprovisionamento de Contas][2]

Pode introduzir uma lista de distribuição como recipient; No entanto, é importante ter em atenção que o e-mail de notificação contém ligações para os relatórios só podem ser acedidos por administradores do AD do Azure.

Se tiver ativadas de notificações de aprovisionamento da conta, irá receber mensagens de correio eletrónico sobre problemas críticos que estão relacionados com o aprovisionamento de utilizadores. No entanto, para evitar uma sobrecarga de e-mail, apenas receberá um e-mail de notificação por dia para cada aplicação SaaS que o e-mail de notificação está ativado para.

## <a name="related-articles"></a>Artigos relacionados
* [Índice de Artigos da Gestão da Aplicação no Azure Active Directory](active-directory-apps-index.md)
* [Automatizar utilizador aprovisionamento/desaprovisionamento para aplicações SaaS](active-directory-saas-app-provisioning.md)
* [Personalizar os mapeamentos de atributos para o aprovisionamento de utilizador](active-directory-saas-customizing-attribute-mappings.md)
* [Escrever expressões para mapeamentos de atributos](active-directory-saas-writing-expressions-for-attribute-mappings.md)
* [Filtros de âmbito para o aprovisionamento de utilizador](active-directory-saas-scoping-filters.md)
* [Utilizar o SCIM para ativar o aprovisionamento automático de utilizadores e grupos do Azure Active Directory a aplicações](active-directory-scim-provisioning.md)
* [Lista de tutoriais sobre como integrar aplicações SaaS](active-directory-saas-tutorial-list.md)

<!--Image references-->
[1]: ./media/active-directory-saas-account-provisioning-notifications/ic766307.png
[2]: ./media/active-directory-saas-account-provisioning-notifications/ic766308.png

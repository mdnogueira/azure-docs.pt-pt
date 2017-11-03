---
title: Controlos de acesso condicional do Azure Active Directory | Microsoft Docs
description: Saiba como funcionam os controlos de acesso condicional do Azure Active Directory.
services: active-directory
keywords: "acesso condicional para aplicações, o acesso condicional com o Azure AD, o acesso seguro aos recursos da empresa, as políticas de acesso condicional"
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/25/2017
ms.author: markvi
ms.reviewer: calebb
ms.openlocfilehash: be3631db20ae744965f9f6677c536ade45e34c49
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="controls-in-azure-active-directory-conditional-access"></a>Controlos de acesso condicional do Azure Active Directory 

Com [acesso condicional do Azure Active Directory (Azure AD)](active-directory-conditional-access-azure-portal.md), pode controlar o acesso de utilizadores autorizados como as suas aplicações em nuvem. Na política de acesso condicional, é possível definir a resposta ("fazê-lo") para uma condição específica ("quando isto acontece"). No contexto do acesso condicional, 

- "**Quando isto acontece**" denomina **declaração de condição**

- "**, Em seguida, fazer isto**" denomina **controlos**

![Controlo](./media/active-directory-conditional-access-controls/11.png)

A combinação de uma instrução de condição com os controlos representa uma política de acesso condicional.

![Controlo](./media/active-directory-conditional-access-controls/12.png)

Cada controlo é o um requisito que tem de ser cumprido por pessoa ou sistema a iniciar sessão ou uma restrição para que o utilizador pode fazê-lo Depois de iniciar sessão. 

Existem dois tipos de controlos: 

- **Controla a conceder** - para acesso de porta

- **Controlos de sessão** - para restringir o acesso dentro de uma sessão

Este tópico explica os vários controlos que estão disponíveis no acesso condicional do Azure AD. 

## <a name="grant-controls"></a>Controlos de conceder

Com controlos de conceder, pode bloquear o acesso totalmente ou permitir o acesso com requisitos adicionais ao selecionar os controlos pretendidos. Para os vários controlos, pode exigir a:

- Selecionar todos os controlos para ser cumprido (*e*) 
- Selecionar um controlo a ser cumprido (*ou*)

![Controlo](./media/active-directory-conditional-access-controls/17.png)



### <a name="multi-factor-authentication"></a>Multi-Factor Authentication

Pode utilizar este controlo para exigir a autenticação multifator, aceda à aplicação de nuvem especificada. Este controlo suporta os seguintes fornecedores de multifator: 

- Multi-Factor Authentication do Azure 

- Um fornecedor de autenticação multifator no local, combinado com os serviços de Federação do Active Directory (AD FS).
 
Utilizar multi-factor authentication ajuda a proteger os recursos de que está a ser acedida por um utilizador não autorizado que poderá ter adquirido o acesso às credenciais de principais de um utilizador válido.



### <a name="compliant-device"></a>Dispositivo conforme

Pode configurar políticas de acesso condicional baseado no dispositivo. O objetivo de uma política de acesso condicional baseado no dispositivo é conceder acesso aos recursos configurados apenas a partir de dispositivos fidedignos. A necessidade de um dispositivo com conformidade é uma opção tem de definir que um dispositivo fidedigno. Para obter mais informações, consulte [configurar políticas de acesso condicional baseado no dispositivo do Azure Active Directory](active-directory-conditional-access-policy-connected-applications.md).

### <a name="domain-joined-device"></a>Dispositivos associados a um domínio

Tem necessidade de que um dispositivo associado ao domínio é outra opção Configurar políticas de acesso condicional baseado no dispositivo. Este requisito refere-se a computadores de secretária, portáteis e tablets empresarial que estão associados a um Active Directory no local do Windows. Para obter mais informações, consulte [configurar políticas de acesso condicional baseado no dispositivo do Azure Active Directory](active-directory-conditional-access-policy-connected-applications.md).





### <a name="approved-client-app"></a>Aplicação de cliente aprovada

Uma vez que os seus empregados utilizam dispositivos móveis para ambos os pessoal e tarefas de trabalho, pode querer ter a capacidade de proteger os dados da empresa acedidos através de dispositivos, mesmo nos casos em que não são geridos por si.
Pode utilizar [políticas de proteção de aplicações do Intune](https://docs.microsoft.com/intune/app-protection-policy) para ajudar a proteger os dados da empresa independentes de qualquer solução de gestão de dispositivos móveis (MDM).


Com as aplicações de cliente aprovada, pode exigir a uma aplicação de cliente tenta aceder às suas aplicações em nuvem para suportar [políticas de proteção de aplicações do Intune](https://docs.microsoft.com/intune/app-protection-policy). Por exemplo, pode restringir o acesso ao Exchange Online para a aplicação do Outlook. Uma política de acesso condicional que requer que as aplicações de cliente aprovada também é conhecido como [política de acesso condicional baseado na aplicação](active-directory-conditional-access-mam.md). Para obter uma lista de aplicações de cliente aprovados suportados, consulte [aprovados requisito da aplicação de cliente](active-directory-conditional-access-technical-reference.md#approved-client-app-requirement).


### <a name="terms-of-use"></a>Termos de Utilização

Pode exigir um utilizador no seu inquilino para os termos de utilização antes de ser concedido acesso a um recurso. Como administrador, pode configurar e personalizar os termos de utilização através do carregamento de um documento PDF. Se um utilizador estiver âmbito deste controlo de acesso a uma aplicação é concedido apenas se tem sido Aceito os termos de utilização. 


### <a name="custom-controls"></a>Controlos personalizados 

Pode criar controlos personalizados no acesso condicional que redireciona os utilizadores a um serviço compatível para satisfazer a requisitos fora do Azure Active Directory. Isto permite-lhe utilizar determinados externo multi-factor authentication e fornecedores de verificação para impor regras de acesso condicional, ou para criar os seus próprios serviços personalizada. Para satisfazer este controlo, browser do utilizador é redirecionado para o serviço externo, efetua qualquer autenticação necessária ou de atividades de validação e, em seguida, é redirecionado para o Azure Active Directory. Se o utilizador foi autenticado ou validado com sucesso, o utilizador continua no fluxo de acesso condicional. 

## <a name="custom-controls"></a>Controlos personalizados

Controlos personalizados no acesso condicional redirecionar os utilizadores a um serviço compatível para satisfazer a requisitos fora do Azure Active Directory. Para satisfazer este controlo, browser do utilizador é redirecionado para o serviço externo, efetua qualquer autenticação necessária ou de atividades de validação e, em seguida, é redirecionado para o Azure Active Directory. Azure Active Directory verifica a resposta e, se o utilizador foi autenticado ou validado com sucesso, o utilizador continua no fluxo de acesso condicional.

Estes controlos permitem a utilização de determinados serviços externos ou personalizadas como controlos de acesso condicional e geralmente expandem as capacidades de acesso condicional.

Fornecedores atualmente oferece um serviço compatível incluem:

- Segurança Duo

- RSA

- Trusona

Para obter mais informações sobre os serviços, contacte diretamente os fornecedores.

### <a name="creating-custom-controls"></a>Criação de controlos personalizados

Para criar um controlo personalizado, primeiro deve contactar o fornecedor de que pretende utilizar. Cada fornecedor de terceiros tem o seu próprio processo e os requisitos para inscrever, subscrever ou, caso contrário, tornar-se uma parte do serviço e para indicar que pretende integrar com o acesso condicional. Nessa altura, o fornecedor irá fornecer-lhe um bloco de dados no formato JSON. Estes dados permite que o fornecedor e o acesso condicional para funcionarem em conjunto para o seu inquilino, cria o novo controlo e define como o acesso condicional é saber se os seus utilizadores tem efetuado com êxito verificação com o fornecedor.

Copie os dados JSON e, em seguida, cole-o a caixa de texto relacionada. Não efetue as alterações JSON, a menos que compreende explicitamente a alteração que está a efetuar. Fazer qualquer alteração foi quebrar a ligação entre o fornecedor e a Microsoft e, potencialmente, bloquear e os seus utilizadores a contas.

A opção para criar um controlo personalizado está no **gerir** secção o **acesso condicional** página.

![Controlo](./media/active-directory-conditional-access-controls/82.png)

Ao clicar em **novo controlo personalizado**, abre um painel com uma caixa de texto para os dados JSON do controlo.  


![Controlo](./media/active-directory-conditional-access-controls/81.png)


### <a name="deleting-custom-controls"></a>Eliminar controlos personalizados

Para eliminar um controlo personalizado, primeiro tem de se certificar de que não está a ser utilizada em qualquer política de acesso condicional. Uma vez concluída:

1. Aceda à lista de controlos personalizados

2. Clique em...  

3. Selecione **Eliminar**.

### <a name="editing-custom-controls"></a>Editar controlos personalizados

Para editar um controlo personalizado, tem de eliminar o controlo atual e criar um novo controlo com as informações atualizadas.




## <a name="session-controls"></a>Controlos de sessão

Controlos de sessão ativar experiência limitada dentro de uma aplicação de nuvem. Os controlos de sessão são impostos pelo aplicações na nuvem e dependem das informações adicionais fornecidas pelo Azure AD para a aplicação sobre a sessão.

![Controlo](./media/active-directory-conditional-access-controls/31.png)

### <a name="use-app-enforced-restrictions"></a>Utilize as restrições da aplicação imposta

Pode utilizar este controlo para exigir o Azure AD para passar as informações do dispositivo para a aplicação de nuvem. Isto ajuda a aplicação na nuvem saber se o utilizador for proveniente de um dispositivo compatível com ou um dispositivo associado a um domínio. Este controlo está atualmente só é suportada com o SharePoint que a aplicação de nuvem. SharePoint utiliza as informações do dispositivo para fornecer aos utilizadores uma experiência completa ou limitada, consoante o estado do dispositivo.
Para obter mais informações sobre como exigir acesso limitado com o SharePoint, consulte [controlar o acesso a partir de dispositivos não geridos](https://aka.ms/spolimitedaccessdocs).



## <a name="next-steps"></a>Passos seguintes

- Se quiser saber como configurar uma política de acesso condicional, consulte [introdução ao acesso condicional no Azure Active Directory](active-directory-conditional-access-azure-portal-get-started.md).

- Se estiver pronto para configurar políticas de acesso condicional para o seu ambiente, consulte o [melhores práticas para acesso condicional no Azure Active Directory](active-directory-conditional-access-best-practices.md). 

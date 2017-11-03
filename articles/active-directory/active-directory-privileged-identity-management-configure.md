---
title: Configurar o Azure AD Privileged Identity Management | Microsoft Docs
description: "Um tópico que explica o que é o Azure AD Privileged Identity Management e como utilizar o PIM para melhorar a segurança de nuvem."
services: active-directory
documentationcenter: 
author: barclayn
manager: mbaldwin
editor: 
ms.assetid: c548ed2e-06e3-4eaf-a63d-0f02ee72da25
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/18/2017
ms.author: barclayn
ms.custom: pim
ms.openlocfilehash: 0318f3546dbd869e8b975ebf7047f3676c227101
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="what-is-azure-ad-privileged-identity-management"></a>O que é o Azure AD Privileged Identity Management?

Com o Azure Active Directory (AD) Privileged Identity Management, pode gerir, controlar e monitorizar o acesso dentro da sua organização. Isto inclui acesso a recursos no Azure AD, recursos do Azure (pré-visualização), e outros serviços Online da Microsoft, como do Office 365 ou o Microsoft Intune.

> [!NOTE]
> Privileged Identity Management está disponível para toda a organização quando licença os administradores de com a edição Premium P2 do Azure Active Directory. Para obter mais informações, consulte [Edições do Azure Active Directory](active-directory-editions.md).

As organizações pretendem minimizar o número de pessoas que têm acesso para proteger informações ou recursos, porque o que reduz a possibilidade de um utilizador mal intencionado obter esse acesso ou um utilizador autorizado inadvertidamente afetar um recurso confidencial.  No entanto, os utilizadores têm de realizar operações privilegiadas no Azure AD, as aplicações do Azure, Office 365 ou SaaS. As organizações podem conceder acesso de com privilégios de utilizadores aos recursos do Azure como subscrições e o Azure AD. Há uma necessidade de supervisão para que os utilizadores estão a fazer com os respetivos privilégios de administrador. O Azure AD Privileged Identity Management ajuda a mitigar o risco de excessiva, desnecessários ou indevidamente direitos de acesso.

O Azure AD Privileged Identity Management ajuda a sua organização:

- Consulte os utilizadores que estão atribuídos a funções com privilégios para gerir recursos do Azure (pré-visualização), bem como os utilizadores que estão atribuídos a funções administrativas no Azure AD
- Ativar a pedido, "just in time" acesso administrativo ao Microsoft Online Services como o Office 365 e o Intune e aos recursos do Azure (pré-visualização) de subscrições, grupos de recursos e recursos individuais, tais como máquinas virtuais 
-   Ver um histórico de ativação de administrador, incluindo a que os administradores das alterações efetuadas aos recursos do Azure (pré-visualização)
- Obtenha alertas sobre as alterações em atribuições de administrador
- Exigir a aprovação para ativar as funções de administrador do Azure AD privilegiado (pré-visualização) 
- Reveja a associação a funções administrativas e exigir que os utilizadores fornecer uma justificação para associação contínua

No Azure AD, o Azure AD Privileged Identity Management pode gerir os utilizadores atribuídos incorporada funções organizacionais do Azure AD, como Administrador Global. No Azure, Azure AD Privileged Identity Management pode gerir os utilizadores e grupos atribuídos através de funções de RBAC do Azure, incluindo o proprietário ou contribuinte.

## <a name="just-in-time-administrator-access"></a>Apenas no acesso de administrador de tempo

Historicamente, pode atribuir um utilizador a uma função de administrador através do portal do Azure, outros portais do Microsoft Online Services ou os cmdlets do Azure AD no Windows PowerShell. Como resultado, esse utilizador torna-se um **administrador permanente**, sempre ativo na função atribuída. O Azure AD Privileged Identity Management apresenta o conceito de uma **admin elegível**. Administradores elegível devem ter acedem de utilizadores que necessitam de privilégios agora e, em seguida, mas não all-day, todos os dias. A função está inativa até que o utilizador precisa de acesso, em seguida, concluir um processo de ativação e tornar-se um administrador do Active Directory para um período de tempo predefinido. Escolher mais as organizações a utilizar esta abordagem para reduzir ou eliminando "acesso de administrador de colocado" funções com privilégios.

## <a name="enable-privileged-identity-management-for-your-directory"></a>Ativar o Privileged Identity Management para o seu diretório

Pode começar a utilizar o Azure AD Privileged Identity Management no [portal do Azure](https://portal.azure.com/).

> [!NOTE]
> Tem de ser um administrador global com uma conta institucional (por exemplo, @yourdomain.com), não uma conta Microsoft (por exemplo, @outlook.com), para ativar o Azure AD Privileged Identity Management para um diretório.

1. Inicie sessão no [Portal do Azure](https://portal.azure.com/) como administrador global do diretório.
2. Se a organização tiver mais do que um diretório, selecione o nome de utilizador no canto superior direito do Portal do Azure. Selecione o diretório onde utilizará o Azure AD Privileged Identity Management.
3. Selecione **Mais serviços** e utilize a caixa de texto Filtro para procurar **Azure AD Privileged Identity Management**.
4. Marque **Afixar ao dashboard** e, em seguida, clique em **Criar**. A aplicação Privileged Identity Management abre.

Se estiver a primeira pessoa a utilizar o Azure AD Privileged Identity Management no diretório e navegar para funções de diretório do Azure AD, aand navegar para funções de diretório do Azure AD, uma [Assistente de segurança](active-directory-privileged-identity-management-security-wizard.md) orienta-o inicial experiência de atribuição. Após a que fiquem automaticamente o primeiro **administrador de segurança** e **administrador com função privilegiada** do diretório.

Para funções do Azure AD, apenas um utilizador que está na função de administrador com privilégios de função pode gerir atribuições de outros administradores no Azure AD PIM. Pode [conceder outros utilizadores a capacidade de gerir funções de diretório no PIM](active-directory-privileged-identity-management-how-to-give-access-to-pim.md). Os administradores globais, administradores de segurança e leitores de segurança podem ver atribuições de funções do Azure AD no Azure AD PIM.
Para funções do RBAC do Azure, apenas um administrador de subscrição, um proprietário de recursos ou um administrador de acesso de utilizador de recurso pode gerir atribuições de outros administradores no Azure AD PIM.  Os utilizadores que são administradores com privilégios de função, os administradores de segurança ou leitores de segurança, não por predefinição, tem acesso para ver as atribuições de funções de RBAC do Azure no Azure AD PIM.

## <a name="privileged-identity-management-overview-entry-point"></a>Descrição geral da gestão de identidade de privilégio (ponto de entrada)

O Azure AD Privileged Identity Management suporta administração de funções de diretório do Azure AD e funções de recursos do Azure (pré-visualização). A função das funções para recursos do Azure diferem das funções administrativas no Azure AD. Funções de recursos do Azure fornecem permissões granulares para o recurso que estão atribuídos e todos os recursos subordinados na hierarquia de recursos (conhecido como herança). [Saiba mais sobre RBAC, a hierarquia de recursos e herança](role-based-access-control-configure.md). PIM para as funções de diretório do Azure AD e de recursos do Azure (pré-visualização) podem ser administrado acedendo na ligação respetiva na secção Gerir do menu de navegação à esquerda de ponto de entrada de descrição geral do PIM.

PIM fornece acesso prático para ativar funções, ver ativações/pedidos pendentes aprovações (para funções de diretório do Azure AD), pendentes e revê pendentes a resposta da secção de tarefas do menu de navegação esquerdo.

Quando aceder qualquer um dos itens de menu tarefas a partir do ponto de entrada de descrição geral, a vista resultante contém resultados para as funções de diretório do Azure AD e funções de recursos do Azure (pré-visualização).

![Início rápido](./media/active-directory-privileged-identity-management-configure/quick-start.png)

As minhas funções contenham uma lista de atribuições de funções de Active Directory e elegível para funções de diretório do Azure AD e funções de recursos do Azure (pré-visualização). [Saiba mais sobre a ativação do atribuições de funções elegível](active-directory-privileged-identity-management-how-to-activate-role.md).

Ativar funções para recursos do Azure (pré-visualização) apresenta uma nova experiência que permite aos membros elegíveis de uma função agendar ativação para uma data/hora no futuro e selecione uma duração de ativação específico no máximo permitido pelos administradores.

![](./media/active-directory-privileged-identity-management-configure/activations.png)

Nos eventos já não é necessária uma ativação agendada, os utilizadores podem cancelar os respetivos pedido pendente, ao navegar para pedidos pendentes a partir do menu de navegação esquerdo e clicando a cancelar botão em linha com esse pedido.

![pedidos pendentes](./media/active-directory-privileged-identity-management-configure/pending-requests.png)

## <a name="privileged-identity-management-admin-dashboard"></a>Dashboard de administração do Privileged Identity Management

O Azure AD Privileged Identity Manager fornece um dashboard de administrador que dá-lhe informações importantes, tais como:

* Alertas que o ponto de oportunidades para melhorar a segurança
* O número de utilizadores que são atribuídos a cada função com privilégios  
* O número de administradores permanentes e elegíveis
* Um gráfico de ativações com função privilegiada no seu diretório
*   O número de Just-In-Time, limite de tempo e permanente atribuições de funções de recursos do Azure (pré-visualização)
*   Utilizadores e grupos com novo atribuições de funções nos últimos 30 dias (funções de recursos do Azure)


![Dashboard PIM - captura de ecrã][2]

## <a name="privileged-role-management"></a>Gestão de funções com privilégios

Com o Azure AD Privileged Identity Management, pode gerir os administradores adicionando ou removendo administradores permanentes ou elegíveis para cada função para funções de diretório do Azure AD. Com o PIM para recursos do Azure (pré-visualização), os proprietários, os administradores de acesso de utilizador e os administradores globais que ativar a gestão de subscrições no seu inquilino podem atribuir utilizadores ou grupos às funções de recursos do Azure como elegíveis (acesso Just-In-Time), ou vínculo de tempo ( acesso de ativação não necessário) com um início e fim de data/hora ou permanente (se estiver ativada nas definições de função).

![Administradores de adicionar/remover PIM - captura de ecrã][3]

## <a name="configure-the-role-activation-settings"></a>Configurar as definições de ativação de função

Utilizar o [definições da função](active-directory-privileged-identity-management-how-to-change-default-settings.md) , pode configurar as propriedades de ativação de função elegível para funções de diretório do Azure AD, incluindo:

* A duração do período de ativação de função
* A notificação de ativação de função
* As informações de que um utilizador tem de fornecer durante o processo de ativação de função
* Número de incidente ou pedido de serviço
* [Requisitos de fluxo de trabalho de aprovação - Preview](./privileged-identity-management/azure-ad-pim-approval-workflow.md)

![Captura de ecrã do PIM definições - ativação do administrador-][4]

Tenha em atenção que a imagem, os botões para **multi-factor Authentication** estão desativadas. Para determinados, altamente privilegiadas funções, iremos exigir a MFA para uma proteção.

Definições de função para funções de recursos do Azure (pré-visualização) permitem aos administradores configurar Just-In-Time e definições de atribuição direta, incluindo:

- A capacidade de atribuir o utilizador ou grupos às funções sem uma data/hora de fim (atribuição permanente)
- A duração predefinida de uma atribuição (quando não permanente)
- A duração máxima de ativação (quando um membro da função elegível ativa)
- As informações de um utilizador tem de fornecer durante a ativação de função (Just-In-Time atribuições) ou o processo de atribuição (atribuições diretos)

![](./media/active-directory-privileged-identity-management-configure/role-settings-details.png)

## <a name="role-activation"></a>Ativação da função

Para [ativar uma função](active-directory-privileged-identity-management-how-to-activate-role.md), um administrador elegível solicita um limite de tempo "ativação" para a função. A ativação pode ser pedida utilizando o **ativar meu função** opção no Azure AD Privileged Identity Management.

Um administrador que pretende ativar uma função tem de iniciar o Azure AD Privileged Identity Management no portal do Azure.

Ativação da função é personalizável. Nas definições do PIM, pode determinar o comprimento de ativação e o que o administrador tem de fornecer para ativar a função de informações.

![Ativação de função de pedido de administrador PIM - captura de ecrã][5]

## <a name="review-role-activity"></a>Actividade de função de revisão

Existem duas formas de controlar a forma como os seus funcionários e admins estiver a utilizar funções com privilégios. Está a utilizar a primeira opção [funções do histórico de auditorias](active-directory-privileged-identity-management-how-to-use-audit-log.md). O histórico de auditoria regista registar alterações em atribuições de função privilegiada, histórico de ativação de função, e e as alterações às definições de funções de recursos do Azure (pré-visualização). 

![Histórico de ativação do PIM - captura de ecrã][6]

A segunda opção consiste em Configurar regular [aceder revisões](active-directory-privileged-identity-management-how-to-start-security-review.md). Estes revisões de acesso podem ser realizadas e atribuídos revisor (como um Gestor de equipa) ou os empregados podem rever si próprios. Esta é a melhor forma de monitorizar quem ainda necessita de acesso, e que já não faz a.

## <a name="azure-ad-pim-at-subscription-expiration"></a>Azure AD PIM em expiração da subscrição

Um inquilino tem de ter uma subscrição do Azure AD Premium P2 (ou do EMS E5) avaliação ou paga no seu inquilino antes de utilizar o Azure AD PIM.  Além disso, as licenças devem ser atribuídas aos administradores do inquilino.  Especificamente, as licenças devem ser atribuídas aos administradores nas funções do Azure AD geridos através do Azure AD PIM, os administradores de funções de RBAC do Azure é gerido através do Azure AD PIM e os utilizadores de não administrador que efetuar revisões de acesso.
Se a sua organização não renovar o Azure AD Premium P2 ou a versão de avaliação expira, as funcionalidades do Azure AD PIM deixará de estar disponíveis no seu inquilino, atribuições de funções elegível serão removidas e os utilizadores já não será possível ativar funções. Pode ler mais no [requisitos da subscrição do Azure AD PIM](./privileged-identity-management/subscription-requirements.md)

## <a name="next-steps"></a>Passos seguintes

[!INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!--Image references-->

[1]: ./media/active-directory-privileged-identity-management-configure/PIM_EnablePim.png
[2]: ./media/active-directory-privileged-identity-management-configure/PIM_Admin_Overview.png
[3]: ./media/active-directory-privileged-identity-management-configure/PIM_AddRemove.png
[4]: ./media/active-directory-privileged-identity-management-configure/PIM_Settings_w_Approval_Disabled.png
[5]: ./media/active-directory-privileged-identity-management-configure/PIM_RequestActivation.png
[6]: ./media/active-directory-privileged-identity-management-configure/PIM_ActivationHistory.png

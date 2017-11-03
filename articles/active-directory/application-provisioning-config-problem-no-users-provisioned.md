---
title: "Não estão a ser aprovisionados para uma aplicação de galeria do Azure AD | Microsoft Docs"
description: "Como resolver problemas comuns deparam quando não for apresentada aos utilizadores numa um Azure AD aplicação de galeria que configurou para o aprovisionamento de utilizador com o Azure AD"
services: active-directory
documentationcenter: 
author: ajamess
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/04/2017
ms.author: asteen
ms.openlocfilehash: 30db71c0706de1dcc0d48fb72fa5d62475a14ba7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="no-users-are-being-provisioned-to-an-azure-ad-gallery-application"></a>Não estão a ser aprovisionados para uma aplicação de galeria do Azure AD

Depois de aprovisionamento automático foi configurado para uma aplicação (incluindo verificar que as credenciais de aplicação fornecidas para o Azure AD para ligar à aplicação são válidas). Em seguida, os utilizadores e/ou grupos são aprovisionados para a aplicação e é determinado pelos seguintes ações:

-   Que utilizadores e grupos foram **atribuído** à aplicação. Para obter mais informações sobre a atribuição, consulte [atribuir um utilizador ou grupo a uma aplicação empresarial no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal).

-   Se pretende ou não **mapeamentos de atributos** estiver ativado e configurado para sincronizar atributos válidos do Azure AD para a aplicação. Para obter mais informações sobre os mapeamentos de atributos, consulte [personalizar aprovisionamento atributo mapeamentos de utilizador para aplicações de SaaS no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-customizing-attribute-mappings).

-   Se pretende ou não existe um **filtro âmbito** que está a filtrar utilizadores com base nos valores de atributo específico. Para obter mais informações sobre filtros de âmbito, consulte [aprovisionamento de aplicações baseadas em atributos com filtros de âmbito](https://docs.microsoft.com/azure/active-directory/active-directory-saas-scoping-filters).

Quando observar a que os utilizadores não estão a ser aprovisionados, consulte os registos de auditoria no Azure AD e procure entradas de registo de um utilizador específico.

Os registos de auditoria aprovisionamento podem ser acedidos no portal do Azure, no **do Azure Active Directory &gt; aplicações da empresa &gt; \[nome da aplicação\] &gt; registos de auditoria** separador. Filtrar os registos de **aprovisionamento da conta** categoria para ver apenas os eventos de aprovisionamento para essa aplicação. Pode procurar utilizadores com base no "ID correspondente" que foi configurado para os mesmos nos mapeamentos de atributos. Por exemplo, se tiver configurado o "nome principal de utilizador" ou "endereço de correio eletrónico" como o atributo correspondente no lado do Azure AD e o utilizador está a ser aprovisionamento não tem um valor de "audrey@contoso.com". Em seguida, procure os registos de auditoria para "audrey@contoso.com" e consulte, em seguida, as entradas devolvidas.

A auditoria de aprovisionamento regista todas as operações executadas pelo serviço de aprovisionamento, incluindo a consultar o Azure AD para os utilizadores atribuídos que se encontrem no âmbito de aprovisionamento, consultar a aplicação de destino a existência desses utilizadores, a comparação com os objetos de utilizador de registo entre o sistema. Em seguida, adicionar, atualizar ou desativar a conta de utilizador no sistema de destino com base na comparação.

## <a name="general-problem-areas-with-provisioning-to-consider"></a>Áreas de problema geral com aprovisionamento a considerar

Segue-se uma lista de áreas de problema geral que pode explorar se tiver uma ideia dos onde começar.

* [Aprovisionamento do serviço não for apresentada a iniciar](#provisioning-service-does-not-appear-to-start)
* [Registos de auditoria indicar os utilizadores estão ignorados e não tenha sido aprovisionados, mesmo que estão atribuídos](#audit-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned)

## <a name="provisioning-service-does-not-appear-to-start"></a>Aprovisionamento do serviço não for apresentada a iniciar

Se definir o **estado de aprovisionamento** ser **no** no **do Azure Active Directory &gt; aplicações da empresa &gt; \[nome da aplicação\] &gt;Aprovisionamento** secção do portal do Azure. No entanto não outros detalhes de estado são apresentadas nessa página depois reloads subsequentes, é provável que o serviço está em execução, mas não tiver sido concluída ainda uma sincronização inicial. Verifique o **registos de auditoria** descrito acima para determinar as operações que o serviço está a ser executado, e se existem quaisquer erros.

>[!NOTE]
>Uma sincronização inicial pode tomar entre 20 minutos ou várias horas, consoante o tamanho do diretório do Azure AD e o número de utilizadores no âmbito de aprovisionamento. As sincronizações posteriores após a sincronização inicial são mais rápidas, como o serviço de aprovisionamento armazena as marcas d'água que representam o estado de ambos os sistemas após a sincronização inicial. Isto melhora o desempenho de sincronizações subsequentes.
>
>

## <a name="audit-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned"></a>Os registos de auditoria indicar os utilizadores estão ignorados e não aprovisionados, embora são atribuídas

Quando um utilizador aparece como "ignorada" nos registos de auditoria, é muito importante lê os detalhes na mensagem de registo para determinar o motivo expandidos. Seguem-se e as resoluções de motivos comuns:

-   **Foi configurado um filtro de âmbito** **que é filtrar o utilizador com base no valor de um atributo**. Para obter mais informações sobre filtros de âmbito, consulte <https://docs.microsoft.com/azure/active-directory/active-directory-saas-scoping-filters>.

-   **O utilizador é "não eficazmente realizado".** Se vir esta mensagem de erro específica, é porque não há um problema com o registo de atribuição do utilizador armazenado no Azure AD. Para corrigir este problema, anule a atribuição de utilizador (ou grupo) a partir da aplicação e volte atribuí-la novamente. Para obter mais informações sobre a atribuição, consulte <https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal>.

-   **Um atributo obrigatório está em falta ou não preenchidos para um utilizador.** Um aspeto importante a considerar ao configurar o aprovisionamento ser para rever e configurar os mapeamentos de atributos e os fluxos de trabalho que definem o que utilizador (ou grupo) propriedades fluxo do Azure AD para a aplicação. Isto inclui a definição "da propriedade correspondente" que ser utilizado para identificar e correspondam aos utilizadores/grupos entre os dois sistemas de forma exclusiva. Para obter mais informações sobre este processo importante, consulte [personalizar aprovisionamento atributo mapeamentos de utilizador para aplicações de SaaS no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-customizing-attribute-mappings).

  * **Mapeamentos de grupos de atributos:** aprovisionamento do nome do grupo e detalhes de grupo, para além de membros, caso seja suportado para algumas aplicações. Pode ativar ou desativar esta funcionalidade ativando ou desativando a **mapeamento** para objetos de grupo apresentados no **aprovisionamento** separador. Se estiver ativado a grupos de aprovisionamento, lembre-se de que reveja os mapeamentos de atributos para garantir que está a ser utilizado um campo adequado para o "ID correspondente". Isto pode ser o alias de apresentação nome ou e-mail), como o grupo e os seus membros não aprovisionadas se a propriedade correspondente está vazio ou não preenchidos para um grupo no Azure AD.

## <a name="next-steps"></a>Passos seguintes
[Sincronização do Azure AD Connect: Noções sobre o aprovisionamento declarativo](active-directory-aadconnectsync-understanding-declarative-provisioning.md)


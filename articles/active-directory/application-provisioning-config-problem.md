---
title: "Problema de configuração de aprovisionamento de utilizadores a uma aplicação de galeria do Azure AD | Microsoft Docs"
description: "Como resolver problemas comuns deparam quando configurar aprovisionamento de utilizadores a uma aplicação já listados na Galeria de aplicações do Azure AD"
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
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: 44e344095352f2bc6b27e389fc8be2cdf3e368d8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="problem-configuring-user-provisioning-to-an-azure-ad-gallery-application"></a>Problema de configuração de aprovisionamento de utilizadores a uma aplicação de galeria do Azure AD

Configurar [aprovisionamento de utilizadores automática](https://docs.microsoft.com/azure/active-directory/active-directory-saas-app-provisioning) para uma aplicação (quando suportadas), requer que a específico instruções ser seguidos para preparar a aplicação para o aprovisionamento automático. Em seguida, pode utilizar o portal do Azure para configurar o serviço de aprovisionamento para sincronizar as contas de utilizador para a aplicação.

Sempre deve começar por ao localizar o tutorial do programa de configuração específicas para configurar o aprovisionamento para a sua aplicação. Em seguida, siga os passos para configurar a aplicação e o Azure AD para criar a ligação de aprovisionamento. Uma lista de tutoriais de aplicação pode ser encontrada em [lista de tutoriais sobre como integrar aplicações SaaS no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list).

## <a name="how-to-see-if-provisioning-is-working"></a>Como ver se aprovisionamento está a funcionar 

Depois de ter configurado o serviço, a maioria das informações sobre a operação do serviço pode ser desenhadas a partir de dois locais:

-   **Registos de auditoria** – a auditoria de aprovisionamento regista todas as operações executadas pelo serviço de aprovisionamento, incluindo a consultar o Azure AD para atribuir utilizadores que se encontrem no âmbito para o aprovisionamento de registo. A aplicação de destino a existência desses utilizadores, os objetos de utilizador entre o sistema de comparação de consulta. Em seguida, adicionar, atualizar ou desativar a conta de utilizador no sistema de destino com base na comparação. Os registos de auditoria aprovisionamento podem ser acedidos no portal do Azure, no **do Azure Active Directory &gt; aplicações da empresa &gt; \[nome da aplicação\] &gt; registos de auditoria** separador. Filtrar os registos de **aprovisionamento da conta** categoria para ver apenas os eventos de aprovisionamento para essa aplicação.

-   **Aprovisionamento de estado –** um resumo das últimas aprovisionamento executar para uma determinada aplicação pode ser vista no **do Azure Active Directory &gt; aplicações da empresa &gt; \[nome da aplicação\] &gt; Aprovisionamento** secção, na parte inferior do ecrã sob as definições do serviço. Esta secção resume quantos utilizadores (e/ou grupos) estão atualmente a ser sincronizados entre os dois sistemas, e se existem quaisquer erros. Detalhes do erro ser nos registos de auditoria. Tenha em atenção que o estado de aprovisionamento não ser preenchido até ser concluída uma sincronização completa inicial entre o Azure AD e a aplicação.

## <a name="general-problem-areas-with-provisioning-to-consider"></a>Áreas de problema geral com aprovisionamento a considerar

Segue-se uma lista de áreas de problema geral que pode explorar se tiver uma ideia dos onde começar.

* [Aprovisionamento do serviço não for apresentada a iniciar](#provisioning-service-does-not-appear-to-start)
* [Não é possível guardar a configuração devido a credenciais de aplicação não está a funcionar](#can’t-save-configuration-due-to-app-credentials-not-working)
* [Os registos de auditoria supor que os utilizadores são "ignorados" e não aprovisionados, mesmo que estão atribuídos](#audit-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned)

## <a name="provisioning-service-does-not-appear-to-start"></a>Aprovisionamento do serviço não for apresentada a iniciar

Se definir o **estado de aprovisionamento** ser **no** no **do Azure Active Directory &gt; aplicações da empresa &gt; \[nome da aplicação\] &gt;Aprovisionamento** secção do portal do Azure. No entanto não outros detalhes de estado são apresentados nessa página depois reloads subsequentes. É provável que o serviço está em execução, mas não tiver sido concluída ainda uma sincronização inicial. Verifique o **registos de auditoria** descrito acima para determinar as operações que o serviço está a ser executado, e se existem quaisquer erros.

>[!NOTE]
>Uma sincronização inicial pode tomar entre 20 minutos ou várias horas, consoante o tamanho do diretório do Azure AD e o número de utilizadores no âmbito de aprovisionamento. As sincronizações posteriores após a sincronização inicial ser mais rápida, como o serviço de aprovisionamento armazena as marcas d'água que representam o estado de ambos os sistemas após a sincronização inicial, melhorando o desempenho de sincronizações subsequentes.
>
>

## <a name="cant-save-configuration-due-to-app-credentials-not-working"></a>Não é possível guardar a configuração devido a credenciais de aplicação não está a funcionar

Por ordem para aprovisionamento para funcionar, o Azure AD requer credenciais válidas que lhe permite ligar a uma API fornecida por essa aplicação de gestão do utilizador. Se estas credenciais não funcionam ou se não souber wat são, consulte o tutorial para configurar esta aplicação, descrita anteriormente.

## <a name="audit-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned"></a>Os registos de auditoria indicar os utilizadores estão ignorados e não aprovisionados, embora são atribuídas

Quando um utilizador aparece como "ignorada" nos registos de auditoria, é muito importante lê os detalhes na mensagem de registo para determinar o motivo expandidos. Seguem-se e as resoluções de motivos comuns:

-   **Foi configurado um filtro de âmbito** **que é filtrar o utilizador com base no valor de um atributo**. Para obter mais informações sobre filtros de âmbito, consulte <https://docs.microsoft.com/azure/active-directory/active-directory-saas-scoping-filters>.

-   **O utilizador é "não eficazmente realizado".** Se vir esta mensagem de erro específica, é porque não há um problema com o registo de atribuição do utilizador armazenado no Azure AD. Para corrigir este problema, anule a atribuição de utilizador (ou grupo) a partir da aplicação e volte atribuí-la novamente. Para obter mais informações sobre a atribuição, consulte <https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal>.

-   **Um atributo obrigatório está em falta ou não preenchidos para um utilizador.** Um aspeto importante a considerar ao configurar o aprovisionamento ser para rever e configurar os mapeamentos de atributos e os fluxos de trabalho que definem o que utilizador (ou grupo) propriedades fluxo do Azure AD para a aplicação. Isto inclui a definição "da propriedade correspondente" que ser utilizado para identificar e correspondam aos utilizadores/grupos entre os dois sistemas de forma exclusiva. Para obter mais informações sobre este processo importante, consulte <https://docs.microsoft.com/azure/active-directory/active-directory-saas-customizing-attribute-mappings>.

   * **Mapeamentos de grupos de atributos:** aprovisionamento do nome do grupo e detalhes de grupo, para além de membros, caso seja suportado para algumas aplicações. Pode ativar ou desativar esta funcionalidade ativando ou desativando a **mapeamento** para objetos de grupo apresentados no **aprovisionamento** separador. Se estiver ativado a grupos de aprovisionamento, lembre-se de que reveja os mapeamentos de atributos para garantir que está a ser utilizado um campo adequado para o "ID correspondente". Isto pode ser o alias de apresentação nome ou e-mail), como o grupo e os seus membros não aprovisionadas se a propriedade correspondente está vazio ou não preenchidos para um grupo no Azure AD.

#<a name="next-steps"></a>Passos seguintes
[Automatizar utilizador aprovisionamento e desaprovisionamento para aplicações SaaS com o Azure Active Directory](active-directory-saas-app-provisioning.md)

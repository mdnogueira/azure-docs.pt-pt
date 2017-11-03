---
title: Como migrar os utilizadores licenciados individuais para um grupo no Azure Active Directory | Microsoft Docs
description: "Como mudar de licenças de utilizador individuais ao grupo baseado licenciamento com o Azure Active Directory"
services: active-directory
keywords: Licenciamento do Azure AD
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/05/2017
ms.author: curtand
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 6b77dd4e9a6d361a05382397e89b575896fdad84
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-add-licensed-users-to-a-group-for-licensing-in-azure-active-directory"></a>Como adicionar os utilizadores licenciados para um grupo para licenciamento no Azure Active Directory

Poderá ter licenças existentes implementadas para os utilizadores em organizações de via "atribuição direta"; ou seja, a utilizar scripts do PowerShell ou outras ferramentas para atribuir licenças de utilizador individuais. Se pretende começar a utilizar o licenciamento baseadas em grupos para gerir licenças na sua organização, precisa de um plano de migração perfeitamente substitui as soluções existentes com base no grupo de licenciamento.

Mais importante: a lembrar é que deve evitar uma situação em que a migrar para o licenciamento baseado no grupo resultará na utilizadores perder temporariamente cujas licenças são atualmente atribuídas. Qualquer processo que pode resultar na remoção das licenças deve ser evitado para remover o risco de perda de aceso aos serviços e os respetivos dados de utilizadores.

## <a name="recommended-migration-process"></a>Processo de migração recomendada

1. Tem de automatização existente (por exemplo, o PowerShell), gestão de atribuição de licenças e remoção de utilizadores. Deixe a funcionar conforme está.

2. Criar um novo grupo de licenciamento (ou decidir qual existente grupos utilizar) e certifique-se de que todas as necessárias para os utilizadores são adicionados como membros.

3. Atribuir as licenças necessárias aos grupos; o seu objetivo deverá ser refletir o estado de licenciamento mesmo que a automatização existente (por exemplo, o PowerShell) estiver a aplicar aos utilizadores.

4. Certifique-se de que as licenças foram aplicadas a todos os utilizadores esses grupos. Isto pode ser feito ao verificar o estado de processamento em cada grupo e verificando os registos de auditoria.

  - Pode utilizadores individuais lugar para cima verificação observando os detalhes de licença. Verá que têm o mesmo licenças atribuídas "diretamente" e "herdadas" dos grupos.

  - Pode executar um script do PowerShell para [Certifique-se de que forma como são atribuídas as licenças aos utilizadores](active-directory-licensing-group-advanced.md#use-powershell-to-see-who-has-inherited-and-direct-licenses).

  - Quando a licença do produto mesmo é atribuída ao utilizador diretamente e através de um grupo, apenas uma licença é consumida pelo utilizador. Por conseguinte, não há licenças adicionais são necessários para efetuar a migração.

5. Certifique-se de que não as atribuições de licenças falharam ao verificar a cada grupo de utilizadores num Estado de erro. Para obter mais informações, consulte [Identifying e resolver problemas de licenciamento para um grupo](active-directory-licensing-group-problem-resolution-azure-portal.md).

6. Considere remover as atribuições de diretas originais; poderá fazê-lo gradualmente, no "waves", para monitorizar o resultado num subconjunto de utilizadores primeiro.

  Pode deixar as atribuições de diretas originais nos utilizadores, mas se deixam dos utilizadores aos respetivos grupos licenciados ainda manterá a licença original, o que é possivelmente que não pretende.

## <a name="an-example"></a>Um exemplo

Temos uma organização com 1000 utilizadores. Todos os utilizadores necessitam de Enterprise Mobility + licenças de segurança (EMS). 200 utilizadores são no departamento financeiro e necessitam de licenças do Office 365 Enterprise E3. Temos um script do PowerShell no local adição e remoção de licenças de utilizadores, visto que vêm e aceda a executar. Queremos substituir o script com baseado no grupo de licenciamento para que licenças são geridas automaticamente pelo Azure AD.

Eis o processo de migração foi aspeto:

1. No portal do Azure a atribuir a licença de EMS para o **todos os utilizadores** grupo no Azure AD. Atribuir a licença E3 o **departamento financeiro** grupo que contém todos os utilizadores necessários.

2. Para cada grupo, confirme que a atribuição de licenças foi concluída para todos os utilizadores. Aceda ao painel para cada grupo, selecione **licenças**e verificar o estado de processamento na parte superior do **licenças** painel.

  - Procure "Mais recente licença alterações ter sido aplicadas a todos os utilizadores" para confirmar o processamento foi concluído.

  - Procure uma notificação na parte superior sobre todos os utilizadores para quem licenças podem ter não foi atribuídas com êxito. Executamos fora de licenças para alguns utilizadores? Alguns utilizadores dispõem de SKUs impedi-los de herança licenças do grupo de licenças em conflito?

3. Lugar para cima verificar alguns utilizadores para verificar se têm ambas as diretas e o grupo licenças aplicadas. Aceda ao painel de para um utilizador, selecione **licenças**e examine o estado de licenças.

  - Este é o estado do utilizador esperado durante a migração:

      ![Estado do utilizador esperado](media/active-directory-licensing-group-migration-azure-portal/expected-user-state.png)

  Isto confirma que o utilizador tem licenças diretas e herdadas. Vemos que ambos **EMS** e **E3** são atribuídos.

  - Selecione cada licença para mostrar os detalhes sobre os serviços ativados. Isto pode ser utilizado para verificar se as licenças diretas e o grupo ativar exatamente os mesmo planos de serviço para o utilizador.

      ![Verifique os planos de serviço](media/active-directory-licensing-group-migration-azure-portal/check-service-plans.png)

4. Depois de confirmar que são equivalentes diretas e o grupo de licenças, pode começar a remover diretas licenças de utilizadores. Pode testar isto, removendo-os para utilizadores individuais no portal e, em seguida, executar scripts de automatização para os remover em massa. Eis um exemplo do mesmo utilizador com as licenças diretas removido através do portal. Tenha em atenção que o estado da licença permanece inalterado, mas já não vemos atribuições diretas.

  ![licenças diretas removidas](media/active-directory-licensing-group-migration-azure-portal/direct-licenses-removed.png)


## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre outros cenários para gestão de licenças através de grupos, leia o artigo

* [Atribuir licenças a um grupo no Azure Active Directory](active-directory-licensing-group-assignment-azure-portal.md)
* [O que é o licenciamento no Azure Active Directory com base no grupo?](active-directory-licensing-whatis-azure-portal.md)
* [Identificar e resolver problemas de licenciamento para um grupo no Azure Active Directory](active-directory-licensing-group-problem-resolution-azure-portal.md)
* [Azure Active Directory baseadas em grupos licenciamento cenários adicionais](active-directory-licensing-group-advanced.md)

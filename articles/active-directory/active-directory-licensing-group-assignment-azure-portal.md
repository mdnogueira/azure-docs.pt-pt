---
title: "Atribuir licenças a um grupo no Azure Active Directory | Microsoft Docs"
description: "Como atribuir licenças aos utilizadores através de licenciamento de grupo do Azure Active Directory"
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
ms.openlocfilehash: 42b18eab9cb419e6ada72ba72dc8be8d7f7b2eed
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="assign-licenses-to-users-by-group-membership-in-azure-active-directory"></a>Atribuir licenças aos utilizadores através da associação a grupos no Azure Active Directory

Este artigo explica como atribuir licenças de produtos a um grupo de utilizadores no Azure Active Directory (Azure AD) e, em seguida, verificar se está a licenciados corretamente.

Neste exemplo, o inquilino contém um grupo de segurança denominado **departamento de RH**. Este grupo inclui todos os membros do departamento de recursos humanos (cerca de 1000 utilizadores). Pretende atribuir licenças do Office 365 Enterprise E3 para o departamento de todo. O serviço de empresa Yammer que está incluído no produto tem de ser temporariamente desativado até que o departamento está pronto para começar a utilizá-la. Também pretender implementar o Enterprise Mobility + licenças de segurança para o mesmo grupo de utilizadores.

> [!NOTE]
> Alguns serviços da Microsoft não estão disponíveis em todas as localizações. Antes de uma licença pode ser atribuída a um utilizador, o administrador tem de especificar a propriedade de localização de utilização no utilizador.

> Para atribuição de licença de grupo, os utilizadores sem uma localização de utilização especificada irão herdar a localização do diretório. Se tiver utilizadores em vários locais, recomendamos que defina a localização de utilização sempre como parte do seu fluxo de criação de utilizador no Azure AD (por exemplo, através do AAD Connect configuração) - irá garantir o resultado da atribuição de licenças sempre está correto e que os utilizadores não recebem serviços em locais que não são permitidos.

## <a name="step-1-assign-the-required-licenses"></a>Passo 1: Atribuir as licenças necessárias

1. Iniciar sessão para o [ **portal do Azure** ](https://portal.azure.com) com uma conta de administrador. Para gerir licenças, a conta tem de ser um administrador de conta de utilizador ou função de administrador global.

2. Selecione **mais serviços** no painel de navegação esquerdo e, em seguida, selecione **do Azure Active Directory**. Pode adicionar este painel aos Favoritos ou afixá-lo ao dashboard do portal.

3. No **do Azure Active Directory** painel, selecione **licenças**. Esta ação abre um painel onde pode ver e gerir todos os produtos sujeito a licença no inquilino.

4. Em **todos os produtos**, selecione o Office 365 Enterprise E3 e Enterprise Mobility + Security selecionando os nomes de produto. Para iniciar a atribuição, selecione **atribuir** na parte superior do painel.

   ![Todos os produtos, atribuir licenças](media/active-directory-licensing-group-assignment-azure-portal/all-products-assign.png)

5. No **atribuir licenças** painel, clique em **utilizadores e grupos** para abrir o **utilizadores e grupos** painel. Procure o nome do grupo *departamento de RH*, selecione o grupo e, em seguida, lembre-se de que confirme clicando **selecione** na parte inferior do painel.

   ![Selecione um grupo](media/active-directory-licensing-group-assignment-azure-portal/select-a-group.png)

6. No **atribuir licenças** painel, clique em **opções de atribuição (opcionais)**, que apresenta todos os planos de serviço incluídos os dois produtos são selecionados anteriormente. Localizar **Yammer Enterprise** e ativá-la **desativar** desativar que o serviço de licença do produto. Confirmar clicando **OK** na parte inferior **opções de atribuição**.

   ![Opções de atribuição](media/active-directory-licensing-group-assignment-azure-portal/assignment-options.png)

7. Para concluir a atribuição, no **atribuir licenças** painel, clique em **atribuir** na parte inferior do painel.

8. É apresentada uma notificação no canto superior direito que mostra o estado e o resultado do processo. Se não foi possível concluir a atribuição de grupo (por exemplo, devido a já existentes licenças no grupo), clique na notificação para ver os detalhes da falha.

Especificamos agora um modelo de licença para o grupo do departamento de RH. Um processo em segundo plano no Azure AD foi iniciado para processar todos os membros desse grupo existentes. Esta operação inicial pode demorar algum tempo, dependendo do tamanho atual do grupo. No próximo passo, vamos descrever como verificar se o processo foi concluída e determinar se mais atenção é necessário resolver problemas.

> [!NOTE]
> Pode iniciar a atribuição do mesma a partir de uma localização alternativa: **utilizadores e grupos** no Azure AD. Aceda a **do Azure Active Directory** > **utilizadores e grupos** > **todos os grupos**. Em seguida, localize o grupo, selecione-o e aceda ao **licenças** separador. O **atribuir** botão na parte superior do painel abre o painel de atribuição de licença.

## <a name="step-2-verify-that-the-initial-assignment-has-finished"></a>Passo 2: Verificar se a atribuição inicial foi concluída

1. Aceda a **do Azure Active Directory** > **utilizadores e grupos** > **todos os grupos**. Em seguida, localize o **departamento de RH** grupo de licenças atribuídas a.

2. No **departamento de RH** painel do grupo, selecione **licenças**. Isto permite-lhe confirmar rapidamente se licenças totalmente atribuídas a utilizadores e se existem quaisquer erros que tem de ter um aspeto no. Estão disponíveis as seguintes informações:

   - Lista de licenças de produtos que estão atualmente atribuídas ao grupo. Selecione uma entrada para mostrar os serviços específicos que foram ativados e efetuar alterações.

   - Estado das alterações mais recentes de licença que foram efetuadas para o grupo (se estiverem a ser processadas as alterações ou se o processamento foi concluída para todos os membros de utilizador).

   - Informações sobre os utilizadores que estão num Estado de erro porque não foi possível atribuir licenças à-las.

   ![Opções de atribuição](media/active-directory-licensing-group-assignment-azure-portal/assignment-errors.png)

3. Ver informações mais detalhadas sobre a licença de processamento em **do Azure Active Directory** > **utilizadores e grupos** > *nome do grupo*  >  **Registos de auditoria**. Tenha em atenção as seguintes atividades:

   - Atividade: **iniciar aplicar licença de grupo com base nos utilizadores**. Isto é registado quando o sistema escolherá a alteração de atribuição de licenças no grupo e inicia aplicá-la a todos os membros de utilizador. Contém informações sobre a alteração que foi efetuada.

   - Atividade: **concluir aplicar licença de grupo com base nos utilizadores**. Isto é registado quando o sistema concluir o processamento de todos os utilizadores no grupo. Contém um resumo de quantos utilizadores foram processados com êxito e o número de utilizadores que não foi possível atribuir licenças do grupo.

   [Leia esta secção](./active-directory-licensing-group-advanced.md#use-audit-logs-to-monitor-group-based-licensing-activity) para saber mais sobre como os registos de auditoria podem ser utilizados para analisar as alterações efetuadas por baseado no grupo de licenciamento.

## <a name="step-3-check-for-license-problems-and-resolve-them"></a>Passo 3: Verificar problemas de licença e resolvê-los

1. Aceda a **do Azure Active Directory** > **utilizadores e grupos** > **todos os grupos**e localize o **departamento de RH** grupo de licenças atribuídas a.
2. No **departamento de RH** painel do grupo, selecione **licenças**. A notificação na parte superior do painel mostra que existem 10 utilizadores que não foi possível atribuir licenças. Clicar nela abre-se de uma lista de todos os utilizadores num Estado de erro de licenciamento para este grupo.
3. O **falha atribuições** coluna indica-nos que ambas as licenças de produto não foi possível atribuir aos utilizadores. O **principais motivo da falha** coluna contém a causa da falha. Neste caso, tem **planos de serviços em conflito**.

   ![Atribuições de falha](media/active-directory-licensing-group-assignment-azure-portal/failed-assignments.png)

4. Selecione um utilizador para abrir o **licenças** painel. Este painel mostra todas as licenças que estão atualmente atribuídas ao utilizador. Neste exemplo, o utilizador tem a licença do Office 365 Enterprise E1 que foi herdada do **utilizadores de local público** grupo. Isto está em conflito com a licença E3 que o sistema estava a tentar aplicar a partir de **departamento de RH** grupo. Como resultado, nenhum das licenças do que o grupo foi atribuído ao utilizador.

   ![Licenças de vista de um utilizador](media/active-directory-licensing-group-assignment-azure-portal/user-license-view.png)

5. Para resolver este conflito, remova o utilizador a **utilizadores de local público** grupo. Depois do Azure AD processa a alteração, o **departamento de RH** corretamente são atribuídas as licenças.

   ![Licença atribuída corretamente](media/active-directory-licensing-group-assignment-azure-portal/license-correctly-assigned.png)

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre a funcionalidade definido para a gestão de licenças através de grupos, consulte os artigos seguintes:

* [O que é o licenciamento no Azure Active Directory com base no grupo?](active-directory-licensing-whatis-azure-portal.md)
* [Identificar e resolver problemas de licenciamento para um grupo no Azure Active Directory](active-directory-licensing-group-problem-resolution-azure-portal.md)
* [Como migrar os utilizadores licenciados individuais para baseadas em grupos de licenciamento no Azure Active Directory](active-directory-licensing-group-migration-azure-portal.md)
* [Azure Active Directory baseadas em grupos licenciamento cenários adicionais](active-directory-licensing-group-advanced.md)

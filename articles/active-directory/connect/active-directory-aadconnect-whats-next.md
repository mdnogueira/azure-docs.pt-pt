---
title: 'Do Azure AD Connect: Passos seguintes e como gerir o Azure AD Connect | Microsoft Docs'
description: "Saber como expandir a configuração predefinida e as tarefas operacionais do Azure AD Connect."
services: active-directory
documentationcenter: 
author: billmath
manager: femila
editor: curtand
ms.assetid: c18bee36-aebf-4281-b8fc-3fe14116f1a5
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: billmath
ms.openlocfilehash: beace24fa00c85a5038a3c39ae8f76af5fd12111
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="next-steps-and-how-to-manage-azure-ad-connect"></a>Passos seguintes e como gerir o Azure AD Connect
Utilize os procedimentos operacionais neste artigo para personalizar Connect do Azure Active Directory (Azure AD) para satisfazer as necessidades e requisitos da sua organização.  

## <a name="add-additional-sync-admins"></a>Adicionar administradores de sincronização adicionais
Por predefinição, apenas o utilizador que tiver a instalação e os administradores locais são capazes de gerir o motor de sincronização instalado. Para outras pessoas possam aceder e gerir o motor de sincronização, localizar o grupo com o nome ADSyncAdmins no servidor local e adicioná-las a este grupo.

## <a name="assign-licenses-to-azure-ad-premium-and-enterprise-mobility-suite-users"></a>Atribuir licenças aos utilizadores do Azure AD Premium e Enterprise Mobility Suite
Agora que os utilizadores foram sincronizados para a nuvem, tem de atribuir uma licença para estes podem começar com aplicações na nuvem como o Office 365.

### <a name="to-assign-an-azure-ad-premium-or-enterprise-mobility-suite-license"></a>Para atribuir um Azure AD Premium ou Enterprise Mobility Suite licença

1. Inicie sessão no portal do Azure como um administrador.
2. No lado esquerdo, selecione **Active Directory**.
3. No **do Active Directory** página, faça duplo clique em diretório que tem os utilizadores que pretende configurar.
4. Na parte superior da página do diretório, selecione **Licenças**.
5. No **licenças** página, selecione **Active Directory Premium** ou **Enterprise Mobility Suite**e, em seguida, clique em **atribuir**.
6. Na caixa de diálogo, selecione os utilizadores a quem pretende atribuir as licenças e clique no ícone de marca de verificação para guardar as alterações.

## <a name="verify-the-scheduled-synchronization-task"></a>Certifique-se a tarefa de sincronização agendada
Utilize o portal do Azure para verificar o estado de uma sincronização.

### <a name="to-verify-the-scheduled-synchronization-task"></a>Para verificar a tarefa de sincronização agendada
1. Inicie sessão no portal do Azure como um administrador.
2. No lado esquerdo, selecione **Active Directory**.
3. No **do Active Directory** página, faça duplo clique em diretório que tem os utilizadores que pretende configurar.
4. Na parte superior da página do diretório, selecione **integração de diretórios**.
5. Em **integração com o local active directory**, tenha em atenção a hora da última sincronização.

<center>![Hora de sincronização de diretórios](./media/active-directory-aadconnect-whats-next/verify.png)</center>

## <a name="start-a-scheduled-synchronization-task"></a>Iniciar uma tarefa de sincronização agendada
Se precisar de executar uma tarefa de sincronização, pode fazê-lo, executando novamente o Assistente do Azure AD Connect.  Tem de fornecer as credenciais do Azure AD.  No assistente, selecione o **personalizar as opções de sincronização** de tarefas e clique em **seguinte** mover através do assistente. No final, certifique-se de que o **inicie o processo de sincronização assim que a configuração inicial for concluída** caixa está selecionada.

<center>![Iniciar a sincronização](./media/active-directory-aadconnect-whats-next/startsynch.png)</center>

Para obter mais informações sobre a sincronização do Azure AD Connect do programador, consulte [do Azure AD Connect programador](active-directory-aadconnectsync-feature-scheduler.md).

## <a name="additional-tasks-available-in-azure-ad-connect"></a>Tarefas adicionais disponíveis no Azure AD Connect
Após a instalação inicial do Azure AD Connect, pode sempre iniciar o assistente novamente do atalho de página ou ambiente de trabalho de início do Azure AD Connect.  Vai notar que percorrer o assistente novamente fornece algumas novas opções sob a forma de tarefas adicionais.  

A tabela seguinte fornece um resumo destas tarefas e uma descrição breve de cada tarefa.

![Lista de tarefas adicionais](./media/active-directory-aadconnect-whats-next/addtasks.png)

| Tarefas adicionais | Descrição |
| --- | --- |
| **Ver o cenário selecionado** |Ver a sua solução do Azure AD Connect atual.  Isto inclui definições gerais, sincronizadas diretórios e definições de sincronização. |
| **Personalizar opções de sincronização** |Altere a configuração atual, como adicionar todas as florestas do Active Directory adicionais para a configuração ou ativar opções de sincronização, tais como o utilizador, grupo, dispositivo ou repetição de escrita de palavras-passe. |
| **Ativar o modo de teste** |Informações de fase imediatamente não estão sincronizadas e não são exportadas para o Azure AD ou no local do Active Directory.  Com esta funcionalidade, pode pré-visualizar as sincronizações antes de ocorrem. |

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre [integrar as identidades no local ao Azure Active Directory](active-directory-aadconnect.md).

---
title: Associar um novo dispositivo de Windows 10 com o Azure AD durante uma primeira | Microsoft Docs
description: "Um tópico que explica como os utilizadores podem configurar a associação do Azure AD durante a experiência de primeira execução."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 06a149f7-4aa1-4fb9-a8ec-ac2633b031fb
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/27/2017
ms.author: markvi
ms.reviewer: jairoc
ms.openlocfilehash: b07386eeb0d3e05a0b4545be39030066fbdafd3d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="join-a-new-windows-10-device-with-azure-ad-during-a-first-run"></a>Associar um novo dispositivo de Windows 10 com o Azure AD durante uma primeira

Gestão de dispositivos no Azure Active Directory (Azure AD), pode certificar-se de que os utilizadores acedem aos seus recursos dos dispositivos que cumprem as normas de segurança e conformidade. Para obter mais detalhes, consulte o [introdução à gestão de dispositivos no Azure Active Directory](device-management-introduction.md).

Com o Windows 10, pode associar um novo dispositivo para o Azure AD durante a experiência de primeira execução (FRX).  
Isto permite-lhe distribuir dispositivos shrink-wrapped aos seus funcionários e alunos.

Se tiver o Windows 10 Professional ou Windows 10 Enterprise instalada num dispositivo, será assumida a experiência para o processo de configuração para dispositivos pertencentes à empresa.

Nas janelas *experiência de out-of-box*, associar um domínio do Active Directory (AD) no local não é suportada. Se pretender associar um computador a um domínio do AD durante a configuração, deve selecionar a ligação **configurar o Windows com uma conta local**. Em seguida, pode associar ao domínio das definições no seu computador.
 


## <a name="before-you-begin"></a>Antes de começar

Para associar um dispositivo Windows 10, o serviço de registo do dispositivo tem de ser configurado para ativar o registo de dispositivos. Além de ter permissão para associar os dispositivos no seu inquilino do Azure AD, tem de ter menos dispositivos registados que o máximo configurado. Para obter mais detalhes, consulte [configurar definições do dispositivo](device-management-azure-portal.md#configure-device-settings).

## <a name="joining-a-device"></a>Associar um dispositivo

**Para associar um dispositivo Windows 10 para o Azure AD durante FRX:**


1. Quando ativar no seu dispositivo novo e iniciar o processo de configuração, deverá ver o **obter pronto** mensagem. Siga as instruções para configurar o seu dispositivo.

2. Comece por personalizar o seu idioma e região. Em seguida, aceite os termos de licenciamento de Software Microsoft.
 
    ![Personalizar a sua região](./media/device-management-azuread-joined-devices-frx/01.png)

3. Selecione a rede que pretende utilizar para ligar à Internet.

4. Clique em **este dispositivo pertence à minha organização**. 

    ![Quem é o proprietário este ecrã de PC](./media/device-management-azuread-joined-devices-frx/02.png)

5. Introduza as credenciais que foram fornecidas pela sua organização e, em seguida, clique em **sessão**.

    ![Ecrã de início de sessão](./media/device-management-azuread-joined-devices-frx/03.png)

6. Dispositivos localiza um inquilino correspondente no Azure AD. Se estiver num domínio federado, são redirecionados para o servidor de Secure Token serviço (STS) no local, por exemplo, serviços do Active Directory Federação (AD FS).

7. Se for um utilizador num domínio não federada, introduza as credenciais diretamente na página do Azure AD alojadas. 

8. É-lhe pedido para um desafio de autenticação multifator. 
 
9. Azure AD verifica se é necessária uma inscrição na gestão de dispositivos móveis.

10. Windows regista o dispositivo no diretório da organização no Azure AD e inscreve-lo na gestão de dispositivos móveis, se aplicável.

11. Se for:
    - Um utilizador gerido, Windows leva-o para o ambiente de trabalho através do processo de início de sessão automático.

    - Um utilizador federado, o utilizador é direcionado para o ecrã de início de sessão do Windows para introduzir as suas credenciais.

## <a name="verification"></a>Verificação

Para verificar se um dispositivo é associado ao seu Azure AD, reveja o **acesso ou escola** diálogo no seu dispositivo Windows. A caixa de diálogo deve indicar que está ligado ao seu diretório do Azure AD.

![Acesso ou escola](./media/device-management-azuread-joined-devices-frx/13.png)


## <a name="next-steps"></a>Passos seguintes

- Para obter mais detalhes, consulte o [introdução à gestão de dispositivos no Azure Active Directory](device-management-introduction.md).

- Para obter mais detalhes sobre a gestão de dispositivos no portal do Azure AD, consulte [gerir dispositivos através do portal do Azure](device-management-azure-portal.md).

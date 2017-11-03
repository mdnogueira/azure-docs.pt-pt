---
title: "Dispositivos associados ao conjunto de cópias de segurança do Azure Active Directory | Microsoft Docs"
description: Saiba como configurar dispositivos do Azure Active Directory associados.
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/29/2017
ms.author: markvi
ms.reviewer: jairoc
ms.openlocfilehash: 415086809efe779c6bcae32719d8be1b48764905
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="set-up-azure-active-directory-joined-devices"></a>Dispositivos associados ao conjunto de cópias de segurança do Azure Active Directory

Gestão de dispositivos no Azure Active Directory (Azure AD), pode certificar-se de que os utilizadores acedem aos seus recursos dos dispositivos que cumprem as normas de segurança e conformidade. Para obter mais informações, consulte [introdução à gestão de dispositivos no Azure Active Directory](device-management-introduction.md).

Se pretender colocar os dispositivos Windows 10 pertencentes no trabalho sob o controlo do Azure AD, pode realizar esta através da configuração de dispositivos do Azure AD associado. Este tópico fornece os passos relacionados. 


## <a name="prerequisites"></a>Pré-requisitos

Para associar um dispositivo Windows 10, o serviço de registo do dispositivo tem de ser configurado para ativar o registo de dispositivos. Além de ter permissão para associar os dispositivos no seu inquilino do Azure AD, tem de ter menos dispositivos registados que o máximo configurado. Para obter mais informações, consulte [configurar definições do dispositivo](device-management-azure-portal.md#configure-device-settings).



## <a name="what-you-should-know"></a>O que deve conhecer


- Windows associa o dispositivo no diretório da organização no Azure AD.

- Poderá ser necessário passar por desafio de autenticação multifator. Este desafio é configurável pelo seu administrador de TI.

- Azure AD verifica se o dispositivo requer a inscrição de gestão de dispositivos móveis e inscreve-la, se aplicável.

- Se for um utilizador gerido, o Windows leva-o no ambiente de trabalho através do automático início de sessão.

- Se um utilizador federado, tem de início de sessão com as suas credenciais.


## <a name="joining-a-device"></a>Associar um dispositivo

Esta secção fornece os passos para associar o seu dispositivo Windows 10 para o seu Azure AD. Se associar com êxito do seu dispositivo para o Azure AD, o **acesso ou escola** caixa de diálogo indica isto com um **à \<seu Azure AD\>**  entrada.

![Ligado](./media/device-management-azuread-joined-devices-setup/13.png)


**Para associar o seu dispositivo Windows 10:**

1. No **iniciar** menu, clique em **definições**.

    ![Definições](./media/device-management-azuread-joined-devices-setup/01.png)

2. Clique em **contas**.

    ![Contas](./media/device-management-azuread-joined-devices-setup/02.png)


3. Clique em **acesso ou escola**.

    ![Acesso ou escola](./media/device-management-azuread-joined-devices-setup/03.png)

4. No **acesso ou escola** caixa de diálogo, clique em **Connect**.

    ![Ligar](./media/device-management-azuread-joined-devices-setup/04.png)


5. No **configurar uma conta escolar ou profissional** caixa de diálogo, clique em **associar este dispositivo ao Azure Active Directory**.

    ![Ligar](./media/device-management-azuread-joined-devices-setup/08.png)


6. No **vamos ajudá-lo a sessão iniciada** caixa de diálogo, introduza o nome da sua conta (por exemplo, someone@example.com) e, em seguida, clique em **seguinte**.

    ![Vamos ajudá-lo a sessão iniciada](./media/device-management-azuread-joined-devices-setup/10.png)


6. No **palavra-passe de Enter** caixa de diálogo, introduza a palavra-passe e, em seguida, clique em **sessão**.

    ![Introduza a palavra-passe](./media/device-management-azuread-joined-devices-setup/05.png)


7. No **certificar-se de que esta é a sua organização** caixa de diálogo, clique em **associar**.

    ![Certifique-se de que esta é a sua organização](./media/device-management-azuread-joined-devices-setup/11.png)


8. No **está tudo pronto** caixa de diálogo, clique em **feito**.

    ![Está tudo pronto](./media/device-management-azuread-joined-devices-setup/12.png)

## <a name="verification"></a>Verificação

Para verificar se um dispositivo é associado a um Azure AD, pode rever o **acesso ou escola** diálogo no seu dispositivo.

![Ligado](./media/device-management-azuread-joined-devices-setup/13.png)

Em alternativa, pode executar o seguinte comando:`dsregcmd /status`  
Num dispositivo associado com êxito, **AzureAdJoined** é **Sim**.

![Ligado](./media/device-management-azuread-joined-devices-setup/14.png)

Também pode rever as definições de dispositivo no portal do Azure AD.

![Ligado](./media/device-management-azuread-joined-devices-setup/15.png)

Para obter mais informações, consulte [localizar dispositivos](device-management-azure-portal.md#locate-devices).


## <a name="next-steps"></a>Passos seguintes

Para obter mais informações, consulte: 

- O [introdução à gestão de dispositivos no Azure Active Directory](device-management-introduction.md)
- [Gestão de dispositivos no portal do Azure](device-management-azure-portal.md)
- 




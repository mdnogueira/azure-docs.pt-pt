---
title: "Resolução de problemas híbrida do Azure Active Directory dispositivos associados a um nível baixo | Microsoft Docs"
description: "Resolução de problemas híbrida do Azure Active Directory dispositivos associados a um nível inferior."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: cdc25576-37f2-4afb-a786-f59ba4c284c2
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/08/2017
ms.author: markvi
ms.reviewer: jairoc
ms.openlocfilehash: 8bc07c551b5554a3f5b8697ca77a00d9cd0f470a
ms.sourcegitcommit: adf6a4c89364394931c1d29e4057a50799c90fc0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/09/2017
---
# <a name="troubleshooting-hybrid-azure-active-directory-joined-down-level-devices"></a>Resolução de problemas híbrida do Azure Active Directory dispositivos associados a um nível baixo 

Este tópico é apenas aplicável a dispositivos que se seguem: 

- Windows 7 
- Windows 8.1 
- Windows Server 2008 R2 
- Windows Server 2012 
- Windows Server 2012 R2 
 

Para Windows 10 ou Windows Server 2016, consulte [híbrida de resolução de problemas do Azure Active Directory associados de dispositivos Windows 10 e Windows Server 2016](device-management-troubleshoot-hybrid-join-windows-current.md).

Este tópico pressupõe que tem [dispositivos associados ao configurado híbrida do Azure Active Directory](device-management-hybrid-azuread-joined-devices-setup.md) para suportar os cenários seguintes:

- Acesso condicional baseado no dispositivo

- [Enterprise roaming das definições](active-directory-windows-enterprise-state-roaming-overview.md)

- [Windows Hello para empresas](active-directory-azureadjoin-passport-deployment.md) 





Este tópico fornece orientações sobre como resolver potenciais problemas de resolução de problemas.  

**O que deve conhecer:** 

- O número máximo de dispositivos por utilizador está centrada nos dispositivos. Por exemplo, se *jdoe* e *jharnett* início de sessão num dispositivo, um registo separados (DeviceID) é criada para cada um no **utilizador** separador de informações.  

- O registo inicial / associação de dispositivos está configurada para efetuar uma tentativa de início de sessão ou bloquear / desbloquear. Pode haver atraso de 5 minutos acionado por uma tarefa de Programador de tarefas. 

- Reinstalar o sistema operativo ou uma manual unregister e volte a registar podem criar um novo registo no Azure AD e resulta em várias entradas no separador de informações de utilizador no portal do Azure. 


## <a name="step-1-retrieve-the-registration-status"></a>Passo 1: Obter o estado do registo 

**Para verificar o estado do registo:**  

1. Abra a linha de comandos como administrador 

2. Tipo`"%programFiles%\Microsoft Workplace Join\autoworkplace.exe /i"`

Este comando apresenta uma caixa de diálogo que fornece-lhe obter mais detalhes sobre o estado de associação.

![Associação à área de trabalho para Windows](./media/active-directory-device-registration-troubleshoot-windows-legacy/01.png)


## <a name="step-2-evaluate-the-hybrid-azure-ad-join-status"></a>Passo 2: Avaliar a Estado de associação do Azure AD de híbrida 

Se a associação do Azure AD híbrido não foi bem sucedida, a caixa de diálogo fornece detalhes sobre o problema ocorreu.

**Os problemas mais comuns são:**

- Uma configuração incorreta do AD FS ou o Azure AD

    ![Associação à área de trabalho para Windows](./media/active-directory-device-registration-troubleshoot-windows-legacy/02.png)

- Não tem sessão iniciada como um utilizador de domínio

    ![Associação à área de trabalho para Windows](./media/active-directory-device-registration-troubleshoot-windows-legacy/03.png)

- Foi atingida a quota de uma

    ![Associação à área de trabalho para Windows](./media/active-directory-device-registration-troubleshoot-windows-legacy/04.png)

- O serviço não está a responder 

    ![Associação à área de trabalho para Windows](./media/active-directory-device-registration-troubleshoot-windows-legacy/05.png)

Também pode encontrar as informações de estado no registo de eventos em **aplicações e serviços Log\Microsoft-Workplace Join**.
  
**As causas mais comuns para uma associação de falha híbrida do Azure AD são:** 

- O computador não está na rede interna da organização ou uma VPN sem ligação a um local controlador de domínio do AD.

- Tem sessão iniciada num computador com uma conta de computador local. 

- Problemas de configuração do serviço: 

  - O servidor de Federação foi configurado para suportar **WIAORMULTIAUTHN**. 

  - Não há nenhum que aponta para o nome de domínio verificado no Azure AD na floresta do AD em que o computador pertence ao objeto de ponto de ligação de serviço.

  - Um utilizador atingiu o limite de dispositivos. 

## <a name="next-steps"></a>Passos seguintes

Para perguntas, consulte o [perguntas mais frequentes sobre a gestão de dispositivos](device-management-faq.md)  

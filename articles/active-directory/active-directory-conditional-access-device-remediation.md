---
title: "Não pode aceder aí a partir daqui no portal do Azure num dispositivo Windows | Microsoft Docs"
description: "Saiba de onde vem \"onde não pode aceder aí a partir daqui\" e o que poderia verificar para evitar esta caixa de diálogo."
services: active-directory
keywords: acesso condicional baseado no dispositivo, registo de dispositivos, ativar o registo de dispositivos, registo de dispositivos e MDM
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: 8ad0156c-0812-4855-8563-6fbff6194174
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/05/2017
ms.author: markvi
ms.reviewer: jairoc
ms.openlocfilehash: d86e47507f7a578e8a0affb690b6d96673b6b69a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="you-cant-get-there-from-here-on-a-windows-device"></a>Não pode aceder aí a partir daqui num dispositivo Windows

Durante uma tentativa de, por exemplo, aceder à intranet do SharePoint Online da sua organização, poderá deparar-se com uma página que diz que *não pode aceder aí a partir daqui*. Esta página aparece, porque o seu administrador configurou uma política de acesso condicional que impede o acesso aos recursos da organização sob determinadas condições. Embora possa ser necessário contactar o suporte técnico ou o seu administrador para resolver este problema, pode experimentar algumas coisas, primeiro.

Se estiver a utilizar um dispositivo **Windows**, deve verificar o seguinte:

- Está a utilizar um browser suportado?

- Está a executar uma versão suportada do Windows no seu dispositivo?

- O seu dispositivo está em conformidade?






## <a name="supported-browser"></a>Browser suportado

Se o administrador tiver configurado uma política de acesso condicional, só pode aceder aos recursos da sua organização através de um browser suportado. Nos dispositivos Windows, só são suportados o **Internet Explorer** e o **Edge**.

Pode determinar facilmente se não consegue aceder a um recurso devido a um browser não suportado, ao ler a secção de detalhes da página de erro:

![Mensagem "Problemas difíceis de resolver" relativos a browsers não suportados](./media/active-directory-conditional-access-device-remediation/02.png "Cenário")

A única remediação é utilizar um browser que a aplicação suporte na plataforma do seu dispositivo. Para obter uma lista completa dos browsers suportados, veja os [browsers suportados](active-directory-conditional-access-supported-apps.md).  


## <a name="supported-versions-of-windows"></a>Versões suportadas do Windows

As condições abaixo sobre o sistema operativo Windows têm de ser verdadeiras no seu dispositivo: 

- Se estiver a executar um sistema operativo de computador no dispositivo, este tem de ser o Windows 7 ou posterior.
- Se estiver a executar um sistema operativo Windows Server no dispositivo, este tem de ser o Windows Server 2008 R2 ou posterior. 


## <a name="compliant-device"></a>Dispositivo conforme

O seu administrador poderá ter configurado uma política de acesso condicional que permita acesso aos recursos da sua organização apenas através de dispositivos conformes. Para ser conforme, o dispositivo tem de ser associado ao seu Active Directory no local ou associado ao seu Azure Active Directory.

Pode determinar facilmente se não consegue aceder a um recurso devido a um dispositivo não conforme, ao ler a secção de detalhes da página de erro:
 
![Mensagens "Problemas difíceis de resolver" relativas a dispositivos não registados](./media/active-directory-conditional-access-device-remediation/01.png "Cenário")


### <a name="is-your-device-joined-to-an-on-premises-active-directory"></a>O dispositivo está associado a um Active Directory no local?

**Se o seu dispositivo estiver associado a um Active Directory no local da sua organização:**

1. Confirme que iniciou sessão no Windows com a sua conta profissional (conta do Active Directory).
2. Ligue à sua rede empresarial através de uma rede privada virtual (VPN) ou DirectAccess.
3. Assim que estiver ligado, prima a tecla do logótipo do Windows + a tecla L para bloquear a sua sessão do Windows.
4. Introduza as credenciais da sua conta profissional para desbloquear a sessão do Windows.
5. Aguarde um minuto e tente aceder novamente à aplicação ou ao serviço.
6. Se vir a mesma página, clique na ligação **Mais detalhes** e, em seguida, contacte o seu administrador com os detalhes.


### <a name="is-your-device-not-joined-to-an-on-premises-active-directory"></a>O dispositivo não está associado a um Active Directory no local?

Se o dispositivo não estiver associado a um Active Directory no local e executar o Windows 10, tem duas opções:

* Executar a Associação do Azure AD
* Adicionar a sua conta profissional ou escolar ao Windows

Para obter mais informações sobre as diferenças entre estas opções, veja [Utilizar dispositivos Windows 10 na sua área de trabalho](active-directory-azureadjoin-windows10-devices.md).  
Se o dispositivo:

- Pertencer à sua organização, deve executar a Associação do Azure AD.
- For um dispositivo pessoal ou um Windows Phone, deve adicionar a sua conta escolar ou profissional ao Windows 



#### <a name="azure-ad-join-on-windows-10"></a>Associação do Azure AD no Windows 10

Os passos para associar o dispositivo ao Azure AD estão associados à versão do Windows 10 que está a executar no mesmo. Para determinar a versão do sistema operativo Windows 10, execute o comando **winver**: 

![Versão do Windows](./media/active-directory-conditional-access-device-remediation/03.png )


**Atualização de Aniversário do Windows 10 (Versão 1607):**

1. Abra a aplicação **Definições**.
2. Clique em **Contas** > **Aceder a trabalho ou escola**.
3. Clique em **Ligar**.
4. Clique em **Associar este dispositivo ao Azure AD**.
5. Autentique na sua organização, forneça a autenticação multifator, se for necessário, e siga os passos apresentados.
6. Termine sessão e reinicie-a com a sua conta profissional.
7. Tente aceder novamente à aplicação.

**Atualização do Windows 10 de Novembro de 2015 (Versão 1511):**

1. Abra a aplicação **Definições**.
2. Clique em **Sistema** > **Sobre**.
3. Clique em **Associar ao Azure AD**.
4. Autentique na sua organização, forneça a autenticação multifator, se for necessário, e siga os passos apresentados.
5. Termine sessão e reinicie-a com a sua conta profissional (conta do Azure AD).
6. Tente aceder novamente à aplicação.


#### <a name="workplace-join-on-windows-81"></a>Associação à Área de Trabalho no Windows 8.1

Se o seu dispositivo não estiver associado a um domínio e executar o Windows 8.1, para fazer o Workplace Join e inscrevê-lo no Microsoft Intune, siga os passos abaixo:

1. Abra as **Definições do PC**.
2. Clique em **Rede** > **Área de trabalho**.
3. Clique em **Associar**.
4. Autentique na sua organização, forneça a autenticação multifator, se for necessário, e siga os passos apresentados.
5. Clique em **Ativar**.
6. Tente aceder novamente à aplicação.



#### <a name="add-your-work-or-school-account-to-windows"></a>Adicionar a sua conta profissional ou escolar ao Windows 


**Atualização de Aniversário do Windows 10 (Versão 1607):**

1. Abra a aplicação **Definições**.
2. Clique em **Contas** > **Aceder a trabalho ou escola**.
3. Clique em **Ligar**.
4. Autentique na sua organização, forneça a autenticação multifator, se for necessário, e siga os passos apresentados.
5. Tente aceder novamente à aplicação.


**Atualização do Windows 10 de Novembro de 2015 (Versão 1511):**

1. Abra a aplicação **Definições**.
2. Clique em **Contas** > **As suas contas**.
3. Clique em **Adicionar conta profissional ou escolar**.
4. Autentique na sua organização, forneça a autenticação multifator, se for necessário, e siga os passos apresentados.
5. Tente aceder novamente à aplicação.





## <a name="next-steps"></a>Passos seguintes
[Acesso condicional ao Azure Active Directory](active-directory-conditional-access.md)


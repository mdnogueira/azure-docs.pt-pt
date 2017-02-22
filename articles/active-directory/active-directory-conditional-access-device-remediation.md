---
title: Resolver problemas de acesso do Azure Active Directory | Microsoft Docs
description: "Passos de aprendizagem que pode seguir para resolver problemas de acesso com os recursos online da sua organização."
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
ms.date: 01/24/2017
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: fbabf6f2e1e588ba509c4da84ab1700b1b5d4f87
ms.openlocfilehash: ad9f9a8c5b370ffa916b9089ef3ce523fe0266c7


---
# <a name="troubleshooting-for-azure-active-directory-access-issues"></a>Resolver problemas de acesso do Azure Active Directory
Tenta aceder à intranet do SharePoint Online da sua organização e obtém uma mensagem de erro que indica "acesso negado". O que deve fazer?


Este artigo descreve os passos de remediação que podem ajudar a resolver problemas de acesso com os recursos online da sua organização.

Para o ajudar a resolver problemas de acesso do Azure Active Directory (Azure AD), aceda à secção no artigo que abrange a plataforma do dispositivo:

* Dispositivo Windows
* Dispositivo iOS (consulte novamente em breve para obter assistência com iPhones e iPads).
* Dispositivo Android (consulte novamente em breve para obter assistência com telemóveis e tablets Android).

## <a name="access-from-a-windows-device"></a>Acesso a partir de um dispositivo Windows
Se o seu dispositivo executar uma das seguintes plataformas, consulte as secções seguintes para obter a mensagem de erro apresentada quando tenta aceder a uma aplicação ou serviço:

* Windows 10
* Windows 8.1
* Windows 8
* Windows 7
* Windows Server 2016
* Windows Server 2012 R2
* Windows Server 2012
* Windows Server 2008 R2

### <a name="device-is-not-registered"></a>O dispositivo não está registado
Se o dispositivo não estiver registado no Azure AD e a aplicação estiver protegida por uma política baseada no dispositivo, poderá ver uma página com uma das seguintes mensagens de erro:

![Mensagens "Problemas difíceis de resolver" relativas a dispositivos não registados](./media/active-directory-conditional-access-device-remediation/01.png "Cenário")

Se o seu dispositivo estiver associado ao domínio do Active Directory na sua organização, tente o seguinte:

1. Confirme que iniciou sessão no Windows com a sua conta profissional (conta do Active Directory).
2. Ligue à sua rede empresarial através de uma rede privada virtual (VPN) ou DirectAccess.
3. Assim que estiver ligado, prima a tecla do logótipo do Windows + a tecla L para bloquear a sua sessão do Windows.
4. Introduza as credenciais da sua conta profissional para desbloquear a sua sessão do Windows.
5. Aguarde um minuto e tente aceder novamente à aplicação ou ao serviço.
6. Se vir a mesma página, clique na ligação **Mais detalhes** e, em seguida, contacte o seu administrador com os detalhes.

Se o dispositivo não estiver associado a um domínio e executar o Windows 10, tem duas opções:

* Executar a Associação do Azure AD
* Adicionar a sua conta profissional ou escolar ao Windows

Para obter mais informações sobre as diferenças entre estas opções, veja [Utilizar dispositivos Windows 10 na sua área de trabalho](active-directory-azureadjoin-windows10-devices.md).

Para executar a Associação do Azure AD, efetue os seguintes passos para a plataforma em que o seu dispositivo é executado. (A Associação do Azure AD não está disponível em Windows Phones.)

**Atualização de Aniversário do Windows 10**

1. Abra a aplicação **Definições**.
2. Clique em **Contas** > **Aceder a trabalho ou escola**.
3. Clique em **Ligar**.
4. Clique em **Associar este dispositivo ao Azure AD**.
5. Autentique na sua organização, forneça a autenticação multifator, se for necessário, e siga os passos apresentados.
6. Termine sessão e reinicie-a com a sua conta profissional.
7. Tente aceder novamente à aplicação.

**Atualização do Windows 10 de Novembro de 2015**

1. Abra a aplicação **Definições**.
2. Clique em **Sistema** > **Sobre**.
3. Clique em **Associar ao Azure AD**.
4. Autentique na sua organização, forneça a autenticação multifator, se for necessário, e siga os passos apresentados.
5. Termine sessão e reinicie-a com a sua conta profissional (conta do Azure AD).
6. Tente aceder novamente à aplicação.

Para adicionar a sua conta profissional ou escolar, siga os passos abaixo:

**Atualização de Aniversário do Windows 10**

1. Abra a aplicação **Definições**.
2. Clique em **Contas** > **Aceder a trabalho ou escola**.
3. Clique em **Ligar**.
4. Autentique na sua organização, forneça a autenticação multifator, se for necessário, e siga os passos apresentados.
5. Tente aceder novamente à aplicação.

**Atualização do Windows 10 de Novembro de 2015**

1. Abra a aplicação **Definições**.
2. Clique em **Contas** > **As suas contas**.
3. Clique em **Adicionar conta profissional ou escolar**.
4. Autentique na sua organização, forneça a autenticação multifator, se for necessário, e siga os passos apresentados.
5. Tente aceder novamente à aplicação.

Se o seu dispositivo não estiver associado a um domínio e executar o Windows 8.1, para fazer o Workplace Join e inscrevê-lo no Microsoft Intune, siga os passos abaixo:

1. Abra as **Definições do PC**.
2. Clique em **Rede** > **Área de trabalho**.
3. Clique em **Associar**.
4. Autentique na sua organização, forneça a autenticação multifator, se for necessário, e siga os passos apresentados.
5. Clique em **Ativar**.
6. Tente aceder novamente à aplicação.

### <a name="browser-is-not-supported"></a>O browser não é suportado
O acesso pode ser negado se estiver a tentar aceder a uma aplicação ou serviço com um dos seguintes browsers:

* Chrome, Firefox ou qualquer outro browser que não seja o Microsoft Edge ou o Microsoft Internet Explorer no Windows 10 ou Windows Server 2016
* Firefox no Windows 8.1, Windows 7, Windows Server 2012 R2, Windows Server 2012 ou Windows Server 2008 R2

Verá uma página de erro com o seguinte aspeto:

![Mensagem "Problemas difíceis de resolver" relativos a browsers não suportados](./media/active-directory-conditional-access-device-remediation/02.png "Cenário")

A única remediação é utilizar um browser que a aplicação suporte na plataforma do seu dispositivo.

## <a name="next-steps"></a>Passos seguintes
[Acesso condicional ao Azure Active Directory](active-directory-conditional-access.md)




<!--HONumber=Jan17_HO4-->



---
title: "Autenticar identidades sem palavras-passe através do Windows Hello para empresas e o Azure AD | Microsoft Docs"
description: "Fornece uma descrição geral do Windows Hello para empresas e informações adicionais sobre como implementar o Windows Hello para empresas."
services: active-directory
documentationcenter: 
author: femila
manager: femila
editor: 
tags: azure-classic-portal
ms.assetid: f907bb90-8776-46ca-9e12-279949af66ff
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2017
ms.author: markvi
ms.openlocfilehash: 62adf8a9fd4400a056e2c0f59c79431acbad5865
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="authenticating-identities-without-passwords-through-windows-hello-for-business"></a>Autenticar identidades sem palavras-passe através do Windows Hello para empresas
Os métodos de autenticação com palavras-passe autónoma atuais não forem suficientes para manter os utilizadores. Os utilizadores reutilizarem e se esqueça de palavras-passe. Palavras-passe são breachable, phishable, suscetível a cracks e guessable. Recebem também difíceis de lembrar-se e suscetível a ataques, como "[passagem do hash](https://technet.microsoft.com/dn785092.aspx)".

## <a name="about-windows-hello-for-business"></a>Sobre o Windows Hello para empresas
Windows Hello para empresas é uma chave pública/privada ou uma abordagem de autenticação baseada em certificado para as organizações e os consumidores que vai mais além palavras-passe. Esta forma de autenticação baseia-se nas credenciais de par de chaves que podem substituir as palavras-passe e são resistentes a falhas, thefts e phishing.

 Windows Hello para empresas permite que um utilizador autenticar-se uma conta Microsoft, uma conta do Windows Server Active Directory, uma conta do Microsoft Azure Active Directory (Azure AD) ou um serviço de terceiros que suporta a autenticação rápido identidade Online (FIDO). Depois de uma verificação de dois passos iniciais durante o Windows Hello para inscrição de negócio, do Windows Hello para empresas está configurada no dispositivo do utilizador, e o utilizador define um gesto, que pode ser o Windows Hello ou um PIN. O utilizador fornece o gesto para verificar a sua identidade. Windows, em seguida, utiliza o Windows Hello para empresas para autenticar o utilizador e ajudá-los para aceder aos serviços e recursos protegidos.

A chave privada é disponibilizada apenas através de um "gesto de utilizador" como um PIN, biometria ou um dispositivo remoto como um smart card que o utilizador utiliza para iniciar sessão no dispositivo. Esta informação está ligada a um certificado ou um par de chaves assimétrico. A chave privada é hardware atestado se o dispositivo tem um chip Trusted Platform Module (TPM). A chave privada nunca sai do dispositivo.

A chave pública está registada no Azure Active Directory e o Windows Server Active Directory (para no local). Fornecedores de identidade (IDPs) validar o utilizador ao mapear a chave pública do utilizador para a chave privada e fornecem informações de início de sessão através de um tempo de palavra-passe (OTP), PhoneFactor ou um mecanismo de notificação diferentes.

## <a name="why-enterprises-should-adopt-windows-hello-for-business"></a>Por que razão as empresas devem adotar o Windows Hello para empresas
Ao ativar a Windows Hello para empresas, o empresas podem efetuar os seus recursos ainda mais seguros por:

* Configurar o Windows Hello para empresas com uma opção preferencial de hardware. Isto significa que as chaves serão geradas no TPM 1.2 ou 2.0 quando disponível. Quando o TPM não estiver disponível, o software irá gerar a chave.
* Definir a complexidade e o comprimento do PIN, e se a utilização de Olá está ativada na sua organização.
* Configurar o Windows Hello para empresas suportar cenários como o smart card através da utilização de confiança baseada em certificado.

## <a name="how-windows-hello-for-business-works"></a>Como Windows Hello para empresas funciona
1. Chaves são geradas no hardware, software ou TPM. Muitos dispositivos têm um chip TPM incorporado que protege o hardware por integrar as chaves criptográficas em dispositivos. TPM 1.2 ou 2.0 gera chaves nem certificados que são criados a partir das chaves geradas.
2. O TPM attests estas chaves de hardware vinculados.
3. Um gesto de desbloqueio único desbloqueia o dispositivo. Este gesto permite o acesso a vários recursos se o dispositivo estiver associado a um domínio ou do Azure AD associados.

## <a name="how-the-windows-hello-for-business-lifecycle-works"></a>Como o Windows Hello para empresas ciclo de vida funciona
![Windows Hello para empresas ciclo de vida](./media/active-directory-azureadjoin/active-directory-azureadjoin-microsoft-passport.png)

O diagrama anterior mostra o par de chaves públicas/privadas e a validação pelo fornecedor de identidade. Cada um destes passos é explicada detalhadamente aqui:

1. O utilizador prove a sua identidade através de vários métodos de proofing incorporados (gestos, smart cards físicos, autenticação multifator) e envia estas informações para um fornecedor de identidade (IDP), como o Azure Active Directory ou no local do Active Directory.
2. O dispositivo, em seguida, cria a chave, attests a chave, demora a parte desta chave pública, anexa-lo com as instruções de estação, inicia sessão e envia-a para IDP para registar a chave.
3. Assim que IDP regista a parte pública da chave, IDP desafia o dispositivo para iniciar sessão com a parte da chave privada.
4. Em seguida, IDP valida e emite o token de autenticação que permite que o utilizador e o acesso de dispositivo os recursos protegidos. IDPs podem escrever aplicações de várias plataformas ou utilizar o suporte de browsers (através de APIs de JavaScript/Webcrypto) para criar e utilizar o Windows Hello para empresas as credenciais para os seus utilizadores.

## <a name="the-deployment-requirements-for-windows-hello-for-business"></a>Os requisitos de implementação do Windows Hello para empresas
### <a name="at-the-enterprise-level"></a>Ao nível da empresa
* A empresa tem uma subscrição do Azure.

### <a name="at-the-user-level"></a>Ao nível do utilizador
* O computador do utilizador executa o Windows 10 Professional ou Enterprise.

Para obter instruções de implementação detalhados, consulte [ativar Windows Hello para empresas na organização](active-directory-azureadjoin-passport-deployment.md).

## <a name="additional-information"></a>Informações adicionais
* [Windows 10 para a empresa: formas de utilizar os dispositivos para o trabalho](active-directory-azureadjoin-windows10-devices-overview.md)
* [Aumentar as capacidades de nuvem para dispositivos com Windows 10 através da Associação do Azure Active Directory](active-directory-azureadjoin-user-upgrade.md)
* [Saiba mais sobre os cenários de utilização da Associação do Azure AD](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Ligar a dispositivos associados a um domínio ao Azure AD para experiências com o Windows 10](active-directory-azureadjoin-devices-group-policy.md)
* [Configurar a Associação do Azure AD](active-directory-azureadjoin-setup.md)


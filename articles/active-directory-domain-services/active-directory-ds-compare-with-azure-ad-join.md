---
title: "Comparar a associação do Azure AD e o Azure serviços de domínio do Active Directory | Microsoft Docs"
description: "Decidir entre a associação do Azure AD e serviços de domínio do Azure AD"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: mahesh-unnikrishnan
editor: curtand
ms.assetid: 31a71d36-58c1-4839-b958-80da0c6a77eb
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/26/2017
ms.author: maheshu
ms.openlocfilehash: 8f34688c53dc909b0ec97df34bbfc7a24209a314
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/31/2017
---
# <a name="choose-between-azure-active-directory-join-and-azure-active-directory-domain-services"></a>Escolha entre a associação do Azure Active Directory e os serviços de domínio do Active Directory do Azure
Este artigo descreve as diferenças entre a associação do Azure Active Directory (AD) e serviços de domínio do Azure AD e ajuda-o que escolher, com base nos seus casos de utilização.

## <a name="azure-ad-registered-and-azure-ad-joined-devices"></a>Azure AD registados e dispositivos associados ao Azure AD
Azure AD permite-lhe gerir a identidade dos dispositivos utilizados pela sua organização e controlo de acesso aos recursos da empresa a partir destes dispositivos. Os utilizadores podem registar o respetivo dispositivo pessoal (traga a sua própria) com o Azure AD, que fornece o dispositivo com uma identidade. Posteriormente, o Azure AD pode autenticar o dispositivo quando um utilizador inicia sessão com o Azure AD e utiliza o dispositivo para aceder a recursos protegidos. Além disso, pode gerir o dispositivo através do software de gestão de dispositivos móveis (MDM) como o Microsoft Intune. Esta funcionalidade permite-lhe restringir o acesso a recursos confidenciais dos dispositivos geridos e conformes de política.

Também pode associar organização pertencentes à empresa de dispositivos para o Azure AD. Este mecanismo oferece vantagens mesmas de registar um dispositivo pessoal com o Azure AD. Além disso, os utilizadores podem iniciar sessão dispositivo com as respetivas credenciais empresariais. Dispositivos do Azure AD associada dão-lhe as seguintes vantagens:
* Single-sign-on (SSO) a aplicações protegidas pelo Azure AD
* Enterprise em conformidade com a política de roaming de definições de utilizador em todos os dispositivos.
* Acesso à loja Windows para empresas com as suas credenciais de empresa.
* Windows Hello para empresas
* Acesso restrito, para aplicações e recursos dos dispositivos em conformidade com a política empresarial.

| **Tipo de dispositivo** | **Plataformas de dispositivos** | **Mecanismo de** |
|:---| --- | --- |
| Dispositivos pessoais | Windows 10, iOS, Android, Mac OS | Azure AD registado |
| Organização pertencentes à empresa de dispositivos não associados ao AD no local | Windows 10 | Azure AD associado |
| Organização associado a um local de dispositivos pertencentes à empresa AD | Windows 10 | Híbrida do Azure AD associada |

Num Azure AD associada ou dispositivo registado, autenticação de utilizador acontece utilizar protocolos de OAuth/OpenID Connect com base modernos. Estes protocolos foram concebidos para funcionar através da internet e são ótimos para cenários móveis em que os utilizadores aceder a recursos da empresa em qualquer lugar.


## <a name="domain-join-to-azure-ad-domain-services-managed-domains"></a>Associação a um domínio para domínios geridos de serviços de domínio do Azure AD
Serviços de domínio do AD do Azure fornece um domínio gerido do AD numa rede virtual do Azure. Pode associar computadores a este domínio gerido utilizando os mecanismos de associação de domínio tradicional. Cliente Windows (Windows 7, Windows 10) e as máquinas do Windows Server podem ser associadas ao domínio gerido. Além disso, também pode associar máquinas Linux e Mac OS ao domínio gerido. Quando associar uma máquina a um domínio do AD, os utilizadores podem iniciar sessão máquina utilizando as respetivas credenciais empresariais. Estas máquinas podem ser geridas utilizando a política de grupo, deste modo, impor a compatibilidade com as políticas de segurança da sua organização.

Num computador associado a um domínio, a autenticação de utilizador ocorre através de protocolos de autenticação de NTLM ou Kerberos. O computador associado a um domínio precisa de linha de visão para os controladores de domínio do domínio gerido por ordem para a autenticação de utilizador trabalhar. Por conseguinte, o domínio associado máquina tem de estar na mesma rede virtual que o domínio gerido. Em alternativa, o domínio associado máquina tem de ser ligado ao domínio gerido através de uma rede virtual em modo de peering ou através de uma ligação de VPN/ExpressRoute de site para site. Assim, este mecanismo não é uma excelente opção para dispositivos móveis ou ligar a recursos a partir de fora da rede empresarial.

> [!NOTE]
> Tecnicamente, é possível associar uma estação de trabalho do cliente no local ao domínio gerido através de uma ligação de VPN ou ExpressRoute do site para site. No entanto, para dispositivos de utilizador final, recomendamos vivamente que utilize a registar o dispositivo com o Azure AD (dispositivos pessoais) ou associação do dispositivo para o Azure AD (dispositivos empresariais). Este mecanismo funciona melhor através da internet e permite aos utilizadores finais trabalhar a partir de qualquer lugar. Serviços de domínio do AD do Azure é excelente para Windows ou Linux Server máquinas virtuais implementadas nas suas redes virtuais do Azure, em que as aplicações são implementadas.


## <a name="summary---key-differences"></a>Resumo - principais diferenças
| **Aspeto** | **Associação ao Azure AD** | **Serviços de domínio do Azure AD** |
|:---| --- | --- |
| Dispositivo controlado pelo | Azure AD | Do domínio gerido dos serviços de domínio do AD do Azure |
| Representação no diretório | Objetos de dispositivo no diretório do Azure AD. | Objetos de computador no domínio gerido AAD DS. |
| Autenticação | OAuth/OpenID Connect baseada em protocolos | Kerberos, protocolos NTLM |
| Gestão | Software de gestão de dispositivos móveis (MDM) como o Intune | Política de grupo |
| Redes | Funciona através da internet | Requer que as máquinas na mesma rede virtual que o domínio gerido.|
| Ótimo para... | Dispositivos móveis ou de ambiente de trabalho de utilizador final | Máquinas virtuais de servidor implementadas no Azure |


## <a name="next-steps"></a>Passos seguintes
### <a name="learn-more-about-azure-ad-domain-services"></a>Saiba mais sobre os serviços de domínio do Azure AD
* [Descrição geral dos serviços de domínio do Azure AD](active-directory-ds-overview.md)
* [Funcionalidades](active-directory-ds-features.md)
* [Cenários de implementação](active-directory-ds-scenarios.md)
* [Saber se os serviços de domínio do Azure AD se adequa aos seus casos de utilização](active-directory-ds-comparison.md)
* [Compreender a forma como os serviços de domínio do Azure AD sincroniza com o seu diretório do Azure AD](active-directory-ds-synchronization.md)

### <a name="learn-more-about-azure-ad-join"></a>Saiba mais sobre a associação do Azure AD
* [Introdução à gestão de dispositivos no Azure Active Directory](../active-directory/device-management-introduction.md)

### <a name="get-started-with-azure-ad-domain-services"></a>Introdução ao Azure AD Domain Services
* [Ativar os serviços de domínio do Azure AD através do portal do Azure](active-directory-ds-getting-started.md)

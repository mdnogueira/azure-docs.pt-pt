---
title: "Introdução à gestão de dispositivos no Azure Active Directory | Microsoft Docs"
description: "Saiba como gestão de dispositivos pode ajudá-lo a obter o controlo sobre os dispositivos que estão a aceder a recursos no seu ambiente."
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
ms.openlocfilehash: fdc597c9e88e932eace5962c79af4dc3805685a0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="introduction-to-device-management-in-azure-active-directory"></a>Introdução à gestão de dispositivos no Azure Active Directory

No mundo mobile-primeiro, primeiro de nuvem, o Azure Active Directory (Azure AD) permite início de sessão único para dispositivos, aplicações e serviços em qualquer lugar. Com a proliferação de dispositivos - incluindo Bring Your Own Device (BYOD), profissionais de TI são confrontados com dois objetivos adversária:

- Capacitar os utilizadores finais para serem produtivos, onde quer que e sempre
- Proteger os ativos da empresa em qualquer altura

Através de dispositivos, os utilizadores estão a obter acesso para os recursos da empresa. Para proteger os ativos empresariais, como um administrador de TI, pretende têm controlo sobre estes dispositivos. Isto permite-lhe para se certificar de que os utilizadores acedem aos seus recursos dos dispositivos que cumprem as normas de segurança e conformidade. 

Gestão de dispositivos também é o alicerce da [acesso condicional baseado no dispositivo](active-directory-conditional-access-policy-connected-applications.md). Com o acesso condicional baseado no dispositivo, pode certificar-se de que o acesso aos recursos no seu ambiente só é possível com dispositivos fidedignos.   

Este tópico explica como funciona a gestão de dispositivos no Azure Active Directory.

## <a name="getting-devices-under-the-control-of-azure-ad"></a>Inscrever os dispositivos sob o controlo do Azure AD

Para obter um dispositivo sob o controlo do Azure AD, tem duas opções:

- A registar 
- Associar

**Registar** um dispositivo para o Azure AD permite-lhe gerir a identidade de um dispositivo. Quando um dispositivo é registado, o registo de dispositivos do Azure AD fornece o dispositivo com uma identidade que é utilizado para autenticar o dispositivo quando um utilizador inicia sessão com o Azure AD. Pode utilizar a identidade para ativar ou desativar um dispositivo.

Quando combinada com uma solução de gestão de dispositivos móveis, como o Microsoft Intune, os atributos do dispositivo no Azure AD são atualizados com informações adicionais sobre o dispositivo. Isto permite criar regras de acesso condicional que impõem o acesso a partir de dispositivos para cumprir as normas de segurança e conformidade. Para obter mais informações sobre a inscrição de dispositivos no Microsoft Intune, consulte inscrever dispositivos para gestão no Intune.

**Associar** um dispositivo é uma extensão para registar um dispositivo. Isto significa que fornece-lhe com todas as vantagens de registar um dispositivo e além disto, também altera o estado do local de um dispositivo. Alteração do Estado da local permite aos utilizadores iniciar sessão num dispositivo utilizando um organizacional conta escolar ou profissional, em vez de uma conta pessoal.

## <a name="azure-ad-registered-devices"></a>Dispositivos do Azure AD registado   

O objetivo de dispositivos do Azure AD registado é para lhe fornecer suporte para o **Bring Your Own Device (BYOD)** cenário. Neste cenário, um utilizador pode aceder a recursos da organização do Azure Active Directory controladas através de um dispositivo pessoal.  

![Dispositivos do Azure AD registado](./media/device-management-introduction/03.png)

O acesso baseia-se numa conta escolar ou profissional que foi introduzida no dispositivo.  
Por exemplo, o Windows 10 permite aos utilizadores adicionar uma conta escolar ou profissional para um computador pessoal, tablet ou telemóvel.  
Quando um utilizador tiver adicionado um trabalho ou conta profissional, o dispositivo é registado com o Azure AD e opcionalmente inscrito no sistema de gestão (MDM) de dispositivos móveis que tenha configurado a sua organização. Os utilizadores da sua organização podem adicionar um trabalho conta escolar ou profissional para um dispositivo pessoal comodamente:

- Ao aceder a uma aplicação de trabalho pela primeira vez
- Manualmente através do **definições** menu no caso do Windows 10 

Pode configurar dispositivos do Azure AD registado para o Windows 10, iOS, Android e macOS.

## <a name="azure-ad-joined-devices"></a>Dispositivos associados ao Azure AD

O objetivo de dispositivos do Azure AD associado é simplificar:

- Implementações de Windows de dispositivos pertencentes à empresa trabalho 
- Acesso para aplicações organizacionais e de recursos a partir de qualquer dispositivo de Windows

![Dispositivos do Azure AD registado](./media/device-management-introduction/02.png)


Estes objetivos são conseguidos fornecendo os seus utilizadores com uma experiência self-service para inscrever os dispositivos pertencentes à empresa trabalho sob o controlo do Azure AD.  
**A associação do Azure AD** foi concebida para organizações que estão na nuvem primeiro / apenas na nuvem. Normalmente, são dimensionados de pequenas e médias empresas que não tenham uma infraestrutura do Windows Server Active Directory no local. 

Implementar dispositivos do Azure AD associado disponibiliza as seguintes vantagens:

- **Single-Sign-On (SSO)** para o Azure gerida por serviços e aplicações SaaS. Os seus utilizadores não verão a pedidos de autenticação adicional quando acedem a recursos de trabalho. A funcionalidade SSO é mesmo quando não estiverem ligados à rede de domínio disponíveis.

- **Empresarial compatível roaming** de definições de utilizador em todos os dispositivos associados a um. Os utilizadores não precisam de ligar a uma conta Microsoft (por exemplo, Hotmail) para ver as definições em dispositivos.

- **Acesso à loja Windows para empresas** utilizando a conta do AD. Os utilizadores podem escolher entre um inventário das aplicações previamente selecionadas pela organização.

- **Windows Hello** suporte para seguro e conveniente acesso aos recursos de trabalho.

- **Restrição de acesso** para a apenas os dispositivos que cumprem a política de conformidade de aplicações.

Enquanto a associação do Azure AD destina-se principalmente nas organizações que não tenham uma infraestrutura do Windows Server Active Directory no local, pode certamente também utilizá-lo em cenários em que:

- Não é possível utilizar uma associação de domínio no local, por exemplo, se necessitar de dispositivos móveis, como telemóveis e tablets sob o controlo.

- Principalmente, os utilizadores tem de aceder ao Office 365 ou outras aplicações de SaaS integradas com o Azure AD.

- Pretende gerir um grupo de utilizadores no Azure AD em vez de no Active Directory. Isto pode aplicar, por exemplo, para trabalhadores sazonais, contratantes ou os estudantes que estejam.

- Pretende fornecer capacidades de associação para os funcionários de sucursais remotas limitado infraestrutura no local.

Pode configurar dispositivos do Azure AD associado para dispositivos Windows 10.


## <a name="hybrid-azure-ad-joined-devices"></a>Dispositivos associados ao Azure AD híbrido

Para mais do que um decade, muitas organizações utilizou a associação a um domínio ao seu diretório Active Directory no local para ativar:

- Departamentos de TI para gerir dispositivos pertencentes à empresa trabalho a partir de uma localização central.

- Aos utilizadores iniciar sessão para os seus dispositivos com o respetivo Active Directory escolar ou profissional contas. 

Normalmente, as organizações com um requisitos de espaço no local dependem imaging métodos para aprovisionar dispositivos e geralmente utilizam **System Center Configuration Manager (SCCM)** ou **(GP) de política de grupo** geri-los.

Se no local para o seu ambiente tem requisitos de espaço do AD e também queira benefício as capacidades fornecidas pelo Azure Active Directory, pode implementar híbrida do Azure AD associado dispositivos. Estes são os dispositivos que estejam ambos, associado ao seu no local do Active Directory e o Azure Active Directory.

![Dispositivos do Azure AD registado](./media/device-management-introduction/01.png)


Deverá utilizar o Azure AD híbrido associado dispositivos se:

- Tiver aplicações de Win32 implementadas nesses dispositivos que utilizam o NTLM / Kerberos.

- Precisa de GP ou SCCM / DCM para gerir dispositivos.

- Pretende continuar a utilizar soluções de processamento de imagens para configurar dispositivos para os seus empregados.

Pode configurar híbrida Azure AD dispositivos associados a um para Windows 10 e dispositivos de nível inferior, tais como o Windows 8 e Windows 7.

## <a name="summary"></a>Resumo

Gestão de dispositivos no Azure AD, pode: 

- Simplificar o processo de colocar dispositivos sob o controlo do Azure AD

- Fornecer os seus utilizadores com acesso fácil de utilizar a recursos baseados na nuvem da sua organização

Como uma regra de um botão, deve utilizar:

- Azure AD registado dispositivos para dispositivos pessoais

- Azure AD associada dispositivos para dispositivos que não estão associados a um local AD 

- Azure AD híbrido associado dispositivos para dispositivos que estão associados a um local AD     




## <a name="next-steps"></a>Passos seguintes

- Para obter uma descrição geral de como gerir dispositivos no portal do Azure, consulte [gerir dispositivos através do portal do Azure](device-management-azure-portal.md)

- Para saber mais sobre o acesso condicional baseado no dispositivo, consulte o artigo [configurar políticas de acesso condicional baseado no dispositivo do Azure Active Directory](active-directory-conditional-access-policy-connected-applications.md).

- A configuração:
    - Azure Active Directory registado dispositivos Windows 10, consulte [como configurar o Azure Active Directory registado dispositivos Windows 10](device-management-azuread-registered-devices-windows10-setup.md)
    - Dispositivos associados ao Azure Active Directory, consulte [dispositivos associados ao como configurar o Azure Active Directory](device-management-azuread-joined-devices-setup.md)
    - Híbrida do Azure AD associado dispositivos, consulte [como para configurar híbrida do Azure Active Directory dispositivos associados a um](device-management-hybrid-azuread-joined-devices-setup.md).



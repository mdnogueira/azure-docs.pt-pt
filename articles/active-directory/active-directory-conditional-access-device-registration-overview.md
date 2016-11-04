---
title: Descrição geral do Registo de Dispositivos do Azure Active Directory | Microsoft Docs
description: é a base para cenários de acesso condicional baseado no dispositivo. Quando um dispositivo é registado, o Registo de Dispositivos do Azure Active Directory aprovisiona o dispositivo com uma identidade que é utilizada para autenticar o dispositivo quando o utilizador inicia sessão.
services: active-directory
keywords: registo de dispositivos, ativar o registo de dispositivos, registo de dispositivos e MDM
documentationcenter: ''
author: femila
manager: swadhwa
editor: ''

ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/27/2016
ms.author: Markvi

---
# Introdução ao Registo de Dispositivos do Azure Active Directory
O Registo de Dispositivos do Azure Active Directory é a base para cenários de acesso condicional baseado no dispositivo. Quando é registado um dispositivo, o Registo de Dispositivos do Azure Active Directory fornece ao dispositivo uma identidade que é utilizada para autenticar o dispositivo quando o utilizador inicia sessão. O dispositivo autenticado e os atributos do dispositivo podem então ser utilizados para aplicar políticas de acesso condicional para aplicações alojadas na nuvem e no local.

Ao serem combinados com uma solução de gestão de dispositivos móveis (MDM), como o Microsoft Intune, os atributos do dispositivo no Azure Active Directory são atualizados com informações adicionais sobre o dispositivo. Isto permite criar regras de acesso condicional que impõem o acesso a partir de dispositivos para cumprir as normas de segurança e conformidade. Para obter mais informações sobre a inscrição de dispositivos no Microsoft Intune, veja [Inscrever dispositivos para gestão no Intune](https://docs.microsoft.com/intune/deploy-use/enroll-devices-in-microsoft-intune).

## Cenários ativados pelo Registo de Dispositivos do Azure Active Directory
O Registo de Dispositivos do Azure Active Directory inclui suporte para dispositivos iOS, Android e Windows. Os cenários individuais que recorrem ao Registo de Dispositivos do Azure AD poderão ter requisitos mais específicos e suporte da plataforma. Estes cenários são os seguintes:

* **Acesso condicional a aplicações alojadas no local**: pode utilizar dispositivos registados com políticas de acesso para aplicações configuradas para utilizar o AD FS com Windows Server 2012 R2. Para mais informações sobre como configurar o acesso condicional no local, consulte [Configurar o Acesso Condicional no local com o Registo de Dispositivos do Azure Active Directory](active-directory-conditional-access-on-premises-setup.md).
* **Acesso condicional para aplicações do Office 365 com Microsoft Intune**: os administradores de TI podem aprovisionar políticas de dispositivos de acesso condicional para proteger recursos empresariais, permitindo ao mesmo tempo que os trabalhadores na área da informação em dispositivos compatíveis acedam aos serviços. Para obter mais informações, veja [Conditional Access Device Policies for Office 365 services (Políticas de Dispositivos de Acesso Condicional para os serviços do Office 365)](active-directory-conditional-access-device-policies.md).

## Configurar o Registo de Dispositivos do Azure Active Directory
Tem de ativar o Registo de Dispositivos do Azure AD de modo a que os dispositivos móveis possam detetar o serviço, procurando registos DNS conhecidos. Tem de configurar o DNS da sua empresa para que os dispositivos com Windows 10, Windows 8.1, Windows 7, Android e iOS possam ver e utilizar o serviço.
Pode ver e ativar/desativar dispositivos registados através do Portal do Administrador do Azure Active Directory.

> [!NOTE]
> Para obter as instruções mais recentes sobre como configurar o registo de dispositivos automático, veja [How to setup automatic registration of Windows domain joined devices with Azure Active Directory (Como configurar o registo automático de dispositivos Windows associados ao domínio com o Active Directory)](active-directory-conditional-access-automatic-device-registration-setup.md).
> 
> 

### Ativar o Serviço de Registo de Dispositivos do Azure Active Directory
1. Inicie sessão como administrador no portal do Microsoft Azure.
2. No painel esquerdo, selecione **Active Directory**.
3. No separador **Diretório** selecione o diretório.
4. Selecione o separador **Configurar**.
5. Desloque o ecrã para a secção denominada **Dispositivos**.
6. Selecione **TODOS** para **UTILIZADORES PODEM UTILIZAR DISPOSITIVOS ASSOCIADOS À ÁREA DE TRABALHO**.
7. Selecione o número máximo de dispositivos que pretende autorizar por utilizador.

> [!NOTE]
> A inscrição com o Microsoft Intune ou a Gestão de Dispositivos Móveis do Office 365 requer o Workplace Join. Se tiver configurado qualquer um destes serviços, a opção TODOS estará selecionada e o botão NENHUM estará desativado.
> 
> 

Por predefinição, a autenticação de dois fatores não está ativada para o serviço. No entanto, recomenda-se a autenticação de dois fatores aquando do registo de um dispositivo.

* Antes de solicitar a autenticação de dois fatores para este serviço, deve configurar o fornecedor de autenticação de dois fatores no Azure Active Directory e configurar as contas de utilizador do Multi-Factor Authentication. Consulte [Adicionar o Multi-Factor Authentication ao Azure Active Directory](../multi-factor-authentication/multi-factor-authentication-get-started-cloud.md)
* Se utilizar o AD FS com o Windows Server 2012 R2, tem de configurar um módulo de autenticação de dois fatores no AD FS. Consulte [Utilizar o Multi-Factor Authentication com Serviços de Federação do Active Directory](../multi-factor-authentication/multi-factor-authentication-get-started-server.md).

## Configurar a deteção do Registo de Dispositivos do Azure Active Directory
Os dispositivos com Windows 7 e Windows 8.1 detetarão o serviço do Registo de Dispositivos ao combinar o nome da conta de utilizador e o nome do servidor do Registo de Dispositivos conhecido.

Tem de criar um registo CNAME no DNS que aponte para o registo A associado ao serviço do Registo de Dispositivos do Azure Active Directory. O registo CNAME tem de utilizar o prefixo conhecido enterpriseregistration, seguido pelo sufixo UPN utilizado pelas contas de utilizador na organização. Se a organização utilizar vários sufixos UPN, deverão ser criados vários registos CNAME no DNS.

Por exemplo, se utilizar dois sufixos UPN na organização com o nome @contoso.com e @região.contoso.com, deverá criar os seguintes registos DNS.

| Entrada | Tipo | Endereço |
| --- | --- | --- |
| enterpriseregistration.contoso.com |CNAME |enterpriseregistration.windows.net |
| enterpriseregistration.region.contoso.com |CNAME |enterpriseregistration.windows.net |

## Ver e gerir objetos de dispositivos no Azure Active Directory
1. No portal do Administrador do Azure, pode ver, bloquear e desbloquear dispositivos. Um dispositivo bloqueado deixará de ter acesso a aplicações configuradas para permitir apenas dispositivos registados.
2. Inicie sessão como administrador no Portal do Microsoft Azure.
3. No painel esquerdo, selecione **Active Directory**.
4. Selecione o diretório.
5. Selecione o separador **Utilizadores**. Em seguida, selecione um utilizador para ver os respetivos dispositivos
6. Selecione o separador **Dispositivos**.
7. Selecione **Dispositivos Registados** no menu pendente.
8. Aqui pode ver, bloquear ou desbloquear os dispositivos registados dos utilizadores.

## Tópicos adicionais
Pode registar os dispositivos Associados a um Domínio Windows 7 e Windows 8.1 com o Registo de Dispositivos do Azure AD. Os tópicos seguintes disponibilizam mais informações sobre os pré-requisitos e os passos necessários para configurar o registo de dispositivos em dispositivos Windows 7 e Windows 8.1.

* [Registo Automático de Dispositivos com o Azure Active Directory para Dispositivos Associados a um Domínio Windows](active-directory-conditional-access-automatic-device-registration.md)
* [Configurar o registo automático de dispositivos para dispositivos associados a um domínio Windows 7](active-directory-conditional-access-automatic-device-registration-windows7.md)
* [Configurar o registo automático de dispositivos para dispositivos associados a um domínio Windows 8.1](active-directory-conditional-access-automatic-device-registration-windows-8-1.md)
* [Registo automático de dispositivos com o Azure Active Directory para dispositivos com associados a um domínio Windows 10](active-directory-azureadjoin-devices-group-policy.md)

<!--HONumber=Sep16_HO4-->



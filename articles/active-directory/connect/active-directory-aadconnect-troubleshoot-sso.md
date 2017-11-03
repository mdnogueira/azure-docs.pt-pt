---
title: "Do Azure AD Connect: Resolução de problemas totalmente integrada Single Sign-On | Microsoft Docs"
description: "Este tópico descreve como resolver problemas do Azure Active Directory totalmente integrada Single Sign-On (Azure AD totalmente integrada SSO)."
services: active-directory
keywords: "o que é o Azure AD Connect, a instalação do Active Directory, os componentes necessários para o Azure AD, SSO, o início de sessão único"
documentationcenter: 
author: swkrish
manager: femila
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/19/2017
ms.author: billmath
ms.openlocfilehash: b383a21500c753d8d2fe6747756541a3ff94ef02
ms.sourcegitcommit: c5eeb0c950a0ba35d0b0953f5d88d3be57960180
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2017
---
# <a name="troubleshoot-azure-active-directory-seamless-single-sign-on"></a>Resolver problemas do Azure Active Directory totalmente integrada Single Sign-On

Este artigo ajuda-o a encontrar informações sobre problemas comuns sobre do Azure AD totalmente integrada Single Sign-On de resolução de problemas.

## <a name="known-issues"></a>Problemas conhecidos

- Em alguns casos, a ativação SSO totalmente integrada pode demorar até 30 minutos.
- Suporte de browser Edge não está disponível.
- Iniciar os clientes do Office, especialmente em cenários de computador partilhado, originar pede extra início de sessão de utilizadores. Os utilizadores terão de introduzir frequentemente os respetivos nomes de utilizador, mas não as palavras-passe.
- Se tiver êxito SSO totalmente integrada, o utilizador não é dada a oportunidade de optar "Manter a minha sessão iniciada". Devido a este comportamento não funcionam cenários de mapeamento do OneDrive e do SharePoint.
- SSO totalmente integrada não funciona no modo privado de navegação no Firefox.
- SSO totalmente integrada não funciona no Internet Explorer, quando o modo de proteção avançada está ativado.
- SSO totalmente integrada não funciona em browsers para dispositivos móveis em dispositivos iOS e Android.
- Se estiver a sincronizar 30 ou mais florestas do AD, não é possível ativar a SSO totalmente integrado com o Azure AD Connect. Como solução, pode [ativar manualmente](#manual-reset-of-azure-ad-seamless-sso) a funcionalidade no seu inquilino.
- A adição de URLs do serviço do Azure AD (https://autologon.microsoftazuread-sso.com, https://aadg.windows.net.nsatc.net) para a zona "Sites fidedignos" em vez da zona "Local intranet" **bloqueia os utilizadores de início de sessão**.

## <a name="check-status-of-the-feature"></a>Verifique o estado da funcionalidade

Certifique-se de que a funcionalidade de SSO totalmente integrado ainda é **ativado** no seu inquilino. Pode verificar o estado, acedendo ao **do Azure AD Connect** painel o [Centro de administração do Azure Active Directory](https://aad.portal.azure.com/).

![Centro de administração do Active Directory do Azure - painel do Azure AD Connect](./media/active-directory-aadconnect-sso/sso10.png)

## <a name="sign-in-failure-reasons-on-the-azure-active-directory-admin-center-needs-premium-license"></a>Motivos da falha de início de sessão no Centro de administração do Azure Active Directory (necessita de licença Premium)

Se o seu inquilino tem uma licença do Azure AD Premium associada à mesma, pode também ver o [relatório de atividade de início de sessão](../active-directory-reporting-activity-sign-ins.md) no [Centro de administração do Azure Active Directory](https://aad.portal.azure.com/).

![Centro de administração do Active Directory do Azure - relatório de inícios de sessão](./media/active-directory-aadconnect-sso/sso9.png)

Navegue para **do Azure Active Directory** -> **inícios de sessão** no [Centro de administração do Azure Active Directory](https://aad.portal.azure.com/) e clique em atividade de início de sessão de um utilizador específico. Procure o **código de erro de início de sessão em** campo. Mapear o valor desse campo um motivo da falha e a resolução utilizando a seguinte tabela:

|Código de erro de início de sessão|Motivo da falha de início de sessão|Resolução
| --- | --- | ---
| 81001 | A permissão do Kerberos do utilizador é demasiado grande. | Reduza as associações de grupo do utilizador e tente novamente.
| 81002 | Não é possível validar a permissão do Kerberos do utilizador. | Consulte [resolução de problemas de lista de verificação](#troubleshooting-checklist).
| 81003 | Não é possível validar a permissão do Kerberos do utilizador. | Consulte [resolução de problemas de lista de verificação](#troubleshooting-checklist).
| 81004 | Falha ao tentar a autenticação do Kerberos. | Consulte [resolução de problemas de lista de verificação](#troubleshooting-checklist).
| 81008 | Não é possível validar a permissão do Kerberos do utilizador. | Consulte [resolução de problemas de lista de verificação](#troubleshooting-checklist).
| 81009 | "Não é possível validar a permissão de Kerberos de utilizador. | Consulte [resolução de problemas de lista de verificação](#troubleshooting-checklist).
| 81010 | O SSO integrado falhou porque a permissão do Kerberos do utilizador expirou ou é inválida. | Utilizador tem de iniciar sessão a partir de um dispositivo associado ao domínio dentro da sua rede empresarial.
| 81011 | Não é possível encontrar o objeto de utilizador com base nas informações na permissão do Kerberos do utilizador. | Utilize o Azure AD Connect para sincronizar informações de utilizador com o Azure AD.
| 81012 | O utilizador que está a tentar iniciar sessão no Azure AD é diferente do utilizador que tem sessão iniciada no dispositivo. | Inicie sessão a partir de um dispositivo diferente.
| 81013 | Não é possível encontrar o objeto de utilizador com base nas informações na permissão do Kerberos do utilizador. |Utilize o Azure AD Connect para sincronizar informações de utilizador com o Azure AD. 

## <a name="troubleshooting-checklist"></a>Lista de verificação de resolução de problemas

Utilize a lista de verificação seguinte para resolver problemas de acesso SSO totalmente integrado:

- Verifique se a funcionalidade de SSO totalmente integrada está ativada no Azure AD Connect. Se não é possível ativar a funcionalidade (por exemplo, devido a uma porta bloqueada), certifique-se de que tem todas as [pré-requisitos](active-directory-aadconnect-sso-quick-start.md#step-1-check-prerequisites) no local.
- Verifique se ambas do Azure AD URLs (https://autologon.microsoftazuread-sso.com e https://aadg.windows.net.nsatc.net) são parte integrante das definições de zona de Intranet do utilizador.
- Certifique-se de que o dispositivo da empresa está associado ao domínio do AD.
- Certifique-se de que o utilizador tiver sessão iniciado para o dispositivo utilizando uma conta de domínio do AD.
- Certifique-se de que a conta de utilizador numa floresta de AD SSO totalmente integrada em que foi configurada.
- Certifique-se de que o dispositivo está ligado na rede empresarial.
- Certifique-se de que o tempo do dispositivo está sincronizado com a hora do Active Directory e os controladores de domínio e está dentro de cinco minutos.
- Lista de permissões Kerberos existentes são o dispositivo utilizando o **klist** comando numa linha de comandos. Verifique se os pedidos de suporte emitido para o `AZUREADSSOACCT` conta de computador estão presentes. Permissões de Kerberos dos utilizadores são normalmente válidos para 12 horas. Pode ter definições diferentes no seu Active Directory.
- Remover permissões Kerberos existentes são do dispositivo com o **klist remover** comando e tente novamente.
- Para determinar se existem problemas relacionados com o JavaScript, reveja os registos da consola do browser (em "ferramentas de programador").
- Reveja o [controlador de domínio regista](#domain-controller-logs) bem.

### <a name="domain-controller-logs"></a>Registos de controlador de domínio

Se o êxito de auditoria estiver ativado no seu controlador de domínio, em seguida, sempre que um utilizador inicia sessão utilizando o SSO totalmente integrado uma entrada de segurança é registada no registo de eventos. Pode encontrar estes eventos de segurança utilizando a seguinte consulta (procure o evento **4769** associada à conta de computador **AzureADSSOAcc$**):

```
    <QueryList>
      <Query Id="0" Path="Security">
    <Select Path="Security">*[EventData[Data[@Name='ServiceName'] and (Data='AZUREADSSOACC$')]]</Select>
      </Query>
    </QueryList>
```

## <a name="manual-reset-of-the-feature"></a>Reposição manual da funcionalidade

Se a resolução de problemas não o ajudaram, pode repor manualmente a funcionalidade no seu inquilino. Siga estes passos no servidor no local onde está a executar o Azure AD Connect:

### <a name="step-1-import-the-seamless-sso-powershell-module"></a>Passo 1: Importar o módulo do PowerShell de SSO totalmente integrada

1. Em primeiro lugar, transfira e instale o [assistente Microsoft Online Services início de sessão](http://go.microsoft.com/fwlink/?LinkID=286152).
2. Em seguida, transferir e instalar o [módulo do Azure Active Directory de 64 bits do Windows PowerShell](http://go.microsoft.com/fwlink/p/?linkid=236297).
3. Navegue para o `%programfiles%\Microsoft Azure Active Directory Connect` pasta.
4. Importe o módulo do PowerShell de SSO totalmente integrada utilizando este comando: `Import-Module .\AzureADSSO.psd1`.

### <a name="step-2-get-the-list-of-ad-forests-on-which-seamless-sso-has-been-enabled"></a>Passo 2: Obter a lista de florestas do AD no qual o SSO totalmente integrada foi ativada

1. Execute o PowerShell como administrador. No PowerShell, chame `New-AzureADSSOAuthenticationContext`. Quando lhe for pedido, introduza as credenciais de Administrador Global do inquilino.
2. Chamar `Get-AzureADSSOStatus`. Este comando apresenta a lista de florestas do AD (ver a lista de "Domínios") em que esta funcionalidade foi ativada.

### <a name="step-3-disable-seamless-sso-for-each-ad-forest-that-it-was-set-it-up-on"></a>Passo 3: Desativar SSO totalmente integrada para cada floresta do AD que este foi configurá-lo no

1. Chamar `$creds = Get-Credential`. Quando lhe for pedido, introduza as credenciais de administrador de domínio de floresta do AD pretendida.
2. Chamar `Disable-AzureADSSOForest -OnPremCredentials $creds`. Este comando remove o `AZUREADSSOACCT` conta de computador do controlador de domínio no local para esta floresta específica do AD.
3. Repita os passos anteriores para cada floresta do AD que configurou a funcionalidade no.

### <a name="step-4-enable-seamless-sso-for-each-ad-forest"></a>Passo 4: Ativar a SSO totalmente integrada para cada floresta do AD

1. Chamar `Enable-AzureADSSOForest`. Quando lhe for pedido, introduza as credenciais de administrador de domínio de floresta do AD pretendida.
2. Repita os passos anteriores para cada floresta do AD que pretende configurar a funcionalidade no.

### <a name="step-5-enable-the-feature-on-your-tenant"></a>Passo 5. Ativar a funcionalidade no seu inquilino

Chamar `Enable-AzureADSSO` e escreva "true", o `Enable: ` pedido ativar a funcionalidade no seu inquilino.

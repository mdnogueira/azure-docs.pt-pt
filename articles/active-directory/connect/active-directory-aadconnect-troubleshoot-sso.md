---
title: 'Azure Active Directory Connect: Resolver totalmente integrada Single Sign-On | Microsoft Docs'
description: "Este tópico descreve como resolver problemas do Azure Active Directory totalmente integrada Single Sign-On"
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
ms.openlocfilehash: b4efb457a58d8b54c9ebb126a8d84fdef01b3847
ms.sourcegitcommit: f847fcbf7f89405c1e2d327702cbd3f2399c4bc2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/28/2017
---
# <a name="troubleshoot-azure-active-directory-seamless-single-sign-on"></a>Resolver problemas do Azure Active Directory totalmente integrada Single Sign-On

Este artigo ajuda-o a encontrar informações sobre problemas comuns sobre Azure Active Directory (Azure AD) totalmente integrada Single Sign-On (SSO totalmente integrado) de resolução de problemas.

## <a name="known-problems"></a>Problemas conhecidos

- Em alguns casos, a ativação SSO totalmente integrada pode demorar até 30 minutos.
- Suporte de browser Edge não está disponível.
- A partir de clientes do Office, especialmente cenários de computador partilhado, faz com que pede extra início de sessão de utilizadores. Os utilizadores tem de introduzir os nomes de utilizador com frequência, mas não as palavras-passe.
- Se tiver êxito SSO totalmente integrada, o utilizador não tem a oportunidade de selecionar **manter a minha sessão iniciada**. Devido a este comportamento, cenários de mapeamento do OneDrive e do SharePoint não funcionam.
- SSO totalmente integrada não funciona no modo privado de navegação no Firefox.
- SSO totalmente integrada não funciona no Internet Explorer, quando o modo protegido avançado é ativado.
- SSO totalmente integrada não funciona em browsers para dispositivos móveis em dispositivos iOS e Android.
- Se estiver a sincronizar 30 ou mais florestas do Active Directory, não é possível ativar a SSO totalmente integrada através do Azure AD Connect. Como solução, pode [ativar manualmente](#manual-reset-of-azure-ad-seamless-sso) a funcionalidade no seu inquilino.
- A adição de URLs do serviço do Azure AD (https://autologon.microsoftazuread-sso.com, https://aadg.windows.net.nsatc.net) para a zona de sites fidedignos, em vez de à zona Local intranet *bloqueia os utilizadores de início de sessão*.

## <a name="check-the-status-of-the-feature"></a>Verificar o estado da funcionalidade

Certifique-se de que a funcionalidade de SSO totalmente integrado ainda é **ativado** no seu inquilino. Pode verificar o estado, acedendo ao **do Azure AD Connect** painel o [Centro de administração do Azure Active Directory](https://aad.portal.azure.com/).

![Centro de administração do Active Directory do Azure: painel do Azure AD Connect](./media/active-directory-aadconnect-sso/sso10.png)

## <a name="sign-in-failure-reasons-in-the-azure-active-directory-admin-center-needs-a-premium-license"></a>Motivos da falha de início de sessão no Centro de administração do Azure Active Directory (necessita de uma licença de Premium)

Se o seu inquilino tem uma licença do Azure AD Premium associada à mesma, pode também ver o [relatório de atividade de início de sessão](../active-directory-reporting-activity-sign-ins.md) no [Centro de administração do Azure Active Directory](https://aad.portal.azure.com/).

![Centro de administração do Active Directory do Azure: relatório de inícios de sessão](./media/active-directory-aadconnect-sso/sso9.png)

Navegue até à **do Azure Active Directory** > **inícios de sessão** no [Centro de administração do Azure Active Directory](https://aad.portal.azure.com/)e, em seguida, selecione a atividade de início de sessão de um utilizador específico. Procure o **código de erro de início de sessão em** campo. Mapear o valor desse campo um motivo da falha e a resolução utilizando a tabela seguinte:

|Código de erro de início de sessão|Motivo da falha de início de sessão|Resolução
| --- | --- | ---
| 81001 | A permissão do Kerberos do utilizador é demasiado grande. | Reduza as associações de grupo do utilizador e tente novamente.
| 81002 | Não é possível validar a permissão de Kerberos do utilizador. | Consulte o [resolução de problemas de lista de verificação](#troubleshooting-checklist).
| 81003 | Não é possível validar a permissão de Kerberos do utilizador. | Consulte o [resolução de problemas de lista de verificação](#troubleshooting-checklist).
| 81004 | Falha ao tentar a autenticação do Kerberos. | Consulte o [resolução de problemas de lista de verificação](#troubleshooting-checklist).
| 81008 | Não é possível validar a permissão de Kerberos do utilizador. | Consulte o [resolução de problemas de lista de verificação](#troubleshooting-checklist).
| 81009 | Não é possível validar a permissão de Kerberos do utilizador. | Consulte o [resolução de problemas de lista de verificação](#troubleshooting-checklist).
| 81010 | O SSO integrado falhou porque a permissão do Kerberos do utilizador expirou ou é inválida. | O utilizador tem de iniciar sessão a partir de um dispositivo associado ao domínio dentro da sua rede empresarial.
| 81011 | Não é possível encontrar o objeto de utilizador com base nas informações na permissão de Kerberos do utilizador. | Utilize o Azure AD Connect para sincronizar as informações do utilizador com o Azure AD.
| 81012 | O utilizador tentar iniciar sessão Azure AD é diferente do utilizador que tenha sessão iniciado no dispositivo. | O utilizador tem de iniciar sessão a partir de um dispositivo diferente.
| 81013 | Não é possível encontrar o objeto de utilizador com base nas informações na permissão de Kerberos do utilizador. |Utilize o Azure AD Connect para sincronizar as informações do utilizador com o Azure AD. 

## <a name="troubleshooting-checklist"></a>Lista de verificação de resolução de problemas

Utilize a lista de verificação seguinte para resolver problemas de SSO totalmente integrado:

- Certifique-se de que a funcionalidade de SSO totalmente integrada está ativada no Azure AD Connect. Se não é possível ativar a funcionalidade (por exemplo, devido a uma porta bloqueada), certifique-se de que tem todas as [pré-requisitos](active-directory-aadconnect-sso-quick-start.md#step-1-check-the-prerequisites) no local.
- Certifique-se de que os dois URLs do Azure AD (https://autologon.microsoftazuread-sso.com e https://aadg.windows.net.nsatc.net) são parte integrante das definições de zona de Intranet do utilizador.
- Certifique-se de que o dispositivo da empresa está associado ao domínio do Active Directory.
- Certifique-se de que o utilizador iniciar sessão num dispositivo através de uma conta de domínio do Active Directory.
- Certifique-se de que a conta de utilizador numa floresta do Active Directory onde SSO totalmente integrado tiver sido configurada.
- Certifique-se de que o dispositivo está ligado à rede empresarial.
- Certifique-se de que a hora do dispositivo está sincronizada com a hora no Active Directory e os controladores de domínio e que estão dentro de cinco minutos.
- Lista de permissões de Kerberos existentes no dispositivo utilizando o `klist` comando numa linha de comandos. Certifique-se de que as permissões emitidas para o `AZUREADSSOACCT` conta de computador estão presentes. Permissões de Kerberos dos utilizadores são normalmente válidos para 12 horas. Poderá ter definições diferentes no Active Directory.
- Remover permissões Kerberos existentes são do dispositivo utilizando o `klist purge` comando e tente novamente.
- Para determinar se existem problemas relacionados com JavaScript, reveja os registos da consola do browser (em **ferramentas de programador**).
- Reveja o [registos de controlador de domínio](#domain-controller-logs).

### <a name="domain-controller-logs"></a>Registos de controlador de domínio

Se ativar a auditoria de êxito no seu controlador de domínio, em seguida, sempre que um utilizador inicia sessão através de SSO totalmente integrada, uma entrada de segurança é registada no registo de eventos. Pode encontrar estes eventos de segurança utilizando a seguinte consulta. (Procure o evento **4769** associada à conta de computador **AzureADSSOAcc$**.)

```
    <QueryList>
      <Query Id="0" Path="Security">
    <Select Path="Security">*[EventData[Data[@Name='ServiceName'] and (Data='AZUREADSSOACC$')]]</Select>
      </Query>
    </QueryList>
```

## <a name="manual-reset-of-the-feature"></a>Reposição manual da funcionalidade

Se a resolução de problemas não o ajudaram, pode repor manualmente a funcionalidade no seu inquilino. Siga estes passos no servidor no local onde está a executar o Azure AD Connect.

### <a name="step-1-import-the-seamless-sso-powershell-module"></a>Passo 1: Importar o módulo do PowerShell de SSO totalmente integrada

1. Transfira e instale o [assistente Microsoft Online Services início de sessão](http://go.microsoft.com/fwlink/?LinkID=286152).
2. Transfira e instale o [módulo do Azure Active Directory de 64 bits do Windows PowerShell](http://go.microsoft.com/fwlink/p/?linkid=236297).
3. Navegue para o `%programfiles%\Microsoft Azure Active Directory Connect` pasta.
4. Importe o módulo do PowerShell de SSO totalmente integrada utilizando este comando: `Import-Module .\AzureADSSO.psd1`.

### <a name="step-2-get-the-list-of-active-directory-forests-on-which-seamless-sso-has-been-enabled"></a>Passo 2: Obter a lista de florestas do Active Directory no qual o SSO totalmente integrada foi ativada

1. Execute o PowerShell como administrador. No PowerShell, chame `New-AzureADSSOAuthenticationContext`. Quando lhe for pedido, introduza as credenciais de administrador global do inquilino.
2. Chamar `Get-AzureADSSOStatus`. Este comando fornece a lista de florestas do Active Directory (ver a lista de "Domínios") em que esta funcionalidade foi ativada.

### <a name="step-3-disable-seamless-sso-for-each-active-directory-forest-where-youve-set-up-the-feature"></a>Passo 3: Desativar SSO totalmente integrada para cada floresta do Active Directory onde configurou a funcionalidade

1. Chamar `$creds = Get-Credential`. Quando lhe for pedido, introduza as credenciais de administrador de domínio da floresta do Active Directory pretendido.
2. Chamar `Disable-AzureADSSOForest -OnPremCredentials $creds`. Este comando remove o `AZUREADSSOACCT` conta de computador do controlador de domínio no local para esta floresta do Active Directory específico.
3. Repita os passos anteriores para cada floresta do Active Directory onde configurou a funcionalidade.

### <a name="step-4-enable-seamless-sso-for-each-active-directory-forest"></a>Passo 4: Ativar a SSO totalmente integrada para cada floresta do Active Directory

1. Chamar `Enable-AzureADSSOForest`. Quando lhe for pedido, introduza as credenciais de administrador de domínio da floresta do Active Directory pretendido.
2. Repita o passo anterior para cada floresta do Active Directory onde pretende configurar a funcionalidade.

### <a name="step-5-enable-the-feature-on-your-tenant"></a>Passo 5. Ativar a funcionalidade no seu inquilino

Para ativar a funcionalidade no seu inquilino, chamar `Enable-AzureADSSO` e introduza **verdadeiro** no `Enable:` linha.

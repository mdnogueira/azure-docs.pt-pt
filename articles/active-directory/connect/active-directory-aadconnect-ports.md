---
title: "Identidade híbrida necessário portas e protocolos - Azure | Microsoft Docs"
description: "Esta página é uma página de referência técnica para portas que são necessárias para ser aberto para o Azure AD Connect"
services: active-directory
documentationcenter: 
author: billmath
manager: femila
editor: curtand
ms.assetid: de97b225-ae06-4afc-b2ef-a72a3643255b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/02/2017
ms.author: billmath
ms.openlocfilehash: da79c2451463794f7a205182830b6be53134507f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="hybrid-identity-required-ports-and-protocols"></a>Portas e Protocolos Necessários para a Identidade Híbrida
O documento seguinte é uma referência técnica sobre as portas e protocolos necessários para implementar uma solução de identidade híbrida. Utilize a seguinte ilustração e consultar a tabela correspondente.

![O que é o Azure AD Connect](./media/active-directory-aadconnect-ports/required3.png)

## <a name="table-1---azure-ad-connect-and-on-premises-ad"></a>Tabela 1 - do Azure AD Connect e AD no local
Esta tabela descreve as portas e protocolos que são necessários para a comunicação entre o servidor do Azure AD Connect e AD no local.

| Protocolo | Portas | Descrição |
| --- | --- | --- |
| DNS |53 (TCP/UDP) |Pesquisas de DNS na floresta de destino. |
| Kerberos |88 (TCP/UDP) |Autenticação Kerberos floresta do AD. |
| MS RPC |135 (TCP/UDP) |Utilizado durante a configuração inicial do Assistente do Azure AD Connect quando está vinculada a floresta do AD e também durante a sincronização de palavra-passe. |
| LDAP |389 (TCP/UDP) |Utilizado para a importação de dados do AD. Dados são encriptados com o início de sessão do Kerberos & selar. |
| RPC | 445 (TCP/UDP) |Utilizada pelo SSO totalmente integrada para criar uma conta de computador na floresta do AD. |
| LDAP/SSL |636 (TCP/UDP) |Utilizado para a importação de dados do AD. A transferência de dados é assinada e encriptada. Só é utilizado se estiver a utilizar SSL. |
| RPC |49152 e 65535 (aleatório elevada RPC Port)(TCP/UDP) |Utilizado durante a configuração inicial do Azure AD Connect, quando está vinculada às florestas do AD e durante a sincronização de palavra-passe. Consulte [KB929851](https://support.microsoft.com/kb/929851), [KB832017](https://support.microsoft.com/kb/832017), e [KB224196](https://support.microsoft.com/kb/224196) para obter mais informações. |

## <a name="table-2---azure-ad-connect-and-azure-ad"></a>Tabela 2 - do Azure AD Connect e o Azure AD
Esta tabela descreve as portas e protocolos que são necessários para a comunicação entre o servidor do Azure AD Connect e o Azure AD.

| Protocolo | Portas | Descrição |
| --- | --- | --- |
| HTTP |80 (TCP/UDP) |Utilizado para transferir o CRL (certificado de listas de revogação) para verificar os certificados SSL. |
| HTTPS |443(TCP/UDP) |Utilizado para sincronizar com o Azure AD. |

Para obter uma lista de URLs e IP endereços é necessário abrir na firewall, consulte [intervalos de endereços IP e URLs do Office 365](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2).

## <a name="table-3---azure-ad-connect-and-ad-fs-federation-serverswap"></a>Tabela 3 – do Azure AD Connect e servidores/WAP do AD FS Federação
Esta tabela descreve as portas e protocolos que são necessários para a comunicação entre os servidores de Federação/WAP do AD FS e o servidor do Azure AD Connect.  

| Protocolo | Portas | Descrição |
| --- | --- | --- |
| HTTP |80 (TCP/UDP) |Utilizado para transferir o CRL (certificado de listas de revogação) para verificar os certificados SSL. |
| HTTPS |443(TCP/UDP) |Utilizado para sincronizar com o Azure AD. |
| WinRM |5985 |O serviço de escuta do WinRM |

## <a name="table-4---wap-and-federation-servers"></a>Tabela 4 - WAP e servidores de Federação
Esta tabela descreve as portas e protocolos que são necessários para a comunicação entre os servidores de Federação e os servidores do WAP.

| Protocolo | Portas | Descrição |
| --- | --- | --- |
| HTTPS |443(TCP/UDP) |Utilizado para autenticação. |

## <a name="table-5---wap-and-users"></a>Tabela 5 - WAP e utilizadores
Esta tabela descreve as portas e protocolos que são necessários para a comunicação entre utilizadores e dos servidores WAP.

| Protocolo | Portas | Descrição |
| --- | --- | --- |
| HTTPS |443(TCP/UDP) |Utilizado para autenticação de dispositivo. |
| TCP |49443 (TCP) |Utilizado para autenticação de certificados. |

## <a name="table-6a--6b---pass-through-authentication-with-single-sign-on-sso-and-password-hash-sync-with-single-sign-on-sso"></a>Tabela 6a & 6b - autenticação pass-through com início de sessão único (SSO) e a sincronização de Hash de palavra-passe com início de sessão único (SSO)
As tabelas seguintes descreve as portas e protocolos que são necessários para a comunicação entre o Azure AD Connect e o Azure AD.

### <a name="table-6a---pass-through-authentication-with-sso"></a>Tabela 6a - autenticação pass-through com o SSO
|Protocolo|Número de porta|Descrição
| --- | --- | ---
|HTTP|80|Ative o tráfego de saída HTTP para a validação de segurança, tal como o SSL. Também é necessário para a capacidade de atualização automática do conector funcionar corretamente.
|HTTPS|443| Ative o tráfego de saída HTTPS para operações como ativar e a desativação da funcionalidade, registar conectores, transferir atualizações do conector e processar todos os pedidos de início de sessão do utilizador.

Além disso, o Azure AD Connect tem de conseguir efetuar ligações diretas de IP para o [intervalos de IP do Centro de dados do Azure](https://www.microsoft.com/en-us/download/details.aspx?id=41653).

### <a name="table-6b---password-hash-sync-with-sso"></a>Tabela 6b - sincronização de Hash de palavra-passe com o SSO

|Protocolo|Número de porta|Descrição
| --- | --- | ---
|HTTPS|443| Ative o registo SSO (apenas necessário para o processo de registo SSO).

Além disso, o Azure AD Connect tem de conseguir efetuar ligações diretas de IP para o [intervalos de IP do Centro de dados do Azure](https://www.microsoft.com/en-us/download/details.aspx?id=41653). Novamente, isto só é necessário para o processo de registo do SSO.

## <a name="table-7a--7b---azure-ad-connect-health-agent-for-ad-fssync-and-azure-ad"></a>Tabela 7a & 7b - agente Azure AD Connect Health para (AD FS/Sync) e o Azure AD
As tabelas seguintes descrevem os pontos finais, portas e protocolos que são necessários para a comunicação entre agentes do Azure AD Connect Health e o Azure AD

### <a name="table-7a---ports-and-protocols-for-azure-ad-connect-health-agent-for-ad-fssync-and-azure-ad"></a>Tabela 7a - portas e protocolos para o agente do Azure AD Connect Health para (AD FS/Sync) e o Azure AD
Esta tabela descreve as seguintes portas de saída e protocolos que são necessários para a comunicação entre os agentes do Azure AD Connect Health e o Azure AD.  

| Protocolo | Portas | Descrição |
| --- | --- | --- |
| HTTPS |443(TCP/UDP) |Saída |
| Service Bus do Azure |5671 (TCP/UDP) |Saída |

### <a name="7b---endpoints-for-azure-ad-connect-health-agent-for-ad-fssync-and-azure-ad"></a>7B - pontos finais para o agente do Azure AD Connect Health para (AD FS/Sync) e o Azure AD
Para obter uma lista de pontos finais, consulte [a secção de requisitos para o agente do Azure AD Connect Health](../connect-health/active-directory-aadconnect-health-agent-install.md#requirements).


---
title: "Do Azure Active Directory B2C: Exemplos de API relatórios de utilização e as definições | Microsoft Docs"
description: "Guia e exemplos sobre a obtenção de relatórios no inquilino do Azure AD B2C utilizadores, as autenticações e autenticações multifator"
services: active-directory-b2c
documentationcenter: dev-center-name
author: rojasja
manager: mbaldwin
ms.service: active-directory-b2c
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/04/2017
ms.author: joroja
ms.openlocfilehash: 52180b760046d273c5a75720df0a73532d0343ad
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="accessing-usage-reports-in-azure-ad-b2c-via-the-reporting-api"></a>Ao aceder aos relatórios de utilização no Azure AD B2C através da API de relatórios

O Azure Active Directory B2C (Azure AD B2C) fornece autenticação com base na sessão do utilizador e do Azure multi-factor Authentication. A autenticação é fornecida para os utilizadores finais da sua família de aplicações através de fornecedores de identidade. Quando souber o número de utilizadores registados no inquilino, os fornecedores que são utilizadas para registar e o número de autenticações por tipo, pode responder a perguntas como:
* Quantos utilizadores a partir de cada tipo de fornecedor de identidade (por exemplo, uma conta Microsoft ou LinkedIn) registou-se nos últimos 10 dias?
* Quantos autenticações utilizando o multi-factor Authentication foram concluídos com êxito no último mês?
* Autenticações de início de sessão baseada em quantos foram concluídas deste mês? Por dia? Por aplicação?
* Como pode a calcular o custo mensal esperado da minha atividade de inquilino do Azure AD B2C?

Este artigo foca-se em relatórios associados para a atividade de faturação baseada no número de utilizadores, autenticações de início de sessão baseada em sujeito a faturação e autenticações multifator.


## <a name="prerequisites"></a>Pré-requisitos
Antes de começar, tem de concluir os passos [pré-requisitos para o Azure AD reporting APIs de acesso](https://azure.microsoft.com/documentation/articles/active-directory-reporting-api-getting-started/). Criar uma aplicação, obtenha um segredo para a mesma e conceder acesso direitos de relatórios do seu inquilino do Azure AD B2C. *Scripts de bash* e *script de Python* exemplos também são fornecidos aqui. 

## <a name="powershell-script"></a>Script do PowerShell
Este script demonstra a criação de relatórios de utilização de quatro utilizando o `TimeStamp` parâmetro e o `ApplicationId` filtro.

```powershell
# This script will require the Web Application and permissions setup in Azure Active Directory

# Constants
$ClientID      = "your-client-application-id-here"  
$ClientSecret  = "your-client-application-secret-here"
$loginURL      = "https://login.microsoftonline.com"
$tenantdomain  = "your-b2c-tenant-domain.onmicrosoft.com"  
# Get an Oauth 2 access token based on client id, secret and tenant domain
$body          = @{grant_type="client_credentials";resource=$resource;client_id=$ClientID;client_secret=$ClientSecret}
$oauth         = Invoke-RestMethod -Method Post -Uri $loginURL/$tenantdomain/oauth2/token?api-version=1.0 -Body $body
if ($oauth.access_token -ne $null) {
    $headerParams  = @{'Authorization'="$($oauth.token_type) $($oauth.access_token)"}

    Write-host Data from the tenantUserCount report
    Write-host ====================================================
     # Returns a JSON document for the report
    $myReport = (Invoke-WebRequest -Headers $headerParams -Uri "https://graph.windows.net/$tenantdomain/reports/tenantUserCount?api-version=beta")
    Write-host $myReport.Content

    Write-host Data from the tenantUserCount report with datetime filter
    Write-host ====================================================
    $myReport = (Invoke-WebRequest -Headers $headerParams -Uri "https://graph.windows.net/$tenantdomain/reports/tenantUserCount?%24filter=TimeStamp+gt+2016-10-15&api-version=beta")
    Write-host $myReport.Content

    Write-host Data from the b2cAuthenticationCountSummary report
    Write-host ====================================================
    $myReport = (Invoke-WebRequest -Headers $headerParams -Uri "https://graph.windows.net/$tenantdomain/reports/b2cAuthenticationCountSummary?api-version=beta")
    Write-host $myReport.Content

    Write-host Data from the b2cAuthenticationCount report with datetime filter
    Write-host ====================================================
    $myReport = (Invoke-WebRequest -Headers $headerParams -Uri "https://graph.windows.net/$tenantdomain/reports/b2cAuthenticationCount?%24filter=TimeStamp+gt+2016-09-20+and+TimeStamp+lt+2016-10-03&api-version=beta")
    Write-host $myReport.Content

    Write-host Data from the b2cAuthenticationCount report with ApplicationId filter
    Write-host ====================================================
    # Returns a JSON document for the " " report
        $myReport = (Invoke-WebRequest -Headers $headerParams -Uri "https://graph.windows.net/$tenantdomain/reports/b2cAuthenticationCount?%24filter=ApplicationId+eq+ada78934-a6da-4e69-b816-10de0d79db1d&api-version=beta")
    Write-host $myReport.Content

    Write-host Data from the b2cMfaRequestCountSummary
    Write-host ====================================================
    $myReport = (Invoke-WebRequest -Headers $headerParams -Uri "https://graph.windows.net/$tenantdomain/reports/b2cMfaRequestCountSummary?api-version=beta")
    Write-host $myReport.Content

    Write-host Data from the b2cMfaRequestCount report with datetime filter
    Write-host ====================================================
    $myReport = (Invoke-WebRequest -Headers $headerParams -Uri "https://graph.windows.net/$tenantdomain/reports/b2cMfaRequestCount?%24filter=TimeStamp+gt+2016-09-10+and+TimeStamp+lt+2016-10-04&api-version=beta")
    Write-host $myReport.Content

    Write-host Data from the b2cMfaRequestCount report with ApplicationId filter
    Write-host ====================================================
    $myReport = (Invoke-WebRequest -Headers $headerParams -Uri "https://graph.windows.net/$tenantdomain/reports/b2cMfaRequestCountSummary?%24filter=ApplicationId+eq+ada78934-a6da-4e69-b816-10de0d79db1d&api-version=beta")
     Write-host $myReport.Content

} else {
    Write-Host "ERROR: No Access Token"
    }
```


## <a name="usage-report-definitions"></a>Definições de relatórios de utilização
* **tenantUserCount**: O número de utilizadores no inquilino por tipo de fornecedor de identidade, por dia nos últimos 30 dias. (Opcionalmente, um `TimeStamp` filtro fornece contagens de utilizadores a partir de uma data especificada para a data atual). O relatório fornece:
  * **TotalUserCount**: O número de todos os objetos de utilizador.
  * **OtherUserCount**: O número de utilizadores do Azure Active Directory (não do Azure AD B2C utilizadores).
  * **LocalUserCount**: O número de contas de utilizador do Azure AD B2C criada com as credenciais local para o inquilino do Azure AD B2C.

* **AlternateIdUserCount**: O número de utilizadores do Azure AD B2C registado com fornecedores de identidade externas (por exemplo, Facebook, uma conta Microsoft ou outro inquilino do Azure Active Directory, também referido como um `OrgId`).

* **b2cAuthenticationCountSummary**: Resumo do número diário de autenticações facturável nos últimos 30 dias, por dia e tipo de fluxo de autenticação.

* **b2cAuthenticationCount**: O número de autenticações num período de tempo. A predefinição é 30 últimos dias.  (Opcional: O início e fim `TimeStamp` parâmetros definem um período de tempo específico.) O resultado inclui `StartTimeStamp` (data mais recente da atividade para este inquilino) e `EndTimeStamp` (atualização mais recente).

* **b2cMfaRequestCountSummary**: Resumo do número diário de autenticações multifator, por dia e tipo (voz ou de SMS).


## <a name="limitations"></a>Limitações
Dados de contagem de utilizador são atualizados a cada 24 para 48 horas. Autenticações são atualizadas várias vezes num dia. Ao utilizar o `ApplicationId` filtro, uma resposta vazia relatório pode ser devido a uma das seguintes condições:
  * O ID da aplicação não existe no inquilino. Certifique-se de que está correto.
  * O ID da aplicação existe, mas não foram encontrados dados no período de relatório. Reveja os parâmetros de data/hora.


## <a name="next-steps"></a>Passos seguintes
### <a name="monthly-bill-estimates-for-azure-ad"></a>Calcula o fatura mensal para o Azure AD
Quando combinada com [o mais recente do Azure AD B2C preço disponíveis](https://azure.microsoft.com/pricing/details/active-directory-b2c/), pode fazer a estimativa diárias, semana e mensal consumo do Azure.  Uma estimativa é especialmente útil ao planear as alterações no comportamento do inquilino que poderá afetar o custo global. Pode rever os custos reais na sua [subscrição do Azure associada](active-directory-b2c-how-to-enable-billing.md).

### <a name="options-for-other-output-formats"></a>Opções para outros formatos de saída
O código seguinte mostra exemplos de envio de saída para JSON, uma lista de valor de nome e XML:
```powershell
# to output to JSON use following line in the PowerShell sample
$myReport.Content | Out-File -FilePath b2cUserJourneySummaryEvents.json -Force

# to output the content to a name value list
($myReport.Content | ConvertFrom-Json).value | Out-File -FilePath name-your-file.txt -Force

# to output the content in XML use the following line
(($myReport.Content | ConvertFrom-Json).value | ConvertTo-Xml).InnerXml | Out-File -FilePath name-your-file.xml -Force
```

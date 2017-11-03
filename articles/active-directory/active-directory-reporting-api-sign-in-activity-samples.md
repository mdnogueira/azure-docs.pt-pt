---
title: "Exemplos de API do relatório de atividade de início de sessão do Active Directory do Azure | Microsoft Docs"
description: "Como começar com a API do Azure Active Directory Reporting Services"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: c41c1489-726b-4d3f-81d6-83beb932df9c
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/18/2017
ms.author: dhanyahk;markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: d8fec08deb44ec1cbb58f45ff5a01d348d99945a
ms.sourcegitcommit: 6acb46cfc07f8fade42aff1e3f1c578aa9150c73
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/18/2017
---
# <a name="azure-active-directory-sign-in-activity-report-api-samples"></a>Amostras de API do relatório de atividade de início de sessão do Azure Active Directory
Este tópico faz parte de uma coleção de tópicos sobre o Azure Active Directory API do relatório.  
Relatórios do Azure AD fornecem-lhe uma API que permite-lhe aceder a dados de atividade de início de sessão utilizando código ou ferramentas relacionadas.  
É o âmbito deste tópico para lhe fornecer o código de exemplo para o **início de sessão atividade API**.

Consulte:

* [Registos de auditoria](active-directory-reporting-azure-portal.md#activity-reports) para obter mais informações concetuais
* [Começar a utilizar a API do Azure Active Directory Reporting](active-directory-reporting-api-getting-started.md) para obter mais informações sobre a API de relatórios.


## <a name="prerequisites"></a>Pré-requisitos
Antes de poder utilizar os exemplos neste tópico, tem de concluir o [pré-requisitos para o Azure AD API do relatório de acesso](active-directory-reporting-api-prerequisites.md).  

## <a name="powershell-script"></a>Script do PowerShell
    # This script will require the Web Application and permissions setup in Azure Active Directory
    $ClientID       = "<clientId>"             # Should be a ~35 character string insert your info here
    $ClientSecret   = "<clientSecret>"         # Should be a ~44 character string insert your info here
    $loginURL       = "https://login.microsoftonline.com/"
    $tenantdomain   = "<tenantDomain>"
    $ daterange            # For example, contoso.onmicrosoft.com

    $7daysago = "{0:s}" -f (get-date).AddDays(-7) + "Z"
    # or, AddMinutes(-5)

    Write-Output $7daysago

    # Get an Oauth 2 access token based on client id, secret and tenant domain
    $body       = @{grant_type="client_credentials";resource=$resource;client_id=$ClientID;client_secret=$ClientSecret}

    $oauth      = Invoke-RestMethod -Method Post -Uri $loginURL/$tenantdomain/oauth2/token?api-version=1.0 -Body $body

    if ($oauth.access_token -ne $null) {
    $headerParams = @{'Authorization'="$($oauth.token_type) $($oauth.access_token)"}

    $url = "https://graph.windows.net/$tenantdomain/activities/signinEvents?api-version=beta&`$filter=signinDateTime ge $7daysago"

    $i=0

    Do{
        Write-Output "Fetching data using Uri: $url"
        $myReport = (Invoke-WebRequest -UseBasicParsing -Headers $headerParams -Uri $url)
        Write-Output "Save the output to a file SigninActivities$i.json"
        Write-Output "---------------------------------------------"
        $myReport.Content | Out-File -FilePath SigninActivities$i.json -Force
        $url = ($myReport.Content | ConvertFrom-Json).'@odata.nextLink'
        $i = $i+1
    } while($url -ne $null)

    } else {

        Write-Host "ERROR: No Access Token"
    }




## <a name="executing-the-script"></a>Executar o script
Depois de concluir a edição do script, executá-lo e certifique-se de que os dados esperados da auditoria os registos de relatório é devolvido.

O script devolve o resultado do relatório início de sessão no formato JSON. Também cria um `SigninActivities.json` ficheiro com o mesmo resultado. Pode experimentar ao modificar o script devolver dados a partir de outros relatórios ou comente os formatos de saída que não é necessário.

## <a name="next-steps"></a>Passos Seguintes
* Gostaria de personalizar os exemplos neste tópico? Veja o [do Azure Active Directory início de sessão atividade referência da API](active-directory-reporting-api-sign-in-activity-reference.md). 
* Se pretender ver uma descrição geral completa de utilizar o Azure Active Directory API do relatório, consulte [introdução ao Azure Active Directory API do relatório](active-directory-reporting-api-getting-started.md).
* Se quiser saber mais sobre os relatórios do Azure Active Directory, consulte o [do Azure Active Directory guia de relatórios](active-directory-reporting-guide.md).  


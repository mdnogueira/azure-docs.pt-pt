---
title: "Protegem as APIs utilizando o cliente de autenticação de certificado na API Management - API Management do Azure | Microsoft Docs"
description: "Saiba como proteger o acesso a APIs da utilização de certificados de cliente"
services: api-management
documentationcenter: 
author: vladvino
manager: erikre
editor: 
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/01/2017
ms.author: apimpm
ms.openlocfilehash: 10cc1daf89212635243cbfe8f7b598a567d0f7c3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-secure-apis-using-client-certificate-authentication-in-api-management"></a>Como protegem as APIs utilizando o cliente de autenticação de certificado na API Management

API Management fornece a capacidade de proteger o acesso a APIs (ou seja, o cliente para gestão de API) utilizando certificados de cliente. Atualmente, pode verificar o thumbprint de um certificado de cliente em relação a um valor pretendido. Também pode verificar o thumbprint de certificados existentes carregado para a API Management.  

Para obter informações sobre como proteger o acesso ao serviço de back-end de uma API utilizando certificados de cliente (ou seja, gestão de API para o back-end), consulte [como proteger serviços de back-end utilizando o cliente de autenticação de certificados](https://docs.microsoft.com/en-us/azure/api-management/api-management-howto-mutual-certificates)

## <a name="checking-the-expiration-date"></a>A verificar a data de expiração

Abaixo políticas podem ser configuradas para verificar se o certificado expirou:

```
<choose>
    <when condition="@(context.Request.Certificate == null || context.Request.Certificate.NotAfter < DateTime.Now)" >
        <return-response>
            <set-status code="403" reason="Invalid client certificate" />
        </return-response>
    </when>
</choose>
```

## <a name="checking-the-issuer-and-subject"></a>A verificar o emissor e o assunto

Abaixo políticas podem ser configuradas para verificar o emissor e o requerente do certificado de cliente:

```
<choose>
    <when condition="@(context.Request.Certificate == null || context.Request.Certificate.Issuer != "trusted-issuer" || context.Request.Certificate.SubjectName != "expected-subject-name")" >
        <return-response>
            <set-status code="403" reason="Invalid client certificate" />
        </return-response>
    </when>
</choose>
```

## <a name="checking-the-thumbprint"></a>A verificar a impressão digital

Abaixo políticas podem ser configuradas para verificar o thumbprint de um certificado de cliente:

```
<choose>
    <when condition="@(context.Request.Certificate == null || context.Request.Certificate.Thumbprint != "desired-thumbprint")" >
        <return-response>
            <set-status code="403" reason="Invalid client certificate" />
        </return-response>
    </when>
</choose>
```

## <a name="checking-a-thumbprint-against-certificates-uploaded-to-api-management"></a>A verificar um thumbprint contra certificados carregados para a API Management

O exemplo seguinte mostra como verificar o thumbprint de um certificado de cliente em relação a certificados carregados para a gestão de API: 

```
<choose>
    <when condition="@(context.Request.Certificate == null || !context.Deployment.Certificates.Any(c => c.Value.Thumbprint == context.Request.Certificate.Thumbprint))" >
        <return-response>
            <set-status code="403" reason="Invalid client certificate" />
        </return-response>
    </when>
</choose>

```

## <a name="next-step"></a>Passo seguinte

*  [Como proteger serviços de back-end utilizando o cliente de autenticação de certificados](https://docs.microsoft.com/en-us/azure/api-management/api-management-howto-mutual-certificates)
*  [Como carregar certificados](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates#a-namestep1-aupload-a-client-certificate)


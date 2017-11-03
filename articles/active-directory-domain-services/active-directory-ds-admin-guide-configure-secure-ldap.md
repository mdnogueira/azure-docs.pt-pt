---
title: "Configurar o LDAP seguro (LDAPS) nos serviços de domínio do Azure AD | Microsoft Docs"
description: "Configurar o LDAP seguro (LDAPS) para um domínio gerido dos serviços de domínio do Azure AD"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: stevenpo
editor: curtand
ms.assetid: c6da94b6-4328-4230-801a-4b646055d4d7
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/14/2017
ms.author: maheshu
ms.openlocfilehash: 93afa49166c5b31d23237c308b9d34f6d6f3507d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="configure-secure-ldap-ldaps-for-an-azure-ad-domain-services-managed-domain"></a>Configurar segura LDAP (LDAPS) para um domínio gerido dos serviços de domínio do Azure AD
Este artigo mostra como pode permitir proteger Lightweight Directory acesso protocolo (LDAPS) para o seu domínio gerido dos serviços de domínio do Azure AD. LDAP seguro também é conhecido como "acesso protocolo LDAP (Lightweight Directory) através de Secure Sockets Layer (SSL) / Transport Layer Security (TLS)'.

## <a name="before-you-begin"></a>Antes de começar
Para executar as tarefas apresentadas neste artigo, tem de:

1. Um **subscrição do Azure**.
2. Um **diretório do Azure AD** -está sincronizada com um diretório no local ou um diretório apenas na nuvem.
3. **Serviços de domínio do Azure AD** tem de estar ativada para o diretório do Azure AD. Se ainda não o feito, siga todas as tarefas descritas no [guia de introdução](active-directory-ds-getting-started.md).
4. A **certificado a ser utilizado para ativar o LDAP seguro**.

   * **Recomendado** -obter um certificado de uma autoridade de certificação pública fidedigna. Esta opção de configuração é mais segura.
   * Em alternativa, pode também optar por [criar um certificado autoassinado](#task-1---obtain-a-certificate-for-secure-ldap) conforme mostrado posteriormente neste artigo.

<br>

### <a name="requirements-for-the-secure-ldap-certificate"></a>Requisitos para o certificado LDAP seguro
Adquirir um certificado válido pelas seguintes diretrizes, antes de ativar LDAP seguro. Ocorrerem falhas se tentar ativar o LDAP seguro para o seu domínio gerido com um certificado inválido incorreto.

1. **Emissor fidedigno** -o certificado tem de ser emitido por uma autoridade fidedigna pelos computadores ao domínio gerido utilizando LDAP seguro. Esta autoridade pode ser uma autoridade de certificação pública fidedigna por estes computadores.
2. **Duração** -o certificado tem de ser válido para, pelo menos, próximos 3 a 6 meses. Proteger o acesso LDAP para o seu domínio gerido é interrompido quando o certificado expirar.
3. **Nome do requerente** -o nome do requerente do certificado tem de ser um caráter universal para o seu domínio gerido. Por exemplo, se o seu domínio com o nome 'contoso100.com', nome do requerente do certificado tem de ser ' *. contoso100.com'. Defina o nome DNS (nome alternativo do requerente) para este nome de caráter universal.
4. **Utilização da chave** -o certificado tem de ser configurado para o seguinte utiliza - as assinaturas Digital e cifragem de chaves.
5. **Objetivo do certificado** -o certificado tem de ser válido para autenticação de servidor SSL.

> [!NOTE]
> **Autoridades de certificação empresarial:** dos serviços de domínio do Azure AD não suporta a utilização segurados LDAP certificados emitidos pela autoridade de certificação empresarial da sua organização. Esta restrição é porque o serviço não fidedigna a AC como uma autoridade de certificação de raiz empresarial. 
>
>

<br>

## <a name="task-1---obtain-a-certificate-for-secure-ldap"></a>Tarefa 1 - obter um certificado para o LDAP seguro
A primeira tarefa envolve a obtenção de um certificado utilizado para proteger o acesso LDAP para o domínio gerido. Tem duas opções:

* Obtenha um certificado de uma autoridade de certificação. A autoridade pode ser uma autoridade de certificação pública.
* Crie um certificado autoassinado.

### <a name="option-a-recommended---obtain-a-secure-ldap-certificate-from-a-certification-authority"></a>Opção (recomendado) - obter um certificado LDAP seguro de uma autoridade de certificação
Se a sua organização obtém os certificados de autoridades de certificação pública, terá de obter o certificado LDAP seguro de nessa autoridade de certificação pública.

Quando solicitar um certificado, certifique-se de que satisfaçam todos os requisitos descritos na [requisitos para o certificado LDAP seguro](#requirements-for-the-secure-ldap-certificate).

> [!NOTE]
> Computadores de cliente que necessitam para ligar ao domínio gerido utilizando LDAP seguro têm de confiar o emissor do certificado LDAP seguro.
>
>

### <a name="option-b---create-a-self-signed-certificate-for-secure-ldap"></a>Opção B - criar um certificado autoassinado para o LDAP seguro
Se não pretende utilizar um certificado de uma autoridade de certificação pública, pode optar por criar um certificado autoassinado para o LDAP seguro.

**Criar um certificado autoassinado com o PowerShell**

No seu computador Windows, abra uma nova janela do PowerShell como **administrador** e escreva os comandos seguintes, para criar um novo certificado autoassinado.

    $lifetime=Get-Date

    New-SelfSignedCertificate -Subject *.contoso100.com -NotAfter $lifetime.AddDays(365) -KeyUsage DigitalSignature, KeyEncipherment -Type SSLServerAuthentication -DnsName *.contoso100.com

No exemplo anterior, substitua '*. contoso100.com' com o nome de domínio DNS do seu domínio gerido. Por exemplo, se tiver criado um domínio gerido chamado 'contoso100.onmicrosoft.com', substitua '*. contoso100.com' no script acima com ' *. contoso100.onmicrosoft.com').

![Selecionar o Azure AD Directory](./media/active-directory-domain-services-admin-guide/secure-ldap-powershell-create-self-signed-cert.png)

O certificado autoassinado criado recentemente é colocado no arquivo de certificados do computador local.


## <a name="next-step"></a>Passo seguinte
[Tarefa 2 - exportar o certificado LDAP seguro para um. Ficheiro PFX](active-directory-ds-admin-guide-configure-secure-ldap-export-pfx.md)

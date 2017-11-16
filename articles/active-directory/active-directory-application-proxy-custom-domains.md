---
title: "Domínios personalizados no Proxy de aplicações do Azure AD | Microsoft Docs"
description: "Gerir domínios personalizados no Proxy de aplicações do Azure AD para que o URL para a aplicação é o mesmo, independentemente de onde os utilizadores aceder ao mesmo."
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: 2fe9f895-f641-4362-8b27-7a5d08f8600f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/11/2017
ms.author: billmath
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: f254f4959c66aeb3eec522f31e0d8a6780358188
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2017
---
# <a name="working-with-custom-domains-in-azure-ad-application-proxy"></a>Trabalhar com domínios personalizados no Proxy de aplicações do Azure AD

Quando publica uma aplicação através do Proxy de aplicações do Azure Active Directory, criar um URL externo para os seus utilizadores Ir para quando estiver a trabalhar remotamente. Este URL obtém o domínio predefinido *yourtenant.msappproxy.net*. Por exemplo, se publicou uma aplicação com o nome de despesas e o inquilino é denominado Contoso, em seguida, o URL externo seria https://expenses-contoso.msappproxy.net. Se pretender utilizar o seu próprio nome de domínio, configure um domínio personalizado para a sua aplicação. 

Recomendamos que configure domínios personalizados para as suas aplicações sempre que possível. Algumas das vantagens de domínios personalizados incluem:

- Os utilizadores acedem à aplicação com o mesmo URL, se estiverem a funcionar dentro ou fora da rede.
- Se todas as aplicações têm os mesmos URLs internos e externos, em seguida, as ligações numa aplicação que apontam para outro continuam a trabalhar mesmo fora da rede empresarial. 
- Pode controla a sua imagem corporativa e criar os URLs que pretende. 


## <a name="configure-a-custom-domain"></a>Configurar um domínio personalizado

### <a name="prerequisites"></a>Pré-requisitos

Antes de configurar um domínio personalizado, certifique-se de que tem os seguintes requisitos preparados: 
- A [adicionado ao Azure Active Directory do domínio verificado](active-directory-domains-add-azure-portal.md).
- Um certificado personalizado para o domínio, sob a forma de um ficheiro PFX. 
- Uma aplicação no local [publicadas através do Proxy de aplicações](application-proxy-publish-azure-portal.md).

### <a name="configure-your-custom-domain"></a>Configurar o domínio personalizado

Quando tiver esses três requisitos prontos, siga estes passos para configurar o domínio personalizado:

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
2. Navegue para **do Azure Active Directory** > **aplicações empresariais** > **todas as aplicações** e escolha a aplicação que pretende gerir.
3. Selecione **Proxy de aplicações**. 
4. No campo URL externo, utilize a lista pendente para selecionar o domínio personalizado. Se não vir o seu domínio na lista, em seguida, se ainda não foi verificado ainda. 
5. Selecione **guardar**
5. O **certificado** fique ativa o campo que foi desativado. Selecione este campo. 

   ![Clique aqui para carregar um certificado](./media/active-directory-application-proxy-custom-domains/certificate.png)

   Se já carregado um certificado para este domínio, o certificado do campo apresenta as informações do certificado. 

6. Carregue o certificado do PFX e introduza a palavra-passe do certificado. 
7. Selecione **guardar** para guardar as alterações. 
8. Adicionar um [registo DNS](../dns/dns-operations-recordsets-portal.md) que redireciona o novo URL externo para o domínio msappproxy.net. 

>[!TIP] 
>Só tem de carregar um certificado por domínio personalizado. Depois de carregar um certificado, pode escolher o domínio personalizado ao publicar uma nova aplicação e tem de efetuar qualquer configuração adicional, exceto o registo DNS. 

## <a name="manage-certificates"></a>Gerir certificados

### <a name="certificate-format"></a>Formato de certificado
Não há nenhuma restrição para os métodos de assinatura de certificado. Todos os outros tipos de certificado comuns, nome de alternativo do requerente (SAN) e criptografia de curva elíptica (ECC) são suportados. 

Pode utilizar um certificado de caráter universal, desde que o URL externo pretendido corresponde a caráter universal. 

### <a name="changing-the-domain"></a>Alterar o domínio
Todos os domínios verificados aparecem na lista pendente URL externo para a sua aplicação. Para alterar o domínio, basta Atualize esse campo para a aplicação. Se o domínio que pretende não se encontra na lista, [adicioná-lo como um domínio verificado](active-directory-domains-add-azure-portal.md). Se selecionar um domínio que têm um certificado associado ainda não, siga os passos 5 a 7 para adicionar o certificado. Em seguida, certifique-se de que atualiza o registo DNS para o novo URL externo de redirecionamento. 

### <a name="certificate-management"></a>Gestão de certificados
Pode utilizar o mesmo certificado para várias aplicações, a menos que as aplicações partilham anfitrião externo. 

Receberá um aviso quando um certificado expira, informá-lo carregue outro certificado através do portal. Se o certificado foi revogado, os utilizadores poderão ver um aviso de segurança quando o acesso à aplicação. Não efetuar as verificações de revogação de certificados.  Para atualizar o certificado para uma determinada aplicação, navegue para a aplicação e siga os passos 5 a 7 para configurar domínios personalizados nos aplicações publicadas para carregar um novo certificado. Se o certificado antigo não está a ser utilizado por outras aplicações, é eliminado automaticamente. 

Atualmente os toda a gestão de certificados é através de páginas de aplicações individuais, pelo que necessita gerir os certificados no contexto das aplicações relevantes. 

## <a name="next-steps"></a>Passos seguintes
* [Ativar o início de sessão único](active-directory-application-proxy-sso-using-kcd.md) às suas aplicações publicadas com a autenticação do Azure AD.
* [Ativar o acesso condicional](application-proxy-enable-remote-access-sharepoint.md) às suas aplicações publicadas.
* [Adicionar o seu nome de domínio personalizado ao Azure AD](active-directory-domains-add-azure-portal.md)



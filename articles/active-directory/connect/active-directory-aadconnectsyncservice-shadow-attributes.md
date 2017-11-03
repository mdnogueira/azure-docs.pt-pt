---
title: "Atributos do sombra de volumes de serviço de sincronização do Azure AD Connect | Microsoft Docs"
description: "Descreve como funcionam os atributos de sombra de volumes no serviço de sincronização do Azure AD Connect."
services: active-directory
documentationcenter: 
author: andkjell
manager: femila
editor: 
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.author: billmath
ms.openlocfilehash: 0b6a7f22d744480a40a878c979986cdd7667109c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-ad-connect-sync-service-shadow-attributes"></a>Atributos do sombra de volumes de serviço de sincronização do Azure AD Connect
A maioria dos atributos são representados da mesma forma no Azure AD, dado que estão no Active Directory no local. Mas alguns atributos têm alguns tratamento especial e o valor do atributo no Azure AD pode ser diferente da que sincroniza o Azure AD Connect.

## <a name="introducing-shadow-attributes"></a>Introdução às atributos de sombra de volumes
Alguns atributos têm dois representações no Azure AD. O valor no local e um valor calculado são armazenados. Estes atributos adicionais são denominados atributos de sombra de volumes. Os dois atributos mais comuns onde vir este comportamento são **userPrincipalName** e **/proxyaddress**. A alteração de valores de atributo acontece quando existem valores nestes atributos que representa os domínios não verificados. Mas o motor de sincronização no Connect lê o valor do atributo a sombra de volumes, a perspetiva, o atributo foi confirmado pelo Azure AD.

Não é possível ver os atributos de sombra de volumes utilizando o Azure portal ou com o PowerShell. Mas compreender o conceito ajuda-o a resolver problemas de determinados cenários em que o atributo tem valores diferentes no local e na nuvem.

Para melhor compreender o comportamento, observe neste exemplo da Fabrikam:  
![Domínios](./media/active-directory-aadconnectsyncservice-shadow-attributes/domains.png)  
Têm vários sufixos UPN no seu Active Directory no local, mas apenas se tem verificado um.

### <a name="userprincipalname"></a>userPrincipalName
Um utilizador com os seguintes valores de atributo num domínio não verificado:

| Atributo | Valor |
| --- | --- |
| userPrincipalName no local | lee.sperry@fabrikam.com |
| Azure AD shadowUserPrincipalName | lee.sperry@fabrikam.com |
| UserPrincipalName do Azure AD | lee.sperry@fabrikam.onmicrosoft.com |

O atributo userPrincipalName é o valor que consulte quando utilizar o PowerShell.

Uma vez que o valor de atributo real no local é armazenado no Azure AD, ao verificar o domínio fabrikam.com, do Azure AD atualiza o atributo userPrincipalName com o valor da shadowUserPrincipalName. Não é necessário que sincronizar as alterações do Azure AD Connect, para estes valores a serem atualizados.

### <a name="proxyaddresses"></a>proxyAddresses
O mesmo processo para apenas incluindo domínios verificados ocorre também para proxyAddresses, mas com algumas lógica adicional. A verificação de domínios verificados só acontece para utilizadores de caixa de correio. Um utilizador com capacidade de correio ou contacte representar um utilizador na outra organização do Exchange e pode adicionar quaisquer valores em proxyAddresses para estes objetos.

Para um utilizador de caixa de correio, no local ou no Exchange Online, são apresentados apenas os valores para os domínios verificados. -Pode ter o seguinte aspeto:

| Atributo | Valor |
| --- | --- |
| no local proxyAddresses | SMTP:abbie.spencer@fabrikamonline.com</br>smtp:abbie.spencer@fabrikam.com</br>smtp:abbie@fabrikamonline.com |
| Exchange Online proxyAddresses | SMTP:abbie.spencer@fabrikamonline.com</br>smtp:abbie@fabrikamonline.com</br>SIP:abbie.spencer@fabrikamonline.com |

Neste caso  **smtp:abbie.spencer@fabrikam.com**  foi removida porque não foi verificado nesse domínio. Mas Exchange também adicionada  **SIP:abbie.spencer@fabrikamonline.com** . Fabrikam não utilizado do Skype/Lync no local, mas o Azure AD e preparar o Exchange Online para o mesmo.

Esta lógica para proxyAddresses é referida como **ProxyCalc**. ProxyCalc é invocado com todas as alterações num utilizador quando:

- O utilizador foi atribuído um plano de serviço que inclua o Exchange Online, mesmo que o utilizador não foi está licenciado para o Exchange. Por exemplo, se o utilizador está atribuído a SKU de E3 do Office, mas só foi atribuído SharePoint Online. Isto acontece mesmo se a caixa de correio ainda está no local.
- O atributo msExchRecipientTypeDetails tem um valor.
- Efetuar uma alteração proxyAddresses ou userPrincipalName.

ProxyCalc poderá demorar algum tempo a processar uma alteração num utilizador, não sendo síncrona com o processo de exportação do Azure AD Connect.

> [!NOTE]
> A lógica de ProxyCalc tem alguns comportamentos adicionais para cenários avançados não documentados neste tópico. Este tópico é fornecido para que possa compreender o comportamento e não documentos lógica todos os interna.

### <a name="quarantined-attribute-values"></a>Valores de atributo em quarentena
Atributos de sombra também são utilizados quando existem valores de atributo duplicado. Para obter mais informações, consulte [resiliência de atributo duplicado](active-directory-aadconnectsyncservice-duplicate-attribute-resiliency.md).

## <a name="see-also"></a>Consultar também
* [Sincronização do Azure AD Connect](active-directory-aadconnectsync-whatis.md)
* [Integrar as identidades no local ao Azure Active Directory](active-directory-aadconnect.md).

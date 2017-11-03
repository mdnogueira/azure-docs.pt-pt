---
title: "Do Azure Active Directory dos serviços de domínio: Sincronização nos domínios geridos | Microsoft Docs"
description: "Compreender a sincronização num domínio do Azure Active Directory Domain Services gerido"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: stevenpo
editor: curtand
ms.assetid: 57cbf436-fc1d-4bab-b991-7d25b6e987ef
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/06/2017
ms.author: maheshu
ms.openlocfilehash: 0c9a9a56e1489ee91fcc332beeef36cdc9c93dc1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="synchronization-in-an-azure-ad-domain-services-managed-domain"></a>Sincronização de um domínio gerido dos serviços de domínio do Azure AD
O diagrama seguinte ilustra como sincronização funciona nos serviços de domínio do Azure AD domínios geridos.

![Topologia de sincronização nos serviços de domínio do Azure AD](./media/active-directory-domain-services-design-guide/sync-topology.png)

## <a name="synchronization-from-your-on-premises-directory-to-your-azure-ad-tenant"></a>Sincronização a partir do seu diretório no local com o seu inquilino do Azure AD
Sincronização do Azure AD Connect é utilizada para sincronizar as contas de utilizador, associações a grupos e credenciais codifica no seu inquilino do Azure AD. Atributos de utilizador das contas, tais como o UPN e no local são sincronizados SID (identificador de segurança). Se utilizar os serviços de domínio do Azure AD, os hashes de credencial legado necessários para a autenticação NTLM e Kerberos também são sincronizados com o seu inquilino do Azure AD.

Se configurar a repetição de escrita, serão sincronizadas as alterações que ocorrem diretório do Azure AD para o Active Directory no local. Por exemplo, se alterar a palavra-passe a utilizar funcionalidades de alteração de palavra-passe self-service do Azure AD, a palavra-passe alterada é atualizada no seu local domínio AD.

> [!NOTE]
> Utilize sempre a versão mais recente do Azure AD Connect para se certificar de que tem correções para todos os erros conhecidos.
>
>

## <a name="synchronization-from-your-azure-ad-tenant-to-your-managed-domain"></a>Sincronização a partir do seu inquilino do Azure AD para o seu domínio gerido
Contas de utilizador, as associações de grupo e hashes de credencial são sincronizados a partir do seu inquilino do Azure AD para o seu domínio gerido dos serviços de domínio do Azure AD. Este processo de sincronização é automático. Não é necessário configurar, monitorizar ou gerir este processo de sincronização. Depois de concluída a sincronização inicial uso do seu diretório, normalmente demora cerca de 20 minutos para as alterações efetuadas no Azure AD para serem refletidas no seu domínio gerido. Este intervalo de sincronização se aplica a alterações de palavra-passe ou as alterações aos atributos efetuados no Azure AD.

O processo de sincronização também é one-way/unidirecionais natureza. O domínio gerido é amplamente só de leitura, exceto para quaisquer UOs personalizados que cria. Por conseguinte, não é possível efetuar as alterações para atributos de utilizador e palavras-passe de utilizador ou associações a grupos no domínio gerido. Como resultado, não há nenhuma sincronização inversa de alterações do seu domínio gerido para o inquilino do Azure AD.

## <a name="synchronization-from-a-multi-forest-on-premises-environment"></a>Sincronização de um ambiente de várias florestas no local
Muitas organizações têm uma infraestrutura de identidade no local relativamente complexa consiste em várias florestas de contas. Azure AD Connect suporta sincronizar os utilizadores, grupos e hashes de credencial de ambientes de várias florestas com o seu inquilino do Azure AD.

Em contrapartida, o seu inquilino do Azure AD é um muito simples e mais simples do espaço de nomes. Para permitir que os utilizadores fiável aceder às aplicações protegidas pelo Azure AD, resolva conflitos UPN em contas de utilizador em florestas diferentes. Os bears de domínio gerido dos serviços de domínio do Azure AD fechar resemblance no seu inquilino do Azure AD. Por conseguinte, verá uma estrutura de UO simples no seu domínio gerido. Todos os utilizadores e grupos são armazenados no contentor 'AADDC utilizadores', independentemente do domínio no local ou a floresta partir do qual foram sincronizadas em. Poderá ter configurado uma UO hierárquica estrutura no local. No entanto, o seu domínio gerido ainda tem uma estrutura de UO simples simples.

## <a name="exclusions---what-isnt-synchronized-to-your-managed-domain"></a>Exclusões - o que não está sincronizado para o seu domínio gerido
Os seguintes objetos ou atributos não estão sincronizados com o seu inquilino do Azure AD ou ao seu domínio gerido:

* **Excluídos atributos:** pode optar por excluir determinados atributos a sincronizar com o seu inquilino do Azure AD do seu domínio no local com o Azure AD Connect. Estes atributos excluídos não estão disponíveis no seu domínio gerido.
* **Políticas de grupo:** políticas de grupo configuradas no seu domínio no local não estão sincronizadas para o seu domínio gerido.
* **Partilha SYSVOL:** da mesma forma, os conteúdos da partilha SYSVOL no seu domínio no local não estão sincronizados para o seu domínio gerido.
* **Objetos de computador:** objetos de computador para computadores associados ao seu domínio no local não estão sincronizados com o seu domínio gerido. Estes computadores não têm uma relação de fidedignidade com o seu domínio gerido e pertence ao seu domínio no local apenas. No seu domínio gerido, localize os objetos de computador apenas para computadores que têm explicitamente domínio associado ao domínio gerido.
* **Atributos de SidHistory para utilizadores e grupos:** o utilizador principal e os SIDs do seu domínio no local do grupo principal são sincronizados para o seu domínio gerido. No entanto, atributos de SidHistory existentes para utilizadores e grupos não estão sincronizados do seu domínio local no seu domínio gerido.
* **Estruturas de unidades (UO) da organização:** unidades organizacionais definida no seu domínio no local não sincronizar com o seu domínio gerido. Existem dois UOs incorporados no seu domínio gerido. Por predefinição, o seu domínio gerido tem uma estrutura de UO simples. No entanto pode optar por [criar uma UO personalizada no seu domínio gerido](active-directory-ds-admin-guide-create-ou.md).

## <a name="how-specific-attributes-are-synchronized-to-your-managed-domain"></a>Como são sincronizados atributos específicos ao seu domínio gerido
A tabela seguinte lista alguns atributos comuns e descreve a forma como são sincronizados para o seu domínio gerido.

| Atributo no seu domínio gerido | Origem | Notas |
|:--- |:--- |:--- |
| UPN |Atributo UPN do utilizador no inquilino do Azure AD |O atributo UPN de inquilino do Azure AD está sincronizado, é ao seu domínio gerido. Por conseguinte, a forma mais fiável para iniciar sessão no seu domínio gerido está a utilizar o UPN. |
| SAMAccountName |MailNickname do utilizador no inquilino do Azure AD de atributo ou gerado automaticamente |O atributo de SAMAccountName é obtido a partir do atributo mailNickname no seu inquilino do Azure AD. Se várias contas de utilizador tem o mesmo atributo de mailNickname, SAMAccountName é gerado automaticamente. Se mailNickname ou o prefixo UPN do utilizador é maior do que 20 carateres, SAMAccountName é gerado automaticamente para satisfazer o limite de 20 carateres nos atributos de SAMAccountName. |
| Palavras-passe |Palavra-passe de utilizador do seu inquilino do Azure AD |Os hashes de credencial necessários para a autenticação NTLM ou Kerberos (também denominada credenciais suplementares) são sincronizados a partir do seu inquilino do Azure AD. Se o seu inquilino do Azure AD é um inquilino sincronizado, estas credenciais são obtidas as do seu domínio no local. |
| Utilizador/SID primário do grupo |Gerado automaticamente |O SID primário para contas de utilizador/grupo é gerado automaticamente no seu domínio gerido. Este atributo não corresponde o utilizador/SID de grupo principal do objeto no seu local de domínio do AD. Este erro de correspondência é porque o domínio gerido com um espaço de nomes de SID diferente ao seu domínio no local. |
| Histórico de SIDs para utilizadores e grupos |Utilizador primário no local e o SID de grupo |O atributo de SidHistory para utilizadores e grupos no seu domínio gerido está definido para corresponder o utilizador primário correspondente ou o grupo SID no seu domínio no local. Esta funcionalidade ajuda-o facilitar a comparação de precisão e shift das aplicações no local para o domínio gerido, uma vez que não é necessário a recursos de re-ACL. |

> [!NOTE]
> **Inicie sessão no domínio gerido utilizando o formato UPN:** SAMAccountName o atributo pode ser gerada automaticamente para algumas contas de utilizador no seu domínio gerido. Se vários utilizadores têm o mesmo atributo de mailNickname ou os utilizadores têm prefixos UPN excessivamente longos, SAMAccountName para estes utilizadores pode ser gerada automaticamente. Por conseguinte, o formato de SAMAccountName (por exemplo, ' CONTOSO100\joeuser') não é sempre uma forma fiável para iniciar sessão no domínio. SAMAccountName de geração automática de utilizadores pode divergir do prefixo respetivo UPN. Utilize o formato UPN (por exemplo, 'joeuser@contoso100.com') para iniciar sessão no domínio gerido de forma fiável.
>
>

### <a name="attribute-mapping-for-user-accounts"></a>Mapeamento de atributos para contas de utilizador
A tabela seguinte ilustra como específicos atributos de utilizador são sincronizados objetos no seu inquilino do Azure AD para atributos correspondentes no seu domínio gerido.

| Atributo de utilizador no inquilino do Azure AD | Atributo de utilizador no seu domínio gerido |
|:--- |:--- |
| accountEnabled |userAccountControl (define ou limpa ACCOUNT_DISABLED bit) |
| city |l |
| País |Co |
| Departamento |Departamento |
| displayName |displayName |
| facsimileTelephoneNumber |facsimileTelephoneNumber |
| givenName |givenName |
| jobTitle |Título |
| capacidade de correio |capacidade de correio |
| mailNickname |msDS-AzureADMailNickname |
| mailNickname |SAMAccountName (por vezes poderá gerado automaticamente) |
| Mobile |Mobile |
| ObjectId |msDS-AzureADObjectId |
| onPremiseSecurityIdentifier |sidHistory |
| passwordPolicies |userAccountControl (define ou limpa DONT_EXPIRE_PASSWORD bit) |
| physicalDeliveryOfficeName |physicalDeliveryOfficeName |
| PostalCode |PostalCode |
| preferredLanguage |preferredLanguage |
| state |St |
| StreetAddress |StreetAddress |
| Apelido |sn |
| telephoneNumber |telephoneNumber |
| userPrincipalName |userPrincipalName |

### <a name="attribute-mapping-for-groups"></a>Mapeamento de atributos de grupos
A tabela seguinte ilustra os atributos como específicos para o grupo de objetos no seu inquilino do Azure AD são sincronizados com atributos correspondentes no seu domínio gerido.

| Atributo de grupo no seu inquilino do Azure AD | Atributo de grupo no seu domínio gerido |
|:--- |:--- |
| displayName |displayName |
| displayName |SAMAccountName (por vezes poderá gerado automaticamente) |
| capacidade de correio |capacidade de correio |
| mailNickname |msDS-AzureADMailNickname |
| ObjectId |msDS-AzureADObjectId |
| onPremiseSecurityIdentifier |sidHistory |
| securityEnabled |groupType |

## <a name="objects-that-are-not-synchronized-to-your-azure-ad-tenant-from-your-managed-domain"></a>Objetos que não estão sincronizados com o seu inquilino do Azure AD do seu domínio gerido
Como descrito na secção anterior deste artigo, não há nenhuma sincronização a partir do seu domínio gerido para o inquilino do Azure AD. Pode optar por [criar uma unidade organizacional personalizado (UO)](active-directory-ds-admin-guide-create-ou.md) no seu domínio gerido. Além disso, pode criar outras UOs, utilizadores, grupos ou contas de serviço dentro destes UOs personalizados. Nenhum dos objetos criados dentro personalizados UOs são sincronizados para o inquilino do Azure AD. Estes objetos estão disponíveis para utilização apenas dentro do seu domínio gerido. Por conseguinte, estes objetos não estão visíveis utilizando cmdlets do Azure AD PowerShell, AD Graph API do Azure ou utilizando a IU de gestão do Azure AD.

## <a name="related-content"></a>Conteúdo relacionado
* [Funcionalidades - Serviços de domínio do Azure AD](active-directory-ds-features.md)
* [Cenários de implementação - serviços de domínio do Azure AD](active-directory-ds-scenarios.md)
* [Considerações sobre o funcionamento em rede para serviços de domínio do Azure AD](active-directory-ds-networking.md)
* [Começar com os serviços de domínio do Azure AD](active-directory-ds-getting-started.md)

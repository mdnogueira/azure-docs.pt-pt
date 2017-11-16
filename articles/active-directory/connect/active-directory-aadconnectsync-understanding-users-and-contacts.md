---
title: "Sincronização do Azure AD Connect: entender utilizadores, contactos e grupos, | Microsoft Docs"
description: "Explica os utilizadores, grupos e contactos na sincronização do Azure AD Connect."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: 8d204647-213a-4519-bd62-49563c421602
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/17/2017
ms.author: markvi;andkjell
ms.openlocfilehash: c298a2f99750ead099b8761699c914a3a6e41ce1
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/15/2017
---
# <a name="azure-ad-connect-sync-understanding-users-groups-and-contacts"></a>Sincronização do Azure AD Connect: entender utilizadores, grupos e contactos
Existem diversas razões diferentes por que motivo, deverá dispor várias florestas do Active Directory e não existirem várias topologias de implementação diferentes. Modelos comuns incluem uma implementação de recursos de conta e da GAL sync'ed florestas após uma fusão & aquisição. Mas, mesmo se não existirem modelos puro, modelos híbridos são comuns bem. A configuração predefinida na sincronização do Azure AD Connect não assume qualquer modelo específico, mas, dependendo de como a correspondência de utilizador tiver sido selecionada no guia de instalação, podem ser observados comportamentos diferentes.

Neste tópico, iremos irá passar por como a configuração predefinida comporta-se algumas topologias. Iremos passarão a configuração e o Editor de regras de sincronização pode ser utilizado para ver a configuração.

Existem algumas regras gerais pressupõe que a configuração:
* Independentemente da que ordem iremos importar a partir da origem de diretórios do Active Directory, o resultado final deve ser sempre igual.
* Uma conta ativa sempre contribuem informações de início de sessão, incluindo **userPrincipalName** e **sourceAnchor**.
* Uma conta desativada contribuem userPrincipalName e sourceAnchor, a menos que é uma caixa de correio ligada, se não existir nenhuma conta Active Directory ser encontrado.
* Uma conta com uma caixa de correio ligada nunca será utilizada para userPrincipalName e sourceAnchor. Presume-se que uma conta Active Directory irá encontrar mais tarde.
* Pode ser aprovisionado um objeto de contacto para o Azure AD como um contacto ou como um utilizador. Ainda não conhece até que todas as florestas do Active Directory de origem tem sido processadas.

## <a name="groups"></a>Grupos
Pontos importantes a ter em consideração ao sincronizar a grupos do Active Directory para o Azure AD:

* O Azure AD Connect exclui grupos de segurança incorporadas de sincronização de diretórios.

* O Azure AD Connect não suporta a sincronização [associações de grupo principal](https://technet.microsoft.com/library/cc771489(v=ws.11).aspx) para o Azure AD.

* O Azure AD Connect não suporta a sincronização [associações a distribuição dinâmica](https://technet.microsoft.com/library/bb123722(v=exchg.160).aspx) para o Azure AD.

* Sincronizar um grupo do Active Directory para o Azure AD como um grupo com capacidade de correio:

    * Se o grupo */proxyaddress* está vazio, o atributo respetivo *correio* atributo tem de ter um valor, ou 

    * Se o grupo */proxyaddress* atributo é não vazia, também tem de conter um valor de endereço de proxy SMTP primário (conforme a designação por maiúsculas **SMTP** prefixo). Eis alguns exemplos:
    
      * Um grupo do Active Directory cujo atributo /proxyaddress tem o valor *{"X500:/0=contoso.com/ou=users/cn=testgroup"}* não será com capacidade de correio no Azure AD. Não tem um endereço SMTP principal.
      
      * Um grupo do Active Directory cujo atributo /proxyaddress tem valores *{"X500:/0=contoso.com/ou=users/cn=testgroup", "smtp:johndoe@contoso.com"}* não será com capacidade de correio no Azure AD. Tem um endereço smtp, mas não é primária.
      
      * Um grupo do Active Directory cujo atributo /proxyaddress tem valores *{"X500:/0=contoso.com/ou=users/cn=testgroup","SMTP:johndoe@contoso.com"}* será com capacidade de correio no Azure AD.

## <a name="contacts"></a>Contactos
Ter contactos que representa um utilizador numa floresta diferente é comum após uma fusão & aquisição onde uma solução de GALSync é bridging duas ou mais florestas do Exchange. O objeto de contacto é sempre a associar a partir do espaço de conector para o metaverso utilizando o atributo de correio. Se já existir um objeto de contacto ou objeto de utilizador com o mesmo endereço de correio, os objetos são Unidos. Este é configurado na regra **do AD – contacte associar**. Há também uma regra com o nome **do AD – contacte comuns** com um fluxo de atributos para o atributo do metaverso **sourceObjectType** com a constante **contacte**. Esta regra tem precedência muito baixa, pelo que, se qualquer objeto de utilizador está associado ao mesmo objeto de metaverso, em seguida, a regra **do AD – utilizador comuns** contribuem o valor de utilizador para este atributo. Com esta regra, este atributo tem o valor contacte se nenhum utilizador tiver sido associado e o valor do utilizador se não foi encontrado pelo menos um utilizador.

Para o aprovisionamento de um objeto com o Azure AD, a regra de saída **Out para o AAD – contacte associar** irá criar um objeto de contacto se o atributo do metaverso **sourceObjectType** está definido como **contacte**. Se este atributo estiver definido como **utilizador**, em seguida, a regra de **Out para o AAD – associar utilizador** criará um objeto de utilizador.
É possível que um objeto é promovido de contacto ao utilizador quando mais diretórios de Active Directory de origem são importados e sincronizados.

Por exemplo, numa topologia GALSync será encontrámos objetos de contactos para todas as pessoas na segunda floresta quando iremos importar a primeira floresta. Isto irá testar novos objetos de contactos no conector AAD. Quando é mais tarde, importar e sincronizar a segunda floresta, iremos localize os utilizadores reais e associe-las para os objetos existentes do metaverso. Iremos, em seguida, elimine o objeto de contacto no AAD e criar um novo objeto de utilizador em vez disso.

Se tiver uma topologia em que os utilizadores são representados como contactos, certifique-se de que seleciona para corresponder aos utilizadores no atributo de correio no guia de instalação. Se selecionar outra opção, em seguida, terá uma configuração de ordem dependentes. Objetos de contactos serão sempre associado no atributo de correio, mas só irão associar objetos de utilizador no atributo de correio se esta opção tiver sido selecionada no guia de instalação. Pode, em seguida, acaba por ficar com dois diferentes objetos no metaverso com o mesmo atributo de correio eletrónico se o objeto de contacto foi importado antes do objeto de utilizador. Durante a exportação para o Azure AD, será emitido um erro. Este comportamento é por predefinição e deverá indicar dados incorretos ou que a topologia não foi identificada corretamente durante a instalação.

## <a name="disabled-accounts"></a>Contas desativadas
Contas desativadas são sincronizadas, bem como para o Azure AD. Contas desativadas são comuns para representar a recursos no Exchange, por exemplo gabinetes de conferência. A exceção é a utilizadores com uma caixa de correio ligada; como anteriormente mencionadas, estes nunca irão aprovisionar uma conta para o Azure AD.

Pressuposto é que o se encontra-se uma conta de utilizador desativado, em seguida, irá não encontrámos outra conta de Active Directory mais tarde e o objeto é aprovisionado para o Azure AD com o userPrincipalName e sourceAnchor encontrado. No caso de outra conta de Active Directory irá aderir ao mesmo objeto de metaverso, em seguida, o userPrincipalName e sourceAnchor serão utilizados.

## <a name="changing-sourceanchor"></a>Alterar sourceAnchor
Quando um objeto foi exportado para o Azure AD, em seguida, não é permitido alterar o sourceAnchor já. Quando o objeto tiver sido exportado o atributo do metaverso **cloudSourceAnchor** está definida com o **sourceAnchor** valor aceites pelo Azure AD. Se **sourceAnchor** é alterado e não corresponde ao **cloudSourceAnchor**, a regra **Out para o AAD – associar utilizador** irá gerar o erro **atributo sourceAnchor mudou**. Neste caso, a configuração ou os dados têm de ser corrigidos para a mesma sourceAnchor está presente no metaverso novamente antes do objeto pode ser sincronizado novamente.

## <a name="additional-resources"></a>Recursos Adicionais
* [Sincronização do Azure AD Connect: Personalizar as opções de sincronização](active-directory-aadconnectsync-whatis.md)
* [Integrar as identidades no local ao Azure Active Directory](active-directory-aadconnect.md)


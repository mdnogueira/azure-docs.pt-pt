---
title: "O Azure AD Connect: Se já tiver do Azure AD | Microsoft Docs"
description: "Este tópico descreve como utilizar ligar quando tiver um inquilino do Azure AD existente."
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
ms.openlocfilehash: a7df154748a4ce8ac592a41f2a3d6b10ac359113
ms.sourcegitcommit: cfd1ea99922329b3d5fab26b71ca2882df33f6c2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/30/2017
---
# <a name="azure-ad-connect-when-you-have-an-existent-tenant"></a>O Azure AD Connect: Se tiver um inquilino existente
A maioria dos tópicos para saber como utilizar o Azure AD Connect parte do princípio de que começa com um novo Azure inquilino do AD e de que não existe nenhum utilizador ou outros objetos não existe. Mas se tiver iniciado com um inquilino do Azure AD, preenchido este com utilizadores e outros objetos e agora pretende utilizar Connect, em seguida, este tópico é que o utilizador.

## <a name="the-basics"></a>Noções básicas
Um objeto no Azure AD ou é controlado na nuvem (Azure AD) ou no local. Para um único objeto, não é possível gerir alguns atributos no local e alguns outros atributos no Azure AD. Cada objeto tem um sinalizador que indica onde o objecto é gerido.

Pode gerir alguns utilizadores no local e outros na nuvem. Um cenário comum para esta configuração é uma organização com uma combinação dos técnicos de gestão de contas e técnicos de vendas. Tem dos trabalhadores de gestão de contas no local conta AD, mas os trabalhadores vendas não compatíveis, que têm uma conta no Azure AD. Iria gerir alguns utilizadores no local e algumas no Azure AD.

Se tiver iniciado para gerir os utilizadores no Azure AD que também estejam no AD no local e posteriormente, pretender utilizar Connect, em seguida, existem algumas questões adicionais, precisa de considerar.

## <a name="sync-with-existing-users-in-azure-ad"></a>Sincronizar com os utilizadores existentes no Azure AD
Quando instalar o Azure AD Connect e iniciá-la, o serviço de sincronização do Azure AD (no Azure AD) faz uma verificação de cada objeto novo e tentar localizar um objeto existente para corresponder. Existem três atributos utilizados para executar este processo: **userPrincipalName**, **proxyAddresses**, e **sourceAnchor**/**immutableID**. Uma correspondência na **userPrincipalName** e **proxyAddresses** é conhecido como um **correspondência de forma recuperável**. Uma correspondência na **sourceAnchor** é conhecido como **correspondência de disco rígida**. Para o **proxyAddresses** atributo apenas o valor com **SMTP:**, que é o endereço de correio eletrónico principal, é utilizado para a avaliação.

A correspondência é avaliada apenas novos objetos feitos Connect. Se alterar um objeto existente para esta correspondência qualquer um destes atributos, em seguida, verá um erro em vez disso.

Se o Azure AD encontrar um objeto onde os valores de atributo são os mesmos para um objeto proveniente de ligar e que já se encontra presente no Azure AD, em seguida, o objeto no Azure AD é tomado over by Connect. O objeto gerido anteriormente nuvem está assinalado como gerido no local. Todos os atributos no Azure AD com um valor no local no AD são substituídos com o valor no local. A exceção é quando um atributo tem um **nulo** valor no local. Neste caso, o valor no Azure AD permanece, mas ainda só pode alterá-la no local para outra coisa.

> [!WARNING]
> Uma vez que todos os atributos no Azure AD que vão ser substituída pelo valor no local, certifique-se de que tem os dados no local. Por exemplo, se apenas a ter geridas endereço de correio eletrónico no Office 365 e não são mantidas atualizadas no local do AD DS, e perder quaisquer valores no Azure AD/Office 365 não está presente no AD DS.

> [!IMPORTANT]
> Se utilizar a sincronização de palavra-passe, o que é sempre utilizada pelas definições rápidas, em seguida, a palavra-passe no Azure AD é substituída com a palavra-passe no local no AD. Se os utilizadores são utilizados para gerir palavras-passe diferentes, terá de informe-os de que devem utilizar a palavra-passe no local quando instalou o ligar.

A secção anterior e o aviso devem ser consideradas no seu planeamento. Se tiver efetuado alterações muitos no Azure AD não serão refletido no local do AD DS, em seguida, terá de planear a forma preencher o AD DS com valores atualizados antes de poder sincronizar os objetos com o Azure AD Connect.

Se corresponder os objetos com uma configuração soft-match, o **sourceAnchor** é adicionada para o objeto no Azure AD para uma correspondência de disco rígida possam ser utilizada mais tarde.

### <a name="hard-match-vs-soft-match"></a>Disco rígido-match vs correspondência de forma recuperável
Para uma nova instalação do Connect, não há qualquer diferença prática entre uma configuração soft - e uma disco rígido correspondência. A diferença é uma situação de recuperação após desastre. Se tiver perdido o seu servidor com o Azure AD Connect, pode reinstalar uma nova instância sem perder quaisquer dados. Um objeto com um sourceAnchor é enviado para ligar durante a instalação inicial. A correspondência de, em seguida, pode ser avaliada pelo cliente (Azure AD Connect), que é muito mais rápido do que a fazer o mesmo no Azure AD. Uma correspondência de disco rígida é avaliada por ligar e pelo Azure AD. Uma correspondência de forma recuperável é avaliada apenas pelo Azure AD.

### <a name="other-objects-than-users"></a>Outros objetos que os utilizadores
Para grupos com capacidade de correio e contactos, pode soft-correspondência com base em proxyAddresses. Disco rígido-match não é aplicável, uma vez que só pode atualizar o sourceAnchor/immutableID (utilizando o PowerShell) nos utilizadores apenas. Para os grupos que não são com capacidade de correio, não são atualmente suportadas para a configuração soft-match ou disco rígido-match.

## <a name="create-a-new-on-premises-active-directory-from-data-in-azure-ad"></a>Criar um novo diretório Active Directory no local a partir dos dados no Azure AD
Alguns clientes começam com uma solução apenas na nuvem com o Azure AD e não têm um local AD. Mais tarde pretendem consumir recursos no local e pretender criar um local AD com base nos dados do Azure AD. O Azure AD Connect não pode ajudá-lo para este cenário. Não cria os utilizadores no local e não tem qualquer capacidade de definir a palavra-passe no local para os mesmos que no Azure AD.

Se o motivo apenas motivo pelo qual planeia adicionar AD no local é para suportar LOBs (aplicações de linha de negócio), em seguida, talvez deve considerar a utilizar [serviços de domínio do Azure AD](../../active-directory-domain-services/index.md) em vez disso.

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre como [Integrar as identidades no local ao Azure Active Directory](active-directory-aadconnect.md).

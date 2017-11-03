---
title: "Sincronização do Azure AD Connect: alterar a configuração predefinida | Microsoft Docs"
description: "Fornece as melhores práticas para alterar a configuração predefinida de sincronização do Azure AD Connect."
services: active-directory
documentationcenter: 
author: andkjell
manager: femila
editor: 
ms.assetid: 7638a031-1635-4942-94c3-fce8f09eed5e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: billmath
ms.openlocfilehash: b723ad800ccc0f3040eb480bb72960943b1fdb16
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-ad-connect-sync-best-practices-for-changing-the-default-configuration"></a>Sincronização do Azure AD Connect: melhores práticas para alterar a configuração predefinida
O objetivo deste tópico é descrever alterações suportadas e não suportadas para sincronização do Azure AD Connect.

A configuração criada pelo Azure AD Connect funciona "tal como está" para a maior parte dos ambientes sincronizar o Active Directory no local com o Azure AD. No entanto, em alguns casos, é necessário aplicar algumas alterações a uma configuração para satisfazer um necessidade específica ou um requisito.

## <a name="changes-to-the-service-account"></a>Alterações para a conta de serviço
Sincronização do Azure AD Connect está em execução com uma conta de serviço criada pelo Assistente de instalação. Esta conta de serviço contém as chaves de encriptação na base de dados utilizado por uma sincronização. É criado com uma palavra-passe de tempo de 127 carateres e a palavra-passe está definida para expirar.

* É **não suportado** para alterar ou repor a palavra-passe da conta de serviço. Se o fizer, destroys as chaves de encriptação e o serviço não é capaz de aceder a base de dados e não é possível ao iniciar.

## <a name="changes-to-the-scheduler"></a>Alterações para o programador
A partir de versões de compilação 1.1 (Fevereiro de 2016), pode configurar o [programador](active-directory-aadconnectsync-feature-scheduler.md) ter um ciclo de sincronização diferentes que não o predefinido de 30 minutos.

## <a name="changes-to-synchronization-rules"></a>Alterações às regras de sincronização
O Assistente de instalação fornece uma configuração que deveria funciona para os cenários mais comuns. No caso de precisar de efetuar alterações à configuração, tem de seguir estas regras a continuará a ter uma configuração suportada.

* Pode [alterar fluxos de atributos](active-directory-aadconnectsync-change-the-configuration.md#other-common-attribute-flow-changes) se os fluxos de atributos direto predefinido não são adequados para a sua organização.
* Se pretender [fluxo não de um atributo](active-directory-aadconnectsync-change-the-configuration.md#do-not-flow-an-attribute) e remover valores de qualquer atributo existente no Azure AD, em seguida, terá de criar uma regra para este cenário.
* [Desativar uma regra de sincronização indesejável](#disable-an-unwanted-sync-rule) em vez de eliminá-lo. Uma regra eliminada é recriada durante uma atualização.
* Para [alterar uma regra de out-of-box](#change-an-out-of-box-rule), deve fazer uma cópia da regra original e desativar a regra de out-of-box. O Editor de regras de sincronização solicita e ajuda-o.
* Exporte as regras de sincronização personalizada utilizando o Editor de regras de sincronização. O editor proporciona um script do PowerShell, que pode utilizar para recriá-los facilmente num cenário de recuperação após desastre.

> [!WARNING]
> As regras de sincronização de out-of-box têm um thumbprint. Se fizer uma alteração a estas regras, o thumbprint já não é correspondente. Poderá ter problemas no futuro quando tenta aplicar uma nova versão do Azure AD Connect. Apenas Efetue alterações a forma como é descrito neste artigo.

### <a name="disable-an-unwanted-sync-rule"></a>Desativar uma regra de sincronização indesejável
Não elimine uma regra de sincronização de out-of-box. Este é recriado durante a atualização seguinte.

Em alguns casos, o Assistente de instalação foi produzida uma configuração que não está a funcionar para a topologia. Por exemplo, se tiver uma topologia de floresta de recursos de conta, mas tiver expandido o esquema de floresta de contas com o esquema do Exchange, as regras para o Exchange são criadas para a floresta de conta e a floresta de recursos. Neste caso, terá de desativar a regra de sincronização para o Exchange.

![Regra de sincronização desativado](./media/active-directory-aadconnectsync-best-practices-changing-default-configuration/exchangedisabledrule.png)

Na imagem acima, o Assistente de instalação encontrou um esquema de Exchange 2003 antigo na floresta de contas. Esta extensão de esquema foi adicionada antes de floresta de recursos foi introduzida no ambiente da Fabrikam. Para garantir que não os atributos da implementação antigo do Exchange são sincronizados, a regra de sincronização deve ser desativada conforme mostrado.

### <a name="change-an-out-of-box-rule"></a>Alterar uma regra de out-of-box
A única vez, deve alterar a uma regra de out-of-box é quando precisar de alterar a regra de associação. Se precisar de alterar um fluxo de atributos, deve criar uma regra de sincronização com precedência superior às regras de out-of-box. A regra só terá praticamente a clonagem é a regra **do AD - associar utilizador**. Pode substituir todas as outras regras com uma regra de precedência superior.

Se precisar de efetuar alterações a uma regra de out-of-box, deve fazer uma cópia da regra de out-of-box e desativar a regra original. Em seguida, efetue as alterações para a regra clonada. O Editor de regras de sincronização está a ajudar com esses passos. Quando abre uma regra de out-of-box, é-lhe apresentada esta caixa de diálogo:  
![Aviso de regra de caixa](./media/active-directory-aadconnectsync-best-practices-changing-default-configuration/warningoutofboxrule.png)

Selecione **Sim** para criar uma cópia da regra. A regra clonada, em seguida, é aberta.  
![Regra clonada](./media/active-directory-aadconnectsync-best-practices-changing-default-configuration/clonedrule.png)

Esta regra clonado, efetue as alterações necessárias ao âmbito, associação e transformações.

## <a name="next-steps"></a>Passos seguintes
**Tópicos de descrição geral**

* [Sincronização do Azure AD Connect: Noções e personalizar a sincronização](active-directory-aadconnectsync-whatis.md)
* [Integrar as identidades no local ao Azure Active Directory](active-directory-aadconnect.md)

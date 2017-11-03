---
title: "Conectores na IU do Gestor de serviço de sincronização do AD do Azure | Microsoft Docs"
description: "Compreenda o separador de conectores no Gestor de serviço de sincronização do Azure AD Connect."
services: active-directory
documentationcenter: 
author: andkjell
manager: femila
editor: 
ms.assetid: 60f1d979-8e6d-4460-aaab-747fffedfc1e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.author: billmath
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c0fae4b1755ca95466eeffb5ce61c1c7855d7381
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="using-connectors-with-the-azure-ad-connect-sync-service-manager"></a>A utilização de conectores com o Azure AD Connect sincronização do Service Manager

![Gestor do serviço de sincronização](./media/active-directory-aadconnectsync-service-manager-ui/connectors.png)

O separador de conectores é utilizado para gerir todos os sistemas que o motor de sincronização está ligado.

## <a name="connector-actions"></a>Ações de conector
| Ação | Comentário |
| --- | --- |
| Criar |Não utilize. Para ligar a florestas adicionais do AD, utilize o Assistente de instalação. |
| Propriedades |Utilizado para o domínio e a filtragem de UO. |
| [Eliminar](#delete) |Utilizado para optar por eliminar os dados no espaço de conector ou ao eliminar a ligação a uma floresta. |
| [Configurar perfis de execução](#configure-run-profiles) |Exceto domínio filtragem, nada para configurar aqui. Pode utilizar esta ação para ver os perfis de execução já configurados. |
| Executar |Utilizado para iniciar uma execução pontuais de um perfil. |
| Parar |Parar um conector atualmente em execução um perfil. |
| Conector de exportação |Não utilize. |
| Importar o conector |Não utilize. |
| Conector de atualização |Não utilize. |
| Atualizar o esquema |Atualiza o esquema em cache. É preferencial para utilizar a opção no Assistente de instalação em vez disso, desde que também as atualizações de sincronizar as regras. |
| [Espaço de conector de pesquisa](#search-connector-space) |Utilizado para localizar objetos e [siga um objeto e os respetivos dados através do sistema](#follow-an-object-and-its-data-through-the-system). |

### <a name="delete"></a>Eliminar
A ação de eliminação é utilizada para duas coisas diferentes.  
![Gestor do serviço de sincronização](./media/active-directory-aadconnectsync-service-manager-ui/connectordelete.png)

A opção **eliminar apenas o espaço de conector** remove todos os dados, mas manter a configuração.

A opção **espaço de conector de eliminar e conector** remove os dados e a configuração. Esta opção é utilizada quando não pretende ligar a uma floresta já.

Ambas as opções sincronizar todos os objetos e atualizar os objetos de metaverso. Esta ação é uma operação de execução longa.

### <a name="configure-run-profiles"></a>Configurar perfis de execução
Esta opção permite-lhe ver os perfis de execução configurados para um conector.

![Gestor do serviço de sincronização](./media/active-directory-aadconnectsync-service-manager-ui/configurerunprofiles.png)

### <a name="search-connector-space"></a>Espaço de conector de pesquisa
A ação de espaço de conector de pesquisa é útil para localizar objetos e resolver problemas de dados.

![Gestor do serviço de sincronização](./media/active-directory-aadconnectsync-service-manager-ui/cssearch.png)

Comece por selecionar uma **âmbito**. Pode procurar com base nos dados (RDN, DN, âncora, árvore secundárias), ou estado do objeto (todas as outras opções).  
![Gestor do serviço de sincronização](./media/active-directory-aadconnectsync-service-manager-ui/cssearchscope.png)  
Se o fizer, por exemplo, uma pesquisa de árvore secundárias, obter todos os objetos num UO.  
![Gestor do serviço de sincronização](./media/active-directory-aadconnectsync-service-manager-ui/cssearchsubtree.png)  
Desta grelha pode selecionar um objeto, selecione **propriedades**, e [segui-la](active-directory-aadconnectsync-troubleshoot-object-not-syncing.md) partir do espaço de conector de origem, através do metaverso e para o espaço de conector de destino.

### <a name="changing-the-ad-ds-account-password"></a>Alterar a palavra-passe de conta do AD DS
Se alterar a palavra-passe de conta, o serviço de sincronização já não será capaz de alterações no local para importar/exportar AD.   Poderá ver o seguinte:

- O passo de importação/exportação para o conector do AD falhar com o erro "credenciais de início de não".
- No Visualizador de eventos do Windows, o registo de eventos da aplicação contém um erro com 6000 de ID de evento e a mensagem "o agente de gestão"contoso.com"Falha ao executar porque as credenciais foram inválidas."

Para resolver o problema, atualize a conta de utilizador do AD DS utilizando o seguinte:


1. Inicie o Synchronization Service Manager (serviço de sincronização do início →).
</br>![Gestor do serviço de sincronização](./media/active-directory-aadconnectsync-service-manager-ui/startmenu.png)
2. Vá para o **conectores** separador.
3. Seleccione o conector do AD que está configurado para utilizar a conta do AD DS.
4. Em ações, selecione **propriedades**.
5. Na caixa de diálogo de pop-up, selecione ligar à floresta do Active Directory:
6. O nome da floresta indica o correspondente no local AD.
7. O nome de utilizador indica a conta do AD DS utilizada para sincronização.
8. Introduza a nova palavra-passe da conta do AD DS na caixa de texto de palavra-passe ![do Azure AD Connect sincronização encriptação chave utilitário](media/active-directory-aadconnectsync-encryption-key/key6.png)
9. Clique em OK para guardar a nova palavra-passe e reinicie o serviço de sincronização para remover a palavra-passe antiga da cache de memória.



## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre o [sincronização do Azure AD Connect](active-directory-aadconnectsync-whatis.md) configuração.

Saiba mais sobre como [Integrar as identidades no local ao Azure Active Directory](active-directory-aadconnect.md).
